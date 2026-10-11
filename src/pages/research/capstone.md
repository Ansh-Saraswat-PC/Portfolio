---
title: "Striking Distance: Predictive Scoring for SEO Content Recovery"
description: "An analysis of predictive machine learning vs heuristic rules for identifying decaying SEO content."
date: "2026-10-11"
layout: "../../layouts/MainLayout.astro" # Adjust this to your actual layout path
---

# Striking Distance: Predictive Scoring for SEO Content Recovery

## Abstract
This research investigates whether predictive machine learning can outperform manual heuristic rules in identifying decaying SEO content. We utilized 79 million rows of daily search performance data to compare a manual "volume-to-position" baseline against a Random Forest classifier. By evaluating both on a strict client-grouped split to prevent data leakage, the Random Forest demonstrated significantly higher precision in identifying pages with severe impression-to-click disparities. The output is a prioritized action queue that enables editorial teams to target high-ROI content refreshes efficiently.

## Introduction & Problem Statement
Content teams frequently waste editorial bandwidth rewriting pages that have reached their natural click ceiling, while ignoring high-volume pages trapped on the second page of search results. This project builds a decision-support engine to score and rank "refresh opportunities," replacing manual spreadsheet sorting with a validated predictive model.

## Data
The analysis relies on the `fact_content_daily_performance` table for March 2026, sourced from the FlyRank ML Internship dataset.

*   **Metrics Used:** Google Search Console (`gsc_impressions`, `gsc_clicks`, `gsc_avg_position`) and Google Analytics 4 (`sessions_organic`).
*   **Exclusions:** Rows with zero impressions were excluded to focus strictly on ranking URLs. 
*   **Leakage Prevention:** To prevent data leakage, we avoided proprietary FlyRank health scores or future-window target labels. All data is aggregated to the `content_hash_id` level for the month.

## Methodology
*   **Target Definition:** A binary proxy label for 'Refresh Opportunity' was created (`impressions > median` AND `CTR < 0.01`).
*   **Baseline:** A heuristic rule calculating `impressions / avg_position` to reward high-volume, striking-distance pages.
*   **Model:** A Random Forest Classifier (`max_depth=6`, `n_estimators=100`).
*   **Validation Design:** A `GroupShuffleSplit` on `client_hash_id`. This prevents the model from memorizing domain-specific ranking patterns by ensuring no website appears in both the training and test sets.

## Results
Evaluated on the strictly isolated test set, the Random Forest model outperformed the heuristic baseline. While the baseline blindly prioritized high-impression navigational queries regardless of their click-through potential, the ML model successfully balanced volume against engagement metrics to isolate true underperformers.

*(Insert your `capstone_scatter.png` here)*

## Limitations & Honest Framing
This model provides a **directional, decision-support signal**. It observes past decay but cannot guarantee future algorithmic movement.
*   **Navigational Traps:** It can falsely flag brand-specific queries where a competitor owns the intent, making clicks impossible to win regardless of content quality.
*   **Seasonality:** The model cannot distinguish between broken content and a natural off-season drop in search volume.
*   **SERP Features:** Zero-click anomalies may be caused by Google AI Overviews or Featured Snippets rather than poor content.

## Ranked Recommendations
The playbook maps the model probabilities to human actions:
*   **`PRIORITY_REFRESH` (Prob > 0.70):** Immediate editorial rewrite required to cross the Page 1 threshold.
*   **`REVIEW_METADATA` (Prob 0.50 - 0.70):** Marginal decay; check title tags and meta descriptions.
*   **`MONITOR` (Prob < 0.50):** Standard performance; no action needed.

*(Insert a snippet or table of your `capstone_action_queue.csv` here)*

## Reproducibility
All code, queries, and validation splits used to generate this analysis are publicly available. 
[View the full Jupyter Notebook pipeline on GitHub](https://github.com/Ansh-Saraswat-PC/FlyRank) *(Make sure to link your actual repo)*

---
## Acknowledgments
Built on the FlyRank ML Internship dataset. Data provided by [FlyRank](https://flyrank.ai).