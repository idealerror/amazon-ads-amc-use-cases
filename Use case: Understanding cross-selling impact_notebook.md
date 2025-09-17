## Metadata

**id:** 6cee57958ceddf2cccb8543d04c301e3a3deb8ce2f89b7e96eb4e3e7a3a1af0c
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Measure advertising's impact by analyzing purchase patterns across ASIN groups to identify high-performing product combinations (ASI required).
**tags:** ['Paid features']
**use_case_categories:** ['ASIN analysis', 'Overlap', 'Performance deep dive']
**advertiser_types:** ['On-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## 1. Introduction 

### 1.1 Purpose 

This IQ helps advertisers understand how their campaigns influences purchase patterns across different ASIN groups (brands or product categories). For multi-brand/category campaigns, it can be used to reveal cross-selling behavior patterns between different brands or categories. For single brand/category campaigns, it helps measure the brand halo effect by showing how ads for one product drive sales across the broader portfolio. 

By analyzing purchase patterns, advertisers can identify which product combinations are mostly common among purchasers, enabling advertiser to enhance marketing strategy and maximize revenue through targeted cross-selling initiatives. You can customize ASIN groups to examine cross-selling behaviors across brands or between product categories within brands.

**Note:** This IQ uses purchase data from `conversions_all` table (paid feature) to track cross-selling behavior. Only ASIN conversions are analyzed; other conversion types (e.g., offline conversions) are not considered  

### 1.2 Requirements

To use this query, advertisers need:

* Relevant campaign data (Amazon DSP and/or sponsored ads) in a single AMC instance.
* **Enrollment (trial or subscription) to Amazon Insights** is required to have access to the table `conversions_all`. Paid Features are only available in certain regions.

## 2. Data query instructions 

### 2.1 Data returned

This IQ provides two queries: 

* Ad-influenced cross-selling analysis: It returns the campaign group, unique reach, a count of unique users that purchased, and total purchases per each purchased product group combination.
* Cross-selling analysis:  It returns per each purchased product group combination a count of unique users that purchased, and total purchases. 

### 2.2 Tables used 

* [dsp_impressions](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/dsp_impressions): This table provides DSP traffic data, capturing `user_id` to identify exposed audiences segmented by `campaign_id_string`.
* [sponsored_ads_traffic](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/sponsored_ads_traffic): This table provides sponsored ads traffic data, capturing `user_id` to identify exposed audiences segmented by `campaign_id_string`.
* [conversions_all](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/conversions_all_paid): This table is used to track all ASIN conversions from the exposed audience during the analysis period, enabling measurement of cross-selling behavior and brand halo effects.

## 3. Data interpretation instructions 

### 3.1 Example query results

**This data is for instructional purposes only. Your results will differ.**

Query 1: Ad-influenced cross-selling analysis

| campaign_group            | reach  | purchase_category_combination | dist_purchaser | purchases |   |   |   |   |
|---------------------------|--------|-------------------------------|----------------|-----------|---|---|---|---|
| Campaign_Outdoor_category | 60000  |                               |                |           |   |   |   |   |
| Campaign_Outdoor_category | 60000  | [Outdoor]                     | 200            | 220       |   |   |   |   |
| Campaign_Outdoor_category | 60000  | [Running]                     | 100            | 140       |   |   |   |   |
| Campaign_Outdoor_category | 60000  | [Running, Outdoor]            | 60             | 80        |   |   |   |   |
| Campaign_Running_category | 200000 |                               |                |           |   |   |   |   |
| Campaign_Running_category | 200000 | [Outdoor]                     | 200            | 200       |   |   |   |   |
| Campaign_Running_category | 200000 | [Running]                     | 1600           | 1800      |   |   |   |   |
| Campaign_Running_category | 200000 | [Running, Outdoor]            | 100            | 300       |   |   |   |   |


Query 2: Cross-selling analysis

| purchase_category_combination | dist_purchaser | purchases |
|-------------------------------|----------------|-----------|
| [Outdoor]                     | 1800           | 2000      |
| [Running]                     | 2000           | 3400      |
| [Running, Outdoor]            | 200            | 400       |


### 3.2 Metrics and dimensions

| metric/dimension             | definition                                                                                                                           |
|------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| `campaign_group`               | Advertiser-defined campaign groups, typically categorized by advertising product category or brand (for multi-brand campaigns).      |
| `reach`                        | Number of unique users exposed to the campaign                                                                                       |
| `product_category_combination` | Combination of purchased product category.                                                                                           |
| `dist_purchaser`               | Number of unique users who purchased at least one time.                                                                              |
| `purchases`                    | Number of times any amount of a promoted product or products are included in a purchase event. Sum of purchases from dist_purchaser. |


### 3.3 Insights and data interpretation

**How does advertising in a given category impact purchase behavior in other categories?** 

* The Outdoor category campaign (Campaign_Outdoor_category) not only drove sales in its own category but also substantially impacted the Running category. With a reach of 60K users, it generated a 0.4% (=(200+60)/60K) purchase rate for Outdoor products and a 0.3%  (=(100+60)/60K) purchase rate for Running products.

* **Sample Recommendation:** Consider creating bundled promotions or cross-category campaigns that feature both Outdoor and Running products. This could capitalize on the observed cross-category purchase behavior and potentially increase overall sales. 

**What is the area of opportunity to build on existing cross-selling activity?**

* The Outdoor category campaign demonstrated a strong cross-selling rate of 17%, with 60 out of 360 distinct purchasers buying products from both Outdoor and Running categories.

* **Sample Recommendation:** Develop follow-up campaigns for customers who purchased Outdoor products, promoting complementary Running items. This could further boost the cross-selling rate and increase customer lifetime value.

**Does advertising drive cross-selling behaviors?**

* Yes, advertising shows a substantial impact on cross-selling. Purchasers exposed to outdoor campaigns demonstrated a 17% cross-selling rate, while those exposed to running campaigns showed a 5.3% rate. Both are notably higher than the overall cross-selling rate of 5% (=200/(200+1800+2000)) among non-ad-exposed customers. 

* **Sample Recommendation:** Maintain and expand category campaign investments, particularly in outdoor category where cross-selling performance is strongest.

## 4. Queries

### 4.1 Exploratory query

#### 4.1.1 Exploratory query for campaigns 


```
-- Instructional Query: Exploratory Query for Ad influenced cross-selling analysis (campaign exploration) -- 
-- AD PRODUCTS: Amazon DSP and/or sponsored ads

SELECT
  advertiser_id,
  'DSP' as ad_product_type, 
  campaign_id_string,
  campaign,
  campaign_start_date,
  campaign_end_date,
  SUM(impressions) AS total_impressions
FROM
  dsp_impressions
GROUP BY
  1,2,3,4,5,6

UNION ALL 

SELECT
  advertiser,
  ad_product_type, 
  campaign_id_string,
  campaign,
  campaign_start_date,
  campaign_end_date,
  SUM(impressions) AS total_impressions
FROM
  sponsored_ads_traffic
GROUP BY
  1,2,3,4,5,6

```

### 4.2.1 Ad-influenced cross-selling analysis (ASIN Conversions) 


```
-- Instructional Query: Ad-influenced cross-selling analysis (ASIN conversions) -- 
-- Enrollment (trial or subscription) in Amazon Insights is required. -- 

/* 
 ------- Customization Instructions -------
 -- REQUIRED UPDATE: 
 1) ASIN grouping: add ASIN, category in asin_category 
 2) campaign grouping:
 Add the grouping for campaigns below. 
 For DSP, add campaign_id_string and campaign_group. 
 For Sponsored Ad product, add campaign name and group. 
 DSP campaigns with no groupings will be tagged as "DSP-Others", SP campaigns will be tagged as "SP-Others" and  SD campaigns will be tagged as "SD-Others" and SB campaigns will be tagged as "SB-Others"
 */

WITH asin_category (asin, category) AS ( 
    -- REQUIRED UPDATE: Define ASIN Grouping
    VALUES  
    ('B00000000C','Category1')
    ,('B00000000D','Category1')
    ,('B00000000E','Category1')
    ,('B00000000F','Category1')
    ,('B00000001C','Category2')
    ,('B00000001D','Category2')
    ,('B00000001E','Category2')
    ,('B00000001F','Category2')
  
), 

dsp_campaign_group (campaign_id_string, campaign_group) AS (
    -- REQUIRED UPDATE: Define DSP campaign Grouping. Leave it blank if no DSP campaign. 
    VALUES
    ('500000000000000001','Category1')
    ,('500000000000000002','Category1')
    ,('500000000000000003','Category1')
    ,('500000000000000004','Category1')
    ,('500000000000000005','Category2')
    ,('500000000000000006','Category2')
    ,('500000000000000007','Category2')
),
sa_campaign_group (campaign_id_string, campaign_group) AS (
    -- REQUIRED UPDATE: Define Sponsored Ads campaign Grouping. Leave it blank if no Sponsored Ads campaign. 
    VALUES
    ('','')
),
user_exposed AS (
    -- Identify user exposed to campaigns 
    SELECT
        user_id,
        COALESCE(d.campaign_group, 'DSP-Others') AS campaign_group,
        SUM(impressions) AS impressions,
        SUM(total_cost) / 1e5 AS total_cost
    FROM
        dsp_impressions i  
    INNER JOIN dsp_campaign_group d on d.campaign_id_string = i.campaign_id_string
    WHERE
        user_id IS NOT NULL
        AND impressions >0 
    GROUP BY
    1,2

    UNION ALL

    SELECT
        user_id,
        COALESCE(sp.campaign_group, concat(ad_product_type,'-Others')) AS campaign_group,
        SUM(impressions) AS impressions,
        SUM(spend) / 1e8 AS total_cost
    FROM
        sponsored_ads_traffic a  
    INNER JOIN sa_campaign_group sp on sp.campaign_id_string = a.campaign_id_string 
    WHERE
        user_id IS NOT NULL
        AND ad_product_type is not null
        AND impressions >0 
    GROUP BY
    1,2
),
purchase_customer AS (
    -- Identify user exposed to campaigns who made a purchase 
    SELECT 
        a.user_id
        , a.event_dt_utc 
        , a.tracked_asin
        , b.category
        , SUM(a.conversions ) AS purchases  -- Sales metric
    FROM conversions_all as a 
    INNER JOIN asin_category as b 
    ON a.tracked_asin = b.asin
    WHERE total_product_sales >0 
    AND a.user_id in ( SELECT DISTINCT user_id FROM user_exposed)
    AND a.event_subtype IN ('order')    -- Feel free to choose your conversion types 
    GROUP BY 1,2,3,4
    ORDER BY 1,2 
),
purchase_customer_ordered AS (
    SELECT 
        user_id
        , category
        , MIN(event_dt_utc) AS event_dt_category_first -- earliest purchase date per each user
        , SUM(purchases) AS purchases  
    FROM purchase_customer
    GROUP BY 1,2
),    

purchase_customer_ordered_sorted AS (
    -- Identify purchased product category combination
    SELECT 
        user_id
        , ARRAY_SORT(COLLECT(DISTINCT category)) AS path 
        , SUM(purchases) AS purchases  
    FROM purchase_customer_ordered
    GROUP BY 1
), 
campaign_agg AS (
    SELECT 
        campaign_group
        , user_id
    FROM user_exposed
    GROUP BY 1, 2 
), 
together AS (
    SELECT 
        c.user_id  
        , c.campaign_group
        , p.user_id as pur_user_id 
        , p.path 
        , p.purchases 
        
    FROM campaign_agg AS c  
    LEFT JOIN purchase_customer_ordered_sorted AS p 
    ON c.user_id = p.user_id  
    WHERE c.campaign_group IS NOT NULL
    GROUP BY 1,2,3,4,5 
), 
reach AS (
    SELECT 
        campaign_group
        , count(distinct user_id) AS reach 
    FROM together 
    WHERE campaign_group IS NOT NULL
    GROUP BY 1
)
SELECT 
    a.campaign_group
    , b.reach 
    , a.path AS purchase_category_combination 
    , count(distinct a.pur_user_id) AS dist_purchaser
    , sum(a.purchases) AS purchases
FROM together AS a 
LEFT JOIN reach AS b 
on a.campaign_group = b.campaign_group
GROUP BY 1,2,3
```

### 4.2.2 Cross-selling analysis (ASIN Conversions)


```
-- Instructional Query: <<IQ Name>>(PIXEL conversions) -- 
-- Enrollment (trial or subscription) in Amazon Insights is required. -- 

/* 
 ------- Customization Instructions -------
 -- REQUIRED UPDATE: 
 1) ASIN grouping: add ASIN, category in asin_category 
*/

WITH asin_category (asin, category) AS (
    -- REQUIRED UPDATE: Define ASIN Grouping
    VALUES  
    ('B00000000C','Category1')
    ,('B00000000D','Category1')
    ,('B00000000E','Category1')
    ,('B00000000F','Category1')
    ,('B00000001C','Category2')
    ,('B00000001D','Category2')
    ,('B00000001E','Category2')
    ,('B00000001F','Category2') 
), 

purchase_customer AS (
    -- identify tracked ASIN purchasers (ad-exposed or non ad-exposed) 
    SELECT 
        a.user_id
        , a.event_dt_utc 
        , a.tracked_asin
        , b.category
        , SUM(a.conversions ) AS purchases  

    FROM conversions_all as a 

    INNER JOIN asin_category as b 
    ON a.tracked_asin = b.asin

    WHERE 
        total_product_sales >0 
        AND a.event_subtype IN ('order')  
        AND a.user_id IS NOT NULL 
        GROUP BY 1,2,3,4
        ORDER BY 1,2 
),

purchase_customer_ordered AS (
    SELECT 
        user_id
        , category
        , MIN(event_dt_utc) AS event_dt_category_first  
        , SUM(purchases) AS purchases  
    FROM purchase_customer
    GROUP BY 1,2
),    

purchase_customer_ordered_sorted AS (
    -- Identify purchased product category combination
    SELECT 
        user_id
        , ARRAY_SORT(COLLECT(DISTINCT category)) AS path  
        , SUM(purchases) AS purchases  
    FROM purchase_customer_ordered
    GROUP BY 1
)

SELECT 
    path AS purchase_category_combination
    , count(distinct user_id) as dist_purchaser
    , SUM(purchases) AS purchases
FROM purchase_customer_ordered_sorted 
WHERE path IS NOT NULL
GROUP BY 1 
```
