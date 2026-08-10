# Automated Historical Content Publisher (n8n Workflow)

An n8n automation that runs daily, uses Google Gemini to find a historical event that happened on today's date, writes a "On This Day" LinkedIn post about it, and publishes it automatically.

## How it works

```
Schedule Trigger (daily @ 9am) → Basic LLM Chain (Gemini) → Create a post (LinkedIn)
```

1. **Schedule Trigger** — fires once a day at 09:00 (server timezone).
2. **Basic LLM Chain** — a Gemini-powered LLM chain that identifies a notable historical event matching today's date, then writes a complete, ready-to-post LinkedIn post about it — including a hook, explanation of impact, a discussion-prompting question, and the hashtags `#OnThisDay #History #TodayInHistory`.
3. **Create a post** — publishes the generated content directly to a LinkedIn profile/page via the LinkedIn node.

## Requirements

- A running [n8n](https://n8n.io) instance
- Google Gemini API access (Google AI Studio / PaLM API)
- A LinkedIn account with API access (LinkedIn OAuth2 app configured for posting)

## Setup

1. **Import the workflow**
   In n8n: `Workflows → Import from File` and select the workflow JSON file. It's exported as inactive, so nothing posts until you finish setup and switch it on.

2. **Connect your credentials**
   No secrets ship with this file — you'll need to attach your own:
   - `Google Gemini Chat Model` node → your Google Gemini (PaLM) API credential
   - `Create a post` node → your LinkedIn OAuth2 credential

3. **Set your LinkedIn profile**
   Open the `Create a post` node and select your LinkedIn person/organization URN from the credential-linked dropdown (don't hardcode someone else's URN).

4. **Adjust the schedule and prompt**
   Edit the `Schedule Trigger` node to change what time it posts, and edit the `Basic LLM Chain` prompt if you want a different tone, length, or hashtag set.

5. **Test before activating**
   Run the workflow manually a couple of times to confirm the generated posts read well and the historical facts check out, then toggle the workflow to **Active**.

## ⚠️ Use with care

The workflow lets an LLM pick and publish a "historical fact" fully unattended — LLMs can occasionally get dates or details wrong. Consider adding a manual review/approval step (e.g. Slack confirmation) before the `Create a post` node, especially early on, until you're confident in the accuracy of what it's generating.

## License

Feel free to use, modify, and share.
