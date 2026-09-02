# CURRENT_STATE.md

> **Purpose:** This is the compressed snapshot a fresh Codex session should read first.
>
> Rewrite this file after every completed or independently verified milestone.
>
> Keep it short. Historical reasoning belongs in `IMPLEMENTATION_HISTORY.md`.

## Snapshot metadata

- **Repository:** `EtixP/krx-disclosure-event-study`
- **Working branch:** `cleanup/research-focus`
- **Last inspected branch commit:** `99bf1b2d5429ebba427ce93c0dbbe7a3e0616984`
- **Snapshot date:** 2026-09-02
- **Last verified milestone:** `M0.4 — Corporate-action / price-adjustment audit`
- **Last verified milestone status:** `VERIFIED`
- **Current milestone:** `M0.4 — Corporate-action / price-adjustment audit`
- **Current milestone status:** `VERIFIED`
- **Next required action:** begin M0.5 only in a separate Builder session

The commit above remains the executable branch tip. The verified M0.0–M0.4 work is present as working-tree changes pending commit. Treat the hash as a reference point, not a permanent truth.

---

# Current project goal

The project is being reframed from a mostly historical event-study / trading-research repository into a **production-like event-driven Korean equity research and paper-trading platform**.

The desired system should eventually:

1. ingest and normalize new DART disclosures;
2. identify economically significant events;
3. place them in defensible historical context;
4. run only frozen, versioned forward experiments;
5. collect prospective intraday data needed to test execution assumptions;
6. support realistic paper execution and risk constraints;
7. remain useful even when the correct recommendation is `NO TRADE`.

The research component remains important, but it should become one validated subsystem inside a usable live platform rather than the entire product.

---

# What the repository currently does

At the inspected `cleanup/research-focus` state, the repository already contains substantial working infrastructure.

## Data / disclosure pipeline

- OPEN DART disclosure ingestion.
- SQLite-backed storage.
- DART disclosure-document fetching and parsing.
- Deterministic supply-contract extraction.
- Historical DART filing-time scraper.
- KRX daily OHLCV retrieval through `pykrx`.
- Pinned KOSPI/KOSDAQ daily index history from Naver Finance with source metadata.
- Title-based event categorization for multiple disclosure classes.

## Research / backtesting

- Event studies across seven disclosure categories.
- Daily event-relative returns through approximately T+5.
- Date-aware and market-aware 2021–2026 transaction-cost modeling.
- Exact-date KOSPI/KOSDAQ benchmark and abnormal-return attribution.
- Idealized vs later-entry execution scenarios.
- Half-year walk-forward summaries.
- Market splits.
- Per-trade descriptive metrics.
- Risk blacklist support for negative event classes.

## Learning system

- Decision-time feature extraction.
- Gradient-boosted trade/skip policy.
- Time-ordered internal validation.
- Champion/challenger walk-forward evaluation.
- `NeverTrade` and `AlwaysTrade` baselines.
- Synthetic planted-edge tests.
- Matched-period / selection-lift reasoning documented in the research branch.
- Benchmark-adjusted realized rewards; raw rewards remain available only for
  historical artifact reproduction and diagnostics.

## Testing

The repository contains a broad pytest suite covering:

- clients;
- storage;
- parser/extraction;
- costs/metrics;
- risk engine;
- learning;
- walk-forward chronology;
- intraday-entry behavior;
- pipeline health.

The repository-local virtual environment passed **194 tests** in the independent
M0.4 re-verification on 2026-09-02 (`.venv/bin/pytest -q`); the focused
price-policy and audit run passed **14 tests**. Regression coverage includes mandatory explicit
provider arguments, both adjustment routes, the observed fivefold uniform-scale
revisions in both `300120` windows, shared-calendar completeness, production-call
scanning, deterministic audit regeneration, and the earlier
cost/benchmark/provenance invariants. The README does not embed a test count
that can drift.

## Research-state baseline

M0.1 now provides a deterministic pre-revision snapshot under `artifacts/baselines/pre_revision/`:

- four generated JSON research summaries plus a provenance manifest;
- hashes for artifacts, tracked event-study CSV inputs, runtime versions, and generator source files;
- a pinned public receipt-number/filing-time slice so the intraday baseline can be regenerated without the ignored 688 MB SQLite database;
- deterministic learner runs with `random_state=0`;
- verification of recorded hashes, pinned-input shape, category counts across aggregate/execution/market/fold views, coverage and intraday fold arithmetic, cross-artifact agreement, and selection-lift arithmetic.

`python -m scripts.verify_research_state` verifies the recorded snapshot without
requiring future corrected research to equal old values. Its historical
manifest is restored to SHA-256 `7618e0d6…`; `--check-inputs` intentionally
reports later input/source drift. Mutation commands must target a new output
directory and refuse to overwrite the verified snapshot.

The first independent verification found two gaps, both corrected in the verified implementation:

- rehashed category-count and fold-count contradictions now fail semantic verification;
- `src/kdtb/backtest/metrics.py` is now provenance-hashed, and a regression test confirms `--check-inputs` detects its drift.

The corrected snapshot regenerated byte-for-byte from both the pinned input and a fresh local-database capture. The Verifier independently replayed the prior rehashed category-count attack and confirmed it is rejected. Research headline values remain unchanged. M0.1 is `VERIFIED`.

## Historical transaction costs

M0.2 now provides a law-verified, explicit 2021–2026 Korean-equity cost model:

- the roundtrip API requires buy date, sell date, and KOSPI/KOSDAQ market;
- securities transaction tax and the KOSPI-only 0.15% special rural tax are separate components;
- all-in modeled roundtrip costs are 0.363% (2021–2022), 0.333% (2023 and 2026), 0.313% (2024), and 0.283% (2025);
- commission and VAT remain explicit assumptions; 5bps slippage is explicitly per side;
- dates outside the verified 2021–2026 interval and unsupported markets fail rather than extrapolate;
- stored event-study rows now retain exact t0/t+1/t+2/t+5 observation dates, and research consumers reject missing date provenance.

The one-time reconstruction covered eight event-study files, 2,241 stocks, and
32,450 rows with zero recorded closes missing their corresponding date.
`artifacts/m0_2/historical_cost_comparison.json` is restored byte-for-byte to
its verified SHA-256 `b529d30e…`. Its command now verifies that immutable hash;
current-tree replays require a separate output path. The M0.1 manifest likewise
remains historical rather than being advanced to later inputs or sources.

Independent verification confirmed the statutory schedule from official law sources, the mandatory API and failure behavior, the eight-file migration without changes to prior fields, and all migrated research consumers. A stratified fresh-source audit matched all 12 sampled observation-date sequences across 2021–2026 and both markets. The deterministic comparison regenerated byte-for-byte, the affected analyses reproduced, and the four M0.1 research artifacts remained byte-identical through the legacy path. M0.2 is `VERIFIED`.

## Broad-market benchmark adjustment

M0.3 fixes benchmark assignment in advance: KOSPI stock → broad KOSPI price
index; KOSDAQ stock → broad KOSDAQ price index. `src/kdtb/data/benchmarks.py`
provides the shared source, normalization, exact-date alignment, missing-data,
and abnormal-return boundary. It never fills a missing index date or converts
it to zero.

The pinned cache has 1,226 observations per market from 2021-06-28 through
2026-07-01. A strict backfill aligned all available stock observation dates
across nine event-study files (34,686 rows). Headline category, learner,
subgroup, intraday, filtered-study, walk-forward, and paper-attribution outputs
now report or consume abnormal returns where appropriate. Raw simulated cash
PnL remains distinct from abnormal attribution.

`artifacts/m0_3/benchmark_adjustment_comparison.json` records the deterministic
M0.2-raw versus M0.3-abnormal comparison. Benchmark exit outcomes remain reward
labels only and are absent from decision-time features. Detailed source and
methodology reasoning is in `docs/history/M0.3.md`.

Independent verification reproduced the provider cache, formulas, M0.3 artifact,
and research changes but returned three boundary failures. The correction pass:

- replaced pandas-wide enrichment writes with source-lexeme-preserving CSV
  updates and restored exactly 32,510 `corp_code` and 10,633 `stock_code` cells;
- pinned projections of all nine pre-benchmark event files, including every
  immutable M0.2 input hash, so return-string or identifier drift fails tests;
- rejects `NaN` and positive/negative infinity during source normalization and
  downstream completeness checks;
- restored the exact verified M0.1 manifest and M0.2 comparison and added guards
  that reject in-place regeneration.

Independent re-verification confirmed that all nine pre-benchmark CSV
projections match their pinned bytes and that repeated enrichment is
byte-idempotent. It rejected `NaN`, `+inf`, and `-inf` at normalization,
strict-alignment, and consumer boundaries; direct overwrite attacks left the
M0.1/M0.2 hashes unchanged. All 34,686 rows, 103,624 price-bearing horizons,
and 345,932 independently recomputed benchmark/formula relationships passed.
A fresh retrieval matched all 2,452 pinned closes, the M0.3 artifact regenerated
byte-for-byte at SHA-256 `0ac51cc5…`, and affected research outputs reproduced.
M0.3 is `VERIFIED`.

## Corporate-action price basis

M0.4 makes the historical stock-price policy explicit. `pykrx==1.2.8` is
pinned; `PriceAdjustment.VENDOR_ADJUSTED` maps to `adjusted=True` and the Naver
Finance route, while `UNADJUSTED` maps to the credential-backed KRX route.
Every production OHLCV call must choose an enum value, and new event-study rows
record the adjustment policy and source.

The announcement-return methodology keeps vendor-adjusted prices to prevent a
split or consolidation from becoming a mechanical event return. Committed
observations are the reproducible vintage because adjusted absolute prices can
revise later; they are not asserted to be executable historical quotes or
formal total shareholder returns.

The corrected audit covers 828 complete bonus-issue and 5,911 complete
rights-offering rows against shared calendars. The union of both committed
category files contains 441 rights-drop receipts and crosses 4 bonus + 66
rights windows. A separately pinned 499-receipt local DART slice crosses 4 + 69
windows; its 58 records absent from the category snapshots add three windows.
Every committed receipt is present in that raw-source slice.

Seven fresh provider cases matched all 28 requested closes. Both the explicit
`300120` bonus rights drop and the formerly omitted rights-offering window
reproduced exact 5× revisions with zero return change. Bonus/rights M0.3
headlines and verdicts remain unchanged. The schema-v2 deterministic artifact
is `artifacts/m0_4/price_adjustment_audit.json` (SHA-256 `1acf9366…`), and the
pinned raw calendar regenerated exactly from the local database. Verified
M0.1–M0.3 hashes remain `7618e0d6…`, `b529d30e…`, and `0ac51cc5…`.

Independent re-verification regenerated the 499-event calendar byte-for-byte
from the local database, confirmed all 441 committed receipts and fields,
reproduced both `4 + 66` and `4 + 69` censuses plus the three raw-only windows,
and matched all seven provider cases freshly. Both `300120` windows remain
exact 5× revisions with unchanged returns. The schema-v2 audit regenerated at
`1acf9366…`, all upstream artifact hashes remained unchanged, focused tests
passed `14/14`, and the full suite passed `194/194`. M0.4 is `VERIFIED`.
Detailed rationale is in `docs/history/M0.4.md`.

---

# Current research conclusions

These conclusions include the independently verified M0.2 cost correction,
M0.3 benchmark correction, and M0.4 price-basis audit. They remain historical
research findings, not forward evidence or a claim of tradability.

## Cross-category conclusion

None of the seven tested categories produces a robust long-side strategy after
realistic T+1-close execution, modeled costs, and broad-market adjustment. The
largest abnormal realistic mean is only `+0.111%` (rights offering,
`positive_noisy`).

## Shareholder-change finding

`shareholder_change` is currently treated as a relatively robust negative event class and used as a long-side risk blacklist signal.

Its abnormal realistic mean is approximately `−1.396%`, with 9/10 scored folds
negative. It must still be rechecked after event normalization and stronger
uncertainty analysis.

## Buyback timing finding

The current most interesting positive result is an **entry-timing effect** around buyback disclosures.

M0.3 reports approximately:

- uniform T+1 mean abnormal net return `−0.093%` (5/10 folds positive);
- time-aware mean abnormal net return `+0.164%` (7/10 folds positive);
- abnormal entry-timing delta `+0.257%`, positive across 9/10 folds.

However, the same analysis reports important fragility:

- median trade near/slightly below zero;
- roughly 50% win rate;
- strong dependence on the positive tail;
- sensitivity to closing-auction fill realism;
- concentration in low-price / KOSDAQ names;
- substantial capacity limitations.

This is **not currently considered a validated tradable strategy**.

The deterministic buyback learner's M0.2 raw `+0.022%` selection lift becomes
`−0.037%` on abnormal rewards; its verdict returns to `no_selection_edge`.
Buyback's whole-category realistic mean changes from `+0.143%` raw to `−0.028%`
abnormal.

The future live system should prospectively test the unresolved execution assumption instead of performing endless historical retuning.

---

# Known methodological / engineering issues

These are the highest-priority known issues.

## 1. Adjusted-price vintages can revise

M0.4 made the provider route explicit and pinned, but Naver-adjusted absolute
prices can still change after later corporate actions. Historical artifacts
must retain their captured observations. Adjusted closes must not be reused as
if they were contemporaneous executable quotes or exact entitlement-aware
total shareholder returns.

## 2. Statistical dependence / research degrees of freedom

Current descriptive metrics and positive-window counts are useful but do not fully address:

- repeat issuers;
- clustered event dates;
- overlapping event windows;
- repeated exploratory hypotheses;
- multiple testing.

**Roadmap:** `M0.5`

## 3. Event normalization is still relatively title-rule-driven

Current event categories rely heavily on `report_name` SQL matching.

The project does not yet have a canonical economic-event abstraction that cleanly connects original filings, amendments, cancellations, and related updates.

**Roadmap:** `M1.1`

## 4. Live usefulness is not yet the central execution path

The current repository is primarily a historical research system.

The project does not yet provide the desired:

- live DART watcher;
- significance engine;
- historical-context alert;
- frozen forward experiment ledger;
- prospective intraday-data collection;
- realistic paper execution.

These are future phases, not tasks to implement opportunistically.

---

# Important invariants

Future changes should preserve these unless a milestone explicitly revises them.

## Research integrity

- Do not tune methodology to restore a previous positive result.
- A weaker or negative corrected result is acceptable.
- If data cannot answer a question, mark it unresolved.
- Never fabricate unavailable historical intraday data.
- Historical and forward results must remain clearly separated.

## Data provenance

- Raw DART/API inputs should remain traceable.
- Derived features should be reproducible from documented source data.
- Missing data must remain missing or explicitly imputed; do not silently replace it with convenient zeros.

## Time / leakage

- Decision-time features must only use information available at the decision timestamp.
- Validation must precede test.
- Training must precede validation.
- Future outcomes should be stored separately from decision records.

## Architecture

- Historical, live, replay, forward-test, and paper modes should converge on the same event normalization and feature logic.
- Avoid duplicate implementations of the same domain concept.
- User interfaces should remain thin layers over tested domain APIs.

## Scope

- One Codex implementation session should normally work on one roadmap milestone.
- Do not implement future milestones because they appear easy while touching nearby files.

---

# Current high-level architecture

Conceptually, the repository is moving toward:

```text
                         ┌──────────────────────┐
                         │ Raw DART disclosures │
                         └──────────┬───────────┘
                                    │
                          ingest + provenance
                                    │
                                    ▼
                         canonical event core
                          (future M1.1 target)
                                    │
                 ┌──────────────────┼──────────────────┐
                 │                  │                  │
                 ▼                  ▼                  ▼
        historical research   live intelligence   forward experiments
                 │                  │                  │
                 └──────────────────┼──────────────────┘
                                    │
                                    ▼
                             paper execution
```

At present, much of the historical-research side exists, while the shared canonical event core and live/forward/paper paths are future work.

---

# Reproduction commands currently documented by the repository

The current branch documents commands along these lines:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
pip install -e .

pytest

python -m scripts.verify_research_state
python -m scripts.compare_cost_revision
python -m scripts.compare_benchmark_adjustment
python -m scripts.summarize_all_categories
python scripts/analyze_event_category.py --category buyback
python scripts/train_learner.py --synthetic-edge
python scripts/train_learner.py --category buyback
python scripts/run_intraday_walkforward.py
```

The pre-revision snapshot and M0.2 comparison are immutable historical evidence.
Default commands verify them; replays with current code/data must use separate
output paths, and M0.1 `--check-inputs` is expected to report later drift.

---

# Files a new agent should read

Always:

1. `AGENTS.md`
2. `CURRENT_STATE.md`
3. the active milestone in `ROADMAP.md`

Then, depending on task:

## Research methodology

- `README.md`
- `RESEARCH_FINDINGS.md`
- `INTRADAY_FEASIBILITY.md`
- `src/kdtb/backtest/`
- relevant `scripts/`

## Learning

- `src/kdtb/learning/`
- learning tests

## Event/data pipeline

- `src/kdtb/data/`
- `src/kdtb/storage/`
- parser/extraction modules
- relevant tests

## Historical intent

- `DESIGN.md`

Read recent relevant entries from `IMPLEMENTATION_HISTORY.md` when prior design decisions affect the active milestone.

Do **not** reread the entire history by default if `CURRENT_STATE.md` already summarizes it.

---

# Immediate next action

M0.4 is `VERIFIED`. M0.5 remains `NOT STARTED` and should begin only in a
separate Builder session.
