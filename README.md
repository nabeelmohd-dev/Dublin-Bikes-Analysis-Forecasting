# Dublin Bikes Data Analysis and Forecasting (R)

Exploratory analysis and short-term demand forecasting for Dublin's public
bike share system, using four months of station-level availability data.

## What this does

Given historical bike availability records across every Dublin Bikes
station, this project explores usage patterns and builds a model to
forecast bike availability an hour ahead. The pipeline:

1. **Data loading.** Four months of historical station data (January to
   April 2022) loaded and combined, alongside station metadata (location,
   capacity).
2. **Feature extraction.** Timestamps parsed into hour, weekday, and month
   components, with duplicates and missing values removed.
3. **Exploratory visualisation.** Hourly availability heatmaps, weekday
   distributions (ridgeline plots), monthly boxplots, a radial hour by
   weekday heatmap, and an interactive Leaflet map showing the busiest
   stations by average availability.
4. **Forecasting.** Hourly aggregation with lag features (1 hour, 2 hours,
   and 24 hours prior), then two models: a Random Forest (500 trees, via
   `ranger`) and a Linear Regression baseline, both predicting bike
   availability from time features and recent lags.
5. **Validation.** The Random Forest is trained on January and February,
   validated on March, then retrained on January through March to forecast
   April.

## Results

**Random Forest** (per station, per hour):

| Evaluation | RMSE | MAE | R-squared |
|---|---|---|---|
| March validation (trained on Jan-Feb) | 2.527 | 1.525 | 92.4% |
| April forecast (trained on Jan-Mar) | 2.607 | 1.554 | 91.9% |

The model explains over 90% of the variance in hourly bike availability a
month ahead of its training window, with a typical error of around 1.5
bikes per station per hour.

**Linear Regression** was also evaluated as a simpler baseline, but on a
different level of aggregation (total predicted bikes across all ~113
stations, summed per hour, for a single day) rather than the per-station
hourly level the Random Forest was scored on. On that basis it reached an
R-squared of 80.5% for April 1st, noticeably weaker than the Random Forest,
though the two numbers aren't directly comparable since they're measuring
different things. The honest takeaway is that the Random Forest is the
model actually validated at the level that matters (per station, per hour),
and the linear model serves mainly to show that a non-linear approach adds
real value over a simple baseline.

## Dataset

633,460 records across 113 unique stations, covering January through April
2022, sourced from a public Dublin Bikes historical dataset (station
availability, GPS coordinates, timestamps). The raw CSVs aren't included in
this repo; download the dataset separately and update the file paths at the
top of the notebook before running.

## Tech stack

R, `tidyverse` (data wrangling and visualisation), `lubridate` (datetime
handling), `tidymodels` and `ranger` (Random Forest), `parsnip` (linear
regression), `leaflet` (interactive station map), `ggridges` and `viridis`
(visualisation).

## Running it

```r
install.packages(c("tidyverse", "lubridate", "ggthemes", "scales",
                    "ggridges", "viridis", "leaflet", "tidymodels",
                    "ranger", "parsnip", "Metrics"))
```

Open `dublin-bikes-data-analysis-in-r.ipynb` in Jupyter with an R kernel,
or import the cells into RStudio, updating the dataset file paths first.
