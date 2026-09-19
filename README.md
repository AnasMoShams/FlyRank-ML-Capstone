# FlyRank ML Capstone — Content Opportunity Scoring

## About Project

This project develops a data-driven framework for **prioritizing content items for review or potential improvement**.

The project uses historical search-performance data and content-level characteristics to identify content items that may deserve attention first.

Rather than predicting a guaranteed Google ranking or claiming that an optimization will directly improve traffic, the project focuses on **relative opportunity ranking and decision support**.

The analysis was developed as part of the **FlyRank Internship Capstone**.

---

## Problem

Content teams may have thousands of content items to review, making it difficult to decide where to focus their effort first.

A simple approach based only on impressions, clicks, or search position may miss useful combinations of signals.

The main research question is:

> **Can historical search performance and content-level characteristics be used to rank content items by their priority for review or improvement?**

The goal is therefore to build a reproducible ranking approach that identifies potentially high-priority content items and provides evidence-based reasons for their prioritization.

---

## What I Did

I developed an end-to-end machine learning workflow covering:

1. Dataset access and schema inspection.
2. Dataset inventory and relationship analysis.
3. Data-quality analysis.
4. Research-question definition.
5. Analytical dataset construction.
6. Exploratory data analysis.
7. ML problem definition.
8. Feature engineering.
9. Baseline ranking methods.
10. Future opportunity benchmark construction.
11. Random Forest modeling.
12. Ranking evaluation.
13. Model interpretation.
14. Content opportunity ranking.
15. Evidence-based recommendation generation.
16. Research-paper documentation.

### Data Preparation

The analysis uses three main datasets:

* `dim_clients.parquet`
* `dim_content.parquet`
* `fact_content_daily_performance_sample.parquet`

The primary analytical unit is:

`client_hash_id + content_hash_id`

The raw performance data contained **11,694,072 observations**. After removing exact duplicate rows and aggregating performance at the client-content level, the analytical dataset contained **409,205 unique client-content observations**.

### Temporal Design

The project separates historical information from future evaluation:

* **Historical feature period:** June 1–20, 2026
* **Future evaluation period:** June 21–30, 2026

Historical performance is used to construct model features, while the future period is used to construct the evaluation benchmark.

### Feature Engineering

The model uses historical performance signals such as:

* Historical impressions
* Historical clicks
* Historical mean position
* Historical CTR
* Impressions per day
* Clicks per day
* Reporting days

These are combined with content and search-environment characteristics including:

* Search volume
* Competition
* Competition level
* CPC
* Search intent
* Backlinks
* Category count
* Word count
* Character count
* Content type

### Modeling

A **Random Forest Regressor** was used to estimate a continuous future opportunity score.

Model configuration:

* Trees: `200`
* Maximum depth: `12`
* Minimum samples per leaf: `5`
* Random state: `42`

The model output is used to rank content items from higher to lower predicted opportunity.

---

## What Came of It

The final system produces a ranked list of content items that may deserve review, together with supporting signals and reason categories.

The model achieved the following ranking performance:

|   K | Precision@K | Recall@K |     NDCG@K |
| --: | ----------: | -------: | ---------: |
|  10 |        0.50 |     0.50 | **0.9831** |
|  25 |        0.64 |     0.64 | **0.9701** |
|  50 |        0.56 |     0.56 | **0.9694** |
| 100 |        0.55 |     0.55 | **0.9693** |

For comparison, the historical-impressions baseline achieved:

|   K | Model NDCG | Impressions NDCG |
| --: | ---------: | ---------------: |
|  10 | **0.9831** |           0.7496 |
|  25 | **0.9701** |           0.7707 |
|  50 | **0.9694** |           0.7924 |
| 100 | **0.9693** |           0.8073 |

Within the evaluation setup, the model showed stronger alignment with the constructed future opportunity benchmark than the tested simple historical-performance baselines.

The final ranking also converts model outputs into interpretable review categories such as:

* Low CTR despite search visibility
* High search volume + weak position
* High impressions + weak position + low CTR
* High impressions + weak position
* Multiple moderate opportunity signals

These categories help translate model predictions into practical content-review priorities.

---

## Key Model Signals

Random Forest feature importance showed that the largest contributors to the model's predictions were:

| Feature                        | Importance |
| ------------------------------ | ---------: |
| Historical mean position       |     0.6778 |
| Historical impressions per day |     0.1620 |
| Historical impressions         |     0.0857 |
| Word count                     |     0.0149 |
| Character count                |     0.0148 |
| Historical CTR                 |     0.0113 |
| Search volume                  |     0.0073 |

Historical mean position was the dominant feature according to the fitted Random Forest model.

These values describe model behavior and should **not** be interpreted as causal effects.

---

## Methodology

The overall workflow can be summarized as:

```text
Raw Data
   ↓
Data Quality
   ↓
Analytical Dataset
   ↓
Exploratory Data Analysis
   ↓
ML Problem Definition
   ↓
Feature Engineering
   ↓
Baseline Ranking
   ↓
Future Opportunity Benchmark
   ↓
Random Forest Model
   ↓
Ranking Evaluation
   ↓
Model Interpretation
   ↓
Opportunity Ranking
   ↓
Recommendations
```

---

## Tools & Technologies

### Programming & Analysis

* **Python**
* **Pandas**
* **NumPy**
* **Matplotlib**
* **DuckDB**

### Machine Learning

* **Scikit-learn**
* Random Forest Regression
* Feature preprocessing pipelines
* One-hot encoding
* Median imputation
* Ranking evaluation using Precision@K, Recall@K, and NDCG@K

### Data

* Parquet
* Hugging Face Datasets / Hub
* DuckDB for analytical querying

### Development & Documentation

* Jupyter Notebook
* VS Code
* Git
* GitHub
* Markdown

---

## Project Structure

```text
FlyRank-ML-Capstone/
│
├── work/
│   ├── notebooks/
│   │   ├── 00_project_setup.ipynb
│   │   ├── 01_dataset_access.ipynb
│   │   ├── 02_dataset_inventory.ipynb
│   │   ├── 03_data_quality.ipynb
│   │   ├── 04_research_direction.ipynb
│   │   ├── 05_analytical_dataset.ipynb
│   │   ├── 06_exploratory_data.ipynb
│   │   ├── 07_ml_problem_definition.ipynb
│   │   ├── 08_feature_engineering.ipynb
│   │   ├── 09_baseline.ipynb
│   │   ├── 10_ml_modeling.ipynb
│   │   ├── 11_model_interpretation.ipynb
│   │   ├── 12_interpretation.ipynb
│   │   ├── 13_opportunity_ranking.ipynb
│   │   └── 14_research_paper.ipynb
│   │
│   └── paper/
│       └── research_paper.html
│
├── submission/
│   └── paper_url.txt
│
└── README.md
```

---

## Research Paper

The complete research paper documents the methodology, experiments, results, limitations, and recommendations in detail.

**Research Paper:**  
Coming soon — public deployment will be added after GitHub Pages setup.

---

## Limitations

This project has several important limitations:

* The future opportunity score is a **constructed benchmark**, not a directly observed optimization-success label.
* The dataset is observational, so the analysis does not establish causal relationships.
* The supervised evaluation uses a random 80/20 split within the future-eligible modeling dataset.
* Missing values may contain information about the underlying data-generation process.
* Random Forest feature importance describes model behavior rather than causal effects.
* The dataset represents a specific time period and content population, limiting generalization to other environments.
* The final ranking is intended as a **decision-support mechanism**, not a guarantee of future SEO improvement.

---

## Reproducibility

The project is organized into separate notebooks covering the complete analytical workflow from data access through final opportunity ranking.

Important modeling configuration values are fixed:

```text
Random State: 42
Number of Trees: 200
Maximum Depth: 12
Minimum Samples per Leaf: 5
```

The preprocessing pipeline is fitted on training data before being applied to test data.

The notebooks document the major analytical decisions, feature construction, evaluation setup, and model configuration.

---

## Dataset Credit

This project uses the **FlyRank Internship Warehouse** dataset provided through FlyRank.

The analysis uses:

* `dim_clients.parquet`
* `dim_content.parquet`
* `fact_content_daily_performance_sample.parquet`

The dataset was used for educational and analytical purposes within the internship project.

---

## Author

**Anas Mohamed Shams**

Data Science / Machine Learning

**Project:** FlyRank ML Capstone
**Research Topic:** Content Opportunity Scoring

---

## Final Note

This project is designed as a **data-driven decision-support framework** for prioritizing content review.

The results identify relative opportunity according to the available historical and future performance signals. They should not be interpreted as a guarantee that changing a specific content item will improve Google rankings, traffic, or CTR.
