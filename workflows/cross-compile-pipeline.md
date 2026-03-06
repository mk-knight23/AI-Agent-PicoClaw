# Cross-Compile Pipeline — PicoClaw

Build PicoClaw binaries for all supported targets and push them to your devices in one command.

## Trigger
- **Manual**: `@PicoClaw cross-compile-pipeline --targets all`
- **On release**: Automatically triggered when you tag a release

## Supported Targets

| Target | GOOS/GOARCH | Device |
|--------|-------------|--------|
| `linux/riscv64` | RISC-V 64-bit | LicheeRV Nano, Milk-V Duo |
| `linux/arm64` | ARM 64-bit | Raspberry Pi 4/5, Orange Pi |
| `linux/arm` | ARM 32-bit (v6) | Raspberry Pi Zero, older Pis |
| `linux/mips` | MIPS | OpenWRT routers |
| `linux/amd64` | x86-64 | Server, x86 SBC |

## Pipeline Steps

1. **Build**: `GOOS=linux GOARCH=<target> go build -ldflags="-s -w" -o dist/picoclaw-<target> .`
2. **Verify**: Check binary size, run `file dist/picoclaw-<target>` to confirm ELF format
3. **Push**: `scp` to each registered device in `config/devices.json`
4. **Restart**: `ssh device "sudo systemctl restart picoclaw"`
5. **Confirm**: Poll all devices for health check after restart

## Files

```
dist/
  picoclaw-linux-riscv64      # ~2.8MB stripped
  picoclaw-linux-arm64        # ~3.1MB stripped
  picoclaw-linux-arm          # ~2.9MB stripped
  picoclaw-linux-mips         # ~3.2MB stripped
  picoclaw-linux-amd64        # ~3.4MB stripped
CROSS_COMPILE_REPORT.md       # Size, push status, restart confirmation per device
```

## Device Registry

```json
// config/devices.json
{
  "devices": [
    { "name": "living-room", "host": "picoclaw-lr.local", "arch": "riscv64" },
    { "name": "garage", "host": "192.168.1.42", "arch": "arm64" },
    { "name": "router", "host": "192.168.1.1", "arch": "mips" }
  ]
}
```

## CI/CD Integration

```yaml
# .github/workflows/release.yml
- name: Cross-compile and deploy
  run: |
    @PicoClaw cross-compile-pipeline --targets all --no-push  # Only build in CI
    # Upload artifacts to GitHub Release
```
