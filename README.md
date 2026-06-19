# dicrp-core
A deterministic, state-machine-driven gatekeeper to eliminate LLM hallucinations and out-of-control agent loops by enforcing input context quality auditing.


PromptOS is a deterministic AI input processing architecture designed to improve large language model reliability by ensuring user intent is fully understood before execution

It introduces a structured input validation and refinement layer between user input and model execution

The system focuses on reducing ambiguity incomplete context and execution failure rather than increasing model intelligence

This document describes each module in detail including function and design rationale


SYSTEM OVERVIEW

PromptOS is composed of a sequential deterministic pipeline

User Input
Intent Detection
Slot Checker
Interactive Refinement Loop
Prompt Compiler
Human Review Gate
LLM Execution


Each stage exists to eliminate uncertainty before execution


MODULE 1 INTENT DETECTION

FUNCTION
This module identifies the high level category of user input such as writing coding translation analysis or conversation

It converts unstructured natural language into a coarse semantic label

OUTPUT
Task Type Label

WHY IT EXISTS
Large language models behave differently depending on task type

Without classification downstream processing becomes inconsistent

This module reduces ambiguity at the earliest stage and allows later modules to apply correct processing rules


MODULE 2 SLOT CHECKER

FUNCTION
This module extracts required structured fields from user input

Core slots include
Role Task Audience Format Length Constraints Language

Each slot is marked as present or missing

It also calculates a completeness score

OUTPUT
Structured slot object
Completeness score

WHY IT EXISTS
Most AI failures come from missing context rather than wrong reasoning

This module ensures all required information is explicitly represented before execution

It transforms vague input into structured data requirements


MODULE 3 INTERACTIVE REFINEMENT LOOP

FUNCTION
When the completeness score is below threshold the system enters a question loop

Each iteration generates exactly one clarification question targeting the most critical missing slot

The loop continues until
Input is complete
Or maximum iteration limit is reached

OUTPUT
User clarified input incrementally improved slot state

WHY IT EXISTS
Users rarely provide complete information in a single message

Traditional systems either guess missing context or overwhelm users with multiple questions

This module balances user experience and information completeness by enforcing minimal cognitive load per interaction


MODULE 4 PROMPT COMPILER

FUNCTION
This module converts structured slot data into a standardized executable prompt format

It organizes information into deterministic sections

Role Task Audience Format Constraints

OUTPUT
Structured final prompt

WHY IT EXISTS
Raw natural language is unstable and varies across users

Compilation ensures consistent prompt structure independent of user writing style

It also improves cross model compatibility


MODULE 5 HUMAN REVIEW GATE

FUNCTION
This module presents the compiled prompt to the user for final confirmation or modification before execution

The user can approve edit or restart refinement

OUTPUT
Final validated prompt

WHY IT EXISTS
Even structured systems can misinterpret user intent

Human review ensures final control remains with the user preventing unintended execution


FLOW CONTROL SYSTEM

FUNCTION
Controls iteration limits and prevents infinite refinement loops

Maximum refinement rounds are set to a fixed number

If limit is reached system forces compilation with available data

WHY IT EXISTS
Without control loops the system may become stuck in infinite questioning cycles reducing usability


DESIGN PRINCIPLES

Principle 1
AI must never execute incomplete input

Principle 2
Only one clarification question is allowed per iteration

Principle 3
System prioritizes clarity over speed

Principle 4
All input must pass structured validation before execution

Principle 5
System behaves as deterministic state machine not probabilistic assistant


SYSTEM PURPOSE SUMMARY

PromptOS does not aim to improve model intelligence

It aims to ensure models only operate on fully understood and structured input

The system functions as a pre execution validation layer for AI systems


FUTURE EXTENSIONS

Prompt Abstract Syntax Tree representation
Prompt Intermediate Representation layer
Conflict detection system
Failure prediction engine
Memory and learning system
Multi model routing system

FINAL STATEMENT
 
PromptOS is a structural input operating layer designed to reduce ambiguity and enforce deterministic understanding before large language model execution
