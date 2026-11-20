# D02 review: continuation, interruption, and full-handoff promotion

Status: **Historical D02-r2 review rationale — consolidated into the v0.1 Proposed contracts**\
Review revision: **D02-r2**  
Prepared: **2026-11-16**  
Revised: **2026-11-17**  
Prior review revision: **D02-r1 at d74e8e70a7559b2bfd18c511328bd9dc50ee01a7**  
Proposal comparison baseline: **093a8ac653a720047f1829ffaa5eb3f5c62c7f08**  
ai-sdlc comparison baseline: **c993fa3a642cfcb95afa05f7d0a4749569872549**

## 1. Bounded review and recommendation

The bounded technical disposition dated **2026-11-19** approves the exact [D02-r2 source at c291ad893679d9eb52c5405c7281a17c5129ee32](https://github.com/rezzell/symphony/blob/c291ad893679d9eb52c5405c7281a17c5129ee32/docs/proposals/autonomous-delivery-v0.1/D02-CONTINUATION-REVIEW.md) (blob `2fd2ce432a13c8139793829eefc563a246b795ee`) for the **v0.1 proposed technical-contract scope only**. Its clauses and planned scenarios are now consolidated in [SPEC](SPEC.md#4-identity-model), [PHASE-CONTRACTS](PHASE-CONTRACTS.md#8-continuation-and-recovery-evidence), and [DECISIONS-AND-CONFORMANCE](DECISIONS-AND-CONFORMANCE.md#d02-bounded-technical-disposition-and-source-mapping), which owns the complete source-to-target mapping and scoped exclusions.

This file preserves historical review rationale and examples, not a second independently normative contract. The immutable source above preserves the exact r2 amendment text and original scenario wording. The wider package remains Proposed; runtime profile permission, implementation, merge, deployment, release, spending and company-standard changes are not granted by the technical disposition.

**Historical recommendation (now consolidated):** permit controlled continuation only after a positively validated checkpoint and an explicitly permitting applicable profile. Treat unexpected interruption as recovery followed by full-handoff promotion before delivery redispatch. Reconstructable evidence does not waive that promotion. A full handoff need not require fresh human permission when the next action is already authorized; persistence is not approval.

Reconcile outcome/work-scope correlation with delivery-item run identity explicitly. Keep receipts, checkpoints, recovery observations, full handoffs, claims, and diagnostic traces distinct. None creates authority or replenishes an allowance.

Not selected here: wire encoding or schema technology (D01), shutdown reserve/grace values (D06), storage engine or fencing implementation (D07), implementation language (D08), provider selection, allocation algorithms, or release/acceptance policy. No runtime implementation is supplied.

## 2. Evidence and compatibility problem

**Observed at the proposal comparison baseline:** [SPEC.md at 093a8ac](https://github.com/rezzell/symphony/blob/093a8ac653a720047f1829ffaa5eb3f5c62c7f08/docs/proposals/autonomous-delivery-v0.1/SPEC.md) distinguishes receipts, checkpoints, and full handoffs, but its `run_id` description is broad and CONT-02 names an unreconstructable interruption while CONT-04 leaves reconstructable-crash handling open. [PHASE-CONTRACTS.md](PHASE-CONTRACTS.md) includes a worker-reported handoff flag but requires controller validation.

**Observed:** the pinned ai-sdlc [terminology](https://github.com/rezzell/ai-sdlc/blob/c993fa3a642cfcb95afa05f7d0a4749569872549/skills/sdlc-deliver/references/terminology.md) and [delivery-run contract](https://github.com/rezzell/ai-sdlc/blob/c993fa3a642cfcb95afa05f7d0a4749569872549/skills/sdlc-deliver/references/delivery-run-contract.md) bind a delivery run to one lead-claim/workboard-item, outcome, requirement, project, confidentiality boundary, and resumability context. Runtime or review-cycle changes alone do not create a run. Their full-handoff definition includes interruption.

**Derived:** broader coordination correlation and item-scoped delivery execution cannot safely share an unqualified identifier with different meanings. Reconstruction, fencing, and handoff policy are independent predicates; successful reconstruction does not prove that either of the other two is satisfied.

Public mechanisms must work without access to private policy documents. A deployment supplies an access-controlled policy reference/revision and the required resolved constraints. This review neither republishes private policy nor changes any external standard.

## 3. Consolidated clause references

| Historical amendment | Canonical proposal text |
| --- | --- |
| D02-ID1 | [Identity table and ID-03](SPEC.md#4-identity-model) |
| D02-C1–C5 | [CONT-01, CONT-02, CONT-04, CONT-06 and CONT-05 respectively](SPEC.md#6-continuity-and-promotion) |
| Phase evidence and PHASE-11 | [PHASE-05](PHASE-CONTRACTS.md#5-checkpoint-versus-full-handoff), [PHASE-08](PHASE-CONTRACTS.md#6-decision-resolution-and-resumability) and [continuation/recovery evidence](PHASE-CONTRACTS.md#8-continuation-and-recovery-evidence) |
| T37–T52 | [Canonical conformance catalog](DECISIONS-AND-CONFORMANCE.md#d02-continuation-and-recovery-scenarios) |

The following examples explain the historical recommendation; current requirements are the linked clauses above. D02-C3 in the examples refers to consolidated CONT-04.

## 4. Historical normal and failure examples

**Normal controlled exchange:** implementation produces receipt `receipt-A` and checkpoint `checkpoint-A` for immutable revision `rev-A`; the controller records acceptance `acceptance-A` bound to those exact identities and the permitting-profile revision, and verifies the writer is stopped/fenced. The same item run enters an already-authorized review phase under its permitting profile. A fresh worker/trace is diagnostic change only. Review consumption is charged to the existing scope ledger.

**Receipt without acceptance:** the worker returns a successful receipt and persists a checkpoint while the controller remains available. Delivery does not advance until the controller validates and records acceptance and every other eligibility condition holds. This incomplete exchange is not automatically an interruption. If the worker disappears before the controlled exchange is established, D02-C3 applies instead. An acknowledgement for another attempt or checkpoint revision does not repair the gap.

**Context approval without permission:** a bootstrap/context manifest is approved and selects the review sources, but no applicable lifecycle/adoption profile explicitly permits checkpoint-only exchange. Even with a valid checkpoint and verified ownership, the controller does not infer permission or redispatch. It records the missing binding and follows the required handoff/escalation path. Technical proposal adoption alone would not supply the missing profile permission.

**Crash after a consequential write:** a worker creates a PR and dies before returning its receipt. Recovery finds the PR using operation/owner-system evidence. It does not create a second PR or invent a successful worker receipt. A full handoff is persisted and linked even if all effects can be reconstructed. Resumption checks current PR head, authority, claim, policy and budget.

**Promotion interruption:** a full packet exists, but the controller fails before updating its coordination pointer. The next recovery reconciles the promotion ID, verifies the existing packet and finishes the missing linkage. It does not create a new budget, a second delivery item or an unrelated duplicate packet. Delivery remains inhibited until the obligation and ownership checks are satisfied.

## 5. Remaining runtime gate and compatibility

The bounded technical-review decision is recorded in the [decision register](DECISIONS-AND-CONFORMANCE.md#d02-bounded-technical-disposition-and-source-mapping). The applicable permitting lifecycle/adoption profile, exact authorized revision, deployment scope and exchange types remain unresolved. Technical consolidation, context-manifest approval and runtime capability declarations do not supply checkpoint-only continuation permission.

Existing adapters may retain a checkpoint as a specialized stage receipt and co-locate evidence roles, while preserving provenance and validating the exact checkpoint/acceptance association under a supported contract version. D01 still owns encoding and D07 storage/fencing. No ai-sdlc field is renamed; its pinned item-run semantics are preserved by explicit mapping. No C01–C10 input, upstream specification, runtime, workflow, license/notice, ai-sdlc implementation or approved company standard is changed.
