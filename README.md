# Concrete Compressive Strength Prediction

> **Course:** MTH3303 – Probability & Statistics for AI
> **Team:** D — Polytechnique Montréal
> **Final Rank:** **1st Place** 🏆
> **Final RMSE on Leaderboard:** **3.96123**

Kaggle competition: [**Résistance à la compression du béton**](https://www.kaggle.com/competitions/Resistance-a-la-compression-du-beton)

---

## Table of Contents

1. [Overview](#overview)  
2. [Approach Summary](#approach-summary)  
    2.1. [Exploratory Data Analysis (EDA)](#exploratory-data-analysis-eda)  
    2.2. [Feature Engineering](#feature-engineering)  
3. [Models Explored](#models-explored)  
4. [Final Solution: Heterogeneous Ensemble](#final-solution-heterogeneous-ensemble)  
5. [Team & Credits](#team--credits)  

---

## Overview

This project is part of the **MTH3303 – Probability & Statistics for AI** course at Polytechnique Montréal. The goal is to predict the **compressive strength of concrete** based on its composition and curing age.

The dataset contains:

* **700 training samples** (`train.csv`) with known compressive strength
* **330 test samples** (`test.csv`) without labels
* Evaluation metric: **Root Mean Squared Error (RMSE)**

Each recipe includes the quantities (kg/m³) of cement, slag, ash, water, superplasticizer, coarse aggregate, fine aggregate, and the concrete’s age in days.

---

## Approach Summary

### Exploratory Data Analysis (EDA)

We analyzed variable correlations and identified:

* **Cement** is the strongest positive predictor of strength.
* **Age** shows a **non-linear** effect — strength increases rapidly at first and then plateaus.
* **Water** and **Superplasticizer** are highly correlated, prompting us to manage multicollinearity carefully.

### Feature Engineering

To better model the underlying relationships, we created the following set of features. **The final winning solution utilized all these features, including a second age transformation.**

* **Physically meaningful ratios from literature:**
    * `Binder = Cement + Slag + Ash`
    * `w_b = Water/Binder`
    * `w_c = Water/Cement`
* **Age transformations (for maximum signal):**
    * `log10(Age+1)` (Proven effective)
    * **`sqrt(Age)` (New feature added for final ensemble)**

---

## Models Explored

We incrementally built and compared several regression models:

| Model                 | Idea            | RMSE (val) |
| --------------------- | --------------- | ---------- |
| Baseline              | Cement + Fine   | ~16        |
| Linear (all vars)     | Full predictors | ~12        |
| + log(Age)            | Nonlinear age   | ~9         |
| Ridge Regression      | L2 Regularization | ~6.45      |
| **EvoTrees (Baseline)**| Tree Ensemble   | **~4.19**  |

---

## Final Solution: Heterogeneous Ensemble

Our final strategy focused on **Variance Reduction** through **Ensemble Blending** to move from an unstable $\text{RMSE} \approx 4.19$ (local) to the winning score of **3.96**. 

[Image of Ensemble Learning Diagram]


| Model Component | Strategy | Weight | Role |
| :--- | :--- | :--- | :--- |
| **EvoTreeRegressor** (Base) | **Homogeneous Ensemble** (Average of 5 runs with different seeds) | **80%** | Provides the strong, stable base prediction. |
| **RandomForestRegressor** (Diversity) | **Heterogeneous Ensemble** (A different algorithm, Bagging) | **20%** | Reduces the systematic errors (bias) inherent in Gradient Boosting. |

**Best configuration used for EvoTrees:**
* Features: All physical ratios + $\mathbf{\text{log10(Age+1)}} + \mathbf{\text{sqrt(Age)}}$
* Hyperparameters: $\mathbf{nrounds = 800}$, $\mathbf{\text{max\_depth} = 6}$, $\mathbf{\text{eta} = 0.05}$

The blend successfully neutralized the variance, leading to the **1st place $\text{RMSE}$ of $\mathbf{3.96123}$**.

---

## Team & Credits

**Team D**
Belhouala Omar-Rayan
Benzekri Omar
Hedad Ireina
Ibrahim Massoud

**Instructor**
**Prof. Jonathan Jalbert**