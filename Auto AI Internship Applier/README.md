# Auto AI Internship Applier (n8n Workflow)

An n8n automation that watches a Google Sheet of internship leads and automatically emails a personalized, ready-to-send application to each hiring manager, using Google Gemini to write the email.

## How it works

```
Google Sheets Trigger → Basic LLM Chain (Gemini + Structured Output Parser) → Send a message (Gmail)
```

1. **Google Sheets Trigger** — polls a Google Sheet every minute for new/updated rows. Each row is expected to have columns: `Full Name`, `HiringManagerEmail`, `Position Applied`, `Details`, `Experience (Years)`, `Skills`.
2. **Basic LLM Chain** — a Gemini-powered LLM chain that drafts a complete, ready-to-send application email (no placeholders) based on the row's data, using a structured output parser to produce `to`, `subject`, and `body` fields.
3. **Send a message** — sends the generated email via Gmail directly to the hiring manager's address from the sheet.

## Requirements

- A running [n8n](https://n8n.io) instance
- A Google account with:
  - A Google Sheet containing your internship application leads
  - Google Gemini API access (Google AI Studio / PaLM API)
- A Gmail account with OAuth2 API access enabled

## Setup

1. **Import the workflow**
   In n8n: `Workflows → Import from File` and select the workflow JSON file. It's exported as inactive, so nothing sends until you finish setup and switch it on.

2. **Connect your credentials**
   No secrets ship with this file — you'll need to attach your own:
   - `Google Sheets Trigger` node → your Google Sheets OAuth2 credential
   - `Google Gemini Chat Model` node → your Google Gemini (PaLM) API credential
   - `Send a message` node → your Gmail OAuth2 credential

3. **Point it at your own Google Sheet**
   Open the `Google Sheets Trigger` node and select your own spreadsheet + sheet tab. Make sure the columns match: `Full Name`, `HiringManagerEmail`, `Position Applied`, `Details`, `Experience (Years)`, `Skills`.

4. **Review the email prompt**
   Open the `Basic LLM Chain` node to adjust the tone, structure, or content instructions for the generated email to match your own voice.

5. **Test before activating**
   Run the workflow manually on a test row first (ideally sending to your own address) to confirm the email reads the way you want, then toggle the workflow to **Active**.

## ⚠️ Use with care

This workflow sends real emails automatically and unattended, straight to hiring managers, based purely on sheet data — there's no human review step before sending. Before activating on your real lead list, strongly consider:
- Adding a manual approval/review step (e.g. a Slack or email confirmation) before the `Send a message` node
- Double-checking the Gemini-generated emails on a few sample rows for accuracy, since it's applying on your behalf
- Being mindful of sending volume/frequency so you don't come across as spammy to hiring managers

## License

Feel free to use, modify, and share.
