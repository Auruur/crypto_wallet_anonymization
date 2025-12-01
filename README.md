---

# 🔐 Breaking and Rebuilding Privacy: Anonymizing and Privatizing Crypto Wallets Data

**Authors:** Fernando Gonzalez Domenech & Giovanni Murgia

---

## 📘 Project Overview

This repository contains two complementary privacy studies performed on the same synthetic **cryptocurrency wallet dataset**, focusing on both **microdata anonymization techniques** (HW1) and **Differential Privacy (DP)** mechanisms (HW2).
The goal is to understand how different privacy frameworks protect sensitive financial data while maintaining analytical utility.

---

# 🧾 Dataset

* **Rows:** 10,000
* **Attributes:** 10
* **Source:** Generated via Mockaroo with custom Ruby scripts
* Contains identifiers, quasi-identifiers, and sensitive attributes such as
  `balance_usd`, `sanctions_score`, `activity_score`, and a binary `high_risk` flag.

---

# 🔒 Homework 1: Microdata Protection Techniques

(unchanged — your existing text fits perfectly)

✔ Identifier masking
✔ QI generalization
✔ Local suppression ensuring **k ≥ 5**
✔ Optional mild perturbation
✔ Evaluation via k-anonymity + logistic regression utility

---

# 🔐 Homework 2: Differential Privacy Protection

The second part of the project applies **Differential Privacy (DP)** to the same dataset, exploring both **Local** and **Central** DP mechanisms and their impact on statistical utility.

## 🟦 Local Differential Privacy (LDP)

We implemented two LDP mechanisms on the binary `high_risk` indicator:

### 1. **Trivial Coin Toss Mechanism**

A simple privacy mechanism returning the true value 50% of the time and a random coin flip the other 50%.
Used as an intuitive introduction (non-parameterized privacy).

### 2. **ε-Coin Toss Mechanism (Randomized Response)**

A tunable LDP mechanism where each user perturbs their own value before sharing it:

[
p = \frac{e^\varepsilon}{e^\varepsilon + 1}
]

* with probability (p): return the true value
* otherwise: return a fair coin flip

Using the inverse estimator, we showed that the true proportion of high-risk users can be reconstructed despite local noise.

---

## 🟩 Central Differential Privacy (CDP)

In CDP, a **trusted curator** holds the raw data and adds Laplace noise to query outputs.

We applied CDP to two key statistical queries:

### **1. Counting Query (high-risk count)**

* Sensitivity = 1
* Laplace noise added to the count for multiple ε values
* Visualized how the distribution of noisy counts tightens as ε increases

### **2. Mean Query (balance_usd)**

* Values clipped to ([L, U]) using the 1st–99th percentiles to bound sensitivity
* Sensitivity = ((U-L)/n)
* Repeated DP mean computation for different ε to illustrate convergence to the true clipped mean
* Demonstrated how **clipping prevents “whale” values** from exploding sensitivity and destroying utility

---

## 🟧 Additional Mechanism: Microsoft 1-Bit LDP

We also implemented the **Microsoft Low-Communication 1-Bit LDP Mechanism**, where each user encodes a normalized numeric value into a single DP-protected bit.
Despite the extreme lossy compression, the true mean can still be **unbiasedly estimated** from the collected bits.

---

## 🟥 Above Threshold (Sparse Vector Technique)

Finally, we implemented the **Above Threshold Mechanism**, a CDP algorithm used to:

* test many statistics
* spend only a single ε
* reveal **only the first query** whose noisy value exceeds a **noisy threshold**

Applied to `sanctions_score` means per country, this mechanism returned only the name of the first country crossing the threshold (e.g., *Slovakia*), hiding all other values.

---

# 📊 Key Findings

* **Both Local and Central DP introduce a clear privacy–utility tradeoff:** smaller ε increases noise, larger ε improves accuracy.
* **LDP protects raw user data before collection**, ideal when no trust exists in the server.
* **CDP provides higher utility**, suitable when a trusted curator holds the dataset.
* Across all DP methods, analytical insights remain possible without exposing sensitive individual-level information.

---

# 📁 Files in This Repository

| File                                | Description                                                                                                |
| ----------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| `PPIA_HW1.ipynb`                    | Microdata anonymization techniques (masking, suppression, generalization, evaluation).                     |
| `PPIA_HW2.ipynb` | Differential Privacy experiments (LDP, CDP, Laplace, Randomized Response, Microsoft LDP, Above Threshold). |

---

# 🧰 Requirements

To run the notebooks:

```bash
pip install pandas numpy scikit-learn matplotlib seaborn
```

---
