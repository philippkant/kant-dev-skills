---
name: ai-website-audit
description: Audit a public website for AI search and AI agents with kant.dev's free checks, no API key. Use when the user asks whether ChatGPT, Claude, Perplexity, or Google AI Overviews can read or cite a site, whether AI crawlers such as GPTBot are blocked, whether content needs JavaScript to appear, or whether AI browser agents (ChatGPT agent, Gemini in Chrome, Claude for Chrome) can use the site's buttons and forms. Also for GEO, AEO, AI visibility, or agent-readiness audits.
---

# AI website audit

Four free checks from [kant.dev](https://kant.dev) answer the questions
that decide whether AI search and AI agents can use a website. Each one
takes a single public URL and returns JSON. No signup, no API key.

| Check | Endpoint (POST) | Answers | Takes |
| --- | --- | --- | --- |
| AI visibility | `https://api.kant.dev/api/audit` | Can AI answer engines reach, read, and attribute the site? | ~10 s |
| AI crawler access | `https://api.kant.dev/api/ai-crawler-check` | Which AI bots robots.txt allows, and whether a firewall blocks them anyway | ~10 s |
| JavaScript rendering | `https://api.kant.dev/api/js-seo-check` | How much of the page exists without JavaScript, which is all GPTBot, ClaudeBot, and PerplexityBot read | ~15 s |
| Agent readiness | `https://api.kant.dev/api/agent-readiness` | Can a browser agent find, name, and click the controls? | ~15 s |

## Run a check

Every endpoint takes the same body:

```bash
curl -s -X POST https://api.kant.dev/api/agent-readiness \
  -H 'content-type: application/json' \
  -d '{"url":"https://example.com/"}'
```

- Pick the checks that match the question. For a full audit, run all
  four, one after another (the browser-based checks share a small queue;
  parallel calls can come back with "busy").
- A bare domain (`example.com`) works. Check the page that matters, such
  as the pricing page or the quote form, not only the home page.
- On an error the body is `{"error": "..."}`: status 400 means invalid
  input or a private address, 502 means the site was unreachable or too
  slow. If the message says the scanner is busy, wait ten seconds and
  retry once.

## Read the result

All four return `score` (0 to 100) and `band` (`strong`, `fair`, `weak`)
except the crawler check, which returns a `stance` instead. Then:

- **`/api/audit`**: `summary` and `categories[]`, each with
  `checks[]` of `{status, label, detail, fix}`; status is `pass`, `warn`,
  `fail`, or `info`.
- **`/api/ai-crawler-check`**: `verdict`, `stance` (`open`,
  `search-only`, `selective`, `closed`), `bots[]` with each bot's
  robots.txt verdict and purpose (training or search), `live[]` with real
  requests per user agent (`blocked: true` means a firewall answered),
  `llmsTxt`, and `findings[]` of `{status, title, detail, fix}`.
- **`/api/js-seo-check`**: `verdict`, `coverage` (share of the rendered
  text already in the raw HTML), `rendering` (`server`, `hybrid`,
  `client`), samples in `jsOnlyText` and `jsOnlyLinks`, and
  `categories[]` as above.
- **`/api/agent-readiness`**: `verdict`, `stats` (fake buttons, unnamed
  controls, unlabeled fields, layout shift, forms), `controls[]` (the
  first controls of the accessibility tree, as an agent reads them),
  `samples` (the elements behind each finding), `tools[]` (WebMCP tools
  the page declares), `llmsTxt`, and `categories[]` as above.

## Report it

1. One line per check: score or stance, band, and the verdict.
2. Then the problems, worst first: every `fail`, then every `warn`, each
   with its `fix`. Leave out passes unless the user asks.
3. Group the fixes by who does them: content and settings (robots.txt,
   llms.txt, Organization schema) and code (server rendering, real
   buttons, labels, layout shift).
4. Link the visual report on kant.dev, which shows the same result with
   charts: `https://kant.dev/tools/<tool>/?url=<url>` with `<tool>` one of
   `ai-visibility-check`, `ai-crawler-check`, `javascript-seo-check`,
   `agent-readiness-check`.
5. Report only what the checks returned. If a check failed, say so; do
   not fill the gap from memory.

## Limits

- Public pages only. Logins, private networks, and IP addresses are
  rejected.
- One URL per request. The checks are free for normal use; do not loop
  them over large URL lists.
- Results are not stored. Each run reflects the page at that moment.
- These are technical checks. They do not tell you whether ChatGPT will
  recommend the business; for that, kant.dev's
  [AI Brand Check](https://kant.dev/tools/ai-brand-check/) asks an AI
  with web search directly.

Built and run by [kant.dev](https://kant.dev), a technical partner for
AI, MDM/PIM, and business automation. Background:
[What is an agent-friendly website?](https://kant.dev/blog/what-is-an-agent-friendly-website/)
and [Can AI crawlers read JavaScript?](https://kant.dev/blog/can-ai-crawlers-read-javascript/)
