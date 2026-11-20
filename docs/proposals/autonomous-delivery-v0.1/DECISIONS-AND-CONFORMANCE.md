# Decision register and conformance scenarios

Status: **Proposed**  
Version: **0.1**  
Parent: [Proposal overview](README.md)

## 1. How to read this register

C-records capture owner-confirmed design direction from the discussion authorizing this proposal. They are requirements inputs, not blanket approval of this document, production deployment, publication of private policy, or all suggested algorithms. D-records distinguish unresolved choices from bounded dispositions; technical direction does not supply runtime permission. No record here changes an existing company's Approved standard by itself.

Observed implementation claims are grounded in the pinned [source baseline](README.md#sources). Derived conclusions and proposed changes are distinguished from those observations. A final decision needs a bounded approval record and corresponding contract/test update; do not mark an entire proposal Approved merely because one decision was accepted.

## 2. Confirmed design inputs

| ID | Direction to preserve | Requirement coverage |
| --- | --- | --- |
| C01 | Separate portable delivery behavior, reference skills, and scheduler implementation. Keep standalone and scheduled use viable. | CORE-01/04, COMPAT-01/03 |
| C02 | Use GitHub Issues preferentially; retain a replaceable knowledge system and conversational product design. Use this fork for durable design/planning. | CORE-01, HOST-05; implementation plan |
| C03 | Mechanical decomposition inherits original approval and restrictions. Specific constraints such as one-issue-only override general decomposition permission. No extra scope or budget follows from new issues. | AUTH-01/05, RES-02 |
| C04 | Distinguish stage receipts, intra-work-scope checkpoints, and full coordination handoffs. Persistence or a new agent alone is not a handoff boundary. | CONT-01/05, PHASE-05/08 |
| C05 | Provider usage and time are independent top-level limits. Elapsed execution and aggregate worker-time are distinct. Runtime controls remain effective. | RES-01, TIME-01/04 |
| C06 | Time defaults are phase-normalized: plan-to-spec starts at 20 minutes; implementation at two hours. Do not impose 20 minutes on every phase or the whole delivery. | TIME-01/02 |
| C07 | Usage allowances belong to provider profiles; allow multiple profiles even from the same vendor. Use Codex or Claude Code through subscription-backed execution, without separately billed inference. | PROFILE-01/03 |
| C08 | Compose sequential, pro rata, shared-pool and routed policies. Routing may use rules, analysis, or a bounded lightweight model. | ALLOC-01/03 |
| C09 | Pro rata weights are target shares with idle-capacity borrowing. Use agent-phase cost estimates: 1,000,000 versus 200,000 comparable allowance units implies a 5:1 consumption target, not a rigid dispatch counter. Opaque caching does not mint capacity. | ALLOC-02/04 |
| C10 | Use OpenTelemetry-compatible observation with aggregate-to-run investigation, high-cardinality attribution where needed, and linked bounded traces. | OBS-01/06 |

## 3. Open decisions and recommendations

| ID | Decision needed | Proposed recommendation | Required before |
| --- | --- | --- | --- |
| D01 | Contract encoding, framing, canonicalization, compatibility and acknowledgement | Versioned JSON interchange and JSON Schema; YAML/Markdown authoring and human rendering; transport-neutral behavior. Pin versions and reject unsupported required semantics. Exact schema/transport remains unselected. | W02 schemas and scheduled adapter |
| D02 | Technical direction resolved for v0.1 proposed contracts; applicable permitting lifecycle/adoption profile remains unresolved | Consolidate the bounded D02-r2 direction below: item-run mapping, exact controller acceptance, interruption recovery and full-handoff promotion. No runtime journal silently replaces coordination authority. | Verified profile authority before checkpoint-only runtime continuation; executable conformance before adoption |
| D03 | Allocation evaluation horizon, quota-window mapping, fairness tolerance and tie-breaks | Align observation with declared provider allowance windows where meaningful, retain per-work consumption across resets, and use explicit normalization for unlike windows/units. Compare least-projected-utilization with deficit/fair-share alternatives using fixtures. Borrowing is already settled. | Production pro rata allocator |
| D04 | Remaining phase defaults, remediation allocation, parent/child phase budgets and overall delivery ceiling | Shared budget-scope phase clocks; retries/children do not replenish. Require finite configured limits for unspecified phases. Do not invent a global 20-minute or 140-minute rule. Decide the overall cap independently. | Full lifecycle admission |
| D05 | Usage observability, opaque metering, uncertainty margins, and external use of shared subscriptions | Separate usage, quota capacity and accounting estimates; declare confidence/lag and conservative reservations. Refuse guarantees the adapter cannot enforce. Validate actual metering before choosing precision and tolerances. | Provider-specific hard-limit claims |
| D06 | Checkpoint/shutdown reserve, cancellation grace and uncertain execution time | Allocate stop/report capacity within the original allowance; enforce host deadlines and reconcile non-cancellable effects. Define margins and conservative clock recovery without unlimited grace. | Unattended live execution |
| D07 | Durable storage, claim enforcement, and initial deployment topology | One active controller per dispatch scope; durable operational journal; mutation fencing where enforceable, otherwise stop/isolate before replacement. Database and deployment packaging remain open. | Recovery core and multi-worker pilot |
| D08 | Rust selection, operating systems, packaging, and upstream compatibility target | Rust is the leading candidate. Validate client protocol, process-tree cancellation and storage on target platforms in a narrow spike. Keep upstream baseline pinned and list intentional deviations. | Host implementation commitment |
| D09 | Supported Codex/Claude Code invocation, authentication, and provider behavior | Verify official-client subscription execution per pinned version; do not route extracted credentials through arbitrary services. First Codex fixture/pilot, then independently verified Claude Code binding. No paid fallback or account-limit evasion. | Each real provider adapter |
| D10 | OpenTelemetry mapping, backend, retention, sampling and access policy | Versioned delivery signals with complete core accounting, bounded linked traces and mandatory contributor drill-down. Select storage/retention from actual requirements; do not ban necessary high-cardinality investigation. | Operational observability acceptance |
| D11 | GitHub eligibility/coordination mapping, cleanup/closeout protocol, migration and publication boundaries | Map existing authoritative states explicitly; keep coordination references non-authoritative; persist closeout obligation before terminal transitions; preserve unmerged work. Promote portable contracts into ai-sdlc once, with versioned references here and protected deployment overlays. | Pilot mutations, cleanup and adoption |

Optional phase-level scheduling, distributed active controllers, hosted multi-tenancy, automatic deployment, and model-assisted routing are not initial prerequisites. Absence of those features must not weaken the baseline contracts.

### D02 bounded technical disposition and source mapping

**Bounded accountable-owner disposition, 2026-11-19:** D02-r2 at commit `c291ad893679d9eb52c5405c7281a17c5129ee32`, blob `2fd2ce432a13c8139793829eefc563a246b795ee`, is approved for the **v0.1 proposed technical-contract scope only**. The [immutable review source](https://github.com/rezzell/symphony/blob/c291ad893679d9eb52c5405c7281a17c5129ee32/docs/proposals/autonomous-delivery-v0.1/D02-CONTINUATION-REVIEW.md) supplies the direction consolidated here. This public projection records exact technical scope and exclusions; private approval provenance remains in its owner system.

This does not approve the whole specification, checkpoint-only runtime permission, runtime implementation, integration/merge, deployment, release, outcome acceptance, new spending or a company-standard amendment. It selects no lifecycle/adoption profile, wire encoding, storage/fencing implementation, language or shutdown reserve. C01–C10 and other unresolved decisions remain unchanged. Technical consolidation does not authorize a runtime exchange; its applicable permitting-profile revision, deployment scope and exchange types must still be verified.

| D02-r2 source | Canonical v0.1 proposal destination | Planned scenarios |
| --- | --- | --- |
| D02-ID1 | [SPEC identity model](SPEC.md#4-identity-model): refined identity table, ID-01/02 preserved, ID-03 mapping and compatibility | T48–T49 |
| D02-C1 | [SPEC continuity](SPEC.md#6-continuity-and-promotion): CONT-01, all six predicates, evidence roles and exact acceptance bindings | T37–T39, T41, T43, T46, T50 |
| D02-C2 | SPEC CONT-02: all existing boundaries plus every unexpected interruption | T40–T41, T45 |
| D02-C3 | SPEC CONT-04: classification, recovery observations and authorized recovery limits | T39–T42, T47 |
| D02-C4 | SPEC CONT-06: stable promotion obligation, persistence/link/read-back and redispatch gates | T40, T44, T46, T50, T52 |
| D02-C5 | SPEC CONT-05: preserved no-effects assumption prohibition and cumulative accounting | T42, T47, T50 |
| Phase evidence additions | [PHASE-CONTRACTS](PHASE-CONTRACTS.md#8-continuation-and-recovery-evidence): evidence matrix and PHASE-11; PHASE-05/08 amended | T38, T45, T49, T51 |
| T37–T52 | [Canonical conformance catalog](#4-test-obligations), following unchanged T01–T36 | All remain planned, not executed |

The [historical review rationale](D02-CONTINUATION-REVIEW.md) retains comparison evidence and examples, with canonical clause links replacing the proposed amendment payload. Existing ai-sdlc item-run semantics remain compatible through explicit versioned mappings; older consumers unable to validate required mappings or acceptance evidence fail closed.

## 4. Test obligations

These are a **planned behavioral conformance catalog**, not executable tests delivered by this proposal and not evidence that any existing implementation passes. Each future test must pin contract/policy versions, inputs, fake clock and provider observations, expected state, allowed/prohibited mutations, and retained evidence. Include negative tests that fail on an intentional regression. Deterministic fixtures should require neither live subscriptions nor paid inference.

| Test | Scenario and required outcome | Requirements |
| --- | --- | --- |
| T01 | Split an approved outcome into three bounded deliverables. All inherit authority/restrictions and shared budget; no unnecessary reapproval. | AUTH-01/04, RES-02 |
| T02 | Authorization says one issue only. Reject additional delivery issues; allow only compliant internal organization. | AUTH-02/05 |
| T03 | Decomposition proposes expanded scope or customer commitment. Stop for the exact unauthorized decision. | AUTH-05, CONT-02 |
| T04 | Planning exhausts allowance before code changes. Report measured/estimated planning and decomposition consumption and incomplete outcome; no automatic extra allowance. | RES-01/02 |
| T05 | Plan-to-spec reaches 20 minutes while implementation has a separate two-hour allocation. Stop planning at its boundary; do not impose 20 minutes on implementation. | TIME-01/03 |
| T06 | Three implementation workers overlap for ten minutes. Attribute ten phase-elapsed minutes, thirty worker-minutes plus coordinator time, and all provider usage without nested-span double counting. | TIME-01, OBS-01 |
| T07 | Child implementations execute sequentially for 50 + 50 + 50 minutes under a shared two-hour allowance. Stop at 120 total active minutes; issue splitting grants no third fresh allowance. | RES-02, TIME-02 |
| T08 | Worker retries or changes provider after 90 implementation minutes. Only the remaining authorized implementation time and usage remain. | TIME-02, RES-02 |
| T09 | Agent emits heartbeats/tool calls while failing to make progress. Absolute deadline still stops it and descendants. | TIME-03/04 |
| T10 | Coordinator or collector crashes while work may still run. Preserve or conservatively reconstruct consumption, reconcile descendants, and prevent new admissions when accounting cannot safely recover. | RES-03/04, OBS-01 |
| T11 | Provider temporarily exhausts capacity but work authorization remains. Park durably and resume only when the declared resource predicate and authority checks pass. | RES-05, HOST-05 |
| T12 | Work usage allowance is exhausted, then provider quota resets. Work remains stopped; the reset does not replenish its authorization. | RES-05 |
| T13 | Two profiles share one subscription. Enforce one underlying capacity constraint and distinct internal allowances; do not double entitlement. | PROFILE-01/02 |
| T14 | API-funded fallback or paid overage is available in configuration. Reject it under the subscription-only profile, including manager/reviewer/router stages. | PROFILE-03 |
| T15 | Synthetic A=1,000,000 and B=200,000, same window/unit, independent capacity, equal 10,000-token phases and continuous eligible demand. Approach 5:1 allocation within a declared dispatch-quantization tolerance; five A/one B consume 5% each. | ALLOC-02/03 |
| T16 | Phase costs differ by profile or phase. Balance estimated/observed proportional consumption; a five-cheap/one-huge count is not sufficient proof of fairness. | ALLOC-03 |
| T17 | A has opaque caching. No invented cache discounts or extra entitlement; expose confidence and reconcile only from observed evidence. | ALLOC-04, D05 |
| T18 | One profile is unavailable or has no eligible demand. Borrow scheduling opportunity within other real limits; rebalance later without terminating healthy work merely to repair the ratio. | ALLOC-02 |
| T19 | Parallel admission and duplicate/cumulative usage events. Reservations prevent oversubscription; settlement and replay do not double-charge. | RES-03, OBS-01 |
| T20 | Lightweight router recommends an ineligible profile or repeatedly invokes itself. Deterministic admission rejects it; routing time/usage remains charged and bounded. | ALLOC-01 |
| T21 | Fresh review agent consumes a valid implementation checkpoint inside the same scope. Continue without automatic full handoff or new authorization. | CONT-01, PHASE-05 |
| T22 | A real product decision is required, then the scheduler restarts. Block remains durable; no redispatch until a current authorized resolution exists. | HOST-05, PHASE-07 |
| T23 | Worker creates a PR then dies before receipt. Recovery discovers the effect and continues/reconciles without creating a duplicate or inventing success. | REC-01, CONT-05 |
| T24 | Stale worker returns after ownership transfer. Reject its receipt and prevent/reconcile unauthorized effects; a generation field without enforcement is insufficient. | HOST-02, PHASE-03 |
| T25 | Linked PR merge closes its issue before evidence publication. Retain a closeout obligation and finish safely; do not clean the only evidence/workspace. | REC-02/03, HOST-06 |
| T26 | Source head, requirements, or policy changes after a receipt/approval. Revalidate applicable evidence; no stale-head merge or stale approval inference. | ID-02, LIFE-01, PHASE-07/10 |
| T27 | Required fresh-context or isolation capability is absent. Use only a declared safe reduced profile or return a blocker; never silently merge roles. | HOST-01 |
| T28 | Same accepted task in interactive and scheduled modes. Material facts, evidence, uncertainty and completion semantics agree; only presentation/transport differs. | CORE-04, PHASE-09 |
| T29 | A phase-duration aggregate regresses due to one run. Find that run, inspect phase/worker/profile contributions, and recompute with/without it with a visible exclusion and denominator. | OBS-02/03 |
| T30 | Run is still executing abnormally and has no completion span. Active age, descendants, retries and reservations make it diagnosable before completion. | OBS-03 |
| T31 | Trace sampling, exporter outage or metric overflow removes detail. Core accounting remains complete within policy; gaps/overflow are visible and required attribution remains available elsewhere. | OBS-01/02/05 |
| T32 | Resume creates a new trace. Correlate through run identity and links without creating a new allowance, run, or handoff solely for tracing. | ID-01, OBS-04 |
| T33 | Sensitive data exists in a prompt, exemplar or propagated attribute. Protected detail is not exposed through an aggregate or public diagnostic route. | OBS-06 |
| T34 | All child issues close but parent outcome acceptance is absent. Report delivery progress, not accepted outcome or release. | LIFE-03/04 |
| T35 | Cancellation, rollback or cleanup encounters user-owned/unmerged work. Preserve it and report recoverability; do not delete for queue convenience. | HOST-06, REC-03 |
| T36 | Malformed receipt, unsupported schema version or missing required verification. Reject scheduled continuation with an actionable reason, not a fabricated success. | PHASE-02/03/10, COMPAT-01 |

### D02 continuation and recovery scenarios

T37–T52 extend the unchanged T01–T36 catalog. They are **planned scenarios, not implemented or passing tests**. Synthetic identifiers only. Each test must assert both permitted behavior and forbidden effects.

| ID | Given / when | Required observable result | Clauses |
| --- | --- | --- | --- |
| T37 | Permitting profile; same item/run and scope; checkpoint plus exact controller-acceptance bindings; old writer quiescent/fenced; fresh review worker. Negative fixture has only a receipt/persisted checkpoint or a producer-supplied acceptance flag, with control continuity intact. | Positive fixture may continue without automatic full handoff, preserving run, authority and budget. Negative fixture inhibits continuation until actual acceptance and all other predicates are verified; it does not invent acceptance or classify a crash solely from the gap. | ID-03, CONT-01; extends T21/T32 |
| T38 | Same checkpoint, but permission is absent, profile is unauthorized for this scope/exchange, or its revision cannot be verified. Include approved context-manifest and runtime-capability-only fixtures. | No checkpoint-only redispatch; record missing authority/policy and satisfy required promotion/escalation. Neither context approval nor declared capability substitutes for explicit applicable profile permission. | CONT-01, PHASE-11 |
| T39 | Worker persists checkpoint then crashes before acceptance is established, including a receipt or producer-supplied acceptance flag without controller validation. | Classify interruption, not controlled yield; reconcile effects and promote full handoff before delivery resume. Do not manufacture or backdate acceptance to reclassify the crash. | CONT-01, CONT-04, CONT-06 |
| T40 | Unexpected controller crash; durable state is fully reconstructable. | Reconstruct state and still fulfill full-handoff promotion; no crash exemption inferred from reconstruction or journal persistence. | CONT-02/04/06 |
| T41 | Valid accepted failure receipt with verified continuity and a permitted bounded retry; alternate fixture has ambiguous termination. | First fixture may retry under remaining allowance without automatic handoff; second enters interruption recovery and promotion. | CONT-01/02/04 |
| T42 | PR creation succeeds but worker receipt is missing. | Discover/reuse existing operation; do not duplicate PR or fabricate worker success. Full handoff records evidence and unresolved verification. | CONT-04, CONT-05; extends T23 |
| T43 | Old lease expires while prior worker may still mutate. | No replacement mutation until effective fencing or verified stop/isolation; generation increment alone fails. | CONT-01, CONT-06; extends T24 |
| T44 | Full packet save succeeds but required coordination linkage fails; then controller restarts. | Promotion remains pending; reconcile same obligation/artifact, repair link and read back; no duplicate packet/effect or premature redispatch. | CONT-06 |
| T45 | Receipt says no handoff, but an authority decision, confidentiality change, external wait or closeout boundary is observed. | Controller overrides advisory flag; persist required full handoff and route to proper owner without broadening authority. | CONT-02, PHASE-11 |
| T46 | Claim, authorization, requirement, head or policy revision changes after checkpoint acceptance but before dispatch; or supplied acceptance binds another checkpoint revision, phase/attempt or next action. | Reject stale or mismatched acceptance as sufficient; revalidate affected evidence, reclassify boundary and inhibit action until satisfied. No acceptance copied across attempts or revisions. | CONT-01, CONT-06; extends T26 |
| T47 | Retry/recovery with depleted or uncertain remaining allowance, including outstanding descendant reservations. | Preserve cumulative ledger; no reset or zero assumption; only already-authorized safe recovery within allowance, otherwise durable stop/escalation. | CONT-04, CONT-05; extends T08/T10/T19 |
| T48 | One authorized scope decomposes into independently resumable delivery items. | Separate item/run/claim identities mapped to shared outcome/work scope and inherited budget; parent acceptance remains separate. | ID-03; extends T01/T34 |
| T49 | Legacy adapter presents aggregate coordination-run ID as item `run_id`, or a consumer cannot validate required mapping. | Fail closed pending explicit versioned mapping; no silent rename, new authorization or budget. | ID-03, PHASE-11 |
| T50 | Duplicate checkpoint acknowledgement or promotion-completion message is replayed. | Idempotent acceptance/publication; no duplicate dispatch, effect, charge or evidence loss; new effects still require their own identity/accounting. | CONT-01, CONT-06, CONT-05 |
| T51 | Equivalent standalone and scheduled interruption/continuation fixtures. | Same identity, authority, evidence, promotion and completion decisions; scheduled malformed/missing records rejected, readable standalone output grants no exception. | PHASE-11; extends T28/T36 |
| T52 | Cleanup requested during unresolved recovery or unverified handoff linkage. | Preserve the only recoverable evidence/unmerged work; cleanup cannot erase the promotion obligation or manufacture completion. | CONT-06; extends T25/T35 |

Fixture evidence should include immutable owner revisions, distinct receipt/checkpoint/controller-acceptance provenance and exact acceptance bindings, applicable permitting-profile authority, operation identity, effect disposition, claim/fencing proof, cumulative accounting, persisted packet/link readbacks and dispatch decisions. A trace alone is not the enforcement or promotion ledger.

## 5. Initial measurable acceptance

The first live vertical slice must demonstrate a bounded happy path and recovery from at least decision suspension, process failure after mutation, provider-capacity interruption, and early ticket closure. It must provide evidence that no separately billed inference path was used. Configuration inspection alone is not evidence of all live behavior; capture supported authentication/metering observations and state their limits.

Report interventions as necessary authority decisions versus avoidable operational handoffs. Report completion, failed verification, unresolved effects, and unknown measurements separately. Numeric quality thresholds, allocation tolerances and retention periods must be agreed before claiming a pass; this proposal does not invent them.

## 6. Review and adoption checklist

- Confirm that C01-C10 reflect the intended design; do not reopen them merely to select an implementation library.
- Resolve only the D-records blocking the next bounded work package, with explicit approval evidence.
- Reconcile existing standards where a proposed clarification changes handoff or authority semantics.
- Implement and run applicable negative and recovery fixtures before unattended live mutation.
- Keep public contracts generic and private deployment policy protected.
- Record conformance against exact versions and preserve known limitations.
- Promote portable specifications into ai-sdlc with one maintained source, leaving this proposal superseded or linked by version.

Review completion is not implementation completion, and merging a Proposed document does not by itself approve production operation.
