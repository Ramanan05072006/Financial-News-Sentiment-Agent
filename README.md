# Financial News Sentiment Agent

> An asynchronous financial news NLP pipeline for sentiment analysis, leakage-safe return forecasting research, and rolling Information Coefficient evaluation.

**Project domain:** Financial Analytics · Natural Language Processing · Data Science · Quantitative Research

**Current status:** Project setup / implementation planned

## 1. Overview

Financial markets react to information released through earnings announcements, company updates, economic reports, analyst commentary, and other financial news. However, positive or negative language does not necessarily translate into predictable stock returns.

The Financial News Sentiment Agent investigates whether sentiment extracted from financial news contains statistically measurable information about subsequent market returns.

The project combines an asynchronous news-processing pipeline, financial sentiment analysis, point-in-time data alignment, rolling statistical evaluation, and an interactive research dashboard.

Unlike a basic sentiment-analysis notebook, this project is designed as a modular and reproducible research system.

## 2. Problem Statement

Financial news analysis presents several challenges:

- Financial language can express sentiment differently from ordinary language.
- News volume and processing latency can affect when a signal becomes available.
- Incorrect timestamp alignment can introduce look-ahead bias.
- A positive sentiment score does not guarantee positive future returns.
- Predictive relationships can weaken or change across market regimes.

The objective is to build a research pipeline that processes historical financial news and evaluates whether its sentiment signals are associated with future stock returns under realistic information-availability constraints.

## 3. Project Objectives

1. Build an asynchronous pipeline that simulates event-driven financial news ingestion.
2. Extract sentiment from financial headlines using baseline and finance-specific NLP models.
3. Aggregate sentiment by stock ticker and decision timestamp.
4. Prevent look-ahead bias through point-in-time feature construction.
5. Calculate forward returns and rolling Information Coefficients.
6. Compare sentiment-based signals against appropriate baselines.
7. Visualize sentiment trends, predictive relationships, and model diagnostics.
8. Create a reproducible and testable research workflow.

## 4. System Architecture

```text
Historical Financial News
          |
          v
Data Validation and Timestamp Normalization
          |
          v
Asynchronous Ingestion Queue
          |
          v
Concurrent News Processing Workers
          |
          v
Financial Sentiment Classification
          |
          v
Ticker-Level Sentiment Aggregation
          |
          v
Point-in-Time Feature Construction
          |
          v
Forward Return Alignment
          |
          v
Rolling IC and Statistical Evaluation
          |
          v
Interactive Research Dashboard
```

The ingestion layer will initially replay historical batches to simulate an incoming event stream. This is a simulation, not a live financial news service.

## 5. Dataset Strategy

### Primary dataset: FNSPID

The Financial News and Stock Price Integration Dataset (FNSPID) will be evaluated as the primary source for historical financial news and associated stock-price data.

Dataset repository:
https://github.com/Zdong104/FNSPID_Financial_News_Dataset

### Secondary dataset: Financial PhraseBank

Financial PhraseBank provides human-labelled financial sentences for benchmarking sentiment classification.

Dataset:
https://huggingface.co/datasets/yiogrdg/FinancialPhraseBank

### Data fields

The pipeline will use the fields available in the selected dataset, potentially including:

- News headline or text
- Stock ticker
- Publication timestamp
- Ingestion or availability timestamp, where recorded or simulated
- Sentiment label and confidence score
- Trading date
- Open, high, low, close, and volume
- Forward returns derived from historical prices

Actual column names, timestamp precision, data coverage, and licensing conditions will be verified during dataset inspection.

**Data limitation:** Daily price data alone cannot validate a strategy that requires 09:15 intraday execution. That experiment will require suitable timestamped intraday market data.

## 6. Asynchronous NLP Pipeline

The pipeline will use Python's `asyncio` and asynchronous queues to simulate event-driven processing.

Planned capabilities:

- Batch-based historical event replay
- Concurrent processing workers
- Headline validation and normalization
- Configurable queue capacity
- Error handling and retry logic
- Duplicate-event detection
- Structured processing logs
- Throughput and processing-latency measurements

The pipeline will be designed with modular interfaces so that a simulated producer can later be replaced with a suitable live-data source.

## 7. Financial Sentiment Analysis

### Baseline model

A lightweight sentiment baseline will establish a reference point for the classification and scoring pipeline.

### Finance-specific model

FinBERT will be evaluated for classifying financial text into positive, negative, and neutral sentiment.

Model reference:
https://huggingface.co/ProsusAI/finbert

### Sentiment features

Potential features include:

- Positive sentiment probability
- Negative sentiment probability
- Neutral sentiment probability
- Net sentiment score
- Average sentiment by ticker and time window
- News volume
- Sentiment dispersion
- Change in sentiment relative to a previous window

A candidate net sentiment score is:

\[
S = P(\text{positive}) - P(\text{negative})
\]

The score lies between -1 and 1 when the probabilities are valid and sum to one.

Classification accuracy and predictive usefulness will be evaluated separately. A model can classify text reasonably well without predicting future market returns.

## 8. Look-Ahead Bias Prevention

Look-ahead bias occurs when a model uses information that would not have been available at the time a trading or research decision was made.

This project will apply the following controls:

1. Preserve publication timestamps and distinguish them from ingestion or availability timestamps.
2. Normalize timestamps to a consistent timezone and account for exchange trading sessions.
3. Include a news event only when its information was available by the decision cutoff.
4. Align features with returns that begin strictly after the signal can be acted upon.
5. Use chronological training, validation, and test periods.
6. Fit preprocessing steps and model parameters without using future test-period data.
7. Exclude or explicitly handle duplicate headlines and repeated news events.
8. Document missing timestamps and assumptions about publication latency.

### Example: Intraday alignment

For a signal generated at 09:00:

- News available by 09:00 may be included.
- News arriving at 09:03 must not affect the 09:00 signal.
- If the assumed execution time is 09:15, the target return must begin at or after that execution time.
- The experiment requires sufficiently granular intraday prices to establish this alignment.

For daily data, the project will use a separately defined daily decision cutoff and subsequent daily return horizon.

These rules prevent future information from entering historical features by construction, subject to the quality of the available timestamps and market data.

## 9. Backtesting and Information Coefficient

The Information Coefficient (IC) measures the relationship between a signal and subsequent returns.

For a cross-sectional research window:

\[
IC_t = \operatorname{SpearmanCorr}
\left(S_{i,t},R_{i,t+h}\right)
\]

Where:

- \(S_{i,t}\) is the sentiment signal for asset \(i\) at time \(t\).
- \(R_{i,t+h}\) is the subsequent return over the selected horizon.
- \(h\) is the forecast horizon.

### Planned evaluation metrics

- Mean and median IC
- Rolling IC
- IC standard deviation
- Fraction of positive IC observations
- Confidence intervals and appropriate significance tests
- Sentiment classification metrics
- Forward-return distributions by sentiment bucket
- Baseline versus sentiment-enhanced performance

A cross-sectional IC requires multiple assets with comparable observations in a given window. If the experiment uses only one stock, we will instead evaluate a suitable time-series relationship and clearly distinguish it from cross-sectional IC.

### Backtesting safeguards

- Chronological splits
- Explicit signal and return timestamps
- No random train-test splitting for time-ordered evaluation
- Appropriate treatment of overlapping return horizons
- Baseline comparisons
- Sensitivity analysis across forecast horizons and rolling windows
- Transaction-cost considerations for any later strategy simulation
- Transparent reporting of failed or statistically weak results

A positive IC indicates a potentially useful predictive relationship, not proof of profitability or causation.

## 10. Interactive Dashboard

The planned Streamlit dashboard will include:

### News and sentiment explorer
- Filter headlines by ticker and date
- View sentiment scores and model confidence
- Track sentiment changes over time

### Market relationship analysis
- Compare sentiment with subsequent returns
- Examine forward-return distributions
- Analyze sentiment by ticker and time window

### Research performance
- Plot rolling IC
- Compare models and baseline signals
- Display evaluation periods and sample sizes
- Inspect uncertainty and stability across windows

### Pipeline monitoring
- Processed and failed event counts
- Processing throughput
- Queue and latency statistics
- Data-quality warnings

## 11. Technology Stack

| Area | Tools |
|---|---|
| Programming | Python |
| Data manipulation | Pandas, NumPy |
| Asynchronous processing | asyncio, asyncio.Queue |
| NLP | Transformers, FinBERT |
| Machine learning | scikit-learn |
| Statistical evaluation | SciPy |
| Visualization | Plotly, Matplotlib |
| Dashboard | Streamlit |
| Storage | CSV, Parquet; SQLite where useful |
| Testing | pytest |
| Version control | Git and GitHub |

## 12. Repository Structure

```text
financial-news-sentiment-agent/
├── configs/
├── data/
│   ├── raw/
│   ├── interim/
│   └── processed/
├── reports/
├── src/
│   ├── ingestion/
│   ├── sentiment/
│   ├── features/
│   ├── backtesting/
│   └── dashboard/
├── tests/
├── .gitignore
├── README.md
└── requirements.txt
```

The repository will contain source code, configuration, tests, and appropriate reports. Large datasets, credentials, and unnecessary generated files will not be committed.

## 13. Implementation Roadmap

- [ ] Phase 1: Create repository and project environment
- [ ] Phase 2: Select, download, and audit historical data
- [ ] Phase 3: Build timestamp normalization and validation
- [ ] Phase 4: Implement asynchronous ingestion and event replay
- [ ] Phase 5: Build baseline sentiment classification
- [ ] Phase 6: Integrate and evaluate FinBERT
- [ ] Phase 7: Construct leakage-safe features and forward returns
- [ ] Phase 8: Implement rolling IC and statistical evaluation
- [ ] Phase 9: Build the Streamlit research dashboard
- [ ] Phase 10: Add automated tests, reproducibility checks, and documentation

## 14. Expected Learning Outcomes

By completing the project, the developer aims to demonstrate practical experience with:

- Asynchronous Python programming
- Natural language processing
- Financial time-series data preparation
- Point-in-time feature engineering
- Statistical hypothesis testing
- Predictive signal evaluation
- Time-aware model validation
- Dashboard development
- Testing and reproducible research

## 15. Limitations and Responsible Use

Historical associations do not guarantee future predictive performance.

Results may be affected by data quality, survivorship bias, publication latency, market regime changes, multiple testing, and transaction costs.

Any simulated strategy will be labelled as a research experiment. The project does not provide personalized investment advice or guarantee returns.

## 16. Project Status

**Current stage:** Repository setup and dataset inspection.

All planned features will be marked complete only after implementation and validation. Reported metrics and dashboard screenshots will be based on actual experiment outputs rather than fabricated results.

## 17. License and Data Attribution

The code license and dataset usage conditions will be specified after reviewing the relevant source licenses and redistribution requirements. Third-party datasets and pretrained models remain subject to their own terms.

---

*Built as a portfolio project exploring financial NLP, data engineering, statistical validation, and quantitative research.*
