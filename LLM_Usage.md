# LLM Usage Declaration

This document discloses where large language model assistance was used in this assignment, in accordance with the course guidelines requiring inclusion of prompts, responses, and critical evaluation.

---

## Usage 1 — Framework Scoping

**Prompt given to LLM:**

> "building a Markov chain model of S&P 500 daily returns for a finance assignment. The chain has 5–7 states based on return quantiles. The transition matrix is treated as a weighted directed graph. What graph-theoretic visualisations or extensions would make this framework more interesting and analytically richer?"

**LLM Response (summarised):**

The model suggested extending the single-asset Markov chain into a multi-asset setting by using the regime labels produced by the chain to condition a sector correlation network. Specifically, it proposed computing pairwise correlations across S&P 500 sector ETFs separately for each Markov regime (Bear / Neutral / Bull), then constructing a Minimum Spanning Tree (MST) for each regime using Mantegna's (1999) distance metric. It noted that MST topology is known to collapse toward a star structure during market stress, which provides a graph-theoretic measure of systemic contagion.

**Our evaluation:**

The suggestion is grounded in published literature (Mantegna 1999; Onnela et al. 2003) and is computationally tractable with standard libraries. The key critical observation is that the LLM framed this as a visualisation exercise. We extended it by analysing degree centrality shifts across regimes and drawing portfolio-construction implications from the graph structure — specifically, which sectors decouple in Bear regimes and therefore offer genuine diversification. The interpretation of Industrials as a persistent structural hub, and the counterintuitive finding that Neutral days are the most diversified regime, were conclusions drawn from our own analysis of the outputs rather than from the LLM suggestion.

---

## Usage 2 — Sense-checking the Markov Chain Results

**Prompt given to LLM:**

> "My Markov chain on SPX daily returns with K=7 states produces a nearly uniform transition matrix. The MDP strategy has Sharpe 0.35 vs buy-and-hold Sharpe 2.03 in 2019. Is this expected?"

**LLM Response (summarised):**

The model confirmed this is a well-known result: daily SPX returns are close to a random walk, so any short-memory Markov model will find weak predictive signal. It noted that K=7 with ~1,000 training observations gives only ~140 observations per state, leading to noisy transition estimates. It suggested this is not a failure of the framework but rather evidence that the graph structure adds value through *description* (stationary distribution, mean first passage times) rather than *prediction*.

**Our evaluation:**

This is a fair and honest assessment. We used it to motivate the pivot to the MST framework, where the graph's value lies in structural description of cross-asset relationships rather than return forecasting. The Markov chain results are retained as Stage 1 and discussed critically in conclusions.
