# Multi-Tool Agent (n8n Workflow)

A general-purpose Telegram AI assistant that can hold natural conversations, look up real-time information, do calculations, look up facts on Wikipedia, and manage Google Calendar events — all through a single chat interface.

## How it works

```
Telegram Trigger → AI Agent (Gemini)
                       ├─ SerpAPI-style HTTP Request tool — real-time/factual web search
                       ├─ Create an event in Google Calendar
                       ├─ Calculator
                       ├─ Wikipedia
                       └─ Simple Memory (per-user session)
AI Agent → Send a text message (Telegram, HTML formatted)
```

1. **Telegram Trigger** — listens for incoming Telegram text messages.
2. **AI Agent** — a Gemini-powered agent that decides which tool (if any) to use based on the user's intent, then replies using Telegram-safe HTML formatting.
3. **HTTP Request (SearchAPI.io)** — used for real-time or factual information: news, current events, prices, anything requiring up-to-date data.
4. **Create an event in Google Calendar** — creates/modifies/deletes calendar events on request.
5. **Calculator** — handles arithmetic, percentages, unit conversions, date/time math.
6. **Wikipedia** — looks up people, companies, and general facts.
7. **Simple Memory** — keeps conversation context per Telegram user (keyed on their Telegram user ID).
8. **Send a text message** — replies to the user on Telegram using HTML parse mode.

## Requirements

- A running [n8n](https://n8n.io) instance
- A Telegram bot (created via [BotFather](https://t.me/BotFather)) and its API token
- Google Gemini API access (Google AI Studio / PaLM API)
- A [SearchAPI.io](https://www.searchapi.io) account and API key
- A Google account with Calendar API access enabled

## Setup

1. **Import the workflow**
   In n8n: `Workflows → Import from File` and select the workflow JSON file. It's exported as inactive.

2. **Connect your credentials**
   - `Telegram Trigger`, `Send a text message` nodes → your Telegram API credential
   - `Google Gemini Chat Model` node → your Google Gemini (PaLM) API credential
   - `Create an event in Google Calendar` node → your Google Calendar OAuth2 credential

3. **Add your SearchAPI.io key**
   Open the `HTTP Request` tool node and replace the placeholder `api_key` value in the URL with your own SearchAPI.io key.
   ⚠️ **Do not leave a real key in that URL if you plan to commit changes back to a public repo.** This node uses a raw query parameter rather than an n8n-managed credential, so any future export will include whatever key you put there in plain text. Consider moving it into an HTTP Query Auth generic credential instead.

4. **Point it at your own calendar**
   Open the `Create an event in Google Calendar` node and select your own calendar (the current file references a specific "Multi-tool-agent" calendar ID that belongs to the original creator).

5. **Test before activating**
   Message your bot with a general question, a calculation, a Wikipedia-style lookup, and a calendar request to confirm each tool routes correctly.

## License

Feel free to use, modify, and share.
