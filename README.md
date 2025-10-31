# 🔐 Breaking and Rebuilding Privacy: Anonymizing Crypto Wallets Data

**Authors:** Fernando Gonzalez Domenech & Giovanni Murgia  

---

## 📘 Project Overview

This project investigates **privacy-preserving techniques** applied to **cryptocurrency wallet data**, a domain known for its sensitive financial and behavioral information.  
The goal is to anonymize a synthetic dataset while maintaining enough utility for analytical modeling.

---

## 🧾 Dataset

- **Rows:** 10,000  
- **Attributes:** 10  
- **Source:** Generated via [Mockaroo.com](https://www.mockaroo.com) with custom Ruby scripts for realistic weighted distributions.  

### 🔎 Key Columns

| Category | Attributes | Description |
|-----------|-------------|-------------|
| Identifier | `wallet_address` | Unique crypto wallet address |
| Quasi-identifiers | `network`, `country`, `client_type`, `last_active` | Used for generalization and suppression |
| Sensitive attributes | `balance_usd`, `activity_score`, etc. | Financial or behavioral data requiring protection |

---

## 🧠 Anonymization Techniques

| Technique | Description |
|------------|-------------|
| **Identifier Masking** | Replaced all characters after the first six with `*` to anonymize wallet addresses. |
| **QI Generalization** | Grouped countries outside the top 10 under “Other”, simplified client types (human vs. non-human), and binned balances into intervals. |
| **Noise Perturbation (Optional)** | Added small random noise to balance values to retain analytical usability. |
| **Local Suppression** | Suppressed quasi-identifiers appearing in fewer than 5 records to enforce **k ≥ 5** anonymity. |

---

## 📊 Evaluation

Two aspects were evaluated before and after anonymization:

1. **k-Anonymity** — verified that each combination of quasi-identifiers had at least 5 identical records.  
2. **Model Utility** — trained a **Logistic Regression** model to verify that predictive performance remained stable post-anonymization.

---

## 🧩 Results

- Achieved **k ≥ 5** for all quasi-identifier groups.  
- Minimal degradation in logistic regression performance, showing that **privacy protection can coexist with analytical utility**.  

---

## 💭 Discussion

The experiment highlights how **privacy–utility trade-offs** can be effectively managed in sensitive financial datasets.  
Further research directions:
- Applying anonymization to **larger or more complex datasets**.  
- Evaluating other ML models (e.g., Decision Trees, Neural Networks) on anonymized data.  

---

## ⚙️ Files in This Repository

| File | Description |
|------|--------------|
| `PPIA_HW1.ipynb` | Jupyter Notebook containing dataset generation, anonymization, and evaluation code. |

---

## 🧰 Requirements

To run the notebook:
```bash
pip install pandas numpy scikit-learn matplotlib
