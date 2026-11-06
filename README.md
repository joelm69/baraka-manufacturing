<p align="center">
  <img src="logo/baraka-logo-horizontal.png" alt="Baraka Manufacturing" width="360">
</p>

# Baraka Manufacturing: Sales, Margin & Plan Dashboard

**Sales are growing faster every year. Profit isn't keeping up.** This project is a three-page Tableau dashboard that shows a Kenyan consumer-goods manufacturer why, where its profit is earned, where discounts eat it and whether the business is on plan.

- **Interactive dashboard:** [Tableau Public](https://public.tableau.com/app/profile/joel.mutiso/viz/BarakaManufacturing/PerformanceOverview)
- **Case study:** [Portfolio page](https://joeldataportfolio.streamlit.app/~/+/baraka_manufacturing)

**Tools:** Tableau · Excel · Python (pandas, for validating every figure)

---

## The brief

Baraka Manufacturing sells six products (cooking oil, maize flour, bar soap, detergent, mango juice and tissue) to five customer segments across seven markets in Kenya, Uganda, Rwanda and Tanzania. The brief supplied 6,264 monthly sales records and 180 monthly sales targets for 2023–2025 and asked for:

1. A data model with a date table and a segment table
2. A library of measures, including last-year and year-to-date comparisons
3. Three dashboard pages:
   - **Performance Overview:** KPI cards and monthly sales and profit against last year
   - **Margin Drivers:** profit and margin by segment, product and discount band
   - **Market & Plan:** domestic vs export markets and actual vs target by month
4. A one-page note for the CFO with findings and recommendations

## 2025 at a glance

| Sales | Profit | Profit margin | Vs sales plan |
|---|---|---|---|
| **KES 107.3M** ▲ 7.7% | **KES 26.8M** ▲ 4.3% | **25.0%** ▼ 0.8 pts | **−KES 0.3M** ▼ 0.3% |

Sales growth sped up from 5.8% in 2024 to 7.7% in 2025 but profit grew only 1.4% and then 4.3%. Margin slipped from 26.9% in 2023 to 25.8% and then 25.0%.

---

## Business questions answered

Figures are for 2025 unless stated. Every number was checked against the source data.

### 1. Which segment brings in the most profit and which has the lowest margin?

**Supermarkets** bring in the most profit (KES 12.3M). **Distributors** have the lowest margin (16.0%), even though they are the second-largest segment by sales (KES 27.9M). Their margin has slipped from 18.2% in 2023.

### 2. How does margin change from the None discount band to the High band?

It falls at every step and in every segment: **30.1% at full price to 12.4% at High discount**, a drop of 17.7 points.

| Segment | None | High | Change |
|---|---|---|---|
| Small shops | 33.6% | 20.9% | −12.7 pts |
| Hotels and restaurants | 30.7% | 18.9% | −11.8 pts |
| Supermarkets | 29.5% | 17.5% | −12.0 pts |
| Government | 27.0% | 11.2% | −15.8 pts |
| Distributors | 23.5% | 10.2% | −13.3 pts |

The High band is getting worse each year: 15.0% in 2023, 14.1% in 2024, 12.4% in 2025.

### 3. Which product has grown fastest since 2023?

**Mango Juice 1L:** sales up **139.7%** (KES 5.9M to 14.1M) with the highest margin in the range (34.4%). It produced KES 8.2M of the company's KES 13.1M sales growth since 2023 and is the only product earning more profit than in 2023.

### 4. Which markets are behind target and since when?

Targets exist only by segment and month, so markets were judged on growth instead. **Kigali** is the only market shrinking (▼ 10.4% in 2024, ▼ 7.5% in 2025). The company missed plan in **all 12 months of 2024** and in 8 of 12 months of 2025. Supermarkets were behind plan for 15 months in a row (June 2024 to August 2025).

### 5. Which months are strongest and weakest each year?

**December is the strongest and January the weakest in every year** for both sales and profit. December sells about 60% more than January.

### 6. Where is discounting costing the most margin?

**With Distributors in the High discount band.** Distributors received KES 2.8M of the KES 5.5M given in discounts in 2025 from 26% of sales. 41% of their sales are at High discount, where they keep a 10.2% margin. On those sales Baraka gave away KES 2.0M in discounts and kept KES 1.2M in profit.

### 7. Which segment should get more budget?

**Small shops, led by Mango Juice.** They have the highest margin (31.6%), the fastest profit growth (▲ 5.8% in 2025, ▲ 10.8% over two years), only 4% of sales at High discount and are back on plan. Distributors' profit is flat (▲ 0.6%) and below 2023.

### 8. What would you change in the discount policy?

1. **Cap the High band, starting with Distributors.** Allow High discounts only for clear conditions such as volume tiers or early payment.
2. **Set a margin floor of about 15%.** Deals below it need finance sign-off.
3. **Review discounting monthly** so a shift like June–July 2025 is caught within the month.

Moving half of Distributors' High-band sales to the Medium band would add an estimated KES 0.4M of profit a year if volume holds.

---

## How it was built

### Data model

Sales and targets are recorded at different levels of detail. Each sale is a month, segment, market, product and discount band (6,264 rows). Each target is just a month and segment (180 rows: 36 months × 5 segments).

- A first attempt joined the two tables on segment. That matched every sale to all 36 monthly targets for its segment and produced **225,504 rows** (6,264 × 36), inflating every total.
- The fix was to **union** the two tables (6,444 rows) so each value exists once, then **relate** the union to shared Date, Segment, Market and Product tables. One filter now controls both sales and targets.
- The fact table's raw month, segment, market and product columns are hidden so charts always use the shared dimension tables.
- Target rows have no market, so selecting a market hides the plan charts. The dashboard explains this in a note.

### Measures

A named measure library so every chart uses one definition: Total Sales, Gross Sales, Total Discounts, Discount %, Total COGS, Total Profit, Profit Margin, Units Sold, Target Sales, Variance to Target and Variance %. Margins are always a ratio of totals (`SUM(profit) / SUM(sales)`), never an average of row-level margins.

### Time intelligence

A **Selected Year** parameter drives current-year and last-year measures:

```
Sales CY = SUM(IF YEAR([month_raw]) = [Selected Year]     THEN [Sales Kes] END)
Sales LY = SUM(IF YEAR([month_raw]) = [Selected Year] - 1 THEN [Sales Kes] END)
```

A normal year filter would remove last year's rows and break every comparison. The same pattern gives Sales Growth %, Profit Growth %, Margin Change (pts), Target CY, Variance CY and Sales YTD.

### Design

- Colour only carries meaning: green for good, orange for bad, charcoal and grey for everything else.
- Colour is tied to stable fields so it stays correct when the year changes.
- The variance heatmap's diverging palette is centred at zero so grey always means on plan.
- Click a segment to filter the Margin Drivers page and click any mark to highlight it across charts.

---

## Repository contents

| Path | What it is |
|---|---|
| `tableau/Baraka Manufacturing.twbx` | The packaged Tableau workbook with its data. Opens in Tableau Desktop or Tableau Public. |
| `Baraka_Manufacturing_ETL.xlsx` | The modelled data: Dim_Date, Dim_Segments, Dim_Markets, Dim_Products, Fact_Sales and Fact_Targets |
| `baraka-sales.csv`, `baraka-targets.csv`, `baraka-markets.csv`, `baraka-products.csv` | The raw data supplied with the brief |
| `portfolio/baraka-case-study.html` | A standalone case-study page |
| `logo/` | The project logo and portfolio card images (SVG and PNG) |

## Limitations

- Profit is gross profit: sales minus discounts minus cost of goods. Overheads and tax are not in the data.
- Targets exist only by segment and month. Setting market-level targets from 2026 would let market performance be judged against plan.
- 2023 is the first year of data so year-on-year comparisons start in 2024.
- Mango Juice launched recently so part of its growth is a new product ramping up.
- The discount-policy estimate assumes customers keep buying the same volume.

---

Portfolio project based on a practice brief and dataset. Analysis and dashboard by [Joel Mutiso](https://www.linkedin.com/in/joel-mutiso/).
