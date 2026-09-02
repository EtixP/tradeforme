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

## 2026-08-30 — M0.0 — Builder acceptance review and handoff

**Status:** AWAITING VERIFICATION
**Commit:** pending (inspected branch tip: `99bf1b2d5429ebba427ce93c0dbbe7a3e0616984`)

- Verified all four governance files are tracked at repository root and have distinct roadmap, current-state, history, and operating-contract responsibilities.
- Inspected the branch, source tree, scripts, tests, and research documentation; the governance summary matches the implemented high-level capabilities and known methodological limitations.
- Corrected the stale inspected-commit metadata from `486f48d` to the current branch tip, `99bf1b2`.
- Verified the repository-local suite: `138 passed` with `.venv/bin/pytest -q`; the README's `140 passing` count is known stale documentation left unchanged under the M0.0 non-goal.
- Confirmed the original M0.0 commit and this handoff touch governance files only; no research logic, tests, backtests, pipelines, or production behavior changed.
- No research outputs were rerun because M0.0 changes no methodology or executable behavior.
- Next step is an independent M0.0 Verifier session; M0.1 has not started.

## 2026-08-30 — M0.0 — Independent verification

**Status:** VERIFIED
**Commit:** pending (verified executable branch tip: `99bf1b2d5429ebba427ce93c0dbbe7a3e0616984`)

- Independently checked all four root governance documents against the tracked tree, implementation, tests, scripts, and research documentation; their responsibilities and repository summary are accurate.
- Confirmed `99bf1b2` added only the four governance files and pending handoff changes are governance-only; no research or production behavior changed.
- Confirmed M0.1 remains unstarted: no baseline artifacts or research-state verification harness exist.
- Passed targeted governance/capability checks and 85 relevant tests; the full suite passed with `138 passed`.
- Reproduced the seven-category summary, synthetic and real-buyback learner results, and buyback time-aware walk-forward headline values.
- Corroborated the documented limitations: constant costs, raw returns, implicit price-adjustment behavior, title-based categories, and no live/forward/paper-execution path.
- No affected research artifact required regeneration because M0.0 changes documentation and process only.
- M0.0 is accepted as `VERIFIED`; M0.1 is still `NOT STARTED` and belongs in a separate Builder session.

## 2026-08-30 — M0.1 — Baseline snapshots and verification harness

**Status:** AWAITING VERIFICATION
**Commit:** pending

- Added `python -m scripts.verify_research_state` to generate or verify four pre-revision JSON summaries and a provenance manifest.
- Pinned only the public buyback receipt-number/filing-time slice needed for intraday reproduction, avoiding dependence on the ignored 688 MB SQLite database.
- Recorded hashes for artifacts, tracked CSV inputs, runtime versions, and generator sources; learner paths fix `random_state=0`.
- Verification checks hashes and structural/arithmetic invariants, not equality between future corrected outputs and old headline values.
- Byte-for-byte regeneration passed; targeted tests passed `5/5`, and the full suite passed `143/143`.
- Reproduced cross-category, shareholder-change, synthetic learner, real-buyback learner, and buyback intraday outputs with no material differences from the prior documented results.
- Research methodology and conclusions are unchanged; the snapshot now makes later cost/benchmark corrections attributable and reviewable.
- Next step: independent M0.1 verification. M0.2 has not started.

## 2026-08-30 — M0.1 — Independent verification returned

**Status:** IN PROGRESS
**Commit:** pending

- Passed the five targeted tests and full suite (`143 passed`); default and `--check-inputs` verification also passed.
- Regenerated every artifact and the manifest byte-for-byte in a fresh directory without SQLite; a fresh local-DB capture also matched the pinned 3,604-row filing-time slice.
- Reproduced the cross-category, learner, shareholder-change, and intraday headline results with no methodology or conclusion change.
- Falsified semantic verification: changing `supply_contract.n_events` from `8,585` to `1` and updating its manifest hash still returned `internal_consistency: passed` despite contradictory nested counts.
- Found incomplete source provenance: `src/kdtb/backtest/metrics.py` directly produces category statistics but is absent from the generator-source manifest, so `--check-inputs` misses that source drift.
- Required correction: enforce cross-category count/fold arithmetic and complete source-hash coverage, with regression tests for both cases.
- M0.1 is not verified and returns to `IN PROGRESS`; M0.2 remains untouched.

## 2026-08-30 — M0.1 — Builder correction after verification failure

**Status:** AWAITING VERIFICATION
**Commit:** pending

- Added category reconciliation across `n_events`, unique stocks, aggregate metrics, execution scenarios, market partitions, and generated/scored/positive fold counts.
- Added regression attacks proving rehashed `n_events=1` and fold-count contradictions now fail semantic verification.
- Added `src/kdtb/backtest/metrics.py` to generator provenance and a regression test proving `--check-inputs` detects its drift.
- Regenerated the manifest; the four research JSON artifacts and headline conclusions remain unchanged.
- Targeted baseline tests passed `8/8`; the full suite passed `146/146`.
- Fresh local-DB capture matched the pinned 3,604-row input, and DB/pinned-input regenerations were byte-identical to the committed snapshot.
- Reproduced all affected research outputs with no material differences; no methodology changed.
- Next step: fresh independent verification of corrected M0.1. M0.2 remains untouched.

## 2026-08-30 — M0.1 — Corrected implementation independently verified

**Status:** VERIFIED
**Commit:** pending (verified executable branch tip: `99bf1b2d5429ebba427ce93c0dbbe7a3e0616984`)

- Independently replayed the prior rehashed `supply_contract.n_events=1` attack; semantic verification now rejects it.
- Confirmed category aggregate, execution, market, and fold counts reconcile, including scored/positive fold arithmetic.
- Confirmed `src/kdtb/backtest/metrics.py` is provenance-hashed and its drift regression passes.
- Default and `--check-inputs` verification passed; targeted baseline tests passed `8/8` and the full suite passed `146/146`.
- Pinned-input and fresh local-DB regeneration were byte-identical to the recorded snapshot; the DB capture matched all 3,604 pinned rows.
- Reproduced cross-category, learner, shareholder-change, and intraday outputs with unchanged headline values and conclusions.
- M0.1 changes no research methodology and is accepted as `VERIFIED`; M0.2 remains `NOT STARTED`.

## 2026-08-31 — M0.2 — Historically correct transaction costs

**Status:** AWAITING VERIFICATION
**Commit:** pending (executable branch tip: `99bf1b2d5429ebba427ce93c0dbbe7a3e0616984`)

- Verified the 2021–2026 KOSPI/KOSDAQ sell-tax schedule against Korea's National Law Information Center; KOSPI securities transaction tax and 0.15% special rural tax remain separate.
- Replaced the optional flat-tax API with mandatory buy date, sell date, and market; added auditable components and explicit 5bps-per-side slippage semantics.
- Preserved exact t0/t+1/t+2/t+5 observation dates in new event studies and reconstructed them for eight existing files (2,241 stocks; 32,450 rows; no recorded close lacked a date).
- Migrated all known research, learner, subgroup, intraday, filtered-study, walk-forward, and paper-backtest cost consumers; missing date provenance now fails instead of falling back.
- Preserved the immutable M0.1 outputs through an explicitly legacy flat-cost path; all four pre-revision artifacts regenerated byte-for-byte.
- Added a deterministic, input/source-hashed M0.2 comparison. Category means moved −1.1 to −1.8bps and all seven category verdicts stayed unchanged; no parameter was tuned.
- Recorded the fragile learner change: buyback selection lift moved −1.66bps → +2.23bps and crossed its mechanical verdict threshold, while whole-category realistic mean fell to +0.14%.
- Passed targeted tests (`37 passed`), the full suite (`165 passed`), and every affected research script; detailed rationale is in `docs/history/M0.2.md`.

## 2026-08-31 — M0.2 — Independent verification

**Status:** VERIFIED
**Commit:** pending (verified executable branch tip: `99bf1b2d5429ebba427ce93c0dbbe7a3e0616984`)

- Independently confirmed every 2021–2026 tax boundary and the KOSPI/KOSDAQ component split from official Korean law sources.
- Falsified boundary, chronology, unsupported-market, missing-provenance, and slippage behavior; focused tests passed `39/39` and the full suite passed `165/165`.
- Audited all eight migrated CSVs: 32,450 price-bearing rows across 2,241 stocks, no prior field changed, and no recorded close lacks a date.
- A fresh-source sample matched 12/12 date sequences across all years and both markets; one bonus-issue price-scale drift remains explicitly deferred to M0.4.
- Regenerated the source-hashed M0.2 comparison byte-for-byte and reran every migrated research consumer; the four M0.1 artifacts also remained byte-identical.
- Reproduced the −1.1 to −1.8bps category changes, unchanged seven verdicts, +2.23bps fragile learner lift, and unchanged +0.317% timing delta.
- No parameters, event definitions, benchmark logic, or M0.3 work changed. M0.2 is accepted as `VERIFIED`.

## 2026-08-31 — M0.3 — Benchmark-adjusted event returns

**Status:** AWAITING VERIFICATION
**Commit:** pending (executable branch tip: `99bf1b2d5429ebba427ce93c0dbbe7a3e0616984`)

- Fixed benchmark assignment prospectively: KOSPI → KOSPI, KOSDAQ → KOSDAQ; documented KRX definitions and Naver Finance as the pinned daily-history delivery source.
- Added a shared exact-date alignment API. Missing index dates remain missing or raise; no offset matching, filling, nearest-date substitution, or zero fallback exists.
- Pinned 1,226 closes per market and strictly enriched nine event-study files (34,686 rows); all 103,624 price-bearing horizon values passed independent formula recomputation.
- Headline category, learner, intraday, subgroup, filtered, walk-forward, and paper-attribution paths now expose raw and abnormal results where appropriate; raw cash PnL remains separate.
- Preserved M0.1/M0.2 historical results through explicit legacy raw modes and regenerated all provenance manifests/comparisons in dependency order.
- Recorded material corrections: buyback realistic +0.143% raw → −0.028% abnormal; learner lift +0.022% → −0.037%; time-aware mean +0.458% → +0.164%; shareholder change strengthens to −1.396% abnormal.
- Added the deterministic source/input-hashed M0.3 comparison and detailed rationale in `docs/history/M0.3.md`; no benchmark or parameter was searched to recover a result.
- Targeted tests passed `22/22`, full suite passed `176/176`, all affected research scripts reran, and `git diff --check` passed.

## 2026-08-31 — M0.3 — Independent verification returned

**Status:** IN PROGRESS
**Commit:** pending

- Fresh Naver retrieval matched all 2,452 pinned closes; all 34,686 enriched rows and 103,624 price-bearing horizons reconciled to exact-date formulas.
- The M0.3 artifact regenerated byte-for-byte, all affected research outputs reproduced, focused tests passed `18/18`, and the full suite passed `176/176`.
- Falsified CSV provenance: enrichment stripped leading zeros from 32,510 `corp_code` and 10,633 `stock_code` cells and rewrote existing return serialization.
- Falsified strict missing-data handling: normalized `NaN` closes pass validation and return `benchmark_alignment=complete` under `strict=True`.
- Found historical-provenance rewriting: the verified M0.2 artifact changed from SHA-256 `b529d30e…` to `8bd58f0d…`, and the M0.1 manifest now tracks M0.3 inputs despite explicit immutability instructions.
- Required corrections: lossless enrichment, finite-value validation with regression tests, and restored immutable M0.1/M0.2 provenance.
- M0.3 is not verified and returns to `IN PROGRESS`; M0.4 remains `NOT STARTED`.

## 2026-09-01 — M0.3 — Builder correction after verification failure

**Status:** AWAITING VERIFICATION
**Commit:** pending (executable branch tip: `99bf1b2d5429ebba427ce93c0dbbe7a3e0616984`)

- Replaced pandas-wide enrichment serialization with a CSV boundary that only
  writes benchmark fields; exact source lexemes and column values survive reruns.
- Restored all 32,510 changed `corp_code` and 10,633 changed `stock_code` cells;
  projections of all nine pre-benchmark files now match their pinned hashes.
- Rejects `NaN`, `+inf`, and `-inf` benchmark closes before alignment and during
  downstream completeness validation; explicit blank stock observations remain missing.
- Restored the verified M0.1 manifest (`7618e0d6…`) and M0.2 artifact
  (`b529d30e…`) byte-for-byte and added in-place regeneration guards.
- Idempotent re-enrichment and an independent audit passed for 34,686 rows and
  103,624 price-bearing horizons; the corrected M0.3 research payload is unchanged.
- All affected analyses reran; focused tests passed `31/31`, the full suite
  passed `185/185`, and `git diff --check` passed. M0.4 remains untouched.

## 2026-09-01 — M0.3 — Corrected implementation independently verified

**Status:** VERIFIED
**Commit:** pending (verified executable branch tip: `99bf1b2d5429ebba427ce93c0dbbe7a3e0616984`)

- All nine source projections match their pinned pre-M0.3 bytes; two-pass
  enrichment remained byte-idempotent across 34,686 rows.
- Independently reconciled 345,932 exact-date close/return relationships,
  including all 103,624 price-bearing horizons.
- Replayed `NaN`, `+inf`, and `-inf` attacks at normalization, strict alignment,
  and consumer boundaries; each failed explicitly.
- Confirmed M0.1 `7618e0d6…` and M0.2 `b529d30e…` hashes; direct in-place
  generate, refresh, and output attempts were rejected without mutation.
- Fresh Naver retrieval exactly matched all 2,452 pinned closes; the M0.3
  artifact regenerated byte-for-byte at `0ac51cc5…`.
- Focused tests passed `31/31`, the full suite passed `185/185`, and affected
  category, learner, intraday, filtered, subgroup, walk-forward, and paper
  outputs reproduced without conclusion changes.
- M0.3 is accepted as `VERIFIED`; M0.4 remains `NOT STARTED`.

## 2026-09-01 — M0.4 — Corporate-action / price-adjustment audit

**Status:** AWAITING VERIFICATION
**Commit:** pending (executable branch tip: `99bf1b2d5429ebba427ce93c0dbbe7a3e0616984`)

- Traced pinned pykrx 1.2.8: `adjusted=True` dispatches to Naver Finance and
  `False` to the credential-backed KRX route; live probes confirmed both paths.
- Added a mandatory `PriceAdjustment` enum, explicit provider keywords, frame
  and new-row provenance, and an exact pykrx dependency pin.
- Fixed the research policy to vendor-adjusted announcement returns before
  comparison; adjusted closes remain outcome inputs, not executable quotes.
- Audited 828 complete bonus-issue and 5,911 complete rights-offering rows; all
  seven windows containing rights drop matched fresh provider closes.
- Reproduced `300120` at exactly 5× after a later consolidation filing; all
  sampled returns were unchanged, as were both corporate-category headlines.
- Added deterministic artifact `ca2d9e50…`, representative regressions, and
  `docs/history/M0.4.md`; focused tests passed 12/12 and full suite 192/192.
- M0.4 awaits independent verification. M0.5 remains untouched.

## 2026-09-01 — M0.4 — Independent verification returned

**Status:** IN PROGRESS
**Commit:** pending

- Confirmed installed pykrx 1.2.8 dispatches `adjusted=True` to Naver and
  `False` to KRX; all production calls choose the typed policy explicitly.
- Fresh retrieval matched all six captured provider cases, including the exact
  5× `300120` revision; the unadjusted route did not fall back without credentials.
- The M0.4 artifact regenerated byte-for-byte at `ca2d9e50…`, M0.1–M0.3 hashes
  remained unchanged, focused tests passed `12/12`, and all `192` tests passed.
- Reproduced unchanged bonus-issue and rights-offering headlines and verdicts.
- Falsified the sensitive-window census: per-category drop calendars report
  only 4 bonus and 3 rights rows, while a union finds 4 and 66; the local DART
  table identifies 73 total crossed windows.
- The 63 committed cross-category omissions include a `300120` rights window
  with the same 5× revision. M0.4 returns to `IN PROGRESS`; M0.5 remains
  `NOT STARTED`.

## 2026-09-02 — M0.4 — Builder correction after verification failure

**Status:** AWAITING VERIFICATION
**Commit:** pending (executable branch tip: `99bf1b2d5429ebba427ce93c0dbbe7a3e0616984`)

- Replaced per-category rights-drop calendars with a shared 441-receipt
  committed union plus a pinned 499-receipt local DART source slice.
- Reconciled 4 bonus + 66 rights crossed windows in the committed union versus
  4 + 69 in DART; every committed receipt is present in the raw calendar.
- Added receipt-level provenance, fail-fast source inclusion, and regressions
  for both populations and the previously omitted `300120` rights window.
- Fresh retrieval matched all seven provider cases; both `300120` windows
  changed by exactly 5× with no return change.
- Regenerated schema-v2 M0.4 at `1acf9366…`; the pinned calendar matched the
  local database, while M0.1–M0.3 hashes remained unchanged.
- Reproduced unchanged bonus/right headlines; focused tests passed `14/14` and
  the full suite `194/194`. Detailed record: `docs/history/M0.4.md`.
- M0.4 awaits independent verification. M0.5 remains untouched.

## 2026-09-02 — M0.4 — Corrected implementation independently verified

**Status:** VERIFIED
**Commit:** pending (verified executable branch tip: `99bf1b2d5429ebba427ce93c0dbbe7a3e0616984`)

- Regenerated the pinned 499-event rights-drop calendar byte-for-byte from the
  local 1,209,249-row DART database.
- Confirmed every one of the 441 committed receipts and its stock, report, and
  event-date fields appears identically in the raw calendar.
- Independently reproduced committed-union `4 + 66` and raw-DART `4 + 69`,
  including the three raw-only windows and omitted `300120` relationship.
- Fresh retrieval matched all seven provider cases; both `300120` windows
  remain exact 5× revisions with zero return change.
- Schema-v2 M0.4 regenerated byte-for-byte at `1acf9366…`; M0.1–M0.3 hashes
  remained unchanged and corporate-category conclusions reproduced.
- Focused tests passed `14/14`, the full suite passed `194/194`, and
  `git diff --check` passed.
- M0.4 is accepted as `VERIFIED`; M0.5 remains `NOT STARTED`.
