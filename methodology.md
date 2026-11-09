# Methodology

## Research Question

Among AI-exposed U.S. public companies, are higher capital expenditure and R&D investment intensity associated with higher valuation multiples, and is that relationship stronger when subsequent operating performance improves?

This project studies associations rather than causal effects. Capital expenditure and R&D are treated as broad investment measures and are not interpreted as direct measures of AI spending.

## Sample

The initial company universe consists of S&P 500 firms in the Information Technology and Communication Services sectors.

The resulting universe contains 97 companies.

The analysis covers fiscal years 2021 through 2025, producing a maximum possible panel of 485 company-year observations.

Because some accounting and market variables are unavailable for particular firms or years, the regression sample is unbalanced.

## Financial Data

Financial statement data are collected from SEC EDGAR Company Facts using XBRL data.

Variables include:

- Revenue
- Research and development expense
- Capital expenditures
- Operating income
- Total assets
- Cash and cash equivalents
- Total debt

Fallback XBRL tags are used when firms report economically similar items using different tags.

Missing financial observations are retained as missing rather than assumed to be zero.

## Derived Variables

### R&D Intensity

R&D intensity is defined as:

R&D Expense / Revenue

### CapEx Intensity

CapEx intensity is defined as:

Capital Expenditures / Revenue

### Operating Margin

Operating margin is defined as:

Operating Income / Revenue

### Leverage

Leverage is defined as:

Total Debt / Total Assets

### Firm Size

Firm size is measured using the natural logarithm of total assets.

### Revenue Growth

Revenue growth measures the year-over-year percentage change in revenue.

### Future Revenue Growth

Future revenue growth is the following fiscal year's revenue growth and is used as a measure of subsequent operating performance.

## Market Valuation

Historical stock prices are collected using yfinance.

Shares outstanding are primarily collected from the SEC DEI `EntityCommonStockSharesOutstanding` fact.

When the SEC Company Facts endpoint does not provide usable shares data, historical shares from yfinance are used as a fallback.

Historical share counts are adjusted for subsequent stock splits so that they are on the same split-adjusted basis as historical stock prices.

Market capitalization is calculated as:

Market Capitalization = Closing Stock Price × Split-Adjusted Shares Outstanding

Enterprise value is calculated as:

Enterprise Value = Market Capitalization + Total Debt - Cash

The primary valuation measure is:

EV / Sales = Enterprise Value / Revenue

Price / Sales is used as a secondary valuation measure.

Market values are measured using the stock price corresponding to the shares-outstanding observation date, or the most recent prior trading day when necessary.

## AI Exposure Classification

AI exposure is identified using each company's annual 10-K filing.

The following terms are searched in the filing text:

- artificial intelligence
- machine learning
- generative AI
- large language model
- large language models

A company-year is classified as AI-exposed when the filing contains at least two total occurrences across these terms.

The rule is designed as a simple, reproducible filing-based classification rather than a measure of AI investment or AI spending.

Across the 485 company-year panel:

- 348 observations are classified as AI-exposed
- 118 observations are classified as not AI-exposed
- 19 observations do not have a usable 10-K classification

## Regression Models

All primary regressions are estimated using ordinary least squares with HC3 heteroskedasticity-robust standard errors.

### Baseline Model

EV/Sales is regressed on:

- R&D intensity
- CapEx intensity

The baseline model contains 216 usable AI-exposed company-year observations.

### Controlled Model

The controlled specification adds:

- Log assets
- Leverage
- Sector fixed effects
- Fiscal-year fixed effects

The controlled model also contains 216 observations.

### Interaction Model

The final specification adds:

- Future revenue growth
- R&D intensity × future revenue growth
- CapEx intensity × future revenue growth

Continuous interaction variables are mean-centered for interpretation.

The interaction model contains 167 observations.

## Main Findings

In the baseline model:

- R&D intensity has a positive and statistically significant association with EV/Sales.
- CapEx intensity has a negative and statistically significant association with EV/Sales.

These relationships remain similar after controlling for firm size, leverage, sector, and fiscal year.

In the interaction model:

- R&D intensity remains positively associated with EV/Sales.
- Future revenue growth is positively associated with EV/Sales.
- The R&D × future revenue growth interaction is not statistically significant.
- The CapEx × future revenue growth interaction is not statistically significant.

Therefore, the analysis does not provide evidence that the relationship between investment intensity and valuation becomes stronger when subsequent revenue growth improves.

## Limitations

This analysis has several limitations.

First, the sample is limited to large S&P 500 firms in Information Technology and Communication Services, so results may not generalize to smaller firms or other industries.

Second, R&D and capital expenditures represent total company investment and should not be interpreted as direct measures of AI-related spending.

Third, the AI-exposure measure is based on keyword mentions in 10-K filings. It captures disclosure of AI-related topics but does not measure the scale, quality, or economic importance of a firm's AI activity.

Fourth, missing financial data produce an unbalanced panel and reduce the usable regression sample.

Fifth, historical market capitalization uses shares-outstanding dates that may differ slightly from fiscal year-end accounting dates.

Sixth, several financial variables contain large outliers, particularly revenue growth.

Finally, the regressions identify associations and should not be interpreted as causal estimates.