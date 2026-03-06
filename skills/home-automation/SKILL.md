---
name: home-automation
description: "Connects PicoClaw to home automation systems via Home Assistant REST API or MQTT. Lets you control devices, query states, create automations, and trigger scenes by talking to PicoClaw. Generates Go structs for your entity types and registers home_control and home_query as tools. Use when PicoClaw is deployed on your home network alongside a Home Assistant instance."
---

# home-automation

Control your home through PicoClaw. Talk naturally; PicoClaw translates to Home Assistant commands.

## Usage
```
@PicoClaw Turn off the living room lights
@PicoClaw What's the temperature in the bedroom?
@PicoClaw Set the thermostat to 68°F
@PicoClaw Run the "good night" scene
```

## Supported Integrations

| System | Method | Requirements |
|--------|--------|-------------|
| Home Assistant | REST API | HASS_URL + Long-Lived Token |
| MQTT broker | TCP | Any MQTT-compatible devices |
| Direct GPIO | `/dev/gpiochip0` | Relays wired to GPIO pins |

## Files Created
```
tools/home_control.go           # home_control tool (turn on/off, set value)
tools/home_query.go             # home_query tool (read entity state)
integrations/homeassistant.go   # Home Assistant REST client
```

## Files Modified
```
tools/registry.go               # Register home_control, home_query
config/config.go                # Add HASS_URL, HASS_TOKEN
```

## Environment Variables
```
HASS_URL=http://homeassistant.local:8123
HASS_TOKEN=your_long_lived_access_token
```

## Getting a Long-Lived Token

Home Assistant → Profile (bottom left) → Long-Lived Access Tokens → Create Token

## Example Interaction
```
You: Turn off the kitchen lights and set bedroom thermostat to 68
PicoClaw:
  → home_control(entity_id="light.kitchen", action="turn_off")  ✓
  → home_control(entity_id="climate.bedroom", action="set_temperature", value=68)  ✓
Done. Kitchen lights off, bedroom set to 68°F (currently 71°F).
```

## Philosophy
PicoClaw lives on your local network. Home Assistant lives on your local network. No cloud needed for any of this. Your home automation runs when the internet is down — because it should.
