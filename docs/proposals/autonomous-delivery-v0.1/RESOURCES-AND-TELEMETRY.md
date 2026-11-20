# Execution accounting, provider allocation, and observability

Status: **Proposed**  
Version: **0.1**  
Parent: [Delivery specification](SPEC.md)

## 1. Independent resource limits

**RES-01:** Provider usage and execution time are independent, co-equal limits. A run may proceed only while every applicable limit permits it. Unused time cannot compensate for exhausted usage; unused usage cannot justify a time overrun. Concurrency, depth, attempts, permissions, and provider limits remain independently binding.

An admitted authorization resolves a versioned budget manifest containing its shared accounting scope, phase allowances, profile allowances, reservations, shutdown reserve, and any overall deadline or worker-time cap. Configuration changes do not retroactively grant new allowance.

**RES-02:** Decomposition, planning, routing, orchestration, implementation, verification, review, remediation, retries, and required closeout consume the governing allowance. Child items receive allocations under the parent budget scope, not copies of the parent's entire budget. Consumption survives process restarts, provider changes, issue replacement, and retries. Charges reported at child and parent levels are the same underlying consumption, not two charges.

A budget-exhaustion report must be able to state that planning/decomposition consumed X before implementation began, distinguish measured from estimated X, identify the remaining work, and stop without requesting or assuming an automatic extension.

## 2. Phase-normalized time

The confirmed starting defaults are **20 minutes for plan-to-spec** and **2 hours for implementation**. These are defaults for logical phases, not a universal deadline for a complete delivery and not a fresh allocation per skill invocation. Other phase defaults and the optional aggregate ceiling are unresolved in D04; scheduled admission must obtain finite applicable limits rather than interpret an unspecified limit as unlimited.

**TIME-01:** Time accounting MUST distinguish:

| Measure | Definition |
| --- | --- |
| Phase active elapsed time | Duration of the union of executing intervals attributable to that logical phase in the budget scope. Overlapping child activity counts once. |
| Run/outcome active elapsed time | Duration of the union of executing intervals in that scope across phases. Do not sum overlapping phase durations to obtain it. |
| Aggregate worker-time | Sum of execution intervals for distinct coordinating and child workers; not CPU time. Nested spans on the same worker do not add another worker. |
| Parked/queue time | Non-executing time classified by reason, including human decision, external dependency, or unavailable capacity. |
| End-to-end duration | Admission-to-observation/completion time, including waiting. |

Three workers executing concurrently for ten minutes consume ten minutes of active elapsed time and thirty worker-minutes, plus any additional coordinator-worker time. Worker-time and usage are attributed to role, phase, profile, and configuration. A coordinator that is still executing counts as a worker even when its useful work is supervision.

**TIME-02:** Re-entering a phase uses its remaining allocation. Returning from review to implementation does not grant a new two hours. A separate remediation allocation is valid only if the governing manifest already authorizes it. Re-labeling work to avoid a limit is prohibited.

**TIME-03:** A phase clock pauses only after its executing workers have stopped and the relevant scope is durably parked. A hung command, repeated tool call, heartbeat, or active retry loop does not pause or reset the deadline. Proposed suspension/recovery details remain subject to D02 and D06. Monotonic intervals should measure local execution; persisted interval records and conservative recovery handle restarts and uncertain termination. Clock uncertainty must be reported, not converted into zero consumption.

**TIME-04:** The host enforces the remaining deadline outside agent reasoning. Cancellation covers process trees and delegated agents; remote operations that cannot be cancelled must be reported and reconciled. Runtime subagent/grandchild/concurrency controls are retained. Active elapsed limits alone do not bound total parallel compute, so provider allowances and concurrency controls must also apply.

## 3. Admission, reservations, and stopping

**RES-03:** Before dispatch, revalidate eligibility and reserve resource allocations atomically across the applicable authorization, profile, and capacity domain. For usage, measured consumption plus outstanding unconsumed reservations plus the new reservation must fit every applicable allowance. Reservations convert to measured consumption; they are not added again after settlement. Unused reservations may be released only after execution is known to be finished or safely reconciled.

Parallel time grants share the parent's phase clock and cancellation boundary; they are not independent copies that can extend the deadline. An additional worker-time cap may constrain their sum.

**RES-04:** Reserve checkpointing/reporting/shutdown capacity inside the existing authorization. Begin controlled stopping before the hard ceiling. A failing agent does not receive unlimited extra model time to describe its failure. A host can still persist already-known recovery evidence after a crash without pretending it ran an authorized extra reasoning phase. Exact reserve and cancellation tolerances remain D06.

**RES-05:** Temporary provider unavailability differs from exhausted work authorization. Capacity recovery may satisfy a declared waiting predicate, but a provider quota reset must not reset the work's consumption. No paid overage, credit purchase, API-funded fallback, or extra allowance may be silently enabled.

Hard provider limits may not be observable or precisely enforceable mid-call. Adapters must declare metering granularity, observation delay, cancellation behavior, and the strongest boundary they can enforce. Conservative reservations and early stopping can reduce risk but cannot turn unknown metering into an exact guarantee. If a required hard guarantee cannot be supplied, admission fails or the authorized operator explicitly selects a different bounded policy; uncertainty never becomes unlimited permission.

## 4. Provider profiles, capacity domains, and pools

**PROFILE-01:** A provider profile is a named execution configuration: agent/client, model/reasoning settings where supported, authentication reference and verified funding mode, capabilities, permissions, usage unit and allowance, metering confidence, concurrency, and version.

A **capacity domain** is the real subscription/entitlement and its simultaneous constraints. Multiple profiles from the same vendor may share a capacity domain. Conversely, profiles may represent independently authorized entitlements. A profile cannot prove independent capacity merely by having a different name or configuration.

A **pool** groups eligible profiles. A **route** determines suitability. An **allocation policy** chooses how to consume suitable capacity. These are composable, not mutually exclusive selections.

**PROFILE-02:** Shared-domain limits MUST be enforced once across all aliases. Preserve profile attribution while deduplicating underlying consumption. External/manual use of the same subscription must be reflected when provider data permits; stale or unavailable global capacity is uncertainty, not evidence that all capacity is free.

**PROFILE-03:** The initial deployment profile permits subscription-authenticated Codex or Claude Code execution and prohibits separately billed inference across workers, reviewers, coordinators, routing, and required evaluators. A local routing model may be permitted by policy; its compute and time are not free. Validate actual client authentication and billing behavior for the selected version. Do not classify funding solely by endpoint name or presence of an SDK. Credentials remain in approved secret mechanisms; profiles contain references only. This is a deployment requirement, not a claim that every third-party integration is supported by either provider.

Subscription capacity, authorized work allowance, estimated token cost, and financial charges are distinct. The core host should represent multiple provider windows rather than invent one universal token bucket. Provider resets update availability, not authorization.

## 5. Routing and allocation

**ALLOC-01:** Apply hard authority, confidentiality, capability, funding, and availability filters before optional ranking. Ranking may use rules, analytical models, or a bounded lightweight ('System 1') model. The final admission check is deterministic. A selector cannot enlarge its candidate set, downgrade required capability, alter authority, or grant budget. Routing overhead is charged and observable; recursive routing must be bounded.

| Policy | Required semantics |
| --- | --- |
| Sequential | Prefer configured eligible profiles in order; use explicit fallback conditions and safe continuation boundaries. |
| Pro rata | Target capacity-weighted consumption shares using agent-phase estimates; borrow idle allocation within real limits. |
| Shared pool / best effort | Use available eligible capacity without guaranteed shares; do not relax limits or requirements. |
| Routed | Rules, analysis, or model-assisted selection chooses a suitable pool/profile, followed by its allocation policy. |

**ALLOC-02:** Pro rata weights are target shares with borrowing of idle allocation, not exclusive reservations. Borrowing is redistribution of scheduling opportunity, not movement of provider credits or entitlement. Prefer rebalance on subsequent dispatches or safe checkpoints; involuntary preemption solely to restore a ratio is not the proposed default.

**ALLOC-03:** Allocation is based on estimated consumption, not raw dispatch count. Use role/phase/profile/configuration history, with task-size information when justified. Record the estimator version, sample coverage, uncertainty, expected and actual consumption, routing reason, target share, realized share, and reason for deviations. Cold-start behavior must be explicit and conservative. Capability/quality eligibility outranks fairness.

### Canonical 5:1 example

These are synthetic allowances, not claims about current commercial plans. Both allowances use comparable token accounting and the same declared evaluation window.

| Profile | Allowance | Cache knowledge | Target |
| --- | ---: | --- | ---: |
| A | 1,000,000 tokens | Opaque | 5/6 |
| B | 200,000 tokens | No cache | 1/6 |

For comparable phases costing 10,000 allowance tokens on each provider, five executions on A consume 50,000 and one on B consumes 10,000. Both have used 5% of their allowance. A roughly 5:1 dispatch count emerges from equal estimated phase costs.

A long implementation and a short planning phase are not equivalent jobs. If reliable evidence instead establishes 5,000 allowance tokens for a comparable phase on A and 10,000 on B, ten A executions and one B execution each consume 5% of their respective allowance. The consumption target remains 5:1; the dispatch count differs. Opaque caching alone is not that evidence.

A candidate reference heuristic is to favor the eligible destination with lower projected proportional utilization:

```text
projected_utilization[p] =
  (consumed[p] + reserved_unconsumed[p] + estimated_next_phase[p])
  / configured_allowance[p]
```

This is a Proposed heuristic, not the final allocation algorithm. Comparability, independent domains, a common accounting window, explicit tie-breaking, and guardrails are prerequisites. Weighted-fair or deficit-based implementations may satisfy the same observable contract. Different native units require a documented normalized policy model or explicit weights; never sum unlike tokens/requests/credits as if they were fungible.

**ALLOC-04:** Unknown caching MUST NOT create assumed free usage. Keep reported model tokens, estimated allowance consumption, and observed remaining provider capacity separate. Reconcile estimates when evidence arrives, retain provenance, and never overwrite history merely to make the target look balanced.

The allocation horizon, quota-window/reset mapping, fairness tolerance, and estimator uncertainty policy remain D03/D05. Borrowing semantics and the consumption-based interpretation are confirmed design inputs, not open questions.

## 6. Accounting and OpenTelemetry

**OBS-01:** The host's durable accounting enforces authorization; OpenTelemetry projects execution evidence for observation. Collector failure, sampling, exporter restart, or metric overflow MUST NOT disable enforcement or erase usage. Journal writes, event identities, cumulative-counter deltas, and receipt replay must avoid duplicate charges. If durable accounting is unavailable, stop new admissions and follow bounded safe-stop policy.

Use OpenTelemetry metrics, traces, and structured logs/events with a versioned delivery vocabulary. Reuse applicable upstream conventions; pin and translate their versions rather than making accounting depend on moving names. Sources [S7](README.md#sources) describe metric aggregation and trace/span/link concepts; the names below are proposed, not existing upstream conventions.

| Proposed signal | Meaning |
| --- | --- |
| `sdlc.execution.active_duration` | Finalized run/phase active duration in seconds; histogram with explicit scope kind. |
| `sdlc.execution.active_age` | Current accumulated active duration for unfinished run/phase scopes; queryable gauge/record. |
| `sdlc.worker.time` | Incremental worker-seconds, charged once per distinct worker interval. |
| `sdlc.execution.wait_duration` | Waiting duration classified by reason. |
| `sdlc.provider.usage` | Consumption with explicit unit, category, profile, domain, confidence and provenance. |
| `sdlc.resource.reserved` | Outstanding reservations, not an additional usage charge. |
| Routing/lifecycle events | Admission, route, fallback, checkpoint, suspend, resume, rejection, limit breach and recovery. |

Never add a provider's total-token field to its component categories. Nested worker, model, and tool spans are drill-down views, not independent worker-time. Persist interval observations sufficient to compute unions for elapsed time. Provider aggregate usage and leaf usage must be reconciled, not blindly summed.

## 7. High-cardinality investigation is required

**OBS-02:** A conforming deployment MUST identify individual runs contributing to an aggregate regression and drill into their phases, workers, attempts, profiles, and routing decisions. It may combine aggregate metrics, intentionally high-cardinality metrics, indexed run summaries, structured events, and linked traces. The backend choice is open; the investigation capability is not.

Preserve `run_id`, phase/attempt/worker identity, profile and capacity-domain identity, policy/estimator revision, and trace correlation in the appropriate queryable representation. Configure active cardinality, retention, overflow and sampling explicitly. Do not silently discard required attribution to meet a metric-label limit. Exemplars are navigation aids, not a complete contributor index.

**OBS-03:** Support analysis of the same population with and without a selected run, showing the exclusion and denominator. Recompute distributions/percentiles from sufficient retained observations; do not subtract percentiles. Make active-run age visible before completion so a runaway cannot disappear from completed-duration charts.

**OBS-04:** Use bounded traces per active execution segment/attempt and causal links across resumption. Keep stable run identity outside a single trace. Neither a new trace nor an exporter boundary creates a new delivery run, budget, or full coordination handoff.

**OBS-05:** Keep core lifecycle/accounting attribution complete within the declared retention window, independently of detailed trace sampling. Report export lag, missing data, estimation, sampling, counter resets, clock uncertainty, and cardinality overflow. Failure/runaway preservation policies and exact retention periods remain D10.

**OBS-06:** Export minimal metadata by default. Protect prompts, code, issue titles, credentials, client context, and raw tool output separately. Apply access control and redaction to logs, metrics, exemplars, trace attributes, links, and propagated context. An opaque identifier is not proof of anonymity. Public aggregate access must not automatically permit confidential per-run lookup.

## 8. Observability acceptance questions

An authorized operator must be able to answer: Which run changed the phase-duration distribution? Was it implementation, orchestration, rework, or waiting? How many workers contributed? Did a routing choice or opaque metering affect usage? Did reservations or retries multiply charges? What remains of the authorized budget? Which evidence supports the next action?

These are conformance requirements, not promises that a dashboard or collector is implemented by this documentation change.
