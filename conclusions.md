# Conclusions

---

## Why HMM and Viterbi

Standard Markov chain approaches to financial regime detection discretise returns into quantile bins — the boundaries are chosen by the analyst rather than the data. A Gaussian Hidden Markov Model is better suited because it treats the underlying market regime as a latent variable and learns a distinct return distribution (mean and variance) for each state from the data itself. The result is that Bear, Neutral, and Bull states have genuine economic content rather than being labelled post-hoc.

The HMM is still fundamentally a Markov chain. The hidden states follow Markov dynamics — tomorrow's regime depends only on today's — which is what gives us the transition matrix and the graph structure. The only difference from a standard chain is that we cannot observe the state directly from prices.

The Viterbi algorithm is what makes this tractable. Finding the most likely regime sequence over N trading days would in principle require evaluating all 3^N possible state paths — computationally impossible for thousands of observations. Viterbi solves this by dynamic programming, tracking only the most likely path to each state at each step rather than the full exponential tree. The output is the single most likely sequence of hidden regimes given the observed returns.

Running 20 random initialisations and selecting the model with the highest log-likelihood guards against the known tendency of expectation-maximisation to converge to local optima.

---

## Regime Quality

The model was trained on 2,263 days of SPX returns from 2010 to end of 2018. The three resulting states are clearly distinct:

| Regime  | Days | Mean Return | Daily Vol | Avg Duration |
|---------|------|-------------|-----------|--------------|
| Bear    | 130  | -0.15%      | 2.18%     | 43 days      |
| Neutral | 544  | -0.10%      | 1.26%     | 29 days      |
| Bull    | 1589 | +0.10%      | 0.58%     | 88 days      |

The most important finding here is that the HMM has learned a volatility-driven regime structure rather than a return-driven one. Bear and Neutral have nearly identical mean returns (-0.15% and -0.10%) but dramatically different daily volatilities (2.18% versus 1.26%). Bull is separated from both by its low volatility (0.58%) and positive drift. The model is not classifying days by whether the market went up or down — it is classifying them by how turbulent conditions were. Volatility is more persistent and regime-defining than daily return direction, so this is a more robust and economically meaningful characterisation of market state.

Average regime durations are long: 43 days for Bear, 29 for Neutral, 88 for Bull. Regimes that lasted only one or two days would be unactionable, but at these durations there is ample time to observe a regime change and respond.

---

## Transition Structure

The empirical transition matrix estimated from the decoded sequence shows:

|         | Bear  | Neutral | Bull  |
|---------|-------|---------|-------|
| Bear    | 0.977 | 0.023   | 0.000 |
| Neutral | 0.002 | 0.967   | 0.031 |
| Bull    | 0.001 | 0.010   | 0.989 |

Two things stand out. First, persistence is very high across all three regimes (97–99% chance of staying in the same regime day to day). This is what makes the signal tradeable — a regime that flips randomly has no predictive value, but regimes that last weeks to months give enough time to position around them.

Second, the Bear→Bull transition probability is 0.000. The market never jumps directly from a crisis regime to a full bull regime in this dataset. Recoveries always pass through Neutral first, giving the Markov chain a strict directional structure: Bear→Neutral→Bull and the reverse. This is visible in the directed graph as the absence of a Bear→Bull edge, and it is a real structural property of how equity markets behave rather than an artefact of the model. Historically, markets coming out of high-volatility stress periods go through a settling phase before returning to sustained low-volatility uptrends.

---

## Regime Timeline

The regime timeline shows Bear periods clustering around known stress events in the training window rather than scattering randomly through time. The 2011 Euro sovereign debt crisis, the 2015-16 China volatility spike, and the Q4 2018 selloff should all be visible as Bear clusters. This validates that the HMM has picked up genuine market structure. The Bull regime dominates the calm stretches (2012-14 post-crisis recovery, the ultra-low-volatility 2017) and accounts for 1,589 of the 2,263 training days, reflecting the structural upward drift of equities over this period.

---

## Per-Stock Results — 2019 Out-of-Sample

In 2019, the decoded signal contained no Bear days at all (211 Bull, 40 Neutral). The strategy was long during Bull days and flat during the 40 Neutral days. Results:

| Ticker | Strategy Sharpe | B&H Sharpe | Outperforms |
|--------|----------------|------------|-------------|
| AAPL   | 3.48           | 2.42       | Yes         |
| MSFT   | 3.06           | 2.32       | Yes         |
| JPM    | 3.02           | 1.99       | Yes         |
| XOM    | 0.92           | 0.26       | Yes         |
| JNJ    | 1.52           | 0.97       | Yes         |
| PG     | 2.35           | 2.08       | Yes         |
| BA     | 0.03           | 0.10       | No          |
| GS     | 2.32           | 1.33       | Yes         |
| AMZN   | 1.71           | 0.80       | Yes         |
| CVX    | 1.04           | 0.68       | Yes         |

9 out of 10 stocks show higher strategy Sharpe than buy-and-hold. The mechanism is worth understanding: since there were no Bear days in 2019, the only effect of the strategy is skipping 40 Neutral days. Those Neutral days have higher volatility (1.26%) than Bull days (0.58%), so avoiding them reduces portfolio volatility by more than it reduces return, lifting Sharpe. This is not a return enhancement — it is a volatility reduction from knowing when not to be invested.

Boeing (BA) is the exception. It likely posted strong returns on those 40 Neutral days, so skipping them hurt both return and Sharpe. This illustrates the key limitation: the SPX regime signal works best for stocks closely tied to broad market conditions. A stock driven by firm-specific events (in Boeing's case, the 737 MAX grounding dominated 2019 returns) will not align well with an index-level regime classifier.

The largest absolute Sharpe improvements came from AAPL (+1.06), JPM (+1.03), GS (+0.99), and AMZN (+0.91). The energy names (XOM, CVX) also outperform but by a smaller margin. This broadly aligns with the signal being most effective for stocks closely tied to broad market risk sentiment, though the individual stock variation suggests firm-specific factors still matter.

---

## Limitations

The model is trained on index-level returns and tested on individual stocks. Any stock with low beta to the SPX, or with returns dominated by idiosyncratic factors, will be poorly served by the signal. The regime classifier knows about the market environment, not about firm-specific events.

The training window (2010-2018) includes one full cycle but is anchored to a predominantly low-rate, post-crisis bull market environment. The regime characteristics learned here — particularly the volatility thresholds that separate Bear from Neutral — may not generalise to structurally different future periods.

The first-order Markov assumption means transition probabilities are constant regardless of how long we have been in a regime. In reality, the longer a Bear regime persists, the more likely a recovery might become (or the opposite — protracted stress can deepen). A semi-Markov or duration-dependent model would capture this, at the cost of a larger parameter space.

Finally, the convergence warnings from the EM fitting indicate the log-likelihood improvement is near zero but not exactly zero. The chosen model is practically well-fitted (the delta was less than 0.0001 for the best seed) but users should be aware the HMM is not a closed-form estimator.
