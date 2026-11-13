# Implementation and migration plan

Status: **Proposed**  
Version: **0.1**  
Parent: [Proposal overview](README.md)

## 1. Outcome and non-goals

Produce a portable delivery contract, an adapted standalone/scheduled `ai-sdlc` reference implementation, a host adapter proven against a pinned Symphony baseline, and a separately testable Symphony-derived host. Rust is the leading candidate pending D08.

The initial goal is GitHub-first delivery from accepted intent through verified integration and closeout, using subscription-backed execution without separately billed inference. It is not a company-wide documentation migration, replacement task platform, automatic approval system, public multi-tenant credential service, or production deployment engine.

This is an issue-ready plan, not authorization to start every work package. Each admitted package must receive its own bounded scope and allocations under the governing outcome authorization. No dates, assignees, commercial commitments, or additional budgets are implied.

## 2. Repository allocation

| Location | Intended responsibility |
| --- | --- |
| `rezzell/symphony/docs/proposals/autonomous-delivery-v0.1/` | Current working proposal, decision backlog, conformance scenarios, and implementation plan. |
| Future `rezzell/ai-sdlc/SPEC.md` and supporting contract/schema/test paths | Portable delivery semantics, contract definitions, reference skills, human/machine projections, and delivery conformance tests. Paths are proposed. |
| `rezzell/symphony` | Pinned upstream baseline, scheduler implementation, host/provider/workspace adapters, compatibility matrix, runtime tests, and repository workflow. |
| Controlled deployment policy | Actual authority rules, secret references, client overlays, routing preferences, and restricted evidence access. |
| Configured knowledge/decision owner | Material company decisions and approval records; this repository does not silently replace that authority. |

On promotion into `ai-sdlc`, replace the working portable-spec copy with an explicit versioned reference or mark it superseded. Do not maintain two independently editable normative specifications. Keep the scheduler's extensions separately named from upstream `SPEC.md`.

## 3. Upstream inheritance and intentional extensions

The fork baseline is `be10a1b79df723d6d7612b5651c8522704dafb2e`. Existing behavior below is source-level observation, not an installed-system test. See [sources S1-S6](README.md#sources).

| Baseline behavior / gap | Integration response | Primary requirement |
| --- | --- | --- |
| Scheduler, workspace lifecycle, continuation, retry and reconciliation exist. | Reuse these concepts; do not duplicate them in an independent skill-level process supervisor. | CORE-02/03 |
| The sample workflow contains its own process and human-review assumptions. | Supply a thin workflow pointing at delivery contracts rather than layering conflicting instructions. | CORE-04, COMPAT-02 |
| GitHub normalization uses open/closed state and does not populate dependencies. | Add explicit eligibility/dependency/coordination mappings; dispatch only ready authorized work. | HOST-05 |
| Operator-input blocks are documented as in-memory. | Persist blocked predicates and validate them after restart. | HOST-05, CONT-02 |
| Worker turns are not typed delivery-phase contracts. | Add dispatch/result validation and phase-level evidence correlation. | PHASE-01/03 |
| In-process claims do not establish cross-controller ownership. | Start single-controller per scope; enforce stale-worker exclusion before later expansion. | HOST-02 |
| Closing a ticket can stop execution and clean the workspace. | Preserve closeout obligations and make cleanup a separately verified operation. | LIFE-04, HOST-06, REC-02 |
| Retry/turn caps are not total logical-run budget enforcement. | Preserve phase clocks, profile usage and reservations across attempts. | RES-02/03, TIME-02/03 |
| The reference runner uses Codex app-server. | Verify a subscription-only Codex binding first; add a separate supported Claude Code binding, not an executable-name substitution. | PROFILE-03 |
| Raw authenticated tools are not a complete mutation policy. | Bind tool/transport permissions and restrictions explicitly. | HOST-04 |

A Rust port must document which behaviors it inherits, extends, or intentionally changes. Passing delivery conformance is distinct from satisfying every upstream scheduler requirement; report both instead of advertising vague full compatibility.

## 4. Work packages and acceptance gates

### W01 — Freeze boundaries and decision prerequisites

Destination: this fork and the appropriate governance decision records. Dependencies: none.

Deliver responsibility map, term reconciliation, pinned baseline, public/private classification, and resolutions for the decisions blocking the next package. Preserve the distinction between agreed design inputs and newly proposed implementation details.

Acceptance: each consequential action has one authority owner; no unresolved choice is disguised as an approved default; current standards remain unchanged unless explicitly amended. Review D01, D02, D04, D07, and D11 before operational contract adoption.

### W02 — Extract portable contracts into ai-sdlc

Destination: `rezzell/ai-sdlc`. Dependency: W01 decisions needed for contracts.

Inventory every callable skill and its references. Map it to a phase or utility contract. Add a normative entrypoint, versioning/compatibility rules, schemas, synthetic examples, and human-readable projections. Reconcile existing lifecycle, handoff, routing, review, merge and closeout documents rather than copying them into a second policy set. Preserve package/version validation requirements.

Acceptance: every external entrypoint is mapped; both execution modes preserve material evidence/authority semantics; receipt validation rejects stale identity/revisions and unsupported capabilities; schema and example checks run in CI. Structural checks are not represented as behavioral proof.

### W03 — Build host-independent conformance fixtures

Destination: portable tests in `ai-sdlc`, host fixtures referenced from this fork. Dependency: W02 contract baseline.

Implement synthetic owner systems, deterministic fake clocks, usage feeds, checkpoints, worker events and fault injection. Turn the scenario catalog into tests with explicit preconditions, allowed side effects, prohibited effects and final evidence.

Acceptance: demonstrate tests failing on intentional regressions; cover crash-after-mutation, blocked restart, duplicate events, early issue closure, cancellation, parent/child accounting and the 5:1 allocation example. Do not require production credentials or paid inference for deterministic conformance.

### W04 — Adapt the existing skills without breaking standalone use

Destination: `ai-sdlc`. Dependencies: W02 and the relevant W03 fixtures.

Add explicit contract declarations, structured scheduled projections, equivalent concise interactive output, and a versioned host binding. Keep judgment-heavy phases in skills. Move only tested mechanical checks into code. Do not force every helper invocation into an externally scheduled phase.

Acceptance: the same synthetic accepted objective has equivalent material evidence in interactive and scheduled modes; missing isolation/capabilities are reported honestly; existing package tests pass; no new governance authority or unnecessary human gates appear.

### W05 — Prove a narrow GitHub/Symphony/Codex vertical slice

Destination: this fork plus its controlled workflow configuration. Dependencies: W03/W04 and applicable D02/D04/D06/D07/D09/D11 resolutions.

Use one trusted execution scope and one verified subscription-backed Codex profile. Wire GitHub eligibility, claims, checkpoint/result validation, budgets, closeout-safe cleanup and minimal telemetry. Supply owner-system access to knowledge via a separately approved tool/projection; do not migrate Notion merely to make the pilot work.

Acceptance: a bounded representative issue proceeds through verified closeout, pauses on a genuine decision, and resumes after a recorded resolution; interruption and quota tests preserve authority and consumption. No routine phase-transition human intervention and no separately billed inference. Failure cases matter as much as the happy path.

This is a contract-validation harness, not a commitment to maintain two production runtimes indefinitely.

### W06 — Implement the selected host language and recovery core

Destination: this fork, a proposed separate runtime directory such as `rust/`. Dependencies: host boundary stabilized by W03/W05; D08 language/platform decision.

Build configuration, capability negotiation, provider/process supervision, workspace management, claim enforcement, durable accounting, suspended predicates, cancellation and recovery. Reuse conformance fixtures. Keep Rust types behind language-neutral schemas and semantics. Preserve upstream source/specification history and licensing/notice files; do not claim an automatic drop-in replacement.

Acceptance: same applicable conformance suite passes against the new host; clean startup/restart/cancellation/cleanup on supported platforms; failures are diagnosable; no control-plane ownership drift. A small protocol/cancellation spike may precede W05, but live adoption waits for the recovery evidence.

### W07 — Multi-profile allocation, second provider, and full observability

Destination: this fork and shared contracts where necessary. Dependencies: W06 plus D03/D05/D09/D10 resolutions.

Implement multiple same-vendor profiles and capacity-domain accounting, sequential/shared-pool allocation, then phase-cost-aware pro rata with borrowing. Add Claude Code through its own verified supported subscription execution path. Add rules/analysis routing first; model-assisted routing is optional and bounded. Complete aggregate-to-run drill-down, protected trace correlation, and with/without-run analysis.

Acceptance: no duplicate entitlement from profile aliases; no provider change resets budget; comparable synthetic phases converge toward 5:1 under the agreed fixture; variable-cost/caching tests balance consumption rather than counts; a live runaway is diagnosable before completion. No external billing path is introduced by the routing or evaluation layer.

### W08 — Adoption, rollback, and contract evolution

Destination: this fork, `ai-sdlc`, and applicable governance records. Dependencies: prior evidence and explicit bounded adoption authorization.

Document supported compatibility profiles, operating limits, migration from the pilot, policy/contract version changes, backup/recovery and rollback. Keep standalone execution available. Rollback stops admissions, reconciles active effects, preserves accounting/evidence, and returns valid continuity records to the prior executor; it must not erase expenditure or pretend unfinished work completed.

Acceptance: explicit operator approval of the adoption scope; repeatable rollback exercise; actionable limits and known gaps; versioned contract references; no duplicated normative specification or shadow backlog.

## 5. Evaluation and progress reporting

Measure completed authorized outcomes, unnecessary interventions, valid decision escalations, recovery success, duplicate or stale mutations, acceptance-criteria coverage, escaped defects, usage by phase/profile, active elapsed time, worker-time, and maintenance work. Report failed/incomplete cases and measurement coverage. Do not fabricate a quality score when no registered test or human label exists.

Scheduling an issue or drafting a document is not implementation progress. Report separately: proposed, authored, structurally validated, behaviorally tested, pilot verified, and adopted. Preserve all failed experiments and estimator revisions needed to explain resource results.

## 6. Handoff into implementation

Goal: implement the portable delivery/host boundary without expanding authority or budget.

Scope: begin with the smallest authorized work package; GitHub-first, subscription-backed execution, standalone compatibility, no production release/deployment authority by default.

Current state: this Proposed package only; no new runtime, executable schemas, adapters, or conformance tests supplied.

Governing sources: pinned public sources in the [overview](README.md#sources), applicable repository instructions, and deployment-accessible canonical policy references. Private sources remain protected.

Decisions already made: design inputs C01-C10 in [Decisions and conformance](DECISIONS-AND-CONFORMANCE.md). Unresolved decisions: D01-D11 in the same document.

Artifacts changed by this proposal: six Markdown documents under this directory. Upstream root specification and executable sources are unchanged.

Risks: ambiguous crash promotion, quota measurement, closeout races, stale mutation ownership, and accidental competing authorities. Do not hide these with a successful demo.

Recommended next action: resolve the contract blockers required by W01 and authorize W02/W03 with bounded budgets, not the entire implementation backlog.
