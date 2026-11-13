# Autonomous delivery contracts and scheduler integration

Status: **Proposed**  
Version: **0.1**  
Prepared: **2026-11-12**  
Working repository: **rezzell/symphony**

## Purpose

Specify the delivery behavior that Rezzell owns, retain `ai-sdlc` as a standalone-capable reference implementation, and define the contract through which a scheduler can host it. Use this fork for the proposed design, implementation planning, and a Symphony-derived runtime. Rust is the leading implementation candidate, not an approved dependency of the portable specification.

The intended operating model is conversational product design followed by autonomous planning, implementation, verification, review, remediation, and closeout. Return to the human for decisions outside delegated authority, not for routine project-management transitions.

This package is a reviewable proposal, not an approved company operating standard, executable configuration, working Rust implementation, or claim of conformance. Recording agreed design direction does not approve every schema, algorithm, or operational recommendation below. No production deployment, credential change, paid inference, or merge is authorized by this document.

## Read in this order

| Document | Responsibility |
| --- | --- |
| [Delivery specification](SPEC.md) | Authority, ownership, lifecycle, continuity, host obligations, and compatibility. |
| [Phase contracts](PHASE-CONTRACTS.md) | Dispatch, result, checkpoint, handoff, and phase-specific evidence contracts. |
| [Resources and telemetry](RESOURCES-AND-TELEMETRY.md) | Time, usage, provider profiles, allocation, routing, and OpenTelemetry investigation requirements. |
| [Implementation plan](IMPLEMENTATION-PLAN.md) | Repository allocation, migration, work packages, dependencies, and acceptance gates. |
| [Decisions and conformance](DECISIONS-AND-CONFORMANCE.md) | Confirmed design inputs, unresolved choices, and test scenarios. |

## Design boundary

| Concern | Owner |
| --- | --- |
| What constitutes authorized and verified delivery | Portable delivery specification and applicable policy |
| How an agent performs a phase | Reference skills or another conforming implementation |
| When and where execution runs | Scheduler and provider/workspace adapters |
| Accepted intent and material approvals | Configured knowledge/decision owner |
| Assignment, priority, dependencies, and delivery status | Configured delivery system; GitHub Issues is the first integration |
| Code, tests, source review, and integration facts | Repository and source-review systems |
| Continuity, waiting triggers, and claims | Configured coordination mechanism; not a competing delivery backlog |
| Attempts, reservations, enforcement, and diagnostics | Host operational records, without authority to redefine product or delivery facts |

**Derived conclusion:** delivery contracts and a scheduler solve complementary problems. A new scheduler does not replace the meaning of approval, completion, or a coordination handoff.

## Publication and authority

This public proposal contains generic mechanisms, synthetic examples, and design requirements explicitly discussed for this work. It does not reproduce private operating pages, client material, credentials, account identifiers, confidential routing, or internal approval matrices. Deployments supply their own authority and confidentiality policy through controlled references.

Use these classifications when extending the proposal:

- **Observed:** supported by a cited source or verified system state.
- **Derived:** an architectural inference from observations.
- **Proposed:** a recommendation or contract detail awaiting review.
- **Ratified:** reserved for a bounded decision with an authorized approval record. This change does not mark any new governance artifact Ratified or Approved.

The confirmed discussion inputs are recorded separately in [Decisions and conformance](DECISIONS-AND-CONFORMANCE.md). Open questions must not be silently promoted into implementation defaults.

## Source baseline

The fork baseline is commit `be10a1b79df723d6d7612b5651c8522704dafb2e`; its root `SPEC.md` blob is `cd24131a1e2358cbfecc4f6efb028fc9fc6edefc`. The inspected `ai-sdlc` baseline is commit `c993fa3a642cfcb95afa05f7d0a4749569872549`.

**Observed:** the root Symphony specification describes a scheduler/runner and tracker reader. Its reference implementation and workflow are distinct from that specification. The existing `ai-sdlc` delivery contract already contains run identity, dispatches, receipts, checkpoints, and promotion boundaries. This proposal extracts and extends those concepts rather than creating a competing lifecycle. See sources S1-S4.

This package leaves the upstream root `SPEC.md`, example workflow, runtime, and license/notice files unchanged. Future upstream compatibility claims must identify a pinned baseline and explicit extensions or deviations. Repository documentation here is the working home for this proposal; company governance remains in its configured canonical location. On adoption, portable contract material should move to `ai-sdlc`, leaving versioned references here rather than two independently edited specifications.

## Sources

Public source references are provided for reproducibility; private governance sources were reviewed separately and are deliberately not exported.

- **S1:** [Pinned Symphony specification](https://github.com/rezzell/symphony/blob/be10a1b79df723d6d7612b5651c8522704dafb2e/SPEC.md), especially sections 1-3, 7-10, and workspace-hook failure semantics.
- **S2:** [Pinned reference implementation documentation](https://github.com/rezzell/symphony/blob/be10a1b79df723d6d7612b5651c8522704dafb2e/elixir/README.md) and [example workflow](https://github.com/rezzell/symphony/blob/be10a1b79df723d6d7612b5651c8522704dafb2e/elixir/WORKFLOW.md).
- **S3:** [Existing delivery-run contract](https://github.com/rezzell/ai-sdlc/blob/c993fa3a642cfcb95afa05f7d0a4749569872549/skills/sdlc-deliver/references/delivery-run-contract.md) and [terminology](https://github.com/rezzell/ai-sdlc/blob/c993fa3a642cfcb95afa05f7d0a4749569872549/skills/sdlc-deliver/references/terminology.md).
- **S4:** [Harness-neutral execution routing](https://github.com/rezzell/ai-sdlc/blob/c993fa3a642cfcb95afa05f7d0a4749569872549/docs/development/execution-routing.md) and [runtime/provider/workspace contract](https://github.com/rezzell/ai-sdlc/blob/c993fa3a642cfcb95afa05f7d0a4749569872549/skills/sdlc-portfolio-governor/references/runtime-provider-workspace-contract.md).
- **S5:** [GitHub tracker normalization](https://github.com/rezzell/symphony/blob/be10a1b79df723d6d7612b5651c8522704dafb2e/elixir/lib/symphony_elixir/github/client.ex) and [provider-native tool](https://github.com/rezzell/symphony/blob/be10a1b79df723d6d7612b5651c8522704dafb2e/elixir/lib/symphony_elixir/github/agent_tool.ex).
- **S6:** [Existing merge skill](https://github.com/rezzell/ai-sdlc/blob/c993fa3a642cfcb95afa05f7d0a4749569872549/skills/sdlc-merge-pr/SKILL.md) and [closeout skill](https://github.com/rezzell/ai-sdlc/blob/c993fa3a642cfcb95afa05f7d0a4749569872549/skills/sdlc-closeout-delivery/SKILL.md).
- **S7:** [OpenTelemetry metrics](https://opentelemetry.io/docs/concepts/signals/metrics/) and [traces](https://opentelemetry.io/docs/concepts/signals/traces/), consulted 2026-11-12. These inform the proposed instrumentation mapping; proposed `sdlc.*` names are not upstream semantic conventions.
- **S8:** [Codex authentication](https://developers.openai.com/codex/auth/), consulted 2026-11-12. Provider behavior must be reverified against the client version used in a pilot.

## Current delivery state

Only proposal documentation is supplied by this change. Schemas, validators, adapters, executable conformance tests, dashboards, and the Rust host are planned deliverables. No implementation issue is automatically admitted for autonomous execution, and no company standard is superseded.

The recommended next action is to review the blocking decisions in [Decisions and conformance](DECISIONS-AND-CONFORMANCE.md), then authorize a bounded contract-extraction and conformance work package. Do not interpret this plan as permission to implement all milestones without limits.
