# Friday Review — PicoClaw

Lightweight end-of-week summary. PicoClaw reports on hardware uptime, sensor health, and code activity.

## Trigger
- **Schedule**: Every Friday at 5:00 PM
- **Manual**: `@PicoClaw run friday-review`

## What PicoClaw Reviews

### Hardware Health (if iot-monitor installed)
- Sensor uptime this week (%)
- Any alert events: count and highest severity
- Devices that went offline (and recovered)

### System Stats
- Heap and goroutine counts over the week
- Any memory pressure events
- Binary version running

### Code Activity
- GitHub: PRs merged, issues closed
- Any new releases or cross-compilation targets built

### Network (if network-guardian installed)
- New devices seen on LAN this week
- Any security events

## Example Output

```
📅 Friday Review — Week of Mar 3–7

Hardware:
  ✓ Temperature sensor: 100% uptime
  ✓ HVAC: 1 alert (Tue 2am — temp spike, auto-resolved)
  ✓ Motion: 100% uptime

System stats:
  Peak heap: 18MB / 80MB limit
  Max goroutines: 34
  Uptime: 7d 0h (clean week)

Code:
  • PicoClaw v0.9.1 deployed (cross-compiled to ARM64 + RISC-V)
  • 1 PR merged: "MQTT channel support"

Network:
  • 1 new device: "ESP32-CAM" (208.67.220.0) — added to known list
```
