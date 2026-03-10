---
name: deploy-everywhere
description: "Cross-compile PicoClaw Go binaries for all supported hardware targets and deploy them in one command. Supports: LicheeRV Nano (RISC-V), Raspberry Pi Zero (ARMv6), Pi 3/4/5 (ARM64), MIPS routers, old Android phones (ARM64), and x86_64 servers. Generates platform-specific init scripts (systemd/OpenWrt procd/Android service), then SCP-deploys and restarts. Adapted from OpenClaw's multi-platform deployment skill for Go cross-compilation."
---

# deploy-everywhere

Cross-compile for every target. Deploy to all hardware in one command.

## Usage
```
@PicoClaw deploy-everywhere --target lichee-rv-nano
@PicoClaw deploy-everywhere --target all                   # Build + deploy all targets
@PicoClaw deploy-everywhere --target rpi-zero --host pi@192.168.1.42
@PicoClaw deploy-everywhere --build-only --target arm64    # Just build, don't deploy
```

## Supported Targets

| Target | GOOS/GOARCH | Device | Deploy Method |
|--------|-------------|--------|---------------|
| `lichee-rv-nano` | `linux/riscv64` | SG2002, 256MB | SCP + systemd |
| `rpi-zero` | `linux/arm/v6` | BCM2835 | SCP + systemd |
| `rpi-arm64` | `linux/arm64` | Pi 3/4/5 | SCP + systemd |
| `router` | `linux/mips` | OpenWrt routers | SCP + procd |
| `android` | `linux/arm64` | Recycled phone (Termux) | ADB push + service |
| `server` | `linux/amd64` | Any x86_64 | SCP + systemd |
| `wasm` | `wasip1/wasm` | Browser/Deno | Bundle |

## Build Pipeline
```bash
# What deploy-everywhere runs internally
GOOS=linux GOARCH=riscv64 go build -ldflags="-s -w" -o dist/picoclaw-riscv64 ./cmd/agent
GOOS=linux GOARCH=arm GOARM=6 go build -ldflags="-s -w" -o dist/picoclaw-armv6 ./cmd/agent
# ... etc for all targets
```

## Deployment
```bash
# For each target with a configured host:
scp dist/picoclaw-riscv64 pi@lichee:/usr/local/bin/picoclaw
ssh pi@lichee "systemctl restart picoclaw"
```

## Generated Files
```
dist/
├── picoclaw-riscv64            # LicheeRV Nano
├── picoclaw-armv6              # Pi Zero
├── picoclaw-arm64              # Pi 3/4/5
├── picoclaw-mips               # Router
├── picoclaw-amd64              # Server
init/
├── picoclaw.service            # systemd unit
├── picoclaw.procd              # OpenWrt procd script
DEPLOY_LOG.md                   # What was built + deployed, timestamps
```

## HEARTBEAT Auto-Registration
After deploy, PicoClaw automatically registers the new device in HEARTBEAT.md, activating all configured tasks on the new hardware.

## Philosophy
One codebase. Every device. The $10 LicheeRV Nano and the $500 server run the same binary, built from the same source. That's the power of Go's cross-compilation.
