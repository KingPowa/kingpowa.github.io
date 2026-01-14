---
layout: page
title: Lightweight recommender system for Recsys 2022
description: A recommender system framework for Recsys 2022 for session-based fashion recommendation that we used in our paper.
img: assets/img/projects/recsys/Recsys-OG.png
importance: 2
category: Research
related_publications: true
---

This project summarizes our solution for the [ACM RecSys Challenge 2022](https://www.recsyschallenge.com/2022/) (team *Boston Team Party*), described in {% cite della2022lightweight %}. The system is a **two-stage, lightweight and scalable** pipeline: strong candidate generators propose items, and a **GBDT learning-to-rank** model blends model scores with **content + seasonality features** to output the final top-100 list.  

<p class="lead">
  Goal: predict the purchased item at the end of each anonymous session and return a ranked <b>top-100</b> list (evaluated with <b>MRR</b>).
</p>

<div class="row g-3">
  <div class="col-md-6">
    <div class="card p-3 shadow-sm rounded">
      <b>Why it worked</b>
      <ul class="mb-0">
        <li>Complementary candidate generators (sequence, graph, KNN, autoencoders, popularity)</li>
        <li>Feature-rich re-ranking with GBDTs (heterogeneous signals)</li>
        <li>Explicit seasonality via interaction weighting + entropy-based tendency</li>
      </ul>
    </div>
  </div>
  <div class="col-md-6">
    <div class="card p-3 shadow-sm rounded">
      <b>Outcome</b>
      <ul class="mb-0">
        <li><b>Public leaderboard MRR:</b> 0.18800</li>
        <li>Strong accuracy–efficiency tradeoff (practical for real-world pipelines)</li>
        <li>Open-source for reproducibility</li>
      </ul>
    </div>
  </div>
</div>

---

## Problem

In session-based fashion recommendation, users are anonymous and may have no long-term profile. For each session (a sequence of views ending with a purchase), the task is to produce a **top-100** ranking containing the purchased item, scored by **Mean Reciprocal Rank (MRR)**.  

---

## Data

The dataset contains **18 months** of online fashion sessions (Jan 2020–Jun 2021), with **~1.1M sessions** and **~24k items**. Each session includes views and a purchase, with timestamps and sparse item attributes.  

<div class="container">
  <div class="row justify-content-center">
    <div class="col-12" style="max-width: 95%;">
      <div class="row flex-nowrap align-items-center g-3 justify-content-center">
        <div class="col-6">
          {% include figure.liquid path="assets/img/projects/recsys/recsys_data1.png"
            title="Dataset sample" class="img-fluid rounded z-depth-1" %}
        </div>
        <div class="col-6">
          {% include figure.liquid path="assets/img/projects/recsys/recsys_data2.png"
            title="Item feature taxonomy" class="img-fluid rounded z-depth-1" %}
        </div>
      </div>
    </div>
  </div>
  <div class="caption">
    Dataset samples and a glimpse of the item-attribute taxonomy used for feature engineering.
  </div>
</div>

<div class="alert alert-light border mt-3">
  <b>Practical challenge:</b> the official test sessions were partially truncated (only the first 50–100% of views kept),
  which can break strict “next-item” assumptions for sequential models.  
</div>

---

## Approach

### 1) Candidate generation (fast, diverse experts)

We trained multiple recommenders and merged their top candidates per session. The pool included:  

- **Sequential**: GRU4Rec  
- **Graph-based**: RP3Beta  
- **Nearest-neighbors**: ItemKNN (CF+CBF), UserKNN (CF), plus a content-only KNN for sparse/cold cases  
- **Autoencoders / shallow models**: EASE^R, MultVAE, RecVAE  
- **Non-personalized**: TopPop

To better match fashion dynamics and recency, we used **interaction weighting** (views vs purchases, cyclic decay, exponential decay) for URM-based models.  

### 2) Feature engineering (content + compact embeddings + seasonality)

Feature engineering was central to the final gain. Highlights:  

- **Item content encoding**: multi-label encoding over attribute pairs (904 unique (category,value) tuples).
- **Dimensionality reduction**: a **VAE** compresses item content into a compact latent representation (latent size 32).
- **Session representations**: embeddings aggregated over session items and a **RecVAE** session encoding used as additional signals.
- **Seasonality signal**: an **entropy-based seasonal tendency** feature measuring whether an item is seasonal vs all-season (computed separately for views and purchases).

### 3) Ranking (GBDT learning-to-rank)

We cast re-ranking as a LETOR task where each row is a *(session, candidate item)* pair with: model scores + item/session features + seasonality signals.  
We trained **LambdaMART** with **LightGBM** (and compared to XGBoost), optimizing **MAP@100** (chosen for strong correlation with MRR and tool support). LightGBM was both faster and stronger in our experiments.  

---

## Solution outline

<div class="row">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid loading="eager" path="assets/img/projects/recsys/recsys22-pipeline.svg"
      title="Two-stage pipeline" class="img-fluid rounded z-depth-1" %}
  </div>
</div>
<div class="caption">
  Diverse candidate generators feed a feature-rich GBDT ranker, augmented with temporal weighting and seasonality features.
</div>

<div class="row g-3 mt-2">
  <div class="col-md-4">
    <div class="card p-3 shadow-sm rounded h-100">
      <b>Stage 1 — Retrieval</b>
      <div class="small text-muted mt-1">Multiple models propose candidates</div>
      <ul class="mb-0 mt-2">
        <li>Sequence (GRU)</li>
        <li>Graph (RP3Beta)</li>
        <li>KNN + content</li>
        <li>Autoencoders</li>
        <li>TopPop</li>
      </ul>
    </div>
  </div>
  <div class="col-md-4">
    <div class="card p-3 shadow-sm rounded h-100">
      <b>Feature fusion</b>
      <div class="small text-muted mt-1">Heterogeneous signals</div>
      <ul class="mb-0 mt-2">
        <li>Model scores (per candidate)</li>
        <li>Item attributes + embeddings</li>
        <li>Session embeddings</li>
        <li>Seasonality tendency</li>
      </ul>
    </div>
  </div>
  <div class="col-md-4">
    <div class="card p-3 shadow-sm rounded h-100">
      <b>Stage 2 — Ranking</b>
      <div class="small text-muted mt-1">Learning-to-rank with GBDTs</div>
      <ul class="mb-0 mt-2">
        <li>LambdaMART (LightGBM)</li>
        <li>XGBoost baseline</li>
        <li>MAP@100 optimization</li>
      </ul>
    </div>
  </div>
</div>

---

## Results

- **Public leaderboard MRR:** **0.18800**    
- LightGBM ranker outperformed XGBoost in our pipeline (public leaderboard MRR 0.18800 vs 0.18347).    
- The final model is **competitive, lightweight, and scalable**, with each feature family contributing meaningfully to ranking.  

---

## Skills developed

- **Session-based recommendation**: retrieval models spanning sequence-aware, graph-based, KNN, and autoencoder approaches.    
- **Learning-to-rank with GBDTs**: LambdaMART with LightGBM, comparison to XGBoost, MAP@100-driven training.    
- **Feature engineering at scale**: multi-label encoding for sparse taxonomies, embeddings via VAE/RecVAE, score normalization.    
- **Temporal/seasonal modeling**: interaction weighting (views vs purchases, cyclic + exponential decay) and entropy-based seasonal tendency features.    
- **Reproducible experimentation**: hyperparameter tuning with Optuna and validation split design aligned to the challenge protocol.    

---

## Artifacts

- **Paper:** {% cite della2022lightweight %} ([PDF](/assets/pdf/3556702.3556829.pdf))    
- **Code:** https://github.com/recsyspolimi/recsys-challenge-2022-dressipi