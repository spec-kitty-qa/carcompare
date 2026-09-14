# CarCompare charter

## Purpose and confirmed direction

A lightweight car comparison website using **Go** and **htmx**, comparing
externally sourced **price, fuel economy, and features**.

## Lightweight working policy

- Prefer server-rendered HTML, the Go standard library, and minimal dependencies.
  A separate SPA framework requires user approval.
- Investigate external providers before integration: coverage, licensing,
  attribution, pricing, and rate limits. Keep credentials server-side and out
  of Git; use bounded requests and useful failure states.
- Preserve sources and retrieval timestamps. Make missing or stale data visible;
  never invent values. Distinguish unknown features from absent ones, and avoid
  misleading comparisons across variants, currencies, price bases, units, or
  fuel-economy test cycles.
- Once code exists, use `gofmt`, `go test ./...`, and `go vet ./...`. Test relevant
  comparison and HTTP behavior with fixtures, including provider failures and
  missing or incompatible data. Check affected browser flows for htmx changes.
  Report checks that could not be run.
- Keep reviews, dependencies, and documentation proportionate. No numeric
  coverage or application-latency target and no multi-reviewer requirement.
  Document setup, tests, provider decisions, and data limitations concisely.
- Obtain user approval for exceptions and update the interview answers and both
  charter files together when direction changes.

These working safeguards are a first-pass synthesis, not additional product
features requested by the user. The data-comparability rules are project-specific
policy: no built-in doctrine match is claimed and no broad paradigm is selected.

## Open decisions

Audience, geographic market, external provider, hosting, refresh cadence, and
numeric performance targets remain open for later discovery. This charter does
not select a provider or authorize a paid subscription.

## Authority

`charter.yaml` contains runtime policy; this document is its human-readable
companion. `interview/answers.yaml` records the interview synthesis. The generated
catalog is a reference inventory, not blanket adoption of its contents.
