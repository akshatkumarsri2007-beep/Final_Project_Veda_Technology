# Final_Project_Veda_Technology
# E-Commerce Customer Segmentation (RFM Analysis)

Customer segmentation using **RFM (Recency, Frequency, Monetary)** analysis and **K-Means clustering** on e-commerce transaction data, with a business recommendation on which segment to target and with which campaign.

> Internship project at **Veda Technology** by **Akshat Srivastava**

---


## Overview

RFM is a standard technique used by retail and marketing analytics teams to decide who gets a loyalty offer and who gets a win-back campaign. This project takes raw transactional data, cleans it, builds an RFM table, segments customers with K-Means, and converts the clusters into named business segments with a clear marketing recommendation.

## Objective

- Learn an industry-standard segmentation technique end to end.
- Translate a segmentation model into a **business recommendation**, not just a set of clusters.

## Dataset

File: `online_retail_sample.csv` (14,383 rows, 600 customers, Dec 2010 to Dec 2011)

| Column | Description |
|---|---|
| InvoiceNo | Invoice number (starts with `C` for cancellations) |
| StockCode | Product code |
| Description | Product name |
| Quantity | Units purchased |
| InvoiceDate | Date and time of the transaction |
| UnitPrice | Price per unit |
| CustomerID | Unique customer identifier |
| Country | Customer country |


## Project Structure

```
.
├── online_retail_sample.csv        # Input dataset
├── rfm_analysis.py                 # Full analysis script (all steps)
├── rfm_scores.csv                  # Output: RFM table + scores + segments for every customer
├── segment_summary.csv             # Output: segment-level summary
├── elbow_silhouette.png            # Chart: choosing K
├── segments_scatter.png            # Chart: cluster visualization
├── segment_sizes.png               # Chart: customers per segment
├── revenue_share.png               # Chart: revenue share per segment
├── RFM_Customer_Segmentation_Report.pdf   # Final project report
└── README.md
```

## Methodology

1. **Data loading & exploration**: shape, data types, missing values, summary statistics.
2. **Data cleaning**
   - Removed rows with missing `CustomerID` (guest checkouts)
   - Removed cancelled orders (`InvoiceNo` starting with `C`)
   - Removed rows with `Quantity <= 0` or `UnitPrice <= 0` and duplicates
   - Created `TotalPrice = Quantity x UnitPrice`
3. **RFM table**: using snapshot date = last transaction date + 1 day
   - **Recency**: days since last purchase
   - **Frequency**: number of unique invoices
   - **Monetary**: total spend
4. **RFM scoring**: quintile scores (1 to 5) for each metric, combined into `RFM_Score` (e.g. `555` = best customer).
5. **Rule-based segments**: baseline business segments from R and F scores.
6. **K-Means clustering**: log transform + standard scaling, K chosen with the Elbow method and Silhouette score (K = 4).
7. **Cluster naming**: clusters named from their average R/F/M profile (not "Cluster 0/1/2").
8. **Visualization**: scatter plot, segment size, revenue share.
9. **Export**: `rfm_scores.csv` and `segment_summary.csv` for Excel / Power BI / Tableau.

## Results

| Segment | Customers | Avg Recency (days) | Avg Orders | Avg Spend | Revenue % |
|---|---|---|---|---|---|
| Champions | 131 | 7.5 | 15.1 | 2,539 | **68.9%** |
| At-Risk | 241 | 116.9 | 6.4 | 538 | 26.9% |
| New Customers | 92 | 32.7 | 1.4 | 85 | 1.6% |
| Lost | 136 | 288.6 | 1.5 | 91 | 2.6% |

Silhouette score for K = 4: **0.45**

![Customer Segments](segments_scatter.png)

<p float="left">
  <img src="segment_sizes.png" width="48%" />
  <img src="revenue_share.png" width="38%" />
</p>

**Key insights**
- About 22% of customers (Champions) generate nearly 69% of revenue.
- At-Risk customers still account for ~27% of revenue but have been inactive for ~117 days.
- Lost customers contribute under 3% of revenue.

## Business Recommendation

**Target segment: At-Risk** (241 customers, 26.9% of revenue)
**Campaign: Win-back** with a personalised "We miss you" email/SMS, a 10-15% discount or free shipping, valid for 7 days.

**Why:** these customers already proved their loyalty (about 6 orders each) and are slipping but not yet gone, so win-back chances are much higher than for Lost customers, at a lower cost than acquiring new ones.

| Segment | Priority | Action |
|---|---|---|
| At-Risk | High | Win-back offer, personalised product picks |
| Champions | High (retain) | Loyalty rewards, early access, referral programme |
| New Customers | Medium | Welcome series, second-purchase incentive |
| Lost | Low | One cheap reactivation email, no further budget |

Full details are in `RFM_Customer_Segmentation_Report.pdf`.

## How to Run

```bash
# 1. Clone the repository
git clone https://github.com/<your-username>/<your-repo-name>.git
cd <your-repo-name>

# 2. Install dependencies
pip install pandas numpy matplotlib seaborn scikit-learn

# 3. Run the analysis
python rfm_analysis.py
```

Outputs (`rfm_scores.csv`, `segment_summary.csv` and the PNG charts) are saved in the same folder.

To use the real UCI dataset, download it, save it as CSV and change the file name in the `pd.read_csv(...)` line of `rfm_analysis.py`. If your file uses the original Excel format, use `pd.read_excel(...)` instead.

## Future Improvements

- Add product-mix and profit-margin data to the segmentation.
- Try other clustering methods (e.g. Gaussian Mixture, hierarchical).
- Build an interactive Power BI / Tableau dashboard from `rfm_scores.csv`.
- Run an A/B test of the win-back campaign to measure incremental revenue.
