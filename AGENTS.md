# AGENTS.md

> **Operating contract for coding agents working in this repository.**
>
> This repository is both a software system and a quantitative research artifact. Research correctness, reproducibility, and controlled scope are first-class engineering requirements.

# 1. Mandatory reading order

Before changing code:

1. Read `AGENTS.md`.
2. Read `CURRENT_STATE.md`.
3. Read the active milestone in `ROADMAP.md`.
4. Read the most recent relevant entries in `IMPLEMENTATION_HISTORY.md`.
5. Inspect the actual implementation and tests related to the milestone.
6. Read a detailed record under `docs/history/` only if the active work depends on that earlier design decision.

Do not assume documentation is perfectly current.

If code and documentation disagree:

- investigate;
- determine what is actually true;
- document the mismatch;
- do not silently choose the more convenient interpretation.

`CURRENT_STATE.md` exists so agents do not need to reread the entire project history.

---

# 2. Work on one milestone only

The default unit of work is exactly one `ROADMAP.md` milestone.

Do **not** implement future milestones opportunistically.

Examples of prohibited scope expansion:

- fixing transaction costs and also adding benchmark-adjusted returns;
- building the DART watcher and also adding a dashboard;
- normalizing events and also redesigning the learner;
- touching market data and adding a new strategy.

A small supporting refactor is allowed only when necessary to complete the active milestone safely.

If adjacent work is desirable:

1. document it;
2. recommend or add a future milestone;
3. leave it unimplemented.

---

# 3. Builder and Verifier are separate roles

Unless the user explicitly says otherwise, assume you are a **Builder**.

## Builder

A Builder should:

1. read the active milestone and acceptance criteria;
2. inspect existing code and tests;
3. state a concise implementation plan;
4. mark the milestone `IN PROGRESS`;
5. implement only that milestone;
6. add targeted tests;
7. run targeted tests;
8. run the full test suite;
9. rerun affected research outputs;
10. compare material result changes against the prior baseline where relevant;
11. explain those changes rather than hiding them;
12. update `CURRENT_STATE.md`;
13. append a compact entry to `IMPLEMENTATION_HISTORY.md`;
14. create `docs/history/<milestone>.md` only when detailed reasoning is worth preserving;
15. mark the milestone `AWAITING VERIFICATION`;
16. stop.

A Builder should not mark its own substantive implementation `VERIFIED`.

## Verifier

A Verifier should ideally begin from a fresh context.

A Verifier should:

1. read the milestone acceptance criteria;
2. inspect the implementation without assuming the Builder is correct;
3. try to falsify it;
4. inspect edge cases and failure behavior;
5. run targeted tests;
6. run the full test suite;
7. rerun affected research outputs;
8. independently inspect methodology;
9. check documentation against actual behavior;
10. look for silent regressions and scope creep.

If correct:

- append a compact verification note to `IMPLEMENTATION_HISTORY.md`;
- update `CURRENT_STATE.md`;
- mark the milestone `VERIFIED`.

If incorrect:

- do not proceed to the next milestone;
- document concrete failures;
- return the milestone to `IN PROGRESS` or `BLOCKED`.

---

# 4. History policy

The project uses three layers of memory:

```text
CURRENT_STATE.md
    What is true now?

IMPLEMENTATION_HISTORY.md
    What changed, in a compact chronological index?

docs/history/<milestone>.md
    Why exactly was a complex design decision made?

git history
    What code changed?
```

## `IMPLEMENTATION_HISTORY.md`

Every implemented milestone gets a short entry.

Keep entries roughly:

- 5–10 bullets;
- under ~200 words;
- focused on conceptual change, verification, scientific/product consequences, and remaining limitations.

Do not paste code diffs.

## `docs/history/`

Detailed records are **optional**.

Create one only when future agents are likely to need the reasoning, especially for:

- transaction-cost methodology;
- benchmark design;
- price-adjustment treatment;
- event normalization;
- statistical methodology;
- leakage/chronology design;
- forward-experiment architecture;
- execution/fill modeling;
- major research corrections;
- major architecture decisions.

A detailed record may contain:

- objective;
- state before;
- design chosen;
- alternatives considered;
- validation;
- changed research conclusions;
- remaining limitations;
- handoff invariants.

Do not create detailed records for routine tooling or trivial refactors.

## Corrections

Never rewrite history to erase a mistake.

If a previous decision or conclusion is later invalidated:

- append a correction to `IMPLEMENTATION_HISTORY.md`;
- update `CURRENT_STATE.md`;
- update or supersede the relevant detailed record if needed.

---

# 5. Research correctness overrides result preservation

Never tune code or assumptions merely to recover a previous headline result.

If a correction causes:

- a positive edge to disappear;
- a return estimate to shrink;
- a blacklist signal to weaken;
- an ML model to look worse;
- a prior conclusion to reverse;

report the corrected result.

The project is stronger when it catches its own false positives.

Do not search parameter space until a desired conclusion returns.

---

# 6. Never fabricate data or capabilities

Do not invent:

- historical intraday data;
- API fields;
- filing timestamps;
- fills;
- broker behavior;
- market-cap histories;
- missing benchmark data;
- forward-test results;
- live trading results.

Before relying on an external API capability:

1. verify it;
2. document relevant limitations;
3. fail explicitly if required data is unavailable.

`BLOCKED` is a valid milestone state.

---

# 7. Preserve decision-time integrity

Anything used to make a decision in historical replay or live mode must have been knowable at that time.

Requirements:

- training data strictly precedes validation;
- validation strictly precedes test;
- forward decisions are frozen before future outcomes;
- benchmark data is aligned to the correct event window;
- future data never enters decision-time features;
- outcome records remain separate from decision records.

Add tests for these invariants whenever a milestone touches them.

---

# 8. Preserve data provenance

Prefer:

```text
raw source
    ↓
normalized representation
    ↓
derived features
    ↓
decision / research output
```

Requirements:

- retain source IDs / receipt numbers where practical;
- retain source timestamps;
- distinguish raw fields from derived fields;
- do not overwrite raw source material with cleaned interpretations;
- keep missing values explicit;
- do not silently convert unavailable values to `0` unless zero is semantically correct and documented.

---

# 9. One event core

The long-term architecture should avoid separate domain logic for:

- historical analysis;
- live monitoring;
- historical replay;
- forward experiments;
- paper execution.

These paths should converge on shared:

- event normalization;
- feature extraction;
- provenance;
- decision-time semantics.

Do not create parallel `historical_*` and `live_*` implementations of the same concept without a strong reason.

---

# 10. Verification requirements

"Tests pass" is necessary but not sufficient.

Every milestone must verify the property it claims to implement.

## Always

- run targeted tests;
- run the full `pytest` suite;
- run lint/format checks once configured;
- rerun affected research scripts.

## Methodology changes

Also compare:

- prior result;
- corrected result;
- reason for the difference.

Do not make tests assert arbitrary old research values when methodology is intentionally changing.

Prefer invariant tests such as:

- correct tax regime selected;
- benchmark aligned to identical trading dates;
- no future feature leakage;
- experiment cannot mutate after activation;
- missing benchmark data never becomes zero silently.

---

# 11. Baselines are diagnostic, not targets

Once M0.1 establishes pre-revision baselines, use them to understand change.

Do not optimize new implementations until they match old artifacts.

Classify material differences as:

- expected scientific change;
- expected implementation change;
- data-version change;
- bug;
- unresolved.

---

# 12. Handle blockers explicitly

If implementation reveals a foundational problem, stop rather than inventing around it.

Examples:

- required historical data is unavailable;
- API semantics contradict assumptions;
- benchmark alignment cannot be made reliable;
- corporate-action adjustment behavior is ambiguous;
- source data lacks necessary provenance.

When blocked:

1. characterize the problem;
2. explain what it affects;
3. record evidence;
4. propose options;
5. update `CURRENT_STATE.md`;
6. append a compact `IMPLEMENTATION_HISTORY.md` entry;
7. create a detailed `docs/history/` record only if the issue needs durable reasoning;
8. mark the milestone `BLOCKED`;
9. stop.

---

# 13. Keep `CURRENT_STATE.md` truthful and short

After each Builder or Verifier session, update it to reflect actual state.

It should answer:

- What works now?
- What is provisional?
- What is known to be wrong?
- What milestone is active?
- What should happen next?

Do not turn it into a diary.

Historical detail belongs in `IMPLEMENTATION_HISTORY.md` or `docs/history/`.

---

# 14. Documentation drift is a bug

If a methodology or interface changes, update relevant:

- README claims;
- research findings;
- examples;
- commands;
- comments;
- architecture docs;
- current-state docs.

Do not leave knowingly stale headline numbers.

If a document is intentionally historical, label it historical rather than rewriting the past.

---

# 15. Avoid unnecessary sophistication

Do not add technology solely because it sounds impressive.

Avoid unless justified by the active milestone:

- deep neural networks;
- reinforcement learning;
- LLM-based trade decisions;
- microservices;
- Kafka;
- Kubernetes;
- distributed databases;
- elaborate cloud deployments;
- broker automation;
- large collections of technical indicators.

Prefer simple, inspectable, testable systems.

---

# 16. ML-specific rules

The learner is not the product.

When working on ML:

- distinguish regime exposure from cross-sectional selection;
- maintain matched-period baselines;
- preserve `AlwaysTrade` and `NeverTrade`;
- preserve synthetic planted-edge tests;
- prefer simple models unless data justifies complexity;
- verify chronology structurally;
- do not add models because they improve historical PnL.

Do not call a model successful merely because it makes money in a favorable regime.

---

# 17. Live-system rules

When live milestones begin:

- ingestion must be idempotent;
- restarts must not duplicate events;
- raw events must be persisted before downstream interpretation;
- source timestamps and processing timestamps should be distinguishable;
- degraded API behavior must be visible;
- downstream failures must not silently lose raw events.

The live system must remain useful when no trading strategy applies.

---

# 18. Forward-experiment rules

Once forward-experiment infrastructure exists:

- experiment definitions are versioned;
- active experiments are immutable;
- modifications create new versions;
- every considered event is logged, including rejections;
- decisions are stored before outcomes;
- outcomes never rewrite decisions;
- historical and forward results remain separate.

Do not call repeatedly inspected historical data "out of sample."

---

# 19. Paper-execution rules

Paper execution should be realistic and conservative.

It should eventually account for:

- spread;
- slippage;
- latency;
- partial fills;
- participation limits;
- position limits;
- conflicting signals;
- taxes and fees.

Do not equate a historical close with a guaranteed executable fill.

No real-money order submission belongs in the current roadmap unless the user explicitly adds such a milestone.

---

# 20. Git / change hygiene

When git operations are available:

- keep milestone changes logically grouped;
- avoid unrelated formatting churn;
- do not rewrite unrelated history;
- prefer one clearly named milestone commit after tests pass;
- record relevant commit hashes in the compact history.

Do not force-push or destructively rewrite history unless explicitly requested.

---

# 21. Architecture audit cadence

After roughly every 4–5 verified milestones, perform a fresh audit-only session.

Inspect:

- architectural drift;
- duplicate abstractions;
- research/documentation inconsistencies;
- stale assumptions;
- technical debt;
- divergence between historical/live/replay/paper paths.

The audit should not modify code.

Recommendations become explicit future milestones.

---

# 22. Stop condition

A Builder stops when:

- the active milestone's acceptance criteria are satisfied;
- required tests and analyses have run;
- documentation is updated;
- compact history is appended;
- detailed history is added only if warranted;
- current state is updated;
- milestone is `AWAITING VERIFICATION`.

A Verifier stops when:

- the milestone is independently accepted as `VERIFIED`, or
- failures/blockers are documented.

**Do not continue to the next roadmap milestone in the same session unless the user explicitly instructs you to do so.**
