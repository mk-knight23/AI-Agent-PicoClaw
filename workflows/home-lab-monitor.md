# Home Lab Monitor — PicoClaw

Comprehensive monitoring for your home lab: servers, services, network, power, and temperature — all from a single PicoClaw instance.

## Trigger
- **Continuous**: Runs as a set of goroutines monitoring in real time
- **Report**: `@PicoClaw home-lab-monitor report`
- **HEARTBEAT**: `home-lab-monitor 09:00` in your `HEARTBEAT.md`

## What It Monitors

### Services (HTTP health checks)
Polls a list of URLs every 60 seconds:
```json
{
  "services": [
    { "name": "Plex", "url": "http://192.168.1.10:32400/health" },
    { "name": "Vaultwarden", "url": "https://vault.local/alive" },
    { "name": "Home Assistant", "url": "http://homeassistant.local:8123/api/" }
  ]
}
```

### Servers (ping + SSH)
- Ping every 5 minutes
- SSH-based disk/CPU/memory check every 15 minutes
- Alert if server unreachable for >10 minutes

### Power (if UPS agent connected via NUT protocol)
- Battery charge %
- Input voltage
- Alert on battery power (utility failure)

### Temperature (ambient room sensors)
- Compares against room-specific thresholds
- Alert if server room temps exceed safe range

## Alert Examples

```
⚡ Alert — Plex server unreachable (3 min)
Check: http://192.168.1.10:32400/health
Last seen: 14:23

🌡️ Alert — Server room: 84°F (threshold: 80°F)
  Trend: +4°F in 20 minutes
  Action: Check AC/fan immediately
```

## Dashboard (terminal)

```
@PicoClaw home-lab-monitor dashboard

Home Lab Status — 2026-03-05 14:30

Services:     5/5 healthy
Servers:      3/3 online
  server-1:   CPU 23% | RAM 4.2/16GB | Disk 234/500GB
  server-2:   CPU 8%  | RAM 1.1/8GB  | Disk 89/200GB
  router:     CPU 12% | WAN 94Mbps ▼ 23Mbps ▲
Network:      All clear (no anomalies)
Temperature:  Server room 72°F ✓ | Living room 68°F ✓
UPS:          On utility | Battery 100% | 14min runtime
```
