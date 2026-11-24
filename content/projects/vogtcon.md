---
title: "vogtcon"
date: 2026-11-08
url: /projects/vogtcon/
tags: ["AgentOps", "AI Governance", "Runtime Verification", "Python"]
author: "Carlos Urteaga"
description: "A Python library implementing runtime contracts and an execution governor for tool-using LLM agents."
summary: "A Python library implementing runtime contracts and an execution governor for tool-using LLM agents."
hideMeta: true
homeOrder: 1
repository: "https://github.com/CarlosUrteaga/vogtcon"
---

`vogtcon` is the Python library implementing runtime governance and contract enforcement for tool-using LLM agents.

It provides an active **Agent Governor** that intercepts proposed tool calls, verifies preconditions and invariants, and enforces execution policies across four operational modes: *No-Contract*, *Advisory*, *Guarded*, and *Strict*.

### Core Capabilities

- **Contract Enforcement:** Executable specifications over tool usage, argument constraints, and external side effects.
- **Intervention Engine:** Intercepts agent actions before execution, allowing safe operations and blocking policy violations.
- **Guided Recovery:** Returns actionable feedback when actions are blocked, enabling agents to self-correct rather than abort.
- **Audit Logging:** Captures structured operational evidence for downstream traceability and compliance.

### Related Research & Artifacts

- **[Agent Contracts and Governors](/agent-contracts-and-governors/)**: Research paper formalizing the runtime governance framework.
- **[Agent Contracts Benchmark](/projects/agent-contracts/)**: Empirical benchmark evaluating `vogtcon` across 21 nominal and adversarial scenarios.
- **[Agent Cards](/agent-cards/)**: Operational documentation standard for AI agents.
