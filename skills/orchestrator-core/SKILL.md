---
name: orchestrator-core
description: Use when coordinating a complex, multi-step objective end-to-end - runs the TARS agentic loop (comprehension, specification, planning, routing, execution, verification, memorize or replan, report) by reusing existing GodMode skills, with explicit verification and human-in-the-loop for risky actions.
---

# TARS Orchestrator Core

## Overview

The orchestrator is a **lightweight coordinator**, not a monolith. It turns a complex objective into coordinated work by delegating to existing capabilities. It decides *what*, *when*, *who* (which skill or agent), and *how to verify* — it does not reimplement procedures that already exist as skills.

Prime directive: **understand before acting**, and **never declare work done without evidence**. This skill is the entry point of the TARS agentic loop.

## When to Use

Use this skill when:
- An objective is complex, multi-step, or spans several capabilities.
- Work must be decomposed, routed to skills/agents, and verified.
- A controlled, auditable execution is required (small steps, rollback, report).

Do NOT use this skill for:
- A single trivial action that one existing skill already covers directly.
- Tasks where no planning or verification is warranted.

If YoloMode is active, the loop is compressed but verification and safety checks are never skipped.

## The Universal Loop

Run this loop explicitly. Verification is a mandatory step, never implicit:

```text
Objective
  -> Comprehension
  -> Context
  -> Planning
  -> Decomposition
  -> Selection (skills / agents / tools)
  -> Execution
  -> Observation
  -> Verification        (mandatory)
  -> success?
       yes -> Memorize
       no  -> Replan
  -> Report
```

Each transition is a checkpoint. If verification fails, stop, diagnose, consider rollback, report, then produce a new plan.

## Routing Rules

Route in this order, preferring reuse over creation:

1. **Existing skill** (GodMode / behavioral) that covers the capability.
2. **Existing specialized agent**, only when no skill covers it and the specialization is justified.
3. **New component** as a last resort, and only with explicit validation.

Reuse the following skills instead of reimplementing them:
- `godmode:intent-discovery` — clarify objective, constraints, success criteria.
- `godmode:specification-first` — turn requirements into a behavior spec.
- `godmode:task-planning` — decompose into small, verifiable steps.
- `godmode:delegated-execution` — execute sequentially with review gates.
- `godmode:team-orchestration` — only when independent parts need peer coordination.
- `godmode:completion-gate` — demand evidence before declaring done.
- `godmode:comprehension-check` — surface understanding before heavy changes.
- `godmode:quality-gate` — run relevant quality checks before closing.
- `godmode:error-recovery` — structured diagnosis when attempts fail.
- `godmode:security-protocol` — for any trust-boundary code or data.
- `godmode:review-response` — when processing review feedback.
- `godmode:knowledge-capture` — distill durable lessons.
- `godmode:candidature-email` — prepare/review/track job applications by e-mail (real sending requires explicit human confirmation).

Behavioral discipline is also provided by the Karpathy guidelines (think before coding, simplicity first, surgical changes, goal-driven execution).

## Verification

Verification is mandatory. Follow `godmode:completion-gate`:
- Prefer executing real verification commands and reading their output over assertions.
- State what was requested, what was executed, what resulted, and any errors.
- Capture artifacts and tests as evidence.
- If criteria are not met, list exactly what is missing — do not claim success.

## Memory

Use explicit memorization, never automatic capture. Persist only what is useful, durable, and verified, following the tars-home conventions (type, status, date, source). Distinguish facts, decisions, hypotheses, and obsolete entries. A stable, reusable procedure belongs in a skill, not in raw memory.

## Security & Human-in-the-Loop

Classify actions by risk (read < reversible change < sensitive change < external/irreversible). Higher risk requires stronger validation. Require explicit human validation before irreversible or external actions (deletion, publishing, sensitive system changes, financial operations). When in doubt, prepare and propose rather than execute.

## Guardrails

- Never skip verification.
- Never modify the existing system without explicit validation.
- Prefer minimal, surgical changes; match existing style.
- Do not introduce external dependencies without necessity.
- On repeated failure, invoke `godmode:error-recovery` and propose a corrected plan.
- Stay honest: signal uncertainty, conflicts, and contradictions rather than hiding them.

## Connections

- Entry clarification: `godmode:intent-discovery`
- Design: `godmode:specification-first`
- Planning: `godmode:task-planning`
- Execution: `godmode:delegated-execution`, `godmode:team-orchestration`
- Verification: `godmode:completion-gate`, `godmode:comprehension-check`, `godmode:quality-gate`
- Recovery: `godmode:error-recovery`
- Safety: `godmode:security-protocol`
- Capture: `godmode:knowledge-capture`
