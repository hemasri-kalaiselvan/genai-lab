# Generative AI Lab

> A personal portal of small, working generative-AI tools — each a standalone project, linked from one landing page, deployed free on GitHub Pages.

**Tech:** HTML, CSS, JavaScript
**Tools:** GitHub, Groq, Pollinations.ai, pdf.js
**AI Tools:** Claude

## About

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
    ├── document-summariser/index.html
    ├── youtube-summariser/index.html
    ├── image-caption-generator/index.html
    ├── text-to-image-generator/index.html
    ├── resume-analyser/index.html
    ├── job-description-matcher/index.html
    └── cover-letter-generator/index.html
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

## How AI Helped

- **Claude** — design direction, code, debugging, and deployment across all nine tools
- Worked through real, provider-specific problems: automatic model selection so tools keep working when Groq retires or renames a model; client-side rate-limit handling that reads Groq's own retry hints; and reading Pollinations'  live model list instead of hardcoding names

---

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

### 04 — YouTube Video Summariser
Same engine again, but for video transcripts instead of documents.
1. This app deliberately does **not** try to auto-fetch a transcript from a pasted YouTube link — browsers can't read YouTube's caption data cross-origin (CORS blocks it), and there's no free public API for it. An "auto-fetch" version would look like it works and then fail unpredictably, so instead:
   - Paste the transcript text directly (YouTube's own video page has a "Show transcript" option — click it, select all, copy, paste here), or
   - Upload a downloaded `.srt`/`.vtt`/`.txt` transcript file — timestamps and sequence numbers are stripped automatically
2. The video URL field is optional and only used to label the summary — it's not fetched or processed
3. Same 14,000-character cap and automatic model selection as the other tools

### 05 — Image Caption Generator
Uses Groq's vision-capable model, `qwen/qwen3.8-27b` — currently the only model on Groq's free tier that can look at images (a couple of Llama 4 vision models existed earlier but were retired). Everything else follows the same pattern.
1. Same Groq key works here too
2. Upload an image; it's automatically resized in your browser (max 1024px on the longest side) before being sent, to keep requests small and fast
3. Pick a caption style — plain description, social-media caption, or detailed alt text for accessibility
4. The model list in the code (`VISION_MODEL_PREFERENCE`) is a single entry today, but is written as a list so a future model addition or rename is a one-line edit, not a rewrite

### 06 — Text-to-Image Generator
The only project that needs **no API key at all**. Runs on [Pollinations.ai](https://pollinations.ai) — a free, open-source image generation service with no signup and no key required for its anonymous tier.
1. Just type a description, pick a size and style, and generate
2. The anonymous tier is rate-limited to roughly one image per 15 seconds — the app enforces a 16-second cooldown on the Generate button so you don't send requests that would just fail
3. There's a seed field (with a Randomize button) — using the same seed, prompt, size, and style again reproduces the same image, useful for small tweaks
4. Since this is someone else's free public service rather than our own key-based setup, it can occasionally be slower or briefly unavailable — the app shows a plain message if an image fails to load rather than a cryptic error
5. The "Style" dropdown is populated from Pollinations' own live model list at page load, rather than a fixed set of names — this service has changed its available models entirely more than once, and hardcoded names silently broke every time. If a chosen style ever gets retired mid-session, generation automatically retries once with no style specified before giving up.

### 07 — Resume Analyser
Same Groq setup and model-selection approach as the summarisers. Upload a resume as PDF or .txt, or paste text directly, optionally name a target role, and get structured feedback.
1. Same Groq key works here too
2. Feedback is organized into fixed sections: Overall Impression, Strengths, Areas to Improve, Formatting & Clarity, and Suggested Next Steps
3. The optional "target role" field sharpens the feedback toward that specific job without requiring it
4. Same 14,000-character cap, automatic model selection, and rate-limit handling as the other text-based tools

### 08 — Job Description Matcher
Same Groq engine as the others. Upload or paste a resume, paste a job description, and get a fit assessment.
1. Same Groq key works here too
2. Output is structured as: Match Score (a percentage with justification), What Matches Well, Gaps & Missing Requirements, Keywords to Add, and Suggested Resume Tweaks
3. The model is explicitly told never to suggest fabricating experience — tweaks are about presentation and emphasis, not invention
4. Since two documents share one request now, each is capped at ~9,000 characters instead of 14,000, to stay within the same free-tier rate limits

### 09 — Cover Letter Generator
Same Groq engine, resume input (upload or paste), and job description paste as the Matcher — this one drafts a letter instead of scoring a fit.
1. Same Groq key works here too
2. Optional fields: your name and the hiring manager/company name, for personalization — left blank, the letter uses a generic professional greeting instead of a placeholder
3. Three tone options: Warm & enthusiastic, Formal & traditional, Concise & direct
4. The model is explicitly instructed to use only real resume content — it will not invent or exaggerate experience to better fit the job
5. Same ~9,000-character-per-document cap as the Job Description Matcher, since two documents share one request
