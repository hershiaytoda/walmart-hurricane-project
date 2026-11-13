# Walmart Hurricane Analytics

**DS2002 Data Science Systems, University of Virginia | Midterm Group Project (Fall 2026)**
**Team data-cane:** Megha, Hershi, Angelia

We act as Walmart data science consultants ahead of a hurricane approaching Florida. Using messy, synthetic point-of-sale data for September 2024 (Hurricane Helene), we clean and consolidate the data, enrich it with real weather data from an API, and answer five questions about demand surges, SKU fragmentation, weather effects, store-level patterns, and a suspicious board games spike.

---

## Overview of findings

> [TODO: finalize once every question is complete in the final notebook.]

- **Q1, Demand surge:** [TODO: confirm numbers and windows against the final notebook.] In our working draft, Florida stores averaged about 1,599 transactions/day from Sep 21-25, roughly 2.8x the 576/day baseline (Sep 1-15), peaking at about 3.5x on Sep 25.
- **Q2, SKU consolidation:** 45 SKU codes and 63 product name spellings map to 20 real products, with unit totals unchanged. Pop-Tarts Strawberry was split across 4 SKUs. Its true volume is about 73 units/day in the baseline and 377/day in the 7 days before landfall (+414%), a size no single SKU shows.
- **Q3, Weather correlation:** [TODO: confirm.] In our working draft (Orlando store, Sep 2024, n = 30 days), daily revenue correlated weakly negatively with precipitation (r = -0.27) and moderately positively with max wind speed (r = 0.43). These are correlations from one store over one month.
- **Q4, Store geography:** Stores closer to the storm path did not surge earlier or harder. All 12 stores reached 2x baseline around Sep 21 and peaked Sep 24-25, with surge multiples between 2.2x and 2.7x.
- **Q5, Board games:** Mostly a data artifact, not a demand signal. A fair window gives +145%, not +250%, and board games rose in line with overall traffic. **Recommendation: do not stock extra board games**; prioritize true storm items like Pop-Tarts and water.

---

## Repository structure

```
walmart-hurricane-project/
├── README.md                          <- this file
├── CHARTER.md                         <- team charter
├── REFLECTION.md                      <- reflection write-up
├── notebook/
│   ├── walmart_hurricane_final.ipynb  <- final notebook (the deliverable)
│   └── SCRATCH/
│       ├── _2026-10-23 — Midterm Checkpoint — Lab.ipynb
│       └── _2026_10_30_—_Midterm_Progress_—_Lab.ipynb
└── data/
    ├── walmart_transactions.csv       <- ~25,000 messy POS records
    ├── store_locations.csv            <- Florida store metadata
    ├── product_catalog.csv             <- product/SKU reference
    ├── distribution_centers.csv       <- distribution center info
    └── inventory_and_sales.db         <- SQLite: daily_sales_summary + inventory_levels

```

---

## How to run

1. Open `walmart_hurricane_final.ipynb` in Google Colab (badge at the top of the notebook) or in Jupyter.
2. Make sure the `data/` folder is next to the notebook. In Colab, the notebook clones this repo to get it.
3. Choose **Restart and Run All**. The weather section calls a live API, so it needs an internet connection.

**Requirements:** Python 3, `pandas`, `matplotlib`, `seaborn`, `requests`, and `sqlite3` (included with Python).

---

## Data sources

**Supplied (synthetic) data:** the five files in `data/` listed above. Each contains intentional quality issues (duplicates, mixed date formats, SKU variants, inconsistent store IDs, and others), which the notebook's cleaning pipeline fixes.

**Weather data:** [Open-Meteo Historical Weather API](https://open-meteo.com/), free with no API key.
- Base URL: `https://archive-api.open-meteo.com/v1/archive`
- Daily precipitation and max wind speed, requested in the `America/New_York` timezone to align with the sales data. [TODO: Hershi to confirm the final variables, location(s), and timezone handling match the final notebook.]

---

## Methodology summary

1. **Load:** all files read as text first (`dtype=str`) so types are fixed deliberately during cleaning.
2. **Clean:** drop exact duplicates, parse mixed timestamp formats, strip `$` from prices, standardize store IDs, remove negative quantities, map SKUs to canonical products.
3. **Enrich:** pull daily weather from Open-Meteo and join it to daily sales.
4. **Analyze:** Q1-Q5 each have code, a chart, and a written finding in the notebook.

---

## Team and contributions

| Member | Role |
|---|---|
| Megha | Data cleaning, decision log, Q1 |
| Hershi | Weather API and join, Q3 |
| Angelia | Q2, Q4, Q5 |

[TODO: add charts and presentation owner(s).] See [CHARTER.md](CHARTER.md) for how we work together.

---

## Reflection

Our reflection write-up is in [REFLECTION.md](REFLECTION.md).

---

*Data is synthetic and the project is inspired by a real Walmart case study.*
