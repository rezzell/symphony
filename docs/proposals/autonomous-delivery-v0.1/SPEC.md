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
| `authorization_ref` | Approval and restrictions for an outcome, bound to a revision. |
| `budget_scope_id` | Shared accounting scope inherited across decomposition. |
| `outcome_ref` | Accepted outcome and acceptance boundary. |
| `delivery_item_ref` | A bounded delivery item in its owner system. |
| `run_id` | Stable logical delivery effort, not a provider session ID. |
| `phase_instance_id` | A particular phase execution whose budget allocation is explicit. |
| `attempt_id` | One dispatch attempt; new on a retry that may repeat work. |
| `claim_ref` | Current execution/coordination ownership and generation, where supported. |
| `worker_id` | A distinct executing worker, including coordinators and descendants. |
| `checkpoint_ref` | Immutable continuation evidence. |

**ID-01:** Runtime, model, provider, session, worktree, and trace changes MUST NOT reset a run or budget by themselves. Independent child deliveries may have separate runs under the same authorization and budget scope. New run IDs are not new allowances.

**ID-02:** References MUST include an owner, stable locator, revision or equivalent freshness evidence, and access classification where required. One global repository SHA is insufficient to version decisions, issue state, and code simultaneously. Mutable owner facts MUST be revalidated before consequential action.

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
| Intra-run checkpoint | Durable or local continuation point within the same authorized work scope. | Evidence only; no new coordination obligation by persistence alone. |
| Full coordination handoff | Meaningful transfer, external wait, escalation, or durable closeout. | Reports authority and claim disposition; does not grant authority. |
| Runtime event | Process, usage, routing, or diagnostic observation. | No product or delivery authority. |

**CONT-01:** A fresh agent, a new trace, or a persisted checkpoint MUST NOT automatically cause a full handoff. Local continuation requires the same authorized objective, scope, confidentiality boundary, valid ownership, and reconstructable continuation contract, with no new external waiting or authority boundary.

**CONT-02:** Full handoffs MUST be emitted when the applicable policy requires promotion: a decision/authority boundary, changed scope or confidentiality, independent coordination transfer, external Waiting/Blocked work, exhausted budget, unreconstructable interruption, or durable closeout. Human approval is required only when the boundary calls for it; automatic resumption from an external wait may still be authorized.

**CONT-03:** A handoff MUST identify goal, scope, current state, classified conclusions, constraints, changed artifacts, verification performed and omitted, questions, risks, owner references, claim disposition, and one executable next action or resumption predicate. A full transcript is not the default transfer format.

**CONT-04:** Controlled yield with a verified checkpoint may remain intra-run when policy permits. Unexpected process failure enters recovery first. The recovery actor must reconstruct facts, not invent the dead worker's final receipt. Whether a fully reconstructable crash may resume without a full handoff remains a blocking adoption clarification, D02; current applicable policy prevails until resolved.

**CONT-05:** Provider switching after possible mutation requires a verified checkpoint or side-effect reconciliation. The system MUST NOT assume that silence or a missing receipt means no action occurred.

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
