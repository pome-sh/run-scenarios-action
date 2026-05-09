# Smoke: triage open issues in acme/api

Placeholder scenario consumed by `.github/workflows/smoke.yml`. Mirrors the
bundled `examples/triage-agent/01-triage-acme-issues.md` happy path in
`pome-sh/pome` so a green smoke run also exercises the canonical seed.

## Setup

Uses the default GitHub twin seed:

- Repository `acme/api`
- Labels already exist: `bug`, `feature`, `question`
- One open issue, number `1`:
  - Title: `500 error on POST /orders after deploy`
  - Body: `Started failing right after the 14:00 deploy. Stack trace points to OrderController#create.`

## Prompt

Triage every open issue in acme/api. For each issue, read it, decide whether
it is a bug, feature request, or question, apply the matching label, then
post a one-sentence comment summarizing the reason for the classification.
Use only the existing repository labels — do not invent new ones.

## Success Criteria

- [D] Issue #1 has the `bug` label applied
- [D] No new labels were created on acme/api

## Config

```yaml
twins: [github]
timeout: 120
```
