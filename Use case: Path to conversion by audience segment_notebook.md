## Metadata

**id:** 3b9173ecd3bd86d99e68be74ec2901a00dc9f88013bce0b4693765d90ff40abf
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Identify the most popular paths taken by an audience segment before purchasing products on-Amazon or converting off-Amazon.
**tags:** []
**use_case_categories:** ['Audience measurement', 'Path-to-conversion']
**advertiser_types:** ['On-Amazon conversions', 'Off-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## Introduction

### Summary

Which were the most popular and effective paths taken by a specific audience segment before purchasing your products on-Amazon or converting off-Amazon? Understand the buying journey for a specific audience and how it differs from other audiences. This IQ also includes an off-Amazon version of the query to help advertisers who do not sell on Amazon.com improve their campaign performance for their focus audience by using Events Manager signals.

### Purpose 

With this instructional query, you can identify the most popular and effective paths taken by a specific audience segment before purchasing your products on-Amazon or converting off-Amazon. This IQ helps you understand the buying journey for a specific audience and how it differs from other audiences. 

For example, when running the analysis for the parents of tweens audience segment, you may discover the path with the highest conversion rate is TNF/Twitch followed by SOV and then Amazon DSP. You can use this insight to create an AMC audience to reach users exposed to the first touchpoint (TNF/Twitch) and then the second touchpoint (SOV). This AMC audience can then be used in Amazon DSP remarketing campaigns to increase the number of parents of tweens in the highest-performing path.

The query considers all touch points to determine their path. However, the paths in the final output are based on the timing of the first impression of the campaign group for simplicity (see image below). 

![Path to conversion audience segment](https://m.media-amazon.com/images/G/01/adsamciql/path_purchase_aud_seg_reduced_4.png)

The example above illustrates a single user in the chosen audience segment being exposed to multiple impressions from different campaign groups throughout their path to purchase. In this case, the `path` column in the final output will be [[1, 'SOV'], [2, 'TNF/Twitch'], [3, 'DSP']]. 

### Which columns are deduplicated?

The 'path' column is deduplicated for simplicity. The 'cost' and 'impression' columns in the output are not deduplicated to ensure we fairly track the actual performance and cost associated with the campaigns.

* When there are multiple exposures to each ad type, the 'path' column will indicate the timing and order of the first impression by each campaign group
* Campaign groups with impressions served after the purchase are not included in the `path` column
* The `impression` and `cost` column will include the impressions and cost of all the campaign groups served before the purchase. For example, despite the example `path` column showing only 3 impression touchpoints, the impression count for the user will be 8, and will contain the cost associated with all 8 impressions.

### Off-Amazon query

In addition to an on-Amazon query, this IQ also includes a off-Amazon version to help advertisers selling off-Amazon improve their campaign performance for their focus audience by using Events Manager signals. For more information, refer to [Introduction to Events Manager](/marketing-cloud/instructional-queries/f28930f6d139cc028439497753c1eb1e6cbef52e49a108df56072c1f6b36d404).

### Requirements

To use this query:

* **Query 4.5.1:** You preferably have data from multiple Amazon DSP campaigns reaching the same audience segment. If you only have one campaign, the query will return results, but they will be limited to the single campaign and the path journey insights will not be available.
* **Query 4.5.2:** Enrollment (trial or subscription) in Audience Segment Insights (ASI) or Amazon Insights is required. Paid Features are only available in certain regions.
* **Query 4.5.3:** To run the off-Amazon query, you also need to have Events Managers signals set up and linked to the campaigns included in the analysis.



## Data query instructions

### Data returned 

The query will return all of the campaign paths the audience segment took, the number of path occurrences and the metrics associated with each path such as impressions, impression cost, and purchase rate.

### Table used 

* [dsp_impressions](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/dsp_impressions) - This data source is used for Amazon DSP traffic and to identify which impressions came from users aligned to the focus audience segment.
* [audience_segments_amer_inmarket](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/audience_segment_membership_paid) - This data source enables advertisers to reach customers at the end of the purchasing funnel when the customer is about to make a buying decision, such as in-market for baby products.
* [amazon_attributed_events_by_traffic_time](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/amazon_attributed_events_by_conversion_time) - Due to the 14-day attribution window, wait two weeks past the end of the campaign to run this query to capture all conversions.


## Data interpretation instructions
### Example query results 

**This data is for instructional purposes only. Your results will differ.**

The table below shows the aggregated output from the on-Amazon SQL query.



| path                                  | path_occurrences | impressions | imp_total_cost | users_that_purchased | sales_amount | purchases | user_purchase_rate | ntb_users_that_purchased | ntb_sales_amount | ntb_purchases | ntb_percentage | cpa        |
|---------------------------------------|------------------|-------------|----------------|----------------------|--------------|-----------|--------------------|--------------------------|------------------|---------------|----------------|------------|
| [[1, dsp], [2, tnf_twitch]]           | 3525             | 92889       | 856.5247       | 8                    | 4040.99      | 10        | 0.00227            | 5                        | 2880             | 5             | 0.625          | 107.06559  |
| [[1, dsp], [2, sov]]                  | 71036            | 4372454     | 35670.88       | 437                  | 292609.3     | 537       | 0.00615            | 309                      | 151823.6         | 315           | 0.70709        | 81.62673   |
| [[1, tnf_twitch], [2, sov], [3, dsp]] | 55163            | 601444      | 11890.45       | 3                    | 119.99       | 3         | 5.44E-05           | 3                        | 119.99           | 3             | 1              | 3963.48333 |
| [[1, dsp], [2, sov]]                  | 686229           | 5727307     | 39008.48       | 39                   | 4162.87      | 39        | 5.68E-05           | 29                       | 3196.92          | 29            | 0.74359        | 1000.21744 |
| [[1, dsp], [2, sov], [3, tnf_twitch]] | 28534            | 314792      | 3953.878       | 3                    | 59           | 3         | 1.05E-04           | 3                        | 59               | 3             | 1              | 1317.95933 |



The table below shows the aggregated output from the off-Amazon SQL query.


| path                              | path_occurrences | impressions | imp_total_cost | users_that_converted | conversions | user_conversion_rate | cpa      |
|-----------------------------------|------------------|-------------|----------------|----------------------|-------------|----------------------|----------|
| [[1, PVa], [2, DSP]]              | 5608             | 30600       | 641.2607       | 10                   | 11          | 0.00178              | 64.12607 |
| [[1, Twitch], [2, PVa], [3, DSP]] | 9                | 41          | 0.59063        | 0                    | 0           | 0                    |          |
| [[1, PVa], [2, Twitch], [3, DSP]] | 7                | 54          | 0.79721        | 0                    | 0           | 0                    |          |
| [[1, PVa]]                        | 21774            | 32577       | 650.2369       | 15                   | 17          | 0.00069              | 43.34913 |
| [[1, Twitch]]                     | 64067            | 188314      | 999.3337       | 24                   | 27          | 0.00037              | 41.6389  |
| [[1, DSP], [2, PVa], [3, Twitch]] | 19               | 165         | 2.33818        | 0                    | 0           | 0                    |          |
| [[1, Twitch], [2, DSP], [3, PVa]] | 28               | 216         | 3.15967        | 0                    | 0           | 0                    |          |
| [[1, DSP]]                        | 545501           | 1405871     | 11243.25       | 34                   | 35          | 0.00006              | 330.6838 |
| [[1, Twitch], [2, DSP]]           | 9082             | 51984       | 265.4698       | 6                    | 6           | 0.00066              | 44.24497 |
| [[1, PVa], [2, DSP], [3, Twitch]] | 15               | 158         | 2.62993        | 0                    | 0           | 0                    |          |
| [[1, PVa], [2, Twitch]]           | 40               | 140         | 1.55541        | 0                    | 0           | 0                    |          |
| [[1, DSP], [2, Twitch]]           | 9215             | 56762       | 248.9198       | 2                    | 2           | 0.00022              | 124.4599 |
| [[1, DSP], [2, Twitch], [3, PVa]] | 18               | 190         | 2.42788        | 0                    | 0           | 0                    |          |
| [[1, Twitch], [2, PVa]]           | 71               | 277         | 2.97816        | 0                    | 0           | 0                    |          |
| [[1, DSP], [2, PVa]]              | 6420             | 41106       | 879.6551       | 4                    | 4           | 0.00062              | 219.9138 |




### Metrics defined


All metrics only include users from the specified audience segment(s).


| Metric                    | Definition                                                                                                                                        |
|---------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| `path_occurrences`          | Number of unique users from the audience segment(s) who were exposed to the conversion path.                                                      |
| `impressions`               | Total impressions each path received over the campaign period from the audience segment(s).                                                       |
| `imp_total_cost`            | Total cost of advertising. Most DSP campaigns are charged on a cost per thousand impressions (CPM) basis. This metric is the advertiser currency. |
| `users_that_purchased`      | Number of unique users who made a purchase for on-Amazon analyses or off-Amazon conversion for off-Amazon analyses.                               |
| `sales_amount`*             | Sales (in local currency) on Amazon after exposed to an ad. For DSP, only promoted ASIN sales are included.                                       |
| `purchases`                 | Purchases by customers on Amazon after exposed to an ad. For DSP, only promoted ASIN purchases are included.                                      |
| `user_purchase_rate`        | Number of distinct users from the path that purchased divided by all users reached by the ad from the path.                                       |
| `ntb_users_that_purchased`* | Number of distinct users who made a New-To-Brand purchase.                                                                                        |
| `ntb_sales_amount`*         | New-to-Brand sales (in local currency) on Amazon after exposed to an ad. For DSP, only promoted ASIN sales are included.                          |
| `ntb_purchases`*            | New-to-Brand purchases by customers on Amazon after exposed to an ad. For DSP, only promoted ASIN purchases are included.                         |
| `ntb_percentage`*           | Percentage (%) of New-To-Brand purchasers (i.e. ntb_users_that_purchased/ users_that_purchased ).                                                 |
| `cpa`                       | Cost per acquisition, or cost per user who converted in your currency.                                                                            |

*sales amount and new-to-brand metrics not available for off-Amazon analyses 


### Insights and data interpretation

**On-Amazon interpretation with examples**

**Note:** Some of the insights are based on data not shown above in the examples.

* **Which path has the highest conversion rate for the focus audience?**
    * DSP followed by Amazon SOV was the path with the highest purchase rate for parents of tweens.
* **Which path had the highest share of NTB purchases for the focus audience?**
    * DSP followed by SOV and then TNF/Twitch tied with TNF/Twitch followed by SOV and then DSP for the  highest share of NTB purchases for parents of tweens.
* **How can the likeliness of users converting across ad types increase?**
    * The inclusion of DSP campaigns doubled the parents of tweens purchase rates compared to paths without DSP.
* **How does the ad journey for the focus audience differ from the journeys for other audiences?**
    * Note: This requires two separate executions of the query
    * Parents of tweens most commonly start their journey with DSP, whereas Gen Z most commonly start their journey with SOV.
* **How does the purchase journey for the focus audience differ from the journeys for other audiences?**
    * Note: This requires two separate executions of the query
    * Parents of tweens had the highest purchase rate when users were exposed to DSP followed by SOV, whereas Gen Z had the highest purchase rate when users were exposed to SOV → TNF/Twitch → DSP.


**Off-Amazon interpretation with examples**

**Note:** Some of the insights are based on data not shown above in the examples.

* **Which path has the highest off-Amazon conversion rate for the focus audience?**
    * Prime Video ads (PVa) followed by Amazon DSP was the path with the highest off_Amazon conversion rate for parents of tweens.
* **How can the likeliness of users converting across ad types increase?**
    * The inclusion of DSP campaigns doubled the parent of tweens off-Amazon conversion rates compared to paths without DSP.
* **How does the ad journey for the focus audience differ from the journeys for other audiences?**
    * Note: This requires two separate executions of the query
    * Parents of tweens most commonly start their journey with DSP, whereas Gen Z most commonly start their journey with SOV.
* **How does the off-Amazon conversion journey for the focus audience differ from the journeys for other audiences?**
    * Note: This requires two separate executions of the query
    * Parents of tweens had the highest off-Amazon conversion rate when users were exposed to PVa → DSP, whereas Gen Z had the highest off-Amazon conversion rate when users were exposed to SOV → TNF/Twitch → DSP.


**Recommendations**

* Suppose when running the analysis for the parents of tweens audience segment, you discover the path with the highest conversion rate is TNF/Twitch followed by SOV and then Amazon DSP.
    * For driving conversion rates for parents of tweens, we recommend creating an AMC audience to reach users exposed to the first touchpoint (TNF/Twitch) and then the second touchpoint (SOV). This AMC audience can then be used in Amazon DSP remarketing campaigns to increase the number of customers in the highest-performing path. 



## Queries

### Exploratory query: Identify Amazon DSP campaigns

Use the query below to identify the campaigns to use in the query templates.



```
-- Instructional Query: Path to conversion by audience segment (Identify campaigns) --
-- AD PRODUCTS: Amazon DSP --
SELECT
  campaign,
  campaign_id_string,
  SUM(impressions) AS impressions
FROM
  dsp_impressions
GROUP BY
  1,
  2
```

### Exploratory query: Identify sponsored ads campaigns

Use the query below to identify the campaigns to use in the query templates.


```
-- Instructional Query: Path to conversion by audience segment (Identify campaigns) --
-- AD PRODUCTS: Sponsored ads --
SELECT
  campaign,
  ad_product_type,
  SUM(impressions) AS impressions
FROM
  sponsored_ads_traffic
GROUP BY
  1,
  2
```

### Exploratory query: Identify audience segment IDs

Use the query below to query audience segment IDs to be used as a focus on for the analysis.

**Note:** Run this query for only one or two days to avoid query timeout.



```
-- Instructional Query: Path to conversion by audience segment (Identify segment IDs) --
-- AD PRODUCTS: Amazon DSP --
SELECT
  behavior_segment_name,
  behavior_segment_id
FROM
  dsp_impressions_by_user_segments
GROUP BY
  1,
  2
```

### Exploratory query: Identify off-Amazon conversion IDs and types

Use the query below to query the inputs to identify off-Amazon conversions (for off-Amazon path to conversion only).



```
-- Instructional Query: Path to conversion by audience segment (Identify off-Amazon conversionsoff Amazon only) --
SELECT
    campaign,
    campaign_id_string,
    conversion_event_type_description,
    tracked_item,
    SUM(conversions) AS conversions
  FROM
    amazon_attributed_events_by_traffic_time
  WHERE
    user_id IS NOT NULL
    AND conversions > 0
    AND conversion_event_category in ('pixel','off-Amazon')
  GROUP BY
    1,
    2,
    3,
    4
```

### Query templates: Path to conversion for a given audience

Below are three query templates: one for on-Amazon DSP campaigns only, one for on-Amazon DSP and sponsored ads campaigns, and one for advertisers tracking off-Amazon conversions using Events Manager signals for Amazon DSP campaigns only.

To run these queries:

* Follow the instructions in the query to suit your use case.
* Replace the audience segment ID with the audience segment that you want to run the analysis for. Consider using a larger audience segment to ensure enough data in each path for analysis.
* You can uncomment optional filters to the query if you want to consider conversions for specific ASINs (for on-Amazon purchases only).

**4.5.1 On-Amazon path to conversion (Amazon DSP campaigns)**


```
-- Instructional Query: Path to conversion by audience segment (On-Amazon)
-- AD PRODUCTS: Amazon DSP --

/*
 ------- Customization Instructions -------
 1) First, run the exploratory queries to identify the campaign ID strings and audience segment(s) of focus
 2) REQUIRED UPDATE [1 of 2]: Replace campaign_id_string and campaign_group placeholders with your campaign values and choice of groupings
 3) REQUIRED UPDATE [2 of 2]: Replace audience segment ID number with the one corresponding to your focus audience
 5) OPTIONAL UPDATE [1 of 1]: Uncomment indicated line to apply ASIN filters. Keep this line commented to skip filters 
*/

WITH campaign_group (campaign_id_string, campaign_group) AS (
  VALUES  
    ('campaign_id_string', 'campaign_group')  -- REQUIRED UPDATE [1 of 2]: Replace campaign_id_string and campaign_group
    ),
    

--P2C Purchases

/* Query Start */
    
impressions AS (
  SELECT
    COALESCE(g.campaign_group, 'DSP-Others') AS campaign_group,
    'DSP' AS product_type,
    user_id,
    MIN(impression_dt_utc) AS impression_dt_first,
    SUM(impressions) AS impressions,
    SUM(total_cost) AS total_cost
  FROM
    dsp_impressions i  INNER JOIN campaign_group dsp on dsp.campaign_id_string = i.campaign_id_string
    LEFT JOIN campaign_group G ON g.campaign_id_string = i.campaign_id_string
  WHERE
    user_id IS NOT NULL
  GROUP BY
    1,
    2,
    3 
),

impressions_segment as (
  SELECT 
    user_id
  FROM
  dsp_impressions
  WHERE 
    impressions > 0 
    AND user_id IN (select user_id from impressions group by 1) -- exposed users
    AND ARRAY_CONTAINS(user_behavior_segment_ids, '9999') -- REQUIRED UPDATE [2 of 2]: Replace audience segment ID number 
  GROUP BY 1
),

-- gather conversions --
converted AS (
  SELECT
    user_id,
    MAX(conversion_event_dt_utc) AS conversion_event_dt_last,
    -- Sales metrics
    SUM(product_sales) AS product_sales,
    SUM(purchases) AS purchases,
    -- NTB sales metrics
    MAX(IF(new_to_brand = TRUE, 1, 0)) AS ntb_cust,
    SUM(new_to_brand_product_sales) AS ntb_product_sales,
    SUM(new_to_brand_purchases) AS ntb_purchases
  FROM
    amazon_attributed_events_by_traffic_time
  WHERE
    purchases > 0
    AND user_id IS NOT NULL  
    AND user_id IN (select user_id from impressions_segment group by 1) -- exposed segment
    AND (campaign_id_string IN ( Select campaign_id_string from campaign_group))
         
    -- AND tracked_asin IN (Select ASIN from tracked_asins) -- OPTIONAL UPDATE [1 of 1]: Uncomment to apply ASIN filters
  GROUP BY
    1
),

-- only include impressions that happened before conversion event time --
filter_impressions AS (
  SELECT
    i.user_id AS imp_user_id,
    c.user_id AS pur_user_id,
    i.campaign_group,
    i.impressions,
    i.impression_dt_first,
    -- DSP cost is reported in millicents. To calculate the cost in dollars/your currency, divide the cost value by 100,000 (1e5)
    (i.total_cost / 1e5) AS total_cost,
    c.conversion_event_dt_last,
    COALESCE(c.product_sales, 0) AS product_sales,
    COALESCE(c.purchases, 0) AS purchases,
    COALESCE(c.ntb_cust, 0) AS ntb_cust,
    COALESCE(c.ntb_purchases, 0) AS ntb_purchases,
    COALESCE(c.ntb_product_sales, 0) AS ntb_product_sales
  FROM
    impressions i
    LEFT JOIN converted c ON c.user_id = i.user_id
  WHERE
    ((
      c.user_id IS NOT NULL
      AND i.impression_dt_first < c.conversion_event_dt_last
    )
    OR c.user_id IS NULL)
    AND i.user_id IN (select user_id from impressions_segment group by 1) -- exposed segment
),
-- order campaigns based on impression event time--
ranked AS (
  SELECT
    NAMED_ROW(
      'order',
      ROW_NUMBER() OVER (
        PARTITION BY f.imp_user_id
        ORDER BY
          f.impression_dt_first
      ),
      'campaign_group',
      f.campaign_group
    ) AS campaign_order,
    imp_user_id
  FROM
    filter_impressions f
  WHERE
    f.imp_user_id IS NOT NULL
),

-- create campaign path group by user --
assembled AS (
  SELECT
    ARRAY_SORT(COLLECT(DISTINCT a.campaign_order)) AS path,
    a.imp_user_id
  FROM
    ranked a
  GROUP BY
    a.imp_user_id
),

-- dedupe table conversions to represent each row as each user --
filter_impressions_dedupe AS (
  SELECT
    imp_user_id AS imp_user_id,
    SUM(impressions) AS impressions,
    SUM(total_cost) AS total_cost,
    MAX(IF(pur_user_id IS NOT NULL, 1, 0)) AS converted,
    MAX(product_sales) AS product_sales,
    MAX(purchases) AS purchases,
    MAX(ntb_cust) AS ntb_cust,
    MAX(ntb_purchases) AS ntb_purchases,
    MAX(ntb_product_sales) AS ntb_product_sales
  FROM
    filter_impressions
  GROUP BY
    imp_user_id
),
-- assemble impressions and conversions --
assembled_with_imp_conv AS (
  SELECT
    path AS path,
    COUNT(DISTINCT a.imp_user_id) AS reach,
    SUM(b.impressions) AS impressions,
    SUM(b.total_cost) AS imp_total_cost,
    SUM(b.converted) AS users_that_purchased,
    SUM(b.product_sales) AS sales_amount,
    SUM(b.purchases) AS purchases,
    SUM(b.ntb_cust) AS ntb_users_that_purchased,
    SUM(b.ntb_product_sales) AS ntb_sales_amount,
    SUM(b.ntb_purchases) AS ntb_purchases
  FROM
    assembled a
    LEFT JOIN filter_impressions_dedupe b ON a.imp_user_id = b.imp_user_id
  GROUP BY
    path
) -- FINAL SELECT

SELECT
  path,
  reach AS path_occurrences,
  impressions,
  imp_total_cost,
  users_that_purchased,
  sales_amount,
  purchases,
  (users_that_purchased / reach) AS user_purchase_rate,
  ntb_users_that_purchased,
  ntb_sales_amount,
  ntb_purchases,
  (ntb_users_that_purchased / users_that_purchased) AS ntb_percentage,
  (imp_total_cost / users_that_purchased) AS cpa
FROM
  assembled_with_imp_conv
  /* Query End */
```

**4.5.2 On-Amazon path to conversion (Amazon DSP and sponsored ads campaigns)**

**Note:** Enrollment (trial or subscription) in Audience Segment Insights (ASI) or Amazon Insights is required.



```
-- Instructional Query: Path to conversion by audience segment (On-Amazon)
-- Enrollment (trial or subscription) in Audience Segment Insights (ASI) or Amazon Insights is required --
-- AD PRODUCTS: Amazon DSP and Sponsored Ads --
/*
 ------- Customization Instructions -------
 1) First, run the exploratory queries to identify the campaign ID strings, campaigns, and audience segment(s) of focus
 2) REQUIRED UPDATE [1 of 4]: Replace campaign_id_string and campaign_group placeholders with your campaign values and choice of groupings for Amazon DSP campaigns.
 3) REQUIRED UPDATE [2 of 4]: Replace audience segment ID number with the one corresponding to your focus audience.
 4) REQUIRED UPDATE [3 of 4]: Replace audience table. The default captures in-market US audiences only. Change table to "audience_segments_amer_lifestyle" to capture US lifestyle audiences. If you are in APAC or EU please change table names to reflect your region ["audience_segments_apac_inmarket", "audience_segments_eu_inmarket", "audience_segments_apac_lifestyle","audience_segments_eu_lifestyle"]
 5) REQUIRED UPDATE [4 of 4]: Replace marketplace_id to one corresponding to your market. The US market is 1.
 6) OPTIONAL UPDATE [1 of 5]: Replace campaign_id_string and campaign_group placeholders with your campaign values and choice of groupings for Sponsored Ads campaigns. By default all Sponsored Ads campaigns are captured in this query.
 7) OPTIONAL UPDATE [2 of 5]: Uncomment this line to apply campaign groupings. Keep this line commented to skip groupings.
 8) OPTIONAL UPDATE [3 OF 5]: Uncomment this line to apply campaign filters. Keep this line commented to skip filters.
 9) OPTIONAL UPDATE [4 OF 5]: Uncomment this filter if Sponsored Ads campaigns groupings were applied.
 10) OPTIONAL UPDATE [5 OF 5]: Uncomment to apply ASIN filters.
*/

WITH campaign_group (campaign_id_string, campaign_group) AS (
  VALUES
    ('991000000000000001', 'dsp'), -- REQUIRED UPDATE [1 of 4]: Replace campaign_id_string and campaign_group for Amazon DSP campaigns.
    ('991000000000000002', 'prime_video'),
    ('991000000000000003', 'olv')
),
/* OPTIONAL UPDATE [1 of 5]: Use the following code if you want to apply campaign filters for Sponsored Ads campaigns. By default all Sponsored Ads campaigns are captured in this query. */
/* sa_campaigns (campaign, campaign_group) AS (
   VALUES
    ('991000000000000005', 'sp'),
    ('991000000000000006', 'sbv'),
    ('991000000000000007', 'sd'),
    ('991000000000000008', 'sb')
),*/

--P2C Purchases
/* Query Start */
impressions AS (
  SELECT
    COALESCE(dsp.campaign_group, 'DSP-Others') AS campaign_group,
    'DSP' AS product_type,
    user_id,
    MIN(impression_dt_utc) AS impression_dt_first,
    SUM(impressions) AS impressions,
    SUM(total_cost) AS total_cost
  FROM
    dsp_impressions i
    INNER JOIN campaign_group dsp ON dsp.campaign_id_string = i.campaign_id_string
  WHERE
    user_id IS NOT NULL
  GROUP BY
    1,
    2,
    3
  UNION ALL
-- All sponsored ads campaigns are captured for ease of the user
  SELECT
/* OPTIONAL UPDATE [2 OF 5]: Uncomment this line to apply campaign groupings. Keep this line commented to skip groupings. */
    --COALESCE(sa.campaign_group, 'SA-Others') AS campaign_group,    
    ad_product_type AS campaign_group, -- comment this line if above campaign groupings are applied
    'SA' AS product_type,
    user_id,
    MIN(event_dt_utc) AS impression_dt_first,
    SUM(impressions) AS impressions,
    SUM(spend) AS total_cost
  FROM
    sponsored_ads_traffic s
/* OPTIONAL UPDATE [3 OF 5]: Uncomment this line to apply campaign filters. Keep this line commented to skip filters. */
--  INNER JOIN sa_campaigns sa ON s.campaign= sa.campaign
    WHERE
    user_id IS NOT NULL
  GROUP BY
    1,
    2,
    3
),
impressions_segment AS (
  SELECT
    user_id
  FROM
    audience_segments_amer_inmarket -- REQUIRED UPDATE [2 of 4]: Replace audience table name. For lifestyle audiences change table name to "audience_segments_amer_lifestyle". If you are in APAC or EU use the following tables: "audience_segments_apac_inmarket", "audience_segments_eu_inmarket", "audience_segments_apac_lifestyle","audience_segments_eu_lifestyle."
  WHERE
    segment_marketplace_id = 1 -- REQUIRED UPDATE [3 of 4]: Replace marketplace_id. The US is 1.
    AND user_id IN (
      SELECT
        user_id
      FROM
        impressions
      GROUP BY
        1
    ) -- exposed users
    AND user_id IS NOT NULL
    AND segment_id IN (9999) -- REQUIRED UPDATE [4 of 4]: Replace audience segment ID number.
  GROUP BY
    1
),
-- gather conversions --
converted AS (
  SELECT
    user_id,
    MAX(conversion_event_dt_utc) AS conversion_event_dt_last,
    -- Sales metrics
    SUM(product_sales) AS product_sales,
    SUM(purchases) AS purchases,
    -- NTB sales metrics
    MAX(IF(new_to_brand = TRUE, 1, 0)) AS ntb_cust,
    SUM(new_to_brand_product_sales) AS ntb_product_sales,
    SUM(new_to_brand_purchases) AS ntb_purchases
  FROM
    amazon_attributed_events_by_traffic_time
  WHERE
    purchases > 0
    AND user_id IS NOT NULL
    AND user_id IN (
      SELECT
        user_id
      FROM
        impressions_segment
      GROUP BY
        1
    ) 
/* OPTIONAL UPDATE [4 of 5]: Uncomment this filter if Sponsored Ads campaigns groupings were applied. */
    --AND (campaign_id_string IN (Select d.campaign_id_string from campaign_group d) OR campaign IN (Select s.campaign from sa_campaigns s))  
    AND (campaign_id_string IN (Select d.campaign_id_string from campaign_group d) OR ad_product_type IS NOT NULL) -- comment this line if Sponsored Ads campaign groupings are applied above
     -- AND tracked_asin IN (Select ASIN from tracked_asins) -- OPTIONAL UPDATE [5 of 5]: Uncomment to apply ASIN filters.
  GROUP BY
    1
),

-- only include impressions that happened before conversion event time --
filter_impressions AS (
  SELECT
    i.user_id AS imp_user_id,
    c.user_id AS pur_user_id,
    i.campaign_group,
    i.impressions,
    i.impression_dt_first,
    -- DSP cost is reported in millicents. To calculate the cost in dollars/your currency, divide the cost value by 100,000 (1e5)
    IF(
      i.product_type IN (
        'DSP'
      ),
      (i.total_cost / 100000),
      (i.total_cost / 100000000)
    ) AS total_cost,
    c.conversion_event_dt_last,
    COALESCE(c.product_sales, 0) AS product_sales,
    COALESCE(c.purchases, 0) AS purchases,
    COALESCE(c.ntb_cust, 0) AS ntb_cust,
    COALESCE(c.ntb_purchases, 0) AS ntb_purchases,
    COALESCE(c.ntb_product_sales, 0) AS ntb_product_sales
  FROM
    impressions i
    LEFT JOIN converted c ON c.user_id = i.user_id
  WHERE
    (
      (
        c.user_id IS NOT NULL
        AND i.impression_dt_first < c.conversion_event_dt_last
      )
      OR c.user_id IS NULL
    )
    AND i.user_id IN (
      SELECT
        user_id
      FROM
        impressions_segment
      GROUP BY
        1
    ) -- exposed segment
),
-- order campaigns based on impression event time--
ranked AS (
  SELECT
    NAMED_ROW(
      'order',
      ROW_NUMBER() OVER (
        PARTITION BY f.imp_user_id
        ORDER BY
          f.impression_dt_first
      ),
      'campaign_group',
      f.campaign_group
    ) AS campaign_order,
    imp_user_id
  FROM
    filter_impressions f
  WHERE
    f.imp_user_id IS NOT NULL
),
-- create campaign path group by user --
assembled AS (
  SELECT
    ARRAY_SORT(COLLECT(DISTINCT a.campaign_order)) AS path,
    a.imp_user_id
  FROM
    ranked a
  GROUP BY
    a.imp_user_id
),
-- dedupe table conversions to represent each row as each user --
filter_impressions_dedupe AS (
  SELECT
    imp_user_id AS imp_user_id,
    SUM(impressions) AS impressions,
    SUM(total_cost) AS total_cost,
    MAX(IF(pur_user_id IS NOT NULL, 1, 0)) AS converted,
    MAX(product_sales) AS product_sales,
    MAX(purchases) AS purchases,
    MAX(ntb_cust) AS ntb_cust,
    MAX(ntb_purchases) AS ntb_purchases,
    MAX(ntb_product_sales) AS ntb_product_sales
  FROM
    filter_impressions
  GROUP BY
    imp_user_id
),
-- assemble impressions and conversions --
assembled_with_imp_conv AS (
  SELECT
    path AS path,
    COUNT(DISTINCT a.imp_user_id) AS reach,
    SUM(b.impressions) AS impressions,
    SUM(b.total_cost) AS imp_total_cost,
    SUM(b.converted) AS users_that_purchased,
    SUM(b.product_sales) AS sales_amount,
    SUM(b.purchases) AS purchases,
    SUM(b.ntb_cust) AS ntb_users_that_purchased,
    SUM(b.ntb_product_sales) AS ntb_sales_amount,
    SUM(b.ntb_purchases) AS ntb_purchases
  FROM
    assembled a
    LEFT JOIN filter_impressions_dedupe b ON a.imp_user_id = b.imp_user_id
  GROUP BY
    path
) -- FINAL SELECT
SELECT
  path,
  reach AS path_occurrences,
  impressions,
  imp_total_cost,
  users_that_purchased,
  sales_amount,
  purchases,
  (users_that_purchased / reach) AS user_purchase_rate,
  ntb_users_that_purchased,
  ntb_sales_amount,
  ntb_purchases,
  (ntb_users_that_purchased / users_that_purchased) AS ntb_percentage,
  (imp_total_cost / users_that_purchased) AS cpa
FROM
  assembled_with_imp_conv
  /* Query End */
```

**4.5.3  Off-Amazon path to conversion**

This query is designed for advertisers with Events Managers signals set up to capture off-Amazon conversions.


```
-- Instructional Query: Path to conversion by audience segment (Off-Amazon)
-- AD PRODUCTS: Amazon DSP --

/*
 ------- Customization Instructions -------
 1) First, run the exploratory queries to identify the campaign ID strings, audience segment(s) of focus, and conversion event values
 2) REQUIRED UPDATE [1 OF 4]: Replace campaign_id_string and campaign_group placeholders with your campaign values and choice of groupings
 3) REQUIRED UPDATE [2 of 4]: Replace ad tag placeholders with Pixel IDs or your ad tags
 4) REQUIRED UPDATE [3 of 4]: Replace conversion_event_type_description placeholder with your chosen conversion event types
 5) REQUIRED UPDATE [4 of 4]: Replace audience segment ID number with the one corresponding to your focus audience
*/

WITH campaign_group (campaign_id_string, campaign_group) AS (
  VALUES  
    ('campaign_id_string', 'campaign_group')   -- REQUIRED UPDATE [1 OF 4]: Replace campaign_id_string and campaign_group placeholders
    ),
    
ad_tag (tracked_item) AS (VALUES ('tag1'), ('tag2')), -- REQUIRED UPDATE [2 of 4]: Replace ad tags with Pixel IDs and Amazon Ad Tags

conversion_event_type_description (conversion_event_type_description) AS (VALUES ('placeholder')), -- REQUIRED UPDATE [3 of 4]: Replace conversion_event_type_description placeholder

-- P2C Off-Amazon conversions

/* Query Start */

impressions AS (
  SELECT
    COALESCE(G.campaign_group, 'DSP-Others') AS campaign_group,
    'DSP' AS product_type,
    user_id,
    MIN(impression_dt_utc) AS impression_dt_first,
    SUM(impressions) AS impressions,
    SUM(total_cost) AS total_cost
  FROM
    dsp_impressions i  INNER JOIN campaign_group dsp on dsp.campaign_id_string = i.campaign_id_string
    LEFT JOIN campaign_group G ON g.campaign_id_string = i.campaign_id_string
  WHERE
    user_id IS NOT NULL
  GROUP BY
    1,
    2,
    3 
),

impressions_segment as (
  SELECT 
    user_id
  FROM
  dsp_impressions
  WHERE 
    impressions > 0 
    AND user_id IN (select user_id from impressions group by 1) -- exposed users
    AND ARRAY_CONTAINS(user_behavior_segment_ids, '9999') -- REQUIRED UPDATE [4 of 4]: Replace audience segment ID number
  GROUP BY 1
),

-- gather conversions --
converted AS (
  SELECT
    user_id,
    MAX(conversion_event_dt_utc) AS conversion_event_dt_last,
    SUM(conversions) AS off_amazon_conversions --exact conversion(s) is specified below
  FROM
    amazon_attributed_events_by_traffic_time
  WHERE
    conversion_event_category in ('pixel','off-Amazon') -- select for pixel conversions
    AND conversions > 0  
    AND (lower(tracked_item) IN (SELECT lower(tracked_item) from ad_tag)
            OR lower(conversion_event_type_description) in (SELECT lower(conversion_event_type_description) from conversion_event_type_description)) 
    AND user_id IS NOT NULL 
    AND user_id IN (select user_id from impressions_segment group by 1) -- exposed audience
    AND (campaign_id_string IN (Select campaign_id_string from campaign_group)) 
  GROUP BY
    1
),

-- only include impressions that happened before conversion event time --
filter_impressions AS (
  SELECT
    i.user_id AS imp_user_id,
    c.user_id AS pur_user_id,
    i.campaign_group,
    i.impressions,
    i.impression_dt_first,
    -- DSP cost is reported in millicents. To calculate the cost in dollars/your currency, divide the cost value by 100,000 (1e5) to get the cost in dollars/your currency.
    (i.total_cost / 1e5) AS total_cost,
    c.conversion_event_dt_last,
    COALESCE(c.off_amazon_conversions, 0) AS off_amazon_conversions
  FROM
    impressions i
    LEFT JOIN converted c ON c.user_id = i.user_id
  WHERE
    ((
      c.user_id IS NOT NULL
      AND i.impression_dt_first < c.conversion_event_dt_last
    )
    OR c.user_id IS NULL)
    AND i.user_id IN (select user_id from impressions_segment group by 1) -- exposed audience
),
-- order campaigns based on impression event time--
ranked AS (
  SELECT
    NAMED_ROW(
      'order',
      ROW_NUMBER() OVER (
        PARTITION BY f.imp_user_id
        ORDER BY
          f.impression_dt_first
      ),
      'campaign_group',
      f.campaign_group
    ) AS campaign_order,
    imp_user_id
  FROM
    filter_impressions f
  WHERE
    f.imp_user_id IS NOT NULL
),

-- create campaign path group by user --
assembled AS (
  SELECT
    ARRAY_SORT(COLLECT(DISTINCT a.campaign_order)) AS path,
    a.imp_user_id
  FROM
    ranked a
  GROUP BY
    a.imp_user_id
),

-- dedupe table conversions to represent each row as each user --
filter_impressions_dedupe AS (
  SELECT
    imp_user_id AS imp_user_id,
    SUM(impressions) AS impressions,
    SUM(total_cost) AS total_cost,
    MAX(IF(pur_user_id IS NOT NULL, 1, 0)) AS converted,
    MAX(off_amazon_conversions) as off_amazon_conversions
  FROM
    filter_impressions
  GROUP BY
    imp_user_id
),
-- assemble impressions and conversions --
assembled_with_imp_conv AS (
  SELECT
    path AS path,
    COUNT(DISTINCT a.imp_user_id) AS reach,
    SUM(b.impressions) AS impressions,
    SUM(b.total_cost) AS imp_total_cost,
    SUM(b.converted) AS users_that_converted,
    SUM(b.off_amazon_conversions) AS off_amazon_conversions
  FROM
    assembled a
    LEFT JOIN filter_impressions_dedupe b ON a.imp_user_id = b.imp_user_id
  GROUP BY
    path
) -- FINAL SELECT

SELECT
  path,
  reach AS path_occurrences,
  impressions,
  imp_total_cost,
  users_that_converted,
  off_amazon_conversions as conversions,
  (users_that_converted / reach) AS user_conversion_rate,
  (imp_total_cost / users_that_converted) AS cpa
FROM
  assembled_with_imp_conv
  /* Query End */
```
