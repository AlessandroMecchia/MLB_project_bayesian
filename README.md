# MLB Bayesian Project

A Bayesian data analysis project focused on Major League Baseball (MLB), with an emphasis on probabilistic modeling of team and player performance under uncertainty.

The repository is centered on a final notebook (`code_final.ipynb`) and an exported HTML report (`code_final.html`). The work combines baseball data collection, exploratory analysis, Bayesian hypothesis testing, regression modeling, and model comparison using WAIC and leave-one-out cross-validation. It uses `PyMC`, `ArviZ`, `pybaseball`, `pandas`, and `scikit-learn` as its main analytical stack.  
Source basis: repository structure, README stub, notebook section titles, and dependency files. 

---

## Repository Structure

```text
MLB_project_bayesian/
├── images/
├── code_final.ipynb
├── code_final.html
├── Pipfile
├── Pipfile.lock
└── README.md
```

The repository currently contains one main notebook and one exported HTML version of the same work, plus environment files and an `images` directory. GitHub reports the repository as a mix of Jupyter Notebook and HTML, which is consistent with a notebook-based final project delivered both as source and rendered report.

---

## Project Scope

The project studies baseball performance through Bayesian modeling rather than only point estimates or deterministic summaries.

From the notebook structure, the work is split into two main studies:

1. **Study I – Moneyball: Team-Level Analysis**  
   A team-level investigation of whether modern offensive metrics are better predictors of run production than traditional batting statistics.

2. **Study II – Are Modern Hitter Categories Meaningful?**  
   A player-level study that evaluates whether common hitter archetypes based on offensive indicators correspond to meaningful differences in production.

---

## Main Objectives

The notebook is designed around a small set of clear analytical goals:

- collect and organize baseball data from public sources;
- inspect team and player metrics through exploratory analysis;
- formalize baseball questions as Bayesian statistical models;
- estimate posterior distributions rather than relying only on single-value estimates;
- compare alternative model specifications using predictive criteria.

The notebook introduction explicitly links the analysis to sabermetrics and focuses on metrics such as **OBP**, **SLG**, and **OPS** as explanatory variables for team success.

---

## Data Sources

The project relies primarily on data accessed through `pybaseball`, which provides interfaces to public baseball data sources such as Baseball Reference, Baseball Savant, and FanGraphs. The notebook also states that some team-level data for the period **2020 to 2024** were collected manually from Baseball-Reference to complement and validate the library-based datasets.

The data collection section shows two main data streams:

- **Statcast event-level data** for detailed pitch or event analysis;
- **player and team statistics** for batting and pitching summaries.

The notebook explicitly references:

- `statcast(...)` data retrieval through `pybaseball`, with a local cached Parquet dataset (`data/statcast_2025.parquet`);
- batter and pitcher statistics loaded from local CSV files (`data/batter_stats_2024.csv`, `data/pitcher_stats_2024.csv`). 

---

## Study I – Moneyball: Team-Level Analysis

The first part of the project is framed around the Moneyball idea that traditional baseball statistics do not always capture the mechanisms that most strongly drive team performance.

### Research question

The notebook formulates a direct question:

> Is on-base percentage (OBP) a better predictor of team success, measured by runs per game, than batting average (BA) in Major League Baseball?

This question is motivated by the historical contrast between:

- **Batting Average (BA)** as a traditional measure of hitting success;
- **On-Base Percentage (OBP)** as a broader indicator of offensive value because it includes walks and other ways of reaching base. 

### Bayesian hypothesis test

To address this, the notebook defines a Bayesian coefficient comparison based on the regression effects of OBP and BA. The core hypothesis is written as:

```text
H: beta_OBP > beta_BA
```

and is evaluated through the posterior probability that the OBP coefficient is larger than the BA coefficient. The model focuses on the posterior distribution of the difference:

```text
delta = beta_OBP - beta_BA
```

This makes the analysis interpretable in terms of direct probabilistic evidence rather than only classical significance testing. 

### Regression models

After the hypothesis-testing section, the notebook builds regression models for **runs per game (R/G)** using offensive metrics such as OBP and SLG.

The baseline model is a Bayesian linear regression of the form:

```text
y_i ~ Normal(mu_i, sigma)
mu_i = beta_0 + beta_OBP * zOBP_i + beta_SLG * zSLG_i
```

where the predictors are standardized. The notebook specifies weakly informative priors, including:

- `beta_0 ~ Normal(4.4, 1)`
- `beta_OBP ~ Normal(0, 1)`
- `beta_SLG ~ Normal(0, 1)`
- `sigma ~ HalfNormal(1)`

This model is used to explain expected offensive output through two core sabermetric variables rather than through traditional aggregate statistics alone. 

---

## Study II – Are Modern Hitter Categories Meaningful?

The second part of the notebook shifts from team-level run production to player-level offensive archetypes.

### Research question

The notebook asks whether common hitter categories derived from **OBP** and **SLG** correspond to meaningful differences in offensive value.

The text explains that hitters are often grouped into four informal archetypes based on OBP and SLG quadrant analysis:

- Elite
- Contact
- Power
- Below-average

To test whether these categories are useful, the project evaluates them against **wOBA** (weighted on-base average), a run-based offensive metric that better reflects overall contribution than simpler descriptive statistics.

### Model specifications

This section includes at least two regression formulations:

- a **Normal model**;
- a **robust Student-t model**.

The robust version is explicitly motivated as a way to reduce the influence of outliers. In the notebook, the residual model is written as:

```text
y_i ~ StudentT(mu_i, sigma, nu)
```

with an exponential prior on the degrees-of-freedom parameter used to control tail heaviness and robustness. 

---

## Offense–Defense Latent Team Model

A later section of the notebook introduces a more structured team-level model for runs per game using latent offensive and defensive strengths.

The formulation is:

```text
y_i ~ Normal(mu_i, sigma)
mu_i = alpha + Off_i - Def_i
```

where:

- `Off_i` is the latent offensive strength of team `i`;
- `Def_i` is the latent defensive strength of team `i`;
- `alpha` is a league-wide baseline;
- `sigma` captures residual variability.

This section compares:

- an **unpooled model**;
- a **hierarchical model**.

The goal is to study the trade-off between flexibility, pooling, and predictive performance in a latent team-strength framework. 

---

## Model Comparison

The notebook uses two standard Bayesian predictive criteria to compare alternative models:

- **WAIC** (Widely Applicable Information Criterion)
- **LOO** (approximate leave-one-out cross-validation)

The reported outputs show a clear preference for the **unpooled offense–defense model** over the hierarchical version in this specific setup.
