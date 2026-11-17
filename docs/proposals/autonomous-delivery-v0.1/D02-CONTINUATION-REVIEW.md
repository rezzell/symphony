# D02 review: continuation, interruption, and full-handoff promotion

Status: **Proposed — not adopted**  
Review revision: **D02-r1**  
Prepared: **2026-11-16**  
Proposal comparison baseline: **093a8ac653a720047f1829ffaa5eb3f5c62c7f08**  
ai-sdlc comparison baseline: **c993fa3a642cfcb95afa05f7d0a4749569872549**

## 1. Bounded review and recommendation

Review D02 first. Preserve confirmed C01-C10 in [the decision register](DECISIONS-AND-CONFORMANCE.md). This document is a proposed amendment to that same proposal, not another normative specification. The replacement/addition text below is not effective until explicitly adopted for an identified version and scope. D02 remains open; creating or merging this document does not approve governance, implementation, or a deployment profile.

**Proposed recommendation:** permit controlled continuation only after a positively validated checkpoint and an explicitly permitting applicable profile. Treat unexpected interruption as recovery followed by full-handoff promotion before delivery redispatch. Reconstructable evidence does not waive that promotion. A full handoff need not require fresh human permission when the next action is already authorized; persistence is not approval.

Reconcile outcome/work-scope correlation with delivery-item run identity explicitly. Keep receipts, checkpoints, recovery observations, full handoffs, claims, and diagnostic traces distinct. None creates authority or replenishes an allowance.

Not selected here: wire encoding or schema technology (D01), shutdown reserve/grace values (D06), storage engine or fencing implementation (D07), implementation language (D08), provider selection, allocation algorithms, or release/acceptance policy. No runtime implementation is supplied.

## 2. Evidence and compatibility problem

**Observed:** [SPEC.md](SPEC.md) distinguishes receipts, checkpoints, and full handoffs, but its `run_id` description is broad and CONT-02 names an unreconstructable interruption while CONT-04 leaves reconstructable-crash handling open. [PHASE-CONTRACTS.md](PHASE-CONTRACTS.md) includes a worker-reported handoff flag but requires controller validation.

**Observed:** the pinned ai-sdlc [terminology](https://github.com/rezzell/ai-sdlc/blob/c993fa3a642cfcb95afa05f7d0a4749569872549/skills/sdlc-deliver/references/terminology.md) and [delivery-run contract](https://github.com/rezzell/ai-sdlc/blob/c993fa3a642cfcb95afa05f7d0a4749569872549/skills/sdlc-deliver/references/delivery-run-contract.md) bind a delivery run to one lead-claim/workboard-item, outcome, requirement, project, confidentiality boundary, and resumability context. Runtime or review-cycle changes alone do not create a run. Their full-handoff definition includes interruption.

**Derived:** broader coordination correlation and item-scoped delivery execution cannot safely share an unqualified identifier with different meanings. Reconstruction, fencing, and handoff policy are independent predicates; successful reconstruction does not prove that either of the other two is satisfied.

Public mechanisms must work without access to private policy documents. A deployment supplies an access-controlled policy reference/revision and the required resolved constraints. This review neither republishes private policy nor changes any external standard.

## 3. Proposed identity amendments

### D02-ID1 — refine SPEC identity table and add ID-03

Retain existing identifiers; clarify their domains and add a work-scope reference:

| Field | Proposed meaning |
| --- | --- |
| `authorization_ref` | Owner-system grant and revision defining permitted actions and restrictions. Not an outcome, run, or claim. |
| `outcome_ref` | Intended authorized outcome and its acceptance boundary. A reference does not assert that the final outcome has been accepted. |
| `work_scope_ref` | Owner and revision of the bounded authorized work scope. It may contain multiple delivery items; it is a reference to scope, not a new authority object. |
| `delivery_item_ref` | One independently governed unit of delivery inside that work scope. |
| `run_id` | One logical delivery-item run, with the item's governing requirement, project, confidentiality and resumability bindings. It is not the identifier of an aggregate outcome or provider session. |
| `claim_ref` and generation | Current ownership evidence within a claim lineage. A generation is not proof that an older actor has stopped or been fenced. |
| `budget_scope_id` | Existing shared allowance ledger scope. Item/run/attempt creation cannot reset it. |
| `phase_instance_id`, `attempt_id` | Phase execution identity and a particular attempt. A possibly repeating invocation receives a new attempt ID and predecessor link. |
| diagnostic references | Trace, session and worker identifiers; correlated to, not substituted for, the identities above. |

**Proposed ID-03:** adapters MUST declare whether each incoming identifier names an outcome/work scope, delivery item, item run, claim, or diagnostic record. An external system's broader "SDLC run" MUST be preserved as a namespaced correlation reference to the work scope and mapped explicitly to its item-run IDs. It MUST NOT be silently aliased to the portable `run_id`. Ambiguous existing records are quarantined for reconciliation; they are not bulk-renamed or assigned fresh budgets.

A controlled runtime exchange preserves the item run and its identity bindings. A new attempt, trace, or valid claim generation alone does not establish a new run. An independently resumable child delivery gets its own item/run/claim under the inherited grant, scope constraints, and shared budget. A change to an identity boundary requires explicit reconciliation and any required full handoff; it is never an implicit continuation.

Compatibility: mapping the existing ai-sdlc item-scoped `run_id` can be lossless. Adding `work_scope_ref` and interpreting legacy broader run records requires an explicit contract/adapter version; older consumers that cannot validate the required mapping fail closed. This review does not rename fields in ai-sdlc or amend external terminology.

## 4. Proposed continuity clauses

### D02-C1 — replace CONT-01: controlled continuation eligibility

A controller MAY continue from an intra-run checkpoint only when **all** of the following are verified:

1. The applicable, revision-bound lifecycle/adoption profile explicitly permits that exchange; no required source is missing, ambiguous, or stale.
2. Authorization, governing requirement, outcome/work scope, delivery item, project, confidentiality and resumability bindings remain compatible with the authorized next action.
3. There is no full-handoff trigger, new authority/decision boundary, independent coordination transfer, external Waiting/Blocked state, budget exhaustion, or closeout boundary.
4. A checkpoint contains immutable input/output references, verification posture, outstanding findings, effect disposition, cumulative accounting references, current ownership evidence, and one bounded next action.
5. The designated receiver/controller has durably recorded and acknowledged acceptance of that checkpoint. The old actor cannot make conflicting mutations: it is verified quiescent or effectively fenced. Publication alone is not acceptance; a timeout or lease expiry is not fencing.
6. Before dispatch, the controller revalidates owner-system revisions, claim generation, policy/authorization and remaining allowances. Unknown side effects or unresolved accounting cannot be treated as zero or absent.

Checkpoint storage, a fresh worker, context compaction, or a new trace alone neither requires nor exempts a full handoff. A checkpoint is a validated receipt; it is not a permission grant or independent delivery backlog entry.

### D02-C2 — amend CONT-02: mandatory promotion boundaries

Preserve CONT-02's authority, scope, confidentiality, independent-transfer, Waiting/Blocked, exhausted-budget and closeout triggers. Replace the interruption qualifier with **unexpected interruption, whether reconstructable or not**. Applicable policy may require additional boundaries. This amendment proposes no checkpoint-only crash exemption.

A normal completed phase reporting `failed` through a valid, accepted receipt is not automatically an interruption. A bounded retry may remain intra-run only if the controller retains verified continuity and every other eligibility condition holds. Failure classification cannot hide lost control, uncertain effects or a policy-required wait.

### D02-C3 — replace CONT-04: classify from evidence, not desired outcome

A **controlled continuation** is an intentional, profile-permitted exchange whose checkpoint acceptance and safe ownership transition are verified. Required control continuity is maintained through the exchange.

An **unexpected interruption** is a loss of required worker/controller continuity, ambiguous termination, or failure before that controlled exchange is established. A worker writing a checkpoint and disappearing before acceptance is an interruption. Reconstructing its last known state later does not retroactively convert the event into a controlled exchange.

On interruption, enter recovery; suppress ordinary delivery redispatch. A recovery actor records its own observations and their sources, not a receipt purportedly emitted by the interrupted worker. Reconcile consequential effects, outstanding descendants/reservations, ownership and accounting. Persist a full handoff with unresolved facts explicitly marked. Resolution may yield an authorized continuation, Waiting/Blocked, cancellation or escalation; it never fabricates phase success.

Recovery and handoff preparation may perform already-authorized bounded inspection, stopping/isolation and continuity writes. They may not repeat a potentially completed consequential operation merely to obtain a cleaner result. When safe inspection itself lacks authority or remaining allowance, persist the known state and escalate rather than spend or mutate beyond the grant. D06 must define any shutdown/reserve mechanism separately; none is invented here.

### D02-C4 — add CONT-06: promotion completion and restart

Give each promotion obligation a stable identity bound to its item run, trigger/event and predecessor evidence. The controller must:

1. Record the obligation and preserve recoverable evidence before cleanup.
2. Persist the full packet in the configured durable coordination store, with artifact ID/version, source revisions, goal/scope/state, decisions, effects and uncertainty, verification, cumulative consumption, claim disposition, and one next action plus resume predicate.
3. Link the required coordination record(s) to the persisted artifact, then read back the artifact and links. An internal host journal alone does not satisfy a required external coordination-store binding.
4. Mark promotion complete only after required persistence/link verification. Reconcile an ambiguous write by stable identity before retrying; reuse the artifact/obligation rather than duplicating handoffs or effects. Amend uncertainty with versioned provenance, not silent replacement.
5. Redispatch delivery only after promotion, required reconciliation, current owner-system triggers, effective ownership/fencing, authorization and remaining budgets are all satisfied.

If the artifact saves but its coordination link fails, promotion remains pending and delivery stays inhibited. If a source is unavailable, unknown remains unknown; the record describes the gap and the safe next action. A full handoff does not itself release/transfer a claim: the recorded disposition must match verified owner state. Separate obligations may be discharged by one packet only when every affected trigger and required linkage is explicitly covered.

This specifies observable guarantees, not a cross-system transaction implementation. D07 must provide equivalent guarantees for its chosen storage and fencing mechanisms.

### D02-C5 — retain CONT-05 and clarify budget and identity continuity

Missing receipts do not establish absence of effects. Recover a created PR or other operation using owner-system evidence and operation identity before considering replay. Report what is observed, what is derived and what remains indeterminate.

Controlled continuation, recovery, handoff publication, re-review and new attempts consume existing allowances. Cumulative consumption and outstanding reservations survive restart; uncertainty is reconciled or conservatively inhibits admission. A full handoff does not automatically create a new item run, and a new item run does not create a new authorization or budget. Elapsed execution, worker-time, waiting and provider usage remain separate measures under the applicable resource profile.

## 5. Proposed phase-contract changes

Amend PHASE-05 and PHASE-08 to require D02-C1's accepted checkpoint predicate and D02-C3's interruption recovery. Add the following conceptual fields/constraints to the future versioned contract. These are requirements for D01, not a finished wire schema:

| Record | Required D02 additions or clarified evidence |
| --- | --- |
| Dispatch/checkpoint | Explicit work-scope/item-run mapping; policy and authorization revisions; checkpoint acceptance reference; ownership safety evidence; ledger/reservation references; bounded next action. |
| Worker receipt | Existing `full_handoff_required` remains advisory evidence. The controller independently evaluates triggers; a false flag cannot suppress a required handoff. |
| Recovery observation | `record_kind` distinct from worker receipt; observer identity; interrupted run/phase/attempt; failure/event identity; last verified checkpoint; actual observation time; observed/unknown effects; ownership and accounting reconciliation; next safe action. No invented worker completion timestamp or success. |
| Promotion obligation | Stable obligation ID; trigger(s); run/scope identities; policy revision; packet artifact/version; required coordination targets; persistence/link verification; claim disposition; resume predicates. |

**Proposed PHASE-11:** reject continuation when required identity mapping, checkpoint acceptance, effect disposition, ownership safety, accounting, policy or promotion evidence cannot be validated. Scheduled exchanges require validated structured records. Standalone operation applies the same predicates and may additionally present a human-readable explanation. A human-readable reconstruction does not substitute for an absent required structured record.

Retain existing receipt statuses; do not disguise a recovery observation as `succeeded`. A recovery observation may establish that a particular side effect happened without proving phase completion, review, integration, closeout, release or outcome acceptance.

## 6. Normal and failure examples

**Normal controlled exchange:** implementation produces a receipt for immutable revision `rev-A`; the controller validates it, records checkpoint acceptance and verifies the writer is stopped/fenced. The same item run enters an already-authorized review phase under its permitting profile. A fresh worker/trace is diagnostic change only. Review consumption is charged to the existing scope ledger.

**Crash after a consequential write:** a worker creates a PR and dies before returning its receipt. Recovery finds the PR using operation/owner-system evidence. It does not create a second PR or invent a successful worker receipt. A full handoff is persisted and linked even if all effects can be reconstructed. Resumption checks current PR head, authority, claim, policy and budget.

**Promotion interruption:** a full packet exists, but the controller fails before updating its coordination pointer. The next recovery reconciles the promotion ID, verifies the existing packet and finishes the missing linkage. It does not create a new budget, a second delivery item or an unrelated duplicate packet. Delivery remains inhibited until the obligation and ownership checks are satisfied.

## 7. Proposed conformance additions

These extend the existing T01-T36 catalog. All are **planned scenarios, not implemented or passing tests**. Synthetic identifiers only. Each test must assert both permitted behavior and forbidden effects.

| ID | Given / when | Required observable result | Clauses |
| --- | --- | --- | --- |
| T37 | Permitting profile; same item/run and scope; accepted checkpoint; old writer quiescent/fenced; fresh review worker. | Continue without automatic full handoff; preserve run, authority and budget; create the appropriate phase/attempt and diagnostic links. | D02-ID1, C1; extends T21/T32 |
| T38 | Same checkpoint, but permission is absent, profile is unapproved for use, or its revision cannot be verified. | No checkpoint-only redispatch; record missing authority/policy and satisfy required promotion/escalation. No inferred permission. | C1, PHASE-11 |
| T39 | Worker persists checkpoint then crashes before acceptance is established. | Classify interruption, not controlled yield; reconcile effects and promote full handoff before delivery resume. | C3, C4 |
| T40 | Unexpected controller crash; durable state is fully reconstructable. | Reconstruct state and still fulfill full-handoff promotion; no crash exemption inferred from reconstruction or journal persistence. | C2-C4 |
| T41 | Valid accepted failure receipt with verified continuity and a permitted bounded retry; alternate fixture has ambiguous termination. | First fixture may retry under remaining allowance without automatic handoff; second enters interruption recovery and promotion. | C1-C3 |
| T42 | PR creation succeeds but worker receipt is missing. | Discover/reuse existing operation; do not duplicate PR or fabricate worker success. Full handoff records evidence and unresolved verification. | C3, C5; extends T23 |
| T43 | Old lease expires while prior worker may still mutate. | No replacement mutation until effective fencing or verified stop/isolation; generation increment alone fails. | C1, C4; extends T24 |
| T44 | Full packet save succeeds but required coordination linkage fails; then controller restarts. | Promotion remains pending; reconcile same obligation/artifact, repair link and read back; no duplicate packet/effect or premature redispatch. | C4 |
| T45 | Receipt says no handoff, but an authority decision, confidentiality change, external wait or closeout boundary is observed. | Controller overrides advisory flag; persist required full handoff and route to proper owner without broadening authority. | C2, PHASE-11 |
| T46 | Claim, authorization, requirement, head or policy revision changes after checkpoint acceptance but before dispatch. | Reject stale acceptance as sufficient; revalidate affected evidence, reclassify boundary and inhibit action until satisfied. | C1, C4; extends T26 |
| T47 | Retry/recovery with depleted or uncertain remaining allowance, including outstanding descendant reservations. | Preserve cumulative ledger; no reset or zero assumption; only already-authorized safe recovery within allowance, otherwise durable stop/escalation. | C3, C5; extends T08/T10/T19 |
| T48 | One authorized scope decomposes into independently resumable delivery items. | Separate item/run/claim identities mapped to shared outcome/work scope and inherited budget; parent acceptance remains separate. | D02-ID1; extends T01/T34 |
| T49 | Legacy adapter presents aggregate coordination-run ID as item `run_id`, or a consumer cannot validate required mapping. | Fail closed pending explicit versioned mapping; no silent rename, new authorization or budget. | D02-ID1, PHASE-11 |
| T50 | Duplicate checkpoint acknowledgement or promotion-completion message is replayed. | Idempotent acceptance/publication; no duplicate dispatch, effect, charge or evidence loss; new effects still require their own identity/accounting. | C1, C4, C5 |
| T51 | Equivalent standalone and scheduled interruption/continuation fixtures. | Same identity, authority, evidence, promotion and completion decisions; scheduled malformed/missing records rejected, readable standalone output grants no exception. | PHASE-11; extends T28/T36 |
| T52 | Cleanup requested during unresolved recovery or unverified handoff linkage. | Preserve the only recoverable evidence/unmerged work; cleanup cannot erase the promotion obligation or manufacture completion. | C4; extends T25/T35 |

Fixture evidence should include immutable owner revisions, checkpoint acceptance, operation identity, effect disposition, claim/fencing proof, cumulative accounting, persisted packet/link readbacks and dispatch decisions. A trace alone is not the enforcement or promotion ledger.

## 8. Review disposition and consolidation

The bounded decision is whether to adopt this strict distinction, the explicit identity mapping and the promotion-completion predicates. External governance interpretation or material standard changes require review by their accountable owner; this public proposal cannot supply that approval.

If adopted for a specified revision, consolidate D02-ID1/C1-C5 into SPEC, the phase additions into PHASE-CONTRACTS, and T37-T52 into DECISIONS-AND-CONFORMANCE. Retain this file as the superseded review rationale with an adoption reference, not as a second independently edited contract. Until then, base proposal clauses and this amendment are both Proposed; deployments remain governed by their currently approved requirements.

No C01-C10 input is changed. No private source is a prerequisite for interpreting or testing the public mechanisms. No upstream root SPEC, runtime, workflow, license/notice file, ai-sdlc implementation, or approved company standard is modified by this review.
