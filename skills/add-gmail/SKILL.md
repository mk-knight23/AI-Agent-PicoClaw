---
name: add-gmail
description: "Adds Gmail monitoring to PicoClaw via IMAP over TLS using pure Go. Creates channels/gmail.go with an IMAP poller using the go-imap library, polls INBOX for unread messages, drafts replies through the agent, and sends via SMTP. Compiles to a static binary with no CGo. Requires a Gmail App Password (not your Google account password)."
---

# add-gmail

Gmail monitoring for PicoClaw: IMAP polling over TLS, static binary, no CGo.

## Usage
```
/add-gmail
```

## Files Created
```
channels/gmail.go               # IMAP poller + SMTP sender using go-imap
```

## Files Modified
```
main.go                         # Start GmailChannel goroutine
config/config.go                # Add GmailAddr, GmailAppPassword fields
go.mod                          # Add github.com/emersion/go-imap
```

## Environment Variables
```
GMAIL_ADDR=you@gmail.com
GMAIL_APP_PASSWORD=your_app_password   # Google Account → Security → App Passwords
GMAIL_POLL_SECS=300                    # Default 5 minutes
```

## Getting a Gmail App Password

1. Google Account → Security → 2-Step Verification (must be enabled)
2. App Passwords → Select App: Mail → Select Device: Other → Generate
3. Copy the 16-character password → set as `GMAIL_APP_PASSWORD`

## Cross-Compilation for Edge Hardware
```bash
# Compile for LicheeRV Nano (RISC-V 64)
GOOS=linux GOARCH=riscv64 go build -o picoclaw-riscv64 .

# Compile for Raspberry Pi
GOOS=linux GOARCH=arm64 go build -o picoclaw-arm64 .
```

## Code Sample
```go
// channels/gmail.go (generated)
package channels

import (
	"github.com/emersion/go-imap/client"
	"crypto/tls"
)

type GmailChannel struct {
	addr     string
	password string
}

func (c *GmailChannel) Poll() ([]string, error) {
	conn, err := client.DialTLS("imap.gmail.com:993", &tls.Config{})
	if err != nil { return nil, err }
	defer conn.Logout()

	if err := conn.Login(c.addr, c.password); err != nil { return nil, err }
	// ... fetch UNSEEN messages from INBOX
	return messages, nil
}
```
