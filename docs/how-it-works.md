# How it works

## The core idea

We built Loomnovel around the way real authors work, and we let each kind of model do what it does best.

When people write a book, writing, editing and polishing are separate roles, and the same is true for models. Load the writing step with too many instructions and the model's own creativity and prose quality drop noticeably. So much of the later work belongs to a dedicated polishing role, or to you, the author.

## Four ways in

The Loomnovel home page has four ways in. You can hand the whole process to the agent, or work without it and arrange every step yourself.

### Weaver · Agent chat · Start here

Weaver is your own editorial team: an agent built and tuned for writing. From starting a book to finishing a chapter, all it takes is conversation. Talk through plot and setting with it, have it look things up in your manuscript and worldbook, or pull up a passage and ask how to improve it. It plans chapters, drafts, reviews, and syncs new facts from the text into your worldbook. Setting documents, character lists and sample chapters can be handed to it directly with "+ File" next to the chat box.

Two modes, and you can switch at any time:

- **Ask me at each step** (the default): every change Weaver wants to make first appears as a proposal card, and nothing takes effect until you click it.
- **Auto**: Weaver takes over and runs the chapter plan, the draft, the review and the worldbook sync in a row. It stops when the chapter is done and waits for you before the next one. It pauses automatically at the spending limit you set, and every change can be rolled back from the version history. De-AI is left to you: you read the draft and decide passage by passage.

Weaver works on the server. Close the page or lose your connection, and it still finishes the job.

### Cockpit · You write, AI helps you polish · For professionals

No agent: you arrange every step yourself. Look at your work and edit by hand at any time. You can also bring in a book you are already writing, whole: it is split into chapters, a worldbook and per-chapter summaries are built from the text, and you carry on writing from there.

- Draft passage by passage, de-AI, and style re-render (rewrite a whole passage in your target style without touching the plot).
- Every writing run, de-AI pass, style re-render and accepted hand edit is saved as a version. Compare any two side by side and switch back in one click.
- Every worldbook entry can be set to "Look up when needed" or "Always include", and you see on the spot how many tokens it takes.

### The Study · Longhand only

A blank page, every word yours. You don't want AI touching your prose, but you would like help keeping your notes in order and someone to think things through with? In the Study, AI only maintains your worldbook, talks through outline and plot with you, and looks things up for you. The text always comes from your hand alone.

### De-AI Studio · Already written elsewhere

Wrote it somewhere else and it reads too much like AI, or isn't the voice you want? The studio offers polishing made for fiction, and works on drafts from most mainstream models.

- **Quick De-AI**: paste and go. It only cuts and adjusts, never rewrites.
- **Style polish**: build a style profile first, then whole passages are rewritten to it, in the voice you want.

## Bring your own agent

Create an access token in the settings page, and Claude Code, Cursor or any other MCP client can work directly with your manuscript, worldbook, search and editorial team. The MCP server has 18 tools in five groups: reading the book, searching and recalling earlier text, planning and drafting, reviewing and revising, and saving. A token sees only your own books, shares the same daily quota as the web app, and can be revoked at any time.

## What all the ways in share

- **Import a long manuscript.** Upload an existing manuscript: it is split into chapters, and a worldbook (characters, places, factions, timeline) and searchable per-chapter summaries are built from it.
- **The worldbook grows with the text.** After each chapter, newly established facts (who was injured, which alliance changed) are offered to you as worldbook updates to confirm.
- **Recall of earlier text.** When you write a new chapter, the system finds the relevant earlier text and setting by itself, to make sure, as far as possible, that even with a very long context the model gets enough complete information to keep setting and story consistent.
- **Review and whole-book check.** Review gives editorial comments with reasons; you decide what to act on. The whole-book check reads the entire book and finds continuity problems across chapters.
- **Edit memory.** Loomnovel remembers the changes you make by hand and follows them in later revisions.
- **Style.** Pick a built-in style, or upload your own sample chapters to derive your style profile; every chapter after that is written to it.
- **Choice of model.** Drafting and editing can use any of several models, and each option shows roughly what it costs per thousand words.
