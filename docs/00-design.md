# muppets — portfolio performance monitoring (v0 design)

Status: seed design for collaboration. No product code yet.  
Audience: Norbert Ruijling and Tom van Buren.  
Repo: [github.com/CraftedQuery/muppets](https://github.com/CraftedQuery/muppets) (lowercase name).

This document is the working agreement for what muppets is, what v0 will and will not do, and how the two of us share work without leaking secrets or implying licensed advice.

---

## Purpose

muppets is a **shared workspace** for monitoring **real portfolio performance** — returns, risk, drawdowns, allocation, and data hygiene — so Norbert and Tom can look at the same numbers, argue from the same definitions, and decide what to change next.

It is a **monitoring and collaboration** project, not a trading engine and not a research lab. The product of v0 is a trusted performance picture and a written agreement on metrics, not an order path.

The name is informal. The work is not: portfolio figures, broker data, and any personal account identifiers stay out of git and off a public remote.

---

## Stakeholders

| Person | Role in this repo |
|---|---|
| **Norbert Ruijling** | Owner of the CraftedQuery GitHub account and this repository. Sets access, visibility, and what may be committed. Contact: `norbert.ruijling@craftedquery.com`. |
| **Tom van Buren** | Collaborator on definitions, review, and (once invited) implementation. GitHub invite waits until a contact email is known. |

No other collaborators are in scope for v0. Agents and tooling (including any “Legal” agent) are helpers, not stakeholders and not counsel.

---

## Relationship to QTrade

[QTrade](https://github.com/CraftedQuery/QTrade) is CraftedQuery’s **paper-trading research laboratory** for liquid U.S. equities and ETFs. Its product is an honest experiment loop (signals, walk-forward, paper execution, risk contracts). It is paper-only by design.

**muppets is not a QTrade fork by default.** Do not copy the QTrade tree, history, or experiment contracts into this repo unless both stakeholders later choose a different option in writing.

Treat the two repos as **siblings** under CraftedQuery. Three relationship options stay open; pick one before sharing code or schemas:

| Option | What it means | When it is appropriate |
|---|---|---|
| **A. Independent (default)** | Separate repo, stack, and vocabulary. muppets may *read* published ideas from QTrade the way it would read any paper-lab write-up, but it does not import QTrade packages or clone its layout. | v0, and any time the goal is live/real-portfolio monitoring rather than paper experiments. |
| **B. Shared libraries later** | If both projects need the same return math, date conventions, or instrument identifiers, extract a small shared library *after* the overlap is proven. The library would be a third, explicit artifact — not a silent copy. | Only after the same function has been written twice and the definitions match. |
| **C. Thin consumer** | muppets treats QTrade paper-session *outputs* (or a future export) as one optional data source, behind an adapter, with no shared mutable state. | Only if a paper sleeve should appear next to a real portfolio for comparison. |

**Do not** fold muppets into QTrade, or QTrade into muppets, in v0. Do not treat QTrade’s paper fills as a substitute for broker statements. GrantsNavigator and other CraftedQuery repos are out of scope.

---

## Goals and non-goals (v0)

### Goals

1. **One written metric dictionary** — names, formulas, and lookback windows for the v0 sketch below, so two people cannot disagree about “YTD” or “max drawdown.”
2. **A repeatable snapshot** — a dated performance picture (returns, risk, allocation, data freshness) that can be regenerated from local or private inputs.
3. **A collaboration path** — private repo, two humans, issues/PRs for metric changes; no secrets in git.
4. **An explicit QTrade stance** — default independent sibling (option A), with B and C recorded as later choices, not implicit forks.
5. **Legal and access hygiene** — visibility, invites, IP, disclaimers, broker ToS, and PII flagged before any account data is ingested.

### Non-goals (v0)

- Trading, order routing, rebalancing, or “the bot should place this.”
- Live brokerage credentials in this repo, in CI, or in committed config.
- Forking, vendoring, or rewriting QTrade as the muppets codebase.
- Tax lots, official performance composites (GIPS), or a marketed fund report.
- Investment advice, suitability, or a product sold to third parties.
- A public dashboard, marketing site, or social feed of positions.
- Multi-user auth, billing, or a general-purpose portfolio SaaS.
- Touching GrantsNavigator or changing QTrade.

---

## Monitoring metrics sketch

v0 is a **sketch**: define and compute these, do not optimize them. Prefer simple, explainable numbers over model-heavy risk. Every figure must carry **as-of time**, **currency**, and **whether cash flows are included**.

### Returns

| Metric | Intent | v0 note |
|---|---|---|
| Period return | What the book did over a window | Daily, MTD, QTD, YTD, since inception. State whether the window is calendar or trading-day. |
| Time-weighted return (TWR) | Performance of the strategy, stripping the timing of deposits/withdrawals | Default “how did the book do?” number. |
| Money-weighted return (MWR / IRR) | What the owner experienced given cash-flow timing | Report next to TWR; do not pick one and hide the other. |
| Excess vs benchmark | TWR minus benchmark TWR over the same window | Benchmark identity is an open question (e.g. a single ETF such as SPY). |

### Risk and path

| Metric | Intent | v0 note |
|---|---|---|
| Peak-to-trough drawdown | Pain and recovery | Current drawdown, max drawdown, days underwater. Use the same NAV series as TWR. |
| Volatility | How noisy the path is | Annualized stdev of a declared return frequency (daily or weekly). Label sample length. |
| Sharpe / Sortino | Return per unit of (downside) vol | Optional in v0; require a stated risk-free rate and do not treat a short sample as a rating. |
| Beta / tracking error vs benchmark | How much the book is just the index | Only after the benchmark is chosen. |

### Composition and contribution

| Metric | Intent | v0 note |
|---|---|---|
| Weights | What we actually hold | Gross, net, cash, top-N concentration, single-name cap. |
| Position P&L | What moved the NAV | Period contribution by name; keep this a table, not a story. |
| Sector / sleeve mix | Concentration that names hide | Optional if classification data is clean; skip if it requires a paid taxonomy. |

### Hygiene (treat as first-class)

| Metric | Intent |
|---|---|
| Last good snapshot | Timestamp of the latest complete NAV and holdings. |
| Missing prices / failed symbols | Count and list; a pretty return built on holes is a bug. |
| Cash-flow log completeness | Deposits, withdrawals, fees, dividends — present or explicitly unknown. |
| Corporate actions | Splits and symbol changes applied or flagged. |

**Out of the v0 sketch (record, do not build):** tax-lot P&L, options greeks, factor attribution, leverage/margin utilization, and any live order or fill stream.

---

## Sharing and access

**This repository is currently public.** That is the wrong default for a portfolio-monitoring collaboration. Flip it to **private** before inviting Tom, before adding sample statements, and before any issue that names accounts, brokers, or sizes.

Recommended access model:

1. **Visibility:** private GitHub repo under CraftedQuery.
2. **Collaborators:** Norbert (admin). Invite Tom as a collaborator **when his email is known**; do not guess or scrape a personal address into git.
3. **Secrets:** never commit API keys, broker tokens, account numbers, raw statements, or `.env` files. Use local untracked files, a password manager, or a private secret store. Add ignore rules before the first data experiment.
4. **Data:** portfolio snapshots live outside git (or in a private, access-controlled store). Committed fixtures, if any, must be synthetic.
5. **History:** assume anything pushed while the repo is public is world-readable, including deleted files in git history. Do not “fix” a leaked secret by amending; rotate and treat history as burned.

Until the repo is private, keep this tree to design docs and synthetic examples only.

---

## Open questions

1. **Tom’s contact and GitHub identity** — email and handle so the invite can be sent after the repo is private.
2. **Whose books** — Norbert only, Tom only, both, or a joint paper/real mix? Separate NAVs or one combined view?
3. **Brokers and export format** — which institutions, which file or API, and whether a human export is enough for v0.
4. **Benchmark** — single ETF, blended, or none until there is more history.
5. **Currency and base** — reporting currency; FX treatment if holdings are not all USD.
6. **QTrade option** — confirm A (independent) for v0, or schedule a written decision on B/C.
7. **Cadence** — daily close snapshot vs on-demand vs weekly review.
8. **Stack** — notebook, small Python package, or spreadsheet-first for the first snapshot.
9. **Contribution license** — are Tom’s commits CraftedQuery-owned, jointly owned, or inbound under a short CLA? (See legal flags.)
10. **Name** — keep “muppets” as the informal repo name, or pick a dull public-facing title before any third party sees it.

---

## Legal review flags

These are **flags for humans and licensed counsel**, not conclusions. A coding or “Legal” agent is **not licensed counsel** and must not be treated as giving legal, tax, or regulatory advice.

| Flag | Why it matters here |
|---|---|
| **Intellectual property** | Who owns the repo, issues, and Tom’s contributions? CraftedQuery vs personal work product should be written down before substantial code lands. Do not copy QTrade or third-party vendor code into this tree without a license check. |
| **Advice disclaimers** | Performance numbers are not a recommendation to buy, sell, or hold. Docs and any UI should say this is **personal monitoring**, not investment advice, not an offering, and not a signal service. |
| **Broker terms of service** | Any later API or scraped statement must stay inside the broker’s ToS (personal use, no credential sharing, no automated trading unless permitted). v0 can stay on manual exports to avoid this until reviewed. |
| **PII and account data** | Names, account numbers, tax IDs, IBANs, and statement PDFs are personal data. Keep them out of git, out of public issues, and off screenshots in public channels. Prefer synthetic fixtures in CI. |
| **Securities / marketing** | Publishing live performance or “track record” copy can become a regulated communication in some jurisdictions. v0 is private two-person monitoring; do not publish results. |
| **Tax** | MWR/TWR are not tax reports. Do not imply wash-sale, lot, or filing accuracy. |
| **Cross-border** | Norbert’s CraftedQuery contact is in this doc; Tom’s location and any data-residency rules are unknown. Confirm before storing statements in a third-party host. |

Resolve flags with a human lawyer where the collaboration, data hosting, or any public claim requires it. Agent output is a checklist, not an opinion letter.

---

## Next steps

1. **Flip this repository to private** (GitHub settings). Do this before invites or any non-synthetic data.
2. **Collect Tom’s email** (out of band) and send a collaborator invite after the repo is private.
3. **Answer the open questions** in a short follow-up note (or issues), starting with whose books, broker export, and benchmark.
4. **Confirm relationship option A** (independent sibling of QTrade) unless both stakeholders write down B or C.
5. **Add a `.gitignore`** and a one-page “no secrets / no statements” rule before the first data-shaped file.
6. **Implement the v0 metric dictionary** as code or a checked notebook that reads *local* inputs and writes a dated snapshot.
7. **Human legal pass** on IP between Norbert and Tom, and on whether any broker ToS is triggered by the chosen export path.
8. **First joint review** of one snapshot: agree the numbers, then freeze definitions before adding features.

When those are done, v0 is a private two-person monitoring loop — not a QTrade fork, not a trading system, and not advice.
