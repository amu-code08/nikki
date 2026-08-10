# Obsidian vault guidance

## Scope

This is a personal, local Obsidian vault, not a conventional software project. Treat Markdown notes as user-authored source material: make the smallest requested change and preserve their language, structure, links, YAML frontmatter, and line endings.

This file is the only required entry point. It routes to everything else by path, and it is authoritative: if any other file in the vault disagrees with it, this file wins and the other file gets fixed. Output is Japanese (technical terms may stay English).

The system is a trusted external memory: a place to write things down so they are not forgotten. It has three functions — selective tagging, on-demand extraction, and a controlled vocabulary.

## Invariants

| ID | Rule | Why it matters |
|---|---|---|
| INV-01 | The user writes only in `daily/`. Mixing several topics in one file is normal | The capture habit is the whole system; friction here stops the record |
| INV-02 | Do not accumulate derived artifacts. Return extractions in chat | The vault fills with AI output and stops being trustworthy |
| INV-03 | `permanent/` holds only notes the user wrote or edited | Human thinking and AI output become indistinguishable |
| INV-04 | Promoting to `permanent/` and changing the taxonomy are always the user's decisions | The approval model collapses |
| INV-05 | An agent may write to `daily/` and `permanent/` only in the frontmatter `tags` / `tagged` fields. Note bodies are off limits | The record stops being trustworthy — the worst failure available |
| INV-06 | Pull only. No scheduled tasks, no push-style automation | This is the design that was abandoned in v1–v4; do not reintroduce it |
| INV-07 | New needs are met by adding one skill, not by adding automation | Protects INV-06 |

## File routing

- `daily/` holds chronological capture notes named `YYYY-MM-DD.md`. Nothing but dated daily notes belongs here. Do not bulk-edit or reformat them unless asked.
  - `daily/untagged/` — not yet processed by `$tag-dailies`. Obsidian creates new dailies here.
  - `daily/tagged/` — reviewed. **The directory a note sits in is the source of truth for whether it was processed**, not the `tagged:` field.
- `inbox/` is where Obsidian puts new non-daily notes. Treat it as unfiled: the user decides whether each becomes a `permanent/` or `references/` note. Do not let notes accumulate silently — surface them when relevant.
- `permanent/` holds durable notes written or edited by the user. Propose before creating one unless asked to create it.
- `references/` holds AI-generated material worth keeping. Exclude it from note analysis unless the user includes it.
- `_meta/` is configuration, not user notes. Exclude it from note analysis.
  - `_meta/taxonomy.md` — source of truth for tags. Only checked (`- [x]`) tags may be applied. Topic tags and axis tags are listed separately.
  - `_meta/skills/<name>.md` — source of truth for skill behavior.
- `.agents/` and `.claude/` contain per-tool discovery stubs that point at `_meta/skills/`. **Change behavior in `_meta/`, never in a stub.** A stub may duplicate only the `name` / `description` frontmatter.
- `CLAUDE.md` is a one-line pointer to this file. Never copy content into it.
- `_trash/` is where `$tag-dailies` moves dailies whose body is empty. It is a reversible holding area, not a delete. Exclude it from all analysis, and never remove anything from it — emptying it is the user's job.
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
| `daily/**` deletion | ○ | **×** | Agents never delete a daily. Empty ones are moved to `_trash/` |
| `daily/**` → `_trash/` move | ○ | △ | Only notes `$tag-dailies` read and confirmed to have an empty body. No approval needed — the move is reversible — but always report what moved |
| `_trash/**` | △ | **×** | Outside all analysis. Only the user removes anything from here |
| `permanent/**` | ○ | △ | Creating requires a proposal and approval first; editing on request |
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
| `$tag-dailies` | Apply approved tags and `tagged:`, move to `daily/tagged/`, move empty dailies to `_trash/` | yes |
| `$commit` | Review the working tree, refresh this file if the diff made it stale, then commit | this file only |

Routing from what the user says:

| The user says | Do |
|---|---|
| "todo", "what's left" | Collect open `- [ ]` items across dailies and answer in chat |
| "ideas", "open questions" | `$resurface-ideas` |
| "what did I write about X", "I want to look back" | Read the relevant dailies and answer in chat |
| "tag them", "process the untagged dailies" | `$tag-dailies` |
| "commit", "save to git", the weekly commit | `$commit` |
| "make this a note" | Propose → get approval → write to `permanent/` |
| "summarize this" with no destination given | Answer in chat; offer to file it, do not write |
| Retrieval, search, status | Answer read-only |

`$tag-dailies` treats the containing directory, never the `tagged:` field, as the record of what has been processed.

## Answering from notes

Retrieval, reflection, and "what did I write about X" are ordinary conversation, not skills. They still follow these rules.

- **Completeness.** When a tag filter narrows the search, always also read dailies in range that have no `tagged:` field. A tagging backlog must never be able to hide content from an answer.
- Scope is `daily/` and `permanent/`. Include `references/` only when the user says so.
- Checkbox lines are tasks, not thinking. For questions about memory, ideas, or patterns, answer from prose and ignore them. For questions about tasks, collect the open `- [ ]` items.
- A checkbox line with no text after it is template residue, not a task. Never list it.
- Cite the source day as `[[daily/YYYY-MM-DD]]`.
- Return observation, not encouragement. When describing the user's own patterns, say what recurs in the notes rather than delivering a verdict about them.
- Answer in chat. Do not write the answer into the vault; propose filing it if it seems worth keeping.

## Delegation

The vault defines no agent roles and no delegation mode. If your runtime has subagents and the work is genuinely multi-step, you may use them: write a self-contained brief — goal, relevant paths, constraints, and what counts as done — rather than assuming the child saw this conversation, and keep at most one write-capable task in flight. Read-only investigation may run in parallel.

If your runtime has no subagents, do the work yourself in the same phased way. Nothing here depends on delegation.

## Failure modes

Mistakes that have happened here, or that the structure invites.

| ID | Wrong | Right |
|---|---|---|
| FAIL-01 | Creating a non-dated note in `daily/` | Put it in `inbox/`. This actually happened and forced the v5.3 restructure |
| FAIL-02 | Writing an extraction or summary into the vault as a note | Answer in chat; only propose filing it (INV-02) |
| FAIL-03 | Writing inline `#hashtags` in a body | YAML `tags:` only |
| FAIL-04 | Using an unchecked `- [ ]` candidate tag as if approved | Do not use it until the user approves |
| FAIL-05 | Adding procedure text to a discovery stub | Edit the canonical file in `_meta/` |
| FAIL-06 | Editing a daily's body, checkbox state, or task text | Only `tags` / `tagged` frontmatter (INV-05) |
| FAIL-07 | Treating checkbox lines as thinking when answering a question about ideas or patterns | Answer from prose; handle tasks separately |
| FAIL-08 | Excluding untagged dailies because of a tag filter | Always scan untagged dailies in range |
| FAIL-09 | Creating a `permanent/` note unprompted | Propose → approve → create |
| FAIL-10 | Putting unedited AI output in `permanent/` | It goes to `references/` (INV-03) |
| FAIL-11 | Moving a note to `daily/tagged/` before validating its frontmatter | Validate first; never overwrite a same-named file at the destination |
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

## Portability

This vault must stay operable by any agent that can read, search, and write files. Auto-discovery, subagents, and reasoning-effort settings are per-tool conveniences layered on top; never make a vault function depend on them. To onboard a new tool: add its entry-point file as a one-line pointer to this file, and optionally add discovery stubs that point at `_meta/`. Nothing more.

## How the user works

Write freely in a daily, several topics mixed, optionally marking lines with `IDEA:` or `Q:`. Non-daily notes land in `inbox/` and get filed to `permanent/` or `references/`. Tagging happens when the user remembers to run `$tag-dailies`. Extraction happens on demand and returns in chat. Promotion and taxonomy changes are always the user's call. Commits are roughly weekly via `$commit`; pushing is always done by the user.

## Maintenance

- The vault is under git. Committing is roughly weekly via `$commit`. Obsidian's File Recovery core plugin is the second line of defense.
- Days with nothing written are not kept in `daily/`. `$tag-dailies` finds them and moves them to `_trash/`, which the user reviews and empties.
- If a tag filter selects more than roughly 30 notes, require a period alongside it. If one tag keeps growing, compress its core into `permanent/` so dailies are read less often.
- Text-only, so size is not a real constraint. Keep images and PDFs outside the vault.

## Validation

For note edits, re-read the changed files and confirm YAML/frontmatter remains valid. There is no build or test command for this vault.
