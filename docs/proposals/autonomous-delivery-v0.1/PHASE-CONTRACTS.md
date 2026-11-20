# Phase, receipt, checkpoint, and handoff contracts

Status: **Proposed**  
Version: **0.1**  
Parent: [Delivery specification](SPEC.md)

## 1. Design rule

A phase is a behavioral boundary; a skill is an implementation. Define contracts for externally callable phases and skills without turning every helper into a scheduled job or every return value into a full handoff. Existing dispatch/receipt and recovery concepts come from [sources S3-S4](README.md#sources).

**PHASE-01:** Every callable entrypoint MUST declare its contract, required capabilities, authoritative inputs, permitted mutations, output evidence, retry classification, and promotion rules. Internal helpers may use narrower contracts inherited through their parent phase.

The forms below are illustrative data models, not shipped JSON Schemas or configuration accepted by today's clients. Schema encoding, canonicalization, and transport are to be decided under D01. Synthetic locators are intentionally not resolvable production references.

## 2. Common dispatch envelope

```yaml
stage_dispatch:
  spec_version: "0.1-proposed"
  contract: "sdlc.implementation@0.1-proposed"
  authorization_ref: {owner: knowledge, locator: "urn:example:approval:42", revision: "4"}
  outcome_ref: "urn:example:outcome:42"
  work_scope_ref: {owner: knowledge, locator: "urn:example:scope:42", revision: "4"}
  delivery_item_ref: "urn:example:issue:101"
  budget_scope_id: "budget-outcome-42"
  run_id: "run-101"
  phase_instance_id: "implementation-101"
  attempt_id: "attempt-2"
  claim_ref: {locator: "urn:example:claim:101", generation: 3}
  policy_revision: "policy-r7"
  bounded_objective: "Implement the already accepted behavior."
  input_refs:
    - {owner: knowledge, locator: "urn:example:spec:42", revision: "4"}
    - {owner: repository, locator: "urn:example:repo", revision: "commit-a"}
  bootstrap_ref: "urn:example:bootstrap:r7"
  budget_allocation_ref: "urn:example:allocation:implementation-101"
  required_capabilities: [fresh_context, isolated_workspace, typed_receipt]
  allowed_mutations: [source_in_declared_paths, tests_in_declared_paths]
  output_contract: "sdlc.implementation-result@0.1-proposed"
```

The dispatch carries pointers and a bounded objective, not the parent transcript. Allowed mutations are constraints, not self-authenticating permissions; the receiving implementation revalidates the governing authorization.

**PHASE-02:** Capability and authority checks occur before consequential execution. A stale claim, mismatched input, inaccessible required source, unknown contract, or insufficient budget prevents admission. A receipt from a previous attempt cannot satisfy a later attempt merely because the issue number matches.

## 3. Common result envelope

```yaml
stage_receipt:
  spec_version: "0.1-proposed"
  contract: "sdlc.implementation-result@0.1-proposed"
  run_id: "run-101"
  phase_instance_id: "implementation-101"
  attempt_id: "attempt-2"
  claim_generation: 3
  input_refs:
    - {owner: repository, locator: "urn:example:repo", revision: "commit-a"}
  output_refs:
    - {owner: repository, locator: "urn:example:repo", revision: "commit-b"}
  status: succeeded
  verification:
    passed: ["urn:example:evidence:unit-tests"]
    failed: []
    not_performed: ["independent_review"]
  mutation_evidence_refs: ["urn:example:diff:a-to-b"]
  accounting_ref: "urn:example:accounting:attempt-2"
  checkpoint_ref: "urn:example:checkpoint:implementation-101-2"
  decision_boundary_crossed: false
  full_handoff_required: false
  next_recommended_stage: review
```

`status` distinguishes succeeded, partial, waiting, blocked, failed, cancelled, and indeterminate. Succeeded means this phase's postconditions, not final delivery or product acceptance. An adapter error must not manufacture an empty successful receipt.

**PHASE-03:** The controller validates identity, claim generation, required evidence, allowed mutations, immutable revisions, policy applicability, and next-stage eligibility. It rejects stale or contradictory evidence and records the reason. Free-form text is not sufficient for scheduled continuation. Human-readable rendering must expose the same material facts, including omitted verification.

**PHASE-04:** Duplicate receipts are acknowledged without repeating effects or charges. Acknowledgement means receipt, not approval. Raw prompts, tool dumps, and full diffs stay in appropriate protected stores and are referenced selectively.

## 4. Phase catalog and existing implementation mapping

The mapping is an extraction plan, not a claim that current skills already emit the proposed envelopes. Each row includes mandatory evidence and limits; common authorization, accounting, and continuity rules apply to all rows.

| Contract / existing entrypoint | Required input and allowed effect | Required output / stop boundary |
| --- | --- | --- |
| Goal composition / `sdlc-compose-goal` | Authorized objective and policy; compile a bounded objective without starting delivery or changing authority. | Goal packet, constraints, required sources; stop on ambiguity in authority or objective. |
| Intake / `sdlc-workboard-router` | Item, triggers, coordination claim and latest continuity evidence; verify and claim within policy. | Verified route, claim correlation, input snapshot; stop on conflicting ownership or unsatisfied trigger. |
| Portfolio coordination / `sdlc-portfolio-governor` | Authorized outcome, linked items and dependencies; derive a graph and delegate eligible work through one scheduler. | Child links, acceptance allocation, budget allocations, progress evidence; never infer outcome acceptance from child closure. |
| Planning/specification / `sdlc-plan-orchestrator`, `sdlc-plan-decompose`, `sdlc-plan-reconcile` | Accepted intent and applicable constraints; investigate and mechanically decompose within delegated authority. | Versioned implementation-ready plan/spec, acceptance mapping, dependencies, verification plan, ambiguity report. Product decisions outside authority cause promotion. |
| Planning transfer / `sdlc-plan-handoff` | Accepted planning decisions and current owner evidence; prepare bounded execution input. | Dispatch-ready references and constraints. Use a checkpoint inside the same run; a full handoff only when the actual coordination boundary requires it. |
| Implementation / `sdlc-implement` | Accepted plan, code revision, allowed surfaces and test criteria; modify only delegated source/test/configuration surfaces. | Candidate revision, changes, tests, unresolved risks and implementation units; stop on scope expansion or required isolation failure. |
| Verification/integration / configured implementation integrator | Candidate units, shared contracts, integration order and test obligations; integrate/test permitted changes. | One immutable review candidate and verification evidence; reject incompatible contracts or unverifiable integration. |
| Review / `sdlc-review-orchestrator`, dispatch and synthesis skills | Immutable candidate, acceptance mapping, review lanes and publication policy; inspect independently and publish permitted findings. | Revision-bound findings and review posture, lane coverage and omitted checks; review posture alone is not platform approval. |
| Remediation/response / fix stages and `sdlc-respond-to-review` | Verified findings and accepted scope; implement bounded corrections and evidence-backed responses. | New revision, finding dispositions and required re-verification; scope-changing remedies return to planning/authority. |
| Transition validation / `sdlc-loop-governor` | Latest receipt/checkpoint, remaining budgets and live owner facts; select only a permitted next action. | Validated checkpoint, rejection reason, or precise escalation; do not treat itself as the owner of requirements or delivery state. |
| Merge / `sdlc-merge-pr` | Exact-head review, checks, threads, mergeability and merge authority; enable authorized auto-merge or integrate when gates hold. | Verified merge evidence or bounded merge-wait checkpoint; no ticket closeout as a side effect of this phase contract. |
| Delivery closeout / `sdlc-closeout-delivery` | Verified integration and declared post-merge policy; persist evidence, update authorized owner records and verify final state. | Closeout record, full handoff where required and verified completion; no completion claim while mandatory evidence is missing. |
| Retrospective / `sdlc-evaluate` | Immutable closeout evidence and a registered deterministic suite; produce quality evidence only. | Suite/version/results and gaps; do not reopen delivery or invent labels unless already-authorized policy requires it. |
| Onboarding/configuration / `sdlc-onboard-repository`, `sdlc-onboard-user`, merge-gate standardization | Explicit configuration-change authorization and current state; prepare or apply only permitted changes. | Configuration diff, version and validation; not automatically authorized by an ordinary delivery issue. |

Future entrypoints must map to a contract rather than bypassing this catalog. A full inventory is part of W02 in the [implementation plan](IMPLEMENTATION-PLAN.md).

## 5. Checkpoint versus full handoff

**PHASE-05:** A checkpoint records the phase result, revision references, verification posture, findings, remaining/reserved budget references, next permitted action, and continuity correlation. A new runtime may consume it without a new workboard item only when every [CONT-01](SPEC.md#6-continuity-and-promotion) predicate holds, including exact positive controller acceptance, applicable permitting-profile authority and safe ownership. Receipt, checkpoint and controller-acceptance provenance MUST remain distinguishable even when co-located. A persisted checkpoint or producer assertion is not self-accepted; missing acceptance alone does not prove interruption.

**PHASE-06:** A full handoff adds the material coordination context: goal and scope, verified versus unverified work, classified decisions, constraints, artifacts changed, verification omissions, unresolved risks, claim release/transfer, owner references, and the exact decision or external predicate needed to continue. It does not copy every intra-run checkpoint or the conversation transcript.

A useful illustrative extension is:

```yaml
coordination_handoff:
  version: "0.1-proposed"
  run_id: "run-101"
  authorization_ref: "urn:example:approval:42@4"
  reason: human_decision_required
  checkpoint_ref: "urn:example:checkpoint:implementation-101-2"
  claim_disposition: retained_suspended
  verified_state_refs: ["urn:example:evidence:current-state"]
  unverified_effects: []
  decision_request:
    question: "Which of the two incompatible behaviors should the product expose?"
    options_refs: ["urn:example:decision-options:42"]
    recommendation_ref: "urn:example:recommendation:42"
    decision_owner_ref: "urn:example:authority:product"
  resume_predicate_ref: "urn:example:predicate:approved-resolution-for-spec-r4"
  next_action: "Record and verify the authorized resolution before resuming."
```

The common full-handoff fields defined in [SPEC.md](SPEC.md) remain required; this compact example shows only the specialized coordination fields. Required information may be carried by accessible immutable references rather than repeated payloads.

## 6. Decision resolution and resumability

**PHASE-07:** A decision response must identify the decision subject, authorized actor, applicable revision, permitted continuation, and any changed constraints. A comment saying 'looks good' on an unrelated or stale revision is not sufficient. Revalidate conflicting changes and budget availability before resuming.

A deadline, quota reset, new trace, or provider change is not itself decision approval. External capacity recovery can satisfy a declared resource predicate but cannot replenish an exhausted work authorization.

**PHASE-08:** Controlled runtime changes may reuse a checkpoint only under CONT-01's accepted-checkpoint and pre-dispatch revalidation predicates. Lost receipts require inspecting effects. Unexpected interruption enters [CONT-04 recovery](SPEC.md#6-continuity-and-promotion), with ordinary delivery redispatch inhibited until CONT-06 full-handoff promotion and reconciliation are complete, even when state is reconstructable. The recovery actor records its own observations, not an invented worker receipt or success. A valid accepted failure receipt with verified continuity may support a bounded retry only when all continuation predicates still hold.

## 7. Transport and projection rules

**PHASE-09:** The structured contract is required for scheduled interchange. CLI stdout, files, local IPC, RPC, or another transport may carry it if framing, identity, size limits, acknowledgement, error behavior, and privacy are defined. Provider-specific session handles remain adapter-local metadata, not portable authority.

Interactive mode should present a compact outcome, evidence, remaining risk, and next action. Machine interchange may add correlation and enforcement fields without forcing humans to read them at every step. Standalone invocations without a scheduler remain subject to the same authority and verification rules, with runtime guarantees honestly declared.

**PHASE-10:** Unknown required fields or versions fail closed. Harmless optional extensions may be retained or ignored according to the declared compatibility policy. Pin contract and policy revisions for an admitted attempt; revalidate material changes rather than silently switching semantics mid-flight.

The initial implementation should validate envelopes and owner evidence while keeping judgment-heavy planning/review in skills. Moving a deterministic transition check into Rust must not change its meaning.

## 8. Continuation and recovery evidence

These conceptual requirements extend the illustrative forms above; they are not a selected wire schema. D01 defines encoding and D07 storage/fencing mechanisms. The canonical acceptance bindings and six eligibility predicates are in [CONT-01](SPEC.md#6-continuity-and-promotion).

| Record | Required evidence |
| --- | --- |
| Dispatch | Explicit work-scope/item-run mapping; contract, policy and authorization revisions; ownership safety and ledger/reservation references; bounded next action. When consuming a predecessor checkpoint, reference that checkpoint and its acceptance evidence. An initial dispatch does not require acceptance of a future checkpoint. |
| Worker receipt | Producer and dispatched phase/attempt identity; immutable input/output and evidence references. Existing `full_handoff_required` remains advisory evidence. The controller independently evaluates triggers; a false flag cannot suppress a required handoff. |
| Intra-run checkpoint | Checkpoint identity/revision; carried or referenced stage receipt; continuation state required by CONT-01. Do not equate successful persistence with positive controller acceptance. |
| Controller acceptance record | The exact bindings and positive validation evidence required by CONT-01, including the applicable permitting-profile revision and bounded next transition. Keep producer assertions distinct from receiver/controller validation. |
| Recovery observation | `record_kind` distinct from worker receipt; observer identity; interrupted run/phase/attempt; failure/event identity; last verified checkpoint; actual observation time; observed/unknown effects; ownership and accounting reconciliation; next safe action. No invented worker completion timestamp or success. |
| Promotion obligation | Stable obligation ID; trigger(s); run/scope identities; policy revision; packet artifact/version; required coordination targets; persistence/link verification; claim disposition; resume predicates. |

**PHASE-11:** Reject continuation when required identity mapping, checkpoint acceptance, effect disposition, ownership safety, accounting, policy or promotion evidence cannot be validated. Scheduled exchanges require validated structured records. Standalone operation applies the same predicates and may additionally present a human-readable explanation. A human-readable reconstruction does not substitute for an absent required structured record.

Retain existing receipt statuses; do not disguise a recovery observation as `succeeded`. A recovery observation may establish that a particular side effect happened without proving phase completion, review, integration, closeout, release or outcome acceptance.
