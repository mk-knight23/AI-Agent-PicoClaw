---
name: weather-reporter
description: "Fetches weather data from Open-Meteo (free, no API key required) or OpenWeatherMap and provides current conditions, forecasts, and weather-triggered automations. Can trigger alerts (close windows, turn on heat, water plants) based on forecast conditions. Integrates with home-automation skill if installed. Use when you want PicoClaw to be weather-aware for scheduling and automation."
---

# weather-reporter

PicoClaw knows the weather. Forecasts, alerts, and weather-triggered automations.

## Usage
```
@PicoClaw What's the weather today?
@PicoClaw Will it rain this weekend?
@PicoClaw Alert me on Telegram if temperature drops below freezing
@PicoClaw Turn on the heat if tomorrow's low is below 40°F
```

## Data Sources

| Source | API Key | Data |
|--------|---------|------|
| Open-Meteo | None (free) | Current, hourly, daily forecast |
| OpenWeatherMap | Required (free tier) | Current + forecast |

**Default: Open-Meteo** — works with zero configuration.

## Files Created
```
tools/weather.go                # weather_current and weather_forecast tools
integrations/openmeteo.go       # Open-Meteo REST client
```

## Files Modified
```
tools/registry.go               # Register weather_current, weather_forecast
config/config.go                # Add WEATHER_LAT, WEATHER_LON
```

## Environment Variables
```
WEATHER_LAT=37.7749             # Your latitude
WEATHER_LON=-122.4194           # Your longitude
WEATHER_UNITS=fahrenheit        # or celsius
WEATHER_API_KEY=                # Only for OpenWeatherMap; leave empty for Open-Meteo
```

## Weather-Triggered Automations (with home-automation skill)

```
@PicoClaw Set up a rule: if tomorrow's forecast has rain, remind me to water plants today
@PicoClaw If tonight's low is below 35°F, turn on the pipe heater at 10pm
```

PicoClaw creates a scheduled goroutine that fetches the forecast at 6pm daily and applies the rule.

## Example Output
```
@PicoClaw What's the weather tomorrow?

Tomorrow: San Francisco
  High: 64°F / Low: 52°F
  Conditions: Partly cloudy, 20% chance of rain in the afternoon
  Wind: 12 mph SW
  Sunrise: 6:42am  Sunset: 7:18pm
```

## Philosophy
Open-Meteo is the right default: no sign-up, no rate limits at hobby scale, and the API is clean. Use OpenWeatherMap only if you need historical data or hyper-local stations.
