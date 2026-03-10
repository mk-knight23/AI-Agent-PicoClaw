# CI Security Gate — PicoClaw

Security gate for Go binaries targeting edge hardware. Validates CVEs, code patterns, binary size, and cross-compilation integrity.

## Trigger
- **Event**: GitHub PR or push to main
- **Blocks**: merge if CRITICAL or HIGH security issues found
- **Special**: validates all cross-compile targets build successfully

## Pipeline

### Step 1: Go Vulnerability Scan
```bash
govulncheck ./...           # Reachable CVEs only
nancy sleuth < go.sum        # Supply chain
```

### Step 2: Code Security
```bash
gosec -severity high ./...
staticcheck ./...
```

### Step 3: Cross-Compile Validation
```bash
# Verify all edge targets build before merge
GOOS=linux GOARCH=riscv64 go build ./...
GOOS=linux GOARCH=arm GOARM=6 go build ./...
GOOS=linux GOARCH=arm64 go build ./...
GOOS=linux GOARCH=mips go build ./...
```

### Step 4: Binary Size Check
```bash
# PicoClaw target: <10MB binary
SIZE=$(wc -c < dist/picoclaw-riscv64)
if [ $SIZE -gt 10485760 ]; then
  echo "FAIL: Binary exceeds 10MB limit ($SIZE bytes)"
  exit 1
fi
```

### Step 5: Hardware Security Audit
```bash
@PicoClaw security-scanner --network-audit   # Check for open ports, default creds
```

### Step 6: PR Report
```markdown
## PicoClaw Security Gate

✅ govulncheck: no reachable CVEs
✅ gosec: no HIGH issues
✅ Cross-compile: all 4 targets build
✅ Binary size: 7.2MB (< 10MB limit)
✅ Network audit: minimal attack surface

**Status: APPROVED**
```

## Thresholds
- **CRITICAL**: Blocks, Telegram URGENT alert
- **HIGH**: Blocks merge
- **Cross-compile failure**: Blocks (all targets must build)
- **Binary > 10MB**: Blocks (edge hardware constraint)
