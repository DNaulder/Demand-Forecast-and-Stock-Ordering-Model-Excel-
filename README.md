# Demand Forecast and Stock Ordering Model (Excel)

A small forecasting project built in Excel on **simulated** vehicle sales data. It forecasts monthly demand for four vehicle segments, tests how accurate the forecast is, and turns the forecast into a recommended order quantity for each segment.

I built this to practise the kind of supply and demand planning work found in inventory and forecasting analyst roles, and to move my skills from IT support into data analysis.

## The business question

*How many units of each segment should be ordered now so that stock covers expected demand over the supplier lead time, plus a target cover period, given current stock and what is already on order?*

## Data

All data is simulated for practice. It is not real sales, stock or market data. The workbook (`Demand_Forecast_Practice_Dataset.xlsx`) contains:

| Sheet | Contents |
|---|---|
| `Monthly_Sales` | Units sold per month for four generic segments (Hatchback, SUV, Ute, Hybrid), Jan 2023 to Oct 2026, with helper columns for year, month number and seasonal index |
| `Market_Index` | Simulated monthly industry registrations (external data) |
| `Stock_Position` | Stock on hand, stock on order, lead time and target cover per segment |
| `Seasonality_Grid` | Seasonal index by calendar month for each segment |
| `Forecast_<segment>` | One forecast and ordering sheet per segment |
| `Forecast_Sheet_SUV` | Excel's automatic (ETS) forecast, used as a benchmark |
| `Market_Share` | Pivot table on the Excel data model showing each segment's share of the market |
| `Summary` | Method, results, findings and limitations |

## Method

1. **Seasonality.** For each segment, each month's sales were divided by that year's average monthly sales. These ratios were averaged by calendar month to give 12 seasonal indices per segment, using 2023 to 2025 only.
2. **Deseasonalise.** Actual sales were divided by the seasonal index to remove the repeating yearly pattern.
3. **Trend.** A linear trend was fitted to the deseasonalised series (`FORECAST.LINEAR`).
4. **Reapply seasonality.** The trend forecast was multiplied by the month's seasonal index.
5. **Test.** The most recent six months (May to Oct 2026) were held back. The model was fitted on earlier data only and its forecasts were compared with actual sales, using mean absolute percentage error (MAPE).
6. **Benchmarks.** Two comparisons: a naive forecast ("same month last year") for all four segments, and Excel's ETS forecast for SUV.
7. **Ordering.** Units to order = forecast demand over (lead time + target cover) minus stock on hand minus stock on order. Demand is summed over the forecast months with `SUMPRODUCT`, and stock figures are looked up with `INDEX`/`MATCH`.

## Results

| Segment | Model MAPE | Naive MAPE | Forecast demand over lead time + cover | Units to order |
|---|---|---|---|---|
| Hatchback | 4.0% | 4.1% | 624 | 204 |
| SUV | 5.4% | 5.2% | 1,551 | 1,001 |
| Ute | 9.1% | 4.5% | 2,114 | 764 |
| Hybrid | 7.3% | 17.1% | 1,192 | 813 |

For SUV, Excel's ETS forecast scored about 5.6% on the same six test months.

## Key findings

- **SUV and Hybrid are the most urgent orders.** Stock on hand covers roughly one month of recent sales for SUV and under one month for Hybrid, against lead times of three and two months.
- **The model clearly beats the naive benchmark only for Hybrid** (7.3% against 17.1%), where steady growth makes "same month last year" a poor guide.
- **For Hatchback and SUV the model is about level with the naive benchmark.** The extra complexity adds little on this data.
- **Ute is the weak case.** The model (9.1%) is less accurate than the naive benchmark (4.5%) over the test months.
- **SUV: manual model and ETS are similar** (5.4% against about 5.6%).

## Limitations

- The data is simulated, so these accuracy figures would not be expected to carry over to real sales.
- The test covers only six months, so the MAPE figures are noisy and one unusual month can move them.
- A straight-line trend suits stable and steadily growing segments but not segments where growth changes.
- Seasonal indices rely on only three years of history.
- ETS was tested on SUV only.
- The ordering calculation ignores order quantity minimums, supplier constraints and price changes.

## Skills demonstrated

Excel tables and structured references, `AVERAGEIFS`, `SUMIFS`, `INDEX`/`MATCH`, `SUMPRODUCT`, `FORECAST.LINEAR`, `FORECAST.ETS`, pivot tables on the Excel data model, DAX measures, forecast accuracy testing (MAPE and benchmarking), and communicating assumptions and limitations.

## How to use

Open the workbook and go to `Summary` first. Each `Forecast_<segment>` sheet runs from one cell: change the segment name in `K1` and the sheet updates its seasonality lookup, stock lookup and order quantity.
