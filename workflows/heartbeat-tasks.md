# Heartbeat Tasks — PicoClaw

The HEARTBEAT system: drop a `HEARTBEAT.md` file anywhere and PicoClaw automatically schedules all tasks defined inside it.

## What is HEARTBEAT?

HEARTBEAT is PicoClaw's zero-config automation scheduler. Instead of writing cron expressions or configuring a task runner, you describe your schedule in plain Markdown.

## HEARTBEAT.md Format

```markdown
INTERVAL: daily
CHANNEL: telegram

- [ ] daily-github-digest 08:00
- [ ] home-lab-monitor 09:00
- [ ] friday-review friday 16:00
- [ ] cross-compile-pipeline monday 06:00
```

PicoClaw watches for `HEARTBEAT.md` files in:
- The current directory
- Your home directory (`~/HEARTBEAT.md`)
- Any directory in `HEARTBEAT_WATCH_DIRS` env var

## How It Works

1. PicoClaw starts and scans for all `HEARTBEAT.md` files
2. For each unchecked task, it parses the schedule (time, day-of-week)
3. Tasks are registered in an in-memory scheduler (no cron daemon needed)
4. At the scheduled time, PicoClaw runs the task and sends output to `CHANNEL`
5. Completed tasks are NOT checked off — they repeat on their schedule

## Task Types

Any PicoClaw skill can be referenced by name:

```markdown
- [ ] daily-github-digest 08:00
- [ ] home-lab-monitor every 5 minutes
- [ ] network-guardian every hour
- [ ] security-patrol sunday 02:00
- [ ] weather-reporter 07:00
- [ ] resource-optimizer every 30 minutes
```

## Advanced: Conditional Tasks

```markdown
- [ ] turn-on-heat when temperature < 40°F
- [ ] alert-motion when motion-sensor == active AND time > 23:00
```

## Multiple HEARTBEAT Files

You can have multiple `HEARTBEAT.md` files in different directories, each with different channels or intervals. PicoClaw aggregates all of them.

```
~/HEARTBEAT.md              → personal tasks → Telegram
~/projects/HEARTBEAT.md     → dev tasks → Slack
/etc/picoclaw/HEARTBEAT.md  → system tasks → email
```
