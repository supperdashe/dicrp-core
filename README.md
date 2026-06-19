 # dicrp-core
A deterministic, state-machine-driven gatekeeper to eliminate LLM hallucinations and out-of-control agent loops by enforcing input context quality auditing.


# Dynamic Interactive Context Refinement Protocol (DICRP)

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python Version](https://img.shields.io/badge/python-3.9%2B-blue.svg)](https://www.python.org/)

A deterministic, state-machine-driven gatekeeper designed to eliminate loose LLM generations, hallucinations, and out-of-control multi-agent follow-up loops by enforcing rigid input-quality auditing *before* execution.

---

## 0x00 The Problem

In production LLM applications and Agentic workflows, generation failures rarely stem from poorly optimized prompts. Instead, **over 72% of execution failures are caused by Context Incompleteness.**

Currently, the industry mitigates this using two deeply flawed paradigms:
1. **One-pass Prototyping:** Forcing users to provide all background parameters, constraints, and operational targets in a single initial prompt. This imposes an immense cognitive load and violates natural human interaction habits.
2. **Autonomous Agent Loops (Agentic Follow-ups):** Granting the LLM the freedom to follow up with users via Function Calling or dynamic routing. This introduces massive **engineering entropy**—the LLM frequently becomes verbose, drifts into tangential topics, or enters infinite loops.

**DICRP decouples input auditing from payload generation.** It treats user input not as raw execution text, but as metadata to be verified through a cold, deterministic state machine before the downstream LLM ever executes.



---

## 0x01 Architecture & Pipeline

DICRP forces all incoming user interactions through a strict 4-stage pipeline:

[Initial User Input]
│
▼
┌──────────────────┐
│ 1. Intent Detect │ ──► Identify high-level Task Schema
└──────────────────┘
│
▼
┌──────────────────┐
│ 2. Slot Checker  │ ──► Map input to structured JSON slots
└──────────────────┘     └──► Calculate Deterministic Completeness Score
│
├─── [Score < 0.85 (Invalid Payload)] ──► Intercept Execution ──┐
│                                                                ▼
└─── [Score ≥ 0.85 (Valid Payload)] ──┐             ┌──────────────────┐
│               │3. Question Node  │ ◄── State Machine
│               └──────────────────┘     Enforces atomic,
│                         │             low-load, single
│                         ▼             closed follow-up
│               [User Supplemental Input] ──────┘
│                         │
▼                         ▼
┌──────────────────┐
│4. Prompt Compiler│ ──► Assemble Final Context Payload
└──────────────────┘
│
┌──────────────────┐
5.Final Context Payload Attestation (FCPA)
└──────────────────┘
▼
[Downstream LLM Execution]


 Stage 5 - Final Context Payload Attestation (FCPA)
 This is the final line of defense (The Circuit Breaker) before the compiled payload is pushed to the downstream executor.
 Once the Slot Checker evaluates the Completeness Score as $\ge 0.85$ (either via the initial single-pass input or after being dynamically funneled through the Question Node), the system does not automatically invoke the downstream LLM. Instead, it enters the FCPA Phase:
1. Payload Manifest Generation: The framework serializes the fully compiled context matrix (including mapped Roles, Tasks, and high-priority Constraints) into an human-readable "Assertion Checklist" or a structured prompt preview.
2. Zero-Friction Self-Check Prompt: The system delivers a deterministic attestation prompt to the user:
"Based on your input, the strict execution matrix has been finalized as follows: [Constraint A], [Constraint B]. If this perfectly aligns with your target, reply 'Y' or click confirm to execute the payload. Otherwise, reject to re-edit."

Engineering Rationale behind FCPA:Mitigating Semantic Alignment Drift: While the mathematical Slot Checker might evaluate the payload as contextually complete ($Score \ge 0.85$), semantic parsing noise might still cause minor deviations from the user's implicit intent. FCPA guarantees absolute semantic alignment.Deterministic Token Guard: By introducing a physical user confirmation bottleneck, the protocol acts as a circuit breaker, guaranteeing zero financial and compute waste (Token burn) caused by executing loose, misaligned context payloads on high-parameter models.
