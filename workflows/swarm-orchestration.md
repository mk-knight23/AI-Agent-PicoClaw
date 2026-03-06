# Swarm Orchestration — PicoClaw

PicoClaw as swarm orchestrator: coordinate multiple edge devices running PicoClaw instances for distributed sensor processing and home automation.

## Use Case

When you have multiple PicoClaw devices on your LAN (e.g., one per room, one at the router), they can coordinate as a swarm. One device acts as orchestrator; others act as workers.

## Architecture

```
Orchestrator (main Pi)
  ├── Worker: PicoClaw-Bedroom (sensor data)
  ├── Worker: PicoClaw-Garage (door sensor + camera)
  ├── Worker: PicoClaw-Router (network guardian)
  └── Synthesize → Alerts + Home Automation
```

## Communication

Workers communicate over MQTT (zero external service needed if you run `mosquitto` on the orchestrator).

```
MQTT Topic: picoclaw/swarm/<worker-id>/status
MQTT Topic: picoclaw/swarm/<worker-id>/result
MQTT Topic: picoclaw/swarm/orchestrator/command
```

## Swarm Patterns

### Distributed Sensor Aggregation
All worker devices report sensor readings every 60 seconds. Orchestrator detects anomalies that are invisible to individual devices (e.g., temperature gradient across rooms).

### Multi-Room Automation
```
Trigger: Motion in living room at 11pm
Orchestrator: Dim all room lights except living room (sends commands to all workers)
```

### Consensus Alerts
Alert only if 2/3 devices agree on a threshold breach (reduces false positives):
```go
// Consensus config
swarmAlert := swarm.Alert{
    Condition: "temperature > 80",
    Quorum:    2, // 2 out of 3 workers must agree
}
```

## Device Discovery

Devices discover each other via mDNS: `picoclaw-<hostname>.local`. No static IP configuration needed.

## Configuration

```go
// config.go
SwarmMQTTBroker := "192.168.1.1:1883"
SwarmRole        := "orchestrator"  // or "worker"
SwarmDeviceID    := hostname()
```
