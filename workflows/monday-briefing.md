# Monday Briefing — PicoClaw

Monday morning hardware and software readiness check for the week ahead.

## Trigger
- **Schedule**: Every Monday at 8:00 AM
- **Manual**: `@PicoClaw run monday-briefing`

## What PicoClaw Checks

### Hardware Readiness
- All sensors responding (IMAP for each registered device)
- Devices that rebooted over the weekend
- Any scheduled maintenance coming this week

### Software Status
- Current binary version vs latest release
- Any security advisories for Go dependencies
- HEARTBEAT.md tasks scheduled for this week

### Network
- Bandwidth usage over the weekend
- Any unknown MAC addresses on LAN

## Example Output

```
☕ Monday Briefing — March 10

Hardware:
  ✅ Temperature: online, reading 68°F
  ✅ HVAC controller: online
  ⚠️  Motion sensor (bedroom): offline since Sat — check physical connection

Software:
  Current: v0.9.1 | Latest: v0.9.2 — update available
  No security advisories

Network:
  Clean weekend. 2 devices rebooted and rejoined (normal).

HEARTBEAT tasks this week:
  Mon: daily-github-digest, home-lab-monitor
  Wed: network-guardian weekly-scan
  Fri: friday-review
```
