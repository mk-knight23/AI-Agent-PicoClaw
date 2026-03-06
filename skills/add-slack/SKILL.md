---
name: add-slack
description: "Adds Slack integration to PicoClaw using the Slack Real Time Messaging API over WebSocket in pure Go. Creates channels/slack.go using gorilla/websocket, handles app_mention events, routes to the skill dispatcher, and replies in-thread. Compiles to a static binary. Requires a Slack bot token and app token with Socket Mode enabled."
---

# add-slack

Slack Socket Mode for PicoClaw: gorilla/websocket, single goroutine, static binary.

## Usage
```
/add-slack
```

## Files Created
```
channels/slack.go               # SlackChannel using gorilla/websocket RTM
```

## Files Modified
```
main.go                         # Start SlackChannel goroutine
config/config.go                # Add SlackBotToken, SlackAppToken
go.mod                          # Add github.com/gorilla/websocket
```

## Environment Variables
```
SLACK_BOT_TOKEN=xoxb-your-bot-token
SLACK_APP_TOKEN=xapp-your-app-level-token
```

## Why WebSocket (not the official Slack SDK)
The official Slack Go SDK adds significant binary size. PicoClaw uses `gorilla/websocket` directly against the Socket Mode API to keep the binary under 10MB. This is appropriate for edge devices where binary size matters.

## Code Sample
```go
// channels/slack.go (generated)
package channels

import (
	"encoding/json"
	"github.com/gorilla/websocket"
)

type SlackEvent struct {
	Type    string `json:"type"`
	Payload struct {
		Event struct {
			Text string `json:"text"`
			Ts   string `json:"ts"`
		} `json:"event"`
	} `json:"payload"`
}

type SlackChannel struct {
	botToken string
	appToken string
}

func (c *SlackChannel) Listen(handler func(string) string) error {
	wsURL, err := c.getWebSocketURL()
	if err != nil { return err }
	conn, _, err := websocket.DefaultDialer.Dial(wsURL, nil)
	if err != nil { return err }
	for {
		_, msg, err := conn.ReadMessage()
		if err != nil { return err }
		var event SlackEvent
		if err := json.Unmarshal(msg, &event); err != nil { continue }
		if event.Type == "events_api" {
			reply := handler(event.Payload.Event.Text)
			c.postMessage(reply, event.Payload.Event.Ts)
		}
	}
}
```
