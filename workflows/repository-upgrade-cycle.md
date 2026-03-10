# Repository Upgrade Cycle — PicoClaw

Automated weekly pass across all PicoClaw Go repositories: module updates, CVE patches, cross-compile testing, and hardware compatibility checks.

## Schedule
- **Trigger**: Sunday 1:00 AM (before OpenClaw's 2 AM cycle)
- **Duration**: ~30 minutes

## Pipeline

### Phase 1: Module Updates
```bash
go get -u ./...              # Update all modules
go mod tidy                  # Remove unused deps
govulncheck ./...            # Check for new CVEs after update
```

### Phase 2: Cross-Compile Test
After module updates, verify all targets still compile:
```bash
for ARCH in riscv64 arm/v6 arm64 mips; do
  GOOS=linux GOARCH=$ARCH go build ./...
done
```

### Phase 3: HEARTBEAT Validation
Validate all HEARTBEAT.md files across monitored devices:
```bash
@PicoClaw code-reviewer --path heartbeats/ --type config
```
- Check interval syntax is valid
- Verify referenced channels are configured
- Flag heartbeats that haven't fired in > 48h

### Phase 4: Hardware Compatibility Report
```
HARDWARE_COMPAT.md:
- LicheeRV Nano (RISC-V): ✅ Binary 7.2MB, boot <1s
- Pi Zero W (ARMv6): ✅ Binary 6.8MB, boot <2s
- Pi 4 (ARM64): ✅ Binary 8.1MB, boot <1s
- OpenWrt Router (MIPS): ✅ Binary 5.9MB, boot <3s
```

### Phase 5: Deploy to Staging Devices
```bash
@PicoClaw deploy-everywhere --target all --staging
```

### Phase 6: Digest
```
📦 PICOCLAW UPGRADE REPORT — March 10, 2026

Modules updated: 3 (net/http minor, crypto patch, golang.org/x/net)
CVEs patched: 1 (net/http: CVE-2025-xxxx, LOW severity)
Cross-compile: all 4 targets ✅
New binary sizes: all within <10MB limit
Staging deploy: SUCCESS on 3/3 devices
```
