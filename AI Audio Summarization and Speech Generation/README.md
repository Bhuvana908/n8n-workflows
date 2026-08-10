# AI Audio Summarization and Speech Generation (n8n Workflow)

An n8n chat-triggered automation that takes an audio file URL, transcribes it, summarizes it into crisp meeting-style bullet points using Google Gemini, then converts that summary back into spoken audio.

## How it works

```
When chat message received → Download audio → Audio Transcription (Groq Whisper) → Text summarizer (Gemini) → Audio generation (Murf) → Download summarized audio
```

1. **When chat message received** — n8n chat trigger; expects the chat input to be a URL pointing to an audio file.
2. **Download audio** — fetches the audio file as binary data from that URL.
3. **Audio Transcription** — sends the audio to [Groq](https://groq.com)'s Whisper Large v3 Turbo API for fast, accurate transcription.
4. **Text summarizer** — a Gemini-powered LLM chain that condenses the transcript into strict, self-contained bullet points (meeting-minutes style).
5. **Audio generation** — sends the summary text to [Murf.ai](https://murf.ai)'s text-to-speech API to generate narrated audio of the summary.
6. **Download summarized audio** — downloads the generated summary audio file.

## Requirements

- A running [n8n](https://n8n.io) instance
- Google Gemini API access (Google AI Studio / PaLM API)
- A [Groq](https://groq.com) account and API key (Bearer token) for Whisper transcription
- A [Murf.ai](https://murf.ai) account and API key for text-to-speech

## Setup

1. **Import the workflow**
   In n8n: `Workflows → Import from File` and select the workflow JSON file. It's exported as inactive.

2. **Connect your credentials**
   - `Google Gemini Chat Model` node → your Google Gemini (PaLM) API credential
   - `Audio Transcription` node → your Groq API key, set up as an n8n **Bearer Auth** generic credential (the node is already configured to use `httpBearerAuth`)

3. **Add your Murf.ai API key**
   Open the `Audio generation` node → Header Parameters, and replace the placeholder `api-key` value with your own Murf.ai key.
   ⚠️ **Do not leave a real key in this parameter if you plan to commit changes back to a public repo.** This node uses a raw header parameter rather than an n8n-managed credential, so any future export will include whatever key you put there in plain text. Consider switching it to a Header Auth generic credential like the `Audio Transcription` node uses.

4. **Test before activating**
   Trigger the chat with a sample audio file URL to confirm transcription, summarization, and speech generation all work end-to-end, then toggle the workflow to **Active**.

## Security note

This workflow was originally exported with a real Murf.ai API key inline in the `Audio generation` node — it has been replaced with a placeholder in this version. If you're reusing your original export, regenerate that key in your Murf.ai dashboard before sharing the file anywhere.

## License

Feel free to use, modify, and share.
