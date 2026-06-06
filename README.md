# Interest Rate Dynamics: CIR-Based Yield Curve Forecasting

## What This Project Does

Fixed income markets are governed by a deceptively simple question: given where short-term rates are today, what should the entire yield curve look like? This repository answers that question using the **Cox-Ingersoll-Ross (CIR)** stochastic short-rate model — rigorously calibrated, stress-tested against a real macroeconomic regime shift, and pushed to its limits through two advanced extensions.

The defining challenge: predict yields across all maturities (6 Months through 30 Years) for any date in the test window, armed with **nothing but the 3-Month rate** as input.

## What Makes This Challenging

The model is trained on data from one market regime and tested on another:

- **2016–2024 (Training)**: A prolonged low-rate era with a conventional upward-sloping curve.
- **2024–2026 (Testing)**: An elevated-rate environment with an inverted yield curve — structurally opposite to what the model was trained on.

Successfully navigating this transition is not guaranteed. It requires careful calibration under the risk-neutral measure $\mathbb{Q}$ and deliberate safeguards against overfitting.

## Key Capabilities

**Data Pipeline**
A production-grade preprocessing layer handles the messiness of real market data: trailing whitespace in headers, gaps from market holidays (filled via business-day reindexing), and transient price spikes removed by a custom Spike-Reversal Filter — all without introducing any forward-looking bias.

**Calibration Methods**
Three approaches are implemented and compared side-by-side:
- Discretized **Ordinary Least Squares (OLS)** — fast and interpretable
- Exact **Maximum Likelihood Estimation (MLE)** — statistically rigorous
- **Risk-Neutral ($\mathbb{Q}$) Calibration** — financially consistent for pricing

**Model Extensions**
- **Shifted CIR++**: Adds a deterministic shift function to the base model, with validation-based chronological shrinkage to prevent overfitting when the yield curve changes shape abruptly.
- **Two-Factor Additive CIR**: Splits the short rate into two independent positive components, capturing both the level and slope of the term structure simultaneously.

**Results**
The Risk-Neutral MLE calibration achieves an out-of-sample $R^2$ of **~0.947** across held-out tenors — comfortably clearing the 0.85 benchmark, even through a full yield curve inversion.

## Project Files

| File | Description |
|------|-------------|
| `Stochastic_Interest_Rate_Modelling.ipynb` | Complete notebook: preprocessing, derivations, calibration, predictions, and visualizations |
| `train_data.csv` | Daily yields across 9 tenors from 2016–2024, used for calibration |
| `test_data_3M.csv` | 3-Month yields from 2024–2026, the sole model input at inference time |
| `test_data.csv` | Full held-out actuals used exclusively for $R^2$ scoring |

## Running the Notebook

**Dependencies**: `pandas`, `numpy`, `scipy`, `scikit-learn`, `matplotlib`, `seaborn`

Open `Stochastic_Interest_Rate_Modelling.ipynb` and run all cells from top to bottom. The notebook is fully self-contained — it loads the data, cleans it, calibrates every model variant, and produces all evaluation metrics and dashboards automatically. No configuration required.
