# Capital Without Category

### Governance, Inequality, and the Micro-Enterprise Paradox in Brazil

A three-part exploratory data analysis of Brazil's CNPJ formal-sector firm
registry, examining the limits of revenue-based firm classification when capital
ownership and governance structure carry information that revenue alone cannot.

**TidyTuesday 2026 · Week 4 (Extended)** · February 2026

---

## Team

| Member | Name | Contribution |
|:------:|:-----|:-------------|
| 1 | **Zhijun He** | Project synthesis lead (`capital_without_category_v3.qmd`); author of Part I — *The Universal Power Law* |
| 2 | **Jerry Miao** | Author of Part II — *Governance as Distributional Shape* |
| 3 | **Paxton Boyd** | Author of Part III — *The Partnership Channel* |

The three parts are designed as a connected trilogy: Part I motivates the
governance question by establishing universal concentration, Part II identifies
the distributional signature of governance archetypes, and Part III zooms into
the Micro tier to identify the legal-form channel through which capital-rich
firms register as micro-enterprises.

---

## Motivation

Brazil's official firm-size classification (Simples Nacional) is built on a
single threshold: **annual gross revenue**. A sole proprietor earning 360,000
BRL is legally a "micro-enterprise" regardless of whether their declared
capital stock is 50,000 BRL or 5,000,000 BRL.

This regulatory choice creates a natural experiment in the CNPJ registry: firms
within the same legal category can have wildly different capital endowments.
The analytical question this project addresses is whether revenue-based
classification successfully targets capital-constrained firms, or whether it is
*porous* — letting capital-rich firms wear the regulatory clothing of
micro-enterprises while structurally functioning as something else.

---

## Research Questions

**Q1 — Within-tier inequality.** Is capital concentration in Brazilian
formal-sector firms a feature of the micro-enterprise category specifically, or
is the same power-law signature visible across every size tier? *(Part I)*

**Q2 — Governance as distributional predictor.** Conditional on firm size, does
owner archetype shape the capital distribution? Is the effect a *level shift*
(governance moves the median), a *variance shift* (governance moves the tail),
or both? *(Part II)*

**Q3 — The capital-rich micro escape route.** Among micro-enterprises with
unusually high capital, which combinations of legal form and governance
archetype are most associated with capital-rich status? Is the escape a single
legal form (the popular EIRELI framing), or a structural combination that no
single dimension captures? *(Part III)*

---

## Headline Findings

**Part I — Universal concentration.** Capital concentration is structural to
the entire Brazilian formal sector, not specific to micro-enterprises. Every
size tier exhibits Gini > 0.98, with the top 1% holding 96–99% of tier capital
and the top 0.1% already holding 87–96%. The "capital trap" is not a
micro-enterprise pathology but a universal power-law signature shared across
regulatory categories.

**Part II — Governance shapes distributions tier-dependently.** Managing
Partners are the only archetype whose median capital scales with firm size
(300k → 1M from Micro to Medium/Large, a 3.3× increase). Capital tails are
heavy in nearly every cell, but *which archetype carries the tail shifts with
size*: Beneficial Owners hold the heaviest tail at Micro (the holding-company-
shell signature), while Managing Partners reach an extraordinary 526× mean/
median ratio at Medium/Large (true industry titans). The two heavy tails are
structurally different phenomena that should not be conflated.

**Part III — The escape route is a partnership, not an LLC.** Among
micro-enterprises, the capital-rich subpopulation (above the 75th-percentile
threshold of BRL 500,000) concentrates in **Simple Partnership × Beneficial
Owner** (~48%) and **Simple Partnership × Managing Partner** (~42%). The
popular framing of EIRELI as the dominant escape vehicle is not what the data
show; the partnership-with-passive-equity combination is the fingerprint of
holding-company architecture operating through Brazil's small-business legal
infrastructure.

**Combined policy implication.** Revenue alone is a porous targeting
instrument. Legal form and governance archetype — both already recorded at
registration, available at zero additional reporting cost — carry the missing
information needed to distinguish genuinely capital-constrained entrepreneurs
from architecturally sophisticated capital-rich firms wearing micro-enterprise
clothing.

---

## Data

**Source.** Brazilian CNPJ Registry, accessed via the TidyTuesday 2026 Week 4
(Extended) release.

**Scope.** All firms with non-missing capital stock, firm size, and owner
qualification, distributed across three size tiers (Micro / Small /
Medium–Large).

**Cleaning conventions.** Records with missing or non-positive capital stock,
missing firm size, or missing owner qualification are excluded. Capital stock
is log-transformed for distributional analysis. Reported zero capital is
treated as a registry-coding artefact (entities awaiting capital declaration
post-registration) rather than as a genuinely zero-capital firm.

**Key derived variables.**

- `size` — three-tier firm size (Micro / Small / Medium–Large), recoded from
  the original `company_size` field
- `governance` — three-archetype owner classification (Sole Entrepreneur /
  Managing Partner / Beneficial Owner) plus an "Other" residual, derived from
  the `owner_qualification` field via regex matching
- `legal_short` — abbreviated legal-form classification (LLC / Sole
  Proprietorship / EIRELI / Simple Partnership / Silent Partnership /
  Corporation)
- `is_capital_rich_micro` — Boolean flag for Micro-tier firms above the 75th
  percentile of micro-tier capital (≈ BRL 500,000)

---

## Repository Structure

```
.
├── README.md                         (this file)
│
├── capital_without_category_v3.qmd   # Unified synthesis (Zhijun He)
│                                     # Combines all three parts into a single
│                                     # narrative under shared design conventions
│
├── eda-member1.qmd                   # Part I — The Universal Power Law
│                                     # (Zhijun He)
│
├── eda-member2.qmd                   # Part II — Governance as Distributional Shape
│                                     # (Jerry Miao)
│
└── eda-member3.qmd                   # Part III — The Partnership Channel
                                      # (Paxton Boyd)
```

Each `eda-memberN.qmd` file is a self-contained Quarto document with its own
data ingestion, analysis, methodological notes, and interpretation, and can be
rendered independently. The unified `capital_without_category_v3.qmd` merges
the three parts into a single document.

---

## How to Reproduce

### Requirements

- R ≥ 4.2
- Quarto ≥ 1.4
- R packages: `tidyverse`, `scales`, `patchwork`, `gt`
  - Additionally for Part I: `boot` (bootstrap Gini CIs)
  - The shared `theme_cwc()` design system uses base ggplot2 only

### Build any individual part

```bash
quarto render eda-member1.qmd        # Part I (Power law)
quarto render eda-member2.qmd        # Part II (Governance)
quarto render eda-member3.qmd        # Part III (Escape route)
```

### Build the unified synthesis

```bash
quarto render capital_without_category_v3.qmd
```

The first render downloads the CNPJ data from the TidyTuesday GitHub repository;
subsequent renders use Quarto's `cache: true` to skip re-fetching. All four
documents are rendered to standalone HTML with `code-fold: true` so prose
appears first and code is available on demand.

---

## Methods at a Glance

| Part | Statistical method | Primary visualization |
|------|-------------------|----------------------|
| I    | Bootstrap Gini 95% CIs (B = 1,000); rank-bucket decomposition (top 1% / next 9% / next 40% / bottom 50%) | Two-panel: discrete log-rank concentration ladder + stacked share decomposition |
| II   | Kruskal–Wallis non-parametric tests within each size tier | Two-panel: median trajectory with IQR (P25–P75) and P10–P90 whiskers + mean ÷ median ratio on log y-axis |
| III  | Wilson 95% CIs on cell-level escape rates; Pearson χ² test of legal-form × capital-rich independence | Horizontal bar ranking of legal-form × governance cell escape rates with Wilson confidence intervals |

Methodological choices that were deliberately *not* used and the reasons are
documented in each part's "Methodological Notes" section: ANOVA (replaced by
Kruskal–Wallis given heavy-tailed distributions), full Theil decomposition
(uninformative when between-tier variance is dominated by within-tier),
quantile regression (the trajectory + ratio design communicates the same
finding more directly), and logistic regression (cell-level visualization
preserves the legal-form × governance interaction more transparently).

---

## Design Conventions

All four documents share a unified visual identity to make figures portable
across parts:

- **Palette.** Terracotta (`#c3553a`) / amber (`#d9a441`) / deep teal
  (`#2a5a6b`) for size tiers; amber / slate blue (`#5b8a9a`) / deep navy
  (`#1d3557`) for governance archetypes
- **Theme.** Custom `theme_cwc()` extends `theme_minimal()` with consistent
  typography (deep-navy titles, grey-tone subtitles), panel grid weight,
  and panel spacing
- **Caption.** Unified caption template with team name and date
- **Code style.** All chunks labelled, all figures captioned, all tables built
  with `gt` for publication-quality output

---

## Notes on Limitations

- This is a **descriptive** analysis. We do not estimate causal effects; we
  identify cross-sectional structural patterns in the registry.
- The "capital-rich micro" threshold (75th percentile within micros, ≈ BRL
  500,000) is descriptive, not normative. A sensitivity check at the 80th and
  90th percentile cutoffs preserves the cell ranking.
- Mean ÷ median ratios at small tiers (Medium/Large in particular) are
  sensitive to a small number of extreme outliers. Qualitative rankings are
  robust; specific magnitudes should be read as order-of-magnitude indicators.
- We cannot distinguish whether capital-rich micros choose partnership form
  *because* they have capital (selection on capacity) or acquire capital
  *because* they chose partnership form (legal-form-as-channel). Distinguishing
  these requires panel data or a registration-reform discontinuity, neither of
  which the CNPJ snapshot supports.
