# 🔐 Breaking and Rebuilding Privacy: Microdata Anonymization and Differential Privacy on Cryptocurrency Wallets Data

**Authors:** Fernando Gonzalez Domenech & Giovanni Murgia

---

## 📘 Project Overview

This repository contains two complementary privacy studies applied to the same synthetic **cryptocurrency wallet dataset**, focusing on:

* **Part 1:** Microdata anonymization (k-anonymity, suppression, generalization)
* **Part 2:** Differential Privacy (Local and Central mechanisms)

The objective is to understand how different privacy frameworks protect sensitive financial data while preserving analytical utility.

---

# 🧾 Dataset

* **Rows:** 10,000
* **Attributes:** 10
* **Source:** Generated via Mockaroo with custom Ruby scripts
* The dataset contains:

  * **Identifiers:** e.g., `wallet_address`
  * **Quasi-identifiers:** `country`, `network`, `client_type`, etc.
  * **Sensitive attributes:** `balance_usd`, `activity_score`, `sanctions_score`, `high_risk`

---

# 🔒 Part 1 — Microdata Protection Techniques

This part applies classical anonymization techniques to achieve **k ≥ 5 anonymity** while maintaining utility for downstream analysis.

## 🟦 Identifier Masking

Sensitive direct identifiers (`wallet_address`) were masked by keeping the first 6 characters and replacing the rest with `*`.

## 🟩 Quasi-Identifier Generalization

Performed generalization on attributes that could indirectly identify users:

* Countries outside the top 10 → grouped into “Other”
* Client types grouped (human vs. non-human)
* Numeric attributes binned into intervals (e.g., balance brackets)

## 🟥 Local Suppression

Quasi-identifier combinations appearing in fewer than **k = 5** records were suppressed or removed to enforce k-anonymity.

## 🟨 Optional Noise Perturbation

Small random noise added to numeric attributes (e.g., `balance_usd`) to preserve analytical trends.

## 📊 Evaluation: k-Anonymity + Utility

* Verified that all QI combinations satisfy **k ≥ 5**
* Trained a Logistic Regression model before/after anonymization
* Observed minimal performance degradation → **utility preserved**

---

# 🔐 Part 2 — Differential Privacy Protection

The second part focuses on **Local** and **Central** Differential Privacy to protect sensitive financial risk indicators while still supporting statistical analysis.

---

## 🟦 Local Differential Privacy (LDP)

LDP protects users **before data collection**, meaning the server never sees their true values.

### 1. **Trivial Coin Toss Mechanism**

* Returns the true `high_risk` value with 50% probability
* Otherwise returns a random coin flip
* Introduced as a simple baseline for data perturbation (non-parameterized)

### 2. **ε-Coin Toss / Randomized Response**

Each user perturbs their own binary value using:

[
p = \frac{e^\varepsilon}{e^\varepsilon + 1}
]

* With probability (p), return the true value
* With probability (1 - p), return a fair coin flip
* Using the inverse estimator, we reconstructed the true `high_risk` rate

### 3. **Microsoft 1-Bit LDP Mechanism**

For numeric values like `sanctions_score`:

* Users normalize (X_i) to ([0,1])
* Each user sends **one single DP-protected bit**
* The server aggregates bits and uses an unbiased estimator to recover the original mean
* Demonstrates strong LDP with minimal communication

---

## 🟥 Central Differential Privacy (CDP)

In CDP, a trusted curator holds raw data and adds noise to **query outputs**.

### 1. **Counting Query (High-Risk Count)**

* Sensitivity = 1
* Laplace noise added to the true count
* Running the mechanism for different ε values shows how noisy counts converge toward the true count as ε increases

### 2. **Mean Query (Balance USD)**

* Values clipped to ([L, U]) (1st–99th percentile) to bound sensitivity
  → prevents “whale” outliers dominating the mean
* Sensitivity = ((U - L) / n)
* Laplace noise added to the mean
* KDE plots show noisy means approaching the true clipped mean for larger ε

---

## 🟧 Above Threshold (Sparse Vector Technique)

A CDP mechanism that:

* Tests many queries while spending only **one ε**
* Adds noise to both the threshold and each query
* Returns **only the first query** whose noisy value exceeds the noisy threshold

In our dataset:

* Queries = mean `sanctions_score` for the top countries
* Threshold = 75th percentile of country means
* Output revealed **only the first passing country** (e.g., *Slovakia*)
* All true means, noisy values, and comparisons remained hidden

---

# 📊 Key Findings

* Differential Privacy enables useful analysis of sensitive financial data without exposing individual information.
* Experiments with multiple ε values highlight the **privacy–utility tradeoff**:
  Stronger privacy → more noise; weaker privacy → higher accuracy.
* Choosing the right model (Local vs Central DP) is essential:
  LDP when no trust in the collector, CDP when accurate statistics are needed.

---

# 📁 Files in This Repository

| File                                | Description                                                                              |
| ----------------------------------- | ---------------------------------------------------------------------------------------- |
| `PPIA_HW1.ipynb`                    | Microdata anonymization techniques (masking, suppression, generalization).               |
| `PPIA_HW2.ipynb` | Differential Privacy mechanisms (LDP, CDP, Laplace, RR, Microsoft LDP, Above Threshold). |

---

# 🧰 Requirements

Install dependencies:

```bash
pip install pandas numpy scikit-learn matplotlib seaborn
```

---
