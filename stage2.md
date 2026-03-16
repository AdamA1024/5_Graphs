# Stage 2 — From Markov Chains to Regime-Conditioned Sector Networks

## What Stage 1 Showed

The Markov chain on SPX daily returns produced a directed weighted graph with K=7 states (return regimes). The key findings were:

- **The transition matrix is nearly uniform.** From any state, the probability of moving to any other state is approximately 1/7 ≈ 14%. There is very little predictive structure in daily SPX returns at this frequency — consistent with the efficient market hypothesis.
- **The MDP trading strategy underperforms buy-and-hold substantially** (Sharpe 0.35 vs 2.03 in the 2019 test year). The optimiser finds a long/short/flat policy, but because the signal is so weak, it trades frequently without edge.
- **The graph does contain useful structural information** beyond trading signals: the stationary distribution confirms the market spends ~10% of time in tail states; mean first passage times tell us how long it takes on average to recover from a Large Down state (~8.7 days to reach Large Up). These are descriptively interesting even if not profitable.

The honest conclusion from Stage 1: **the Markov chain graph is a better description tool than a prediction tool for a single asset.**

---

## The Conceptual Pivot

If the graph's value is in description rather than prediction, what is it describing well?

The key insight is that the Markov chain gives us **regime labels** — each day is classified as Bear, Neutral, or Bull based on where it falls in the state space. We can use these labels as a lens through which to examine a *different* and richer graph: the **cross-asset correlation network**.

This pivots the question from:

> *"Given today's SPX return state, what will tomorrow's SPX return be?"*

to:

> *"Given today's market regime, how does the structure of connections between financial assets change?"*

This second question is more important for practitioners — it is the question of **systemic risk and diversification failure**.

---

## Stage 2 Framework: Regime-Conditioned Sector MST

### The Graph

- **Nodes:** 9 S&P 500 sector ETFs (XLB, XLE, XLF, XLI, XLK, XLP, XLU, XLV, XLY)
- **Edges:** Mantegna (1999) distance between each pair of sectors, defined as $d_{ij} = \sqrt{\frac{1}{2}(1 - \rho_{ij})}$, where $\rho_{ij}$ is the Pearson correlation of daily log-returns within a given regime
- **Structure:** For each regime (Bear / Neutral / Bull), we build a **Minimum Spanning Tree** — the connected spanning subgraph with minimum total distance, i.e. maximum total correlation

### Why MST?

The full correlation matrix has $\binom{9}{2} = 36$ edges, making it hard to read. The MST retains only the 8 most important structural connections, giving a clean, interpretable graph that preserves the essential topology of the network (Mantegna 1999; Onnela et al. 2003).

### The Optimisation Link

The MST is not just a visualisation tool — it enables a portfolio construction rule:

1. In each regime, compute the **degree centrality** of each sector node in the MST (how many other sectors it is directly connected to).
2. Highly central nodes are **systemically important** — they co-move with many others, so holding them provides little diversification benefit.
3. A **regime-aware portfolio** underweights or avoids hub sectors during Bear regimes, where centrality is highest and diversification fails most severely.

This is an optimisation problem on the graph: minimise portfolio variance subject to a constraint that portfolio weights are inversely proportional to node centrality.

### The Expected Finding

Based on prior literature, we expect:
- **Bear regime:** high average correlation, star-like MST (one or two hub sectors), low effective diversification
- **Neutral regime:** moderate correlation, balanced tree structure
- **Bull regime:** lower correlation, distributed tree, more diversification benefit

The practical implication is stark: **diversification fails exactly when investors need it most** — during market crashes. The graph makes this visible and quantifiable.

---

## Connection to the Assignment Brief

The assignment asks for a study that "admits a graph representation and benefits from optimisation on graphs." This framework satisfies both:

- **Graph representation:** The MST is a natural sparse graph on financial assets, grounded in information-theoretic distance (Mantegna 1999)
- **Optimisation on graphs:** Hub centrality in the MST drives portfolio weight constraints; minimising portfolio risk subject to these constraints is a quadratic programme whose feasible set is defined by graph structure
- **Link to Markov chains (Stage 1):** The regime labels that condition the MST come directly from the Markov chain — the two stages are connected, not independent analyses
