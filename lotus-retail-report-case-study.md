# Case Study: Lotus Group Retail, From Messy Source Files to a Six-Page Power BI Dashboard

**A business intelligence project covering data cleaning, star-schema modelling, DAX and dashboard design**

---

## The Business Question

A retailer operating 15 stores across Egypt wants one place to see how it is performing: how revenue moves over time, which customers, products and stores drive it, and how much it loses to returns. The task was to turn nine raw, inconsistent files into a trustworthy model and a dashboard a manager could use without asking an analyst for help.

## The Dataset

The Lotus Group Retail dataset is a fictional educational dataset: nine CSV files, about 43,000 rows, covering January 2022 to December 2024. It has five dimension tables (dates, stores, customers, employees, products) and four transactional tables (two years of orders, order line items, returns). It deliberately contains realistic data quality problems, so cleaning was part of the work, not a preliminary step.

## Approach

**1. Cleaning in Power Query.** Each table was inspected before it was changed, and every fix was checked against an expected count.

- Removed 50 duplicate customer rows (3,050 to 3,000), confirmed by the gap between distinct and total counts.
- Converted text dates to real dates and verified the conversion on rows where day and month could not be confused.
- Split combined name and product fields, trimmed whitespace, and standardised text casing. Payment methods fell from 12 distinct values to 6 once stray spaces were trimmed.
- Kept phone numbers as text, because a numeric type would silently drop leading zeros.
- Appended the two order tables (12,000 rows) and merged the returns table with a left outer join, so orders without a return were kept.

**2. Data modelling.** The model is a star schema: five dimensions around two linked fact tables (order headers and order lines), with a date table marked as such so time-intelligence functions behave. A second, inactive relationship between customer registration dates and the date table lets one measure count new customers without disturbing the main date relationship.

**3. DAX.** More than 30 measures, grouped into display folders: revenue, cost and profit, orders, returns, customers, products, store and employee metrics, and time intelligence (year-on-year, month-on-month, prior period). Each was cross-checked against an independent calculation from the raw files before being used in a visual.

**4. Dashboard.** Six pages, one question each: executive overview, sales and revenue trends, customer insights, product performance, store and employee performance, and returns analysis. A custom theme was built from the colours of the company logo, year slicers are synced across pages, and charts follow a consistent rule: the same colour always means the same measure.

## A Definition Decision: Cancelled Orders

Late in the build, an unused column (`order_status`) revealed that 495 of the 12,000 orders were cancelled, carrying 1.68M, or 4%, of the revenue figure. A sale that was cancelled is not revenue, so the cancelled orders were excluded from revenue, cost, order counts, quantity and active customers. Headline revenue moved from 45.35M to 43.67M, Return Rate from 8.80% to 9.18%, and several findings and callouts were recomputed. The rule is stated on the dashboard's first page. The point to take from it is that a metric's definition can move the headline number by a few percent, so the definition should be chosen deliberately and shown.

## Key Findings

All figures exclude cancelled orders and cover 2022 to 2024 combined unless stated.

- **Overall performance.** Revenue was 43.67M with a 21.3% profit margin. After refunds, Net Revenue was 40.47M.
- **The 2023 dip was one subcategory.** Revenue fell 3.9% in 2023 and recovered 5.6% in 2024. Mobile Phones fell 24% in 2023 (2.68M to 2.04M), a drop of about 640K, which is larger than the company's entire net decline of 572K that year. All other subcategories combined grew slightly. Discounting does not explain it: Mobile Phones' average discount (7.97%) ranks 6th of 15 subcategories, close to the 7.88% overall average.
- **Margin and revenue point in opposite directions.** Clothing earns a 47.5% margin against Electronics' 13.0%, about 3.6 times as much per dollar of sales, on less than a third of Electronics' revenue.
- **Store rankings change with the yardstick.** Ranked by revenue per square metre, Mansoura Mega Mall moves from 6th to 2nd, just behind City Stars (15,207 against 15,297), while Cairo Festival City falls from 1st to 4th. Sharm Plaza ranks last at 6,067, about 40% of the leader. Flagship stores earn about 3 times the revenue of a Small store, but only about 1.3 times per square metre, so their edge comes mainly from size.
- **Returns.** 9.18% of orders were returned (1,056). Every return refunds the full order, and all arrive within 14 days (7.2 on average). Of the returns, 78% were refunded (3.21M), 14% are pending (0.40M) and 8% were rejected. About two thirds cite reasons that point to order handling or product quality, with Duplicate Order the most common (197) and the costliest (857K). That grouping is my own classification of the raw reason labels.
- **Customers.** 3,000 customers are on file, 2,932 have a completed purchase and 68 do not. Customers registered before 2022 account for 50.65% of revenue, the rest from newer customers.

## What This Suggests

If this were a real business, the questions worth taking back to it would be:

1. What happened to Mobile Phones in 2023? The data shows where the decline sits, but not why.
2. Is Electronics, at a 13% margin, priced or costed correctly compared with Clothing?
3. Why are duplicate orders the leading reason for a return, and can the ordering process prevent them?
4. Are the lowest-yielding stores per square metre carrying too much space?

These are questions the dashboard raises, not conclusions it supports. The dataset is fictional, so no real-world cause exists behind any pattern, and the findings illustrate how to analyse a business, not how this one performs. I also kept the store-level return rates (6.3% to 10.6%) out of any causal story, since each small store's rate rests on only 320 to 440 orders, and differences of a point or two fall within normal variation.

## Technical Stack

Power BI Desktop, Power Query (M), DAX, star-schema modelling, bookmarks, drill-down hierarchies, conditional formatting, custom report themes (JSON).

## Reflection

Several of the most useful lessons came from things that went wrong:

- **Empty is not null.** A flag meant to find missing emails reported every row as present, because Power Query imports blank CSV fields as empty strings. Checking the logic against the profile statistics caught it.
- **Time-intelligence needs a continuous timeline.** A year-on-year card showed 51.75% with no year selected, a cumulative artefact and not a growth rate. A month-on-month measure also misbehaved when several years were merged under one month name. Both were fixed by building measures and axes around real, contiguous periods.
- **Relationships can create phantom rows.** A registration-date relationship, whose dates start before the date table does, produced a "(Blank)" entry in an unrelated year slicer.
- **Ratios must compare the same group.** Revenue per employee divided revenue that included unattributed orders by named employees only, and the fix was to put both sides on the same population.
- **Chart choice is analysis.** A pie chart of profit margins silently turned independent ratios into meaningless shares, and a bar chart told the true story.
- **A merge can destroy data without an error.** A leftover filter shrank a table from 12,000 rows to 632 before a join. Checking row counts after every merge, not only that it ran, is what caught it.

---

*Data: Lotus Group Retail Dataset by Abdelrahman Mahmoud (Kaggle), licensed CC BY-NC-SA 4.0. Fictional data for educational and portfolio use. Cleaned and modelled by the author.*
