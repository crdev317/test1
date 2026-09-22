# Technical Context

Engineering-practice contract for code-writing agents in this project. Every implementation must respect what's written here. If a constraint conflicts with the user's request, flag the conflict — don't silently bypass it.

## Overriding Principles
1. **Secrets never live in source or plain config.** Any API key or credential is stored in the OS credential store (Windows Credential Manager / macOS Keychain / libsecret) via the Rust `keyring` crate — never in `.env`, committed config, or the frontend bundle. A secret in the webview is readable by anyone who opens devtools.
2. **The app degrades gracefully offline.** When Open-Meteo is unreachable or errors, the app shows the last cached data with a visible staleness indicator — it never crashes and never renders a blank screen. Network loss is a normal state for a desktop app, not an exception.
3. **Forecasting logic is pure and deterministic.** Forecast functions take their inputs as arguments and return a result — no network, filesystem, clock, or randomness inside them. The same inputs always produce the same forecast, so the core of the product is testable without the network.
4. **No new runtime dependency without justification.** Anything outside *Packages in use* needs a written reason in the PR. Every dependency ships inside the installer and widens the attack surface.
5. **No breaking change to persisted user data without an ADR and a migration.** Saved locations, settings, and the observation cache survive every upgrade. Users do not lose their setup to a release.

## Frameworks
- **Tauri 2.x** — desktop shell: native window, OS integration, IPC between the Rust core and the webview, installer bundling.
- **Rust (stable, pinned — see *Toolchain pinning*)** — the Tauri core: commands exposed to the frontend, credential-store access, plugin hosting.
- **React 19** — the UI, rendered in the Tauri webview.
- **TypeScript (strict mode)** — all frontend code, including the forecasting logic.
- **Vite** — frontend dev server and bundler, driven by the Tauri CLI.
- **Target platforms:** Windows, macOS, and Linux. All three are supported equally.

## 3rd-party tech
- **Open-Meteo** — the sole weather data provider: current conditions, hourly and daily forecasts, historical observations (the input to the app's own forecasting), and geocoding for location search. No API key is required. **Licence constraint:** the free tier is for non-commercial use only; any commercial distribution of this app requires a paid Open-Meteo plan first.
- **GitHub** — source hosting (`crdev317/test1`) and GitHub Releases as the installer distribution channel.
- **Azure DevOps** — work tracking (`EnateInternal` / `test1`, per `.factory.yml`) and the CI pipeline that posts the `ci` status check.

## Packages in use
**UI:** `react`, `react-dom`, `@tauri-apps/api`
**State & data fetching:** `@tanstack/react-query` — caching, background refresh, and stale-data handling for principle 2
**Validation:** `zod` — every Open-Meteo response is parsed at the boundary; nothing unvalidated reaches the forecasting logic
**Dates & time:** `date-fns`, `date-fns-tz`
**Charts:** `recharts`
**Persistence:** `@tauri-apps/plugin-store` (settings, saved locations), `@tauri-apps/plugin-sql` with SQLite (the observation cache the forecasting reads)
**Secrets:** `keyring` (Rust crate) — only if a keyed provider is ever added; Open-Meteo needs none
**Logging:** `@tauri-apps/plugin-log` (Rust `log` underneath)
**Testing — unit/integration:** `vitest`, `@testing-library/react`, `msw` (fake Open-Meteo for recorded replay)
**Testing — E2E:** `@playwright/test` (against the Vite dev server), `tauri-driver` + WebdriverIO (against the real built app)
**Lint & format:** `eslint`, `typescript-eslint`, `prettier`; `cargo clippy`, `rustfmt`

## Toolchain pinning
- Policy: **exact-pinned.**
- Pinned tools:
  - **Node.js** — exact version in `.nvmrc`, mirrored in `package.json` `engines`.
  - **Rust** — exact toolchain in `rust-toolchain.toml` (with `clippy` and `rustfmt` components).
  - **npm dependencies** — exact versions (no `^`/`~`) in `package.json`; `package-lock.json` committed; installs use `npm ci`.
  - **Cargo dependencies** — `Cargo.lock` committed.
  - **Tauri CLI** — exact version as a `devDependency`, never a global install.
- Bumping: a toolchain or dependency bump is its own PR, containing nothing else, and must pass the full test suite on all three OSes before merge.

## Instrumentation
- **Logger:** `@tauri-apps/plugin-log`. Rust and the frontend write to one log file in the OS app-log directory, rotated at 5 MB with 3 files kept. Debug builds also log to stdout.
- **Levels:**
  - `ERROR` — an operation failed and the user saw the effect.
  - `WARN` — a fallback engaged (cache served, request retried).
  - `INFO` — lifecycle events: startup, location change, refresh.
  - `DEBUG` — development detail; disabled in release builds.
- **Always logged:**
  - Every Open-Meteo call — endpoint, HTTP status, duration.
  - Every cache fallback — with the age of the data served.
  - Every forecast run — an input summary and the forecasting model version.
  - Every unhandled error or Rust panic.
- **No telemetry.** Nothing leaves the user's machine — no crash reporter, no analytics — unless an ADR introduces it.
- **Privacy:** coordinates are logged rounded to 1 decimal place (≈11 km). Location names are never logged.

## User Feedback Approach
- **Channels:** in-app only — inline status in the UI and non-blocking toasts for background events. OS notifications only for severe-weather alerts the user has opted into. No email, no push service.
- **Tone:** short, plain, friendly. Example: "Showing forecast from 2 hours ago — can't reach the weather service." No jargon, no HTTP codes, no stack traces.
- **Errors:** always say what the user is seeing and what, if anything, they can do. Technical detail goes to the log only.
- **Forecast confidence:** the app's own forecast is always labelled as an estimate, with a simple confidence cue (high / medium / low), so it is never mistaken for the provider's official forecast.
- **Units & locale:** dates and times follow the OS locale. Metric/imperial is a user setting, defaulted from the locale.
- **Accessibility:** colour is never the only carrier of information — weather icons and charts carry text labels — and every action is reachable by keyboard.

## Branching and Environments
- Model: **trunk-based**, per `CONTRIBUTING.md`.
- Branches:
  - `main`.
  - `feature/<feature-id>-<slug>` — one per Feature, cut from `main`.
  - `story/<issue#>-<slug>` — cut from its parent Feature branch and squash-merged back into it.
  - `story/…` / `bug/…` — parentless work, cut from and squash-merged to `main`.
- Environments:
  - **dev** — local `tauri dev` build with debug logging.
  - **CI** — the Azure DevOps pipeline posting the `ci` check on every PR. Runs the test tiers and builds unsigned installers for Windows, macOS, and Linux.
  - **release** — installers for each OS, built from a `v*` tag on `main` and published as a GitHub Release. Releases are code-signed (Windows certificate, Apple Developer ID); until those certificates are provisioned, releases ship unsigned and are marked pre-release. Auto-update is out of scope without an ADR.
  - There is no staging channel.
- Protected: `main` — PR-only, squash-merge, linear history, no force-push, no deletion.

## Git Guardrails
- Never, without explicit user confirmation:
  - `git push --force` (any form).
  - `git reset --hard`.
  - `git clean -fd`.
  - `git branch -D`.
  - `git rebase` on a shared branch.
  - `git commit --no-verify`.
  - Deleting or rewriting tags.
  - A direct push to `main`.
- Hooks:
  - `.githooks/pre-commit` blocks scratch and diff dump files. Enable once per clone: `git config core.hooksPath .githooks`.
  - `/factory-setup-pre-commit` (Husky + lint-staged: Prettier, typecheck, tests) is added once code exists.
  - `/factory-git-guardrails` (Claude Code hooks blocking the destructive commands above) is added once code exists.
- Never committed: `src-tauri/target/`, `node_modules/`, built installers, local SQLite caches, log files.
- **HITL delivery opens and closes its own board state — nothing else will.** No skill in the HITL pipeline (`/factory-to-stories`, `/factory-check-security-design`, `/factory-triage`) sets an assignee, an iteration, or an in-progress state at creation — they deliberately leave a HITL-tagged Story, Bug, or wholly-HITL Feature untouched for a human to clear. So: **before** making any change while hand-delivering one, set `System.AssignedTo` to yourself, `System.IterationPath` to the current sprint, and `System.State` to `Active`. **When** the delivering PR merges and the work item is done, set `System.State` to `Resolved`/`Closed` yourself before ending the session. Skipping either half leaves the item unassigned, out of sprint, and looking untouched for the entire time it's actually being worked.

## Testing & the ratchet
> Seeded from the Factory standard — **opt-out, not opt-in**. Trim a line only with a deliberate, recorded decision; don't start from nothing. Full standard: <https://github.com/enateltd/factory-skills/blob/main/docs/testing-and-the-ratchet-standard.md> · the *why*: <https://github.com/enateltd/factory-skills/blob/main/docs/agentic-e2e-testing-principles.md>.
>
> **Recorded adaptation:** this product contains no LLM. The standard's LLM-specific wording is reinterpreted for live weather data, the product's non-deterministic input. No line is dropped.

- **Test the contract, not the weather.** Assert on the deterministic envelope — the shape of validated data, cache-fallback behaviour, UI state transitions, and forecast output for fixed inputs — never on live weather values.
- **Every seam gets a real-IO test on at least one side.** Mock-on-both-sides only proves the mocks agree; defects live at integration seams — Open-Meteo ↔ `zod` parsing, frontend ↔ Tauri IPC, app ↔ SQLite — not inside modules.
- **Three tiers, each catching a class the others can't.**
  - **Tier 1 — recorded replay.** `vitest` + `msw` replay recorded Open-Meteo responses against a real SQLite file in a temp directory. Costs $0 and runs on every commit.
  - **Tier 2 — live, disposable.** The real built Tauri app, driven by `tauri-driver`, calls live Open-Meteo with a fresh app-data directory per run. Runs on a schedule, bounded by an API-call ceiling rather than a cost ceiling.
  - **Tier 3 — live.** The released app in real use.
- **Platform matrix is non-negotiable for OS-touching code.** Filesystem, credential-store, and IPC code runs Tier 1 + Tier 2 on Windows, macOS, and Linux — not just the dev box's OS.
- **Plan test coverage at the Feature level, not the Story level.** Each Feature declares at kickoff which tiers it needs, which recorded fixtures and substrates must exist, and a Tier-2 API-call ceiling.
- **The ratchet — every Tier-3 defect pins a cheap-tier regression.** A live defect's fix lands with a Tier-1 (or Tier-2) regression that would have caught it, encoding the **class** of defect **bounded by the seam** where it surfaced — not the exact reproducer, and not a project-wide sweep. A Story that surfaced from a live defect carries that ratcheting fixture as an acceptance criterion.
- **Disposable substrate, not shared.** Tier-2 app-data directories and SQLite files are created per run and torn down. Reproducibility comes from a seed (recorded fixtures, a seed script), never persistent state.
- **Assert on observable behaviour, not internal state.** For Tier 2/3 the target is what a user or operator could verify from outside: what the UI renders, the files the app writes, the ordered log events.
