# Pakistan Real Estate Market Analysis

An end-to-end data analysis project in Python and Pandas: generating a realistic property-listing dataset, auditing and cleaning it, exploring price drivers across five Pakistani cities, and turning the results into business recommendations and a PDF report.

> **Data note:** all data is **synthetic**, generated with NumPy (fixed seed `42`) to mimic the structure and the data-quality problems of a real property portal. The findings below demonstrate the analytical method; they are **not** statements about the real Pakistani property market.

## Project Overview

**Business problem.** A real estate company wants to know what drives property prices across Lahore, Karachi, Islamabad, Faisalabad and Multan, which market segments dominate its inventory, and how reliable its listing data is.

**What this project does**

- Generates a reproducible dataset of 500 listings and 15 columns, with deliberately injected missing values and duplicate listings.
- Audits and cleans the data with documented, defensible choices (group-wise imputation, duplicate detection that ignores the listing ID, validation rules).
- Engineers pricing features: price per Marla, property age and listing month.
- Explores the market with grouping, pivot tables, cross-tabulations, correlation analysis and time-series resampling.
- Segments listings into Budget, Mid-range, Premium and Luxury bands.
- Exports a cleaned dataset, 10 summary tables, 12 charts and a 5-page PDF report.

## Tech Stack

| Area | Tools |
|---|---|
| Language | Python 3 |
| Data manipulation | Pandas, NumPy |
| Visualization | Matplotlib |
| Reporting | ReportLab (PDF) |
| Environment | Jupyter Notebook |

Developed and tested with Python 3.12, pandas 3.0, NumPy 2.4 and Matplotlib 3.10. The `requirements.txt` lists minimum versions; other versions have not been tested.

## Key Findings

Based on 495 clean listings (500 generated, 5 duplicates removed, 113 missing cells filled).

| # | Finding | Evidence |
|---|---|---|
| 1 | Islamabad is the most expensive market, Faisalabad the most affordable | Average price PKR 38.6M vs 13.9M; Islamabad also leads on price per Marla (PKR 4.06M) |
| 2 | Property size is the dominant price driver | Size-price correlation 0.81; distance to centre (-0.10) and age (-0.01) are weak |
| 3 | Prices are heavily right-skewed | Mean PKR 27.0M vs median PKR 16.0M; 9.1% of listings are high-price outliers |
| 4 | Commercial property has the highest price per Marla in every city | Median PKR 5.38M per Marla; houses make up 47.5% of listings |
| 5 | Inventory is concentrated in the mid-range | 44.0% of listings are priced between PKR 10M and 30M |
| 6 | Listing volume shows no clear trend | 15 to 33 listings per month; fitted trend is almost flat |
| 7 | Data quality needs attention at source | About 1.5% of cells were missing and 5 listings were duplicated |

### Recommendations

1. Benchmark and price listings by **price per Marla** within city and property type, because total price mostly reflects size.
2. Focus pricing and marketing effort on the **mid-range** segment, and value Luxury listings individually because they contain the extreme prices.
3. Enforce **required fields and duplicate checks** at listing entry to reduce estimated values.

## Visualizations

<table>
  <tr>
    <td><img src="reports/figures/avg_price_by_city.png" alt="Average property price by city" width="420"></td>
    <td><img src="reports/figures/price_size_distributions.png" alt="Price and size distributions" width="420"></td>
  </tr>
  <tr>
    <td align="center"><sub>Average price by city</sub></td>
    <td align="center"><sub>Right-skewed price and size distributions</sub></td>
  </tr>
  <tr>
    <td><img src="reports/figures/correlation_heatmap.png" alt="Correlation heatmap" width="420"></td>
    <td><img src="reports/figures/pivot_and_status.png" alt="Price by city and type, and listing status" width="420"></td>
  </tr>
  <tr>
    <td align="center"><sub>Correlation heatmap: size drives price</sub></td>
    <td align="center"><sub>Average price by city and type; listing status by city</sub></td>
  </tr>
  <tr>
    <td><img src="reports/figures/monthly_listings.png" alt="Monthly listings" width="420"></td>
    <td><img src="reports/figures/price_categories.png" alt="Properties by price category" width="420"></td>
  </tr>
  <tr>
    <td align="center"><sub>Monthly listings (no clear trend)</sub></td>
    <td align="center"><sub>Properties by price category</sub></td>
  </tr>
</table>

All 12 charts are in [`reports/figures/`](reports/figures/), and the full write-up is in [`reports/real_estate_report.pdf`](reports/real_estate_report.pdf).

## Project Structure

```
.
├── real_estate_analysis.ipynb      # Main notebook: generation, cleaning, EDA, insights, report
├── requirements.txt
├── data/
│   ├── raw/pakistan_properties.csv           # Generated dataset (500 rows, 15 columns)
│   └── processed/cleaned_properties.csv      # Cleaned dataset (495 rows, with derived columns)
└── reports/
    ├── figures/                    # 12 charts (PNG)
    ├── tables/                     # 10 summary tables (CSV)
    └── real_estate_report.pdf      # Management report
```

## How to Run

```bash
# 1. Clone the repository (replace the placeholder with your own repository URL)
git clone https://github.com/<your-username>/pakistan-real-estate-analysis.git
cd pakistan-real-estate-analysis

# 2. Create and activate a virtual environment
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Open the notebook and run all cells
jupyter notebook real_estate_analysis.ipynb
```

Run the notebook from top to bottom (**Kernel > Restart & Run All**): later cells reuse variables from earlier ones. Running it regenerates every file in `data/` and `reports/`, and the fixed seed means the numbers will match those above.

If `reportlab` is not installed, the notebook prints a message and skips only the PDF step.

## Notebook Outline

1. **Executive Summary**
2. **Data Acquisition and Audit**: synthetic data generation, data dictionary, structural audit, statistical profile
3. **Data Cleaning and Preprocessing**: missing values, duplicates, type validation, feature engineering
4. **Exploratory Data Analysis and Visualizations**: segment queries, rankings, city and city-by-type comparisons, market composition, distributions, price drivers, listing trends
5. **Advanced Insights**: pivot tables and cross-tabulation, price segmentation, city-level income merge
6. **Business Recommendations, Deliverables and Limitations**
7. **Appendix**: data access patterns (`loc`, `iloc`, index handling)

## Key Design Decisions

- **Group-wise imputation.** Bedrooms, bathrooms and parking are filled with the median of the same property type, and missing prices with the median price per Marla of the same city and type multiplied by the property's size. A single overall median would give plots bedrooms and treat a 2-Marla flat like a 40-Marla house.
- **`"Unknown"` for missing labels.** Filling locality or seller type with the most frequent value would invent information.
- **Duplicates detected without `property_id`.** A re-listed property gets a new ID, so duplicates are found on all other columns.
- **Price per Marla as the comparison metric.** It removes the size effect that dominates total price.
- **Small groups flagged.** Averages for segments with very few listings (for example Multan commercial, 2 listings) are excluded or marked as unreliable.

## Limitations and Next Steps

**Limitations**

- The data is synthetic, so relationships (such as size driving price) reflect the generator's assumptions, not the real market.
- The city-level income and population table used for the merge is illustrative; the income-versus-price correlation it produces is not a finding.
- Imputed values (about 1.5% of cells) are estimates and slightly reduce natural variation.

**Next steps**

- Replace the generator with real listing data and rerun the pipeline.
- Add a predictive pricing model with cross-validation and feature importance.
- Add locality-level geographic analysis and an interactive dashboard (Streamlit or Power BI).
- Extend the time series to several years to test for genuine seasonality.

## Author

**<LinkeDin>**: [LinkedIn](www.linkedin.com/in/mehtab--hussain

) | [GitHub](https://github.com/mahtabhussainofficial)

Add a `LICENSE` file (for example MIT) before publishing.
