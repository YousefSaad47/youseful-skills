---
name: follow-standards
description: Use when making design decisions involving interoperable formats — dates, times, currencies, identifiers, HTTP, authentication, serialization, or APIs. Triggers on date formatting, timezones, currency handling, ID generation, HTTP status codes, error formats, auth flows, token formats, serialization choices, API design, or any "what format should I use" decision.
---

# Follow Standards

Custom formats are a tax everyone downstream pays: parsers written by hand,
ambiguities resolved by guessing, migrations when the custom scheme meets
reality. Standards exist because thousands of engineers already paid that
tax. Use their work — pick the established, interoperable pattern for the
job instead of inventing one.

This skill answers *which standard governs this problem*. Whether that
standard is still current is `current-sources`' job; which library
implements it is `prefer-libraries`' job. Three different questions, three
different skills.

## Check before deciding

Before choosing a format, encoding, or protocol behavior:

1. Name the problem class (instant in time? money amount? unique ID?
   error response? auth flow?).
2. Find the governing standard — RFC, ISO, W3C, or vendor-neutral spec —
   and read what it actually requires, not what you remember of it.
3. If several standards compete, choose the most widely adopted one for
   the use case, and say why the others lost.
4. Check the standard is not superseded (an RFC that obsoletes another,
   a deprecated OAuth flow). Old standards are just custom formats with
   better marketing.

## Default mappings (non-exhaustive)

| Problem | Standard | Custom trap to avoid |
|---|---|---|
| Instant in time | ISO 8601 / RFC 3339 (`2026-10-01T12:00:00Z`) | Locale strings, Unix floats, ambiguous `MM/DD/YYYY` |
| Money amount | ISO 4217 currency + minor units (integer) | Floats, assumed 2 decimals, missing currency |
| Unique ID | UUID v4 (RFC 4122) or `crypto.randomUUID()` | `Math.random`, timestamps, incrementing integers |
| HTTP errors | RFC 9457 problem details (`type`, `title`, `status`, `detail`) | Bespoke `{ error: "bad" }` shapes per endpoint |
| Auth delegation | OAuth 2.1 + OIDC discovery | Hand-rolled tokens, custom SSO, password sharing |
| Pagination | Cursor-based (opaque cursor + `has_more`) | Offset for large or mutating datasets |
| Webhooks | HMAC-signed payload, timestamp tolerance, replay dedup | Unsigned callbacks, no idempotency |
| Serialization | JSON with explicit schema, or protobuf where justified | Ambiguous CSV, stringly-typed fields |

## Rules

- Interoperability wins ties. When two standards both work, the one more
  systems already speak is the answer.
- Never invent a format a standard already defines. "Ours is simpler" is
  how you get a migration in eighteen months.
- Deprecated is deprecated: a superseded RFC, a removed OAuth flow, or a
  withdrawn draft is not a standard anymore — treat it as custom.
- Record which standard you chose and its version or date in code comments
  or docs, so the next reader can re-verify instead of re-deriving.

## Example

**Task:** return errors from a new REST API.

Without this skill: `{ error: "bad request" }` — unparseable by clients,
no stable shape, every endpoint invents its own.

With this skill:
1. Problem class: machine-readable HTTP error → RFC 9457.
2. Shape: `{ type, title, status, detail, instance }` with a `type` URI
   per error kind.
3. Record "RFC 9457, July 2023" next to the error module.

## Limits — read this honestly

Standards cover the common case, not your domain's sharp edges — Money
needs more than ISO 4217 (rounding rules live in business logic), and time
needs more than RFC 3339 (DST policy is yours to define). The standard is
the floor, never the whole design. And standards move: re-verify currency
via `current-sources` before treating any entry above as settled.

## Common mistakes

- Inventing a custom envelope when a standard shape exists (observed: bespoke `{error: {code…}}` instead of RFC 9457)
- Citing a superseded standard (RFC 7807, expired drafts) as current
- Recording no version or date, leaving the next reader unable to re-verify
