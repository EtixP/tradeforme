# IMPLEMENTATION_HISTORY.md

> **Purpose:** Compact chronological index of important project changes.
>
> Keep this file fast to scan. It should answer:
>
> **What changed, what mattered, and where can I read more if I need the details?**
>
> Git records code changes. `CURRENT_STATE.md` records current truth. Detailed design reasoning belongs in `docs/history/` only when it is worth preserving.

# Rules

1. Append new entries; do not rewrite old history to make the project look cleaner.
2. Keep each milestone entry to roughly **5–10 bullets and under ~200 words**.
3. Record conceptual changes, validation, and scientific/product consequences — not code diffs.
4. If an older conclusion is later invalidated, append a correction.
5. Every implemented milestone gets a compact entry here.
6. Create a detailed `docs/history/<milestone>.md` record **only when future agents are likely to need the reasoning**.
7. `CURRENT_STATE.md`, not this file, is the source for what is currently true.

# When a detailed history record is worth creating

Usually create one for milestones involving:

- transaction-cost methodology;
- benchmark selection or abnormal returns;
- adjusted/unadjusted price handling;
- event normalization;
- statistical methodology;
- leakage / chronology architecture;
- forward-experiment design;
- execution/fill assumptions;
- major architecture changes;
- a correction that materially changes a research conclusion.

Usually **do not** create one for routine work such as:

- formatting/linting;
- simple CI setup;
- small refactors with no methodological consequence;
- straightforward dependency/tooling changes.

# Compact entry template

```markdown
## YYYY-MM-DD — M?.? — <Milestone title>

**Status:** VERIFIED | AWAITING VERIFICATION | BLOCKED
**Commit:** <hash or pending>

- Changed: ...
- Design: ...
- Verified by: ...
- Research/product consequence: ...
- Remaining limitation: ...
- Detailed record: `docs/history/M?.?.md`  # only if one exists
```

---

## 2026-08-30 — M0.0 — Project memory and governance

**Status:** IN PROGRESS  
**Commit:** pending

- Reframed the destination from a historical event-study repo into a live disclosure-intelligence, forward-testing, and paper-execution platform with rigorous research underneath.
- Added a repo-native memory model: `ROADMAP.md`, `CURRENT_STATE.md`, `IMPLEMENTATION_HISTORY.md`, and `AGENTS.md`.
- Adopted one-conceptual-milestone-per-session execution to reduce context loss and scope drift.
- Separated Builder and Verifier roles so substantial milestones are independently challenged before becoming `VERIFIED`.
- Established research-integrity rules: corrections may weaken prior results; unavailable data must not be fabricated; `BLOCKED` is an acceptable outcome.
- No research or production behavior is changed by M0.0.
- Next step: finish integrating these governance files, then run `M0.1 — Baseline research snapshots and verification harness`.
