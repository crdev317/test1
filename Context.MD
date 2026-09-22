# Desktop Weather App

A desktop app that shows the weather provider's forecast for the user's places alongside its own simple, clearly-labelled estimate built from recent weather.

## Language

### Places

**Location**:
A place on Earth that weather is fetched for, identified by coordinates and a display name — the general term, not necessarily a **Saved Location**.
_Avoid_: City, place, spot

**Saved Location**:
A **Location** the user has chosen to keep in their list across sessions.
_Avoid_: Favourite, bookmark, pinned location

**Current Location**:
The device's own position used as a temporary **Location**, never a **Saved Location** unless the user explicitly saves it.
_Avoid_: My location, GPS location, here

**Default Location**:
The one **Saved Location** shown when the app starts.
_Avoid_: Home, primary location

### Weather data

**Weather Variable**:
One measurable aspect of the weather — temperature, precipitation, wind speed, wind gust, humidity, and so on.
_Avoid_: Metric, parameter, field

**Observation**:
The weather at one **Location** for one past hour — always past, never a forecast, even where the provider modelled rather than measured it.
_Avoid_: Reading, measurement, history point, actuals

**Current Conditions**:
A view of the most recent **Observation** for a **Location**, shown as "now" — not a separate kind of data and not a forecast.
_Avoid_: Current weather, live weather

**Provider Forecast**:
The forecast supplied by the weather provider, shown as the official outlook and never altered by the app.
_Avoid_: Official forecast, API forecast, real forecast

**Hourly Forecast**:
The **Provider Forecast** viewed as one set of **Weather Variable** values per hour.
_Avoid_: Hour-by-hour

**Daily Forecast**:
The **Provider Forecast** viewed as one set of **Weather Variable** values per calendar day.
_Avoid_: Day summary, outlook

### The app's forecasting

**App Forecast**:
The app's own labelled short-range estimate (a nowcast) of the next few hours' temperature and **Rain Persistence** at a **Location**, shown alongside — never replacing or adjusting — the **Provider Forecast**.
_Avoid_: Prediction, our forecast, local forecast, AI forecast

**Rain Persistence**:
The **App Forecast**'s judgement of whether rain already falling in the latest **Observation** continues or eases — it never predicts rain starting.
_Avoid_: Rain forecast, chance of rain, precipitation forecast

**Forecasting Model**:
The versioned, deterministic method that turns **Observations** — and only **Observations**, never the **Provider Forecast** — into an **App Forecast**; not a meteorological model and not an AI model.
_Avoid_: Algorithm, predictor, engine, ML model

**Confidence**:
A high / medium / low rating of how far the user should trust an **App Forecast**.
_Avoid_: Accuracy, probability, certainty, score

**Forecast Horizon**:
How far ahead of now a forecast reaches.
_Avoid_: Range, lookahead, window

### Freshness

**Observation Cache**:
The app's local record of fetched **Observations** and **Provider Forecasts**, kept so there is weather to show when the provider is unreachable.
_Avoid_: History, offline store, database

**Refresh**:
Fetching the latest **Observations** and **Provider Forecast** for a **Location**, automatically or on request.
_Avoid_: Sync, reload, update, poll

**Stale Data**:
Weather data shown from the **Observation Cache**, with its age, because a **Refresh** could not reach the provider.
_Avoid_: Old data, cached data, offline data

**Freshness Cutoff**:
The age of the latest **Observation** beyond which no **App Forecast** is produced at all — past it, the app shows only **Stale Data** and says the estimate is unavailable.
_Avoid_: Timeout, expiry, max age

### Alerts

**Severe-Weather Alert**:
The app's own warning — never an official one from a weather authority — raised when the **Provider Forecast** for a **Saved Location** crosses an **Alert Threshold**.
_Avoid_: Weather warning, official alert, notification

**Alert Threshold**:
A limit on a **Weather Variable** whose crossing triggers a **Severe-Weather Alert**.
_Avoid_: Trigger, limit, rule

**Background Watch**:
The app continuing, after its window is closed, to **Refresh** the **Saved Locations** that have **Severe-Weather Alerts** turned on — it exists only while at least one such **Saved Location** does.
_Avoid_: Background mode, daemon, service, always-on

### Preferences

**Unit System**:
The user's choice of metric or imperial display units, which changes how values are shown but never the values themselves.
_Avoid_: Units, measurement system

## Relationships

- A **Location** is either a **Saved Location** or the **Current Location**; the **Current Location** becomes a **Saved Location** only when the user saves it
- Exactly one **Saved Location** is the **Default Location** whenever any **Saved Location** exists
- A **Location** has many **Observations**; its **Current Conditions** is the most recent one
- A **Location** has one **Provider Forecast**, viewed as an **Hourly Forecast** and a **Daily Forecast**
- A **Location** has one **App Forecast**, produced by one version of the **Forecasting Model** from that **Location**'s **Observations**
- The **Provider Forecast** judges the **App Forecast** (through **Confidence**) but never builds it — the two stay independent, so their agreement is real information
- Every **App Forecast** carries exactly one **Confidence**; a **Provider Forecast** carries none
- An **App Forecast** carries **Rain Persistence** only when the latest **Observation** shows rain falling
- The **Provider Forecast** and the **App Forecast** each have their own **Forecast Horizon**
- A **Refresh** fills the **Observation Cache**; a failed **Refresh** means the **Observation Cache** is served as **Stale Data**
- An **App Forecast** exists only while the latest **Observation** is within the **Freshness Cutoff**; **Stale Data** can outlive it, the **App Forecast** cannot
- A **Severe-Weather Alert** is driven by the **Provider Forecast** for a **Saved Location** crossing an **Alert Threshold** — never by the **App Forecast**, and only where the user has opted in for that **Saved Location**
- **Background Watch** runs only while at least one **Saved Location** has **Severe-Weather Alerts** on; with none, closing the window quits the app

## Example dialogue

> **Dev:** "The **App Forecast** says 18° at 5pm but the **Provider Forecast** says 21° — should we average them?"
> **Domain expert:** "No — the **App Forecast** never adjusts the **Provider Forecast**. Show both; the disagreement is what lowers the **Confidence** on ours."
>
> **Dev:** "The provider's been unreachable since this morning. Can the **Forecasting Model** still run on the **Observation Cache**?"
> **Domain expert:** "Only while the latest **Observation** is inside the **Freshness Cutoff**. Past that there's no **App Forecast** at all — just **Stale Data**, with its age, and a note that the estimate is unavailable."
>
> **Dev:** "It's been dry all morning, but the **Provider Forecast** shows a shower at 4pm. Should the **App Forecast** show it too?"
> **Domain expert:** "No — **Rain Persistence** only speaks when rain is already falling. The **App Forecast** never predicts rain starting; that's the **Provider Forecast**'s job."
>
> **Dev:** "**Rain Persistence** says the rain will continue all afternoon — do we raise a **Severe-Weather Alert**?"
> **Domain expert:** "Not from the **App Forecast**. Only the **Provider Forecast** crossing an **Alert Threshold** raises one, and only for a **Saved Location** the user opted in."

## Flagged ambiguities

- "forecast" alone is ambiguous — resolved: always say **Provider Forecast** (the provider's, official, unaltered) or **App Forecast** (the app's own labelled estimate).
- "current weather" could mean a separate live feed, or the provider's 15-minute "current" values — resolved: **Current Conditions** is just the most recent hourly **Observation**; **Observations** are hourly everywhere, so the app behaves the same in every region.
- "alert" could imply an official warning — resolved: the provider publishes no official warnings; a **Severe-Weather Alert** is always the app's own and is worded that way.
- "model" could mean a meteorological or AI model — resolved: the **Forecasting Model** is a simple, versioned, deterministic method, neither.
- "rain forecast" from the app could imply it predicts showers — resolved: the **App Forecast** covers precipitation only as **Rain Persistence**; predicting rain starting is left to the **Provider Forecast**.
- "improving" the **App Forecast** by nudging it towards the **Provider Forecast** — resolved: the **Forecasting Model** takes **Observations** only; otherwise agreement between the two forecasts, and so **Confidence**, becomes circular.
- a "low **Confidence**" **App Forecast** built from hours-old **Observations** — resolved: past the **Freshness Cutoff** no **App Forecast** is shown at all; a guess shown as a number reads as a fact.
- "alerts" could imply the app watches only while open — resolved: **Background Watch** keeps **Refreshing** opted-in **Saved Locations** after the window closes, and exists only because the user opted in.
