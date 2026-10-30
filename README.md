# BlackJack-Simulation-Project

**Python | NumPy | pandas | matplotlib | pytest**

A Python-based Blackjack game and Monte Carlo simulation framework for investigating automated playing strategies, estimating expected returns, analysing produced statistics and analysing decision-making under uncertainty.

Originally developed as my final project for Harvard's **CS50P: Introduction to Programming with Python**, this project began as an interactive command-line Blackjack game. I subsequently extended it independently into a simulation and statistical analysis framework capable of evaluating strategies across millions of simulated hands.

## Features

- Interactive command-line Blackjack supporting multiple players against a computer-controlled dealer.
- Object-oriented hand representation with dynamic Ace valuation.
- Automated hit/stand strategies and dealer behaviour.
- Monte Carlo simulation of individual games and large batches of games.
- Calculation of win, loss, push and bust rates.
- Estimation of expected returns for different strategies.
- Repeated experiments to quantify sampling variability.
- CSV export of simulation results.
- Statistical analysis using NumPy and pandas.
- Visualisation of strategy performance using matplotlib.
- Calculation of standard deviations, standard errors and 95% confidence intervals.
- Automated unit testing using pytest.

## Project Structure

```text
Blackjack/
├── project.py
├── test_project.py
├── experiment.py
├── analysis.py
├── requirements.txt
├── README.md
└── results/
    ├── threshold_results.csv
    ├── threshold_summary.csv
    └── threshold_expected_return.png
```

### `project.py`

Contains the core Blackjack game and simulation engine, including deck generation, shuffling, hand valuation, interactive gameplay, automated strategies and repeated game simulation.

### `test_project.py`

Contains automated tests for game mechanics, strategy behaviour and simulation functions.

### `experiment.py`

Runs Monte Carlo experiments and exports the results of individual simulation runs to CSV.

### `analysis.py`

Loads the saved experimental data, calculates statistical summaries and generates visualisations.

### `results/`

Stores the experimental dataset, summary statistics and generated graph.

---

## Blackjack Game

The original game allows a configurable number of human players to play Blackjack against a computer-controlled dealer.

The game uses a standard 52-card deck and supports:

- Player decisions to hit or stand.
- Automatic dealer decisions.
- Hand-value calculation, including Aces valued as 1 or 11.
- Detection of busts.
- Determination of wins, losses and pushes.

The `Hand` class manages each hand's cards and calculates its value, adjusting Ace values to avoid busting where possible.

### Automated Strategies

The simulation engine separates the game mechanics from the strategy used to make decisions.

A threshold strategy follows a simple rule:

**Hit if the player's hand value is below a specified threshold; otherwise, stand.**

For example, a threshold of 16 means that the player hits below 16 and stands at 16 or above.

The dealer follows a fixed strategy of hitting below 17 and standing on 17 or above.

This separation makes it possible to compare different strategies without rewriting the underlying game logic.

---

## Experiment 1 — Hit Threshold Strategies

### Research Question

**How does the player's chosen hit/stand threshold affect expected return in simulated Blackjack?**

The experiment compares nine threshold strategies, ranging from 12 to 20.

### Methodology

Each threshold was evaluated using five independent simulation runs of **1,000,000 games each**.

| Parameter | Value |
|---|---|
| Thresholds tested | 12–20 |
| Number of strategies | 9 |
| Runs per strategy | 5 |
| Games per run | 1,000,000 |
| Total simulated games | **45,000,000** |

Each simulated game produces an outcome of win, loss or push, together with player and dealer hand values and bust indicators.

For each run, the program records:

- Total games played.
- Wins, losses and pushes.
- Player and dealer bust counts.
- Win, loss and push rates.
- Estimated expected return.

The run-level results are saved to [`results/threshold_results.csv`](results/threshold_results.csv).

### Expected Return

The experiment uses a simplified payoff model:

| Outcome | Return |
|---|---:|
| Win | +1 |
| Loss | −1 |
| Push | 0 |

Expected return is estimated using:

\[
EV=\frac{\text{Wins}-\text{Losses}}{\text{Total Games}}
\]

This allows strategies to be compared using a common performance measure.

### Statistical Analysis

Because simulation results contain random variation, each threshold is evaluated across multiple runs.

The saved data is analysed using pandas and NumPy to calculate:

- Mean expected return.
- Sample standard deviation.
- Standard error of the mean.
- 95% confidence intervals.

Confidence intervals are calculated using the Student's t-distribution to account for the small number of independent runs per threshold.

The derived statistics are saved to [`results/threshold_summary.csv`](results/threshold_summary.csv).

### Results

![Expected Return by Blackjack Hit Threshold](results/threshold_expected_return.png)

The experiment found that intermediate hit thresholds generally performed better than strategies that continued hitting until very high hand values.

Thresholds **15 and 16 produced nearly identical estimated expected returns**, while performance deteriorated substantially for thresholds above 17.

Selected results from the experiment are shown below:

| Hit threshold | Mean expected return |
|---|---:|
| 15 | -0.0751 |
| 16 | -0.0751 |
| 17 | -0.0856 |
| 18 | -0.1208 |
| 19 | -0.2038 |
| 20 | -0.3542 |

The close estimates for thresholds 15 and 16 should not be interpreted as evidence that one is definitively superior. The analysis therefore includes confidence intervals to communicate sampling uncertainty.

**Conclusion:** Under the simplified rules implemented in this simulator, threshold strategies around 15–16 performed similarly and substantially better than more conservative strategies.

---

## Monte Carlo Convergence

During development, I also investigated how the number of simulated games affects the stability of estimated expected returns.

Experiments were conducted using sample sizes ranging from 100 to 1,000,000 games.

Smaller simulations produced substantial variation between repeated runs, occasionally suggesting positive expected returns for strategies that were unprofitable over larger samples.

As the number of simulated games increased, the estimated returns became more stable.

This demonstrated the importance of sample size when using Monte Carlo methods to estimate probabilistic outcomes.

---

## Installation and Usage

### Requirements

- Python 3
- NumPy
- pandas
- matplotlib
- pytest

Install the required dependencies:

```bash
pip install -r requirements.txt
```

### Run the Interactive Blackjack Game

```bash
python project.py
```

To specify the number of players:

```bash
python project.py --players 3
```

### Generate Simulation Data

```bash
python experiment.py
```

This runs the threshold-strategy experiment and writes the results to:

```text
results/threshold_results.csv
```

**Note:** Running the experiment overwrites the existing CSV dataset. The number of games and repetitions can be adjusted in `experiment.py` to reduce execution time during development.

### Analyse Experimental Results

```bash
python analysis.py
```

This reads the saved simulation data and generates:

```text
results/threshold_summary.csv
results/threshold_expected_return.png
```

The analysis can therefore be repeated without rerunning the simulations.

### Run Automated Tests

```bash
pytest
```

The tests cover the core Blackjack mechanics, automated strategies and simulation functions.

---

## Limitations

The simulator currently implements a simplified version of Blackjack.

It does not yet include:

- Splitting.
- Doubling down.
- Insurance.
- Natural Blackjack payouts.
- Multiple-deck shoes.
- Card counting.

The threshold strategies also make decisions using only the player's hand value, without considering the dealer's visible card.

Consequently, the estimated returns represent performance under the simulator's implemented rules rather than the expected returns of equivalent strategies in a real casino.

The current experiment also compares a limited family of strategies, so its results should not be interpreted as identifying an optimal Blackjack strategy in general.

---

## Future Development

The next planned experiment will investigate **dealer-aware strategies**.

Rather than relying solely on the player's hand value, these strategies will incorporate the dealer's visible card when deciding whether to hit or stand.

This will allow the simulator to investigate the value of additional information in decision-making under uncertainty.

Further possible extensions include:

- Comparing dealer-aware strategies with fixed thresholds.
- Implementing established Blackjack basic strategy.
- Adding more realistic game rules and payout structures.
- Investigating how different rules affect expected return.
- Extending the statistical analysis and visualisations.

---

## Project Development

This project has evolved through several stages:

1. **Interactive Blackjack:** Developed the original multiplayer command-line game for CS50P.
2. **Strategy abstraction:** Separated decision-making logic from game mechanics.
3. **Automated simulation:** Implemented functions for simulating individual hands and complete games.
4. **Monte Carlo experimentation:** Evaluated automated strategies over repeated simulations.
5. **Data persistence:** Exported experimental observations to CSV.
6. **Statistical analysis:** Used pandas and NumPy to calculate expected returns and quantify uncertainty.
7. **Visualisation:** Produced comparative graphs with 95% confidence intervals.

The project has provided practical experience in object-oriented programming, software testing, Monte Carlo simulation, probability, statistical analysis and computational experimentation.
