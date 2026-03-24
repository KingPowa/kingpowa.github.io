---
layout: page
title: Non-Stationary RL for Forex Trading
description: A reinforcement-learning framework that clusters FX market regimes from the behavior of offline-trained trading agents, then trains regime-specialized policies and predicts the next regime to improve returns.
img: assets/img/projects/trading/trading_OG.png
importance: 2
category: Research
related_publications: false
---

This project summarizes my MSc thesis work on **non-stationary reinforcement learning for Forex trading**, introducing **Regime-Forecaster**: a framework that **detects and forecasts market regimes by clustering days based on the behavior of offline RL traders**, rather than clustering raw price series.  

<div class="row">
  <div class="col-md-7">
    <p class="lead">
      The core idea is to turn non-stationarity into a <b>switching-MDP</b> problem: identify recurring regimes, learn a policy per regime, and select the best policy for tomorrow by predicting the next regime.
    </p>
  </div>
  <div class="col-md-5">
    <div class="card p-3 shadow-sm rounded">
      <b>Focus</b>
      <ul class="mb-0">
        <li>Offline RL trading (FQI)</li>
        <li>Market regime discovery via clustering</li>
        <li>Cost-sensitive regime prediction</li>
      </ul>
    </div>
  </div>
</div>

---

## Problem

Forex markets are **highly non-stationary**: trends and reward/transition dynamics change due to events and evolving market conditions. The goal is to **improve trading performance** by explicitly modeling regime changes and exploiting **regime-specialized strategies**.  

---

## Approach: Regime-Forecaster

<div class="card p-3 shadow-sm rounded mb-3">
  <b>Key innovation:</b> represent each day not by prices, but by the <i>behavior and performance</i> of a diverse ensemble of RL traders simulated on that day.
</div>

<div class="row">
  <div class="row justify-content-center">
      <div class="col-12" style="max-width: 70%;">
        {% include figure.liquid loading="eager" path="assets/img/projects/trading/method.png"
        title="Pipeline for the Forex Regime-Forecaster" class="img-fluid rounded z-depth-1" %}
      </div>
  </div>
</div>
<div class="caption">
  Pipeline for the Forex Regime-Forecaster.
</div>

The pipeline is organized into five high-level steps:  

1. **Train a diverse ensemble of traders (offline RL)**  
   Policies are trained with **Fitted-Q Iteration (FQI)** using **XGBoost** as a regressor; diversity is induced via different hyperparameters.

2. **Select representative policies**  
   Similar strategies are removed using policy-distance measures (e.g., **Total Variation** / **KL-based** similarity) to keep a compact but expressive set.

3. **Cluster days into regimes**  
   Each day is embedded via policy-derived features (e.g., reward/action time series). A clustering model groups days into **market regimes**.

4. **Train regime-specialized policies**  
   One policy per cluster/regime is trained and compared to a general policy trained across all data.

5. **Predict the next regime and trade accordingly**  
   A classifier predicts tomorrow’s cluster using only information available before/early in the day (calendar features, price statistics, volatility proxy, recent cluster history, and short “probe” executions of policies).  
   To align prediction with profit, the predictor can be **cost-sensitive**, penalizing mistakes by their return impact (guess-averse loss).  

---

## Modeling details

- **Environment**: daily episodes (8:00–18:00 CET), minute-level steps.  
- **Actions**: *long / short / flat*.  
- **State**: recent normalized rate variations + time/spread/day encoding + previous allocation.  
- **Reward**: position × price change minus transaction/spread costs.  

---

## Data & Experiments

Two datasets were used:  

- **Real FX**: **EUR/USD** historical data (2017–2021), exhibiting regime shifts and non-stationarity.
- **Synthetic**: a **Vasicek** process with regime switches driven by a Markov chain (used to validate predictability and end-to-end gains).

---

## Results

- **Regime discovery + specialization worked**: specialized policies tended to outperform the general policy when evaluated inside their inferred cluster (shown via test return matrices).  
- **EUR/USD regime prediction was challenging**: regime forecasting models (Markov chain and XGBoost) achieved limited accuracy and did not yield reliable end-to-end gains in that setting.  
- **Synthetic dataset succeeded end-to-end**: regime prediction became accurate and profitable. In particular, the **XGBoost predictor with cost-sensitive/guess-averse loss** achieved strong gains over the general policy (e.g., ~110% expected profit gain reported for that setup).  

<div class="alert alert-light border mt-3">
  <b>Takeaway:</b> clustering market regimes from <i>agent behavior</i> is viable and can unlock strong returns when the regime process is sufficiently predictable; real FX remains harder and motivates richer temporal clustering and predictors.
</div>

---

## Skills developed

- **Offline Reinforcement Learning**: MDP formulation, FQI, policy evaluation, hyperparameter exploration.    
- **Gradient-Boosted Trees**: XGBoost for value-function approximation and for multi-class regime prediction.    
- **Unsupervised Learning**: clustering design, silhouette-based selection, hierarchical clustering / k-means, distance metrics (TV / KL, Gower).    
- **Non-stationarity & Regime Modeling**: switching-MDP framing, regime specialization, robustness considerations.    
- **Feature engineering for time series**: day/calendar features, OHLC-derived statistics, volatility proxy, label-history windows.    
- **Experimentation & evaluation**: ablations across representations (returns vs reward/action series), cluster-quality assessment, return-gain metrics (expected vs effective).    

---

## Artifacts

- **Executive summary (PDF)**:  [Download](/assets/pdf/executive_summary_thesis.pdf)  
- **Thesis figures & results** (included in the PDF): regime transition matrix (synthetic generator), test return matrices, and gain table for predictors.