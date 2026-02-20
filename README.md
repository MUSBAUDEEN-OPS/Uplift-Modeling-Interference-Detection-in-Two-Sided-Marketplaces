# Uplift Modeling & Interference Detection in Two-Sided Marketplaces

**By Ibrahim Musbaudeen | Data Scientist**

> Moving beyond *"who will buy?"* to *"who will buy **because** of the promotion?"*

---

## 🚀 Key Results

| Metric | Value |
|---|---|
| ROI Improvement (vs. blanket targeting) | **+589%** |
| ROI Multiplier | **5.02×** |
| Campaign Cost Reduction | **70% ($35K saved per campaign)** |
| Modelled Annual Savings | **$140,000** |
| Network Interference Bias Detected | **18% theoretical bias** |
| Best Qini Coefficient (T-Learner) | **11.06** |

---

## 📌 Problem Statement

Promotional coupons are among the most powerful — and most wasteful — tools in e-commerce. Traditional ML models predict *who will convert*, but many of those customers would have bought anyway. Sending coupons to them wastes budget. Worse, some customers (the "Sleeping Dogs") actually convert *less* when targeted.

This project uses **causal machine learning (uplift modeling)** to estimate the **Conditional Average Treatment Effect (CATE)** at the individual level — identifying precisely which customers genuinely respond to a promotion — and combines it with **network interference detection** for two-sided marketplaces where shared courier supply creates spillover between customers.

---

## 🧠 Methodology

### Data
- 10,000 synthetic customers across 50 geographic neighborhoods
- Cluster-randomized controlled trial design (entire neighborhoods assigned to treatment/control)
- Network interference explicitly modelled via courier scarcity spillover

### Customer Segments (Ground Truth)
| Segment | Prevalence | True Avg CATE | Action |
|---|---|---|---|
| Persuadable | 5.3% | +0.39 | ✅ Target these |
| Sure Thing | 3.5% | +0.06 | ⚠️ Skip — converts anyway |
| Other | 91.2% | ~0.00 | ⚠️ Skip — no response |
| Sleeping Dog | <1% | Negative | ❌ Never target — suppresses conversion |

### Uplift Meta-Learners Implemented

**T-Learner** — Trains separate models for treatment and control groups. CATE = μ₁(x) − μ₀(x). Best Qini (11.06), strongest targeting rank.

**S-Learner** — Single model with treatment as a feature. Naturally regularizes effect size. Best correlation (0.549).

**X-Learner** — Cross-imputation approach with propensity weighting. Best RMSE (0.1242) and MAE (0.1003). Selected for business impact analysis.

### Evaluation Metrics
- **Qini Coefficient** — primary uplift-specific metric (area between uplift curve and random baseline)
- RMSE / MAE against ground-truth CATE
- Pearson correlation of predicted vs. true CATE
- Segment-level accuracy breakdown

### Network Interference Detection
- Compared individual-level vs. cluster-level ATE estimates
- Computed Intra-Cluster Correlation (ICC = 0.0044)
- Quantified theoretical interference bias of 18% from spillover parameter

---

## 📊 Model Performance

| Metric | T-Learner | S-Learner | X-Learner |
|---|---|---|---|
| RMSE | 0.1427 | 0.1267 | **0.1242** |
| MAE | 0.1130 | 0.1004 | **0.1003** |
| Correlation | 0.3913 | **0.5488** | 0.4833 |
| Qini Coefficient | **11.061 ★** | 2.435 | 4.867 |

---

## 💼 Business Impact

| Strategy | Customers Targeted | ROI |
|---|---|---|
| Blanket (all 10K) | 10,000 | 146.5% |
| Random (30%) | 3,000 | 691.4% |
| **Uplift (top 30% by CATE)** | **3,000** | **735.5%** |

Uplift targeting delivers the same number of coupons as random targeting but concentrates them on customers with the highest genuine incremental response — converting more customers per dollar spent while avoiding Sleeping Dogs entirely.

---

## 📁 Project Structure

```
uplift-modeling-marketplace/
│
├── README.md
├── requirements.txt
├── .gitignore
│
├── data/
│   ├── README.md                  ← Data schema & generation guide
│   └── marketplace_data.csv       ← Synthetic dataset (10K rows)
│
├── notebooks/
│   └── Uplift_Modeling_Complete_Project.ipynb
│
├── reports/
│   ├── Uplift_Modeling_Report_Ibrahim_Musbaudeen.pdf
│   └── Uplift_Modeling_Executive_Summary.pptx
│
└── figures/                       ← Exported plots for this README
    ├── eda_overview.png
    ├── uplift_curve.png
    ├── roi_comparison.png
    └── interference_analysis.png
```

---

## ⚙️ How to Run

### 1. Clone the repository
```bash
git clone https://github.com/YOUR_USERNAME/uplift-modeling-marketplace.git
cd uplift-modeling-marketplace
```

### 2. Create a virtual environment (recommended)
```bash
python -m venv venv
source venv/bin/activate        # macOS/Linux
venv\Scripts\activate           # Windows
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Launch the notebook
```bash
jupyter notebook notebooks/Uplift_Modeling_Complete_Project.ipynb
```

Run all cells from top to bottom. The notebook is self-contained — it generates the synthetic data, trains all three meta-learners, evaluates them, and produces all business impact and interference analysis outputs.

> **Note:** The dataset in `data/marketplace_data.csv` was pre-generated with `random_seed=42`. Running the data generation cells will reproduce the exact same file.

---

## 🔬 Key Technical Concepts

**Uplift Modeling / CATE Estimation** — Rather than predicting P(convert), we estimate the *incremental* effect of a treatment: P(convert | coupon) − P(convert | no coupon). This is the Conditional Average Treatment Effect (CATE).

**Cluster-Randomized Controlled Trial (CRCT)** — Randomizing at the neighborhood level (not individual level) avoids violating the Stable Unit Treatment Value Assumption (SUTVA) in two-sided marketplaces where shared courier supply creates interference between customers.

**Network Interference** — When treated and control units share resources (couriers), the control group's outcome is suppressed by courier scarcity, biasing standard A/B test ATE estimates upward.

**Qini Coefficient** — The area between the cumulative uplift curve and a random targeting baseline. The primary evaluation metric for uplift model quality in marketing applications.

---

## 📋 Limitations & Future Work

- Synthetic data cannot capture temporal dynamics, competitive effects, or multi-touch attribution
- Meta-learners attenuate extreme heterogeneous effects (Persuadable uplift underestimated: 0.206 predicted vs. 0.388 true); Platt scaling or isotonic regression calibration could reduce this bias
- Future extensions: dynamic/sequential uplift modeling, real-time scoring API integration, explicit network graph modeling of courier-customer connections, joint optimization of coupon value and targeting threshold

---

## 📄 Reports

Full write-up and executive summary are available in the `reports/` directory:
- [`Uplift_Modeling_Report_Ibrahim_Musbaudeen.pdf`](https://github.com/MUSBAUDEEN-OPS/Uplift-Modeling-Interference-Detection-in-Two-Sided-Marketplaces/blob/main/Project%20overview/Uplift_Modeling_Report_Ibrahim_Musbaudeen.pdf) — Complete technical report
- [`Uplift_Modeling_Executive_Summary.pptx`](https://github.com/MUSBAUDEEN-OPS/Uplift-Modeling-Interference-Detection-in-Two-Sided-Marketplaces/blob/main/Executive%20summary/Uplift_Modeling_Executive_Summary.pptx) — Slide deck for stakeholder presentation

---

## 👤 Author

**Ibrahim Musbaudeen** — Data Scientist

*This project was developed as a complete end-to-end portfolio demonstration of causal machine learning applied to marketing optimization in two-sided marketplace environments.*
