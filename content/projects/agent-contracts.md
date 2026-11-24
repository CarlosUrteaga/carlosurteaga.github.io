---
title: "Agent Contracts Benchmark"
date: 2026-06-20
url: /projects/agent-contracts/
aliases:
  - /agent-contracts-benchmark/
  - /agent-contracts-benchmark
tags: ["AgentOps", "AI Governance", "Reliability", "Benchmarking"]
author: "Carlos Urteaga"
description: "A reproducible research benchmark for evaluating contract enforcement, safe completion, and recovery in tool-using LLM agents."
summary: "A reproducible research benchmark for evaluating contract enforcement, safe completion, and recovery in tool-using LLM agents across 21 nominal and adversarial scenarios."
hideMeta: true
homeOrder: 2
repository: "https://github.com/CarlosUrteaga/agent-contracts-benchmark"
---

<div class="work-resources" style="margin-bottom: 2rem;">
  <a href="https://github.com/CarlosUrteaga/agent-contracts-benchmark">[Benchmark Repository]</a>
  <a href="https://github.com/CarlosUrteaga/vogtcon">[vogtcon (Library)]</a>
  <a href="/agent-contracts-and-governors/">[Related Research Paper]</a>
  <a href="/agent-cards/">[Agent Cards Standard]</a>
</div>

**Agent Contracts Benchmark** is an empirical evaluation harness designed to test whether runtime contracts and an active Agent Governor (implemented in the [`vogtcon`](https://github.com/CarlosUrteaga/vogtcon) library) can prevent harmful actions and guide tool-using LLM agents toward safe recovery without compromising task completion.

The harness evaluates autonomous agents against **21 nominal and adversarial scenarios** operating over controlled, instrumented mock environments.

---

### Runtime Governance Loop

In production environments, prompt instructions alone fail under distribution shifts or adversarial pressure. This benchmark tests a **runtime intervention loop**:

```
 [User Task / Prompt]
          │
          ▼
   ┌─────────────┐       Proposed Tool Call / Action
   │  LLM Agent  │ ────────────────────────────────────────┐
   └─────────────┘                                         │
          ▲                                                ▼
          │ Action Feedback                       ┌───────────────────┐
          │ (Error / Intervention message)        │   Agent Governor  │
          │                                       │ (Enforces Schema, │
          └────────────────────────────────────── │  Pre/Post-conds)  │
                                                  └───────────────────┘
                                                           │
                                                           │ Allowed Action
                                                           ▼
                                                  ┌───────────────────┐
                                                  │ Instrumented Tool │
                                                  │   (Mock Env)      │
                                                  └───────────────────┘
```

---

### Evaluated Enforcement Modes

The benchmark evaluates agents across four operational regimes to observe the tradeoff between safety and task completion:

| Mode | Governor Action | Feedback to Agent | Evaluation Objective |
| :--- | :--- | :--- | :--- |
| **No-Contract** | None (pass-through) | Raw tool errors only | Baseline: Measures default failure rate and unmitigated side effects. |
| **Advisory** | Flags contract breaches | Audit log only (silent) | Measures auditability without interfering with autonomous decisions. |
| **Guarded** | Blocks forbidden calls | Actionable error message | Tests whether the agent can adapt, self-correct, and complete the goal safely. |
| **Strict** | Aborts execution immediately | Halts session | Maximum security: Eliminates side effects at the expense of task completion. |

---

### Scenario Matrix (21 Scenarios)

The evaluation dataset stresses agent decision-making across three distinct failure topologies:

1. **Nominal Complex Workflows (7 Scenarios):** Multi-step dependencies, stateful transactions, and standard read/write invariants where governance must not introduce false positives or prevent task completion.
2. **Tool Misuse & Boundary Probing (7 Scenarios):** Schema violations, unauthorized filesystem/network traversal, privilege escalation attempts, and invalid argument injections.
3. **Recovery Under Adversarial Blockades (7 Scenarios):** Injection of deceptive tool responses, resource exhaustion constraints, and blocked-action recovery loops where the agent must find alternate valid execution paths.

---

### Key Evaluation Metrics

Instead of basic accuracy, the harness measures operational metrics vital for enterprise production:

* **Unsafe Side-Effect Rate (USER):** Percentage of runs resulting in destructive or out-of-policy tool executions.
* **Safe Completion Rate (SCR):** Ratio of runs that satisfy all post-conditions without violating any invariant.
* **Recovery Resilience Rate (RRR):** Percentage of blocked actions where the agent successfully recovers instead of entering infinite loops or failing.
* **Governance Overhead:** Latency (ms) and token consumption added by runtime validation checks.

---

### Quickstart & Reproducibility

The benchmark is packaged for deterministic local reproduction with no external cloud dependencies required:

```bash
# Clone the evaluation harness
git clone https://github.com/CarlosUrteaga/agent-contracts-benchmark.git
cd agent-contracts-benchmark

# Install dependencies
pip install -e .

# Run the 21 benchmark scenarios under Guarded mode
python -m benchmark.run --mode guarded --scenarios all --repeats 5

# Inspect deterministic audit evidence
cat results/audit_summary.json
```

---

### Related Research & Artifacts

* **[vogtcon (GitHub)](https://github.com/CarlosUrteaga/vogtcon)**: Python library implementing the Agent Governor and contract enforcement runtime loop.
* **[Agent Contracts and Governors](/agent-contracts-and-governors/)**: The theoretical framework defining contract obligations and governor states.
* **[Agent Cards](/agent-cards/)**: Documentation standard published at MICAI 2025 Workshops for describing agent boundaries and operational profiles.
* **[The Agentic Illusion](/agentic-illusion/)**: Analysis of why functional demos diverge from production operational readiness.
