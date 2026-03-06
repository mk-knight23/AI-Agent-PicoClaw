# Security Patrol — PicoClaw

Scheduled security sweeps of your home network and connected devices. Detects new devices, port scans, unusual traffic, and software vulnerabilities.

## Trigger
- **Schedule**: Nightly at 2:00 AM (or configurable via HEARTBEAT)
- **Manual**: `@PicoClaw security-patrol --now`
- **Event-based**: Automatically triggered on new device detection

## What It Checks

### Network Intrusion Detection
- ARP scan: any new MAC addresses since last patrol?
- Port scan detection: any host attempting >20 ports in 5 minutes?
- Unusual outbound connections: new destination IPs for known devices

### Device Vulnerability Scan
- For each known device: check open ports against known-vulnerable services
- Flag devices running services with public CVEs (from NVD database)
- Check firmware versions of router, smart home hubs

### Go Dependency Security (binary analysis)
- `govulncheck` against the running PicoClaw binary
- Reports any known vulnerabilities in compiled Go dependencies

### Access Log Review
- SSH: failed login attempts in the past 24 hours
- Home Assistant: failed auth attempts
- Alert if brute force pattern detected

## Nightly Report

```
🛡️ Security Patrol — 2026-03-05 02:00

Network:
  ✅ No new devices
  ✅ No port scan attempts
  ⚠️  new outbound connection from TV → 142.250.x.x (Google ads, not unusual)

Device scan:
  ✅ Router: firmware current
  ⚠️  Raspberry Pi 2 (192.168.1.55): OpenSSH 7.4 — CVE-2023-38408 (update available)

Go binary:
  ✅ No known vulnerabilities in current binary

Access logs:
  SSH: 3 failed attempts from 45.33.32.156 (Shodan scanner — normal)
  HA: 0 failed attempts
```

## Auto-Response Actions

| Event | Auto-response |
|-------|--------------|
| New device on LAN | Alert immediately via Telegram |
| Port scan detected | Log attacker IP, alert |
| Brute force SSH | Alert + optionally add to iptables block (configurable) |
| Critical CVE on device | Alert with upgrade instructions |
