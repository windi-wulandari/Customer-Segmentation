# **Problem Statement**

In an increasingly competitive retail business era, companies face significant challenges in understanding and meeting diverse customer needs. Data shows that the company has a broad customer base with varying demographic characteristics and purchasing behaviors, but the marketing strategies implemented are still general and not personalized. This is reflected in the suboptimal marketing campaign response rates, as seen from historical campaign data. Additionally, the company also faces difficulties in optimizing marketing resource allocation due to a lack of deep understanding of different customer segments. This situation potentially leads to inefficiencies in marketing expenditure and lost opportunities to increase customer engagement and sales.

---

# **Problem Formulation**

1. How to identify and group customers based on their characteristics and purchasing behavior?
2. What are the unique characteristics of each customer segment that can be utilized for marketing strategies?
3. How to optimize marketing campaign strategies based on customer segmentation?

---

# **Project Objectives**

1. Develop a customer segmentation model using clustering methods to identify groups of customers with similar characteristics
2. Analyze the characteristics and purchasing patterns of each customer segment
3. Formulate personalized marketing strategy recommendations for each segment

---

# **Data Dictionary**


**Original Dataset**

| #  | Column               | Dtype   | Description                                                                |
|----|----------------------|---------|---------------------------------------------------------------------------|
| 0  | `ID`                 | int64   | Unique ID for each customer.                                             |
| 1  | `Year_Birth`         | int64   | Year of birth of the customer.                                          |
| 2  | `Education`          | object  | Customer's education level (e.g., Graduate, PhD, etc.).                |
| 3  | `Marital_Status`     | object  | Customer's marital status (e.g., Single, Married, Divorced, etc.).     |
| 4  | `Income`             | float64 | Customer's annual income. Some values may be missing (NaN).            |
| 5  | `Kidhome`            | int64   | Number of small children (under 12 years old) in the customer's home.   |
| 6  | `Teenhome`           | int64   | Number of teenagers (ages 12-18) in the customer's home.                |
| 7  | `Dt_Customer`        | object  | Date when the customer started registering. Format: date.               |
| 8  | `Recency`            | int64   | Number of days since the last interaction with the customer.            |
| 9  | `MntWines`           | int64   | Total money spent on wine products in the last two years.              |
| 10 | `MntFruits`          | int64   | Total money spent on fruit products in the last two years.             |
| 11 | `MntMeatProducts`    | int64   | Total money spent on meat products in the last two years.              |
| 12 | `MntFishProducts`    | int64   | Total money spent on fish products in the last two years.              |
| 13 | `MntSweetProducts`   | int64   | Total money spent on sweet products in the last two years.             |
| 14 | `MntGoldProds`       | int64   | Total money spent on gold products in the last two years.              |
| 15 | `NumDealsPurchases`  | int64   | Number of purchases made through discount offers.                      |
| 16 | `NumWebPurchases`    | int64   | Number of purchases made through the website.                           |
| 17 | `NumCatalogPurchases`| int64   | Number of purchases made through catalogs.                              |
| 18 | `NumStorePurchases`  | int64   | Number of purchases made in physical stores.                            |
| 19 | `NumWebVisitsMonth`  | int64   | Number of visits to the website in the last month.                     |
| 20 | `AcceptedCmp3`       | int64   | Did the customer accept the 3rd marketing campaign (0 = No, 1 = Yes)? |
| 21 | `AcceptedCmp4`       | int64   | Did the customer accept the 4th marketing campaign (0 = No, 1 = Yes)? |
| 22 | `AcceptedCmp5`       | int64   | Did the customer accept the 5th marketing campaign (0 = No, 1 = Yes)? |
| 23 | `AcceptedCmp1`       | int64   | Did the customer accept the 1st marketing campaign (0 = No, 1 = Yes)? |
| 24 | `AcceptedCmp2`       | int64   | Did the customer accept the 2nd marketing campaign (0 = No, 1 = Yes)? |
| 25 | `Complain`           | int64   | Has the customer ever filed a complaint (0 = No, 1 = Yes)?            |
| 26 | `Z_CostContact`      | int64   | Fixed cost for each contact (constant, always 3).                      |
| 27 | `Z_Revenue`          | int64   | Fixed revenue from each contact (constant, always 11).                 |
| 28 | `Response`           | int64   | Did the customer accept the last marketing campaign (0 = No, 1 = Yes)?|

---

# **Pre-Processing**

1. **Data Cleaning**
   - Removal of null values found in columns to ensure data quality

  <div style="text-align: center;">
    <img src="https://drive.google.com/uc?id=1y-rfSFBzvTP1Bu9tGhYwUi0dSKFRTcXr" alt="Gambar Pertama" />
</div>

  Based on the image above, there are Null Values in the Income column.
   - Removal of duplicate data to avoid bias in analysis
   - Column name changes for consistency and ease of use

2. **Temporal Data Transformation**
   - Conversion of `Dt_Customer` column to datetime format

3. **Feature Engineering**
   - Creation of new features:
     * `Customer_For`: Duration of subscription in days
     * `Age`: Age calculation based on registration year
     * `Spent`: Total spending across all categories
     * `Living_With`: Living status (with partner/others)
     * `Children`: Total children in household
     * `Family_Size`: Family size
     * `Is_Parent`: Parent status
   - Simplification of education categories into three groups

4. **Outlier Handling**
   - Identification and removal of outliers in Income and Age columns

5. **Correlation Analysis**

    <div style="text-align: center;">
    <img src="https://drive.google.com/uc?id=1zsJE21uHUc9mxB1mWhWkG4tkPvhc0oSn" alt="Gambar Kedua" />
</div>

   - Strong correlations found between:
     * `Spent` (total spending) strongly correlates with product categories (`MntWines`, `MntMeatProducts`, `MntFishProducts`, `MntSweetProducts`, `MntFruits`)
     * `Income` shows significant correlation with spending patterns (`MntWines`, `MntMeatProducts`) and total expenditure (`Spent`)
     * `Children` has strong correlation with `Family_Size`, showing logical relationship between number of children and family size

6. **Categorical Encoding**
   - Implementation of Label Encoding for categorical variables
   - Reason: limited number of categories and computational efficiency

7. **Feature Selection**
   ```python
   # Copy dataset for modeling
   ds = data.copy()
   # Remove irrelevant features
   cols_del = ['AcceptedCmp3', 'AcceptedCmp4', 'AcceptedCmp5', 'AcceptedCmp1',
               'AcceptedCmp2', 'Complain', 'Response']
   ds = ds.drop(cols_del, axis=1)
   ```
   - Removal of campaign acceptance and complaint columns to focus on customer characteristics
   - Storage of original data for further analysis

8. **Standardization**
   - Data scale normalization for better modeling performance

9. **Dimension Reduction**
   - PCA implementation with 3 components
   - Purpose: reduce data dimensions while maintaining important information
   - Save transformation results for cluster analysis

These pre-processing steps are designed to produce a clean, structured dataset ready for clustering process, while maintaining result interpretability for business purposes.

---

# **Clustering Model**

Langkah-langkah yang terlibat dalam Pengelompokan:

1. Metode Elbow untuk menentukan jumlah kluster yang akan dibentuk.
2. Pengelompokan melalui Pengelompokan Aglomeratif.
3. Memeriksa kluster yang terbentuk melalui diagram sebar. Terdapat 4 cluster hasil pemodelan.

<div style="text-align: center;">
    <img src="https://drive.google.com/uc?id=1vtMXK2CFUwSxuZs45AXp61dab58Joxdc" alt="Gambar Baru" />
</div>

---

# **Model Evaluation & EDA**

#### **Distribution Of The Clusters**

<div style="text-align: center;">
    <img src="https://drive.google.com/uc?id=14ivDUJeFZ4GsgXqE2DkMBjPPB2ex-q35" alt="Gambar Pertama" />
</div>
<br>
<br>

#### **Cluster's Profile Based on Income and Spending**

<div style="text-align: center;">
    <img src="https://drive.google.com/uc?id=1HYAlH5rHl1VD0nTrJtncpYNXvDznr1kF" alt="Gambar Kedua" />
</div>

The conclusion of this analysis shows that Category 2 and Category 0 have higher average spending, while Category 1 and Category 3 show lower spending. This indicates the need for different marketing strategies to enhance engagement and spending from each customer category.

<br>
<br>

#### **Count of Promotion Accepted**

<div style="text-align: center;">
    <img src="https://drive.google.com/uc?id=1hL4HnuaqlKvUgjbaOWbRkdMfp538CRkR" alt="Gambar Ketiga" />
</div>

Category 0 and Category 2 show higher participation rates in promotions, with averages of 0.32 and 0.74, respectively, while Category 1 and Category 3 tend to have lower participation rates, with averages of 0.09 and 0.12. Mentioning this indicates that Category 0 and Category 2 likely have a greater interest in promotions, whereas Category 1 and Category 3 may require additional marketing strategies to increase their participation.
<br>
<br>

#### **NumWebPurchases, NumCatalogPurchases, NumStorePurchases, NumWebVisitsMonth Density**

<div style="text-align: center;">
    <img src="https://drive.google.com/uc?id=1fArQvt13uKFLWX-6dZNIts0DSR3j3aj6" alt="Gambar Keempat" />
</div>

Online Purchases: Categories 0 and 2 are more active in making online purchases, possibly due to better accessibility or more attractive offers. Meanwhile, Categories 1 and 3 need to identify strategies to enhance the user experience and encourage more purchases through their web platform.

Catalog Purchases: Category 2 is more active in making purchases from catalogs, possibly due to more appealing products or better accessibility to product information. Category 0 also shows significant interest, while Categories 1 and 3 need more effective strategies to boost catalog purchases.

In-Store Purchases: Categories 2 and 0 are more active in making purchases at physical stores, reflecting a preference for the in-store shopping experience. Categories 3 and 1 need better strategies to attract consumers to make purchases in stores.

Monthly Website Visits: Categories 1 and 3 are more active in visiting the website, possibly due to a higher interest in online offers. Category 0 also shows a good level of visits, while Category 2 needs more engaging marketing strategies to increase website engagement.

Each category can enhance their marketing strategies based on these findings to better attract and meet consumer needs across various purchasing platforms.

---

**Note: Modeling, Model Evaluation, visualization, and other detailed findings related to customer segmentation clustering can be accessed in the [customer_segmentation_clustering]([LINK](https://github.com/windi-wulandari/Customer-Segmentation/blob/main/customer_segmentation_clustering.ipynb)) Notebook.**

---

# **FINDINGS & RECOMMENDATIONS**

**Comprehensive Table of Findings and Strategic Recommendations**

| Aspect | Findings | Strategic Recommendations | KPI |
|-------|---------|----------------------|-----|
| **Category 0 - "Active Established Family"** |
| Demographic Profile | • Medium income (€61,686)<br>• Large family (2.84 members)<br>• Average age 47.97 years | • Develop "Family Rewards" program with progressive benefits<br>• Create family product bundling packages<br>• Offer volume discounts for bulk purchases | • 15% basket size increase<br>• 85% retention rate<br>• 40% family program adoption |
| Shopping Behavior | • Active in promotions<br>• High offline purchases<br>• Responsive to discounts | • Implement cross-channel rewards program<br>• Create exclusive shopping hours for members<br>• Develop family referral program | • 25% referral conversion<br>• 30% cross-channel purchase<br>• 40% promotion response rate |
| Channel Preference | • High in store purchases (8.19)<br>• Moderate web visits (5.57)<br>• Active in catalog | • Optimize omnichannel experience<br>• Integrate offline-online rewards<br>• Personalize catalog per family | • 50% omnichannel engagement<br>• 35% catalog response rate |
| **Category 1 - "Young Professional"** |
| Demographic Profile | • Medium income (€45,070)<br>• Youngest age (37.76)<br>• Small family | • Launch "Smart Shopper" program for millennials<br>• Develop subscription model<br>• Offer flexible payment options | • 30% subscription adoption<br>• +25% young member acquisition<br>• 60% digital payment adoption |
| Shopping Behavior | • Low spending (€95.08)<br>• Active on web (6.74 visits)<br>• Small but frequent purchases | • Implement gamification rewards<br>• Create mobile-first shopping experience<br>• Develop micro-reward system | • +40% app engagement<br>• +30% purchase frequency<br>• +25% average order value |
| Channel Preference | • Dominant in digital<br>• Low in catalog<br>• Moderate in store | • Optimize mobile app UX<br>• Implement social commerce<br>• Create digital-first loyalty program | • +35% mobile conversion<br>• +40% social commerce revenue |
| **Category 2 - "Premium Shopper"** |
| Demographic Profile | • Highest income (€75,158)<br>• Small family (1.60)<br>• Mature age (45.94) | • Launch "Premium Club" membership<br>• Create concierge shopping service<br>• Offer exclusive product access | • 45% premium membership adoption<br>• 30% luxury segment growth<br>• +40% customer lifetime value |
| Shopping Behavior | • Highest spending (€1,370)<br>• High premium purchases<br>• Brand loyal | • Develop VIP events program<br>• Create personalized shopping experience<br>• Implement recognition program | • 60% VIP event attendance<br>• 50% premium product adoption<br>• 90% satisfaction score |
| Channel Preference | • Balanced offline-online<br>• Active in premium catalog<br>• Moderate web usage | • Create integrated luxury experience<br>• Develop digital concierge<br>• Personal shopper service | • 40% luxury service adoption<br>• 55% premium channel engagement |
| **Category 3 - "Mature Family"** |
| Demographic Profile | • Lowest income (€30,196)<br>• Largest family (3.44)<br>• Highest age (49.26) | • Develop "Value Family Program"<br>• Create education series<br>• Implement community rewards | • 35% program adoption<br>• 40% community engagement<br>• +30% value perception score |
| Shopping Behavior | • Low spending (€174.72)<br>• Price sensitive<br>• Focus on value | • Implement smart budgeting tools<br>• Create value bundle packages<br>• Develop price alert system | • 40% value bundle adoption<br>• 35% smart shopping tool usage<br>• +20% savings per transaction |
| Channel Preference | • Moderate in all channels<br>• High web visits (5.80)<br>• Low in catalog | • Optimize multi-channel value communication<br>• Create digital saving tools<br>• Implement easy compare feature | • 45% digital tool adoption<br>• +30% value discovery rate |

**Monitoring and Evaluation**

**Main KPIs:**
  1. Customer Lifetime Value (CLV)
  2. Net Promoter Score (NPS)
  3. Customer Satisfaction Score (CSAT)
  4. Channel Engagement Rate
  5. Program Adoption Rate
  6. Average Order Value (AOV)
  7. Purchase Frequency
  8. Retention Rate


---

# **Conclusion**

| **No** | **Problem Statement** | **Action** | **Result** | **Success** |
|--------|----------------------|------------|------------|-------------|
| 1 | How to identify and group customers based on their characteristics and purchasing behavior? | Develop customer segmentation model using clustering methods to identify groups of customers with similar characteristics | Successfully developed model with 4 clusters | ✅ |
| 2 | What are the unique characteristics of each customer segment that can be leveraged for marketing strategy? | Analyze characteristics and purchasing patterns of each customer segment | Obtained insights related to customer characteristics for each cluster | ✅ |
| 3 | How to optimize marketing campaign strategies based on customer segmentation? | Formulate personalized marketing strategy recommendations for each segment | Formulated recommendations for future campaign success | ✅ |

---

# **Limitation**

Business simulation is still conducted with a high-level approach due to several required variables not being available. The assumptions used may not accurately represent the business domain. Therefore, it is important to focus on metric calculations, especially business metrics, as the main priority for upcoming campaigns.

---
