# Learning Path Generator (n8n Workflow)

An n8n chat agent that takes a learning goal (e.g. "10-day Python learning path starting tomorrow") and builds out a full day-by-day curriculum: researching resources, writing a structured Google Doc, and scheduling a Google Calendar event for each day.

## How it works

```
When chat message received → AI Agent (Gemini)
                                 ├─ HTTP Request tool (SearchAPI.io) — research resources
                                 ├─ Create a document in Google Docs
                                 ├─ Update a document in Google Docs
                                 ├─ Create an event in Google Calendar (once per day)
                                 └─ Simple Memory — conversational context
```

1. **When chat message received** — chat trigger; the user describes their learning goal, duration, and optional start date.
2. **AI Agent** — a Gemini-powered agent that plans a day-wise curriculum, researches real resources, and orchestrates the tools below. Detailed step-by-step instructions live in its system prompt.
3. **HTTP Request (SearchAPI.io)** — used sparingly (2–3 calls) to find real YouTube videos, articles, and documentation for the curriculum.
4. **Create / Update a document in Google Docs** — creates one Google Doc with the title `[X]-Day [Topic] Learning Path` and fills it in with the full day-by-day plan.
5. **Create an event in Google Calendar** — adds one 2-hour calendar event per day (11:00 AM–1:00 PM, IST) with that day's topic and content.
6. **Simple Memory** — gives the agent short-term conversational memory within a session.

## Requirements

- A running [n8n](https://n8n.io) instance
- Google Gemini API access (Google AI Studio / PaLM API)
- A Google account with Docs and Calendar API access enabled
- A [SearchAPI.io](https://www.searchapi.io) account and API key

## Setup

1. **Import the workflow**
   In n8n: `Workflows → Import from File` and select the workflow JSON file. It's exported as inactive.

2. **Connect your credentials**
   - `Google Gemini Chat Model` node → your Google Gemini (PaLM) API credential
   - `Create a document in Google Docs` / `Update a document in Google Docs` nodes → your Google Docs OAuth2 credential
   - `Create an event in Google Calendar` node → your Google Calendar OAuth2 credential

3. **Add your SearchAPI.io key**
   Open the `HTTP Request` tool node and replace the placeholder `api_key` value in the URL with your own SearchAPI.io key.
   ⚠️ **Do not leave a real key in that URL if you plan to commit changes back to a public repo.** This node uses a raw query parameter rather than an n8n-managed credential, so any future export will include whatever key you put there in plain text. Consider moving it into an HTTP Query Auth generic credential instead.

4. **Point it at your own Google Docs folder and Calendar**
   Open the `Create a document in Google Docs` node and update `folderId` to a folder in your own Drive. Open the `Create an event in Google Calendar` node and select your own calendar (the current file references a specific "Learning path" calendar ID that belongs to the original creator).

5. **Test before activating**
   Try a short (e.g. 2-day) learning path first to confirm the Doc and Calendar events are created correctly, then use it for real.

## Notes

- The agent's system prompt has strict rules around only calling SearchAPI 2–3 times and creating exactly one Doc, to keep API usage and cost predictable — worth reviewing if you plan to adjust behavior.
- The Google Doc folder ID and Calendar ID in this export are specific to the original setup — update both before use.

## License

Feel free to use, modify, and share.
