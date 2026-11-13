# Walmart Hurricane Analytics

**DS2002 Data Science Systems, University of Virginia | Midterm Group Project (Fall 2026)**

## Team

| Name | GitHub | Role / main contributions |
|------|--------|---------------------------|
| Megha | [megha-krish](https://github.com/megha-krish) | Cleaned the data, made the decision log, and worked on Q1 (demand surge) |
| Hershi | [hershiaytoda](https://github.com/hershiaytoda) | Worked with the weather API, joined the data, and did Q3 (weather correlation) |
| Angelia | [angeliaanne](https://github.com/angeliaanne) | Worked on Q2 (SKU consolidation), Q4 (store locations), and Q5 (board games) |

For this project, we cleaned Walmart sales data from Florida stores in September 2024 and combined it with weather data from the Open-Meteo API. We wanted to see how people's shopping changed before Hurricane Helene made landfall on September 26. Overall, stores sold 146% more units in the week before the storm, and people seemed to start buying more around 2 days before the worst weather. Breakfast foods like Pop-Tarts and emergency supplies had some of the biggest increases. We also looked into the reported 250% jump in board games, but it seemed to be more from overall shopping increasing and messy category labels rather than people specifically buying more games.


## Data

| File | Source | Notes |
|------|--------|-------|
| `walmart_transactions.csv` | Course repo | Around 25,500 sales rows. There were duplicates, different timestamp formats, `$` signs in prices, negative quantities, and inconsistent store IDs and SKUs |
| `store_locations.csv` | Course repo | 14 rows, but only 12 actual stores because 2 were repeated. Tallahassee was missing latitude and longitude |
| `product_catalog.csv` | Course repo | Product/SKU reference file. Some products had multiple SKUs and some package sizes were wrong |
| `distribution_centers.csv` | Course repo | 5 distribution centers. Some values were messy, like `~750`, and some costs were missing |
| `inventory_and_sales.db` | Course repo | SQLite database with `daily_sales_summary` and `inventory_levels`. Some dates had different formats and `units_on_order` was missing in places |

We also used the **Open-Meteo Historical Weather API**: `https://archive-api.open-meteo.com/v1/archive`.

We pulled daily rain, maximum wind speed, and average sea-level pressure. We set `timezone=America/New_York` so the weather dates would match the sales dates in Eastern time.

---

## Overview of findings

## Key findings

- **Q1 Demand surge:** In the week before landfall (Sep 19–25), sales went up **146%** compared to our Sep 1–15 baseline. Breakfast had the biggest increase (+414%), followed by emergency supplies (+184%), electronics (+150%), alcohol (+126%), and entertainment (+125%).
- **Q2 Pop-Tarts:** We found 45 SKU codes and 63 different product name spellings, even though there were only 20 actual products. Pop-Tarts alone was split into 4 SKUs. Once we combined them, sales went from around **73 to 377 units a day (+414%)**. That's about 4 times what we would've seen looking at just one SKU.
- **Q3 Weather:** For the Orlando store (FL-239), sales had some correlation with wind (r = 0.46), pressure (r = -0.32), and rain (r = -0.26). The strongest relationship was between sales and wind **2 days later** (r = 0.77), which suggests people were shopping ahead of the storm. This was only based on 30 days and one store though.
- **Q4 Stores:** All the stores had sales spikes around the same time, starting around Sep 21 and peaking Sep 24–25, even if they weren't as close to landfall. Miami (2.7x) and Orlando (2.2x) stood out the most.
- **Q5 Board games:** Entertainment sales went up around 145%, which was almost the same as the 146% increase across all products. Monopoly also stayed at about 4% of total units sold, so it didn't really seem like board games had their own separate surge.
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

1. Open `notebooks/walmart_hurricane_final.ipynb` in Google Colab. You can use the "Open in Colab" badge, or go to File → Open notebook → GitHub and paste the repo link.
2. Run the first cell, which clones the repo so the notebook can access the `data/` folder.
3. Go to Runtime → **Restart and run all** to run the whole notebook. You'll need internet for the weather API part.
4. You don't need an API key since Open-Meteo is free. We used `pandas`, `matplotlib`, `sqlite3`, and `requests`, which are already available in Colab.

You can also run it on your own computer by cloning the repo, opening the notebook in Jupyter, and running all the cells. The notebook checks for the files in `data/` or `../data/`.

---
## Methodology summary

1. **Load:** all files read as text first (`dtype=str`) so types are fixed deliberately during cleaning.
2. **Clean:** drop exact duplicates, parse mixed timestamp formats, strip `$` from prices, standardize store IDs, remove negative quantities, map SKUs to canonical products.
3. **Enrich:** pull daily weather from Open-Meteo and join it to daily sales.
4. **Analyze:** Q1-Q5 each have code, a chart, and a written finding in the notebook.
---

## Reflection

Our reflection write-up is in [REFLECTION.md]. 

---
