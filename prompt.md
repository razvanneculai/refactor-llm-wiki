Set up a personal AI wiki for this Claude Code workspace.

Walk me through 4 quick questions, one at a time — wait for my answer before asking the next:

1. What's your name?
2. What is this project or workspace for? (one or two sentences)
3. Where should wiki files be stored? Suggest `~/Documents/[project-name]-wiki`, but I can pick any path.
4. What kinds of notes will you keep here? (examples: architecture decisions, meeting notes, research, specs)

After I answer all 4, create these three things:

---

**.claude/skills/wiki/skill.md** — a /wiki skill pointed at my chosen folder:
- `/wiki` with no args → list available files and summarize what's there
- `/wiki <topic>` → search files by name and content, read the most relevant one, answer the question
- Fall back to raw source files only if the wiki folder has no answer

---

**CLAUDE.md** — create it if it doesn't exist, or append to it if it does. Include:
- My project description (from question 2)
- A clear instruction: always use `/wiki [topic]` before answering questions about project context, decisions, or history

---

**[wiki-folder]/getting-started.md** — a short welcome note, personalized with my name, that explains:
- What this wiki is and why it exists
- How to add notes (create any .md file in the folder)
- How to search with `/wiki topic`

---

When everything is created, show me a short summary of what was set up and one example of how to use `/wiki`.
