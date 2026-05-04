# Edu-PRO-analysis

# EduPro: Student Segmentation & Personalized Course Recommendation System

> A data-driven machine learning project to segment online learners and deliver personalized course recommendations using K-Means Clustering and Content-Based Filtering.

---

## Project Overview

EduPro is an online learning platform serving **3,000 active learners** across **60 courses** in 12 categories. This project builds an end-to-end **Student Segmentation and Personalized Recommendation System** to address the platform's core challenge: generic, one-size-fits-all course recommendations that fail to engage diverse learner types.

Using unsupervised machine learning on **10,000 enrollment transactions**, we identify **4 distinct learner personas** and build a cluster-aware recommendation engine that delivers personalized learning pathways for each segment.

---

## Problem Statement

EduPro currently faces:
- **One-size-fits-all recommendations** — no learner differentiation
- **Limited understanding of behavior patterns** — no framework to classify users
- **No structured segmentation** — engagement and revenue opportunities lost

As a result, learners struggle to discover relevant content and platform retention suffers.

---

## Objectives

1. **Segment learners** into distinct behavioral groups using unsupervised ML (K-Means Clustering)
2. **Engineer learner-level features** from raw transaction, demographic, and course data
3. **Identify learner personas** with interpretable, actionable business profiles
4. **Build a recommendation engine** using content-based filtering and rating-weighted relevance
5. **Evaluate cluster quality** using Silhouette Score and intra-cluster consistency
6. **Visualize insights** through an interactive Streamlit/HTML dashboard
7. **Deliver actionable strategies** for each learner segment to improve engagement and revenue

---

## Dataset

| Sheet | Records | Key Fields |
|-------|---------|------------|
| Users | 3,000 | UserID, Age, Gender |
| Courses | 60 | CourseID, Category, Type, Level, Rating, Price |
| Transactions | 10,000 | UserID, CourseID, Date, Amount, PaymentMethod |

**Platform Stats:**
- Age range: 15–35 years (mean: 24.97)
- Gender split: 50.7% Female / 49.3% Male
- Course categories: 12 (Programming, AI, Design, Data Science, Finance, etc.)
- Course types: 38 Free / 22 Paid
- Payment methods: PayPal, Credit Card, Bank Transfer

---

## Methodology

### Step 1 — Learner-Level Aggregation
Merge Transactions → Courses → Users and aggregate at UserID level.

### Step 2 — Feature Engineering (11 features)

| Feature | Description |
|---------|-------------|
| `total_courses` | Total enrollments per user |
| `total_spent` | Total amount paid |
| `avg_spent` | Average spend per course |
| `avg_course_rating` | Mean rating of enrolled courses |
| `free_courses` | Count of free course enrollments |
| `paid_courses` | Count of paid course enrollments |
| `unique_categories` | Number of distinct categories explored |
| `pref_category` | Most frequent course category (mode) |
| `pref_level` | Most frequent course level (mode) |
| `Age` | Learner age from Users sheet |
| `Gender` | Encoded gender (LabelEncoder) |

### Step 3 — Preprocessing
- **Normalization**: `StandardScaler` applied to all features
- **Encoding**: `LabelEncoder` for pref_category, pref_level, gender
- **Null handling**: Zero-imputation for sparse enrollment users

### Step 4 — Cluster Selection
Evaluated k=2 through k=7 using **Silhouette Score**:

| k | Silhouette Score |
|---|-----------------|
| 2 | 0.4694 |
| 3 | 0.2315 |
| **4** | **0.2053 ← selected** |
| 5 | 0.2003 |
| 6 | 0.1954 |
| 7 | 0.2060 |

> k=4 chosen for best balance of cluster quality + business interpretability.

### Step 5 — K-Means Clustering
```python
from sklearn.cluster import KMeans
km = KMeans(n_clusters=4, random_state=42, n_init=10)
learner_df['cluster'] = km.fit_predict(X_scaled)
```

### Step 6 — Recommendation Engine
Cluster-aware content-based filtering:
- Match courses to learner's preferred category and adjacent domains
- Apply difficulty ladder based on current level preference
- Rank by CourseRating within cluster
- Boost courses popular within the same cluster

---

## Results: 4 Learner Segments

| # | Persona | Size | Avg Courses | Avg Spend | Top Category | Level |
|---|---------|------|------------|-----------|-------------|-------|
| 0 | 🔐 Casual Explorers | 933 (31.1%) | 1.35 | ₹25.5 | Cybersecurity | Intermediate |
| 1 | 🚀 Power Learners | 454 (15.1%) | 13.41 | ₹1,248 | AI | Advanced |
| 2 | 🎯 Focused Investors | 615 (20.5%) | 1.58 | ₹418.7 | AI | Beginner |
| 3 | 🎨 Creative Browsers | 998 (33.3%) | 1.69 | ₹63.5 | Design | Advanced |

### Segment Insights
- **Casual Explorers**: Price-sensitive, high free course usage — conversion opportunity
- **Power Learners**: Highest LTV users — 50× the spend of Casual Explorers; retain via subscription
- **Focused Investors**: Career changers investing heavily per course — benefit from learning paths
- **Creative Browsers**: Largest segment, lowest satisfaction (2.64★) — re-engagement priority

---

## Evaluation

| Metric | Value | Interpretation |
|--------|-------|---------------|
| Silhouette Score (k=4) | 0.205 | Moderate — acceptable for mixed high-dim data |
| Intra-cluster Consistency | High | Distinct behavioral profiles confirmed |
| Power Learner vs Explorer spend ratio | 50× | Strong segment differentiation |
| Cluster size range | 454–998 | All segments actionable for campaigns |

---

## Project Structure

```
EduPro-Segmentation/
│
├── data/
│   └── EduPro_Online_Platform.xlsx       # Raw dataset (Users, Courses, Transactions)
│
├── notebooks/
│   └── EduPro_Segmentation.ipynb         # Full analysis notebook
│
├── outputs/
│   ├── EduPro_Dashboard.html             # Interactive analytics dashboard
│   ├── EduPro_Research_Paper.docx        # Full research paper (10 sections)
│   └── EduPro_Executive_Summary.docx     # Executive summary for stakeholders
│
├── app/
│   └── streamlit_app.py                  # Streamlit web application
│
└── README.md
```

---

## Dashboard Features

The interactive HTML dashboard includes:
- **Overview Page**: KPI cards, enrollment trends, category/age/payment breakdowns
- **Segments Page**: Learner persona cards, radar chart, bubble chart, metric comparisons
- **Recommendations Page**: Filterable course cards by segment + strategy table
- **Methodology Page**: 6-step pipeline, silhouette chart, feature importance

---

## Tech Stack

| Tool | Purpose |
|------|---------|
| Python 3.x | Core analysis language |
| pandas | Data manipulation and aggregation |
| scikit-learn | KMeans, StandardScaler, LabelEncoder, silhouette_score |
| Chart.js | Interactive dashboard visualizations |
| HTML/CSS/JS | Dashboard frontend |
| Streamlit | Web application framework |
| openpyxl / docx | Excel + Word document generation |

---

## Key Findings

1. **AI courses** generate the highest avg spend: ₹244.6 per enrollment
2. **Cybersecurity** is the top free-course category with the highest-rated course (4.94★)
3. **15.1% of users** (Power Learners) drive disproportionate platform revenue
4. **33.3% of users** (Creative Browsers) show low satisfaction — highest re-engagement risk
5. **June** peaks in enrollments (899); consistent year-round activity observed

---

## Recommendations

| Segment | Strategy | Expected Impact |
|---------|----------|----------------|
| Casual Explorers | Free-to-paid conversion via cybersecurity career path | ↑ Paid enrollment 15–25% |
| Power Learners | Subscription plan + loyalty rewards | ↑ LTV 30%, ↓ churn 20% |
| Focused Investors | AI beginner-to-intermediate course bundles | ↑ Courses/user to 3.5+ |
| Creative Browsers | High-rated Design/Web Dev recommendations | ↑ Avg rating to 3.5+ |

---

## Deliverables

- [x] Exploratory Data Analysis (EDA)
- [x] Feature Engineering (11 learner-level features)
- [x] K-Means Clustering (k=4, silhouette-validated)
- [x] 4 Learner Persona Profiles
- [x] Personalized Recommendation Engine
- [x] Interactive HTML Dashboard (4 pages)
- [x] Full Research Paper (10 sections, with tables and references)
- [x] Executive Summary (for government/stakeholder presentation


link for dashboard : https://eduprodashboard.netlify.app/
link for summary:https://github.com/Aditibhatia01/Edu-PRO-analysis/blob/main/EduPro_Executive_Summary.docx
