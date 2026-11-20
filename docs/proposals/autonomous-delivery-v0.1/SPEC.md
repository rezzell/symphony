# Portable autonomous delivery specification

Status: **Proposed**  
Version: **0.1**  
Parent: [Proposal overview](README.md)

## 1. Contract scope

This specification defines authorized work, evidence-producing phases, continuity, recovery, and verified completion. It is independent of scheduler, provider, implementation language, task tracker, and user interface.

`MUST`, `MUST NOT`, `SHOULD`, and `MAY` describe intended conformance requirements if adopted. They do not change the proposal's approval status. Requirement identifiers are stable review and test references.

The scheduler's polling algorithm, database engine, dashboard, and subprocess protocol are outside the portable delivery specification. [Resources and telemetry](RESOURCES-AND-TELEMETRY.md) defines accounting outcomes; a host chooses an implementation that can enforce them. The Symphony baseline is a reference architecture, not an exclusive dependency. Sources: [S1, S3, S4](README.md#sources).

## 2. Responsibility split

**CORE-01:** A deployment MUST identify the owner of knowledge/decisions, delivery state, coordination, repository state, and source review. Physical co-location does not merge authority. A GitHub-hosted coordination reference does not become an acceptance decision merely because it shares an issue with delivery evidence.

**CORE-02:** The delivery controller owns permitted lifecycle transitions and evidence validation. The host owns admission, process supervision, isolation, reservations, deadlines, cancellation, and recovery mechanics. A skill may implement the controller; a deterministic controller may replace its mechanical parts while preserving the contracts.

**CORE-03:** There MUST be one effective scheduling authority for a dispatch scope. Portfolio reasoning may determine eligible bounded work, but MUST NOT independently launch duplicates alongside the host. A derived dependency graph is reconstructable coordination data, not a new editable backlog.

**CORE-04:** The same delivery semantics MUST apply in interactive and scheduled modes. Interactive mode may render concise human-readable results without scheduler envelopes; scheduled mode requires validated envelopes. Missing evidence or runtime capabilities MUST be visible in either mode. Standalone usability does not imply unattended enforcement capabilities that the interactive runtime lacks.

The first host integration is proposed to dispatch one delivery run. Internal phases execute through fresh bounded contexts when their contracts require them. External phase-by-phase scheduling is an optional later capability, not a condition of initial compatibility.

## 3. Authorization and mechanical decomposition

An authorization binds an outcome, accepted scope, acceptance boundary, constraints, delegated operations, budget scope, applicable policy revisions, and accountable approval reference.

**AUTH-01:** Approval follows the authorized outcome, not its current issue arrangement. Mechanical decomposition, issue creation, sequencing, dependency management, and plan revision MAY proceed without renewed approval when they remain within that authorization.

**AUTH-02:** A child inherits all applicable restrictions and may narrow them. It MUST NOT enlarge scope, acceptance, confidentiality access, delegated authority, or allowance. Specific applicable restrictions override general permissions; an explicit requirement to use one issue prohibits creating extra delivery issues. Internal implementation units remain possible only when consistent with the restriction's intent.

**AUTH-03:** Creating an issue does not itself create new authority or require human approval. Independently owned or resumable child work may require durable delegation records and separate run/claim identities. Conjoined technical units that share an acceptance boundary need not become new issues.

**AUTH-04:** Mechanical changes MUST preserve traceability to the governing outcome, acceptance criteria, authorization, and aggregate budget. Planning, routing, decomposition, and rework consume that budget. Creating children, retiring items, or changing runtime MUST NOT mint fresh allowance.

**AUTH-05:** An actor MUST stop at a material boundary outside its authority and request the smallest resolvable decision. A routing model, scheduler, checkpoint, or receipt cannot approve that decision. Explicit constraints apply only within their issuer's authority; a more specific instruction cannot override a higher-authority prohibition. Unresolved conflicts are surfaced rather than resolved in favor of greater autonomy.

A deployment's actual approval matrix remains policy data. This generic contract does not publish or infer an organization's reserved decisions.

## 4. Identity model

| Identity | Meaning |
| --- | --- |
| `authorization_ref` | Owner-system grant and revision defining permitted actions and restrictions. Not an outcome, run, or claim. |
| `outcome_ref` | Intended authorized outcome and its acceptance boundary. A reference does not assert that the final outcome has been accepted. |
| `work_scope_ref` | Owner and revision of the bounded authorized work scope. It may contain multiple delivery items; it is a reference to scope, not a new authority object. |
| `delivery_item_ref` | One independently governed unit of delivery inside that work scope. |
| `run_id` | One logical delivery-item run, with the item's governing requirement, project, confidentiality and resumability bindings. It is not the identifier of an aggregate outcome or provider session. |
| `claim_ref` and generation | Current ownership evidence within a claim lineage. A generation is not proof that an older actor has stopped or been fenced. |
| `budget_scope_id` | Existing shared allowance ledger scope. Item/run/attempt creation cannot reset it. |
| `phase_instance_id`, `attempt_id` | Phase execution identity and a particular attempt. A possibly repeating invocation receives a new attempt ID and predecessor link. |
| `worker_id` | A distinct executing worker, including coordinators and descendants. |
| `checkpoint_ref` | Immutable continuation evidence; availability alone is not controller acceptance. |
| diagnostic references | Trace, session and worker identifiers; correlated to, not substituted for, the identities above. |

**ID-01:** Runtime, model, provider, session, worktree, and trace changes MUST NOT reset a run or budget by themselves. Independent child deliveries may have separate runs under the same authorization and budget scope. New run IDs are not new allowances.

**ID-02:** References MUST include an owner, stable locator, revision or equivalent freshness evidence, and access classification where required. One global repository SHA is insufficient to version decisions, issue state, and code simultaneously. Mutable owner facts MUST be revalidated before consequential action.

**ID-03:** Adapters MUST declare whether each incoming identifier names an outcome/work scope, delivery item, item run, claim, or diagnostic record. An external system's broader "SDLC run" MUST be preserved as a namespaced correlation reference to the work scope and mapped explicitly to its item-run IDs. It MUST NOT be silently aliased to the portable `run_id`. Ambiguous existing records are quarantined for reconciliation; they are not bulk-renamed or assigned fresh budgets.

A controlled runtime exchange preserves the item run and its identity bindings. A new attempt, trace, or valid claim generation alone does not establish a new run. An independently resumable child delivery gets its own item/run/claim under the inherited grant, scope constraints, and shared budget. A change to an identity boundary requires explicit reconciliation and any required full handoff; it is never an implicit continuation.

Compatibility: mapping the existing ai-sdlc item-scoped `run_id` can be lossless. Adding `work_scope_ref` and interpreting legacy broader run records requires an explicit contract/adapter version; older consumers that cannot validate the required mapping fail closed. This contract does not rename fields in ai-sdlc or amend external terminology.

## 5. Lifecycle and completion

The logical path is intake -> planning/specification -> implementation -> verification/integration -> review/remediation -> merge or configured integration -> closeout. Profiles may compose or omit phases only when their postconditions are satisfied by equivalent evidence. They MUST NOT omit approval, verification, or closeout obligations merely by changing phase names.

**LIFE-01:** A phase result recommends a next action. The controller validates the result against its dispatch, contract, policy, and current owner-system evidence before selecting a transition. Schema validity is not proof of correctness.

**LIFE-02:** Operational states MUST distinguish ready, executing, recovering, waiting on an external condition, blocked on an authority/capability conflict, closeout pending, completed, and cancelled/incomplete. These are logical meanings; adapters map them to existing owner states without establishing another authoritative task store.

**LIFE-03:** Implementation complete, review complete, checks passing, approval, merge readiness, merge, delivery closeout, outcome acceptance, release, and deployment are distinct facts. The proposed v0.1 baseline ends at verified integration plus required durable closeout. Release and deployment require separately declared contracts and authority.

**LIFE-04:** A run MUST NOT report completed until required integration evidence, ticket updates, coordination closeout, and final owner-system verification are durable. Closing all child issues alone does not accept the parent outcome.

## 6. Continuity and promotion

| Artifact | Purpose | Authority |
| --- | --- | --- |
| Stage receipt | Bounded result against a particular dispatch and revisions. | Evidence only. |
| Intra-run checkpoint | Continuation evidence within the same item run and authorized scope; carries or references a stage receipt. | Evidence only; no new coordination obligation by persistence alone. |
| Controller acceptance record | Positive controller validation of the exact checkpoint for one bounded next transition; distinct provenance even when co-located. | Evidence only; not profile permission or ownership transfer. |
| Recovery observation | Observer-attributed reconstruction after interruption; never a fabricated worker receipt. | Evidence only; not phase success. |
| Full coordination handoff | Meaningful transfer, external wait, escalation, or durable closeout. | Reports authority and claim disposition; does not grant authority. |
| Runtime event | Process, usage, routing, or diagnostic observation. | No product or delivery authority. |

**CONT-01:** A controller MAY continue from an intra-run checkpoint only when **all** of the following are verified:

1. The applicable, revision-bound lifecycle/adoption profile is authorized for use in this scope and explicitly permits this exchange; no required source is missing, ambiguous, or stale. An approved bootstrap/context manifest, context-selection profile, or runtime capability declaration does not supply that permission by itself.
2. Authorization, governing requirement, outcome/work scope, delivery item, project, confidentiality and resumability bindings remain compatible with the authorized next action.
3. There is no full-handoff trigger, new authority/decision boundary, independent coordination transfer, external Waiting/Blocked state, budget exhaustion, or closeout boundary.
4. A checkpoint contains immutable input/output references, verification posture, outstanding findings, effect disposition, cumulative accounting references, current ownership evidence, and one bounded next action.
5. The designated receiver/controller has durably recorded and acknowledged positive acceptance of that exact checkpoint for the bounded next transition, with the acceptance evidence described below. The old actor cannot make conflicting mutations: it is verified quiescent or effectively fenced. Publication or a producer-supplied acceptance flag alone is not controller acceptance; a timeout or lease expiry is not fencing.
6. Before dispatch, the controller revalidates owner-system revisions, claim generation, policy/authorization and remaining allowances. Unknown side effects or unresolved accounting cannot be treated as zero or absent.

Checkpoint storage, a fresh worker, context compaction, or a new trace alone neither requires nor exempts a full handoff. A checkpoint is a continuation-oriented stage receipt, not proof of its own acceptance, a permission grant, or an independent delivery backlog entry.

**Evidence roles:** distinguish the worker's reported result, the checkpoint's continuation state, and the controller's acceptance of that checkpoint. These roles may share a versioned artifact, but their identities, provenance and validation evidence MUST remain distinguishable; this does not require separate files, services or a new storage technology.

| Evidence role | Meaning and limit |
| --- | --- |
| Worker receipt | Producer-attributed result of the dispatched phase/attempt, bound to immutable inputs and outputs. Its status and handoff flag do not approve a transition. |
| Intra-run checkpoint | Bounded continuation evidence that carries or references the stage receipt plus the state required by CONT-01. Persistence makes it available for validation, not automatically accepted. |
| Controller acceptance record | Designated receiver/controller's positive validation of an exact checkpoint for a bounded next transition. It records evidence checks; it does not create policy permission, transfer ownership, or eliminate pre-dispatch revalidation. |

The acceptance record MUST bind the checkpoint identity/revision, referenced receipt and input/output revisions, item/run/phase/attempt identity, claim generation, validator identity and actual validation time, applicable contract/authorization/profile revisions, effect/ownership/accounting evidence checked, and the bounded next action. A generic acknowledgement of receipt, an acceptance for another revision or attempt, or the producer's unvalidated assertion cannot satisfy this predicate. A single implementation may perform producer and controller roles only while preserving their distinct provenance and the required controller checks.

An **accepted checkpoint** means that the exact checkpoint and its positive controller-acceptance evidence can both be verified. It is not a fourth, independently authoritative object. Acceptance is necessary but not sufficient for dispatch: all six conditions above, including current ownership safety and remaining allowance, still apply. Missing acceptance inhibits continuation; it is not by itself evidence of a crash. Loss of required continuity is classified separately under CONT-04.

**CONT-02:** Full handoffs MUST be emitted when the applicable policy requires promotion: a decision/authority boundary, changed scope or confidentiality, independent coordination transfer, external Waiting/Blocked work, exhausted budget, unexpected interruption whether reconstructable or not, or durable closeout. Applicable policy may require additional boundaries. No checkpoint-only crash exemption is supplied. Human approval is required only when the boundary calls for it; automatic resumption from an external wait may still be authorized.

A normal completed phase reporting `failed` through a valid, accepted receipt is not automatically an interruption. A bounded retry may remain intra-run only if the controller retains verified continuity and every other eligibility condition holds. Failure classification cannot hide lost control, uncertain effects or a policy-required wait.

**CONT-03:** A handoff MUST identify goal, scope, current state, classified conclusions, constraints, changed artifacts, verification performed and omitted, questions, risks, owner references, claim disposition, and one executable next action or resumption predicate. A full transcript is not the default transfer format.

**CONT-04:** A **controlled continuation** is an intentional, profile-permitted exchange whose checkpoint acceptance and safe ownership transition are verified. Required control continuity is maintained through the exchange.

An **unexpected interruption** is a loss of required worker/controller continuity, ambiguous termination, or failure before that controlled exchange is established. A worker writing a checkpoint and disappearing before acceptance is an interruption. Reconstructing its last known state later does not retroactively convert the event into a controlled exchange.

On interruption, enter recovery; suppress ordinary delivery redispatch. A recovery actor records its own observations and their sources, not a receipt purportedly emitted by the interrupted worker. Reconcile consequential effects, outstanding descendants/reservations, ownership and accounting. Persist a full handoff with unresolved facts explicitly marked. Resolution may yield an authorized continuation, Waiting/Blocked, cancellation or escalation; it never fabricates phase success.

Recovery and handoff preparation may perform already-authorized bounded inspection, stopping/isolation and continuity writes. They may not repeat a potentially completed consequential operation merely to obtain a cleaner result. When safe inspection itself lacks authority or remaining allowance, persist the known state and escalate rather than spend or mutate beyond the grant. D06 must define any shutdown/reserve mechanism separately; none is invented here.

**CONT-05:** Provider switching after possible mutation requires a verified checkpoint or side-effect reconciliation. The system MUST NOT assume that silence or a missing receipt means no action occurred.

Missing receipts do not establish absence of effects. Recover a created PR or other operation using owner-system evidence and operation identity before considering replay. Report what is observed, what is derived and what remains indeterminate.

Controlled continuation, recovery, handoff publication, re-review and new attempts consume existing allowances. Cumulative consumption and outstanding reservations survive restart; uncertainty is reconciled or conservatively inhibits admission. A full handoff does not automatically create a new item run, and a new item run does not create a new authorization or budget. Elapsed execution, worker-time, waiting and provider usage remain separate measures under the applicable resource profile.

**CONT-06:** Give each promotion obligation a stable identity bound to its item run, trigger/event and predecessor evidence. The controller must:

1. Record the obligation and preserve recoverable evidence before cleanup.
2. Persist the full packet in the configured durable coordination store, with artifact ID/version, source revisions, goal/scope/state, decisions, effects and uncertainty, verification, cumulative consumption, claim disposition, and one next action plus resume predicate.
3. Link the required coordination record(s) to the persisted artifact, then read back the artifact and links. An internal host journal alone does not satisfy a required external coordination-store binding.
4. Mark promotion complete only after required persistence/link verification. Reconcile an ambiguous write by stable identity before retrying; reuse the artifact/obligation rather than duplicating handoffs or effects. Amend uncertainty with versioned provenance, not silent replacement.
5. Redispatch delivery only after promotion, required reconciliation, current owner-system triggers, effective ownership/fencing, authorization and remaining budgets are all satisfied.

If the artifact saves but its coordination link fails, promotion remains pending and delivery stays inhibited. If a source is unavailable, unknown remains unknown; the record describes the gap and the safe next action. A full handoff does not itself release/transfer a claim: the recorded disposition must match verified owner state. Separate obligations may be discharged by one packet only when every affected trigger and required linkage is explicitly covered.

This specifies observable guarantees, not a cross-system transaction implementation. D07 must provide equivalent guarantees for its chosen storage and fencing mechanisms.

## 7. Host capability contract

A host claiming scheduled-delivery compatibility MUST supply or explicitly bind the following capabilities. API shape and wire transport remain proposed details; required behavior is not synonymous with a specific function name.

| Capability | Required behavior |
| --- | --- |
| Negotiate | Declare version, provider, tool, isolation, receipt, accounting, and cancellation capabilities before dispatch. |
| Admit | Revalidate authorization, owner state, claim, eligibility, resource reservations, and required capabilities. |
| Execute | Dispatch a bounded objective with minimal authoritative pointers, pinned contract/policy versions, and explicit mutations. |
| Inspect | Report active workers, progress evidence, budgets, and pending conditions without claiming completion from process status. |
| Receive | Correlate, deduplicate, validate, and durably acknowledge results/checkpoints. |
| Suspend/resume | Persist stop conditions and resumption predicates; revalidate them across restarts. |
| Cancel | Stop the relevant process tree and delegated execution; reconcile work that may already have acted. |
| Recover | Restore accounting, ownership and continuity, then inspect possible effects before retry. |
| Observe | Export normalized telemetry and retain the required per-run attribution independently of trace sampling. |

**HOST-01:** Required fresh contexts, filesystem/mutation isolation, or tool restrictions MUST NOT be silently removed. A permitted serialized mode may replace concurrency; a missing mandatory capability yields a capability blocker.

**HOST-02:** The initial design proposes one active controller per configured dispatch scope. Before replacement work mutates state, stale execution MUST be fenced at an enforcing mutation boundary or stopped/isolated with side effects reconciled. A generation field alone is not fencing. Distributed active controllers are outside the initial implementation target.

**HOST-03:** A host MAY persist attempts, budgets, reservations, recovery records, and checkpoint references. Such records MUST NOT redefine delivery priority, acceptance, requirements, or approval. Coordination ownership follows its configured owner; a runtime journal cannot silently replace an existing coordination system.

**HOST-04:** Host-side secret handling MUST be paired with resource/operation restrictions. Hiding a token from a subprocess does not constrain every request made through an injected tool. Git transport, CLI tools, MCP tools, and injected tools each require verified access bindings.

**HOST-05:** Durable blocked state MUST survive restart and remain ineligible until its predicate is satisfied. Tracker labels indicate routing state, not approval. GitHub dependencies, richer phase state, and claim rules require explicit mapping beyond a generic open/closed poller.

**HOST-06:** Cleanup MUST be a verified, recoverable operation. An issue becoming terminal MUST NOT destroy the only copy of unmerged work or incomplete closeout evidence. Before deletion, verify ownership, recoverability, checkpoint/evidence persistence, and absence of pending closeout. A best-effort hook cannot be the sole protection.

## 8. Closeout and ambiguous failures

**REC-01:** External mutations are not assumed exactly-once. Use operation identities where possible, inspect existing effects before retry, and record unresolved ambiguity. Prevent duplicate PR creation, repeated ticket transitions, and stale-head merges through owner-system checks and appropriate idempotency controls.

**REC-02:** Persist integration evidence and a closeout obligation before a transition that could stop execution or trigger cleanup. Reconcile repository auto-close behavior. If a linked PR closes an issue early, preserve a path to finish closeout rather than treating issue closure as sufficient evidence.

**REC-03:** A closeout attempt is itself resumable. Completion and cleanup are separate decisions. Cancellation can leave preserved artifacts and an incomplete outcome; it MUST NOT be relabeled success merely to clear the queue.

## 9. Versioning and implementation modes

**COMPAT-01:** Every dispatch pins a specification version, phase-contract version, policy revision, and relevant implementation/adapter versions. Unknown required fields, unsupported versions, and incompatible capabilities fail admission. Versioned adapters may migrate evidence only with explicit provenance.

**COMPAT-02:** `ai-sdlc` remains a reference implementation, not the definition of every internal algorithm. Each externally callable skill declares the contract it implements; helper skills may remain private implementation details. Human-readable and machine-readable projections preserve the same conclusions and evidence semantics.

**COMPAT-03:** A profile is compatible through capability and conformance evidence, not because it is described as similar to Symphony. Report supported, optional, unsupported, and intentionally deviating behavior against a pinned baseline. Existing skills and the upstream reference host are not declared conformant by this proposal.

## 10. Adoption boundary

Adoption requires resolving the relevant blocking decisions, validating the public/private seam, and passing the applicable scenarios in [Decisions and conformance](DECISIONS-AND-CONFORMANCE.md). No schema or source change is implied by merely publishing this Proposed package. Material changes to existing operating standards require their own authorized review.
