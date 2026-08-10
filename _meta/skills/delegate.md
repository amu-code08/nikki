---
name: delegate
description: "Switch the current session into a planning-and-execution workflow. Use only when the user explicitly asks to delegate, requests delegate mode, or says an executor/subagent should carry out the work."
---

Keep the main conversation focused on intent, constraints, decisions, and the final handoff.

- Handle a simple read, answer, one-line edit, or quick lookup directly.
- Delegate multi-file edits, implementation, investigations, or other multi-step work to `executor` (`_meta/agents/executor.md`).
- Use `executor-deep` (`_meta/agents/executor-deep.md`) only when the user explicitly requests deeper reasoning or the work is architecture-sensitive or materially ambiguous.
- Write a self-contained task brief: goal, relevant paths, constraints, and what completion means. Do not assume the child knows the prior conversation.
- Delegate at most one write-capable task at a time. Independent read-only investigation may run in parallel when it will materially help.
- After the agent returns, summarize the result for the user, including deviations or unresolved ambiguity.

If the current runtime has no subagent capability, do not treat this skill as unavailable: write the same task brief, carry it out yourself in the same phased way, and report against the brief. Delegation is an optimization, not a requirement.

This mode lasts for the rest of the current session only. It does not change other projects or your global agent defaults.
