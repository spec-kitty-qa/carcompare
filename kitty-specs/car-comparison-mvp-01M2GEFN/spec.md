# Mission Specification: Car comparison MVP

**Mission Branch**: `feat/car-comparison-mvp`  
**Mission**: `car-comparison-mvp-01M2GEFN`  
**Created**: 2026-09-14  
**Status**: Ready for planning  
**Input**: Confirmed discovery interview; see `decisions/DM-*.md`.

## Confirmed Intent

US new-car shoppers can choose two or three specific model-year/trim combinations
and compare MSRP in US dollars, EPA fuel economy, and features side by side,
without signing in. The site uses free, permitted external data. Coverage may
be limited, but missing values, stale information, and incompatible figures
must never be disguised as complete, current, directly comparable data.

The user selected Go and htmx. Those are explicit delivery constraints, not
architecture decisions made by this specification.

**Primary scenario**: a shopper choosing between new cars selects supported
variants and sees their prices, efficiency figures, and features together.
**Main exception**: a field or provider is unavailable; preserve useful available
information and clearly explain gaps or stale values instead of inventing data.
**Invariant**: each displayed fact must refer to the identified vehicle variant
and retain its source and meaning.

**Confirmation**: `DM-01M2GF1NWFDB47T51ECERNMZPE` records the user's approval.

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Compare a shortlist (Priority: P1)

As a US new-car shopper, I want to select two or three variants and compare
price, fuel economy, and features without creating an account.

**Why this priority**: this is the complete core value of the website.

**Independent Test**: with a controlled catalog of three supported variants,
select two, compare them, add the third, and remove or replace a selection.
Verify labels and values against the catalog without any sign-in step.

**Acceptance Scenarios**:

1. **AC-001**: Given supported variants with identifiable make, model, model year,
   trim, and any distinguishing powertrain, when the shopper selects two distinct
   variants, then a side-by-side comparison identifies both and shows separate
   MSRP, EPA fuel-economy, and feature sections.
2. **AC-002**: Given two selected variants, when the shopper adds a third, then all
   three appear; a fourth or duplicate selection is prevented with an explanation.
   Removing or replacing a selection updates the comparison without mixing values.
3. **AC-003**: Given fewer than two selected variants, when the shopper attempts
   comparison, then the site explains that two or three distinct variants are
   required. An unsupported or invalid selection does not produce a fabricated car.
4. **AC-004**: Given available comparison data, when the shopper reads it, then
   MSRP is labelled in USD with its known price basis, EPA metrics retain their
   units and labels, and feature rows align by meaning rather than merely by name.
5. **AC-005**: Given a new visitor, when they select cars and view or change the
   comparison, then no account or sign-in is requested.

### User Story 2 - Understand limits and trust the comparison (Priority: P1)

As a shopper, I want to distinguish known facts from unavailable or outdated
information so I do not make a purchase decision from misleading comparisons.

**Why this priority**: honest limitations are necessary when relying on free data.

**Independent Test**: compare fixture variants with missing MSRP, unknown features,
mixed EPA metric types, and differing retrieval times; then simulate provider
failure. Verify labels and preserved values against the fixture evidence.

**Acceptance Scenarios**:

1. **AC-006**: Given a missing value, when the comparison is shown, then it is
   explicitly unavailable, not zero, an invented estimate, or a claim of absence.
   Features distinguish known standard, optional, absent, and unknown status when
   those distinctions are supported; unknown must not be presented as absent.
2. **AC-007**: Given available values, when the shopper inspects their provenance,
   then each fact can be traced to its source and retrieval time. Shared labels
   are acceptable only where they unambiguously cover the same facts. Source
   publication/update dates are shown when provided and are not confused with
   retrieval time.
3. **AC-008**: Given figures with differing price bases, EPA metrics, or test
   cycles, when displayed together, then their differences are labelled and no
   unsupported difference, ranking, or equivalence is asserted. MPG and MPGe
   are not silently treated as the same metric.
4. **AC-009**: Given provider failure, rate limiting, or invalid upstream data,
   when the shopper requests comparison, then useful already available facts
   remain usable and affected data is marked unavailable, or retained data is
   clearly labelled stale where permitted. The site explains the failure and
   offers a retry or return to selection rather than an indefinite loading state.
5. **AC-010**: Given a catalog with limited coverage or no matching supported
   variant, when the shopper searches or selects cars, then the site states the
   coverage limitation or no-results state and allows changing the selection.

### Edge Cases

- Zero, one, four, duplicate, invalid, or no-longer-supported selections: AC-002/003.
- Same model name but different year, trim, engine, or drivetrain: AC-001/004;
  do not merge distinct configurations or silently apply a model-wide fact to a trim.
- Missing price components or optional equipment pricing: AC-004/006/008; MSRP is
  not a dealer offer or an out-the-door quote.
- Unknown versus absent feature; differing names for equivalent features:
  AC-004/006; only align when the meaning is supported by source evidence.
- Gasoline, hybrid, plug-in hybrid, or electric variants if covered by verified
  sources: AC-008; do not promise coverage of every powertrain or invent metrics.
- Partial response, malformed upstream values, timeout, and rate limit: AC-006/009.
- Different source update dates or expired freshness windows: AC-007/009; the
  planning phase must define a documented freshness policy before integration.
- No useful free data source: stop integration and report the feasibility blocker
  under C-003; do not substitute fabricated production data.

## Requirements *(mandatory)*

### Functional Requirements

| ID | Title | User Story | Priority | Status |
|----|-------|------------|----------|--------|
| FR-001 | Discover supported variants | As a shopper, I can find and select supported US new-car variants identified by make, model, model year, trim, and any necessary powertrain distinction, and see no-results or coverage limitations. Acceptance: AC-001, AC-010. | High | Open |
| FR-002 | Manage a comparison set | As a shopper, I can compare two or three distinct variants, add, remove, or replace them, and receive clear validation for duplicates, invalid variants, or selection counts outside that range. Acceptance: AC-002, AC-003. | High | Open |
| FR-003 | View a side-by-side comparison | As a shopper, I can see the selected variants together with aligned MSRP, EPA fuel-economy, and feature sections that remain correctly associated when selections change. Acceptance: AC-001, AC-002, AC-004. | High | Open |
| FR-004 | Understand MSRP | As a shopper, I can read available manufacturer suggested retail prices in USD with their known basis and inclusions or exclusions, without confusing them with dealer or out-the-door quotes. Acceptance: AC-004, AC-006, AC-008. | High | Open |
| FR-005 | Understand EPA fuel economy | As a shopper, I can read available EPA efficiency figures with units and metric labels, including city/highway/combined distinctions where provided, without incompatible metrics being presented as equivalent. Acceptance: AC-004, AC-008. | High | Open |
| FR-006 | Compare feature availability | As a shopper, I can compare equivalent feature meanings and distinguish known standard, optional, absent, and unknown statuses without unsupported inferences. Acceptance: AC-004, AC-006. | High | Open |
| FR-007 | Inspect source and freshness | As a shopper, I can identify sources, retrieval times, provided source update dates, and whether displayed data is stale under the documented freshness policy. Acceptance: AC-007, AC-009. | High | Open |
| FR-008 | Recognize unavailable data | As a shopper, I see explicit unavailable labels for missing or invalid facts rather than fabricated values, zeros, or false feature-absence claims. Acceptance: AC-006. | High | Open |
| FR-009 | Recover from data failures | As a shopper, I receive an understandable failure state with retry or return-to-selection, while any remaining useful data stays available and stale data is labelled. Acceptance: AC-009. | High | Open |
| FR-010 | Compare without an account | As a visitor, I can complete the selection and comparison workflow without registration or authentication. Acceptance: AC-005. | High | Open |

### Non-Functional Requirements

| ID | Title | Requirement | Category | Priority | Status |
|----|-------|-------------|----------|----------|--------|
| NFR-001 | Complete fact traceability | In acceptance fixtures and the verified live-data sample, 100% of displayed non-missing comparison facts map to an identified variant, source, and retrieval timestamp; zero unsupported substitutions are accepted. | Data integrity | High | Open |
| NFR-002 | Credential confidentiality | If a free provider requires credentials, zero provider credentials may appear in visitor-visible content, URLs, browser requests, application logs, or committed files; verify these surfaces before delivery. | Security | High | Open |

No numeric traffic, latency, or coverage-percentage target was agreed. Planning
must define bounded external-request behavior and a freshness policy; this spec
does not introduce unapproved performance infrastructure or national catalog claims.

### Constraints

| ID | Title | Constraint | Category | Priority | Status |
|----|-------|------------|----------|----------|--------|
| C-001 | Lightweight chosen stack | Use a Go backend, server-rendered HTML, and htmx. Prefer the Go standard library and minimal dependencies; a separate SPA framework requires user approval. | Technical | High | Open |
| C-002 | Free and permitted external data | Use only external sources that permit the intended access and display without payment, meeting their licensing, attribution, rate-limit, and retention terms. No paid subscription is authorized. | Business | High | Open |
| C-003 | Provider feasibility before integration | During planning, verify source access, terms, variant matching, field coverage, and freshness limitations. Demonstrate useful real comparisons with at least two distinct supported variants and real values in each core category across that sample. If this cannot be established using free sources, stop integration and report evidence and scope options to the user; fabricated data cannot satisfy this checkpoint. | Delivery | High | Open |
| C-004 | Bounded product scope | Cover US new-car comparisons only. Exclude used-car listings, accounts, sign-in, purchasing, and dealer/out-the-door price promises. Full-market or all-powertrain coverage is not required. | Scope | High | Open |
| C-005 | Verification and documentation | Follow the charter's Go formatting, tests, and static checks; use deterministic fixtures for provider failures and missing/incompatible data. Check affected browser flows and document setup, tests, sources, coverage, freshness, and known limitations. Report checks not performed. | Quality | High | Open |

### Key Entities *(include if feature involves data)*

- **Vehicle variant**: a supported US-market make/model/model-year/trim combination,
  further distinguished by engine, drivetrain, or other configuration when needed
  to correctly associate price, efficiency, and feature facts.
- **Comparison set**: the shopper's two or three distinct selected variants; no
  user account or durable saved-comparison capability is required.
- **MSRP fact**: a manufacturer suggested retail price in USD with its price basis,
  known inclusions/exclusions, variant association, and provenance.
- **Efficiency fact**: an EPA-labelled metric and value with unit, applicable
  conditions such as city/highway/combined, variant association, and provenance.
- **Feature fact**: a named capability with evidence-supported meaning and
  standard/optional/absent/unknown status for the associated variant.
- **Source observation**: provenance for one or more facts, including source,
  retrieval timestamp, source update date if known, and freshness status.

### Domain Language

- **New car** means a US-market new-car variant covered by the selected sources,
  not an individual used listing or a guarantee of dealer inventory.
- **Variant** is the canonical comparison unit; do not use “model” as shorthand
  when model year, trim, or powertrain distinctions affect facts.
- **Price** in this MVP means **MSRP**, not transaction price, financing payment,
  dealer discount, or an out-the-door total.
- **Fuel economy** retains the source's EPA metric meaning. MPG and MPGe are
  distinct labels, not interchangeable units for an unsupported ranking.
- **Unavailable/unknown** expresses lack of evidence; **absent** requires evidence.
- **Retrieved** is when data was obtained, not when its underlying facts changed.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: A new visitor completes both a two-variant and a three-variant
  comparison without an account; all selected variant identities and all three
  comparison categories are visible together. Verify AC-001 through AC-005.
- **SC-002**: Every invalid-count, duplicate, unsupported-selection, and no-results
  acceptance case produces the specified guidance without a fabricated comparison.
- **SC-003**: Across the missing-data, mixed-metric, stale-data, and provider-failure
  acceptance cases, zero missing facts are fabricated, zero unknown features are
  falsely absent, and zero incompatible figures imply unsupported equivalence.
- **SC-004**: Every displayed fact in the acceptance sample has inspectable source
  and retrieval context, and every provider-failure case has a visible recovery
  path. Verify NFR-001 and AC-007 through AC-009.
- **SC-005**: A verified free-data sample contains at least two distinct variants
  that can be compared with real price, EPA efficiency, and feature evidence
  represented across the sample. Record missing fields and source terms; a
  fixture-only demonstration does not satisfy this outcome.

## Dependencies and Assumptions

- Free-data feasibility is unverified, not a claim that one free provider offers
  complete MSRP, EPA, and trim-level features. Multiple permitted sources may
  be considered; cross-source variant matching must be evidenced.
- Limited coverage was accepted. A bounded, explicitly identified supported
  catalog is sufficient; specific makes, model years, trims, and powertrains
  depend on the planning investigation. Unsupported choices must remain honest.
- Feature taxonomy and source freshness limits will be derived from verified
  evidence during planning, not guessed from trim names. Missing source update
  dates must not be replaced with retrieval dates presented as publication dates.
- Fixtures may verify behavior offline but may not be represented as live data.
- Hosting, accounts with free providers if needed, and deployment credentials
  remain operational decisions; no paid access or public launch is authorized.
- Numeric sample counts in SC-005/C-003 express the smallest useful two-car
  comparison, not a promise of broad catalog completeness.

## Definition of Done

The functional acceptance scenarios and both non-functional checks pass; the
free-data feasibility checkpoint is evidenced; charter verification results and
browser checks are recorded; and setup plus provider/coverage/freshness limitations
are documented. A blocked feasibility checkpoint means the MVP is not complete,
even if fixture-backed UI behavior works. Planning readiness means confirmed
requirements, not verified provider feasibility or implemented software.
