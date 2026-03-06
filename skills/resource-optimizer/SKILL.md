---
name: resource-optimizer
description: "Monitors and optimizes PicoClaw's own resource usage and the host system's CPU, memory, and storage. Since PicoClaw runs on constrained hardware (256MB RAM typical), this skill tracks goroutine counts, heap size, and system load, terminates idle goroutines, rotates logs, and applies memory pressure relief. Runs as a background goroutine. Use when deploying PicoClaw on low-memory edge devices."
---

# resource-optimizer

Keep PicoClaw lean on constrained hardware: goroutine management, memory pressure relief, log rotation.

## Usage
```
@PicoClaw resource-optimizer --target-ram 80      # Stay under 80MB RAM
@PicoClaw resource-optimizer --status             # Show current resource usage
@PicoClaw resource-optimizer --emergency-gc       # Force GC immediately
```

## What It Does

### Continuous Monitoring (every 30s)
- `runtime.MemStats`: HeapAlloc, HeapInuse, NumGoroutine
- `/proc/meminfo`: system free memory
- `/proc/loadavg`: CPU load
- Disk free on data partition

### Automatic Actions

| Trigger | Action |
|---------|--------|
| HeapAlloc > 80% of target | `runtime.GC()` |
| NumGoroutine > 100 | Log goroutine dump to stderr, alert |
| System free < 20MB | Pause non-critical pollers, notify |
| Log files > 100MB | Rotate and compress with `compress/gzip` |
| Disk free < 500MB | Alert and stop writing verbose logs |

### Status Report
```
@PicoClaw resource-optimizer --status

PicoClaw Resource Status
  Heap: 12.4 MB (target: 80 MB)
  Goroutines: 23
  System RAM free: 128 MB / 256 MB
  Disk free: 1.2 GB
  Uptime: 14d 6h 23m
  GC runs today: 4
```

## Files Created
```
monitors/resource_optimizer.go  # Background resource monitoring goroutine
```

## Environment Variables
```
RESOURCE_TARGET_RAM_MB=80
RESOURCE_LOG_PATH=/var/log/picoclaw
RESOURCE_ALERT_THRESHOLD_GOROUTINES=100
```

## Philosophy
On a 256MB device, the difference between 8MB and 80MB heap usage is the difference between running one agent or ten. Know your budget and stay within it.
