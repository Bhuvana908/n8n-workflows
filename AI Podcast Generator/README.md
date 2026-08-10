# AI Podcast Generator (n8n Workflow)

An n8n webhook-triggered automation that takes a topic, writes a ~2-minute podcast script with Google Gemini, converts it to speech with Murf.ai, and returns the downloaded audio file.

## How it works

```
Webhook (POST) → Podcast Script Generator (Gemini) → Murf API (text-to-speech) → Podcast downloader → Respond to Webhook
```

1. **Webhook** — receives a POST request with `{ "text": "<topic>" }` in the body.
2. **Podcast Script Generator** — a Gemini-powered LLM chain that writes a conversational, ~2-minute plain-text podcast script on the given topic.
3. **Murf API** — sends the script to [Murf.ai](https://murf.ai)'s text-to-speech API to generate narrated audio.
4. **Podcast downloader** — downloads the generated audio file from the URL Murf returns.
5. **Respond to Webhook** — returns the result to whoever called the webhook.

## Requirements

- A running [n8n](https://n8n.io) instance
- Google Gemini API access (Google AI Studio / PaLM API)
- A [Murf.ai](https://murf.ai) account and API key

## Setup

1. **Import the workflow**
   In n8n: `Workflows → Import from File` and select the workflow JSON file. It's exported as inactive.

2. **Connect your Gemini credential**
   Open the `Google Gemini Chat Model` node and attach your own Google Gemini (PaLM) API credential.

3. **Add your Murf.ai API key**
   Open the `Murf API` node → Header Parameters, and replace the placeholder `api-key` value with your own Murf.ai key.
   ⚠️ **Do not leave a real key in this parameter if you plan to commit changes back to a public repo.** This node uses a raw header parameter rather than an n8n-managed credential, so n8n will **not** automatically strip it out of any future export. Consider using n8n's HTTP Request "Generic Credential Type" (Header Auth) instead, so the key is stored encrypted.

4. **Get a fresh webhook URL**
   The Webhook node's path in this export has been replaced with a placeholder for safety. After importing, open the `Webhook` node — n8n will generate a new unique path/URL automatically. Use that new URL (found in the node's "Test URL" / "Production URL" fields) to call the workflow; keep it private since anyone with the URL can trigger the workflow and consume your API quota.

5. **Test before activating**
   Send a test POST request (e.g. via `curl` or Postman) with `{ "text": "a topic here" }` to confirm the full flow works, then toggle the workflow to **Active** to enable the production webhook.

## Security notes

- This workflow was originally exported as **active** with a real webhook path and a real Murf.ai API key inline — both have been removed/placeholdered in this version. If you're reusing your original export, regenerate your Murf.ai key and get a fresh webhook path before sharing publicly.
- Because this workflow is triggered by an open webhook, anyone who obtains the URL can call it and consume your Gemini/Murf.ai quota. Consider adding authentication (e.g. a header secret checked in an IF node) if you make this public-facing.

## License

Feel free to use, modify, and share.
