# Bricklayer Blueprint - Quick Reference

## One-Line Summary
A workflow system that forces you to build software in small, tested, independently-reviewed pieces.

---

## Key Terms in 5 Seconds Each

| Term | What It Means |
|------|---------------|
| **Brick** | One small unit of work with clear boundaries |
| **Builder** | The one doing the work (you or an AI) |
| **Skeptic** | Independent reviewer who challenges the work |
| **Skeptic Packet** | Evidence bundle sent for review |
| **Loop Count** | How many times you've failed the current brick (max 3) |
| **spec.md** | The contract for the current active brick |
| **roadmap.md** | Where you plan bricks before working on them |
| **state.json** | Tracks progress, status, and test results |

---

## The 5-Step Workflow

```
1. PLAN    → Write brick in spec.md
2. BUILD   → Code only files listed in FILES section
3. VERIFY  → Run tools to check rules followed
4. TEST    → Run tests and capture output
5. REVIEW  → Get Skeptic approval (Verdict: PASS)
```

If any step fails, fix and retry (max 3 times, then reduce scope).

---

## Essential Commands (In Order)

```bash
# 1. See what you're building
python3 tools/print_brick_contract.py

# 2. (First time only) If no .git folder
python3 tools/verify_files_touched.py --snapshot-init

# 3. Make your changes to allowed files

# 4. Verify you only touched allowed files
python3 tools/verify_files_touched.py

# 5. Run tests
python3 tools/run_tests_and_capture.py

# 6. Package evidence for review
python3 tools/make_skeptic_packet.py

# 7. Get Skeptic to review and write skeptic_verdict.md

# 8. If Skeptic said PASS, complete the brick
python3 tools/update_state.py --complete
```

---

## The Golden Rules

1. **One brick at a time** - Never work on multiple bricks simultaneously
2. **Only touch listed files** - If it's not in the FILES section, don't change it
3. **Tests are mandatory** - Feature bricks must include test requirements
4. **Skeptic approval required** - Builder cannot approve their own work
5. **3 strikes and rescope** - If loop_count >= 3, reduce the brick size
6. **Small wins fast** - Smaller bricks pass more reliably

---

## File Purpose Cheat Sheet

### Planning & Contracts
- `roadmap.md` - Where ideas become bricks (planning)
- `spec.md` - Current active brick contract
- `manifest.md` - System rules (brutal honesty mode)

### Process Documentation
- `OVERVIEW.md` - Detailed system explanation (start here)
- `Readme.md` - Getting started guide
- `WORKFLOW.md` - Exact command sequence
- `BUILDER.md` - Rules for the Builder role
- `skeptic-gate.md` - Review checklist for Skeptic

### State & Configuration
- `state.json` - Current brick status and history
- `context.txt` - Language and test command

### Evidence & Review
- `skeptic_packet/` - Evidence bundle for review
- `skeptic_verdict.md` - Skeptic's decision (PASS or FAIL)

### Tools
- `tools/*.py` - Verification and workflow enforcement scripts
- `PROMPTS/BUILDER_PROMPT.md` - Canonical prompt for AI builders

---

## Decision Tree: "What Do I Do?"

```
Starting fresh?
└─ Read Readme.md → Write PRD in roadmap.md → Create first brick in spec.md

Have a brick in spec.md?
└─ Follow the 5-step workflow (Plan → Build → Verify → Test → Review)

Tests failed?
└─ Fix code → Re-run from step 4 (Verify)

Skeptic said FAIL?
└─ Address concerns → Regenerate packet → Get new review

loop_count >= 3?
└─ STOP → Reduce brick scope → Rewrite spec.md → Start fresh

Brick completed?
└─ Choose next brick from roadmap.md → Update spec.md → Repeat
```

---

## Quick Troubleshooting

| Problem | Solution |
|---------|----------|
| "Not in repo root" error | cd to folder containing spec.md |
| verify_files_touched fails | You modified files not in FILES section - revert them |
| Tests won't run | Check context.txt for correct TEST_COMMAND |
| Skeptic keeps failing | Brick too large - reduce scope and simplify |
| loop_count hit 3 | Must rescope - split brick into smaller pieces |
| Can't complete brick | Need exact line "Verdict: PASS" in skeptic_verdict.md |

---

## Success Checklist

Before marking a brick complete, ensure:

- [ ] Only files in FILES section were modified
- [ ] All tests pass
- [ ] Skeptic packet generated successfully
- [ ] Skeptic reviewed and wrote `Verdict: PASS`
- [ ] `update_state.py --complete` succeeded

---

## Tool-Agnostic Design

Works with:
- ✅ GitHub Copilot (Codex)
- ✅ Claude Code (Anthropic)
- ✅ ChatGPT (OpenAI)
- ✅ Manual execution (human)

All tools follow the same workflow and rules.

---

## Philosophy: The Brick Principle

> **Small bricks pass. Large bricks fail.**

When scope feels too big, it probably is. Break it down further.

---

## Next Steps

1. **New user?** → Read `OVERVIEW.md` for detailed explanation
2. **Ready to build?** → Read `Readme.md` for walkthrough
3. **Need commands?** → Check `WORKFLOW.md`
4. **Starting a brick?** → Use templates in `roadmap.md`
5. **Need AI prompts?** → Use `PROMPTS/BUILDER_PROMPT.md`

---

## Emergency Reset

Lost or confused? Start fresh:

```bash
# Verify you're in repo root
python3 -c "from pathlib import Path; print('OK' if Path('spec.md').exists() else 'NOT ROOT')"

# Check current state
cat state.json

# See current brick
python3 tools/print_brick_contract.py
```

Then follow the 5-step workflow from the top.
