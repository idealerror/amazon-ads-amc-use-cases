## Metadata

**id:** 5497797b830054c67a5b53206ffe1b484b1ee7598726c2e0f9dc3b4887219810
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Analyze how off-Amazon conversions are influenced by Amazon Ads exposure across multiple channels.
**tags:** ['Advertiser data upload']
**use_case_categories:** ['Path-to-conversion']
**advertiser_types:** ['Off-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## 1. Introduction

### 1.1 Summary

This instructional query (IQ) measures the impact of advertising by quantifying sales derived off-Amazon from ad product exposure. This IQ seeks to answer questions such as: 

* Which ad products are most effective in driving conversions? 
* What is the cost per customer acquisition? 

### 1.2 Purpose

Amazon Ads is an omni-channel partner with the ability to reach customers at multiple touch points in their shopping journey. Leveraging Amazon Marketing Cloud (AMC), advertisers can upload off-Amazon transactions into AMC and analyze how exposure to one or more Amazon Ads channels influenced off-Amazon conversion activity.

This query captures all touch points by ad product for each customer’s initial ad-attributed purchase, and their subsequent ad-attributed spend within the queried time period. This query does not consider the order of ad product exposure and does not consider the impression frequency of each product.

This visual below is an example of how the data can be interpreted and illustrated.

![off-amazon conversions path](https://m.media-amazon.com/images/G/01/adsamciql/off-amazon-exposure-path.png)

There are two queries:

* **Section 4.2.1** - Highlights Amazon DSP campaigns. Sponsored ads impressions and conversions are included in the analysis by default to avoid users having to manually include tens to hundreds of campaign names. 
* **Section 4.2.2** - Includes sponsored ads campaigns only. Similar to query template 4.2.1, all sponsored ads campaigns are included by default to avoid users having to manually include tens to hundreds of campaign names.

### 1.3 Requirements

* To use this query, you must have uploaded first-party (1P) conversion events to AMC. You can use 1P inputs in conjunction with Amazon Ads events for a variety of measurement and audience use cases. Refer to [Advertiser Data Upload API documentation](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/advertiser-data-upload/advertiser-data-overview) or [AMC Uploader from AWS](https://aws.amazon.com/solutions/implementations/amazon-marketing-cloud-uploader-from-aws/) for more information.
* You ran Amazon DSP or sponsored ads campaigns over the time period queried. 

## 2. Data query instructions

### 2.1 Data returned

The query will return performance metrics per ad product exposure, including unique customers reached, impressions, impressions cost, off-Amazon converters, and sales. Use the query in section 4.2.1 to highlight Amazon DSP campaigns; you have the ability to customize Ad Products by grouping campaigns. For both queries (section 4.2.1 and 4.2.2), sponsored ads are included by default, requiring no campaign name or ID inputs.

### 2.2 Tables used

* **first_party_conversions_table**: This is a placeholder table name; replace this table name with the name of the conversions table you have uploaded into AMC
    * Refer to [IQ: Introduction to using first-party data in AMC](/marketing-cloud/instructional-queries/f301989fa6543b1b32301fff3b361b45f7e4507d9e214218f81fc187edc10e6b) to learn more about how you can use first-party inputs within AMC.
* [dsp_impressions](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/dsp_impressions): This table provides traffic data for Amazon DSP campaigns.
* [sponsored_ads_traffic](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/sponsored_ads_traffic): This table provides traffic data for sponsored ads campaigns.

## 3. Data interpretation instructions

### 3.1 Example query results

This synthetic data is for instructional purposes only. Your results will differ.



| exposure                  | audience_reach | impressions | total_cost | total_users_that_purchased | conversion_rate | total_off_amazon_ad_attributed_sales |
|---------------------------|----------------|-------------|------------|----------------------------|-----------------|--------------------------------------|
| [DSP, sponsored_ads, PVa] | 63,435         | 768,008     | 4,750      | 1,269                      | 2.00%           | 69,779                               |
| [OLV, sponsored_ads]      | 616,089        | 16,342,273  | 64,566     | 9,241                      | 1.50%           | 508,273                              |
| [PVa]                     | 4,862,124      | 24,607,533  | 97,910     | 24,311                     | 0.50%           | 1,337,084                            |



### 3.2 Metrics defined

| metric                               | Definition                                                                                                                                                                                                               |
|--------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `exposure`                             | Ad product exposure, agnostic of exposure order. Each exposure is mutually exclusive.                                                                                                                                    |
| `audience_reach`                       | Number of distinct users reached. Each unique user is assigned to one exposure group only. For example, a user exposed to DSP, sponsored ads, and PVa would not be assigned to the exposure group DSP and sponsored ads. |
| `impressions`                          | Total impressions each exposure group received over queried time frame.                                                                                                                                                  |
| `total_users_that_purchased`           | Number of unique shoppers who made at least 1 ad-attributed conversion off-Amazon. Each shopper is mutually exclusive.                                                                                                   |
| `conversion_rate`                      | total_users_that_purchased divided by reach                                                                                                                                                                              |
| `total_off_amazon_ad_attributed_sales` | Total off-amazon ad-attributed sales from total_users_that_purchased                                                                                                                                                     |



**Note:** Impression totals will reflect all impressions served to those who did not purchase, but only pre-purchase impressions served to those who did purchase.

### 3.3 Insights and data interpretation

**Which ad products are most effective at driving total sales off-Amazon?**

Running Prime Video ads, Amazon DSP, and Sponsored Products campaigns together has the highest conversion rate and is, therefore, most impactful; increase investment in the aforementioned products to further improve conversion rates.

**Recommendations:**

Create custom audiences focusing on users that were exposed to both PVa and Sponsored Products only. Remarket these customers with Amazon DSP campaigns to drive conversions as the combination of PVa, Sponsored Products, and DSP has had the most success.

## 4. Queries

### 4.1 Exploratory query

Use the query below to identify the Amazon DSP campaigns for use in the query template in section 4.2.1. 

**Note:** You do not need to identify sponsored ads campaigns, as the query template 4.2.1 and 4.2.2 includes all sponsored ads campaigns by default. This eliminates the need to enter campaign names into the template and reduces the chance for error.



```
/* Instructional Query: Exploratory Query to pull Amazon DSP campaign IDs */
-- AD PRODUCTS: Amazon DSP --
SELECT 
  campaign_id_string,
  campaign,
  campaign_start_date,
  campaign_end_date,
  SUM(impressions) AS total_impressions
FROM 
   dsp_impressions
 GROUP BY
   1,
   2,
   3,
   4
```

### 4.2 Query templates

**4.2.1 Amazon DSP and sponsored ads exposure**

Copy and paste the following query into the Query Editor.

Follow the instructions in the query to adjust attribution window and define Amazon DSP products. 

Replace `first_party_conversions_table` with the name of your first party table. This table will need the following columns:

* `user_id`
* `order_id`
* `sales`
* `date_UTC` (purchase date in UTC)


```

-- Instructional Query: Off-Amazon coversions by ad product exposure --
-- AD PRODUCTS: Amazon DSP and sponsored ads --
/*
 ------- Customization Instructions -------
 1) REQUIRED UPDATE [1 of 4] : Search Amazon DSP Campaign Groupings
 2) REQUIRED UPDATE [2 of 4] : Search Enter Amazon DSP Campaign IDs
 3) REQUIRED UPDATE [3 of 4] : Search Enter Amazon 1P Transaction Table Fields
 4) REQUIRED UPDATE [4 of 4] : Search Enter Amazon 1P Transaction Table
 5) OPTIONAL UPDATE [1 of 1] : Search Enter Desired Attribution
*/


WITH campaign_group (campaign_id_string, campaign_group) AS (
  /* REQUIRED UPDATE [1 of 4] Amazon DSP Campaign Groupings: Define the group of Amazon DSP campaigns that will be included in this analysis. */
  VALUES
    ('58500000000000000', 'STV')
),


dsp_campaigns (campaign_id_string) AS (
  /* REQUIRED UPDATE [2 of 4] Enter Amazon DSP Campaign IDs: Insert campaign ids of defined Amazon DSP campaigns above. */
  VALUES
    ('58500000000000000')
),


-- Grab Amazon DSP and sponsored ads Impressions

impressions AS (
  SELECT
    COALESCE(campaign_group, 'Amazon DSP') AS campaign_group,
    'DSP' AS product_type,
    user_id,
    MIN(impression_dt_utc) AS impression_dt,
    SUM(impressions) AS impressions,
    SUM(total_cost) AS total_cost
  FROM 
    dsp_impressions i 
  INNER JOIN 
    dsp_campaigns dsp ON dsp.campaign_id_string = i.campaign_id_string 
  LEFT JOIN 
    campaign_group G ON g.campaign_id_string = i.campaign_id_string
  WHERE 
    user_id IS NOT NULL
  GROUP BY
    1,
    2,
    3

  UNION ALL

   SELECT
     ad_product_type AS campaign_group,
     'sponsored ads' AS product_type,
     user_id,
     MIN(event_dt_utc) AS impression_dt,
     SUM(impressions) AS impressions,
     SUM(spend) AS total_cost
   FROM 
     sponsored_ads_traffic  
   WHERE 
     user_id IS NOT NULL
   AND
     ad_product_type IS NOT NULL 
   GROUP BY 
     1,
     2,
     3
),


-- Pull in Advertiser off-amazon transactions

off_amazon_purchases_total AS (
   SELECT
  /* REQUIRED UPDATE [3 of 4] Enter Amazon 1P Transaction Table Fields: Insert name of fields that correspond to uploaded table. */
    order_id AS conversion_id,
    date_utc AS purchase_dt,
    user_id,
    sales AS off_amazon_sales
   FROM 
  /* REQUIRED UPDATE [4 of 4] Enter Amazon 1P Transaction Table: Insert name of uploaded 1P transaction table. */
    first_party_conversions_table  
   WHERE  
     sales > 0
   AND 
     user_id IS NOT NULL 
),


-- Determine impressions prior to off-amazon transaction

off_amazon_match_purchase_impressions AS (
   SELECT 
     op.user_id as off_user_id,
     i.user_id as imp_user_id,
     campaign_group,
     impression_dt,     
     purchase_dt,
     conversion_id,
     off_amazon_sales,
     seconds_between(impression_dt,purchase_dt) AS days_between
   FROM 
     off_amazon_purchases_total op
   JOIN 
     impressions i ON op.user_id = i.user_id
   WHERE 
     i.impression_dt < op.purchase_dt
),


-- Determine which transactions occured within desired attribution window

combined_off_amazon_match_purchase_impressions AS (
   SELECT
     imp_user_id, 
     impression_dt,
     campaign_group,     
     purchase_dt,
     conversion_id,
     off_amazon_sales,
     ROW_NUMBER() OVER (
        PARTITION BY imp_user_id, conversion_id
        ORDER BY impression_dt DESC) AS conversion_order
   FROM 
     off_amazon_match_purchase_impressions
   WHERE 
   /* OPTIONAL UPDATE [1 of 1] Enter Desired Attribution: Default is 14 days, if you prefer 30 days, update 14 to 30. */
     days_between <= 60 * 60 * 24 * 14
),


-- Grab all desired attributed off_amazon transactions

ad_attributed_off_amazon_purchases_total AS (
    SELECT
     imp_user_id AS user_id, 
     impression_dt,
     campaign_group,     
     purchase_dt,
     conversion_id,
     off_amazon_sales
    FROM
     combined_off_amazon_match_purchase_impressions a
    WHERE 
     conversion_order = 1
),


--  Create table of first off_amazon transaction

ad_attributed_off_amazon_purchases AS (
    SELECT
     user_id, 
     MIN(purchase_dt) AS purchase_dt,
     SUM(off_amazon_sales) AS off_amazon_sales
    FROM
     ad_attributed_off_amazon_purchases_total
    GROUP BY
     1
),



-- Combine total first ad-attributed transactions with impressions

filter_impressions AS (
  SELECT
    i.user_id AS imp_user_id,
    i.campaign_group,
    i.impressions,
    i.impression_dt,
    i.product_type,
    IF(
      i.product_type = 'DSP',
      (i.total_cost / 1e5),
      (i.total_cost / 1e8)
    ) AS total_cost,
    c.user_id AS pur_user_id,
    c.purchase_dt,
    COALESCE(c.off_amazon_sales, 0) AS off_amazon_sales
  FROM
    impressions i
    LEFT JOIN ad_attributed_off_amazon_purchases c ON c.user_id = i.user_id
  WHERE
    (
      c.user_id IS NOT NULL
      AND i.impression_dt < c.purchase_dt
    )
    OR c.user_id IS NULL
),


-- Order Campaign Groups

ranked AS (
 SELECT
    imp_user_id,
    campaign_group,
      ROW_NUMBER() OVER (
        PARTITION BY f.imp_user_id
        ORDER BY
          campaign_group ASC
      ) AS campaign_order
  FROM
    filter_impressions f
  WHERE
    f.imp_user_id IS NOT NULL
),


-- Create campaign path group by user

assembled AS (
  SELECT
    imp_user_id,
    ARRAY_SORT(COLLECT(DISTINCT a.campaign_group)) AS path
  FROM
    ranked a
  GROUP BY
    1
),


-- Dedupe table conversions to represent each row as each user

filter_impressions_dedupe AS (
  SELECT
    imp_user_id,
    SUM(impressions) AS impressions,
    SUM(total_cost) AS total_cost,
    MAX(off_amazon_sales) AS off_amazon_sales,
    MAX(IF(pur_user_id IS NOT NULL, 1, 0)) AS converted
  FROM
    filter_impressions
  GROUP BY
    1
),


-- Assemble impressions and conversions

assembled_with_imp_conv AS (
  SELECT
    path,
    COUNT(DISTINCT a.imp_user_id) AS reach,
    SUM(b.impressions) AS impressions,
    SUM(b.total_cost) AS total_cost,
    SUM(b.converted) AS total_users_that_purchased,
    SUM(b.off_amazon_sales) AS off_amazon_sales
  FROM
    assembled a
    LEFT JOIN filter_impressions_dedupe b ON a.imp_user_id = b.imp_user_id
  GROUP BY
    1
) 


-- FINAL SELECT
  SELECT
   path AS exposure,
   reach AS audience_reach,
   impressions,
   total_cost,
   total_users_that_purchased,
   total_users_that_purchased / reach AS conversion_rate,
   off_amazon_sales as total_off_amazon_ad_attributed_sales
  FROM
   assembled_with_imp_conv

/* Query end */

```


**4.2.2 Sponsored ads only exposure** 

Copy and paste the following query into the Query Editor.

Follow the instructions in the query to adjust attribution window. 

Replace `first_party_conversions_table` with the name of your first party table. This table will need the following columns:

* `user_id`
* `order_id`
* `sales`
* `date_UTC` (purchase date in UTC)


```

-- Instructional Query: Off-Amazon coversions by ad product exposure --
-- AD PRODUCTS: Sponsored ads --
/*
 ------- Customization Instructions -------
 1) REQUIRED UPDATE [1 of 2] : Search Enter Amazon 1P Transaction Table Fields
 2) REQUIRED UPDATE [2 of 2] : Search Enter Amazon 1P Transaction Table
 3) OPTIONAL UPDATE [1 of 1] : Search Enter Desired Attribution
*/


WITH impressions AS (
   SELECT
     ad_product_type AS campaign_group,
     'sponsored ads' AS product_type,
     user_id,
     MIN(event_dt_utc) AS impression_dt,
     SUM(impressions) AS impressions,
     SUM(spend) AS total_cost
   FROM 
     sponsored_ads_traffic  
   WHERE 
     user_id IS NOT NULL
   AND
     ad_product_type IS NOT NULL 
   GROUP BY 
     1,
     2,
     3
),


-- Pull in Advertiser off-amazon transactions

off_amazon_purchases_total AS (
   SELECT
  /* REQUIRED UPDATE [1 of 2] Enter Amazon 1P Transaction Table Fields: Insert name of fields that correspond to uploaded table. */
    order_id AS conversion_id,
    date_utc AS purchase_dt,
    user_id,
    sales AS off_amazon_sales
   FROM 
  /* REQUIRED UPDATE [4 of 4] Enter Amazon 1P Transaction Table: Insert name of uploaded 1P transaction table. */
    first_party_conversions_table  
   WHERE  
     sales > 0
   AND 
     user_id IS NOT NULL 
),


-- Determine impressions prior to off-amazon transaction

off_amazon_match_purchase_impressions AS (
   SELECT 
     op.user_id as off_user_id,
     i.user_id as imp_user_id,
     campaign_group,
     impression_dt,     
     purchase_dt,
     conversion_id,
     off_amazon_sales,
     seconds_between(impression_dt,purchase_dt) AS days_between
   FROM 
     off_amazon_purchases_total op
   JOIN 
     impressions i ON op.user_id = i.user_id
   WHERE 
     i.impression_dt < op.purchase_dt
),


-- Determine which transactions occured within desired attribution window

combined_off_amazon_match_purchase_impressions AS (
   SELECT
     imp_user_id, 
     impression_dt,
     campaign_group,     
     purchase_dt,
     conversion_id,
     off_amazon_sales,
     ROW_NUMBER() OVER (
        PARTITION BY imp_user_id, conversion_id
        ORDER BY impression_dt DESC) AS conversion_order
   FROM 
     off_amazon_match_purchase_impressions
   WHERE 
   /* OPTIONAL UPDATE [1 of 1] Enter Desired Attribution: Default is 14 days, if you prefer 30 days, update 14 to 30. */
     days_between <= 60 * 60 * 24 * 14
),


-- Grab all desired attributed off_amazon transactions

ad_attributed_off_amazon_purchases_total AS (
    SELECT
     imp_user_id AS user_id, 
     impression_dt,
     campaign_group,     
     purchase_dt,
     conversion_id,
     off_amazon_sales
    FROM
     combined_off_amazon_match_purchase_impressions a
    WHERE 
     conversion_order = 1
),


--  Create table of first off_amazon transaction

ad_attributed_off_amazon_purchases AS (
    SELECT
     user_id, 
     MIN(purchase_dt) AS purchase_dt,
     SUM(off_amazon_sales) AS off_amazon_sales
    FROM
     ad_attributed_off_amazon_purchases_total
    GROUP BY
     1
),



-- Combine total first ad-attributed transactions with impressions

filter_impressions AS (
  SELECT
    i.user_id AS imp_user_id,
    i.campaign_group,
    i.impressions,
    i.impression_dt,
    i.product_type,
    IF(
      i.product_type = 'DSP',
      (i.total_cost / 1e5),
      (i.total_cost / 1e8)
    ) AS total_cost,
    c.user_id AS pur_user_id,
    c.purchase_dt,
    COALESCE(c.off_amazon_sales, 0) AS off_amazon_sales
  FROM
    impressions i
    LEFT JOIN ad_attributed_off_amazon_purchases c ON c.user_id = i.user_id
  WHERE
    (
      c.user_id IS NOT NULL
      AND i.impression_dt < c.purchase_dt
    )
    OR c.user_id IS NULL
),


-- Order Campaign Groups

ranked AS (
 SELECT
    imp_user_id,
    campaign_group,
      ROW_NUMBER() OVER (
        PARTITION BY f.imp_user_id
        ORDER BY
          campaign_group ASC
      ) AS campaign_order
  FROM
    filter_impressions f
  WHERE
    f.imp_user_id IS NOT NULL
),


-- Create campaign path group by user

assembled AS (
  SELECT
    imp_user_id,
    ARRAY_SORT(COLLECT(DISTINCT a.campaign_group)) AS path
  FROM
    ranked a
  GROUP BY
    1
),


-- Dedupe table conversions to represent each row as each user

filter_impressions_dedupe AS (
  SELECT
    imp_user_id,
    SUM(impressions) AS impressions,
    SUM(total_cost) AS total_cost,
    MAX(off_amazon_sales) AS off_amazon_sales,
    MAX(IF(pur_user_id IS NOT NULL, 1, 0)) AS converted
  FROM
    filter_impressions
  GROUP BY
    1
),


-- Assemble impressions and conversions

assembled_with_imp_conv AS (
  SELECT
    path,
    COUNT(DISTINCT a.imp_user_id) AS reach,
    SUM(b.impressions) AS impressions,
    SUM(b.total_cost) AS total_cost,
    SUM(b.converted) AS total_users_that_purchased,
    SUM(b.off_amazon_sales) AS off_amazon_sales
  FROM
    assembled a
    LEFT JOIN filter_impressions_dedupe b ON a.imp_user_id = b.imp_user_id
  GROUP BY
    1
) 


-- FINAL SELECT
  SELECT
   path AS exposure,
   reach AS audience_reach,
   impressions,
   total_cost,
   total_users_that_purchased,
   total_users_that_purchased / reach AS conversion_rate,
   off_amazon_sales as total_off_amazon_ad_attributed_sales
  FROM
   assembled_with_imp_conv

/* Query end */

```

