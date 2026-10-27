# kant.dev agent skills

Agent skills that give a coding agent (Claude Code, Codex, Cursor, and
other tools that read `SKILL.md`) access to the free website checks from
[kant.dev](https://kant.dev).

| Skill | What it does |
| --- | --- |
| [`ai-website-audit`](skills/ai-website-audit/SKILL.md) | Audits a public URL for AI search and AI agents: AI visibility, AI crawler access (robots.txt and firewall), content that needs JavaScript, and whether browser agents can use the page. Four free checks, no API key. |

## Install

```bash
npx skills add philippkant/kant-dev-skills
```

Or copy `skills/ai-website-audit/` into your agent's skills folder, for
example `~/.claude/skills/` for Claude Code.

Then ask your agent something like "Can ChatGPT and AI agents use
example.com?" or "Run an AI website audit on our pricing page."

## How it works

The skill calls the same engine that runs the tools on
[kant.dev/tools](https://kant.dev/tools/), at `https://api.kant.dev`.
Each check takes one public URL and returns JSON; nothing is stored.
Every result can also be opened as a visual report on kant.dev.

## About

Built by Philipp Kant at [kant.dev](https://kant.dev), a technical
partner for AI, MDM/PIM, and business automation. Questions or a check
that misreads a site: [philipp@kant.dev](mailto:philipp@kant.dev).

MIT licensed.
