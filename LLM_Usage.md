# LLM Usage Declaration

---

## Usage 1 — Regime Definition Approach

**Prompt given to LLM:**

> "What are good ways to define market regimes for a Markov chain model of stock returns, and what metrics are most worth looking at once you have the states?"

**LLM Response (summarised):**

The model suggested using a Hidden Markov Model to identify regimes from the return data rather than imposing arbitrary quantile thresholds. It noted that HMM states emerge from the return distribution itself so the resulting regimes have cleaner economic interpretation. It also suggested looking at regime persistence (self-loop probabilities), average regime duration, and mean return and volatility per state as the key descriptive metrics.

**Our evaluation:**

The HMM suggestion is well-grounded and it is the standard approach in quantitative finance for regime detection. The shift from quantile bins to HMM is a meaningful improvement because the states are no longer arbitrary slices of the return distribution but instead reflect genuinely distinct market behaviours that the data supports. We extended the analysis to test whether the SPX regime signal carries useful information for trading individual stocks across different sectors, which was not part of the LLM suggestion.

---

## Usage 2 — Per-Stock Testing Framework

**Prompt given to LLM:**

> "How would you test whether SPX regime labels are useful for trading individual stocks?"

**LLM Response (summarised):**

The model suggested using the out-of-sample decoded regime sequence as a trading signal, applying a long/flat/short rule per regime, and comparing annualised Sharpe ratios against buy-and-hold across a basket of stocks as the primary evaluation metric.

**Our evaluation:**

Straightforward and standard. We implemented this on a 10-stock basket in 2019 and the results are honest in both directions. The regime signal works differently across sectors, which is itself a useful finding about the generalisability of index-level regime models to individual names.
