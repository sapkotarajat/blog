---
title: "The prompt I run before every coding session (full text)"
date: 2026-11-01T19:30:00+05:45
description: "Role, context, rules, output: the four-part prompt I paste before I let an AI touch my code, as text you can copy, plus how to keep it in the repo so every session starts from the same rules."
categories: ["tech"]
tags: ["ai", "claude code", "prompting", "software engineering", "developer workflow"]
slug: "prompt-before-coding"
ShowToc: true
TocOpen: false
draft: false
---

You commented PROMPT. Here it is as text, then the reasons, then how I keep it in the repository so I never have to paste it again.

## The prompt

```text
ROLE
You are a senior engineer on this repository. You have worked on it for a year. You care about the next person who reads the code.

CONTEXT
Stack: <language and versions, framework, database, infra>.
Layout: <where the code lives, the main modules, where tests are>.
Conventions: <formatter, naming, how errors are handled, how config is loaded, what must never be logged>.
Today's task: <one or two sentences>.

RULES
1. Ask before assuming. If anything about the task, the data or the constraints is unclear, ask one clear question and stop.
2. Plan before code. Give me the plan as a short numbered list: files you will touch, what changes in each, what could break. Wait for my OK.
3. Smallest diff that solves the task. No drive-by refactors, no renames, no new dependencies unless you say why.
4. Do not touch files outside the plan. If you must, say so first.
5. Keep behaviour: existing tests must still pass. Add a test for what you change.
6. Never invent an API, a flag or a library function. If you are not sure it exists, check or ask.
7. Secrets, tokens and personal data stay out of the code, the logs and this conversation.

OUTPUT
First the plan. After my OK, a diff I can review, file by file, with a one-line reason per file. Not a rewrite of the file.
```

Fill the angle brackets once. For one of my Django and React projects the context block reads, roughly: Python 3.12, Django 5, DRF, PostgreSQL, React 18 with TypeScript, Docker Compose for local, GitHub Actions for CI; apps under `backend/apps/`, the SPA under `frontend/src/`, tests next to the code; black and isort, errors as typed exceptions, config from environment variables only, nothing from the request body is ever logged.

## Why these four parts

**Role** sets the standard the model holds itself to. "Senior engineer who cares about the next reader" produces different code than "helpful assistant".

**Context** is the part people skip and then blame the model. A model that does not know your folder layout will create a second `utils` folder. It cannot know your conventions unless you state them.

**Rules** are the ones I kept after a few months of real work:

- *Plan before code* is the biggest win. A wrong plan takes ten seconds to correct. Two hundred wrong lines take an hour to unpick.
- *Ask, don't assume* is the second. Models guess by default and guess confidently. One clarifying question beats a confident guess every time.
- *Smallest diff* keeps reviews possible. A rewrite you cannot review is a rewrite you cannot trust.

**Output** says what you want back. A diff with reasons is reviewable; a pasted file is not.

## Keep it in a file

Paste it once into a file at the root of the repository and every session starts from the same rules, not from memory:

- Claude Code reads `CLAUDE.md` automatically.
- Cursor reads `.cursor/rules/` (or the older `.cursorrules`).
- GitHub Copilot reads `.github/copilot-instructions.md`.
- For anything else, keep it as `AI.md` and paste it at the start.

Commit it. Review it like code. When the model makes the same mistake twice, the fix is a new line in the rules, not a longer conversation.

## What I don't put in it

Long style guides. The formatter enforces style; the prompt should hold what the formatter cannot: intent, boundaries, and the order of work. If the file grows past a screen, something belongs in a linter instead.

Questions in the comments on the reel, or on LinkedIn.
