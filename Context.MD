# Domain Glossary

<!-- Places -->

## Current Location
The device's own position, detected automatically, used as a Location. Temporary: it is not a Saved Location unless the user saves it.

## Default Location
The one Saved Location shown when the app starts. Exactly one Saved Location is the Default Location whenever any Saved Location exists.

## Location
A place on Earth that weather is fetched for, identified by coordinates and a display name. It comes from a search or from the device's position (the Current Location). The general term — not necessarily a Saved Location.

## Saved Location
A Location the user has chosen to keep in their list, so it persists across sessions. Not the Current Location, unless the user has explicitly saved that position.

<!-- Weather data -->

## Current Conditions
The latest weather at a Location, shown as "now". A view of the most recent Observation for that Location — not a separate kind of data, and not a forecast.

## Daily Forecast
A Provider Forecast expressed as one set of Weather Variable values per calendar day (for example, a high, a low, and total precipitation). A coarser view of the same Provider Forecast as the Hourly Forecast.

## Hourly Forecast
A Provider Forecast expressed as one set of Weather Variable values per hour. A finer view of the same Provider Forecast as the Daily Forecast.

## Observation
The weather at one Location for one past hour, holding a value for each Weather Variable. Observations are the input to the Forecasting Model. An Observation always describes a time that has already passed — it is never a forecast. Historical values the weather provider reports for past hours count as Observations, even where the provider modelled them rather than measured them at a station.

## Provider Forecast
The forecast supplied by the weather provider (Open-Meteo), shown as the official outlook, as an Hourly Forecast and a Daily Forecast. The app never alters it. Not the App Forecast.

## Weather Variable
One measurable aspect of the weather — temperature, precipitation, wind speed, wind gust, humidity, and so on. Observations, Provider Forecasts, App Forecasts and Alert Thresholds are all expressed in Weather Variables.

<!-- The app's forecasting -->

## App Forecast
The app's own estimate of upcoming weather at a Location, produced by the Forecasting Model from recent Observations. Always shown alongside the Provider Forecast, labelled as an estimate and carrying a Confidence. It never replaces, adjusts, or feeds into the Provider Forecast.

## Confidence
A rating — high, medium or low — of how far the user should trust an App Forecast. It rises with a shorter Forecast Horizon, more complete recent Observations, and closer agreement between the App Forecast and the Provider Forecast. Applies only to the App Forecast; the Provider Forecast carries no Confidence.

## Forecast Horizon
How far ahead of now a forecast reaches (for example, "the next 24 hours"). The Provider Forecast and the App Forecast each have their own Forecast Horizon.

## Forecasting Model
The deterministic method that turns Observations into an App Forecast. It carries a version, so any App Forecast can be traced to the method that produced it. Not a meteorological weather model and not an AI model.

<!-- Freshness -->

## Observation Cache
The app's local record of the Observations and Provider Forecasts it has fetched, kept so the app still has weather to show when the provider is unreachable. It is the source of Stale Data.

## Refresh
Fetching the latest Observations and Provider Forecast for a Location from the weather provider, which then produces a new App Forecast. It happens automatically or when the user asks.

## Stale Data
Weather data shown from the Observation Cache because a Refresh could not reach the weather provider. Always shown with its age. Not wrong data — out-of-date data, labelled as such.

<!-- Alerts -->

## Alert Threshold
A limit on a Weather Variable (for example, wind gusts above a given speed) that, when crossed by the Provider Forecast for a Saved Location, triggers a Severe-Weather Alert.

## Severe-Weather Alert
A notice the app raises itself when the Provider Forecast for a Saved Location crosses an Alert Threshold. The user opts in per Saved Location. It is the app's own warning, never an official warning from a weather authority, and is always worded that way. Driven by the Provider Forecast, never the App Forecast.

<!-- Preferences -->

## Unit System
The user's choice of metric or imperial units for displaying Weather Variables, defaulted from the device's locale. It changes how values are shown, never the values themselves.
