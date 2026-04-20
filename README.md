# refactor-llm-wiki

A setup prompt that turns Claude Code into a persistent, compounding knowledge base — maintained entirely by AI.

Inspired by [Andrej Karpathy's LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f).

---

## The idea

Most LLM tools re-derive knowledge from scratch on every question. This is different.

The LLM **builds and maintains a wiki** — a structured, interlinked collection of markdown files. When you add a source, it doesn't just index it. It reads it, extracts key information, and integrates it into the existing wiki: updating entity pages, flagging contradictions, strengthening the synthesis. The knowledge compounds.

You never write the wiki. The LLM does.  
You source, explore, and ask questions.  
The LLM summarizes, cross-references, files, and keeps everything current.

In practice: **Obsidian open on one side, Claude Code on the other.** The LLM edits the wiki, you browse it in real time — following links, checking the graph view, reading updated pages.

---

## What gets set up

Paste the prompt → answer 4 questions → done.

**Folder structure inside your Obsidian vault (or any folder):**
```
[vault]/
├── raw/          ← drop source documents here (immutable)
├── wiki/
│   ├── index.md  ← LLM-maintained catalog of all pages
│   └── log.md    ← append-only operation log
└── CLAUDE.md     ← the schema: conventions, workflows, rules
```

**Three Claude Code slash commands:**
```
/ingest raw/[file]    ← process a new source into the wiki
/wiki [topic]         ← query the wiki for answers
/lint                 ← health-check, find contradictions and gaps
```

---

## How to use

1. Open Claude Code in any folder (your Obsidian vault, a project, anywhere)
2. Open `prompt.md` from this repo and paste the entire contents into Claude Code
3. Answer 4 questions about your name, wiki topic, vault path, and folder names
4. Claude builds the environment — CLAUDE.md, folder structure, and all three skills
5. Drop a file into `raw/` and run `/ingest raw/[filename]` to start

---

## The three operations

**`/ingest`** — Process a new source  
Reads the document, discusses takeaways with you, writes a summary page, updates the index, touches 3–10 entity/concept pages, flags contradictions, appends to the log. A single source might update 10–15 wiki pages.

**`/wiki`** — Query the wiki  
Reads the index first, finds relevant pages, synthesizes an answer with citations. Good answers get filed back into the wiki as new pages — your explorations compound just like ingested sources do.

**`/lint`** — Health-check  
Finds contradictions between pages, stale claims, orphan pages, missing cross-references, and data gaps. Suggests new sources to find and new questions to ask.

---

## Why Obsidian

The wiki is just a folder of markdown files — it works without Obsidian. But Obsidian adds:

- **Graph view** — see which pages are hubs, which are orphans, how knowledge connects
- **`[[WikiLink]]` navigation** — every link the LLM writes becomes a clickable link
- **Obsidian Web Clipper** — browser extension that converts articles to markdown, ready to drop into `raw/`
- **Dataview plugin** — query YAML frontmatter the LLM writes; generate dynamic tables and lists

---

## Files in this repo

```
prompt.md                   ← paste this into Claude Code to set up
example-wiki/
  getting-started.md        ← what the wiki folder looks like after setup
  decisions.md              ← template for architecture decision records
```

---

Made by [@razvanneculai](https://github.com/razvanneculai)  
Based on the LLM Wiki pattern by [@karpathy](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
