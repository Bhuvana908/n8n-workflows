# Automated LinkedIn Job Tracker (n8n Workflow)

An n8n automation that runs daily, pulls new job postings from an RSS feed, uses Google Gemini to extract required skills and draft a tailored cover letter for each posting, and logs everything to a Google Sheet.

## How it works

```
Schedule Trigger (daily @ 10am) → RSS Read → Basic LLM Chain (Gemini + Structured Output Parser) → Append or update row in sheet
```

1. **Schedule Trigger** — fires once a day at 10:00 (server timezone).
2. **RSS Read** — pulls new job postings from an RSS feed (e.g. a saved LinkedIn job search feed via a service like FetchRSS).
3. **Basic LLM Chain** — a Gemini-powered LLM chain that reads each posting, extracts the required technical skills, and drafts a personalized 250–300 word cover letter, returned as structured output (`Title`, `Link`, `Published Date`, `About Company`, `skills`, `cover letter`).
4. **Append or update row in sheet** — writes/updates a row in a Google Sheet for each job posting, keyed on job title, so you get a running tracker of postings with ready-to-use cover letters.

## Requirements

- A running [n8n](https://n8n.io) instance
- An RSS feed of job postings (e.g. a saved LinkedIn search converted to RSS via [FetchRSS](https://fetchrss.com) or similar)
- Google Gemini API access (Google AI Studio / PaLM API)
- A Google account with a Sheet to log tracked jobs into

## Setup

1. **Import the workflow**
   In n8n: `Workflows → Import from File` and select the workflow JSON file. It's exported as inactive, so nothing runs until you finish setup and switch it on.

2. **Connect your credentials**
   No secrets ship with this file — you'll need to attach your own:
   - `Google Gemini Chat Model` node → your Google Gemini (PaLM) API credential
   - `Append or update row in sheet` node → your Google Sheets OAuth2 credential

3. **Set your own RSS feed**
   Open the `RSS Read` node and replace the feed URL with your own job-postings RSS feed.

4. **Point it at your own Google Sheet**
   Open the `Append or update row in sheet` node and select your own spreadsheet + sheet tab. It expects columns: `Title`, `Link`, `Published Date`, `About Company`, `skills`, `Cover letter`.

5. **Adjust the schedule and prompt**
   Edit the `Schedule Trigger` node to change what time it runs, and edit the `Basic LLM Chain` prompt if you want a different cover letter tone/length or additional extracted fields.

6. **Test before activating**
   Run the workflow manually once to confirm postings are parsed correctly and the sheet updates as expected, then toggle the workflow to **Active**.

## License

Feel free to use, modify, and share.
