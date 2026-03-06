# AI- **Heartbeats**:
  - `heartbeats/system-diagnostic.md`: Continuous health monitoring.
- **Use Cases**:
  - `use-cases/network-guardian`: Security-focused monitoring.
- **Hardware**:
  - `docs/hardware/esp32-setup.md`: specialized MCU guides.
 No Docker, no runtime, binary-only.

## Quick Context
- Language: Go 1.21+, pure Go (no CGo)
- Targets: RISC-V 64, ARM64, ARMv6, MIPS, x86_64
- Primary board: LicheeRV Nano (SG2002, 256MB RAM, $10)
- Boot time: <1 second | RAM usage: <10MB

## Key Skills
- `iot-monitor` — Continuous device uptime + MQTT alerting
- `home-automation` — Home Assistant REST API control
- `network-guardian` — Network scan, firewall verification, anomaly alerts
- `weather-reporter` — Hyperlocal weather + Telegram daily forecast
- `resource-optimizer` — CPU/RAM/temp tracking, OOM prevention
- Plus: add-telegram, add-gmail, add-slack, add-obsidian, add-supabase

## Key Concept: HEARTBEAT
Drop a `HEARTBEAT.md` file anywhere. PicoClaw watches and schedules all tasks inside automatically. No restart needed.

```
INTERVAL: 30m
CHANNEL: telegram/@alerts

- Ping all IoT devices
- Check disk usage > 80%
```

## Cross-Compilation
```bash
GOOS=linux GOARCH=riscv64 go build -o picoclaw-riscv64 ./cmd/agent
```
Deploy: `scp binary pi@device:/usr/local/bin/`
