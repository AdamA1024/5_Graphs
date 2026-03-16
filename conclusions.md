# Conclusions

## Overview

This project built two graph-based frameworks applied to U.S. equity markets. The first is a Markov chain graph on SPX daily returns, and the second is a regime-conditioned Minimum Spanning Tree of sector correlations. Together they show both what graphs in finance are good at and where they fall short.

---

## Stage 1 — The Markov Chain Graph

### What it showed

The SPX return sequence was discretised into K=7 states and a directed weighted graph was estimated from transition frequencies. The stationary distribution confirmed the market spends around 10% of time in tail return states, and mean first passage times gave a concrete estimate of how long recovery from a Large Down state typically takes, around 8.7 days to reach Large Up.

### What it did not show

The transition matrix came out nearly uniform, so from any state all transitions are roughly equiprobable at about 1/7. This means the graph has almost no predictive edge. The MDP trading strategy built on this achieved a Sharpe of 0.35 versus 2.03 for buy-and-hold in 2019, which is a large underperformance.

### Why this happened and why it is still useful

This is not a failure of the implementation. Daily SPX returns are close to a random walk at this frequency and a first-order Markov chain can only exploit short-memory autocorrelation, which there is very little of. The graph is more useful as a descriptive tool. The stationary distribution and mean first passage times correspond to real economic quantities — how much time the market spends in different regimes and how quickly it recovers from downturns — and those are worth knowing even without a trading signal attached.

The key lesson here is that graphs in finance do not need to generate alpha to be useful. Description and structural measurement are independently valuable.

---

## Stage 2 — The Regime-Conditioned MST

### Main findings

The MST of sector correlations changes substantially across regimes.

Bear days push mean pairwise sector correlations up to 0.49, and the MST has thick edges throughout, meaning all retained connections are strong. Some Neutral-day pairs are actually negatively correlated (down to -0.28), which disappears entirely in Bear, confirming that crisis periods erase the natural idiosyncratic variation between sectors. On Neutral days the mean correlation drops to just 0.08 and the MST is a thin distributed tree with no clear hub. Bull sits at 0.36, with sectors moving together due to broad risk-on momentum but not as severely as during selloffs.

The practical implication is that diversification is not a fixed property of a portfolio. It depends on what regime the market is in. A portfolio that looks well diversified using unconditional correlations will behave very differently in Bear versus Neutral conditions.

### The Industrials finding

Industrials (XLI) is the most connected sector in the MST across all three regimes, but its degree centrality is actually highest in Bull (0.500) compared to Bear and Neutral (both 0.375). So Bear does not produce a more hub-dominated tree structurally, it just has stronger edge weights. Bull has Industrials connecting to more sectors topologically, probably because broad cyclical momentum routes connections through a common growth-sensitive sector. This distinction between how strongly sectors are linked and how many links each sector has is something you only see once you frame it as a graph problem.

### What the graph adds over a correlation matrix

A 9x9 correlation matrix has 36 distinct values and is hard to read structurally. The MST keeps 8 edges and is immediately interpretable. The Mantegna distance is needed to make the MST construction valid, as Pearson correlation does not satisfy the triangle inequality so it cannot be used as a distance directly. Converting to a proper metric space is what allows the MST to be geometrically meaningful and comparable across regimes.

### Limitations

Regime labels are coarse. Pooling all Bear days together assumes they are internally similar, but a Bear day in January 2016 and one in December 2018 could have very different cross-sector dynamics. A hidden Markov model or rolling window approach would give finer conditioning.

Correlation is also not causation. High Bear-regime correlation tells us sectors move together but not which is driving which. A directed graph built from Granger causality tests would be needed to answer that.

The MST also drops 28 of 36 possible edges, so some structurally relevant links are discarded. A Planar Maximally Filtered Graph retains more structure while staying readable, which would be a natural next step.

---

## Broader Reflections on Graphs in Finance

Graphs are good at visualising relationships that are too dense to read in matrix form. The MST is the clearest example of this. They are also good at measuring systemic structure through metrics like centrality and path length that have no natural expression in standard statistics.

Where graphs struggle is in predicting returns. The Markov chain confirmed this — there is not enough serial structure in daily SPX returns to exploit. Graphs also become harder to use reliably at scale, as a full 500-stock S&P 500 pairwise network has over 124,000 edges and requires large samples and careful regularisation to estimate well.

The connection between the two stages reflects a broader principle. The Markov chain failed to predict SPX returns, but the regime labels it produced turned out to be useful inputs for conditioning a different graph. The value shifted from prediction to structural conditioning. Graphs in finance seem most useful not as forecasting tools but as lenses for understanding how the shape of financial relationships changes over time and what that means for portfolio construction and risk.
