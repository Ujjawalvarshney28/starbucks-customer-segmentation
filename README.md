# Starbucks Customer Segmentation

Customer segmentation of Starbucks Rewards data using PCA and K-Means, used to recommend which offers to send to which customers.

**Tools:** Python, Pandas, NumPy, Matplotlib, Seaborn, scikit-learn, Jupyter

## Problem
Starbucks sends offers (BOGO, discount, informational) to customers through its rewards app. Which kinds of customers actually respond, and how should offers be targeted?

## Data
Starbucks Rewards simulated dataset (Kaggle: "Starbucks Customer Rewards Program"), three files:
- `portfolio.json`: 10 offers
- `profile.json`: 17,000 customers
- `transcript.json`: 306,534 events (offer received, viewed, completed, and transactions)



## Approach
1. **Cleaning:** Removed 2,175 customers with placeholder age (118) and missing gender and income, leaving 14,825. Converted dates, created a membership tenure feature, unpacked the nested event values, and encoded offer channels.
2. **Feature engineering:** Built one row per customer with offer view rate, offer completion rate, total spend, number of transactions, and average transaction.
3. **EDA:** Offer funnel, response by offer type, and spend and completion by age, income and gender.
4. **Preprocessing:** Dropped redundant features after checking a correlation heatmap (for example, completed vs completion rate, r = 0.91). Log-transformed the spending columns and standardized all features.
5. **PCA:** Reduced to the components that explain at least 85% of the variance.
6. **K-Means:** Tried K = 2 to 10 and compared Elbow (WCSS), Silhouette score and Calinski-Harabasz index. Chose **K = 3** (silhouette 0.218, balanced segment sizes, no tiny cluster, BCSS/TSS 0.396).

**Key decision:** Gender was first included in the clustering and produced a segment made up entirely of one small group, with no behavioral meaning. I removed gender from the inputs and used it only to describe the segments afterward.

## Segments
| Segment | Share | Profile | BOGO completion | Discount completion |
|---|---|---|---|---|
| Loyal frequent buyers | 33.0% | Longest tenure, most transactions, small tickets | 65.6% | 80.2% |
| High-value spenders | 37.3% | Highest income, spend and ticket size, fewer visits | 82.6% | 86.4% |
| New and disengaged | 29.7% | Newest members, lowest view rate and spend | 14.2% | 18.0% |

## Recommendations
1. **High-value spenders:** They respond most, but many completions may be purchases that would have happened anyway. Test before increasing reward spend; favor offers that encourage extra visits over deeper discounts.
2. **Loyal frequent buyers:** Discounts outperform BOGO. Use spend thresholds slightly above their usual ticket to lift basket size.
3. **New and disengaged:** Use low-cost onboarding and low-difficulty offers instead of expensive rewards, and check which channels they actually see.
4. **All segments:** Run a holdout test (a group that receives no offer) to measure true offer impact.

## Limitations
- Silhouette is about 0.2, so segments overlap. They describe behavioral tendencies, not sharply separated groups.
- There is no control group, so offer completion does not prove the offer caused the purchase. In several segments, completions exceed views, meaning some customers completed offers without viewing them.
- The data is simulated, and results may not match real Starbucks customers.

## Supply chain angle
Expected redemptions for a campaign = customers in the segment x offers sent x segment completion rate. Estimated per segment, this gives planners a volume forecast to stock and staff for a promotion.

