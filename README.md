# refactor-llm-wiki

Give Claude Code a searchable long-term memory for any project — in one paste.

---

## What it does

You paste one prompt into Claude Code. Claude asks you 4 questions. You answer them. Done.

You end up with:
- A `/wiki` slash command that searches your personal notes folder
- A `CLAUDE.md` that tells Claude to use your wiki for context
- A starter note to get you going

After that, you grow your wiki by dropping `.md` files into a folder. Claude finds them automatically with `/wiki topic`.

---

## How to use

1. Open Claude Code in any project folder (or an empty one)
2. Copy the prompt below and paste it into the chat
3. Answer 4 quick questions
4. Type `/wiki` to test it

---

## The prompt

Copy everything in the block below and paste it into Claude Code:

```
Set up a personal AI wiki for this Claude Code workspace.

Walk me through 4 quick questions, one at a time — wait for my answer before asking the next:

1. What's your name?
2. What is this project or workspace for? (one or two sentences)
3. Where should wiki files be stored? Suggest ~/Documents/[project-name]-wiki, but I can pick any path.
4. What kinds of notes will you keep here? (examples: architecture decisions, meeting notes, research, specs)

After I answer all 4, create these three things:

---

.claude/skills/wiki/skill.md — a /wiki skill pointed at my chosen folder:
- /wiki with no args → list available files and summarize what's there
- /wiki <topic> → search files by name and content, read the most relevant one, answer the question
- Fall back to raw source files only if the wiki folder has no answer

---

CLAUDE.md — create it if it doesn't exist, or append to it if it does. Include:
- My project description (from question 2)
- A clear instruction: always use /wiki [topic] before answering questions about project context, decisions, or history

---

[wiki-folder]/getting-started.md — a short welcome note, personalized with my name, that explains:
- What this wiki is and why it exists
- How to add notes (create any .md file in the folder)
- How to search with /wiki topic

---

When everything is created, show me a short summary of what was set up and one example of how to use /wiki.
```

---

## How it works

Claude Code has a [skills system](https://docs.anthropic.com/en/docs/claude-code/slash-commands) where `.md` files in `.claude/skills/` become slash commands. This prompt wires up a `/wiki` skill that points at a folder you own — no plugins, no extensions, no sync required.

Your wiki folder is just a folder of markdown files. Add a file, and Claude can find it on the next `/wiki` call.

---

## Example workflow

```
/wiki                          → lists your notes
/wiki auth decisions           → finds your auth architecture note
/wiki why did we choose X      → searches by content, not just filename
```

---

## Files in this repo

```
prompt.md              the raw prompt (same as above, easy to copy)
example-wiki/
  getting-started.md   sample starter note
  decisions.md         template for logging architecture decisions
```

---

Made by [@razvanneculai](https://github.com/razvanneculai)
