# Cross-Sectional Alpha Signal Research and Market-Neutral Portfolio Construction

## Overview

This project investigates whether historical stock-price and trading-volume information can predict future stock returns through cross-sectional quantitative signals.

Using the JPX Tokyo Stock Exchange stock-price dataset, the research pipeline covers data exploration, feature engineering, predictive signal evaluation, statistical significance testing, and market-neutral long-short portfolio backtesting.

The primary objective is to distinguish statistically detectable return patterns from signals that may generate economically meaningful returns after transaction costs.

## Research Objectives

- Explore and validate historical stock-market data.
- Engineer cross-sectional predictors using price, volatility, momentum, reversal, and liquidity information.
- Evaluate predictive strength using daily cross-sectional Information Coefficients (IC).
- Test statistical significance using heteroskedasticity-and-autocorrelation-consistent (HAC) standard errors and false discovery rate correction.
- Construct equal-weighted market-neutral long-short portfolios.
- Evaluate out-of-sample performance and transaction-cost sensitivity.
- Examine the robustness of results across different portfolio sizes.

## Dataset

The project uses the JPX Tokyo Stock Exchange stock-price dataset.

The original dataset contains daily stock observations from January 2017 through December 2021, covering approximately 2,000 securities.

The raw dataset includes stock prices, trading volume, adjustment factors, and a forward-return target.

**Dataset files are not included in this repository** because the prepared dataset exceeds GitHub's standard 100 MB file-size limit.

Obtain the dataset from its permitted source and place the required files in the local directory structure expected by the notebooks.

## Repository Structure

```text
Cross-Sectional-Alpha-Signal-Research-and-Market-Neutral-Portfolio-Construction/
│
├── notebooks/
│   ├── 01_data_exploration.ipynb
│   └── 02_feature_engineering.ipynb
│
├── .gitignore
├── readme.md
└── requirements.txt
```

The prepared dataset, `jpx_prepared_prices.csv`, is generated or stored locally and is excluded from version control because of its size.

## Methodology

### 1. Data Exploration and Preparation

The first notebook explores the dataset's structure, missing observations, duplicate records, and price and volume distributions.

It also prepares the data for feature engineering and examines the relationship between adjusted prices and the supplied forward-return target.

### 2. Feature Engineering

The second notebook constructs five features:

| Feature | Description |
|---|---|
| `Momentum_5D` | Five-session price momentum |
| `Momentum_20D` | Twenty-session price momentum |
| `Reversal_5D` | Negative of five-session momentum |
| `Volatility_20D` | Rolling 20-session standard deviation of daily returns |
| `Liquidity_20D` | Rolling 20-session average trading value |

Features are calculated using trailing observations. Missing feature values are retained rather than automatically replaced with zero.

### 3. Predictive Signal Evaluation

Daily cross-sectional Spearman Information Coefficients are calculated to measure the rank relationship between each feature and the future-return target.

The analysis evaluates:
- Mean and median IC.
- IC variability and Information Coefficient Information Ratio (ICIR).
- Percentage of positive-IC days.
- Research-period versus out-of-sample performance.
- Statistical significance using HAC standard errors.
- Multiple-testing adjustment using false discovery rate correction.

### 4. Market-Neutral Portfolio Construction

The backtest ranks stocks daily and constructs an equal-weighted long-short portfolio.

- Long positions are assigned to the highest-ranked stocks.
- Short positions are assigned to the lowest-ranked stocks.
- Long exposure is 50% and short exposure is 50%.
- Gross exposure is 100%, with net exposure of zero.

The strategy is evaluated using long-short portfolio fractions of 5%, 10%, and 20%.

The return alignment assumes signals are calculated after the signal-date close, positions are entered at the next observed session's close, and the forward target return is earned over the subsequent close-to-close interval. Actual execution prices are not simulated.

### 5. Transaction Costs and Robustness

Portfolio turnover is calculated from changes in stock weights between consecutive portfolios.

Net returns are estimated as:

\[
R_t^{net}=R_t^{gross}-T_t c
\]

where \(T_t\) is portfolio turnover and \(c\) is the assumed transaction-cost rate.

The analysis evaluates transaction-cost scenarios and compares performance across different portfolio fractions.

Performance metrics include:
- Cumulative return.
- Annualized return.
- Annualized volatility.
- Sharpe ratio.
- Maximum drawdown.
- Average daily turnover.

## Key Findings

### Predictive Relationships

The 20-session volatility feature exhibited the strongest negative mean IC among the tested features, at approximately **-0.0164**.

It remained statistically significant after HAC inference and false discovery rate correction.

The momentum, reversal, and liquidity features exhibited weaker predictive relationships in the evaluated sample. Five-session reversal is defined as the exact negative of five-session momentum, so these two features are not independent signals.

### Out-of-Sample Evaluation

The 2021 period was used as the out-of-sample test period, following research-period analysis on 2017–2020 data.

For the 10% long-short portfolio, the volatility signal produced approximately:

| Metric | Result |
|---|---:|
| Gross cumulative return | +3.16% |
| Cumulative return at 5 bps transaction costs | +1.25% |
| Cumulative return at 10 bps transaction costs | -0.62% |
| Average daily turnover | 16.49% |

These results show that the volatility strategy's profitability is sensitive to transaction costs.

The tested momentum and reversal strategies did not demonstrate robust profitability in the evaluated backtest.

### Overall Interpretation

The volatility feature provides the strongest statistical evidence of a cross-sectional relationship with future returns among the features examined.

However, its positive gross performance does not translate into robust net profitability across all tested transaction-cost assumptions.

The results highlight the distinction between statistical predictability and economic profitability. The findings do not establish that the strategy would be profitable in live trading.

## Limitations

- The out-of-sample evaluation covers only the 2021 period.
- Transaction costs are modelled using fixed assumptions rather than measured execution costs.
- The backtest does not fully model market impact, bid-ask spreads, short-sale constraints, stock-borrow costs, or execution-price slippage.
- Results depend on the selected features, portfolio construction method, dataset, and evaluation period.
- The findings may not generalize to other market conditions.

## Reproducibility

### Requirements

Python and Jupyter Notebook are required.

Install the dependencies listed in `requirements.txt`:

```bash
pip install -r requirements.txt
```

### Running the Notebooks

1. Clone the repository.
2. Obtain the required JPX dataset files from their permitted source.
3. Place the dataset files in the local paths expected by the notebooks.
4. Open and run `notebooks/01_data_exploration.ipynb`.
5. Ensure the prepared dataset is generated or saved at the path expected by the second notebook.
6. Open and run `notebooks/02_feature_engineering.ipynb`.

**Note:** The prepared CSV is not tracked by Git. If the notebooks expect it to exist, it must be generated locally before running the feature-engineering notebook. Adjust file paths if your local directory structure differs from the notebook configuration.

## Conclusion

This research evaluates whether simple price- and volume-derived features can support cross-sectional stock selection.

Among the tested signals, 20-session volatility exhibited the strongest statistical relationship with future returns. Its long-short strategy showed positive gross performance in the test period, but net profitability was sensitive to transaction costs.

The findings provide a starting point for further quantitative research into independent predictive signals, realistic trading costs, and more extensive out-of-sample validation.

## Disclaimer

This project is intended for educational and quantitative research purposes only. Backtested results do not guarantee future performance and should not be interpreted as investment advice.
