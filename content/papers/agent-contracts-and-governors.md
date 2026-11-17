---
title: "Agent Contracts and Governors: A Runtime Governance Framework for Tool-Using LLM Agents"
date: 2026-11-08
url: /agent-contracts-and-governors/
author: ["J. Carlos Urteaga-Reyesvera"]
description: "Executable contracts and a runtime governor for controlling tool use, side effects, and audit evidence in LLM agents."
summary: "Executable contracts and a runtime governor for controlling tool use, side effects, and audit evidence in LLM agents."
status: under_review
homeOrder: 1
bibtex: "/citations/agent-contracts-and-governors.bib"
repository: "https://github.com/CarlosUrteaga/agent-contracts-benchmark"
---

This research manuscript is currently under review.

## Overview

Agent Contracts define executable obligations over tool use, memory operations, side effects, and audit evidence. An Agent Governor checks proposed actions against those obligations during execution, using advisory, guarded, and strict enforcement modes.

The research examines how these modes balance prevention, recovery, and useful task completion. Evaluation uses a frozen benchmark with 21 scenarios, controlled local tools, repeated campaigns, and multiple LLM execution conditions. These experiments study runtime governance in a controlled research setting; they do not establish guarantees for arbitrary production environments.

## Related material

- [Agent Contracts Benchmark](/projects/agent-contracts/)
- [Agent Cards](/agent-cards/)
