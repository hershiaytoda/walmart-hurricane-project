# Walmart-Hurricane-Project
DS 2002 at UVA midterm project

## Team

| Name | gitHub | Role / main contributions |
|------|--------|---------------------------|
| Megha | [megha-krish](https://github.com/megha-krish) | Cleaned the data, made the decision log, and worked on Q1 (demand surge) |
| Hershi | [hershiaytoda](https://github.com/hershiaytoda) | Worked with the weather API, joined the data, and did Q3 (weather correlation) |
| Angelia | [angeliaanne](https://github.com/angeliaanne) | Worked on Q2 (SKU consolidation), Q4 (store locations), and Q5 (board games) |

## Summary
Our team was able to clean up Walmart sales information for stores located in Florida during the month of September 2024. We then merged that data set with weather information we pulled via the Open-Meteo API. Our goal was to determine if people were changing their purchasing habits prior to when Hurricane Helene hit on September 26. In general, stores saw an increase of approximately 146% in total number of units sold the week prior to the storm. It appears that people began making more purchases about two days before the worst weather arrived. Breakfast food items such as Pop-Tarts and emergency supply items experienced some of the highest increases. We also investigated the reported 250% increase in board game sales; however, it appeared that the increased volume of sales was due to an overall increase in purchasing activity and poor categorization of products rather than people purchasing more board games.
 

## Repository Structure

```
walmart-hurricane-project/
├── README.md                          <- this file
├── CHARTER.md                         <- team charter
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

## How to run

1. Open `notebooks/walmart_hurricane_final.ipynb` in Google Colab. You can use the "Open in Colab" badge, or go to File → Open notebook → GitHub and paste the repo link.
2. Run the first cell, which clones the repo so the notebook can access the `data/` folder.
3. Go to Runtime → **Restart and run all** to run the whole notebook. You'll need internet for the weather API part.
4. You don't need an API key since Open-Meteo is free. We used `pandas`, `matplotlib`, `sqlite3`, and `requests`, which are already available in Colab.

You can also run it on your own computer by cloning the repo, opening the notebook in Jupyter, and running all the cells. The notebook checks for the files in `data/` or `../data/`.

## Key findings

- **Q1 Demand surge:** The sales in the week prior to Hurricane Irma landfall (Sep 19–25) increased by 146% over the Sep 1–15 baseline. Breakfast was the greatest increase in terms of dollars (+414%) followed by Emergency Supplies (+184%), Electronics (+150%), Alcohol (+126%) and Entertainment (+125%).
- **Q2 Pop-Tarts:** There were 45 different SKU codes for 63 different product name spellings for the 20 actual products. Pop-Tarts, for example, had 4 different SKUs. After combining the different SKUs for the Pop-Tarts, sales increased from approximately 73 units per day to 377 units per day, an increase of 414% over the average for the 20 products.
- **Q3 Weather:** Sales for the Orlando store (FL-239) had a correlation of r = 0.46 with wind, r = -0.32 with pressure, and r = -0.26 with rain. There was a very strong relationship between sales and wind 2 days later (r = 0.77). This is only based on 30 days of data for one store.
- **Q4 Stores:** All stores saw sales spikes around the same time period with the largest sales increases occurring between Sep 21 and Sep 24–25. The stores closest to the landfall were Miami (2.7x) and Orlando (2.2x).
- **Q5 Board games:** The increase in entertainment sales was nearly identical to the overall increase in sales of all products of 146%. The sales of Monopoly also remained at roughly 4% of total units sold, so there did not appear to be a separate increase in sales of board games.

## Recommendation

Based on what we found, I think Walmart should start sending extra stock to **all Florida stores** about a week before a hurricane is expected to make landfall, instead of only focusing on stores directly in its path. Extra space on trucks should go toward products that increase more than normal shopping does, like Pop-Tarts, other breakfast foods, water, and emergency supplies.

I wouldn't recommend sending extra board games beyond what stores would normally need for the increase in customers. We also think Walmart should combine duplicate SKUs, like the 4 Pop-Tarts codes and 3 Monopoly codes, before analyzing demand for the next storm. Otherwise, it can look like stores need less of a product than they actually do.

## Reflection

### 1. Data Quality Impact 
In the course of trying to make sense of the Walmart data, I ran into a few products that had been assigned multiple SKU codes and names. For example, there were 45 SKUs for 20 products, with 63 different spellings of product names. For example, Strawberry Pop-Tarts had 4 SKUs (`PT-12`, `SKU#459812`, `POPTART-STRAW`, and `PT_STRAWBERRY`) and 6 different names. I created a mapping table to link the different names to a single product name and verified that the total number of units for the product was the same before and after the mapping (47,897).

After mapping the products, Pop-Tarts went from being a product that on average sold 20 units a day per SKU to a product that on average sold 73 units a day with peak sales of 377 units in the week before landfall. This is a very different picture than before mapping the products. In fact, Pop-Tarts is one of the top selling products in the stores. If we hadn’t mapped the products, Walmart would have ordered less of the product than stores were actually selling. This also happened with Monopoly which had 3 SKUs and entertainment which had 4 category names.

### 2. ETL Trade-offs (Hershi)

One decision I made was how to handle sales rows with missing timestamps. In the Orlando store, 75 of 2,103 raw rows (about 3.6%) had no timestamp, and across all stores 884 rows were affected after de-duplication. Because the weather data is daily, a transaction with no timestamp cannot be placed on a day, so we dropped these rows. The alternatives were to keep them in an “unknown date” bucket so total sales stayed intact, to impute a date from the transaction ID order, or to spread them across days in proportion to each day’s sales. Each has a cost. Imputing or spreading would add invented precision to a lag analysis that depends on which day sales happened. Dropping is only harmless if the missing timestamps are spread evenly across days. If they were concentrated on the busiest pre-landfall days, we would understate the surge and flatten the lead between the Sep 24 sales peak and the Sep 26 wind peak. We did not check how the missing rows were distributed by day, and with more time I would, and I would rerun the lag table with the rows added back proportionally to see whether the 2-day lead holds. The weather join involved a similar choice. We requested weather with timezone=America/New_York so API days matched local sales days. Open-Meteo defaults to UTC, which would have pushed evening sales onto the next weather day and moved the sales-versus-wind lag by roughly a day.


### 3. Pipeline Trust (Megha)

I agree with the CDO. We didn't use any complicated model in this project as everything was averages, percent changes, and correlations, so our answers depended on whether the cleaned data was right. Our code ran fine on the raw data with no errors, but the numbers it gave would have been wrong. The raw data had 60 spellings of 12 store IDs, 37 spellings of 10 categories, prices stored as text with a $, mixed date formats, and 30 duplicate rows. If we hadn't merged the categories, Q1 would have split each real category into pieces and made every surge look smaller. The most fragile part was the timestamps. About 884 rows had no timestamp and had to be dropped, because a row with no date can't go in the baseline or surge window. We never checked whether those rows were spread evenly across days, and we can't check now because the date is gone. If a lot of them were from the days right before landfall, then all of our surge numbers are too low. We also used hand-made dictionaries for the category and SKU fixes, so a new spelling could slip through without an error. Our unit totals didn't match at first because the cleaning steps were copied into more than one place. If we had more time, I would put the cleaning in one shared function and add checks that stop the code if there is an unmapped category or SKU, or a date outside September.

### 4. Business vs. Data (Angelia)

For the board games question, the data wasn't completely clear. The +250% increase only showed up when looking at the peak day, and the inventory data had missing values and some incorrect SKUs. So we couldn't really say for sure what happened.

I was surprised to see that Monopoly sales were around 4% of total sales before and after the storm (3.9% vs. 4.0%). It seems that the increase in sales was due to more customers being in the store, rather than customers specifically wanting to buy board games.

If we make the wrong decision here and Walmart does not stock up on games, then shelves may be empty but games do not spoil and are not as important as other products such as emergency supplies. On the other hand, if Walmart stocks up on too many games then they are taking up space in trucks and on shelves that could be used for other products such as water, Pop-Tarts, etc. that people need more. Therefore, I would not stock up on extra board games for this hurricane, but only increase sales as normal for other products. If we had more time then we could look at multiple hurricanes and see if there is an increase in sales for board games after landfall as well, because on September 26 there was a small increase in sales that I was not able to explain with the data that we have.

### 5. Team Collaboration
We split up the work based on our team charter. Megha worked on cleaning the data, the decision log, and Q1. Hershi did the weather API, joining the data, and Q3. I worked on Q2, Q4, and Q5.
**Angelia:** 

If we had another week, I think I would've started Q4 and Q5 earlier since they weren't started yet at our first checkpoint. I also think we should've agreed on one cleaning method earlier because some of our numbers didn't match at first. For example, my first Q2 results showed 49,643 units, but after removing duplicates and rows with missing timestamps, it was actually 47,897.

I think my biggest contribution was figuring out the different SKU codes and category names and combining them so the data was more accurate. I also used SQL to check the board game sales against the database instead of only relying on the CSV.

**Megha:** 

I cleaned the data, made the decision log, and did Q1, which made the most sense because the other questions all depended on having clean data first. 

If we had another week, I would have put the cleaning in one shared function at the start and started this earlier, since we ended up with the cleaning steps copied in more than one place and our unit totals didn't match at first. I would also have checked the rows with missing timestamps before dropping them, instead of just assuming they wouldn't change the results. 

I think my most valuable skill was careful cleaning and writing down every decision, with how many rows it affected, so that everyone else could trust the data and explain it. One thing that showed it worked was that my Q1 Breakfast numbers (73 to 377 units/day) matched Angelia's Pop-Tarts numbers from Q2, so our cleaning agreed.


**Hershi:** _TODO_
