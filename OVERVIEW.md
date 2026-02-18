# Bricklayer Blueprint - System Overview

## What Is This?

**Bricklayer Blueprint** is a reusable workflow system for building software in small, verified, independently-reviewed pieces called **bricks**.

This is NOT an application or code library. It's a **process framework** that enforces disciplined software development through:

1. **Small Scope** - Work is broken into bite-sized pieces
2. **Clear Contracts** - Each piece has explicit requirements and boundaries
3. **Independent Review** - Work is challenged by a separate reviewer (the "Skeptic")
4. **Automated Verification** - Tools enforce that rules are followed
5. **Loop Prevention** - If you fail 3 times, you must reduce scope

## Core Concept: The Brick

A **brick** is the fundamental unit of work. Each brick:

- Defines exactly which files can be changed (`FILES` section)
- States clear success criteria (`ACCEPTANCE CRITERIA`)
- Specifies required tests (`TEST REQUIREMENTS`)
- Lists what is explicitly forbidden (`OUT OF SCOPE`)

**Key Rule:** Only one brick is active at a time.

## How It Works: The Workflow Loop

```
1. Plan → Write brick spec in spec.md
2. Build → Implement changes (only files listed in spec)
3. Verify → Run verification tools
4. Test → Run tests and capture output
5. Package → Generate skeptic packet
6. Review → Skeptic reviews and gives verdict
7. Complete → If PASS, mark complete and move to next brick
           → If FAIL, fix issues and loop (max 3 times)
```

## The Two Roles

### Builder (Implementer)
- Reads `spec.md` and implements the brick
- Can be a human, Codex, Claude Code, or ChatGPT
- Must follow `PROMPTS/BUILDER_PROMPT.md`
- Runs verification tools after implementing

### Skeptic (Reviewer)
- Independent challenger of the work
- Can be a different AI or human
- Reviews the "skeptic packet" (evidence bundle)
- Must write `skeptic_verdict.md` with `Verdict: PASS` or `Verdict: FAIL`
- Uses `skeptic-gate.md` as review checklist

**Critical Rule:** The Builder cannot approve their own work. The Skeptic is mandatory.

## Key Files and Their Purpose

| File | Purpose |
|------|---------|
| `Readme.md` | User-facing documentation and getting started guide |
| `spec.md` | The current active brick contract |
| `roadmap.md` | Where ideas are planned before becoming bricks |
| `manifest.md` | System protocol rules (brutal honesty mode) |
| `WORKFLOW.md` | Exact command sequence for executing a brick |
| `BUILDER.md` | Hard rules for the Builder role |
| `skeptic-gate.md` | Review checklist for the Skeptic |
| `state.json` | Tracks current brick, status, loop count, test results |
| `context.txt` | Language and test command configuration |
| `PROMPTS/BUILDER_PROMPT.md` | Canonical prompt for AI builders |

## The Verification Tools

Located in `tools/`, these Python scripts enforce the workflow:

| Tool | Purpose |
|------|---------|
| `print_brick_contract.py` | Displays current brick requirements |
| `verify_files_touched.py` | Ensures only allowed files were modified |
| `run_tests_and_capture.py` | Runs tests and saves output |
| `make_skeptic_packet.py` | Bundles evidence for review |
| `update_state.py` | Marks brick as complete (if skeptic passed) |

## The Skeptic Packet

After implementing a brick, the Builder generates a **skeptic packet** containing:

- `skeptic_packet/spec.md` - Copy of the brick contract
- `skeptic_packet/diff.txt` or `diff.patch` - What changed
- `skeptic_packet/test_output.txt` - Test results
- `skeptic_packet/state_excerpt.json` - Relevant state data

The Skeptic reviews this evidence bundle to decide PASS or FAIL.

## Safety Mechanisms

### 1. File Scope Enforcement
`verify_files_touched.py` checks that only files listed in the `FILES` section were modified. If you touch anything else, it fails.

### 2. Loop Count Limiter
If `loop_count >= 3`, you must stop and **rescope** - reduce the brick size before trying again. This prevents infinite failure loops.

### 3. Mandatory Testing
Feature bricks must include test requirements:
- Happy path
- At least 2 edge cases
- At least 1 invalid input test
- At least 1 error handling test
- Side effect verification

### 4. Independent Review
The Skeptic must explicitly write `Verdict: PASS` in `skeptic_verdict.md`. Without this exact line, completion is rejected.

## Workflow States

The `state.json` file tracks:

```json
{
  "current_brick": "Brick name",
  "status": "IN_PROGRESS" | "COMPLETED",
  "loop_count": 0,
  "last_gate_failed": "TESTS" | "SKEPTIC" | null,
  "completed_bricks": [...],
  "last_test_run": {...}
}
```

## Why "Brutal Honesty Mode"?

The `manifest.md` defines **Brutal Honesty Mode**:

- No validation of ideas
- Point out flaws directly
- No motivational fluff
- If uncertain, say "UNCERTAIN"
- Before agreeing, provide one reason it could fail

This forces realistic evaluation and catches problems early.

## Tool Independence

The workflow is designed to work with:
- **Codex** (GitHub Copilot)
- **Claude Code** (Anthropic)
- **ChatGPT** (OpenAI)
- **Manual execution** (human developer)

All follow the same rules, run the same commands, and produce the same outputs.

## Typical Success Flow

1. Write brick in `roadmap.md`
2. Copy to `spec.md` as active brick
3. Run `print_brick_contract.py` to review requirements
4. Implement changes
5. Run `verify_files_touched.py` → PASS
6. Run `run_tests_and_capture.py` → PASS
7. Run `make_skeptic_packet.py` → packet created
8. Skeptic reviews → writes `Verdict: PASS`
9. Run `update_state.py --complete` → brick marked COMPLETED
10. Move to next brick

## Typical Failure Scenarios

### Tests Fail
- `loop_count` increments
- Fix implementation
- Re-run from step 4

### Skeptic Says FAIL
- `loop_count` increments
- Address skeptic concerns
- Regenerate packet
- Request new review

### Loop Count Hits 3
- **STOP**
- Reduce brick scope
- Rewrite `spec.md`
- Reset `loop_count` to 0
- Try smaller brick

## What This Repository Does NOT Include

- No demo application code
- No example source files (beyond workflow tools)
- No compiled artifacts
- No language-specific dependencies

It's a **pure workflow template** - you bring your own project and apply these rules.

## The Philosophy

> **Small bricks pass. Large bricks fail.**

When in doubt, reduce scope. The system is designed to force you to:

1. Think before coding
2. Work incrementally
3. Produce verifiable evidence
4. Accept independent challenge
5. Complete safely

## Getting Started

1. Go to repository root (where `spec.md` exists)
2. Read `Readme.md` for detailed walkthrough
3. Check `roadmap.md` for planning templates
4. Write your first brick in `spec.md`
5. Follow the command sequence in `WORKFLOW.md`
6. Generate skeptic packet
7. Get independent review
8. Complete and move to next brick

## Summary

Bricklayer Blueprint is a **discipline enforcement system** for software development. It prevents scope creep, ensures testing, requires independent review, and forces you to work in small, verifiable increments. It's tool-agnostic and can be applied to any software project in any language.
