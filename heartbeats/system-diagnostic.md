# System Diagnostic Heartbeat

INTERVAL: 1h
CHANNEL: discord/@system_logs

## Purpose
Periodic health check of the PicoClaw runtime and host hardware to ensure stability and early detection of resource exhaustion.

## Checks
- [ ] CPU Load Average (1m, 5m, 15m) < 2.0
- [ ] Memory Usage < 80% (Threshold: 8MB for RPi Zero)
- [ ] List zombie processes `ps aux | grep 'Z' | grep -v grep`
- [ ] Check `/var/log/syslog` for "Out of memory" errors
- [ ] Verify internet connectivity `ping -c 3 8.8.8.8`
- [ ] Report uptime `uptime -p`

## Recovery
- [ ] If Memory > 90%, restart `picoclaw` service
- [ ] If Load > 5.0, send CRITICAL alert to Discord
