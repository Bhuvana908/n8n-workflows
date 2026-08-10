# Transcription Summarizer Agent (n8n Workflow)

An n8n chat agent that takes an audio file URL, transcribes it with Groq's Whisper API, saves the transcript and a bullet-point summary to a Google Doc, and returns the summary to the user.

## How it works

```
When chat message received → HTTP Request (download audio) → HTTP Request2 (Groq Whisper transcription) → AI Agent (Gemini)
                                                                                                                 ├─ Create a document in Google Docs
                                                                                                                 ├─ Update a document in Google Docs
                                                                                                                 └─ Get a document in Google Docs
```

1. **When chat message received** — chat trigger; expects the chat input to be a URL pointing to an audio file.
2. **HTTP Request** — downloads the audio file as binary data.
3. **HTTP Request2** — sends the audio to Groq's `whisper-large-v3` API for transcription.
4. **AI Agent** — a Gemini-powered agent that creates a Google Doc, adds the original transcript, generates a structured bullet-point summary (themes, insights, action items, conclusions, data points), appends the summary to the doc, retrieves the full content, and returns the summary + doc link to the user.

## Requirements

- A running [n8n](https://n8n.io) instance
- Google Gemini API access (Google AI Studio / PaLM API)
- A [Groq](https://groq.com) account and API key for Whisper transcription
- A Google account with Docs API access enabled

## Setup

1. **Import the workflow**
   In n8n: `Workflows → Import from File` and select the workflow JSON file. It's exported as inactive.

2. **Connect your credentials**
   - `Google Gemini Chat Model` node → your Google Gemini (PaLM) API credential
   - `Create/Update/Get a document in Google Docs` nodes → your Google Docs OAuth2 credential

3. **Add your Groq API key**
   Open the `HTTP Request2` node → Header Parameters, and replace the placeholder `Authorization` value with your own Groq API key (format: `Bearer YOUR_KEY`).
   ⚠️ **Do not leave a real key in this parameter if you plan to commit changes back to a public repo.** This node uses a raw header parameter rather than an n8n-managed credential, so any future export will include whatever key you put there in plain text. Consider switching to an n8n Header Auth generic credential instead.

4. **Point it at your own Google Drive folder**
   Open the `Create a document in Google Docs` node and update `folderId` to a folder in your own Drive (the current file references the original creator's folder).

5. **Test before activating**
   Try it with a short sample audio file URL first to confirm transcription, document creation, and summarization all work end-to-end.

## Security note

This workflow was originally exported with a real Groq API key inline in the `HTTP Request2` node — it has been replaced with a placeholder in this version. If you're reusing your original export, regenerate that key in your Groq console before sharing the file anywhere.

## License

Feel free to use, modify, and share.
