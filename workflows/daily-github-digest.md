# Daily GitHub Digest — PicoClaw

PicoClaw delivers a terse GitHub digest optimized for reading on a small terminal or mobile notification.

## Trigger
- **Schedule**: Daily at 8:00 AM
- **Manual**: `@PicoClaw run daily-github-digest`
- **HEARTBEAT.md**: Add `daily-github-digest 08:00` to any HEARTBEAT.md file

## Prerequisites
- `GITHUB_TOKEN` env var
- Repos in `config/repos.json`
- Channel installed (Telegram recommended for mobile)

## What PicoClaw Reports

- New issues and PRs (with direct links)
- Merged PRs (confirmation)
- CI failures on main/master
- New releases from watched repos

PicoClaw keeps it short. No noise, no summaries — just the facts.

## Example Output

```
GitHub 2026-03-05

your-org/edge-service
  NEW issue #45: IMAP auth failing on ARM64
  PR #39 merged: "Reduce binary size by 2MB"
  CI FAIL: main (test_network_guardian)

picoclaw/picoclaw
  Release v0.9.1: hot path optimization
```

## HEARTBEAT Integration

```markdown
<!-- ~/HEARTBEAT.md -->
INTERVAL: daily
CHANNEL: telegram

- [ ] daily-github-digest 08:00
- [ ] home-lab-monitor 09:00
```

PicoClaw reads this file on startup and schedules all tasks automatically.

## Configuration

```json
{
  "repos": ["your-org/edge-service", "picoclaw/picoclaw"],
  "digest_time": "08:00",
  "terse": true
}
```
