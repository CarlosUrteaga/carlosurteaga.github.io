---
title: "Agent Contracts Benchmark"
date: 2026-06-20
url: /projects/agent-contracts/
tags: ["AgentOps", "AI Governance"]
author: "Carlos Urteaga"
description: "A reproducible research benchmark for evaluating contract enforcement, safe completion, and recovery in tool-using LLM agents."
summary: "A reproducible research benchmark for evaluating contract enforcement, safe completion, and recovery in tool-using LLM agents."
hideMeta: true
homeOrder: 1
repository: "https://github.com/CarlosUrteaga/agent-contracts-benchmark"
---

Agent Contracts Benchmark evaluates Agent Contracts and an Agent Governor over a tool-using LLM agent. It compares no-contract, advisory, guarded, and strict enforcement in 21 nominal and adversarial scenarios.

The agent makes stochastic decisions while interacting with controlled local mock tools. The benchmark records execution evidence and evaluates unsafe side effects, safe completion, and recovery after blocked actions.

This is a research benchmark and prototype, not a production deployment. Its controlled tool environment does not establish integration with external orchestration frameworks.

## Related research

- [Agent Contracts and Governors](/agent-contracts-and-governors/)
- [Agent Cards](/agent-cards/)
