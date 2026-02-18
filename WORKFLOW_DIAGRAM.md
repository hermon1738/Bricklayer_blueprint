# Bricklayer Blueprint - Visual Workflow Diagram

## The Complete Brick Lifecycle

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         PLANNING PHASE                                  │
│                                                                         │
│  ┌──────────┐        ┌───────────┐        ┌──────────┐               │
│  │  Idea    │───────▶│  Write    │───────▶│  Create  │               │
│  │          │        │  PRD in   │        │  Brick   │               │
│  │          │        │ roadmap   │        │  in      │               │
│  └──────────┘        └───────────┘        │ spec.md  │               │
│                                            └──────────┘               │
└─────────────────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                       IMPLEMENTATION PHASE                              │
│                                                                         │
│  ┌────────────────────────────────────────────────────────────────┐   │
│  │  STEP 1: Print Contract                                        │   │
│  │  $ python3 tools/print_brick_contract.py                       │   │
│  └────────────────────────────────────────────────────────────────┘   │
│                                   │                                    │
│                                   ▼                                    │
│  ┌────────────────────────────────────────────────────────────────┐   │
│  │  STEP 2: (Optional) Snapshot Init (first time, no .git)        │   │
│  │  $ python3 tools/verify_files_touched.py --snapshot-init       │   │
│  └────────────────────────────────────────────────────────────────┘   │
│                                   │                                    │
│                                   ▼                                    │
│  ┌────────────────────────────────────────────────────────────────┐   │
│  │  STEP 3: Implement Changes                                     │   │
│  │  • Only touch files listed in FILES section                    │   │
│  │  • Write tests                                                 │   │
│  │  • Follow acceptance criteria                                  │   │
│  └────────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                       VERIFICATION PHASE                                │
│                                                                         │
│  ┌────────────────────────────────────────────────────────────────┐   │
│  │  STEP 4: Verify File Scope                                     │   │
│  │  $ python3 tools/verify_files_touched.py                       │   │
│  └────────────────────────────────────────────────────────────────┘   │
│                                   │                                    │
│                         ┌─────────┴─────────┐                          │
│                         │                   │                          │
│                      PASS                FAIL                           │
│                         │                   │                          │
│                         │                   ▼                          │
│                         │          ┌──────────────────┐                │
│                         │          │ Fix: Revert      │                │
│                         │          │ unauthorized     │                │
│                         │          │ file changes     │                │
│                         │          └──────────────────┘                │
│                         │                   │                          │
│                         │                   │ (retry)                  │
│                         └───────────────────┘                          │
│                                   │                                    │
│                                   ▼                                    │
│  ┌────────────────────────────────────────────────────────────────┐   │
│  │  STEP 5: Run Tests                                             │   │
│  │  $ python3 tools/run_tests_and_capture.py                      │   │
│  └────────────────────────────────────────────────────────────────┘   │
│                                   │                                    │
│                         ┌─────────┴─────────┐                          │
│                         │                   │                          │
│                      PASS                FAIL                           │
│                         │                   │                          │
│                         │                   ▼                          │
│                         │          ┌──────────────────┐                │
│                         │          │ loop_count++     │                │
│                         │          │ Fix tests/code   │                │
│                         │          └──────────────────┘                │
│                         │                   │                          │
│                         │                   │ (retry)                  │
│                         │                   │                          │
│                         │          ┌────────▼────────┐                 │
│                         │          │ loop_count >= 3?│                 │
│                         │          └────────┬────────┘                 │
│                         │                   │                          │
│                         │                 YES                          │
│                         │                   │                          │
│                         │                   ▼                          │
│                         │          ┌──────────────────┐                │
│                         │          │ RESCOPE:         │                │
│                         │          │ Reduce brick     │                │
│                         │          │ scope, rewrite   │                │
│                         │          │ spec.md          │                │
│                         │          └──────────────────┘                │
│                         │                                              │
│                         └──────────────────────────────────────────────┤
│                                   │                                    │
│                                   ▼                                    │
│  ┌────────────────────────────────────────────────────────────────┐   │
│  │  STEP 6: Generate Skeptic Packet                               │   │
│  │  $ python3 tools/make_skeptic_packet.py                        │   │
│  │                                                                 │   │
│  │  Creates:                                                       │   │
│  │  • skeptic_packet/spec.md                                      │   │
│  │  • skeptic_packet/diff.txt                                     │   │
│  │  • skeptic_packet/test_output.txt                              │   │
│  │  • skeptic_packet/state_excerpt.json                           │   │
│  └────────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                         REVIEW PHASE                                    │
│                                                                         │
│  ┌────────────────────────────────────────────────────────────────┐   │
│  │  Skeptic Reviews Packet Using skeptic-gate.md                  │   │
│  │                                                                 │   │
│  │  Reviews:                                                       │   │
│  │  1. Flaw Hunt - What's broken or inefficient?                  │   │
│  │  2. Blind Spot Hunt - What scenarios are missing?              │   │
│  │  3. Scaling Check - What fails at 10x load?                    │   │
│  │  4. Failure-First Challenge - Why could this fail?             │   │
│  │  5. Honesty Constraint - No fluff, brutal truth                │   │
│  └────────────────────────────────────────────────────────────────┘   │
│                                   │                                    │
│                         ┌─────────┴─────────┐                          │
│                         │                   │                          │
│                      PASS                FAIL                           │
│                         │                   │                          │
│  ┌────────────────────────────┐   ┌────────────────────┐              │
│  │ Skeptic writes:            │   │ Skeptic writes:    │              │
│  │ skeptic_verdict.md         │   │ skeptic_verdict.md │              │
│  │                            │   │                    │              │
│  │ Verdict: PASS              │   │ Verdict: FAIL      │              │
│  │ (exact line required)      │   │ + reasons          │              │
│  └────────────────────────────┘   └────────────────────┘              │
│                  │                          │                          │
│                  │                          ▼                          │
│                  │                 ┌──────────────────┐                │
│                  │                 │ loop_count++     │                │
│                  │                 │ Address feedback │                │
│                  │                 │ Regenerate packet│                │
│                  │                 └──────────────────┘                │
│                  │                          │                          │
│                  │                          │ (retry)                  │
│                  │                          │                          │
│                  │                 ┌────────▼────────┐                 │
│                  │                 │ loop_count >= 3?│                 │
│                  │                 └────────┬────────┘                 │
│                  │                          │                          │
│                  │                        YES                          │
│                  │                          │                          │
│                  │                          ▼                          │
│                  │                 ┌──────────────────┐                │
│                  │                 │ RESCOPE:         │                │
│                  │                 │ Reduce brick     │                │
│                  │                 │ scope, rewrite   │                │
│                  │                 │ spec.md          │                │
│                  │                 └──────────────────┘                │
│                  │                                                     │
│                  └─────────────────────────────────────────────────────┤
│                                   │                                    │
└─────────────────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                       COMPLETION PHASE                                  │
│                                                                         │
│  ┌────────────────────────────────────────────────────────────────┐   │
│  │  STEP 7: Mark Complete                                         │   │
│  │  $ python3 tools/update_state.py --complete                    │   │
│  │                                                                 │   │
│  │  Checks:                                                        │   │
│  │  • Does skeptic_verdict.md exist?                              │   │
│  │  • Does it contain "Verdict: PASS"?                            │   │
│  │                                                                 │   │
│  │  If YES:                                                        │   │
│  │  • Updates state.json status to "COMPLETED"                    │   │
│  │  • Resets loop_count to 0                                      │   │
│  │  • Adds brick to completed_bricks list                         │   │
│  │                                                                 │   │
│  │  If NO:                                                         │   │
│  │  • Completion rejected                                         │   │
│  │  • loop_count++ (if skeptic failed)                            │   │
│  └────────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────┘
                                   │
                                   ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                         NEXT BRICK                                      │
│                                                                         │
│  ┌────────────────────────────────────────────────────────────────┐   │
│  │  Select next brick from roadmap.md                             │   │
│  │  Update spec.md with new brick                                 │   │
│  │  Return to IMPLEMENTATION PHASE                                │   │
│  └────────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## Key Decision Points

### File Verification Gate
- **Purpose:** Ensures only approved files were modified
- **Pass:** Continue to tests
- **Fail:** Revert unauthorized changes and retry

### Test Gate
- **Purpose:** Validates code works correctly
- **Pass:** Continue to package
- **Fail:** Increment loop_count, fix code/tests, retry
- **Loop Count >= 3:** Stop and rescope

### Skeptic Gate
- **Purpose:** Independent quality review
- **Pass:** Continue to completion
- **Fail:** Increment loop_count, address feedback, retry
- **Loop Count >= 3:** Stop and rescope

---

## The Loop Count Safety Mechanism

```
loop_count = 0  │  First attempt - fresh start
                │
loop_count = 1  │  First failure - minor adjustments
                │
loop_count = 2  │  Second failure - major revision needed
                │
loop_count = 3  │  ⚠️  RESCOPE REQUIRED ⚠️
                │  Brick is too large or complex
                │  Must reduce scope before continuing
```

---

## Two Roles, One Workflow

```
┌──────────────────────────────────────────────────────────────┐
│                         BUILDER                              │
│                                                              │
│  • Reads spec.md                                            │
│  • Implements changes                                        │
│  • Runs verification tools                                   │
│  • Generates skeptic packet                                  │
│  • Cannot approve own work                                   │
│                                                              │
│  Can be: Codex, Claude Code, ChatGPT, Human                 │
└──────────────────────────────────────────────────────────────┘
                            │
                            ▼ (sends skeptic packet)
┌──────────────────────────────────────────────────────────────┐
│                         SKEPTIC                              │
│                                                              │
│  • Receives skeptic packet                                   │
│  • Challenges the work                                       │
│  • Uses skeptic-gate.md checklist                           │
│  • Writes skeptic_verdict.md                                │
│  • Must write "Verdict: PASS" for approval                  │
│                                                              │
│  Can be: ChatGPT, Claude, Gemini, Human                     │
└──────────────────────────────────────────────────────────────┘
```

---

## State Transitions

```
┌─────────────┐
│   INITIAL   │  (new brick added to spec.md)
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ IN_PROGRESS │  (implementation started)
└──────┬──────┘
       │
       │  [Tests PASS + Skeptic PASS + complete command]
       │
       ▼
┌─────────────┐
│  COMPLETED  │  (brick finished, loop_count reset to 0)
└─────────────┘
```

---

## The Skeptic Packet Contents

```
skeptic_packet/
│
├── spec.md              ← Copy of the brick contract
│   • FILES list
│   • ACCEPTANCE CRITERIA
│   • TEST REQUIREMENTS
│   • OUT OF SCOPE
│
├── diff.txt             ← What changed
│   or diff.patch
│   • Shows all modifications
│   • Proves file scope compliance
│
├── test_output.txt      ← Test results
│   • Full test run output
│   • Pass/fail status
│   • Coverage info (if available)
│
└── state_excerpt.json   ← Context
    • current_brick
    • loop_count
    • last_gate_failed
    • last_test_run status
```

---

## File Dependency Map

```
                         roadmap.md
                              │
                              │ (planning)
                              ▼
                          spec.md ◄────── manifest.md
                              │           (brutal honesty rules)
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
        ▼                     ▼                     ▼
   BUILDER.md          WORKFLOW.md          skeptic-gate.md
   (builder rules)     (command seq)        (review checklist)
        │                     │                     │
        │                     │                     │
        ▼                     ▼                     ▼
   [Implementation]      [Verification]        [Review]
        │                     │                     │
        └─────────────────────┼─────────────────────┘
                              │
                              ▼
                         state.json
                         (tracks progress)
```

---

## Success vs Failure Paths

### Success Path (Happy Path)
```
Plan → Build → Verify ✓ → Test ✓ → Package → Review ✓ → Complete ✓
│                                                                  │
└──────────────────── 1 iteration ─────────────────────────────────┘
```

### Failure Path (Recovery)
```
Plan → Build → Verify ✓ → Test ✗ → Fix → Test ✓ → Package → Review ✓ → Complete ✓
│                                                                                  │
└────────────────────────────── 2 iterations ─────────────────────────────────────┘
```

### Rescope Path (Too Complex)
```
Plan → Build → Verify ✓ → Test ✗ → Fix → Test ✗ → Fix → Test ✗
│                                                             │
│                         (loop_count = 3)                    │
│                                                             │
└─────────── STOP → Reduce Scope → Rewrite spec.md ──────────┘
                           │
                           └─── Start fresh with smaller brick
```

---

## Command Flow Visualization

```
START
  │
  ▼
┌─────────────────────────────────────────────┐
│ python3 tools/print_brick_contract.py      │  ← See requirements
└─────────────────────────────────────────────┘
  │
  ▼
┌─────────────────────────────────────────────┐
│ [OPTIONAL - First time only]                │
│ python3 tools/verify_files_touched.py       │  ← Create baseline
│         --snapshot-init                     │
└─────────────────────────────────────────────┘
  │
  ▼
┌─────────────────────────────────────────────┐
│ [IMPLEMENT YOUR CHANGES]                    │  ← Code + tests
└─────────────────────────────────────────────┘
  │
  ▼
┌─────────────────────────────────────────────┐
│ python3 tools/verify_files_touched.py       │  ← Check file scope
└─────────────────────────────────────────────┘
  │
  ▼
┌─────────────────────────────────────────────┐
│ python3 tools/run_tests_and_capture.py      │  ← Run tests
└─────────────────────────────────────────────┘
  │
  ▼
┌─────────────────────────────────────────────┐
│ python3 tools/make_skeptic_packet.py        │  ← Package evidence
└─────────────────────────────────────────────┘
  │
  ▼
┌─────────────────────────────────────────────┐
│ [SKEPTIC REVIEWS]                           │  ← Independent review
│ Writes skeptic_verdict.md                   │
└─────────────────────────────────────────────┘
  │
  ▼
┌─────────────────────────────────────────────┐
│ python3 tools/update_state.py --complete    │  ← Mark done
└─────────────────────────────────────────────┘
  │
  ▼
NEXT BRICK
```

---

## Quick Troubleshooting Flowchart

```
                        ┌───────────────┐
                        │ Issue?        │
                        └───────┬───────┘
                                │
                ┌───────────────┼───────────────┐
                │               │               │
                ▼               ▼               ▼
        ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
        │ Tests fail   │  │ Verify fails │  │ Skeptic fails│
        └──────┬───────┘  └──────┬───────┘  └──────┬───────┘
               │                 │                 │
               │                 │                 │
               ▼                 ▼                 ▼
        ┌──────────────┐  ┌──────────────┐  ┌──────────────┐
        │ Fix code/    │  │ Revert       │  │ Address      │
        │ tests        │  │ unauthorized │  │ feedback     │
        │              │  │ files        │  │              │
        └──────┬───────┘  └──────┬───────┘  └──────┬───────┘
               │                 │                 │
               └────────┬────────┴────────┬────────┘
                        │                 │
                        ▼                 ▼
                ┌──────────────┐  ┌──────────────┐
                │ loop_count   │  │ loop_count   │
                │ < 3?         │  │ >= 3?        │
                └──────┬───────┘  └──────┬───────┘
                       │                 │
                     YES              YES
                       │                 │
                       ▼                 ▼
                ┌──────────────┐  ┌──────────────┐
                │ Retry        │  │ RESCOPE:     │
                │ from verify  │  │ Reduce scope,│
                │              │  │ rewrite      │
                │              │  │ spec.md      │
                └──────────────┘  └──────────────┘
```

---

## Summary

This visual guide shows the complete workflow from idea to completed brick, including all decision points, safety mechanisms, and recovery paths. The system is designed to enforce small, verifiable increments with independent review at every step.
