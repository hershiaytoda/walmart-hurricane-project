Reflection Questions
Answer each question thoughtfully (one solid paragraph minimum per question).

1. Data Quality Impact

Describe a specific data quality issue you encountered in the supplied data. How did your cleaning decision change the outcome of your analysis? What would have happened if you had not caught it?

2. ETL Trade-offs

You made choices about how to standardize SKUs, handle missing timestamps, and join weather data. Pick one decision and explain: what alternative approach could you have taken, and how might it have changed your results?

One decision I made was how to handle sales rows with missing timestamps. In the Orlando store, 75 of 2,103 raw rows (about 3.6%) had no timestamp, and across all stores 884 rows were affected after de-duplication. Because the weather data is daily, a transaction with no timestamp cannot be placed on a day, so we dropped these rows. The alternatives were to keep them in an “unknown date” bucket so total sales stayed intact, to impute a date from the transaction ID order, or to spread them across days in proportion to each day’s sales. Each has a cost. Imputing or spreading would add invented precision to a lag analysis that depends on which day sales happened. Dropping is only harmless if the missing timestamps are spread evenly across days. If they were concentrated on the busiest pre-landfall days, we would understate the surge and flatten the lead between the Sep 24 sales peak and the Sep 26 wind peak. We did not check how the missing rows were distributed by day, and with more time I would, and I would rerun the lag table with the rows added back proportionally to see whether the 2-day lead holds. The weather join involved a similar choice. We requested weather with timezone=America/New_York so API days matched local sales days. Open-Meteo defaults to UTC, which would have pushed evening sales onto the next weather day and moved the sales-versus-wind lag by roughly a day.

3. Pipeline Trust

The Walmart CDO argued that the biggest risk is not the model — it is the pipeline. Based on your experience in this project, do you agree or disagree? What was the most fragile part of your data pipeline?



4. Business vs. Data

For Question 5 (board games), your team had to make a recommendation with imperfect data. How did you weigh statistical evidence against business risk? Would you have decided differently with more time or more data?

5. Team Collaboration

How did your team divide the work? What would you do differently if you had another week? What was the most valuable skill each team member contributed?
