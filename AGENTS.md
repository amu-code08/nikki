# Obsidian vault guidance

## Scope

This is a personal, local Obsidian vault, not a conventional software project. Treat Markdown notes as user-authored source material: make the smallest requested change and preserve their language, structure, links, YAML frontmatter, and line endings.

This file is the only required entry point. It routes to everything else by path, and it is authoritative: if any other file in the vault disagrees with it, this file wins and the other file gets fixed. Output is Japanese (technical terms may stay English).

The system is a trusted external memory: a place to write things down so they are not forgotten. Capture is free-form in `daily/`; everything else — tagging, retrieval, monthly review — runs only when the user asks for it.

## Invariants

| ID | Rule | Why it matters |
|---|---|---|
| INV-01 | The user writes only in `daily/`. Mixing several topics in one file is normal | The capture habit is the whole system; friction here stops the record |
| INV-02 | Do not accumulate derived artifacts. Return extractions in chat. The sole exception is `slices/`, which `$tag-dailies` writes by copying the user's own lines verbatim — never a summary, never generated prose | The vault fills with AI output and stops being trustworthy |
| INV-03 | `permanent/` holds only notes the user wrote or edited | Human thinking and AI output become indistinguishable |
| INV-04 | Promoting to `permanent/` and changing the taxonomy are always the user's decisions | The approval model collapses |
| INV-05 | In `daily/`, an agent writes only the frontmatter `tags` / `tagged` fields — bodies, checkbox state, and task text are off limits, always. In `permanent/`, an agent writes only when explicitly asked, and never rewrites goal text or checkbox state in the two goal notes | The record stops being trustworthy — the worst failure available |
| INV-06 | Pull only. No scheduled tasks, no push-style automation | This is the design that was abandoned in v1–v4; do not reintroduce it |
| INV-07 | New needs are met by adding one skill, not by adding automation | Protects INV-06 |

## File routing

- `daily/` holds chronological capture notes named `YYYY-MM-DD.md`, flat, with no subdirectories. **Every daily stays here permanently as the source of truth**; nothing is ever moved out except to `_trash/`. Nothing but dated daily notes belongs here. Do not bulk-edit or reformat them unless asked.
  - **The `tagged:` frontmatter field is the record of whether `$tag-dailies` has processed a note** — no field means unprocessed. It is written last, only once tagging and slicing have both succeeded. Obsidian creates new dailies directly in `daily/`.
- `slices/` holds per-topic cuts of multi-topic dailies, written only by `$tag-dailies`, named `YYYY-MM-DD--<domain>.md`. Bodies are **verbatim copies** of the user's own lines — a slice is never a summary. The daily remains the source of truth. Exclude `slices/` from note analysis by default, or the same text gets counted twice.
- `inbox/` is where Obsidian puts new non-daily notes. Treat it as unfiled: the user decides whether each becomes a `permanent/` or `references/` note. Do not let notes accumulate silently — surface them when relevant.
- `permanent/` holds durable notes written or edited by the user. Propose before creating one unless asked to create it.
  - The note tagged `type/north-star` (currently `permanent/motivation/North Star.md`) states long-term direction in prose — what the user is aiming at over years. No checkboxes; nothing here is ever "completed". Read it when the question is about direction, values, or whether current work is pointed the right way.
  - The note tagged `type/monthly-goals` (currently `permanent/motivation/Monthly Goals.md`) holds the checkable objectives, one `## YYYY/MM` section per month. `- [ ]` items are judgeable; a plain `- ` bullet is a direction, not a goal to complete. Read it when the question touches this month's goals or progress.
  - **The user writes and maintains both; never edit their goal text or checkbox state** (INV-05). The user reorganizes `permanent/` freely, so locate these two by tag and treat the paths above as a hint, not a guarantee.
- `references/` holds AI-generated material worth keeping. Exclude it from note analysis unless the user includes it.
- `_meta/` is configuration, not user notes. Exclude it from note analysis.
  - `_meta/taxonomy.md` — source of truth for tags. Only checked (`- [x]`) tags may be applied. Topic tags and axis tags are listed separately.
  - `_meta/skills/<name>.md` — source of truth for skill behavior.
- `.agents/` and `.claude/` contain per-tool discovery stubs that point at `_meta/skills/`. **Change behavior in `_meta/`, never in a stub.** A stub may duplicate only the `name` / `description` frontmatter.
- `CLAUDE.md` is a one-line pointer to this file. Never copy content into it.
- `_trash/` is where `$tag-dailies` moves dailies with no prose in them — either empty, or nothing but a todo list. It is a reversible holding area, not a delete. Exclude it from all analysis, and never remove anything from it — emptying it is the user's job.
- `_templates/daily.md` is the template for new daily notes. Do not put placeholder prose in it.
- `.obsidian/` is application state. Do not edit it unless asked for an Obsidian configuration change.
- `system-idea/` holds proposals and backlog. Keep historical notes intact.

## Write permissions

`○` allowed by default, `△` conditional, `×` not allowed.

| Path | Read | Write | Condition |
|---|---|---|---|
| `daily/**` note body | ○ | **×** | INV-05. Even on explicit request, confirm first |
| `daily/**` frontmatter `tags` / `tagged` | ○ | △ | Via `$tag-dailies`, or on direct request |
| `daily/**` checkbox state | ○ | **×** | The user marks things done |
| `daily/**` deletion | ○ | **×** | Agents never delete a daily. Prose-less ones are moved to `_trash/` |
| `daily/**` → `_trash/` move | ○ | △ | Only notes `$tag-dailies` read and confirmed to hold no prose — empty, or checkbox lines only. No approval needed — the move is reversible — but always report what moved, and quote the task lines when the note was a todo-only one |
| `slices/**` creation | △ | △ | Only via `$tag-dailies`, and only as verbatim copies of daily lines. Never overwrite an existing slice |
| `slices/**` editing and deletion | △ | **×** | Reorganizing, rewriting, and deleting slices is the user's job |
| `_trash/**` | △ | **×** | Outside all analysis. Only the user removes anything from here |
| `permanent/**` | ○ | △ | Creating requires a proposal and approval first; editing on request. Never on your own initiative |
| The `type/north-star` and `type/monthly-goals` notes | ○ | △ | Frontmatter and section structure on explicit request only. **Goal text and checkbox state are never yours to change**, even when asked — report the evidence and let the user write it |
| `inbox/**` | ○ | △ | Filing is the user's decision. Report accumulation |
| `references/**` | △ | △ | Outside analysis by default; read only when the user includes it |
| `_meta/taxonomy.md` candidate lines `- [ ]` | ○ | △ | May append candidates |
| `_meta/taxonomy.md` approved lines `- [x]` | ○ | △ | Only with in-session user approval. Never delete, rename, or check a line on your own |
| `_meta/skills/**` | ○ | △ | On request. This is the canonical layer |
| Discovery stubs | ○ | △ | `description` sync only. Never add procedure text |
| `_templates/**` | ○ | △ | On request. No placeholder prose |
| `.obsidian/**` | ○ | **×** | Only on an explicit Obsidian configuration request |
| `AGENTS.md`, `CLAUDE.md`, `system-idea/**` | ○ | △ | On request |

## Working rules

- Prefer read-only answers for retrieval, reflection, and status requests. Do not create derived summary notes unless asked.
- Preserve existing `tags:` values and only add `tagged:` metadata through `$tag-dailies` or a direct request.
- Do not add inline `#hashtags`; use YAML `tags:` only.
- Do not change checkbox state, delete notes, rename tags, or change taxonomy approval state without explicit user confirmation.
- When a request is ambiguous, ask **one** question, then proceed on sensible defaults and report what you assumed.
- Use UTF-8 when reading or writing Markdown.

## Vault skills

When the user invokes `$<name>`, read `_meta/skills/<name>.md` and follow it. Do this whether or not your runtime auto-discovered a skill of that name: the file is the behavior. `$` is used rather than `/` so the names do not collide with per-tool slash commands.

| Skill | Role | Writes |
|---|---|---|
| `$resurface-ideas` | Collect line-initial `IDEA:` / `Q:` markers | none |
| `$monthly-review` | Compare a month's dailies against `Monthly Goals.md` and report in chat | none |
| `$tag-dailies` | Apply approved tags and `tagged:`, cut multi-topic dailies into `slices/`, move prose-less dailies to `_trash/` | yes |
| `$commit` | Review the working tree, refresh this file if the diff made it stale, then commit | this file only |

Routing from what the user says:

| The user says | Do |
|---|---|
| "todo", "what's left" | Collect open `- [ ]` items across dailies and answer in chat. The checkboxes in `Monthly Goals.md` are goals, not todos — leave them to `$monthly-review` |
| "ideas", "open questions" | `$resurface-ideas` |
| "what did I write about X", "I want to look back" | Read the relevant dailies and answer in chat |
| "tag them", "process the untagged dailies" | `$tag-dailies` |
| "how did last month go", "monthly review" | `$monthly-review` |
| "commit", "save to git", the weekly commit | `$commit` |
| "make this a note" | Propose → get approval → write to `permanent/` |
| "summarize this" with no destination given | Answer in chat; offer to file it, do not write |
| Retrieval, search, status | Answer read-only |

`$tag-dailies` treats the `tagged:` field as the record of what has been processed, and never moves a daily out of `daily/`.

`$monthly-review` reads the whole month from `daily/`, processed or not, which is why it is a separate skill rather than a step inside `$tag-dailies` — that one only ever sees notes with no `tagged:` field.

## Answering from notes

Retrieval, reflection, and "what did I write about X" are ordinary conversation, not skills. They still follow these rules.

- **Completeness.** When a tag filter narrows the search, always also read dailies in range that have no `tagged:` field. A tagging backlog must never be able to hide content from an answer.
- Scope is `daily/` and `permanent/`. Include `references/` and `slices/` only when the user says so. `slices/` repeats text that is already in `daily/`, so reading both double-counts it.
- Checkbox lines are tasks, not thinking. For questions about memory, ideas, or patterns, answer from prose and ignore them. For questions about tasks, collect the open `- [ ]` items.
- A checkbox line with no text after it is template residue, not a task. Never list it.
- Cite the source day as `[[daily/YYYY-MM-DD]]`.
- Return observation, not encouragement. When describing the user's own patterns, say what recurs in the notes rather than delivering a verdict about them.
- Answer in chat. Do not write the answer into the vault; propose filing it if it seems worth keeping.
- When a daily indicates an open item in `Monthly Goals.md` was met, say so and cite the day. Never tick the box yourself — report the evidence and let the user do it. North Star holds no checkboxes and is never evaluated for completion.

## Sensitive content

The vault is pushed to a remote and read on a phone, so what used to stay on one machine now leaves it. Whenever you are already reading daily bodies — tagging, slicing, reviewing, answering, or preparing a commit — watch for the following and **say so in chat**.

- **Possible MNPI (insider information).** Unpublished figures, guidance, or results; an M&A, financing, or rating action that reads as not yet announced; an in-house rating or price-target change and the reasoning behind it; words like 未公表 / 開示前 / NDA / embargo standing next to a named issuer.
- **Employer confidential material.** Contents of internal systems and reports, a client or deal name paired with terms, non-public internal policy, a named colleague attached to a personnel judgment.
- **Third parties.** A private individual's real name attached to their health, relationships, or an assessment of them.

How to raise it:

- **Flag it; never act on it.** Do not edit, redact, mask, move, or delete the note (INV-05), and do not refuse to commit. The call is the user's.
- Cite the file and line and say specifically why it fits the category. A general caution is worthless.
- Scope the check to what you are already reading — the notes being processed, or the diff being committed. Do not sweep the vault for this, and do not re-raise something the user has already seen and chosen to keep.
- The trigger is non-public specifics, not the fact that this vault is about finance. Do not flag every company name. Within that limit, asking and being wrong is cheap; staying quiet is not.

## Delegation

The vault defines no agent roles and no delegation mode. If your runtime has subagents and the work is genuinely multi-step, you may use them: write a self-contained brief — goal, relevant paths, constraints, and what counts as done — rather than assuming the child saw this conversation, and keep at most one write-capable task in flight. Read-only investigation may run in parallel.

If your runtime has no subagents, do the work yourself in the same phased way. Nothing here depends on delegation.

## Failure modes

Mistakes that have happened here, or that the structure invites.

| ID | Wrong | Right |
|---|---|---|
| FAIL-01 | Creating a non-dated note in `daily/` | Put it in `inbox/`. This actually happened and forced the v5.3 restructure |
| FAIL-02 | Writing an extraction or summary into the vault as a note | Answer in chat; only propose filing it (INV-02). `slices/` is the one exception, and only as verbatim copy |
| FAIL-03 | Writing inline `#hashtags` in a body | YAML `tags:` only |
| FAIL-04 | Using an unchecked `- [ ]` candidate tag as if approved | Do not use it until the user approves |
| FAIL-05 | Adding procedure text to a discovery stub | Edit the canonical file in `_meta/` |
| FAIL-06 | Editing a daily's body, checkbox state, or task text | Only `tags` / `tagged` frontmatter (INV-05) |
| FAIL-07 | Treating checkbox lines as thinking when answering a question about ideas or patterns | Answer from prose; handle tasks separately |
| FAIL-08 | Excluding untagged dailies because of a tag filter | Always scan untagged dailies in range |
| FAIL-09 | Creating a `permanent/` note unprompted | Propose → approve → create |
| FAIL-10 | Putting unedited AI output in `permanent/` | It goes to `references/` (INV-03) |
| FAIL-11 | Writing `tagged:` before the tags and slices are actually in place | It is the only record that a note was processed, so a premature one means the note is never picked up again. Write it last |
| FAIL-12 | Rewriting block-format frontmatter back to inline | Leave it. Both formats exist in the vault |
| FAIL-13 | Proposing scheduled tasks, automation, or a morning brief | Add one pull-style skill instead (INV-06) |
| FAIL-14 | Inferring ideas from unmarked lines in `$resurface-ideas` | Only `IDEA:` / `Q:` line-initial markers. For notes predating the convention, read them and answer directly instead |
| FAIL-15 | Including `references/` in analysis by default | Only when the user includes it |
| FAIL-16 | Deleting notes, changing checkbox state, or renaming tags without confirmation | Always confirm explicitly |
| FAIL-17 | Putting placeholder prose in the template | An LLM reads it as content and pollutes extraction |
| FAIL-18 | Copying this file's content into `CLAUDE.md` or another entry point | Keep them one-line pointers |
| FAIL-19 | Symlinking stubs to canonical files | Use real files. Symlinks break on Windows, Obsidian, and git |
| FAIL-20 | Answering in English | Japanese; technical terms may stay English (see Scope) |
| FAIL-21 | Bulk-editing or reformatting `daily/` | Do not touch it without an explicit request |
| FAIL-22 | Asking clarifying question after clarifying question | One question, then proceed and report assumptions |
| FAIL-23 | `git push`, `--amend`, `reset --hard`, `--no-verify` | Never. Remote and history rewriting are the user's |
| FAIL-24 | `git add -A` sweeping in the user's unrelated Obsidian edits | List the mixture and let the user decide the grouping |
| FAIL-25 | Updating this file on every commit, or inventing changes not in the diff | Only when the judgment table says so, and only facts visible in the diff |
| FAIL-26 | Counting template residue (`## todo`, empty `- [ ]`) as body content when judging emptiness | Notes with real content also end with that residue. It is not content |
| FAIL-27 | Judging emptiness by line count instead of reading the note | Read it. Size and line count say nothing — a note of pure template residue can be 9 lines |
| FAIL-28 | Deleting an empty daily instead of moving it, or removing anything from `_trash/` | Move to `_trash/` and report. Emptying the trash is the user's job alone |
| FAIL-29 | Ticking an item in `Monthly Goals.md`, or rewriting either goal document | Report the evidence and cite the day; the user ticks it. A false ✅ costs more than a missed one, because it makes the whole document untrustworthy |
| FAIL-30 | Treating a North Star statement as a goal to complete | It is direction, not a task. Only `Monthly Goals.md` has judgeable items |
| FAIL-31 | Summarizing, rewording, or tidying the user's lines when writing a slice | Copy verbatim. A slice made of AI prose makes `slices/` untrustworthy the same way a false ✅ does |
| FAIL-32 | Counting `slices/` in tag distributions, todo collection, or `$resurface-ideas` | The daily is the source of truth. Reading both counts the same text twice |
| FAIL-33 | Copying checkbox lines into a slice | The state drifts the moment the user ticks the box in the daily. Slices carry prose only |
| FAIL-34 | Moving a todo-only daily to `_trash/` without quoting its task lines | `_trash/` is outside all analysis, so any open `- [ ]` in it silently disappears from todo collection and `$monthly-review`. The report is the only thing that keeps it findable |
| FAIL-35 | Redacting a note, or refusing to commit, over suspected sensitive content | Report it with the file and line and let the user decide. Quietly altering the record is the worse failure (INV-05) |
| FAIL-36 | Staying silent about likely MNPI because flagging it felt presumptuous | Once the repo has a remote, a push cannot be taken back. Say it and be wrong |

## Portability

This vault must stay operable by any agent that can read, search, and write files. Auto-discovery, subagents, and reasoning-effort settings are per-tool conveniences layered on top; never make a vault function depend on them. To onboard a new tool: add its entry-point file as a one-line pointer to this file, and optionally add discovery stubs that point at `_meta/`. Nothing more.

## How the user works

Write freely in a daily, several topics mixed, optionally marking lines with `IDEA:` or `Q:`. Non-daily notes land in `inbox/` and get filed to `permanent/` or `references/`. Tagging happens when the user remembers to run `$tag-dailies`. Extraction happens on demand and returns in chat. Promotion and taxonomy changes are always the user's call. Commits are roughly weekly via `$commit`; pushing is always done by the user.

## Maintenance

- The vault is under git. Committing is roughly weekly via `$commit`. Obsidian's File Recovery core plugin is the second line of defense.
- Days with no prose written are not kept in `daily/` — neither the empty ones nor the ones holding only a todo list. `$tag-dailies` finds them and moves them to `_trash/`, which the user reviews and empties.
- If a tag filter selects more than roughly 30 notes, require a period alongside it. If one tag keeps growing, compress its core into `permanent/` so dailies are read less often.
- Text-only, so size is not a real constraint. Keep images and PDFs outside the vault.

## Validation

For note edits, re-read the changed files and confirm YAML/frontmatter remains valid. There is no build or test command for this vault.
