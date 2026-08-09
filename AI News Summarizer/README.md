# AI News Summarizer (n8n Workflow)

An n8n automation that runs daily, pulls AI/tech news from RSS feeds and upcoming AI events, summarizes everything into a structured "Tech Brief," and emails it to you via Gmail.

## How it works

```
Schedule Trigger (daily @ 10am)
   ├─→ AI News Feed (aibusiness.com RSS)     ─┐
   ├─→ RSS Read (TechCrunch RSS)              ├─→ Data Merger → Data Aggregator → AI Summarizer (Gemini) → Send a message (Gmail)
   └─→ Fetch Events (SearchAPI.io events)    ─┘
```

1. **Schedule Trigger** — fires once a day at 10:00 (server timezone).
2. **AI News Feed** / **RSS Read** — pull the latest items from two RSS feeds (AI Business, TechCrunch).
3. **Fetch Events** — queries the [SearchAPI.io](https://www.searchapi.io) Google Events API for upcoming AI tech events.
4. **Data Merger / Data Aggregator** — combine all three sources into one payload.
5. **AI Summarizer** — a Gemini-powered LLM chain that formats everything into a structured "Tech Brief" (AI news highlights, technology updates, upcoming events).
6. **Send a message** — emails the finished brief via Gmail.

## Requirements

- A running [n8n](https://n8n.io) instance
- Google Gemini API access (Google AI Studio / PaLM API)
- A Gmail account with OAuth2 API access enabled
- A [SearchAPI.io](https://www.searchapi.io) account and API key (used for the events search)

## Setup

1. **Import the workflow**
   In n8n: `Workflows → Import from File` and select the workflow JSON file. It's exported as inactive, so nothing runs until you finish setup and switch it on.

2. **Connect your credentials**
   No secrets ship with this file — you'll need to attach your own:
   - `Google Gemini Chat Model` node → your Google Gemini (PaLM) API credential
   - `Send a message` node → your Gmail OAuth2 credential

3. **Set your recipient email**
   Open the `Send a message` node and replace the `sendTo` placeholder with your own email address.

4. **Add your SearchAPI.io key**
   Open the `Fetch Events` node → Query Parameters, and replace the `api_key` placeholder value with your own SearchAPI.io key.
   ⚠️ **Do not hardcode this key directly in the URL or leave a real key in this parameter if you plan to commit changes back to a public repo.** For production use, consider moving it into n8n's built-in HTTP Request "Generic Credential Type" (Header Auth or Query Auth) instead of a plain node parameter, so it's stored encrypted rather than in the workflow JSON.

5. **Adjust the schedule and search query**
   Edit the `Schedule Trigger` node to change what time the brief runs, and edit the `q` parameter in `Fetch Events` if you want events for a different topic/region.

6. **Test before activating**
   Run the workflow manually once to confirm the email arrives formatted correctly, then toggle the workflow to **Active**.

## Security note

This workflow uses an HTTP Request node with a raw API key query parameter rather than an n8n-managed credential. Unlike the other node types here (Gmail, Gemini), n8n does **not** automatically keep this key out of the exported JSON — if you paste a real key into that parameter, it will be included in plain text if you export or share the workflow. Always double-check HTTP Request nodes for hardcoded secrets before sharing a workflow file publicly.

## License

Feel free to use, modify, and share.
