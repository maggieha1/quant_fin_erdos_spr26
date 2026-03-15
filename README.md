# Pricing and Delta-Hedging American Options: An Empirical Evaluation of Geometric Brownian Motion and Black-Scholes Assumptions Using Historical Market Data

## Project Objective

This project aims to build a quantitative model to price and hedge American options under the standard Black-Scholes assumption that underlying stock prices follow Geometric Brownian Motion (GBM). The main objective is to backtest this theoretical framework against historical market data to quantify the model risk in order to understand how the assumptions of constant volatility and continuous trading break down in real-world scenarios.

## Part 1: Mathematical Framework and Pricing Model

The project utilizes a numerical method, specifically the Cox-Ross-Rubinstein Binomial Tree, to price American options. This is necessary because American options lack a closed-form pricing formula due to the early exercise premium. The model assumes the underlying stock price follows Geometric Brownian Motion ($dS_t = \mu S_t dt + \sigma S_t dW_t$).

The binomial tree discretizes the GBM into $N$ time steps, with stock prices moving up by a factor $u = e^{\sigma\sqrt{\Delta t}}$ or down by $d = e^{-\sigma\sqrt{\Delta t}}$. Risk-neutral probability $p = \frac{e^{r\Delta t} - d}{u - d}$ is used for discounting. The pricing algorithm works backward from expiration, comparing the continuation value with the intrinsic value to determine the option price at each node, allowing for early exercise.

## Part 2: Volatility Modeling

Volatility, $\sigma$, is a critical input to the Black-Scholes framework. This section explores two types of volatility:

### 1. Historical Volatility (Realized Volatility)

Calculated as the annualized standard deviation of daily log returns from historical data, it measures past price fluctuations. The formula used is $\sigma_{historical} = s \sqrt{252}$, where $s$ is the daily standard deviation of log returns.

### 2. Implied Volatility (IV)

Implied volatility is the market's forward-looking expectation of volatility, derived from current option prices. It is the value of $\sigma$ that equates the theoretical Black-Scholes price to the observed market price. Since there's no algebraic solution, a numerical root-finding algorithm (Newton's method) is used:

$\sigma_{n+1} = \sigma_n - \frac{C_{BS}(\sigma_n) - C_{market}}{\mathcal{V}(\sigma_n)}$

### Visualizations:

*   **Historical Volatility Plot:** Displays the 21-day rolling historical volatility for a given ticker (e.g., AAPL).
*   **Implied Volatility Plot:** Illustrates the Newton method's convergence to implied volatility by showing the American option price as a function of volatility, the market price, and the calculated implied volatility.

## Part 3: The Hedging Strategy

This section defines a delta-neutral hedging strategy to offset the option's directional risk using the underlying stock.

*   **Delta Calculation:** The sensitivity of the American option's price to changes in the underlying stock price ($\Delta=\frac{\partial V}{\partial S}$) is calculated numerically using a finite difference method on the binomial tree.
*   **Hedging:** The process simulates rebalancing the hedge at discrete intervals. As stock prices and time change, the delta adjusts, requiring buying or selling shares to maintain a net-zero delta.

## Part 4: Empirical Evaluation and Backtesting

The project simulates the perspective of a market maker who has sold an American Call option and is maintaining a delta-neutral portfolio. While perfect Black-Scholes assumptions with continuous rebalancing would yield zero hedging error, discrete rebalancing and real-world factors introduce tracking error.

The portfolio value is calculated daily as $V_{portfolio} = (\Delta \times S_t) - V_{market} + Cash$. The simulation uses actual market closing prices for the stock and *simulated* market option prices, with a critical injection of a volatility jump (from 25% to 40% halfway through the period) to model real-world volatility surface shifts.

### Findings:

The backtest revealed a **final hedging error of approximately -0.21** (21 cents). This quantifies the bleed due to real-world frictions such as discrete rebalancing and sudden volatility shifts, especially when the hedging model assumes a constant volatility while the market experiences changes. The plot of the cumulative hedging error clearly illustrates this divergence, particularly after the artificial volatility jump.

## Part 5: Conclusion

While Geometric Brownian Motion and the Cox-Ross-Rubinstein Binomial Tree are mathematically sound foundations, the empirical backtest highlights their limitations in real-world American option pricing and hedging. The assumption of constant volatility and log-normal returns breaks down, leading to quantifiable hedging errors. This tracking error stems from:

*   **Discrete Rebalancing:** Rebalancing only at discrete intervals (e.g., daily) instead of continuously.
*   **Jump Risks:** Sudden, discontinuous price movements (e.g., overnight gaps) that violate GBM assumptions.
*   **Volatility Surface Shifts:** Changes in implied volatility that are not accounted for by a constant volatility assumption.

In conclusion, this project demonstrates that while GBM is a vital stepping stone, successful risk management in live markets demands empirical calibration and models that respect the complex, dynamic nature of real-world volatility. Future work could involve dynamic local volatility models, such as volatility smile functions, or the integration of stochastic volatility models like Heston with local volatility.

## Setup and Usage

To run this project, you will need Python 3 and the following libraries:

```bash
pip install numpy pandas yfinance matplotlib scipy
```

The project is structured into several parts, as outlined above. You can execute the code cells sequentially in a Jupyter Notebook or Google Colab environment to reproduce the analysis and visualizations.
