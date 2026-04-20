# LLM Wiki Setup for Claude Code

Paste this entire file into Claude Code to set up a personal AI-powered wiki.
The LLM will ask you a few questions, then build the full environment.

---

You are setting up a personal LLM-powered wiki — a persistent, compounding knowledge base
that you never write yourself. The LLM writes and maintains it. You source, explore, and ask
questions. This follows the pattern described by Andrej Karpathy's LLM Wiki.

The architecture has three layers:
- **raw/** — your source documents (articles, PDFs, notes). Immutable. You add files here.
- **wiki/** — LLM-maintained markdown pages. Summaries, entities, concepts, cross-references.
- **CLAUDE.md** — the schema. Tells the LLM how the wiki is structured and how to operate it.

In practice: Obsidian open on one side, Claude Code on the other. The LLM edits the wiki,
you browse it in real time. Obsidian is the IDE; the LLM is the programmer; the wiki is the codebase.

---

## Step 1 — Ask these questions ONE AT A TIME. Wait for each answer before asking the next.

1. What's your name?
2. What is this wiki for? (domain, topic, or purpose — e.g. "AI research papers", "my startup", "reading a book series")
3. Where is your Obsidian vault? Give the full path. (If you don't use Obsidian, any folder works — the wiki will still be navigable markdown.)
4. What folder names should we use? Suggest `wiki/` for the LLM-maintained pages and `raw/` for your source documents. Confirm or change them.

---

## Step 2 — Create the folder structure

Inside the vault (or chosen root folder), create:

```
[vault]/
├── raw/                    ← source documents (you add files, LLM reads them)
├── wiki/                   ← LLM-maintained pages
│   ├── index.md            ← catalog of all pages (updated on every ingest)
│   └── log.md              ← append-only operation log
└── CLAUDE.md               ← the schema: conventions, workflows, rules for this wiki
```

---

## Step 3 — Create CLAUDE.md (the schema)

This is the most important file. It makes the LLM a disciplined wiki maintainer rather than
a generic chatbot. Write it in the voice of instructions to yourself (future Claude sessions).

Include these sections:

### Wiki Identity
- Owner name, topic/domain, date created
- One paragraph describing what this wiki is for and what a "good answer" looks like here

### Architecture
- raw/ is immutable source of truth — LLM reads, never writes
- wiki/ is owned entirely by the LLM — creates, updates, cross-references pages
- CLAUDE.md is the schema — co-evolved by user and LLM over time

### Workflows

**Ingest** (triggered by `/ingest`):
1. Read the source document
2. Discuss key takeaways with the user — confirm emphasis before writing
3. Write a summary page in wiki/ named after the source
4. Update wiki/index.md — add the new page with a one-line description
5. Identify 3–10 entity or concept pages touched by this source — update or create each
6. Note any contradictions with existing pages and flag them explicitly
7. Append to wiki/log.md: `## [YYYY-MM-DD] ingest | [source title]`

**Query** (triggered by `/wiki`):
1. Read wiki/index.md first to find relevant pages
2. Read the most relevant pages and synthesize an answer with citations
3. For complex questions, read across multiple pages and synthesize
4. Offer to file the answer as a new wiki page if it's worth keeping

**Lint** (triggered by `/lint`):
1. Read wiki/index.md to get the full page list
2. Check each page for: contradictions, stale claims, orphan pages (no inbound links),
   concepts mentioned but lacking their own page, missing cross-references
3. Report findings as a prioritized list with suggested fixes
4. Suggest new sources to investigate, new questions worth asking

### Page Conventions
- YAML frontmatter on every page: `title`, `type` (entity/concept/source/synthesis), `created`, `updated`, `source_count`
- Always use `[[WikiLink]]` syntax for internal cross-references
- Each page ends with a `## Sources` section listing what it's based on
- Synthesis pages (answers worth keeping, comparisons, analyses) are first-class citizens — file them

### Index Format
Each line: `- [[PageName]] — one-line description (type: entity/concept/source/synthesis)`
Grouped by type. LLM updates on every ingest and every filed synthesis.

### Log Format
Each entry starts with `## [YYYY-MM-DD] operation | title`
Operations: `ingest`, `query`, `lint`, `init`
This format is grep-parseable: `grep "^## \[" wiki/log.md | tail -10`

---

## Step 4 — Create the three Claude Code skills

Create these files in `.claude/skills/` inside the current working directory
(wherever Claude Code is open — not necessarily the Obsidian vault).

---

**.claude/skills/wiki/skill.md**

```markdown
---
name: wiki
description: "Query the [TOPIC] wiki — search pages, synthesize answers, file results"
trigger: /wiki
---

# /wiki

Search the wiki at `[WIKI_PATH]`.

## Usage
/wiki                     → read index.md, list all pages with summaries
/wiki <topic>             → find relevant pages, synthesize answer with citations
/wiki <complex question>  → read across multiple pages, build a synthesis

## Rules
1. Always read wiki/index.md first — never guess which pages exist
2. Cite page names in answers: "According to [[PageName]]..."
3. If the answer is worth keeping, offer to file it as a new wiki page
4. If the wiki has no answer, say so — don't fabricate from outside the wiki
```

---

**.claude/skills/ingest/skill.md**

```markdown
---
name: ingest
description: "Ingest a new source document into the [TOPIC] wiki"
trigger: /ingest
---

# /ingest

Ingest a source document into the wiki at `[WIKI_PATH]`.

## Usage
/ingest <path>            → ingest a file from raw/ or any path
/ingest <description>     → ingest content you paste directly

## Workflow (always follow this order)
1. Read the source document fully
2. Discuss key takeaways with the user — ask what to emphasize before writing anything
3. Write wiki/[source-name].md — summary page with YAML frontmatter
4. Update wiki/index.md — add the new page
5. Identify 3–10 entity/concept pages this source touches — update or create each
6. Flag any contradictions with existing pages explicitly
7. Append to wiki/log.md: `## [YYYY-MM-DD] ingest | [source title]`

## Rules
- Confirm with user before writing each major update
- Never ingest more than one source at a time without explicit permission
- Every new page must appear in index.md before the session ends
- If a source contradicts an existing page, update both — note the conflict in each
```

---

**.claude/skills/lint/skill.md**

```markdown
---
name: lint
description: "Health-check the [TOPIC] wiki — find contradictions, orphans, gaps"
trigger: /lint
---

# /lint

Health-check the wiki at `[WIKI_PATH]`.

## What to check
1. **Contradictions** — claims in two pages that conflict with each other
2. **Stale claims** — statements superseded by newer ingested sources
3. **Orphan pages** — pages with no inbound links from other wiki pages
4. **Missing pages** — concepts mentioned but never given their own page
5. **Missing cross-references** — two pages that should link to each other but don't
6. **Data gaps** — important questions the wiki can't answer yet

## Output format
Report findings as a prioritized list:
- 🔴 Critical: contradictions and stale claims
- 🟡 Important: orphans and missing pages
- 🟢 Nice to have: cross-references and data gaps

After the report, suggest:
- 3 new sources worth finding and ingesting
- 3 new questions worth asking the wiki
```

---

## Step 5 — Seed the wiki

Create wiki/index.md:

```markdown
---
title: Wiki Index
updated: [DATE]
---

# Wiki Index

*Read this first when answering any query. Updated by LLM on every ingest.*

## Entities
(none yet)

## Concepts
(none yet)

## Sources
(none yet)

## Syntheses
(none yet)
```

Create wiki/log.md:

```markdown
# Log

*Append-only. Format: `## [YYYY-MM-DD] operation | title`*
*Grep tip: `grep "^## \[" wiki/log.md | tail -10`*

## [DATE] init | Wiki created — [TOPIC] — by [NAME]
```

---

## Step 6 — Show a summary

After everything is created, show the user:

**What was created:**
```
[vault]/
├── raw/                    ← drop source documents here
├── wiki/
│   ├── index.md            ← wiki catalog (auto-maintained)
│   └── log.md              ← operation log
└── CLAUDE.md               ← wiki schema and workflows

.claude/skills/
├── wiki/skill.md           → /wiki <topic>
├── ingest/skill.md         → /ingest <path>
└── lint/skill.md           → /lint
```

**Your three commands:**
- `/ingest raw/[filename]` — process a new source into the wiki
- `/wiki [topic]` — query the wiki for answers
- `/lint` — health-check and find gaps

**First step:** Drop a document into `raw/` and run `/ingest raw/[filename]`.

**Obsidian tip:** Open the vault in Obsidian alongside Claude Code. Use the graph view to see
how pages connect. Every `[[WikiLink]]` the LLM writes becomes a real Obsidian link.
