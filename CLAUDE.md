# CLAUDE.md

This repo contains documentation for a Sales AI Agent (Luma) built on Botpress for Halo Lab.

## How we work together

### 1. Always start by reading the project files

Before making any suggestion or change, read the relevant files in this repo first. Do not rely on memory alone. The source of truth is always the current file content. This applies every time — even if you read the file in a previous session.

### 2. Check Botpress documentation before answering platform questions

When the user asks about Botpress behavior, capabilities, or limitations — use `/botpress-docs` or web search to check the actual Botpress documentation first. Do not guess based on general knowledge.

### 3. Do not assume issues are "Haiku behavioral specifics"

When something doesn't work as expected in the bot, do not default to blaming the model (Haiku). Investigate the actual cause: check the prompt wording, variable setup, node configuration, transition logic, KB content. Model quirks are the last explanation, not the first.

### 4. Use web search when you don't know

If you're unsure about a platform feature, integration, API behavior, or technical detail — run a web search. Do not speculate or give a hedged answer when you can look it up.

### 5. Try harder before giving up

When facing an issue, look at it from multiple angles before concluding it can't be solved. Try different prompt structures, different variable approaches, different node configurations. Exhaust practical options before saying "this is a platform limitation."

### 6. Update this file with collaboration patterns

If you notice a recurring pattern in how we work together (something that works well, a mistake you keep making, a preference the user has), add it to this file so future sessions benefit.

### 7. Read the exact file before suggesting changes

When the user asks about a specific node prompt, KB file, or variable description — read that exact file first. Not "relevant files in general," but the specific file being discussed.

### 8. Check git status at session start

Look at what's uncommitted or in progress from the last session before diving into new work.

### 9. Never auto-commit

Do not commit unless explicitly asked. When asked, commit and push together. User always reviews files first.

### 10. Keep responses concise

No over-explaining, no fluff. Get to the point. The user can ask follow-up questions if they need more detail.

### 11. Open files with TextEdit

Use `open -a TextEdit <path>`, not plain `open` (which fails on .md files on this machine).

### 12. Know the production model

The bot runs on Claude Haiku 4.5 (GPT-5 Mini fallback). Consider what prompt techniques actually work on Haiku when suggesting prompt changes — it follows instructions literally and struggles with negative instructions.

---

## Project structure

```
KB/                          # Knowledge Base files (factual content only, no behavioral rules)
Agent blueprint/
  System prompt/             # Node prompts, global instructions, few-shot examples
  Variable descriptions/     # Enriched Botpress variable descriptions
  Qualification framework/   # Scoring schema, handoff rules
  Diagrams/                  # Flow diagrams
Research/                    # PRD
Archive/                     # Old versions of system prompt files
Archive_KB/                  # Old versions of KB files
```

## Key rules

- **KB = factual data only.** Guardrails, scope rules, and behavioral instructions go in System Prompt (Global Instructions), never in KB files.
- **No competitor names in KB.** Describe categories (e.g., "chatbot builders"), never name specific tools.
- **Avoid "custom" label.** The service uses no-code/low-code platform configuration, not custom development.
- **Platform-agnostic KB.** No Botpress mentions in KB files.
- **Fill gaps with boundary statements** (e.g., "discussed on call"), never fabricate data or add unverified claims.

## File conventions

- **Version in filename only:** `vX.Y` two-part format. No version in H1 titles. No internal `Version:` lines.
- **Dates on all docs:** `> Created: [Month] [Day], [Year] | Updated: [Month] [Day], [Year]`
- **KB front-matter:** All KB files have YAML `type` + `topic` metadata.
- **Case studies:** One file per case study for clean RAG retrieval; brief description under H1 for first-chunk retrieval.

## Versioning workflow (`/update` command)

1. Identify the file and whether change is minor or major
2. Archive the current version to `Archive/` (or `Archive_KB/` for KB files)
3. Create new file in the same directory with bumped version in filename
4. Apply changes, update the `Updated:` date
5. Do NOT commit automatically — user reviews first

Version bumps:
- **Minor** (tweaks, added sections): `v1` -> `v1.1`, `v1.1` -> `v1.2`
- **Major** (structural rework): `v1.2` -> `v2`, `v2.1` -> `v3`

## Writing style (bot personality)

- No em dashes
- No filler words
- No extra trailing spaces
- Few-shot examples go in node instructions, not KB. Mark essential examples with a star, rest is builder reference.

## Session commands

- `/update` — Version bump a doc file (archive old, create new)
- `/remember` — Save session learnings to memory files
- `/handoff` — Write handoff note for next session + update memory

## Contacts

- `salesai@halo-lab.team` — Sales AI inquiries (used in System Prompt)
- `inquiry@halo-lab.com` — Other Halo Lab services (used in KB)
