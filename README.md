# Customer Segmentation

[![Customer Segmentation Workflow](image/showcase.png)](image/showcase.png)

## Table of Contents

- [Project Mission](#project-mission)
- [About the Project](#about-the-project)
- [Dataset](#dataset)
- [Data Cleaning & Feature Engineering](#data-cleaning--feature-engineering)
- [Clustering Approach](#clustering-approach)
- [Results](#results)
- [DBSCAN — Outlier Detection](#dbscan--outlier-detection)
- [Key Insights](#key-insights)

---

## Project Mission

Help businesses move away from one-size-fits-all marketing by grouping customers into distinct, actionable segments based on their real behavior.

## About the Project

This project applies unsupervised machine learning to the Customer Personality dataset to uncover natural customer groupings based on income, spending, family size, and campaign responsiveness — turning raw transactional data into segments a marketing team can act on.

## Dataset

- Customer Personality dataset
- Demographics (age, income, number of children)
- Spending behavior (log-transformed)
- Marketing campaign acceptance
- Website engagement (visits per month)

## Data Cleaning & Feature Engineering

- Removed missing values and outliers
- Log-transformed spending to reduce skew
- Scaled 26 behavioral features with `StandardScaler`
- Engineered a campaign acceptance rate and web engagement metrics

## Clustering Approach

- **K-Means** as the primary segmentation model, chosen for producing clean, interpretable customer personas
- **DBSCAN** used separately as a secondary check to flag customers who don't fit the main groups
- Compared clustering directly on the 26 scaled features vs. clustering after PCA compression to 16 components — clustering with PCA produced poor, overlapping groups, while clustering on the original scaled features preserved more of the true distance structure and produced better-separated segments

[![Clustering Results Without PCA](image/kmean_without_PCA.jpg)](image/kmean_without_PCA.jpg)

## Results

K-Means with **k = 3** produced three well-balanced, well-separated segments (630 / 714 / 868 customers):

[![Cluster Profiles](image/customer_cluster.jpg)](image/customer_cluster.jpg)

| Cluster | Profile |
|---|---|
| 1 — Premium | High income (~$73.6K), highest spending, most responsive to campaigns |
| 0 — Mid-Tier | Moderate income (~$53.7K), balanced spending and engagement |
| 2 — Budget-Conscious | Lower income (~$32.9K), larger families, price-sensitive |

## DBSCAN — Outlier Detection

K-Means groups every customer into a segment, even ones that don't really resemble their neighbors. DBSCAN was applied as a second lens specifically to catch these edge cases — customers whose behavior is dense and consistent get grouped, while anyone who doesn't fit any dense region is flagged as noise instead of forced into a segment.

[![DBSCAN Results](image/DBSCAN.jpg)](image/DBSCAN.jpg)

- Detected 2 dense behavioral clusters and 148 outlier customers
- These 148 outliers are not random noise — they're customers whose combination of income, spending, and engagement doesn't match any common pattern in the data, which is exactly why they get missed inside a fixed K-Means segment
- This matters for the business because outliers cut both ways: some may be very high-value customers with unique buying habits worth a dedicated approach, while others may show erratic or risky behavior worth a closer look
- Practically, this list becomes a manual-review queue — instead of applying one of the three standard segment strategies, the marketing team can look at these customers individually before deciding how to treat them
- DBSCAN is not a replacement for K-Means here; it doesn't produce clean, business-ready personas the way K-Means does, but it adds a safety net the main segmentation alone can't provide

## Key Insights

- Income and spending are the strongest drivers of segment separation
- Premium customers respond to campaigns at nearly 2x the rate of other segments
- Budget-conscious customers have larger families and visit the website more often, despite lower spending
- DBSCAN's outlier group is a useful watchlist for customers who behave atypically

