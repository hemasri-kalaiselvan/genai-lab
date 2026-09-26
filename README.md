# Generative AI Lab

> A personal portal of small, working generative-AI tools — each a standalone project, linked from one landing page, deployed free on GitHub Pages.

**Tech:** HTML, CSS, JavaScript
**Tools:** GitHub, pdf.js
**AI Tools:** Claude, Groq, Pollinations.ai

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
    ├── cover-letter-generator/index.html
    ├── blog-social-generator/index.html
    ├── grammar-writing-improver/index.html
    ├── language-translator/index.html
    ├── voice-ai-assistant/index.html
    ├── speech-to-text-transcriber/index.html
    ├── ai-quiz-generator/index.html
    ├── ai-interview-practice/index.html
    ├── research-paper-summariser/index.html
    ├── document-qa-rag/index.html
    ├── basic-ai-agent/index.html
    ├── sentiment-feedback-analyser/index.html
    ├── code-explainer-debugger/index.html
    ├── prompt-comparison-tool/index.html
    ├── small-model-fine-tuning/index.html
    └── ai-output-evaluation-dashboard/index.html
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

### 10 — Blog & Social Content Generator
Same Groq engine again. Give it a topic or rough outline, pick a format, and get a draft.
1. Same Groq key works here too
2. Four formats, each with format-appropriate output rules: Blog post (markdown, ~500-700 words, headings), LinkedIn post (~100-200 words, hook-first, hashtags), X/Twitter thread (5-8 numbered tweets, each under 280 characters), Instagram caption (~60-150 words, light emoji use, hashtags)
3. Tone options (Professional, Casual, Witty, Authoritative) and an optional audience field to sharpen the draft
4. The model is told not to invent statistics, quotes, or specific claims beyond what's in the topic/outline you gave it

### 11 — Grammar & Writing Improver
Uses the same free Groq API key and automatic model selection as the other text tools. Paste any passage, choose how strongly it should be edited, and get a corrected version ready to copy.
1. Three editing levels: grammar only, grammar plus clarity, or a polished rewrite
2. Six tone choices, including preserving the original tone, professional, academic, friendly, concise, and persuasive
3. Optional explanations list the most important edits without cluttering the corrected text
4. Paragraph and list formatting can be preserved, and names, facts, numbers, and meaning are explicitly protected
5. Input is capped at 14,000 characters to stay within free-tier request limits

### 12 — Language Translator
Uses the same Groq key and automatic model selection. Choose the source and target languages, paste text, and receive a natural translation.
1. Auto-detects the source language or lets the user specify it directly
2. Includes 26 commonly used languages, including Tamil, Hindi, Telugu, Malayalam, Kannada, Bengali, Urdu, Arabic, and major international languages
3. Provides a language-swap control and can move the latest translation back into the input for reverse translation
4. Can preserve paragraphs and lists and optionally use a formal, respectful tone
5. Protects names, numbers, facts, and meaning, with the same 14,000-character free-tier cap

### 13 — Voice-Based AI Assistant
Combines Groq chat with the browser's built-in speech recognition and speech synthesis. It needs no installed software or separate voice API.
1. Tap the microphone, speak a question, and hear the AI answer aloud
2. Supports English (India/US/UK), Tamil, Hindi, Telugu, Malayalam, and Kannada speech settings
3. Keeps a short conversational history so follow-up questions retain context
4. Includes brief, medium, and detailed answer modes, plus a typed-question fallback
5. Voice recognition works best in Chrome or Edge and requires microphone permission; unsupported browsers can still use the typed chat

### 14 — Speech-to-Text Transcriber
Uses Groq's multilingual Whisper model to produce an editable transcript from recorded or uploaded audio.
1. Records directly from the browser microphone or accepts FLAC, MP3, MP4, MPEG, MPGA, M4A, OGG, WAV, and WEBM files
2. Automatically selects `whisper-large-v3-turbo`, with `whisper-large-v3` as a fallback if availability changes
3. Can auto-detect the language or accept an ISO language hint for better accuracy and speed
4. Allows names and technical terms to be supplied as optional transcription context
5. Supports editing, copying, and downloading the transcript as a `.txt` file; audio is limited to 25 MB on Groq's free tier

### 15 — AI Quiz Generator
Uses Groq to create an interactive, automatically scored quiz from a topic or pasted study material.
1. Generates 5, 10, or 15 questions at beginner, intermediate, or advanced difficulty
2. Supports multiple-choice, true/false, or a balanced mixture of both
3. Grades the user's selections in the browser and displays the score, correct answers, and teaching explanations
4. Includes a retry option and downloads the questions plus answer key as a `.txt` file
5. The prompt requires every question to remain grounded in the supplied material and rejects incomplete quiz structures before display

### 16 — AI Interview Practice
Conducts a structured mock interview for a chosen role and evaluates each answer separately.
1. Supports technical, behavioural, or mixed interviews for entry, mid-level, and senior candidates
2. Generates 5, 8, or 10 role-specific questions, optionally guided by a pasted job description
3. Scores each response from 0 to 10 and gives a strength, an improvement point, and a stronger example answer
4. Shows an average score and question-by-question improvement summary at the end
5. Downloads a complete practice report containing all questions, answers, scores, and feedback

### 17 — Research Paper Summariser
Produces a structured, evidence-conscious analysis from an uploaded PDF/TXT paper or pasted text.
1. Extracts PDF text entirely in the browser using pdf.js
2. Covers the problem, novelty, methodology, experimental setup, results, comparisons, limitations, future work, and key terms
3. Offers beginner, engineering-student, and researcher explanation levels plus selectable analysis focus
4. For long papers, samples the beginning, middle, and ending so the conclusion is not lost to a simple first-character cutoff
5. Explicitly marks missing information instead of inventing it and downloads the result as a Markdown file

### 18 — Document Q&A System (Basic RAG)
Demonstrates retrieval-augmented generation without a server or vector-database installation.
1. Accepts multiple PDF and TXT files and extracts their text locally with pdf.js
2. Splits documents into overlapping passages and builds a browser-side weighted keyword index
3. Retrieves the five most relevant passages for each question, then sends only those passages to Groq
4. Instructs the model to answer only from retrieved evidence, acknowledge insufficient evidence, and cite file/page/chunk labels
5. Displays the retrieved passages under every answer so the user can inspect the grounding evidence

### 19 — Basic AI Agent
Demonstrates a controlled plan-act-observe loop in which the model chooses browser tools and uses their returned results.
1. Offers four safe tools: arithmetic calculator, local date/time, text statistics, and unit conversion
2. Lets the model perform up to five tool actions before producing a final answer
3. Uses a custom arithmetic parser instead of JavaScript `eval`, and validates unit categories before conversion
4. Displays each tool request and observation while keeping hidden model reasoning private
5. Runs entirely in the browser apart from Groq model calls and does not permit arbitrary code, web access, or file-system actions

### 20 — Sentiment & Feedback Analyser
Turns individual or batched feedback into validated classifications and practical summaries.
1. Accepts pasted text or TXT/CSV uploads and separates items by line, paragraph, or as one combined entry
2. Analyses up to 30 items per run as positive, neutral, negative, or mixed, with confidence and urgency labels
3. Extracts up to three themes and a short neutral interpretation for every item
4. Displays a sentiment-distribution dashboard, batch summary, recommended actions, and a detailed item table
5. Exports item-level results to UTF-8 CSV and explicitly avoids demographic, personality, or mental-state inferences

### 21 — Code Explainer & Debugger
Analyses pasted code as text without executing it.
1. Includes Explain, Debug, Review, and Optimise modes with beginner, intermediate, and advanced detail levels
2. Supports automatic language detection plus Python, JavaScript, Java, C/C++, C#, MATLAB, HTML/CSS, SQL, PHP, R, and Verilog/VHDL
3. Debug mode accepts the exact error message and expected behaviour, separates confirmed faults from hypotheses, and proposes verification steps
4. Review and optimisation modes cover correctness, security, edge cases, maintainability, performance, and trade-offs without inventing defects
5. Sanitises rendered Markdown, warns users to remove secrets, and exports the result as a Markdown file

### 22 — Prompt Comparison Tool
Runs two prompt variants against one shared input using the same selected Groq model.
1. Supports `{{input}}` placeholders and appends the shared input automatically when no placeholder is present
2. Displays both outputs side by side with measured client-side latency and API token usage when returned
3. Evaluates relevance, clarity, completeness, factual accuracy, and format compliance using selectable criteria
4. Randomly presents outputs as X and Y to the evaluator to reduce prompt-label bias, then maps the scores back to A and B
5. States that AI judging is not an objective benchmark and exports the full comparison as JSON

### 23 — Small Model Fine-Tuning Experiment
Keeps the portfolio's no-local-install promise by preparing data in the browser and running training in Google Colab.
1. Builds or imports instruction/input/output examples and validates every JSONL row
2. Creates a deterministic 80/20 train-validation split in TRL's conversational prompt-completion format
3. Downloads `train.jsonl`, `validation.jsonl`, and a ready-to-run Colab notebook
4. Fine-tunes `HuggingFaceTB/SmolLM2-135M-Instruct` using TRL SFTTrainer and a PEFT LoRA adapter, then evaluates, tests, saves, and downloads the adapter
5. Warns about dataset size, accuracy, privacy, licensing, bias, memorisation, and the fact that free Colab GPU availability is not guaranteed

### 24 — AI Output Evaluation Dashboard
Evaluates and ranks two to six candidate outputs against one task and optional reference answer.
1. Accepts manually pasted candidates or imports outputs from Project 22's JSON comparison report
2. Scores selectable criteria: relevance, accuracy, completeness, clarity, conciseness, and instruction following
3. Randomises candidates behind anonymous IDs before evaluation and restores their original labels afterward
4. Shows an overall ranking, per-criterion score table, and strengths/weaknesses cards
5. Exports both UTF-8 CSV and complete JSON reports and states that AI scoring must be supplemented by human judgement
