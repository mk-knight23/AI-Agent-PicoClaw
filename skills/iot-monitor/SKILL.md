---
name: iot-monitor
description: "Sets up continuous monitoring of IoT sensors and devices connected to PicoClaw. Reads from GPIO pins, I2C/SPI sensors, serial ports, or MQTT topics, aggregates readings, detects anomalies using configurable thresholds, and sends alerts via any registered channel (Telegram, Slack, email). Use when PicoClaw is deployed on a device physically connected to sensors."
---

# iot-monitor

Monitor any sensor. PicoClaw reads, aggregates, detects anomalies, and alerts you.

## Usage
```
@PicoClaw iot-monitor --sensor temperature --pin GPIO4 --alert-above 75
@PicoClaw iot-monitor --mqtt broker:1883 --topic sensors/# --alert-on-silence 300
@PicoClaw iot-monitor --i2c-addr 0x48 --type ADS1115 --channels 0,1,2,3
```

## Supported Input Sources

| Source | Interface | Example |
|--------|-----------|---------|
| GPIO pin | `/dev/gpiochip0` | Temperature sensor, motion detector |
| I2C sensor | `i2cdev` | ADS1115 ADC, BME280 temp/humidity |
| SPI sensor | `spidev` | MAX31865 thermocouple |
| Serial port | `/dev/ttyUSB0` | Arduino, GPS, air quality sensor |
| MQTT topic | TCP | Any MQTT-compatible sensor network |

## Alert Conditions

- **Threshold exceeded**: value above/below a numeric limit
- **Silence detected**: no reading for N seconds (dead sensor detection)
- **Rate of change**: value changed by >X% in 60 seconds
- **Pattern**: CSV of expected values with tolerance

## Files Created
```
monitors/<name>_monitor.go      # Goroutine-based sensor poller
config/sensors.yaml             # Sensor definitions and thresholds
MONITOR_SETUP.md                # Hardware wiring guide for your sensor type
```

## Step-by-Step Walkthrough

1. Connect sensor to device
2. `@PicoClaw iot-monitor --sensor <type> --describe <what you connected>`
3. PicoClaw generates the monitor goroutine and wiring guide
4. Build and deploy: `go build && scp picoclaw device:/usr/local/bin/`
5. PicoClaw runs the monitor — alerts fire via your configured channels

## Philosophy
PicoClaw runs directly on the hardware that reads the sensors. No cloud intermediary. A temperature alert fires in <100ms of threshold crossing. That's the point.
