---
name: referee
description: This is a human-centered AI assisted referee skill, which runs a structured referee review of an academic manuscript, keeping the assistant's opinion sealed until the user has written their own. Use when the user wants to review a paper, or prepare a referee report. Produces 1) a paper summary in the user's reading structure, 2) a sealed independent draft, 3) a fact check of the user's own comments against the paper, 4) a final referee report, and a letter to the editor.
license: "Apache-2.0. © 2026 Xue Xiao. See LICENSE.md"
metadata:
  version: "5.0"
  created: "2026-07-19"
  updated: "2026-11-06"
---

# Referee Skill

A five-phase review workflow.

The point of it: peer review is only as good as the referee's own judgment. So the skill splits the work:

- **The user does the thinking.** Their comments are the core of the report.
- **The assistant does the legwork.** It reads the paper into a structure, cross-checks the user's comments against the paper for errors, and builds the report and letter in the right format.
- **The assistant's view stays sealed.** It writes its own read of the paper first, seals it, and shows it only after the user has written their comments.

Read this whole file before starting. Run the phases in order. Ask one question at a time and wait for the answer.

## Starting a new case

One project folder per paper. Before Phase 1, check all of this:

1. **A new project folder for this paper.** Not shared with another review, and not the skill's own folder.
2. **The manuscript** is in `input/`.
3. **Both structure files** are in `input/` — `read_paper.docx` and `referee_report_template.docx`. See **Setting up the two structure files** below.
4. **`output/` exists** and is empty.
5. **You know which folder is the case folder.** If more than one folder is connected, ask.

If the manuscript is missing, say so and stop. Run the structure-file setup step below at the start of every case, whether or not the files are already there.

## Before starting

Ask the user: **"Do you want me to keep a `project_log.md` for this case?"**

- If yes, create it in the case folder. Add a dated entry each time a phase finishes or something changes — what was made, what they decided, what is still open. In a referee case this log is about the review itself, so it contains case material and gets deleted at the end.
- If no, do not create it and do not ask again.
- Ask once, at the start. Write the answer in `.referee_state.md` so a later session does not ask twice.

## What you need

Check for these before starting. If anything required is missing, ask for it and do not start Phase 1.

| Input | Required? | What it is |
|---|---|---|
| Manuscript | **Required** | The paper being reviewed, as a PDF, in `input/`. |
| `read_paper.docx` | **Required** | The reading structure — the headings Phase 1 fills in. See the setup step below. |
| `referee_report_template.docx` | **Required** | The report format Phase 4 fills in. See the setup step below. |
| Target journal | Optional (beta) | If given, Claude sets how demanding the review is and how the editor letter reads, using its own general sense of the journal. If not given, do not name or guess a journal. |
| Review deadline | Optional | Affects pacing only. |
| Author names | **Do not collect** | The review works from the paper alone. Do not look the authors up. If the user mentions them, do not use it. |

### Setting up the two structure files

These two files decide what the review looks like. `read_paper.docx` sets the headings Phase 1 fills in. `referee_report_template.docx` sets the format Phase 4 fills in.

**Ask at the start of every case, even if both files are already in `input/`.** Use these words:

> "This review uses two structure files: a reading structure (`read_paper.docx`) and a report format (`referee_report_template.docx`). Would you like to use the defaults that come with the skill, or your own files?"

If both files are already in `input/`, say so first — "I found both files in `input/`." — and treat "your own files" as using those. Ask once per case. Write the answer in `.referee_state.md` so a later session does not ask twice.

Then act on the answer:

1. **Use the bundled defaults.** A `templates/` folder ships next to this `SKILL.md`. Copy both files from it into the case's `input/` yourself — the user does not need to do anything. Never edit the originals — every future case uses them.
   - **If you cannot copy them into `input/`** (for example, no write access to the case folder), send both files in the chat and ask the user to save them into `input/` under the same names.
   - **If the `templates/` folder did not ship with the install**, there is nothing to send. Say so plainly, and ask the user either to upload their own files (option 2) or to reinstall the skill with its `templates/` folder. Do not invent a template file and do not guess at a format.
2. **Supply their own.** The user has a reading structure or a report format they already use, or their journal requires one. Ask them to upload the files under the names `read_paper.docx` and `referee_report_template.docx`, or tell you where they are so you can copy them in. A `.docx` is expected; if they offer another format, say what you can and cannot keep.

Do not start Phase 1 until both files are in `input/`. There is no plain-structure fallback.

### Checking a supplied template

Whenever `referee_report_template.docx` did not come from the bundled defaults, read it before Phase 1 — `pandoc -t markdown input/referee_report_template.docx` — and check it has:

- A place for a summary of the paper.
- Repeating blocks for major comments, so Phase 4 can add or remove one.
- A place for minor comments.

If any of these is absent, tell the user which one, and ask whether to add the missing part to their template or to work around it. Do not silently reshape their file, and do not start Phase 1 with a template you cannot fill.

## Folders

**Every path in this file is inside the case project folder** — the folder holding the paper being reviewed. All files are written there.

If more than one folder is connected, ask which one is the case folder before writing anything. The skill's own folder — the one holding this `SKILL.md` and its `templates/` — is only a place to copy templates from. **Never write anything into it** — no reports, no drafts, no state file, no log.

Set these up at the start of a case. Ask for the paths if they are not obvious.

| Path | What goes there |
|---|---|
| `input/` | The manuscript, `read_paper.docx`, `referee_report_template.docx` |
| `input/.staging/` | The sealed draft. Not shown to the user until the gate opens. |
| `output/` | All numbered files the user keeps |
| `.referee_state.md` | Notes for the skill only — where the sealed draft is, whether a log is wanted, which structure files are in use, which phase is current. Made fresh for each case. Not something the user reads or maintains. |

## Confidentiality and clean-up

A paper under review is confidential. It belongs to its authors and the journal.

**Never save any of this to memory:**

- Anything from the paper — title, authors, hypotheses, data, results, numbers, quotes.
- The user's comments.
- The assistant's draft criticisms.
- Anything written for the editor's eyes only.
- Anything that identifies the paper, its authors, or the journal.

How the user likes to work can be remembered. What any particular paper says cannot.

**At the end**, once the editor letter is delivered, ask: *"Is the referee work for this paper finished?"*

**If yes** — ask *"Shall I clear the working files?"* On a yes:

- Delete `input/.staging/` and everything in it.
- Delete `.referee_state.md`.
- Delete `project_log.md`, if one was kept. It contains case material and is no longer needed.
- Leave `output/` and the manuscript alone. Those are the user's to keep or delete.

**If no** — more work is coming, such as a revised version or a second round. Keep everything, and note in the state file that the case is still open.

If the user says no to deleting, say plainly which files are still on disk.

**Last, either way:** remind the user that this conversation still holds the whole paper, and ask *"Do you want to delete this conversation once the review is submitted?"* The assistant cannot delete it. Say plainly that the user has to do this from their chat history.

## Rules that apply throughout

- **Stick to what the paper says.** No guessing, no filling in gaps, no reading between the lines. If something is an interpretation, say it is the authors'.
- **Point to where everything comes from** — a page, table, figure, or section. Any number quoted must be read off the source, not recalled.
- **Never save the user's comments to memory.**
- **Never save editor-only material to memory.**

## Phase 1 — Read the paper

Fill in the `read_paper.docx` structure from the manuscript. Use the headings exactly as that file has them — do not add, drop, or reorder them.

- Output: `output/1_paper_ingestion.docx`, plus a short summary in chat.
- Describe only. See the rules above.

## Checkpoint A

Ask what the user wants explained further or looked into. Handle side requests here — for example, "find the three closest papers in the literature."

- Output: `output/2_<topic>.docx`, named after the question actually asked.

## Phase 2 — Sealed draft

Write an independent referee report. Do not use, guess at, or work around the user's views.

**Shape:** a summary, then exactly 3 major and 3 minor comments.

**The summary covers, in this order:**

1. What the paper does.
2. **What it is built on.** This depends on the kind of paper:
   - **Empirical** — what data it uses, and what hypothesis it tests.
   - **Theoretical** — what model it develops or proposes: the setting, who the agents are, what they choose, and the main assumptions the results turn on.
   - **Both** — the model first, then the data used to take it to evidence. Do not drop either.
3. The key results — one short sentence for each main results section. In a theory paper these are the propositions or main results, one sentence each.
4. What the paper concludes.

The final report in Phase 4 uses this same summary: same four parts, same order, about one paragraph of 150 words. Keep them the same so the summary can carry through without rewriting. The summary describes the paper. It does not argue with it.

**Also list any clear mistakes** — panels or tables labelled wrongly, typos, numbers that disagree with each other or with the text, references that point nowhere, terms never defined. Only things that are plainly wrong on the page. A choice you would have made differently is not a mistake.

**What to do with the file:**

- Save it to `input/.staging/claude_draft.docx`. Never to `output/`. Never show it in chat.
- The folder name starts with a dot so it does not show up in normal browsing. The whole thing rests on the user agreeing not to open it before they write their own comments — tell them this when the draft is made.
- Write where the draft is, and that it is waiting, into `.referee_state.md`. The user may take days.
- While the draft is sealed, do not quote it, summarize it, or hint at what is in it.

## Gate — the user writes their own comments

Ask how they want to write, before they start:

- **Free** — anything they want, in any order.
- **By theme** — one comment on each of these, in order:
  1. Contribution
  2. Whether the research question and the results match
  3. An empirical concern

Either way: suggest nothing, give no examples, and do not react as the comments come in.

### Opening the gate

Once the user has finished, ask: **"Should I release my report now?"**

- Only on a yes, open the sealed draft and go to Phase 3.
- If the sealed draft is gone, say so and ask before writing a new one. Never rebuild it from the conversation. A new draft must be written from the paper alone, before rereading the user's comments — and tell the user it was rewritten, so it is not as strictly independent as the original.
- Once the gate is open, the draft can be moved into `output/` if the user wants to keep it.

## Phase 3 — Show the draft and check

Show the draft. Then check the user's comments against the paper.

- Look only for places where the user has read the paper wrongly — where the paper clearly says otherwise.
- Do **not** flag a comment just because the paper is unclear or badly written. That is the authors' problem, not the user's.
- Point out where the two reads agree, and where the user raised something the draft missed.

## Phase 4 — Build the report

**Edit a copy of the template. Do not build a new document.** A new file loses the template's fonts, headings, spacing, and numbering, even if the section names happen to match. Starting from the template file itself is the only way to keep the look.

Steps:

1. Copy `input/referee_report_template.docx` to `output/3_referee_report.docx`.
2. Read the template — `pandoc -t markdown input/referee_report_template.docx` — and note exactly what is in it: every heading, its number, its level, and any smaller headings inside a comment.
3. Edit the copy directly, using the `docx` skill's editing route (unzip → edit `word/document.xml` → zip again). Replace only the text in brackets and the instructional filler. Do not touch the styles.
4. Do not add sections the template does not have. Do not remove sections it does have. Do not renumber or reorder.
   - **No greeting and no sign-off.** Do not open with "Dear Editor" or anything like it, and do not close with a name, a salutation, or a reviewer line. The report starts at the template's first heading and ends at its last. Greetings belong in the editor letter, not here.
5. **If there are more major comments than the template has room for**, add space rather than dropping a comment or squeezing two into one. Copy the last major-comment block — its heading, number, title line, and `Suggestion` heading — and carry the numbering on (a fourth becomes "5. Major Comment 4", and everything after it moves up one). Use the same styles; do not invent a new layout. The same in reverse: with fewer comments than blocks, delete the spare blocks and renumber so there are no gaps.
6. For any other place the content does not fit the template, ask the user before changing anything.

What goes in it:

- The user's comments are the major comments, unless they say otherwise.
- Points from the sealed draft that the user wants to keep become minor comments.
- **Length limits.** A major comment is 300 words at most. A `Suggestion` is 3 sentences at most. Minor comments are shorter than both.
- **When a comment runs long.** Do not squeeze it into something vague, and do not stop drafting to ask. Instead:
  1. Write every comment out in full, true to what the user wrote. Ignore length for now.
  2. When they are all written, check each one. Note any over 300 words, or any suggestion over 3 sentences.
  3. Report all of them in one message. For each, give the title, the word count, and the separate points it is making — that is what shows whether it should be split.
  4. Ask whether to split any of them into separate major comments. Wait for the answer.
  5. On a yes, split it and add a block as in step 5 above. On a no, cut it to length and say plainly what was dropped or shortened.
  6. If nothing is over the limit, say nothing and carry on.
- **The summary keeps its Phase 2 shape.** Four parts, in the order set out in Phase 2: what the paper does; what it is built on (data and hypothesis if empirical, the model if theoretical, both if the paper does both); the key results, one short sentence each; the conclusion. Do not let it grow into an essay — about one paragraph, 150 words. The summary shows the paper was read. It is not where the argument goes.

**Check before showing it:** put the headings in the report next to the headings in the template — they should match one for one. Check there is no greeting at the top and no sign-off at the bottom. Count the words in each major comment and the sentences in each suggestion, and check they are inside the limits. Then turn it into an image and look at it.

- Output: `output/3_referee_report.docx`

## Phase 5 — Letter to the editor

A separate file. Never folded into the referee report. Everything in it comes from the final report and from the user — invent no new criticisms.

Do these in order, waiting after each:

1. **Check the report is done.** Ask "Is the referee report final?" If not, stop and go back to Phase 4.
2. **Ask the decision.** Reject / reject and resubmit / major revision / minor revision / accept. Write the whole letter to back up that decision.
3. **Group the report's comments into a few themes.** Use only comments already in the report. Do not add new themes, new angles, or concerns the report never raised. This is repeating what is there, not adding to it. Show the themes to the user.
4. **Ask the user to list their concerns as bullet points.** Do not write them for the user. They should follow the themes from step 3. Then turn those bullets into a shorter summary built around themes — group related bullets under a theme name rather than listing them one by one. The only thing that may go beyond the report is editor-only material the user gives in step 6.
5. **Say what a revision would need to show.** Short, and tied to the themes from step 4.
6. **Offer an editor-only section.** Ask "Anything confidential you want to add that the authors won't see?" Add a `Confidential — Editor Only` part only if the user gives you something — a bold label line followed by their text, placed after the revision list and before the sign-off. Never write editor-only remarks yourself.
7. **Write the file in the default letter format** (below), check how it looks, and show it to the user. Then ask: **"Any formatting changes you'd like for the letter?"** Apply what they ask, check how it looks again, and show it again. Repeat until they say it is done.
8. Do the clean-up under **Confidentiality and clean-up**, then end the session.

**Make it look like the report.** Both documents go to the same editor and should look like they came from the same person.

- Before writing, look at `output/3_referee_report.docx` and copy its formatting: body font and size, heading font, size and colour, spacing between paragraphs, line spacing, alignment, and page margins.
- The safest way is the same one Phase 4 uses — copy the referee report, clear out the text, and write the letter into the emptied file. That keeps the styles exactly instead of guessing at them.
- Keep the running page header, with its text changed to `Letter to the Editor`.
- Turn both files into images and compare them side by side before showing them.

**Default letter format.** A formal letter, not a report. No title, no section headings, no horizontal lines. Everything left-aligned, from top to bottom:

1. `[Date]`
2. Recipient block, one line each with no space between: `[Editor Name]`, `[Editor Title]`, `[Journal Name]`, `[Street Address]`, `[City, State ZIP, Country]`. Fill in a line only if the user has given it. Never guess a journal or an editor.
3. Subject line in bold: `RE: Referee Report on "<manuscript title>"`. Add the manuscript ID if the user gave one.
4. Greeting: `Dear [Editor Name],`
5. Opening: one or two sentences thanking the editor and pointing to the referee report.
6. Recommendation: one short paragraph with the decision from step 2 and the main reason.
7. Main concern: one lead-in sentence, then one bullet per theme, with the theme name in bold.
8. The line `A revision would need to show:`, then one bullet per theme, with the theme name in bold.
9. The optional `Confidential — Editor Only` part from step 6.
10. Sign-off: `Sincerely,`, a blank space to sign, then `[Referee Name]` and `[Referee Institution]` on separate lines.

Placeholders stay in square brackets for the user to fill in.

- Output: `output/4_editor_letter.docx`

## How to write — all phases

- Major comments should be about method and identification, or about contribution and framing. A point about the balance of the paper is an extra, not a major comment.
- Tone: professional, constructive but kind, short, clear, encouraging.
- Write the way a finance scholar writes: short paragraphs, short clear sentences, no difficult words. Use the plain word instead of the technical one whenever both are correct. Keep a field term only when it is doing real work.
- Reports and letters are black text throughout.
- **The referee report has no greeting and no sign-off.** It follows the template's own structure exactly — nothing added at the top or the bottom. **The editor letter does** have both, because it is a letter.
- Every file in a case looks the same — same fonts, headings, spacing, and margins, taken from `referee_report_template.docx`. Never let one file come out in default styling while the rest use the template.

## Keeping the log

If the user asked for a `project_log.md` at the start, add a dated entry each time a phase finishes or something changes — what was made, what was decided, what is still open. If they said no, skip this.

A `project_log.md` records whatever its project is about. In a referee case that is the review itself, so it holds case material and is deleted at the end.

## Making the files

Use the `docx` skill for every `.docx`. Turn the result into an image and look at it before showing it.

If `require('docx')` fails under node, set `NODE_PATH` to the global module directory instead of running `npm install`.
