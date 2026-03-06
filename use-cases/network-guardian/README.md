# Use Case: Network Guardian (PicoClaw)

Turn your $10 board into a dedicated network security assistant.

## Goal
To monitor your local network for unauthorized devices and known security vulnerabilities, providing instant alerts via Telegram.

## Setup

### 1. Identify Network Range
Configure your local network range in `config/network.yaml`.
```yaml
network:
  range: "192.168.1.0/24"
  trusted_macs:
    - "AA:BB:CC:DD:EE:FF" # My Laptop
    - "11:22:33:44:55:66" # RPi Zero
```

### 2. Enable Network Guardian Skill
Ensure the `network-guardian` skill is active.
```yaml
active_skills:
  - network-guardian
```

### 3. Deploy Heartbeat
Enable `security-patrol.md` in the `heartbeats/` directory.

## Features
- **ARP Scanning**: Regularly scans the network to identify all connected IP/MAC addresses.
- **Anomaly Detection**: Alerts if a non-trusted MAC address joins the network.
- **Port Monitoring**: Periodically checks for open ports on internal devices that shouldn't be exposed.
- **DNS Hijack Check**: Verifies that DNS settings haven't been maliciously altered.

## Benefits
- **Efficiency**: Runs passively in the background with minimal CPU/RAM usage.
- **Real-time**: Get notified the second an intruder is detected.
- **Cost**: Replaces expensive hardware firewalls for basic home security monitoring.
