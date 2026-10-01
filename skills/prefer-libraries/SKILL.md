---
name: prefer-libraries
description: Use when writing helpers, parsers, validators, date or format logic, or algorithms — wherever a maintained library might already solve the problem. Triggers on writing utility functions, date/time handling, validation schemas, parsing, ID generation, logging setup, async helpers, HTTP wrappers, cryptographic or security-sensitive code, or any "should I write this myself" decision.
---

# Prefer Libraries

Before writing a non-trivial helper, check whether a well-maintained library
already solves it — and use it if so. Hand-rolled utilities for solved problems
are a bug farm: date/time edge cases, unicode subtleties, and validation gaps
are exactly the things library authors have already fixed and you have not hit
yet. Fewer custom lines means fewer custom bugs.

## Use a library when

- The logic is non-trivial and easy to get subtly wrong: date/time handling,
  timezone and DST, parsing, validation, encryption, encoding, unicode,
  regex-heavy text processing, diffing, scheduling.
- It is a solved problem with a de-facto standard package.
- You would need more than ~10 lines or more than one edge case.
- The library is actively maintained, has a permissive license, and ships
  TypeScript types.

## Hand-roll only when

- It is a genuinely trivial local helper (< ~10 lines, no edge cases) that
  exists only to serve the surrounding code.
- The logic is business-specific and a library would be a forced fit.
- A dependency would be heavier than the thing it replaces.

## Never hand-roll

Anything cryptographic, security-sensitive, or with subtle spec-compliance
requirements. Use a vetted library. No exceptions.

## Check first, in this order

1. Grep `package.json` — is it already installed?
2. Is the library already imported somewhere in the repo? Reuse it.
3. If not installed, verify it exists on npm, is maintained, and fits the
   rules above.
4. Check bundle and runtime cost is acceptable for this project
   (server vs. client).

## Default mappings (TypeScript, non-exhaustive)

| Instead of writing | Use |
|---|---|
| Input and runtime validation, schemas | `zod` (or `valibot`) |
| Dates and timezones | `date-fns`, `dayjs`, or `Temporal` |
| String manipulation | `slugify`, `ts-case` |
| Queues | `p-queue` |
| Array and collection utilities | Native methods; `lodash-es` or `remeda` if truly needed |
| IDs and UUIDs | `nanoid` or `crypto.randomUUID()` |
| Concurrency control | `p-limit`, `p-map`; otherwise native `async`/`await` |
| HTTP requests | Native `fetch`; `zod` to parse the response |
| Env config | `env-schema` (zod) or `t3-env` |
| Logging | `pino` (+ `pino-pretty` in dev) |
| Test runner | `vitest` |
| E2E and browser testing | `playwright` |
| Lint and format | `biome` (or `eslint` + `prettier`) |
| File paths and globs | `node:path`, `tinyglobby` |
| Immutable state | `zustand`, `immer`, or `jotai` as appropriate |

## Rules

- Every new dependency must be justified: state what it replaces and why the
  library wins.
- Never add a dependency for a one-line utility the standard library already
  covers. Fewer dependencies is better.
- Match the existing `package.json` conventions: bundler, ESM vs CJS, semver
  range style. If the project already has a library that covers it, use that.
- Prefer packages with zero or few dependencies and native types.

## Example

**Task:** display a relative timestamp like "3 hours ago".

Without this skill: hand-write date math — wrong across DST boundaries,
untested, another copy of logic every project already imports.

With this skill:
1. Check `package.json` — no date library installed, nothing similar imported.
2. Shortlist maintained, MIT-licensed, typed candidates; install the one whose
   relative-time formatter replaces ~40 lines of custom calendar math.
3. Use it — DST-tested by its maintainers, zero custom code.

## Red flags

- `Math.random` anywhere near IDs, tokens, or security boundaries
- Dividing by `86400000` (or any fixed millis-per-day constant) in date logic
- Recommending a library without checking `package.json` first
