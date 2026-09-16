# Retrieve — IELTS Reading Retrieval Practice

A fully functional, installable PWA for improving IELTS Reading from
Band 6–6.5 toward Band 9 using retrieval practice:

**Read → Close Passage → Retrieve → Check → Correct → Retrieve Again → Spaced Review**

No build step, no backend, no external JS dependencies. Everything runs
client-side and persists to `localStorage`.

## Running it

Service workers (required for offline/PWA install) only work over
**HTTPS or `localhost`** — opening `index.html` directly via `file://`
will run the app fine, but installability and offline caching won't work.

Easiest local option:

```bash
cd ielts-app
python3 -m http.server 8080
# then open http://localhost:8080
```

Or deploy the folder as-is to any static host (Netlify, GitHub Pages,
Vercel, Cloudflare Pages). No configuration needed — it's static files.

## What's included

- `index.html` — app shell (topbar, bottom tab nav)
- `css/style.css` — full design system (light + dark themes)
- `js/data.js` — 3 original IELTS-style passages (700–900 words each),
  each with paragraph-purpose recall answers, vocabulary-in-context items,
  paraphrase pairs, and a full set of IELTS question types
- `js/app.js` — all app logic: router, spaced-repetition engine, error
  logging, adaptive practice, dashboard
- `manifest.json`, `service-worker.js`, `icons/` — PWA install + offline support

## How the retrieval loop works

1. **Read** a passage (timer running, paragraphs labeled A, B, C…).
2. **Close** it — tapping "Close passage" hides the text and moves you
   into free recall.
3. **Retrieve**: type the main idea and each paragraph's purpose from
   memory, then reveal the model answer and self-grade (self-grading is
   used for free recall since there's no way to auto-mark open text).
4. Move into **IELTS question types** (True/False/Not Given, Matching
   Headings, Multiple Choice, Matching Information, Summary Completion,
   Sentence Completion) — still without the passage visible.
5. **Evidence training**: before the answer is revealed, you're asked
   which paragraph supports it. Getting the answer right but the
   evidence wrong (or vice versa) is graded as "partial," not "correct."
6. **Correct**: wrong or partial answers can be tagged with a mistake
   type (paraphrase, vocabulary, inference, distractor, careless,
   wrong paragraph…) and are logged to the **Error Log**.
7. **Spaced Review**: every graded item — free recall, questions,
   paraphrase drills — goes into a spaced-repetition queue with the
   schedule 10 min → 1 day → 3 days → 7 days → 14 days → 30 days.
   Wrong answers reset to the 10-minute box; correct answers advance.
8. **Adaptive Practice**: pulls extra questions weighted toward your
   currently weakest skills (by recent accuracy), across all passages.
9. **Dashboard**: streak, recent accuracy, reviews due, 7-day activity,
   per-skill accuracy bars, and recurring mistake patterns.

## Adding more passages

Append a new object to the `PASSAGES` array in `js/data.js` following
the existing shape (`paragraphs`, `freeRecall`, `vocabulary`,
`paraphrasePairs`, `questions`). Nothing elsewhere needs to change —
the reader, retrieval flow, practice engine, paraphrase trainer, SRS,
error log, and dashboard all read from this array and the skill tags
on each question.

## Testing persistence

1. Complete a passage's free recall and a few questions.
2. Close the browser tab (or fully quit the installed app).
3. Reopen — your streak, accuracy, error log, and due-review schedule
   should all be exactly as you left them (everything lives in
   `localStorage` under keys prefixed `ielts_`).
