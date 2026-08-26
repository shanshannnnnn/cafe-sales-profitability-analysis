# Campus Café Sales & Profitability Analysis — Power BI

An 11-page Power BI report analysing **~315,000 transaction records** across three
campus food outlets over three semesters, joining point-of-sale data to menu
costs, monthly operating costs and an academic calendar to answer a single
business question: how do these outlets raise revenue enough to hit a **$500,000
per semester** sales target?

The analysis identifies where each outlet loses ground — a specific weekday, a
specific time of day, and a cost structure that doesn't flex with demand — and
recommends targeted interventions rather than blanket discounting.

<!-- IMAGE: add docs/images/dashboard-overview.png, then uncomment
![Dashboard overview](docs/images/dashboard-overview.png)
-->

---

## Problem

> **Objective:** To improve total revenue and profitability of canteen vendors in
> order to support long-term financial sustainability.
>
> **Driving question:** How to improve Total Revenue so that the Profit of each
> Canteen increases?

Three outlets — **Makan Place**, **Food Club 22** and **Munch** — operate on the
same campus, serve overlapping menus, and are each measured against a $500k
per-semester sales target. They share a customer base whose presence is dictated
entirely by a timetable, which makes demand highly predictable *if* you model it
against the academic calendar rather than the ordinary calendar.

## Data model

Five source tables, related into a star-ish model:

| Table | Rows | Contents |
|---|---:|---|
| **Sales — Oct 2022** | 60,735 | Order ID, Date, Menu Item, Quantity, Location, Mode |
| **Sales — Apr 2023** | 104,486 | as above |
| **Sales — Oct 2023** | 150,444 | as above |
| **Menu Prices** (Oct 22 & Oct 23) | 21 items each | Category, Menu Item, **Cost**, **Price** |
| **Operating Costs** | 58 | Op Month, Location, Rental Cost, Staff Salaries, Utility Expenses |
| **Academic Calendar** | 547 | Semester, Week, Date, Weekday Attribute (e.g. School Day) |

Two joins do most of the analytical work:

**Menu Prices → Sales** turns quantities into money. Because the workbook holds
*two* price lists — October 2022 and October 2023 — the same basket can be costed
at both, which is what makes the **price-adjustment comparison** possible: how
much of the revenue change is more items sold, and how much is simply higher
prices?

**Academic Calendar → Sales** is the join that makes the findings interpretable.
A raw date tells you a Wednesday was quiet. A date joined to a semester, teaching
week and school-day flag tells you *why*.

## Measures

Built across the sales and cost tables:

| Measure | Purpose |
|---|---|
| `Total Revenue` | Quantity × menu price |
| `Total Ingredient Cost` | Quantity × item cost — the variable cost of goods |
| `Total Costs` | Ingredient cost plus rental, staff and utilities |
| `Total Profit` | Revenue less total costs |
| `Total Items Sold` | Unit volume |
| `Total Sales Transactions` | Distinct orders — separates basket size from footfall |
| `Fixed Costs` / `Variable Costs` | Cost-structure split |
| `AVG Menu Price (Before / After Adjustment)` | Effect of the price revision |
| `Sales Variance` | Actual revenue against the $500k semester target |
| `Date Duration (Days)` | Normalises comparisons across unequal periods |

Separating **transactions** from **items sold** matters more than it looks: it
distinguishes *fewer customers* from *customers buying less*, and those two
problems have completely different fixes.

## Report structure

| # | Page | What it answers |
|---:|---|---|
| 1 | Metrics Verification | Nine KPI cards reconciling revenue, costs and profit — a check that every measure ties out before any conclusion rests on it |
| 2 | Problem Statement | Objective and driving question |
| 3 | Menu & Outlet Analysis | Top 10 items by volume, sales share per outlet, average menu price before vs after adjustment |
| 4 | Revenue & Profit by Month | Monthly revenue and profit per outlet, with period totals |
| 5 | Sales Trend | Items sold by month and **by hour**, filterable by weekday |
| 6 | Specific Analysis | A single item's performance across semesters, and Eat-In vs Delivery mix |
| 7 | Cost Impact | Fixed vs variable cost split |
| 8 | Profitability | Profit per outlet across semesters |
| 9 | Peak Hour | Transactions by hour of day, per outlet |
| 10 | Sales Target | Revenue and variance against the $500k/semester target, per outlet |
| 11 | Recommendations | Transactions by weekday, and the resulting proposals |

<!-- IMAGES: see docs/images/README.md for the list of screenshots to add. -->

## Key findings

**Wednesday is the weakest trading day.** Transactions by day of week show a clear
Wednesday trough. Joined against the academic calendar, the cause is structural
rather than commercial — most students have no timetabled lessons that day, so
they simply aren't on campus. No amount of menu or service improvement fixes a
day when the customers are elsewhere.

**Demand is concentrated in hours, not spread across the day.** The hourly
transaction profile is sharply peaked, which is what drives the cost problem
below.

**The cost structure doesn't flex.** Rental, staff salaries and utilities are
fixed monthly commitments, while ingredient cost is the only component that
tracks demand. On a quiet day the outlet still pays full rent and full staffing —
so a low-traffic Wednesday costs nearly as much to run as a peak Monday. That is
precisely why the recommendations target *filling* quiet periods rather than
cutting costs.

**Price adjustment vs volume.** Comparing average menu price before and after the
October 2023 revision separates revenue growth driven by price from growth driven
by genuine volume.

## Recommendations

From the analysis, two interventions aimed directly at the Wednesday trough:

**1. Wednesday promotions.** Sales are lowest on Wednesday because most students
have no lessons that day. A targeted promotion — for example 30% off menu items —
would draw students from nearby institutions who otherwise wouldn't make the trip.
Because fixed costs are incurred regardless, additional Wednesday volume at a
discount still contributes toward covering them.

**2. Delivery platforms.** Listing on GrabFood or Foodpanda removes the trip
entirely, letting the outlets capture demand from students who are at home. The
existing data already shows a Delivery transaction mode, so the demand pattern
can be measured rather than assumed.

Together these target the specific gap between current revenue and the $500k
per-semester target, rather than trying to lift every day uniformly.

## What I'd do differently

- **Quantify the recommendations.** The report identifies the Wednesday trough but
  doesn't model what a 30% discount does to margin. Since ingredient cost per item
  is in the data, the break-even uplift — how many extra covers a 30% discount must
  generate to be worth running — is computable and would make the proposal far more
  persuasive.
- **Three pages share the name "Task 2".** They should be named for what they show.
- **No forecasting.** Three semesters of data supports a trend projection against
  the target rather than only reporting historical variance.
- **Basket analysis untouched.** Order ID groups items into baskets, so which items
  sell together — and therefore what to bundle in a promotion — is available in the
  data but unexplored.
- **Weekday Attribute underused.** The calendar distinguishes school days from
  non-school days, which would sharpen the Wednesday finding into a general model
  of demand versus campus presence.

## Technologies

**BI** — Microsoft Power BI Desktop (data model, DAX measures, interactive report)
**Modelling** — Star schema, relationships across sales, price, cost and calendar tables
**Source data** — Excel workbook, seven sheets
**Visuals** — KPI cards, clustered column & bar charts, line charts, donut chart, tables, slicers

## A note on the data

The source dataset and the `.pbix` file are **not published in this repository.**
The data was provided for the assignment and its licensing does not permit
redistribution, and the `.pbix` embeds the full ~315,000-row model inside it.

The schema, measures and findings are documented above so the analysis is fully
legible without it.

## License

MIT — see [LICENSE](LICENSE). Applies to the documentation in this repository;
the underlying dataset is not covered and is not included.
