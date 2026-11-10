© 2026 Xue Xiao  
SPDX-License-Identifier: Apache-2.0  
Version 2.0 · 6 November 2026 · first released 19 July 2026

# 📝 Referee Skill

A structured referee-review workflow in Claude designed for finance, economics and real estate related manuscripts.

Peer review is an important service to the academic community, but doing it well takes considerable time and effort. Referee skill is designed to make the process more manageable without losing what makes it valuable: your independent judgment and constructive feedback.

- 🧠 **You** focus on evaluating the paper and developing your key comments.
- 🛠️ **This skill** helps fact-check your comments against the paper, then turns them into a polished report in your preferred format.
- 🛠️ **This skill** also prepares an independent review, which stays hidden until you've written your comments. You can then compare perspectives, revisit your arguments, and strengthen your final report.

🎯 <ins>***The goal is simple: save time on the process while keeping your human intellectual contribution at the center of peer review.***</ins>

> 💡 **Note:** You can also use this skill to study a paper like a referee.

## 📦 What's here

```
referee/
├── SKILL.md      the workflow
├── README.md     this file
├── LICENSE.md    Apache-2.0 license text
└── templates/    default reading structure and report format
```

## ⚙️ Installing

**In the Claude app (web, desktop or mobile)**

1. Get the latest released ZIP, the file named `referee_….zip`. On GitHub, it's under **Releases**. DO NOT use "Source code" or the green **Code → Download ZIP** button.
2. Go to **Customize → Skills**.
3. Click **+**, then **Create skill → Upload a skill**, and choose the ZIP.
4. Make sure `referee` is switched on.

**In Claude Code**

Unzip the latest released ZIP and copy the `referee/` folder into `~/.claude/skills/`.

## 👓 Starting a review

Make a **new folder for the paper** — one folder per manuscript, not the skill's own folder. Inside it:

```
your_case_folder/
├── input/        put the manuscript PDF here
└── output/       leave empty; deliverables land here
```

Then ask Claude to referee the paper. It will walk you through the rest.

## 🗂️ The two structure files

Two files decide what your review looks like:

- **`read_paper.docx`** — the headings Claude fills in when it reads the paper.
- **`referee_report_template.docx`** — the format your final report takes.

At the start of a case Claude will ask which you want:

1. **The bundled defaults** in `templates/`. Fine for a first run.
2. **Your own.** If you or your journal already has a reading structure or a report format, put those two files in the case's `input/` under those names. Claude will check the report template has room for a summary, repeating major-comment blocks, and minor comments, and will tell you if something is missing.

You can edit the bundled defaults to suit yourself once you've run a case or two.

## 📄 What you get

| File | What it is |
|---|---|
| `output/1_paper_ingestion.docx` | The paper read into your structure |
| `output/2_<topic>.docx` | Anything extra you asked for |
| `output/3_referee_report.docx` | The referee report |
| `output/4_editor_letter.docx` | The letter to the editor, as a separate file |

Claude's own sealed draft sits in `input/.staging/` until you have written your comments. **Don't open it before then** — the whole method rests on that.

## 🔒 Confidentiality

A paper under review belongs to its authors and the journal. Nothing from the manuscript, your comments, or your editor-only remarks is written to Claude's memory. At the end of a case Claude offers to clear the working files, and reminds you that the conversation itself still holds the paper — deleting that one is your step.
