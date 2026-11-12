# Call Summarizer

A minimal full-stack web app that turns a pasted call transcript into an **executive summary**, **key points**, and **action items** (with owners and due dates) using Claude.

- **Backend:** Node.js + Express, `@anthropic-ai/sdk` (model `claude-sonnet-4-5`)
- **Frontend:** a single `public/index.html` with plain CSS and JavaScript, no frameworks

## Setup

**Prerequisites:** Node.js 18+ and an [Anthropic API key](https://console.anthropic.com/).

```bash
# 1. Install dependencies
npm install

# 2. Add your API key
cp .env.example .env
#    then edit .env and set ANTHROPIC_API_KEY=your-key

# 3. Start the server
npm start
```

Open <http://localhost:3000>, paste a transcript (or click **Use sample**), and press **Summarize call** (or `Ctrl/Cmd + Enter`).

You can also set the key directly in your shell instead of using `.env`:

```bash
ANTHROPIC_API_KEY=your-key npm start
```

Set `PORT` to change the port (default `3000`). Use `npm run dev` to auto-restart on file changes.

## API

### `POST /api/summarize`

Request:

```json
{ "transcript": "Maya: Thanks for joining..." }
```

Response `200`:

```json
{
  "executiveSummary": "…",
  "keyPoints": ["…", "…"],
  "actionItems": [
    { "task": "…", "owner": "Maya", "due": "Friday" }
  ]
}
```

Errors return `{ "error": "message" }` with status `400` (empty input), `413` (over 200,000 characters), `429` (rate limited), or `500`/`502` (server or upstream problem).

## How it works

The server forces Claude to call a `record_call_summary` tool with a JSON schema, so the response is always well-formed structured data rather than text that has to be parsed. The transcript is wrapped in tags and treated as untrusted data.

## Project structure

```
.
├── public/index.html   # Frontend (HTML + CSS + JS)
├── server.js           # Express server and /api/summarize endpoint
├── package.json
├── .env.example        # Copy to .env and add your key
├── .gitignore          # Ignores node_modules and .env
└── README.md
```

## Notes

- Your API key stays on the server and is never sent to the browser.
- Never commit `.env`; it is listed in `.gitignore`.
- There is no authentication or rate limiting. Add both before exposing this app publicly, since each request spends API credit.
