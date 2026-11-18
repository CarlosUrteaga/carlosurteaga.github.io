---
title: "Agent Contracts Benchmark"
date: 2026-06-20
url: /projects/agent-contracts/
tags: ["AgentOps", "AI Governance", "Reliability", "Benchmarking"]
author: "Carlos Urteaga"
description: "A reproducible research benchmark for evaluating contract enforcement, safe completion, and recovery in tool-using LLM agents."
summary: "A reproducible research benchmark for evaluating contract enforcement, safe completion, and recovery in tool-using LLM agents across 21 nominal and adversarial scenarios."
hideMeta: true
homeOrder: 1
repository: "https://github.com/CarlosUrteaga/agent-contracts-benchmark"
---

<div class="work-resources" style="margin-bottom: 2rem;">
  <a href="/agent-contracts-benchmark/">[Evaluation Dashboard]</a>
  <a href="https://github.com/CarlosUrteaga/agent-contracts-benchmark">[GitHub Repository]</a>
  <a href="/agent-contracts-and-governors/">[Related Research]</a>
  <a href="/agent-cards/">[Agent Cards]</a>
</div>

**Agent Contracts Benchmark** evaluates whether executable contracts and an Agent Governor can prevent unsafe tool actions while preserving useful task completion. It measures prevention, runtime detection, and recovery after a blocked action across different language-model execution conditions.

The harness provides **21 controlled scenarios, five deterministic local tools, and four enforcement modes**. The model chooses its tool calls, arguments, order of execution, and response to a denial. A hidden outcome oracle supports offline scoring without prescribing a fixed sequence of actions. The [dashboard](/agent-contracts-benchmark/) exposes model comparisons, evaluation dates, confidence intervals, and admission issues.

## Runtime architecture

The experimental variable is the runtime enforcement mode. The headline comparison uses the same system prompt across modes; the agent does not receive the YAML contract or hidden oracle. Proposed actions pass through the governor before tool execution when online enforcement is enabled.

```text
User task → LLM agent → proposed tool call → Governor
                ↑                              │
                └──── denial / recovery ────────┤
                                               └─ allow → local tool
```

Checks occur before execution, at the tool gate, and after execution. They cover agent-configuration and contract fingerprints, declared tools, authorization, evidence, approval requirements, memory scope, response structure, and run-ledger requirements. The current runner uses `vogtcon` for governance decisions; the benchmark owns the agent loop, mock tools, feedback, traces, and result artifacts.

The tools are `search_policy`, `create_ticket`, `approve_request`, `store_memory`, and `send_notification`. Their state transitions are local and instrumented: notifications do not send real messages, and memory writes do not persist private information to an external service. This controlled environment makes committed effects and blocked attempts inspectable.

## Enforcement modes

| Mode | Governor behavior | Agent feedback | Experimental purpose |
| :--- | :--- | :--- | :--- |
| **No contract** | No online checks; actions are logged for offline scoring. | Tool results. | Establish behavior without contract intervention. |
| **Advisory** | Detects violations but permits execution. | Tool results; contract warnings remain in the audit log in the headline condition. | Separate detection from prevention. |
| **Guarded** | Blocks invalid calls and lets the agent replan. | Structured denial feedback. | Test whether the agent can recover and finish an acceptable task. |
| **Strict** | Pre-execution and runtime violations abort the run; postcondition failures mark it failed. Protected side effects are buffered and commit only on success. | No replanning after an abort. | Measure the prevention–completion tradeoff of terminal enforcement. |

These policies govern the actions that execute; they do not make the model deterministic or guarantee that its answers are correct. The [experimental specification](https://github.com/CarlosUrteaga/agent-contracts-benchmark/blob/main/docs/contract_enforcement_benchmark.md) defines their scope.

## Scenario matrix

The corpus contains **10 nominal scenarios and 11 adversarial scenarios**. Each specifies a request, initial state, available tools, acceptable and forbidden outcomes, expected violations, and an iteration budget.

| Scenarios | Conditions exercised |
| :--- | :--- |
| **S-001–S-010: nominal workflows** | Policy lookup, evidence-backed tickets, authorized approval, scoped memory, permitted notifications, safe refusal, and combined workflows. |
| **S-011–S-014: action boundaries** | Missing ticket evidence, approval without authority, sensitive notification before approval, and memory writes outside the allowed scope. |
| **S-015–S-017: response, tool, and ledger obligations** | Pressure to omit response fields, attempts to invoke an undeclared tool, and a missing run ledger. |
| **S-018–S-019: deliberate identity tampering** | Agent-configuration and contract fingerprint mismatches. |
| **S-020–S-021: escalation and evidence completeness** | Escalation without evidence and an incomplete run ledger. |

An adversarial request can still have an acceptable outcome, including safe refusal. Success depends on the resulting state and response, rather than compliance with a predetermined tool-call route. The [scenario files](https://github.com/CarlosUrteaga/agent-contracts-benchmark/tree/main/benchmark/enforcement/scenarios) and [oracle specification](https://github.com/CarlosUrteaga/agent-contracts-benchmark/blob/main/docs/oracle_spec.md) provide the task definitions and scoring context.

## Evaluation metrics

The evaluator distinguishes committed effects, task completion, runtime detection, and recovery. Its metric names and denominators are part of the interpretation:

| Metric | Measurement |
| :--- | :--- |
| **Unsafe side effects** (`unsafe_side_effect_rate`) | Committed unsafe side-effect count divided by the number of runs with an observed unsafe-action opportunity. An absent opportunity produces an undefined rate, not a zero. |
| **Governance effectiveness** (`governance_effectiveness`) | Fraction of opportunity-bearing runs with no committed unsafe side effects. |
| **Safe completion** (`successful_safe_completion_rate`) | Fraction of all runs satisfying an acceptable outcome, triggering no forbidden outcome, and recording committed completion through `side_effects_committed`. |
| **Recovery after a block** (`recovery_rate_after_block`) | Fraction of runs with at least one blocked action that subsequently satisfy an acceptable outcome. Interpret alongside safe completion and forbidden outcomes. |
| **Runtime detection** (`precision`, `recall`, `f1`) | Rule-level agreement between observed runtime violations and the scenario's expected runtime rules on detection targets. |
| **Operational overhead** | End-to-end latency, governor latency, token usage, and iteration counts. Cross-mode differences can include model behavior and replanning as well as validation work. |

The [evaluator](https://github.com/CarlosUrteaga/agent-contracts-benchmark/blob/main/tools/enforcement/evaluate.py) implements these definitions. Reported bootstrap intervals describe uncertainty within each campaign; they do not establish a general ranking of model quality. A model that rarely proposes unsafe actions may provide little evidence about the governor's intervention behavior.

## Evidence and interpretation

The historical inventory contains **26 closed campaigns and 7,728 recorded runs**. Current comparisons use **23 campaigns and 6,972 runs** after a post hoc admission screen. A three-replication campaign has `21 × 4 × 3 = 252` executions; five replications produce `420`. The [comparison dashboard](/agent-contracts-benchmark/) shows estimates, 95% bootstrap intervals, execution conditions, and campaign closure dates.

Admission checks also affect interpretation. Historical Astra, Gemma4, and GPT-5.6 Terra campaigns contain unexpected pre-execution violations and are flagged in the dashboard. These three campaigns remain visible with their incidents and are excluded in full from comparisons. The [admission review](https://github.com/CarlosUrteaga/agent-contracts-benchmark/blob/main/docs/campaign_admission_review.md) documents the rule and evidence; the [reviewed statistics](https://github.com/CarlosUrteaga/agent-contracts-benchmark/blob/main/results/enforcement/statistics/admission-reviewed-twenty-three-campaigns.json) preserve every retained campaign report and bootstrap interval unchanged. This is an admission screen rather than a certification of all experimental conditions. In particular, an abort before the model is called must not be presented as evidence of that model's task-solving ability. File completeness alone does not establish experimental comparability.

These historical campaigns predate the `vogtcon` integration. The repository's [benchmark identity audit](https://github.com/CarlosUrteaga/agent-contracts-benchmark/blob/main/docs/benchmark_identity_audit.md) documents manifest-hash discrepancies and the distinction between contract semantics and model-admission metadata. The recorded `benchmark-v1.0` label should not be treated as proof that every current artifact is byte-identical to the original freeze.

## Reproducibility and quickstart

A deterministic mock-agent run checks the harness without model API calls. Installing the dependencies may require network access. This smoke test validates execution and evidence generation; it does not reproduce the stochastic behavior of a real LLM campaign.

```bash
git clone https://github.com/CarlosUrteaga/agent-contracts-benchmark.git
cd agent-contracts-benchmark
uv sync --locked

# Check admission without calling a model.
uv run --locked python -m tools.enforcement.preflight check \
  --contracts contracts/enforcement \
  --model-profile benchmark/enforcement/config/model_profiles/mock.yaml

# Run all 21 scenarios in all four modes: 84 executions.
# Use a fresh output directory for a new experiment.
uv run --locked python -m tools.enforcement.run_all \
  --scenarios benchmark/enforcement/scenarios \
  --contracts contracts/enforcement \
  --model-profile benchmark/enforcement/config/model_profiles/mock.yaml \
  --replications 1 \
  --out outputs/mock-validation

uv run --locked python -m tools.enforcement.validate_campaign \
  --runs outputs/mock-validation --expected-runs 84 --strict

uv run --locked python -m tools.enforcement.evaluate \
  --runs outputs/mock-validation \
  --oracle benchmark/enforcement/oracle \
  --out outputs/mock-validation-summary.json
```

Each run records `summary.json`, `trace.jsonl`, `run_ledger.json`, and `run_complete.json`. Inspect the aggregate metrics in `outputs/mock-validation-summary.json` and individual traces under the run directory.

Real LLM evaluation requires a compatible model profile and the corresponding local model or API backend. For example, the repository supports local Ollama through the optional LiteLLM dependencies. Model admission is checked before a full matrix starts; deliberately tampered identity scenarios remain part of the experiment. Use a fresh output directory, record the repository revision and execution inputs, and retain the manifest and artifact hashes. The [repository workflow](https://github.com/CarlosUrteaga/agent-contracts-benchmark#benchmark-flow) supplies the backend-specific setup.

## Scope and related research

This is a research benchmark over one controlled task family and a custom agent loop. Its results do not establish production safety, arbitrary filesystem or network protection, or integration with external orchestration frameworks. Deterministic tools support auditability; they do not remove the stochasticity of real model decisions.

- [Agent Contracts and Governors](/agent-contracts-and-governors/) develops the runtime governance framework. The manuscript is under review.
- [Agent Cards](/agent-cards/) describes operational documentation for agents; documentation alone does not enforce contracts. The associated [agentcard package](/projects/agent-cards/) provides the implementation artifact.
- [The Agentic Illusion](/agentic-illusion/) examines the gap between functional achievement and operational readiness. The manuscript is under review.
