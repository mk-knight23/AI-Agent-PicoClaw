---
name: network-guardian
description: "Monitors network activity from PicoClaw: scans for new devices on the LAN, detects port scans and unusual traffic patterns, monitors bandwidth by device, and sends alerts for security events. Uses pure Go net libraries (no nmap dependency). Generates a NETWORK_REPORT.md on schedule. Use when PicoClaw is deployed as a network monitor on your home or office LAN."
---

# network-guardian

LAN monitoring from PicoClaw: device discovery, security alerts, bandwidth stats — pure Go.

## Usage
```
@PicoClaw network-guardian --scan-interval 300     # Scan every 5 minutes
@PicoClaw network-guardian --alert-new-devices     # Alert when unknown device joins
@PicoClaw network-guardian --watch-host 192.168.1.10  # Monitor specific host
@PicoClaw network-guardian --report                # Generate NETWORK_REPORT.md now
```

## What It Monitors

### Device Discovery
- ARP scan of the local subnet every N seconds
- Compares against known-devices list
- Alerts when new MAC address appears
- Resolves hostnames via mDNS

### Security Events
- Port scan detection: >20 unique ports from single host in 60s
- SSH brute force: >10 failed auth attempts to port 22
- Unusual outbound traffic: new destination IPs for known hosts

### Bandwidth (via `/proc/net/dev` on Linux)
- Per-device bytes in/out (requires traffic shaping or SNMP on router)
- Alert when device exceeds N MB in 5 minutes

## Files Created
```
monitors/network_guardian.go    # Main monitoring goroutine
monitors/device_registry.go     # Known-device persistence
NETWORK_REPORT.md               # Scheduled network status report
```

## Environment Variables
```
NETWORK_SUBNET=192.168.1.0/24   # LAN subnet to monitor
NETWORK_ALERT_CHANNEL=telegram  # Channel for security alerts
NETWORK_KNOWN_DEVICES=/etc/picoclaw/known-devices.json
```

## Philosophy
Network monitoring belongs on your LAN, not in the cloud. PicoClaw has access to the raw network — no traffic leaves your network to report on your network.
