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
The app's own labelled short-range estimate (a nowcast) of the next few hours' weather at a **Location**, shown alongside — never replacing or adjusting — the **Provider Forecast**.
_Avoid_: Prediction, our forecast, local forecast, AI forecast

**Forecasting Model**:
The versioned, deterministic method that turns **Observations** into an **App Forecast** — not a meteorological model and not an AI model.
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

### Alerts

**Severe-Weather Alert**:
The app's own warning — never an official one from a weather authority — raised when the **Provider Forecast** for a **Saved Location** crosses an **Alert Threshold**.
_Avoid_: Weather warning, official alert, notification

**Alert Threshold**:
A limit on a **Weather Variable** whose crossing triggers a **Severe-Weather Alert**.
_Avoid_: Trigger, limit, rule

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
- Every **App Forecast** carries exactly one **Confidence**; a **Provider Forecast** carries none
- The **Provider Forecast** and the **App Forecast** each have their own **Forecast Horizon**
- A **Refresh** fills the **Observation Cache**; a failed **Refresh** means the **Observation Cache** is served as **Stale Data**
- A **Severe-Weather Alert** is driven by the **Provider Forecast** for a **Saved Location** crossing an **Alert Threshold** — never by the **App Forecast**, and only where the user has opted in for that **Saved Location**

## Example dialogue

> **Dev:** "The **App Forecast** says 18° at 5pm but the **Provider Forecast** says 21° — should we average them?"
> **Domain expert:** "No — the **App Forecast** never adjusts the **Provider Forecast**. Show both; the disagreement is what lowers the **Confidence** on ours."
>
> **Dev:** "The provider's been unreachable for three hours. Can the **Forecasting Model** still run?"
> **Domain expert:** "Yes, on the **Observations** in the **Observation Cache** — but everything on screen is **Stale Data** and says how old it is."
>
> **Dev:** "The **App Forecast** is predicting 90 km/h gusts — do we raise a **Severe-Weather Alert**?"
> **Domain expert:** "Not from the **App Forecast**. Only the **Provider Forecast** crossing an **Alert Threshold** raises one, and only for a **Saved Location** the user opted in."

## Flagged ambiguities

- "forecast" alone is ambiguous — resolved: always say **Provider Forecast** (the provider's, official, unaltered) or **App Forecast** (the app's own labelled estimate).
- "current weather" could mean a separate live feed — resolved: **Current Conditions** is just the most recent **Observation**.
- "alert" could imply an official warning — resolved: the provider publishes no official warnings; a **Severe-Weather Alert** is always the app's own and is worded that way.
- "model" could mean a meteorological or AI model — resolved: the **Forecasting Model** is a simple, versioned, deterministic method, neither.
