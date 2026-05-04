# DataBridge — Consulting Workflow Walkthroughs

Five worked examples using the sample data files in `sample_data/`.
Each covers a real pattern from consulting engagements.

---

## Setup

1. Open `databridge.html` in your browser
2. Load `transactions_2023.csv` (the main dataset — 120 rows, 14 columns)
3. Load `customer_master.csv` (the lookup table — 10 rows, 8 columns)

---

## Walkthrough 1 — Revenue by Region and Category (Pricing / GTM)

**The question:** How is revenue distributed across regions and product categories?
Which region-category combination is driving the most value?

**Steps:**

1. Select `transactions_2023` as your active table
2. **Filter rows** → `[status] == 'Won'` (exclude lost deals)
3. **Group by & summarise**
   - Group by: `region`, `category`
   - Aggregations:
     ```
     revenue SUM Total_Revenue
     transaction_id COUNT Deal_Count
     discount_pct AVG Avg_Discount
     ```
4. **Calculated column** → `Margin = ([Total_Revenue] - [Total_Cost]) / [Total_Revenue] * 100`
   *(first add cost: Group by again with `cost SUM Total_Cost`)*
5. **Rank** → rank by `Total_Revenue`, descending, no group
6. **Pareto / 80-20** → Category column: `region`, Value: `Total_Revenue`

**What you'll see:** The North + Software combination dominates. Pareto will show which 2–3 combinations drive 80% of revenue.

---

## Walkthrough 2 — Customer Enrichment via Join (Customer Analytics)

**The question:** Enrich the transaction data with customer tier and industry
so you can analyse revenue by segment.

**Steps:**

1. Select `transactions_2023` as active table
2. **Join tables**
   - Join with: `customer_master`
   - Left key: `customer_id`
   - Right key: `customer_id`
   - Join type: Left (keep all transactions)
3. **Anti-join** (run separately on a fresh load)
   - To find: transactions with no matching customer record
   - Useful for data quality checks before analysis
4. After joining → **Group by & summarise**
   - Group by: `tier`, `industry`
   - Aggregations:
     ```
     revenue SUM Total_Revenue
     customer_id COUNT_DISTINCT Unique_Customers
     ```
5. **Percent of total** → Value: `Total_Revenue`, % of grand total

**What you'll see:** Enterprise tier dominates revenue. Technology industry is the highest-value segment.

---

## Walkthrough 3 — Monthly Revenue Trend with MoM Change (Time Intelligence)

**The question:** How is revenue trending month by month?
Where were the biggest jumps or drops?

**Steps:**

1. **Date parts** → Date column: `date`
   - Select: Year, Month, Month_Name, Quarter
2. **Group by & summarise**
   - Group by: `date_Year`, `date_Month`, `date_Month_Name`
   - Aggregations:
     ```
     revenue SUM Monthly_Revenue
     transaction_id COUNT Deal_Count
     ```
3. **Period comparison**
   - Date column: `date` (or use the Month column)
   - Value: `Monthly_Revenue`
   - Period: MoM
4. **Calculated column**
   - `Revenue_Flag = IF [MOM_Pct_Chg] > 10 THEN 'Strong Growth' ELSE IF [MOM_Pct_Chg] < -10 THEN 'Decline' ELSE 'Stable'`
5. **Rolling average** → trailing 3-month average of `Monthly_Revenue`

**What you'll see:** Clear growth trend through the year with Q4 acceleration. MoM % change flags outlier months instantly.

---

## Walkthrough 4 — Sales Rep Performance & Discount Discipline (Pricing)

**The question:** Which sales reps are discounting too aggressively?
Is there a correlation between discount depth and win rate?

**Steps:**

1. **Group by & summarise**
   - Group by: `sales_rep`, `status`
   - Aggregations:
     ```
     transaction_id COUNT Deal_Count
     discount_pct AVG Avg_Discount
     revenue SUM Total_Revenue
     ```
2. **Pivot** → Row: `sales_rep`, Column: `status`, Value: `Deal_Count`, Agg: SUM
   *(this gives you a Won/Lost matrix per rep)*
3. Back on the grouped data → **Calculated column**
   - `Win_Rate = [Won_Deals] / ([Won_Deals] + [Lost_Deals]) * 100`
4. **Conditional tagging**
   - `[Avg_Discount] > 15 AND [Win_Rate] < 70 → High Discount, Low Win Rate`
   - `[Avg_Discount] > 15 AND [Win_Rate] >= 70 → High Discount, High Win Rate`
   - `[Avg_Discount] <= 15 AND [Win_Rate] >= 70 → Disciplined, High Win Rate`
   - `→ Review Needed`
5. **Correlation matrix** → select `Avg_Discount`, `Win_Rate`, `Total_Revenue`

**What you'll see:** Whether discount depth actually drives wins — or just erodes margin.

---

## Walkthrough 5 — Customer Segmentation by Value (Customer Analytics / CLTV)

**The question:** Which customers are high-value vs at-risk?
Build a simple RFM-style segmentation.

**Steps:**

1. **Filter rows** → `[status] == 'Won'`
2. **Group by & summarise**
   - Group by: `customer_id`, `customer_name`
   - Aggregations:
     ```
     revenue SUM Total_Revenue
     transaction_id COUNT Transaction_Count
     date MAX Last_Purchase_Date
     ```
3. **Date difference** → Start: `Last_Purchase_Date`, End: today's date (add a column manually or use a calculated column with a fixed date), Unit: days → `Days_Since_Last_Purchase`
4. **Percentile / decile**
   - `Total_Revenue` → Quartile → `Revenue_Quartile`
   - `Transaction_Count` → Quartile → `Frequency_Quartile`
5. **Conditional tagging** on the quartiles
   - `[Revenue_Quartile] == 'Q4' AND [Frequency_Quartile] == 'Q4' → Champion`
   - `[Revenue_Quartile] == 'Q4' → High Value`
   - `[Revenue_Quartile] == 'Q1' AND [Days_Since_Last_Purchase] > 180 → At Risk`
   - `[Frequency_Quartile] == 'Q4' → Frequent Buyer`
   - `→ Standard`
6. **Export** → Excel → paste directly into your deck

**What you'll see:** A clean customer segmentation table ready for a client presentation.

---

## Tips for large files

- Load your file, then immediately use **Filter rows** or **Select columns** to reduce size before running heavy operations like Pivot or Correlation
- For million-row files: **Sample** (10%) first to build and test your pipeline, then re-run on the full dataset
- **Running total** and **Lag/Lead** on large files sort the entire dataset — expect 2–5 seconds on 500K+ rows
- Export to Excel → use Power Query if you need to push beyond what DataBridge covers

---

*DataBridge — built by Archishman Bandyopadhyay. Part of a 50-week open-source build.*
