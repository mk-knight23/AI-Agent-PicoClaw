---
name: code-reviewer
description: "Static analysis + AI review on Go code targeting edge and embedded systems. Runs go vet, staticcheck, gosec, and errcheck — then an AI pass focused on concurrency safety, goroutine leaks, context propagation, and low-memory patterns critical for <10MB edge deployments. Outputs CODE_REVIEW.md with CRITICAL/HIGH/MEDIUM/LOW severity. Adapted from Nanobot's Python code-reviewer for Go/edge runtime."
---

# code-reviewer

Go static analysis + AI review. Edge-focused: concurrency, memory, and goroutine safety.

## Usage
```
@PicoClaw code-reviewer --path ./cmd/
@PicoClaw code-reviewer --diff HEAD~1..HEAD
@PicoClaw code-reviewer --file monitors/temp_monitor.go
@PicoClaw code-reviewer --strict       # Fail on MEDIUM+ issues
```

## What It Runs

| Tool | Purpose |
|------|---------|
| `go vet` | Suspicious constructs, format string issues |
| `staticcheck` | Deprecated APIs, dead code, correctness |
| `gosec` | Security: hardcoded creds, path traversal, G-rules |
| `errcheck` | Unchecked errors (critical on edge: silent failures) |
| `goleak` | Goroutine leak detection in tests |
| AI review | Goroutine safety, context propagation, memory patterns |

## Files Created
```
CODE_REVIEW.md                  # Full report with severity-rated findings
```

## Edge/PicoClaw-Specific Checks
- **Goroutine leaks**: channels not closed, goroutines without done signals
- **Memory allocation**: heap escapes on hot paths in embedded code
- **Context propagation**: missing `ctx` in long-running operations
- **Signal handling**: missing `SIGTERM` graceful shutdown
- **Cross-compilation**: OS-specific code paths (build tags)
- **Binary size**: imports that bloat the binary beyond <10MB target

## Sample Report
```
## Summary
CRITICAL: 0 | HIGH: 1 | MEDIUM: 3 | LOW: 5

## HIGH Issues
### monitors/temp_monitor.go:47 — Unchecked error on sensor read
**Issue**: `sensor.Read()` return value not checked. Silent failure on disconnect.
**Fix**: `val, err := sensor.Read(); if err != nil { return fmt.Errorf(...) }`

## MEDIUM Issues
### cmd/agent.go:112 — Goroutine without done signal
...
```

## Exit Codes (for CI)
```bash
picoclaw code-reviewer --path ./... --strict
# Exit 0: no MEDIUM+ issues
# Exit 1: issues found above threshold
```

## Philosophy
On a $10 device with 256MB RAM, silent errors and goroutine leaks don't get noticed until the device is rebooted at 3 AM. Every unchecked error is a ticking clock.
