---
name: explain
description: Summarize or explain what was just done, or what was just said, briefly and at a high level, in terms a non-technical Product Manager understands. Use when the user asks for a brief, high-level, PM-friendly, non-technical, plain-language, or "explain it to a PM" summary or explanation of recent work, a change, a bug, a PR, or a previous answer.
argument-hint: "[what to cover, e.g. 'what you did' or 'the caching fix']"
---

Summarize or explain the subject below briefly, at a high level, in terms
appropriate for a non-technical Product Manager.

Subject: $ARGUMENTS

If the subject is empty, pick it from the conversation: the work you just
did if you did work, otherwise what you just said. If you did work, summarize
it; if you gave an answer, explain it.

The reader:

- Cares about what changed for users, customers, or the team, why it matters,
  and any risk, cost, or decision it needs from them.
- Does not care how it works. Leave out file names, function names, code, and
  tooling unless a name is the only way to point at something.
- Knows the product, not the system behind it. Replace jargon with plain words;
  if a technical term cannot be avoided, say what it means in a few words.

Keep it short: a few sentences or a handful of bullets. Lead with the outcome.
State what is done, what is not, and anything that needs their input. Do not
overstate certainty or progress.
