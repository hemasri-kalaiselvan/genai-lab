# Generative AI Lab

A personal portal of small, working generative-AI tools — each one a standalone project, linked from a single landing page. Deployed as a static site with GitHub Pages. No install, no build step, no cost.

**GitHub repository name:** `genai-lab`

This is the only README in the repo. Every project lives in its own folder, but setup notes for all of them are kept here in one place.

## Folder structure

```
genai-lab/
├── index.html              ← the landing page (this is the site's homepage)
└── projects/
    ├── chatbot/index.html
    ├── chat-with-pdf/index.html
    └── document-summariser/index.html
```

Every future project gets its own folder under `projects/`, each with its own `index.html` — but no extra README files. This keeps every project self-contained and gives each one a clean URL, e.g.:

```
https://your-username.github.io/genai-lab/projects/chatbot/
```

## Deploy for free on GitHub Pages
1. Create a free GitHub account at https://github.com if you don't have one
2. Create a new repository named **`genai-lab`**
3. Upload the whole folder structure above, keeping the paths intact (GitHub's web UI lets you drag a folder in, or create files one at a time in the right path)
4. Go to the repo's **Settings → Pages**
5. Under "Source," select the `main` branch and root folder, then Save
6. Your site goes live at:
   `https://your-username.github.io/genai-lab/`

That's the link for your portfolio. It works on mobile and desktop — the layout is responsive by default.

## Adding a new project later
1. Create a new folder under `projects/`, e.g. `projects/pdf-chat/`
2. Put that project's `index.html` inside it
3. In the landing page (`index.html` at the root), find the matching "PLANNED" card and:
   - change its class from `card planned` to `card live`
   - change its status badge to `LIVE`
   - replace the `<div class="card-bottom">not yet built</div>` line with a link that opens in a new tab: `<a class="open" href="./projects/pdf-chat/" target="_blank" rel="noopener noreferrer">Open project</a>`
4. Add any setup notes that project needs to the relevant section below
5. Re-upload the changed files to GitHub — the site updates automatically

## Project setup notes

### 01 — Echo (chatbot)
Runs on Groq's free API.
1. Get a free key at https://console.groq.com/keys (no card required)
2. Open the chatbot page and paste the key into the box at the top — it's saved only in your own browser
3. The app automatically asks Groq for its current list of available models and picks a working one itself — no model name is hardcoded, so it keeps working even after Groq retires or renames a model. It shows which model it picked as the first message in the chat.

### 02 — Chat with PDF
Also runs on Groq's free API (same automatic model selection as above, but tuned to favor higher-throughput models since this app sends larger payloads), plus pdf.js (loaded from a free CDN) for in-browser PDF text extraction.
1. Same Groq key as the chatbot works here too — it's stored per-browser, not per-project
2. Upload a PDF; the text is extracted entirely client-side and never uploaded anywhere except as plain text sent to Groq along with your question
3. Long PDFs are truncated to the first ~14,000 characters — Groq's free tier caps requests at a fairly low tokens-per-minute budget (as low as ~6,000-8,000 on some models), and a full PDF's worth of text can exceed that in a single request even with light use. A note appears in the chat if truncation happens.
4. Only the last few exchanges are kept in the running conversation (plus the original PDF text) so a longer back-and-forth doesn't keep growing the per-message token cost
5. If a rate limit is still hit, the app reads Groq's own "try again in Ns" response and waits that long automatically before retrying, rather than giving up

### 03 — Document Summariser
Same Groq setup and model-selection approach as the other two. Upload a PDF or .txt file, or paste text directly, pick a summary length (short/medium/detailed), and get a formatted markdown summary with a copy button.
1. Same Groq key works here too
2. Also caps input at ~14,000 characters for the same free-tier rate-limit reasons as Chat with PDF
