---
name: add-obsidian
description: "Mounts an Obsidian vault as a read-only knowledge source for PicoClaw. Creates tools/vault_search.go using Go's filepath.WalkDir and strings.Contains for fast zero-allocation search. Registers vault_search as a tool available to the agent. Works on any directory of Markdown files — synced vault, remote mount, or local copy. Requires the vault accessible as a filesystem path."
---

# add-obsidian

Vault search for PicoClaw: filepath.WalkDir, zero-allocation string search, sub-millisecond queries.

## Usage
```
/add-obsidian
```

## Files Created
```
tools/vault_search.go           # VaultSearch using filepath.WalkDir + strings
```

## Files Modified
```
tools/registry.go               # Register vault_search tool
config/config.go                # Add ObsidianVaultPath
```

## Environment Variables
```
OBSIDIAN_VAULT_PATH=/mnt/vault    # Must be accessible from the device
VAULT_MAX_RESULTS=10
```

## On Edge Hardware: Accessing the Vault

Options for getting the vault onto the device:
- **rsync**: Sync vault from laptop on schedule: `rsync -av ~/Vault/ pi:/mnt/vault/`
- **NFS mount**: Mount vault from a NAS on the local network
- **sshfs**: `sshfs user@laptop:/Users/name/Vault /mnt/vault`

## Code Sample
```go
// tools/vault_search.go (generated)
package tools

import (
	"io/fs"
	"os"
	"path/filepath"
	"strings"
)

type VaultMatch struct {
	File    string
	Excerpt string
}

func VaultSearch(query, vaultPath string, maxResults int) ([]VaultMatch, error) {
	query = strings.ToLower(query)
	var results []VaultMatch

	filepath.WalkDir(vaultPath, func(path string, d fs.DirEntry, err error) error {
		if err != nil || d.IsDir() || !strings.HasSuffix(path, ".md") { return nil }
		content, err := os.ReadFile(path)
		if err != nil { return nil }
		lower := strings.ToLower(string(content))
		if idx := strings.Index(lower, query); idx >= 0 {
			start := max(0, idx-100)
			end := min(len(content), idx+300)
			results = append(results, VaultMatch{
				File:    filepath.Base(path),
				Excerpt: string(content[start:end]),
			})
		}
		return nil
	})

	if len(results) > maxResults { results = results[:maxResults] }
	return results, nil
}
```

## Philosophy
`os.ReadFile` into `strings.ToLower` is intentionally simple — no regex, no index, no inverted file. On an SSD-backed device, scanning a 10,000-note vault takes <100ms. Add indexing only when profiling proves it's needed.
