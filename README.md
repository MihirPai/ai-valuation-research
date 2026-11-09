# AI Investment and Valuation Research

## Overview

This project investigates whether higher R&D and capital expenditure intensity are associated with higher valuation multiples among AI-exposed U.S. public companies, and whether those relationships become stronger when subsequent operating performance improves.

The analysis focuses on S&P 500 companies in the Information Technology and Communication Services sectors from 2021 through 2025.

The project combines:

- SEC EDGAR Company Facts financial data
- SEC 10-K filing text
- Historical stock price and share data
- Rules-based AI exposure classification
- Panel-style regression analysis with robust standard errors

The goal is to build a transparent and reproducible empirical research pipeline rather than claim causal effects.

## Research Question

> Among AI-exposed U.S. public companies, are higher capital expenditure and R&D investment intensity associated with higher valuation multiples, and is that relationship stronger when subsequent operating performance improves?

R&D and capital expenditures are treated as broad investment measures. They should not be interpreted as direct measures of AI spending.

## Dataset

The initial universe contains 97 S&P 500 companies from:

- Information Technology
- Communication Services

The study covers fiscal years 2021–2025, producing a maximum panel of 485 company-year observations.

### AI Exposure

10-K filings are searched for the following terms:

- artificial intelligence
- machine learning
- generative AI
- large language model
- large language models

A company-year is classified as AI-exposed when at least two total occurrences are found across these terms.

Final classification:

- 348 AI-exposed company-years
- 118 non-AI-exposed company-years
- 19 company-years without a usable 10-K classification

## Core Variables

### Investment Measures

**R&D Intensity**

`R&D Expense / Revenue`

**CapEx Intensity**

`Capital Expenditures / Revenue`

### Valuation Measures

**Enterprise Value**

`Market Capitalization + Total Debt - Cash`

**EV / Sales**

`Enterprise Value / Revenue`

**Price / Sales**

`Market Capitalization / Revenue`

### Controls

- Log total assets
- Leverage
- Sector
- Fiscal year

### Subsequent Performance

Future revenue growth is defined as the following fiscal year's year-over-year revenue growth.

## Data Engineering

Financial statement data are retrieved from the SEC EDGAR Company Facts API.

Because companies sometimes use different XBRL tags for economically similar concepts, the pipeline includes fallback logic for variables such as:

- Revenue
- R&D
- Capital expenditures
- Debt
- Cash
- Assets

Historical stock prices are collected with `yfinance`.

Shares outstanding are primarily taken from SEC filings. Historical share counts are adjusted for subsequent stock splits before being multiplied by split-adjusted historical stock prices.

Missing financial observations remain missing rather than being replaced with zero.

## Regression Design

All primary regressions use OLS with HC3 heteroskedasticity-robust standard errors.

### Model 1: Baseline

EV/Sales is regressed on:

- R&D intensity
- CapEx intensity

Usable observations: **216**

### Model 2: Controlled

Adds:

- Log assets
- Leverage
- Sector fixed effects
- Fiscal-year fixed effects

Usable observations: **216**

### Model 3: Interaction

Adds:

- Future revenue growth
- R&D intensity × future revenue growth
- CapEx intensity × future revenue growth

Continuous interaction variables are mean-centered.

Usable observations: **167**

## Main Results

| Variable | Baseline | Controlled | Interaction |
|---|---:|---:|---:|
| R&D intensity | 24.293*** | 28.092*** | 23.109*** |
| CapEx intensity | -10.273*** | -11.392*** | -10.517* |
| Future revenue growth |  |  | 11.876*** |
| R&D × Future growth |  |  | -19.186 |
| CapEx × Future growth |  |  | -27.830 |
| Log assets |  | 0.484 | 0.175 |
| Leverage |  | 4.128 | 3.712 |
| Observations | 216 | 216 | 167 |
| R-squared | 0.157 | 0.209 | 0.374 |

Significance levels:

- `***` p < 0.01
- `**` p < 0.05
- `*` p < 0.10

## Interpretation

The main result is that R&D intensity is consistently positively associated with EV/Sales among AI-exposed company-years.

A one-percentage-point increase in R&D intensity corresponds to roughly a 0.24–0.28 increase in EV/Sales in the baseline and controlled specifications.

CapEx intensity is negatively associated with EV/Sales in the baseline and controlled models.

Future revenue growth is positively associated with EV/Sales in the interaction model.

However, neither the R&D × future growth interaction nor the CapEx × future growth interaction is statistically significant.

Therefore, the analysis does not provide evidence that the valuation relationship of R&D or CapEx becomes stronger when subsequent revenue growth improves.

These results should be interpreted as associations rather than causal effects.

## Figures

### R&D Intensity and EV/Sales

![R&D Intensity and Valuation](figures/rd_intensity_vs_ev_sales.png)

### CapEx Intensity and EV/Sales

![CapEx Intensity and Valuation](figures/capex_intensity_vs_ev_sales.png)

## Project Structure

```text
ai-valuation-research/
├── data/
│   ├── raw/
│   └── clean/
├── figures/
│   ├── rd_intensity_vs_ev_sales.png
│   └── capex_intensity_vs_ev_sales.png
├── notebooks/
│   └── 01_data_collection.ipynb
├── methodology.md
├── README.md
└── requirements.txt
```

## Key Output Files

The cleaned outputs include:

- `final_analysis_dataset.csv`
- `ai_classification_2021_2025.csv`
- `basic_regression_sample.csv`
- `controlled_regression_sample.csv`
- `interaction_regression_sample.csv`
- `descriptive_statistics.csv`
- `regression_results_summary.csv`
- `regression_table_final.csv`

## Limitations

Several limitations should be considered.

The sample is restricted to large S&P 500 technology and communication-services companies, so the results may not generalize to smaller firms or other industries.

R&D expense and capital expenditure represent total company investment rather than direct AI investment.

AI exposure is based on keyword disclosure in 10-K filings and does not measure the actual scale or economic importance of AI activity.

Missing accounting observations create an unbalanced panel and reduce the usable regression sample.

Market values and accounting values are measured on slightly different dates in some cases because shares-outstanding observations may occur near, but not exactly on, fiscal year-end.

Some financial variables contain large outliers, particularly revenue growth.

Finally, the regressions identify statistical associations and should not be interpreted as causal estimates.

## Tools

- Python
- pandas
- NumPy
- requests
- lxml
- yfinance
- matplotlib
- statsmodels
- Jupyter Notebook
- SEC EDGAR APIs