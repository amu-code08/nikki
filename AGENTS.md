# Obsidian vault guidance

## Scope

This is a personal, local Obsidian vault, not a conventional software project. Treat Markdown notes as user-authored source material: make the smallest requested change and preserve their language, structure, links, YAML frontmatter, and line endings.

## File routing

- `daily/` contains chronological capture notes, named `YYYY-MM-DD.md`. Do not bulk-edit or rewrite them unless the user explicitly requests it. Nothing but dated daily notes belongs here.
  - `daily/untagged/` holds dailies not yet processed by `$tag-dailies`; this is where Obsidian creates new daily notes.
  - `daily/tagged/` holds reviewed dailies. The directory a note sits in is the source of truth for whether it has been processed.
- `inbox/` is where Obsidian puts new non-daily notes. Treat it as unfiled: the user decides whether each note becomes a `permanent/` or `references/` note. Do not let notes accumulate here silently — surface them when relevant.
- `permanent/` contains durable notes written or edited by the user. Propose a new permanent note before creating one unless the user asks to create it.
- `_meta/taxonomy.md` is the source of truth for permitted tags: only checked (`- [x]`) tags are allowed.
- `_templates/daily.md` is the template for new daily notes.
- `references/` contains saved reference material; exclude it from note analysis unless the user includes it.
- `.obsidian/` is application state. Do not edit it unless the user specifically asks for an Obsidian configuration change.
- `system-idea/` contains specifications and proposals. Keep historical notes intact; update an active specification only when the requested change affects it.

## Working rules

- Prefer read-only answers for retrieval, reflection, and status requests. Do not create derived summary notes unless asked.
- Preserve existing `tags:` values and only add `tagged:` metadata through `$tag-dailies` or a direct request.
- Do not add inline `#hashtags`; use YAML `tags:` only.
- Do not change checkbox state, delete notes, rename tags, or change taxonomy approval state without explicit user confirmation.
- Use UTF-8 when reading or writing Markdown.

## Vault skills

- `$open-todos`: list incomplete checkbox items across daily notes without writing files.
- `$resurface-ideas`: list explicit `IDEA:` and `Q:` entries without inferring new ones.
- `$extract`: answer a question from relevant daily and permanent notes.
- `$tag-dailies`: apply approved taxonomy tags and a `tagged:` marker to reviewed notes.
- `$delegate`: switch the current session into the inherited planning/execution workflow only when the user explicitly requests delegation.

## Delegation

When `$delegate` is active, use the project `executor` agent for ordinary multi-step work and `executor-deep` only for architecture-sensitive or unusually ambiguous work. Delegate at most one write-capable task at a time.

## Validation

For note edits, re-read the changed files and confirm YAML/frontmatter remains valid. There is no build or test command for this vault.
