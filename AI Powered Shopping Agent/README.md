# AI Powered Shopping Agent (n8n Workflow)

A Telegram-based shopping and styling assistant ("Maya") that can search Amazon India for products (including from voice messages) and give personalized outfit styling advice.

## How it works

```
Telegram Trigger → If (voice message?)
                       ├─ Yes → Get a file → Code in Python (fix extension) → HTTP Request1 (Groq Whisper transcription) → AI Agent
                       └─ No  → AI Agent
AI Agent (Gemini)
   ├─ HTTP Request tool (ScraperAPI → Amazon India search)
   └─ Simple Memory (per-user session)
AI Agent → Send a text message (Telegram)
```

1. **Telegram Trigger** — listens for incoming Telegram messages (text or voice).
2. **If** — branches based on whether the message contains a voice note.
3. **Get a file / Code in Python / HTTP Request1** — for voice messages: downloads the Telegram voice file, fixes its extension, and transcribes it via Groq's Whisper API.
4. **AI Agent** — "Maya," a Gemini-powered agent that either searches Amazon India for products (via the ScraperAPI tool) or has a styling conversation, asking clarifying questions about occasion, color, and style before giving outfit advice.
5. **HTTP Request (ScraperAPI tool)** — scrapes Amazon India search result pages for product name, price, rating, and link.
6. **Simple Memory** — keeps conversation context per Telegram user (keyed on their Telegram user ID).
7. **Send a text message** — replies to the user on Telegram.

## Requirements

- A running [n8n](https://n8n.io) instance
- A Telegram bot (created via [BotFather](https://t.me/BotFather)) and its API token
- Google Gemini API access (Google AI Studio / PaLM API)
- A [Groq](https://groq.com) account and API key for Whisper transcription
- A [ScraperAPI](https://www.scraperapi.com) account and API key

## Setup

1. **Import the workflow**
   In n8n: `Workflows → Import from File` and select the workflow JSON file. It's exported as inactive.

2. **Connect your credentials**
   - `Telegram Trigger`, `Get a file`, `Send a text message` nodes → your Telegram API credential
   - `Google Gemini Chat Model` node → your Google Gemini (PaLM) API credential
   - `HTTP Request1` node → your Groq API credential (already set up as a proper n8n credential type — just attach your own account)

3. **Add your ScraperAPI key**
   Open the `HTTP Request` tool node → Query Parameters, and replace the placeholder `api_key` value with your own ScraperAPI key.
   ⚠️ **Do not leave a real key in this parameter if you plan to commit changes back to a public repo.** This node uses a raw query parameter rather than an n8n-managed credential, so any future export will include whatever key you put there in plain text. Consider moving it into an HTTP Query Auth generic credential instead.

4. **Test before activating**
   Message your bot with a text product query (e.g. "sneakers under 3000") and a styling question to confirm both flows work, then try a voice message.

## Security note

This workflow was originally exported with a real ScraperAPI key inline in the `HTTP Request` tool node — it has been replaced with a placeholder in this version. If you're reusing your original export, regenerate that key in your ScraperAPI dashboard before sharing the file anywhere.

## License

Feel free to use, modify, and share.
