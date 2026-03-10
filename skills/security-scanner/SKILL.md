---
name: security-scanner
description: "Go security audit for edge deployments: govulncheck for CVEs in Go modules, gosec for code-level security patterns, nancy for supply chain vulnerabilities, and analysis of network exposure on edge hardware (open ports, default credentials, TLS config). Generates SECURITY_REPORT.md. Adapted from ZeroClaw's Rust security-scanner for Go and IoT edge deployments."
---

# security-scanner

Go security audit — CVEs, code patterns, supply chain, and edge network exposure.

## Usage
```
@PicoClaw security-scanner --full
@PicoClaw security-scanner --deps-only         # govulncheck + nancy only
@PicoClaw security-scanner --network-audit     # Scan open ports + TLS on edge device
@PicoClaw security-scanner --fail-on high      # CI mode: exit non-zero if HIGH+ found
```

## What It Checks

### 1. Known CVEs — `govulncheck`
- Scans Go module graph against Go vulnerability database (vuln.go.dev)
- Only reports vulnerabilities reachable from your code (not just deps)
- Includes: CVE ID, affected function, call stack trace to the vulnerable code

### 2. Supply Chain — `nancy`
- Sonatype OSS Index integration for Go module audit
- Flags: known malicious packages, deprecated modules, license violations

### 3. Code Security — `gosec`
- Hardcoded credentials in source code
- Weak crypto: `math/rand` instead of `crypto/rand`
- Path traversal in file operations
- Shell injection in `exec.Command` with user input
- HTTP server without TLS
- Insecure temp file creation

### 4. Edge Network Audit (PicoClaw-Specific)
- Scans open ports on the edge device (`nmap -sV localhost`)
- Checks TLS certificate validity for any HTTPS endpoints
- Detects default credentials on admin interfaces
- Verifies firewall rules match the minimal attack surface

## Files Created
```
SECURITY_REPORT.md          # Full report with severity + remediation
security_audit.json         # Machine-readable JSON
network_exposure.md         # Open ports, TLS status, credential audit
```

## CI Integration (cross-compile pipeline)
```yaml
# Runs security scan before cross-compile + deploy
- name: Security Gate
  run: |
    go install golang.org/x/vuln/cmd/govulncheck@latest
    picoclaw security-scanner --full --fail-on high
```

## Philosophy
An edge device is physically accessible. A compromised PicoClaw on a home network is a lateral movement vector. Security scanning is not optional — it's the pre-flight check before every binary lands on hardware.
