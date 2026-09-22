# PRD — Desktop Weather App

## Problem Statement

I want to know what the weather is doing where I am and where I care about — right now, over the next few hours, and over the next few days — from a fast, private app on my desktop, without opening a browser full of ads and trackers.

Existing weather apps give me a single forecast and ask me to trust it. I can't tell how much to trust it for the next couple of hours, which is exactly when I'm deciding whether to leave now or wait for the rain to ease. When my connection drops, most apps show a blank screen or an error instead of the last weather they knew. And if I want to be warned about a storm overnight, I have to keep a browser tab open or install a phone app that tracks me.

## Solution

A desktop app for Windows, macOS and Linux that shows, for each of my **Locations**:

- **Current Conditions** — the most recent hourly **Observation**, labelled with the hour it describes.
- The **Provider Forecast** — the weather provider's official outlook, as an **Hourly Forecast** and a **Daily Forecast**, never altered by the app.
- The **App Forecast** — the app's own short-range nowcast of the next 6 hours: temperature, plus **Rain Persistence** (whether rain already falling will continue or ease). It is always labelled as an estimate, always carries a **Confidence** (high / medium / low), and is built from **Observations** only, so where it agrees or disagrees with the **Provider Forecast** tells me something real.

When the weather provider can't be reached, the app keeps showing the last weather it knew as **Stale Data**, with its age. Once that data passes the **Freshness Cutoff**, the app withholds the **App Forecast** entirely rather than presenting a guess as a number.

I can keep **Saved Locations**, pick a **Default Location** to open on, and use my **Current Location** where my operating system can provide it. I can opt any **Saved Location** into **Severe-Weather Alerts**, with **Alert Thresholds** that start from sensible defaults and that I can adjust for my climate. With alerts on, closing the window leaves the app running in the system tray (**Background Watch**) so I'm warned even when the window is closed. Nothing about me leaves my machine except the weather requests themselves.

## Requirements

### Locations

1. As a user, I want to search for a place by name, so that I can see its weather without knowing its coordinates.
2. As a user, I want search results to show enough detail (region, country) to tell same-named places apart, so that I pick the right one.
3. As a user, I want to save a searched **Location** as a **Saved Location**, so that I can return to it without searching again.
4. As a user, I want my **Saved Locations** to persist across restarts and upgrades, so that I never lose my setup.
5. As a user, I want to rename, reorder and remove **Saved Locations**, so that my list stays the way I like it.
6. As a user, I want to mark one **Saved Location** as my **Default Location**, so that the app opens on it.
7. As a user, when I remove my **Default Location**, I want the app to make it obvious that no default is set (or choose the next one predictably), so that startup never becomes surprising.
8. As a user, I want the app to use my **Current Location** from my operating system's location services when I grant permission, so that I can see local weather without searching.
9. As a user who has refused or doesn't have OS location services, I want the app to work fully through search, so that I'm never blocked.
10. As a privacy-conscious user, I want the app never to guess my position from my network or IP address, so that my location isn't sent to a third party or guessed wrongly.
11. As a user, I want to save my **Current Location** as a **Saved Location** if I choose, so that I can keep a place I'm visiting.
12. As a user, I want the app at startup to show my **Default Location**, or — with no **Saved Location** — my **Current Location**, or — with neither — a search prompt, so that startup is predictable and never shows a made-up place.
13. As a traveller with a **Default Location**, I want my **Current Location** one click away, so that I can check where I am without changing my default.
14. As a user, I want to switch between **Locations** quickly, so that I can compare places.

### Current Conditions and the Provider Forecast

15. As a user, I want to see **Current Conditions** for the selected **Location**, labelled with the hour they describe, so that I know how current "now" is.
16. As a user, I want the **Hourly Forecast** for the coming hours, so that I can plan my day.
17. As a user, I want the **Daily Forecast** for the coming days (high, low, precipitation, conditions), so that I can plan my week.
18. As a user, I want the **Provider Forecast** shown as the provider's own outlook, never modified by the app, so that I know exactly whose forecast I'm reading.
19. As a user, I want key **Weather Variables** — temperature, precipitation, wind speed, wind gust, humidity — shown clearly, so that I get the picture at a glance.
20. As a user, I want the forecast shown as a chart as well as numbers, so that I can see trends quickly.
21. As a keyboard or screen-reader user, I want every chart and weather icon to carry text labels and every action to be keyboard-reachable, so that the app is usable without a mouse or without colour.

### The App Forecast

22. As a user, I want an **App Forecast** of temperature for each of the next 6 hours, so that I get a second, independent view of the short term.
23. As a user caught in the rain, I want **Rain Persistence** to tell me whether the rain is likely to continue or ease, so that I can decide when to leave.
24. As a user, I want **Rain Persistence** shown only when rain is actually falling, and never a prediction of rain starting, so that the app never pretends to see showers coming.
25. As a user, I want the **App Forecast** always labelled as an estimate and visually distinct from the **Provider Forecast**, so that I never confuse the two.
26. As a user, I want every **App Forecast** to carry a **Confidence** of high, medium or low, so that I know how far to trust it.
27. As a user, I want **Confidence** to fall as the **Forecast Horizon** lengthens, as recent **Observations** become patchier, and as the **App Forecast** disagrees with the **Provider Forecast**, so that the rating reflects real uncertainty.
28. As a user, I want the **App Forecast** withheld — with a plain "estimate unavailable: no recent weather data" message — once the latest **Observation** is older than the **Freshness Cutoff**, so that I'm never shown a guess dressed as a number.
29. As a curious user, I want to see which version of the **Forecasting Model** produced an **App Forecast**, so that changes in behaviour are traceable.
30. As a user, I want the **App Forecast** to be the same every time for the same weather data, so that it behaves predictably.

### Freshness and offline behaviour

31. As a user, I want the app to **Refresh** automatically on a sensible schedule while open, so that the weather stays current without effort.
32. As a user, I want to **Refresh** on demand, so that I can check for the latest data before I go out.
33. As a user whose connection has dropped, I want the app to keep showing the last weather it had as **Stale Data**, so that I still have something useful.
34. As a user viewing **Stale Data**, I want it clearly marked with its age ("from 2 hours ago"), so that I know it may be out of date.
35. As a user, I want the app never to crash or show a blank screen because the weather provider is unreachable or returns something unexpected, so that it's dependable.
36. As a user, I want the app to recover automatically when the connection returns, so that I don't have to restart it.
37. As a user, I want error messages in plain language that say what I'm seeing and what I can do, so that I'm not confronted with codes or stack traces.

### Severe-Weather Alerts and Background Watch

38. As a user, I want to opt a **Saved Location** into **Severe-Weather Alerts**, so that I'm warned about dangerous weather where I care about.
39. As a user, I want alerts off by default for every **Saved Location**, so that the app only notifies me when I've asked it to.
40. As a user, I want each opted-in **Saved Location** to start with sensible default **Alert Thresholds** (for example gusts, heavy rain, extreme heat and cold), so that alerts work immediately.
41. As a user in a windy or hot climate, I want to adjust each **Alert Threshold** per **Saved Location**, so that alerts match what's actually unusual where I live.
42. As a user, I want a **Severe-Weather Alert** delivered as an OS notification when the **Provider Forecast** for an opted-in **Saved Location** crosses an **Alert Threshold**, so that I see it even when the app isn't in front.
43. As a user, I want each alert worded clearly as the app's own warning, not an official one from a weather authority, so that I'm not misled about its source.
44. As a user, I want to be alerted once per event rather than on every **Refresh**, so that alerts don't become noise.
45. As a user, I want alerts driven only by the **Provider Forecast**, never the **App Forecast**, so that warnings rest on the stronger forecast.
46. As a user with alerts on, I want closing the window to leave the app running in the system tray (**Background Watch**), so that I'm warned overnight without keeping the window open.
47. As a user with no alerts on, I want closing the window to quit the app, so that nothing runs in the background I didn't ask for.
48. As a user, I want to quit the app completely from the tray menu, so that I stay in control.
49. As a user, I want the option to start the app in the tray when I log in, so that **Background Watch** survives a restart.
50. As a user, I want **Background Watch** to use little CPU, memory and network, so that it doesn't slow my machine or drain my battery.

### Preferences

51. As a user, I want to choose metric or imperial units (**Unit System**), defaulted from my locale, so that values read naturally to me.
52. As a user, I want dates and times in my operating system's locale and my **Location**'s timezone handled correctly, so that "3pm" means what I expect.
53. As a user, I want my preferences to persist across restarts and upgrades, so that I set them once.

### Privacy, security and trust

54. As a user, I want nothing about me — no analytics, no crash reports, no location — sent anywhere except the weather requests themselves, so that the app respects my privacy.
55. As a user, I want the app's local logs to hold only coarse coordinates and never my **Location** names, so that my logs don't expose where I go.
56. As a user, I want installers for Windows, macOS and Linux that are signed once certificates are available, so that I can install with confidence.
57. As a user, I want the app to start quickly and stay light on memory, so that it feels like a native desktop tool.

## Implementation Decisions

The engineering contract is `Technical-Context.MD`; the domain language is `Context.MD`. The decisions below are the ones specific to this product.

### Modules

Four deep, pure modules hold the product's logic; five modules sit at its edges.

1. **Forecasting Model** (pure). Input: a **Location**'s recent hourly **Observations** and the current time. Output: either an **App Forecast** — an hourly temperature series across the 6-hour **Forecast Horizon**, **Rain Persistence** when the latest **Observation** shows rain falling, and a **Confidence** — or an explicit "unavailable" result when the latest **Observation** is past the **Freshness Cutoff**. It carries a version identifier. No network, filesystem, clock or randomness inside it.
2. **Alert Evaluator** (pure). Input: a **Provider Forecast**, a **Saved Location**'s **Alert Thresholds**, and the alerts already raised. Output: the **Severe-Weather Alerts** to raise now, de-duplicated per event.
3. **Startup Location Resolver** (pure). Input: the **Saved Locations**, the **Default Location**, and whether a **Current Location** is available. Output: the **Default Location**, else the **Current Location**, else "prompt for search".
4. **Unit Formatter** (pure). Converts stored **Weather Variable** values into the chosen **Unit System** for display. It never alters stored values.
5. **Weather Provider adapter**. The only code that talks to Open-Meteo: hourly **Observations** (recent past hours), the **Provider Forecast** (hourly and daily), and Location search (geocoding). Every response is parsed at the boundary into domain types; a malformed response fails cleanly rather than propagating.
6. **Weather Repository**. Owns the **Observation Cache** and **Refresh**. Refreshing a **Location** returns fresh data, or **Stale Data** with its age when the provider is unreachable — it never throws at the UI.
7. **Location Store**. **Saved Locations**, the **Default Location**, per-**Saved Location** alert opt-in and **Alert Thresholds**, and the **Current Location** via OS location services (Rust side). No IP-based geolocation.
8. **Background Watch**. System-tray lifecycle and the **Refresh** schedule for opted-in **Saved Locations**. It exists only while at least one **Saved Location** has alerts on; with none, closing the window quits the app.
9. **UI**. React views for **Current Conditions**, the **Hourly Forecast** and **Daily Forecast**, the **App Forecast** with **Confidence**, **Stale Data** indicators, preferences and alert settings.

### Domain rules the modules enforce

- The **Forecasting Model** takes **Observations** only; the **Provider Forecast** is never an input. The **Provider Forecast** is used only to judge the **App Forecast**, through **Confidence**. This keeps the two forecasts independent, so their agreement is real information. (Recorded for a provisional ADR on the forecasting Feature.)
- **Observations** are hourly in every region. The provider's 15-minute "current" values are not used, so the app behaves the same everywhere.
- **Current Conditions** is the most recent hourly **Observation**, not a separate data feed.
- **Rain Persistence** speaks only when rain is falling in the latest **Observation**; the **App Forecast** never predicts rain starting.
- Past the **Freshness Cutoff**, no **App Forecast** is produced; **Stale Data** can still be shown.
- **Severe-Weather Alerts** are driven by the **Provider Forecast** for opted-in **Saved Locations** only.

### Values the Spec must fix

These are deliberately not settled here; each Feature's Spec sets them:

- The **Freshness Cutoff** age.
- The automatic **Refresh** interval, while open and under **Background Watch**.
- The default **Alert Thresholds** (initial candidates: gusts ≥ 70 km/h, heavy rain ≥ 10 mm/h, temperature ≥ 35 °C or ≤ −10 °C).
- The **Confidence** banding rules and the **Forecasting Model**'s method for temperature.
- How many past hours of **Observations** the **Forecasting Model** needs, and so how much the **Observation Cache** keeps.

## Testing Decisions

Tests follow the three-tier model and the ratchet in `Technical-Context.MD`.

- **A good test checks behaviour you can see from outside, not implementation.** Pure modules are tested through their inputs and outputs. Edge modules are tested through what they return and what they write. The UI is tested through what the user sees. Tests never assert on live weather values: they assert on the shape of the data, fallback behaviour and deterministic outputs.
- **Every module is tested:**
  - **Forecasting Model:** Tier 1. Fixed **Observation** series produce fixed **App Forecasts**. Tests cover the edges: **Rain Persistence** present only when rain is falling, the "unavailable" result at the **Freshness Cutoff**, **Confidence** falling with horizon, gaps and disagreement, and the same result on repeated runs.
  - **Alert Evaluator:** Tier 1. Threshold crossings, per-event de-duplication, and thresholds that differ per **Saved Location**.
  - **Startup Location Resolver:** Tier 1. All combinations of Default, Saved and Current Location availability.
  - **Unit Formatter:** Tier 1. Metric and imperial conversion and rounding, with stored values never changed.
  - **Weather Provider adapter:** Tier 1 against recorded Open-Meteo responses (`msw`), including malformed and error responses. Tier 2 against live Open-Meteo, within the API-call ceiling.
  - **Weather Repository:** Tier 1 with a real temporary SQLite file. Covers **Refresh** success, provider-unreachable leading to **Stale Data** with the correct age, and the **Freshness Cutoff** hand-off to the **Forecasting Model**.
  - **Location Store:** Tier 1 with real temporary storage. Tier 2 on all three OSes for persistence and OS location services (granted, refused, unavailable).
  - **Background Watch:** Tier 2 against the real built app on all three OSes. Covers tray on close when alerts are on, quit on close when they're off, and quit from the tray.
  - **UI:** a small set of Playwright tests covering the estimate label on the **App Forecast**, the **Stale Data** age indicator, the "estimate unavailable" message, and keyboard reachability. No snapshot tests.
- **Seams:** every seam has a real-I/O test on at least one side:
  - Open-Meteo ↔ adapter
  - adapter ↔ Repository
  - frontend ↔ Tauri IPC
  - app ↔ SQLite
  - app ↔ OS location services
  - app ↔ OS notifications
- **Prior art:** none. This is a new project, so these tests set the patterns later ones follow.

## Out of Scope

- An **App Forecast** beyond the next few hours, or covering **Weather Variables** other than temperature and **Rain Persistence**.
- Predicting rain starting.
- Official weather warnings from a weather authority (the provider publishes none).
- Weather providers other than Open-Meteo.
- IP-based or network-based location detection.
- Telemetry, analytics and crash reporting.
- Auto-update of the installed app.
- Accounts, sync across devices and cloud storage.
- Mobile apps.
- Commercial distribution, which would first need a paid Open-Meteo plan.

## Further Notes

- **Licence:** Open-Meteo's free tier is for non-commercial use only. Commercial distribution needs a paid plan first.
- **Provisional ADR owed:** the rule that the **Forecasting Model** takes **Observations** only, never the **Provider Forecast**. It will be attached to the forecasting Feature once `/factory-roadmap` creates it.
- **Engineering follow-up:** "no IP-based geolocation" should also be stated in `Technical-Context.MD`, which is human-owned.
- **Code signing:** releases ship unsigned and marked pre-release until the Windows certificate and Apple Developer ID are provisioned.
