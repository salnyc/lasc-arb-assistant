# The Landing at Swift Creek — ARB Assistant

A chatbot website for LASC homeowners: answers questions about the ARB
Standards & Design Guidelines (Aug 2025, v5), helps draft ARB applications,
and gives a preliminary read on proposed projects. Strictly limited to ARB
topics — off-topic questions get a one-sentence redirect.

## What's in this folder

```
index.html      ← the chat website (frontend)
api/chat.js     ← serverless function that holds the API key and calls Anthropic
README.md       ← this file
```

The API key is NEVER in the frontend. It lives only in a Vercel environment
variable, read by `api/chat.js` on the server.

## Deploy to Vercel (free) — about 15 minutes

### 1. Get an Anthropic API key
1. Go to https://console.anthropic.com and create an account.
2. Add prepaid credits (minimum $5 — this will likely last months at HOA scale).
3. Under **API Keys**, create a key. Copy it somewhere safe. You will not
   see it again after closing the dialog.

### 2. Put the code on GitHub
1. Create a free account at https://github.com if you don't have one.
2. Create a new repository (e.g., `lasc-arb-assistant`). Private is fine —
   Vercel can deploy private repos.
3. Upload the contents of this folder (drag-and-drop works in the GitHub web
   UI: upload `index.html`, `README.md`, and create the `api` folder with
   `chat.js` inside it).

### 3. Deploy on Vercel
1. Create a free account at https://vercel.com — sign up **with GitHub** so
   they're linked.
2. Click **Add New → Project**, and import your `lasc-arb-assistant` repo.
3. Before clicking Deploy, open **Environment Variables** and add:
   - Name: `ANTHROPIC_API_KEY`
   - Value: (paste your key)
4. Click **Deploy**. In about a minute you'll have a live URL like
   `https://lasc-arb-assistant.vercel.app`.

That's it. Share the URL with the community (Town Square, newsletter, etc.).

## Costs

- **Hosting: $0.** Vercel's Hobby tier is free for non-commercial projects.
- **API usage: pay-as-you-go.** The code uses Claude Haiku 4.5 — the
  cheapest current-generation model ($1/$5 per million input/output tokens) —
  with prompt caching enabled (the guidelines document is cached, so repeat
  requests pay ~10% of normal input cost on it). A typical member conversation
  costs about a penny. If you ever want higher answer quality, edit one line
  in `api/chat.js`: change the model to `claude-sonnet-4-6` (~3x cost).
- Set a **spend limit** in the Anthropic console (Settings → Limits) for
  peace of mind.

## Built-in abuse protection

Because the endpoint is public, `api/chat.js` includes:
- Rate limit: 10 requests/minute per IP
- Max 40 turns per conversation, 4,000 characters per message
- `max_tokens: 1000` cap on every response
- Topic lockdown in the system prompt (off-topic = one-sentence redirect)

For a small HOA this is plenty. If you ever see abuse, you can also enable
Vercel's built-in firewall / attack challenge mode from the dashboard.

## Updating the guidelines

When the ARB publishes a new guidelines version, edit the `GUIDELINES` text
block at the top of `api/chat.js` (it's plain English — no coding needed),
commit the change on GitHub, and Vercel redeploys automatically. Also update
the version note in the footer of `index.html`.

## Disclaimers already built in

- Welcome card and persistent footer state the assistant is informational only.
- The system prompt forbids the words "approved/denied" as determinations;
  the bot says "appears consistent / inconsistent with §X" and always defers
  to the ARB's written decision (§2.7).
