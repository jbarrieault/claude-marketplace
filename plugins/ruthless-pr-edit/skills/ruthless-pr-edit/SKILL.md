---
name: ruthless-pr-edit
description: Write or edit a pull request title and body by ruthless subtraction — cut until only the essential remains, then add back only what it would be wrong without. Use when opening a PR, drafting or rewriting a PR description, or tightening an existing PR whose body is bloated.
---

# Ruthless PR Edit

Rick Rubin calls himself a reducer, not a producer. "A lot of the records I produce
are about stripping things away." Subtraction is the method, not the cleanup: you
cannot know what a song needs until you hear it without the parts you assumed were
necessary.

Apply that to pull requests. **Removal is the work. Addition is the exception, and it
comes last.**

## The premise

A pull request should say **what** is changing. The commits should say **how**. The diff says
**where**. The PR title and body should never do the commits' job or the diff's job.

Discriminator: if a sentence names a class, file, method, flag, or variable, it is
almost always *how* — cut it. The exception is when the identifier *is* the change: a
renamed public API, a new endpoint, a removed config key.

- *How* (cut): "Wraps `OrgLookup#find` in a `RedisCache` with a 5-minute TTL keyed on
  org id and `updated_at`."
- *What* (keep): "Org lookups now hit Redis instead of Postgres on every request."

The reader is a colleague who knows the codebase but not this branch. They should
finish the body knowing what changed and whether they are the right reviewer, in under
thirty seconds. Length is not thoroughness. "There's a tremendous power in using the
least amount of information to get a point across."

## Before you cut

Read `examples.md` in this skill directory. It carries the taste this skill is for.

Then gather only what you need:

- Existing PR: `gh pr view <n> --json title,body,url,headRefName,baseRefName`
- Base branch: `gh repo view --json defaultBranchRef -q .defaultBranchRef.name`
- The change: `git log <base>..HEAD --oneline` then `git diff --stat <base>...HEAD`
- Conventions: `.github/pull_request_template.md`, `CLAUDE.md`, and one or two recently
  merged PRs (`gh pr list --state merged --limit 3 --json title,body`)

Understand the change well enough to describe it in one sentence before you write
anything. If you cannot, read the diff, not more prose.

Write the honest, overlong draft first if it helps you find the shape. It is scaffolding.
Expect to delete most of it.

## The passes

Run them in order. Do not skip to pass 5.

**1 — Delete whole sections.** Anything restating the diff: file-by-file walkthroughs,
"Changes" lists naming functions, code snippets already in the diff, "Testing" sections
that say tests pass, unfilled checklists, "Notes for reviewers" that say "let me know
what you think."

**2 — Delete sentences that exist to sound thorough.** Rationale already in the linked
ticket. Background the reader already has. Explanations of how the code works.
Alternatives nobody asked about. Future work not in this PR. Self-assessment of the
work's quality.

**3 — Delete fluff words.** "simply", "just", "basically", "essentially", "currently",
"leverage", "robust", "comprehensive", "seamlessly", "under the hood". Hedges. Adverbs.

**4 — Challenge the identity.** Rubin: "Reduce something to the point that its identity
is challenged. Notice how many pieces you can remove before the work you're making
ceases to be the work you're making."

Cut one more real thing — the piece you are sure has to stay. Reread as the uninformed
reader. If they still get it, the cut stays. Repeat until a cut genuinely breaks
understanding. Restore that one cut. Stop there.

**5 — Add back, reluctantly.** One test: *would a reviewer make a wrong call without
it?* Things that pass:

- A risk not visible in the diff
- Deploy, migration, or merge ordering
- A decision that could have gone another way and is expensive to reverse
- Blast radius larger than the diff suggests
- Something deliberately left undone that looks like an oversight

Nothing else. Each addition is one sentence, in the body, not a new section.

## The title

The title is the whole PR for most people who see it. It carries what changed, in the
reader's terms, and stands alone in a merge log a year from now.

- Lead with the change, not the area: "Cache org lookups in Redis", not "Redis changes"
- No `[WIP]`, no ticket-key prefix, no trailing period
- No `refactor:`/`chore:` prefix unless the repo uses conventional commits
- Under ~70 characters, and shorter is better than clever

## Shape

Two sections, and the second is optional.

```markdown
## Summary

<one sentence: what is changing>

## Details

<only if the change demands it: 1–2 more sentences>
```

**Summary** is one sentence. Not two joined by a semicolon or an em-dash. If you cannot say it in one,
you do not yet understand the change well enough to describe it — reread the diff.

**Details** does not exist by default. It appears only when pass 5 found something a
reviewer would be wrong without, and then it is one or two sentences held to the same
standard as the Summary. An empty or superfluous Details section is worse than no
Details section. Delete it.

Never a section per file. Never a `Changes` bullet list — that is the commit log,
rendered worse.

**IMPORTANT:** A repo's own `pull_request_template.md` overrides this shape. Keep its structure, starve
it: one sentence per heading, never invent content to fill one, never delete a required
one. If a section genuinely has nothing to say, "None."

## Rules that outrank brevity

1. **Accuracy.** Never cut a true, critical fact to hit a word count. Never let
   compression imply something false about scope or risk.
2. **You are the reducer, not the author.** Editing someone else's PR, keep their
   meaning and their voice. Cut their words; do not replace them with yours.
3. **Repo templates win on structure; this skill wins over general PR guidance.** A
   checked-in `pull_request_template.md` sets the headings. Absent one, Summary/Details
   above is the shape, even where broader instructions describe a fuller template.
   Mechanics outside the body — labels, reviewers, ticket links — always follow
   project and user convention. Draft status follows **Apply it** below.

## Apply it

The passes are not done until the title and body are on the PR.

If the PR exists, edit it in place and leave its draft status alone — a ready PR stays
ready, a draft stays a draft. If it does not exist, create it as a draft, unless the
user explicitly asked for a non-draft one. Silence means draft.

## Report

After applying the final title and body, notify the user. If you cut something a reasonable person would have
kept, say what and why, in one line. Do not narrate the passes.
