---
name: stack-advisor
description: Use when the user asks what to build with, which library to pick, or how to stack an app. Triggers on "what stack should I use", "what library for", "how should I build", "pick the stack", "which tools", greenfield projects, or unfamiliar stacks.
---

# Stack Advisor

Recommending a stack from memory produces stale versions, over-installed
dependencies, and choices the user never approved. This skill chains three
disciplines so none of that happens: verify currency, decide buy-vs-build
per need, propose compactly and wait.

## Required companion skills

Load all three when this skill triggers. Each covers a step below:

- **current-sources** — version pinning and freshness verification.
- **prefer-libraries** — the library-vs-hand-roll decision and mappings.
- **propose-before-edit** — the proposal and approval protocol.

If a companion is missing, say so and continue with its step done manually —
never skip the step because the skill is absent.

## Workflow

### 1. Intake — project shape, briefly

Ask only what changes the answer, then stop asking:

- What is being built (one sentence is enough)?
- Hard constraints, if any: language, hosting, team familiarity, license
  or compliance needs, offline or edge requirements.

Do not interrogate. Three questions maximum; assume sane defaults for the
rest and state them with the proposal so they can be corrected.

### 2. Research — current-sources

For every candidate library, tool, or framework: pin the current version
and verify its docs at that version before recommending it. A stack
containing a deprecated API is worse than no recommendation. Follow
current-sources for routing (official docs for how it works, changelog
for deprecation and release status) and report the version checked.

### 3. Decide — prefer-libraries

For each need in the project, run the library-vs-hand-roll decision:
non-trivial or edge-case-heavy → library; trivial local helper →
hand-roll; crypto or security-sensitive → vetted library, no exceptions.
Check `package.json` first when a project exists. Prefer what is already
installed over adding new dependencies.

### 4. Propose — propose-before-edit protocol

Present the stack compactly and stop. No installs, no scaffolding, no
`package.json` edits until approval:

| Need | Pick | Version checked | Why this one |
|---|---|---|---|
| … | … | … | … plus one alternative considered and rejected |

Follow the token-cheap rule: file list and intent only, full detail after
approval. Approval covers the listed picks only — adding anything later
needs a new round.

## Boundaries

- Recommend, do not install. Installation is a separate approved step.
- Versions are pinned at recommendation time. If the user acts days later,
  re-verify before installing — do not trust this proposal past its date.
- One stack per proposal. If constraints conflict (e.g. edge runtime vs a
  Node-only library), surface the conflict instead of silently picking.
- Stay within the companion skills' scope: this skill decides *what*,
  they govern *how verified*, *whether to build*, and *whether to proceed*.

## Example

User: "what libs and stack to build a small booking app with payments?"

1. Intake: Next.js assumed (stated as default); no constraints given.
2. Research: verify current versions of shortlisted candidates at their
   official docs and changelogs.
3. Decide: auth → library (edge cases); date handling → library (DST);
   email receipts → library; cron reminders → library (missed-run handling).
4. Propose the table — five rows, versions, one-line whys — and stop.
   User says "go": only then scaffold or install.

## Limits — read this honestly

A recommendation rots: versions move, advisories land, better options
appear. This skill reduces stale-stack risk at decision time; it cannot
keep the decision fresh. Re-run verification at install time, and treat any
proposal older than a few weeks as suspect until rechecked.
