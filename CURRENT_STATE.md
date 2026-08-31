# CURRENT_STATE.md

> **Purpose:** This is the compressed snapshot a fresh Codex session should read first.
>
> Rewrite this file after every completed or independently verified milestone.
>
> Keep it short. Historical reasoning belongs in `IMPLEMENTATION_HISTORY.md`.

## Snapshot metadata

- **Repository:** `EtixP/krx-disclosure-event-study`
- **Working branch:** `cleanup/research-focus`
- **Last inspected branch commit:** `486f48de1dacdc31853aa65fcab886ea0af344e9`
- **Snapshot date:** 2026-08-30
- **Current milestone:** `M0.0 — Project memory and agent-governance infrastructure`
- **Current milestone status:** `IN PROGRESS`
- **Next intended milestone:** `M0.1 — Baseline research snapshots and verification harness`

The commit above is the repository state inspected when these governance documents were drafted. Treat it as a reference point, not a permanent truth. Update this section after the files are committed or the branch moves.

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
- Title-based event categorization for multiple disclosure classes.

## Research / backtesting

- Event studies across seven disclosure categories.
- Daily event-relative returns through approximately T+5.
- Simple transaction-cost modeling.
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

The README currently states approximately 140 passing tests, but this governance snapshot has **not independently rerun and certified that exact count**. Treat the actual test run as part of M0.1.

---

# Current provisional research conclusions

These are the conclusions documented by the current research-focused branch **before the planned methodology corrections**.

They are provisional and may change.

## Cross-category conclusion

The repository currently reports that none of the seven tested disclosure categories produces a robust long-side strategy after its chosen realistic T+1-close execution assumptions and modeled transaction costs.

## Shareholder-change finding

`shareholder_change` is currently treated as a relatively robust negative event class and used as a long-side risk blacklist signal.

This finding must be rechecked after:

- historically correct costs;
- abnormal-return adjustment;
- event normalization;
- stronger uncertainty analysis.

## Buyback timing finding

The current most interesting positive result is an **entry-timing effect** around buyback disclosures.

The existing branch reports approximately:

- uniform T+1 entry mean net return around `+0.157%`;
- time-aware entry mean net return around `+0.473%`;
- entry-timing delta around `+0.317%`;
- timing delta positive across `10/10` half-year folds.

However, the same analysis reports important fragility:

- median trade near/slightly below zero;
- roughly 50% win rate;
- strong dependence on the positive tail;
- sensitivity to closing-auction fill realism;
- concentration in low-price / KOSDAQ names;
- substantial capacity limitations.

This is **not currently considered a validated tradable strategy**.

The future live system should prospectively test the unresolved execution assumption instead of performing endless historical retuning.

---

# Known methodological / engineering issues

These are the highest-priority known issues.

## 1. Historical transaction costs

The current cost model uses a single transaction-tax assumption over a multi-year sample.

The historical sample crosses multiple Korean tax regimes.

This must be corrected before trusting basis-point-level conclusions.

**Roadmap:** `M0.2`

## 2. Raw returns instead of benchmark-adjusted returns

The current event study primarily measures raw stock returns.

It does not yet consistently subtract KOSPI/KOSDAQ benchmark movement.

This makes it difficult to distinguish disclosure effects from broad market beta.

**Roadmap:** `M0.3`

## 3. Adjusted-price behavior is not sufficiently explicit

The `pykrx` market-data path needs an explicit audit of adjusted vs unadjusted prices.

This matters especially for corporate-action categories.

**Roadmap:** `M0.4`

## 4. Statistical dependence / research degrees of freedom

Current descriptive metrics and positive-window counts are useful but do not fully address:

- repeat issuers;
- clustered event dates;
- overlapping event windows;
- repeated exploratory hypotheses;
- multiple testing.

**Roadmap:** `M0.5`

## 5. Event normalization is still relatively title-rule-driven

Current event categories rely heavily on `report_name` SQL matching.

The project does not yet have a canonical economic-event abstraction that cleanly connects original filings, amendments, cancellations, and related updates.

**Roadmap:** `M1.1`

## 6. Live usefulness is not yet the central execution path

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

python -m scripts.summarize_all_categories
python scripts/analyze_event_category.py --category buyback
python scripts/train_learner.py --synthetic-edge
python scripts/train_learner.py --category buyback
python scripts/run_intraday_walkforward.py
```

Do not assume these commands remain sufficient forever.

`M0.1` should formalize a stable research-state verification path and record exact currently reproducible outputs.

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

Finish `M0.0` by placing these four governance files at repository root and checking them for consistency.

Then start a fresh session for:

`M0.1 — Baseline research snapshots and verification harness`

Do not begin transaction-cost corrections until the current pre-revision research state has been captured.
