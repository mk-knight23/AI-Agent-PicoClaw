---
name: add-telegram
description: "Adds a Telegram bot channel to PicoClaw using the go-telegram-bot-api library. Creates channels/telegram.go with a TelegramChannel struct using long-polling, registers it in main.go, and runs in its own goroutine. Compiles to a single static binary — no external dependencies at runtime. Requires a bot token from @BotFather."
---

# add-telegram

Telegram integration for PicoClaw: pure Go, static binary, goroutine-per-connection, zero runtime deps.

## Usage
```
/add-telegram
```

## Files Created
```
channels/telegram.go            # TelegramChannel using go-telegram-bot-api
```

## Files Modified
```
main.go                         # Start TelegramChannel goroutine in main
config/config.go                # Add TelegramBotToken field
go.mod                          # Add github.com/go-telegram-bot-api/telegram-bot-api/v5
```

## Environment Variables
```
TELEGRAM_BOT_TOKEN=your_bot_token_from_botfather
PICOCLAW_TRIGGER=@PicoClaw      # Default trigger word
```

## Step-by-Step Walkthrough

1. Message `@BotFather` → `/newbot` → copy token
2. Run `/add-telegram`
3. Set `TELEGRAM_BOT_TOKEN` in `.env` or as an env var
4. Build: `go build -o picoclaw .`
5. Deploy: `scp picoclaw pi@raspberry:/usr/local/bin/` (3MB binary, no other files needed)
6. Run: `TELEGRAM_BOT_TOKEN=xxx ./picoclaw`

## Code Sample
```go
// channels/telegram.go (generated)
package channels

import (
	"strings"
	tgbotapi "github.com/go-telegram-bot-api/telegram-bot-api/v5"
)

type TelegramChannel struct {
	bot     *tgbotapi.BotAPI
	trigger string
}

func NewTelegramChannel(token, trigger string) (*TelegramChannel, error) {
	bot, err := tgbotapi.NewBotAPI(token)
	if err != nil {
		return nil, err
	}
	return &TelegramChannel{bot: bot, trigger: trigger}, nil
}

func (c *TelegramChannel) Listen(handler func(string) string) {
	u := tgbotapi.NewUpdate(0)
	u.Timeout = 60
	updates := c.bot.GetUpdatesChan(u)
	for update := range updates {
		if update.Message == nil { continue }
		text := update.Message.Text
		if !strings.Contains(text, c.trigger) { continue }
		reply := handler(text)
		msg := tgbotapi.NewMessage(update.Message.Chat.ID, reply)
		c.bot.Send(msg)
	}
}
```

## Philosophy
PicoClaw deploys as a single static binary. The Telegram channel fits neatly in one goroutine — no threads, no locks, no runtime allocation beyond the message queue.
