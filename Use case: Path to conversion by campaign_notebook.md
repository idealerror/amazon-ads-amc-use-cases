## Metadata

**id:** 47b4188b0848e7bf5f0b4417cc5c284fafe7517099431e8a0a26c6fa5911cc88
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Analyze customers' buying journey before they purchased on Amazon.
**tags:** []
**use_case_categories:** ['Path-to-conversion']
**advertiser_types:** ['On-Amazon conversions', 'Off-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

#### Notice:

As of August 31, 2022, an improved version of this IQ has been made available in AMC. We recommend advertisers to use the new version: [Path to Conversion by Campaign Groups](/marketing-cloud/instructional-queries/753292eb51df1051c541d71eb40f198a4deb2ed026b499796ca0131a23c59483).
##### New IQ key differences:
* Better output structuring & ability to group campaigns: Grouping will reduce the likelihood of violating aggregation thresholds and users will see a smaller number of NULL rows.
* Ability to filter based on campaigns and/or ASINs
* Addition of Sponsored Display conversions


## Introduction 
### Purpose 
Taking time to understand your customers’ buying journey can help you improve your campaign performance. To better understand the customer journey, you’ll need to look at the conversion paths your customers took. This instructional query will enable you to identify the most popular path your customers took before converting. We offer two query templates: 1) Path to Conversion for DSP campaigns and 2) Path to Conversion for DSP and Sponsored Ad campaigns. Both options considers all touch points and their order.     

### Requirements 
To use this query, advertisers preferably have data from multiple campaigns reaching the same audience. If you only have one campaign, the query will return results, but they will be limited to the single campaign and the path journey insights will not be available. 


## Data Query Instructions 
### Data Returned 
The query will return the campaign path users took, the number of path occurrences and the performance metrics associated with each path such as impressions, impression cost and purchase. 

### Tables Used 
- **dsp_impressions**
- **sponsored_ads_traffic**
- **amazon_attributed_events_by_traffic_time**: Due to the 14-day attribution window, wait two weeks past the end of the campaign to run this query to capture all conversions.

### Query Template 
The queries are located in the appendix of this page. Scroll down to see the query templates.

We have two templates: 1) Path to Conversion DSP, 2) Path to Conversion DSP + Sponsored Ads. 

- You may choose a template based on your use case. Use Path to Conversion DSP if you want to look at DSP campaigns only. Use Path Conversion DSP + Sponsored Ads if you want to look at both DSP and Sponsored Ads together.   
- You can add optional filters to the query if you want to look at specific advertisers or campaigns using WHERE advertiser_id ='111111111' or WHERE campaign_id = '222222222'.
- You may also group / rename your campaigns for easy pathing analysis. The instruction is in the template.
- Use the filter WHERE purchases > 0 when pulling conversions to ensure you are only pulling purchase conversions and not all conversions. Other types of conversions include detail page views, add-to-cart, pixel conversions, etc. You can remove this filter, if necessary.



## Data Interpretation Instructions  
### Example Query Results 
This data is for instructional purposes only. Your results will differ.

| path                                                                              | path\_occurrences | impressions | imp\_total\_cost | users\_that\_purchased | sales\_amount | purchases | user\_purchase\_rate |
| --------------------------------------------------------------------------------- | ----------------- | ----------- | ---------------- | ---------------------- | ------------- | --------- | -------------------- |
| \[\[1, OTT\_Q32021\], \[2,SB Awareness\], \[3,Display Conversion\]\]         | 1005              | 87540       | 262.62           | 394                    | 7879          | 669  | 0.39197              |
| \[\[1, SB Awareness\], \[2, Display SOV\], \[3,Display Conversion \] | 987               | 45007       | 135.02           | 54                     | 972           | 75   | 0.05472              |
| \[1, OTT\_Q32021\], \[2, Display Conversion\]\]                                   | 456               | 9120        | 27.36            | 32                     | 383           | 76   | 0.07                 |
| \[\[1,SB Awareness\], \[2, OTT\_Q32021\], \[3, Display Conversion\]\]        | 132               | 4122        | 12.37            | 19                     | 464           | 26  | 0.14054              |


### Metrics Defined 

| metric                     | definition                                                                                                                                            |
| -------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| path\_occurences           | Number of users who were exposed to the ad campaigns in their conversion path                                                                         |
| impressions                | Total impressions each path received over the campaign period.                                                                                        |
| imp\_total\_cost           | Total cost of impressions served.                                                                                                                     |
| users\_that\_purchased     | Number of distinct users who made a purchase.                                                                                                         |
| sales\_amount              | The total sales (in local currency) of promoted ASINs purchased by customers on Amazon after exposed to an ad.                                         |
| user\_purchase\_rate       | Number of distinct users that purchased divided by all users reached by the ad.                                                                       |


### Insights and Data Interpretation 
The most frequent campaign path involves OTT + SB Awareness + Display Conversion. Furthermore, the two paths with the highest conversion rate include these three campaigns as well. On the other hand, the path that includes Display SOV campaign had the lowest conversion rate. If the goal is to drive the highest purchase rate, then continue to run OTT and SB Awareness + Display Conversion. 


## Query Template

These queries considers all touch points and their order.

We have two templates: 1) Path to Conversion DSP, 2) Path to Conversion DSP + Sponsored Ads. 

- You may choose a template based on your use case. Use Path to Conversion DSP if you want to look at DSP campaigns only. Use Path Conversion DSP + Sponsored Ads if you want to look at both DSP and Sponsored Ads together.   
- You can add optional filters to the query if you want to look at specific advertisers or campaigns using WHERE advertiser_id ='111111111' or WHERE campaign_id = '222222222'.
- You may also group / rename your campaigns for easy pathing analysis. The instruction is in the template.
- Use the filter WHERE purchases > 0 when pulling conversions to ensure you are only pulling purchase conversions and not all conversions. Other types of conversions include detail page views, add-to-cart, pixel conversions, etc. You can remove this filter, if necessary.

### Query Template for DSP campaigns only


```
-- Instructional Query: Path to Conversion Campaign / DSP campaigns only--
/*Bug Fix (8/31/22): A new CTE, filter_impressions_dedupe has been added to dedupe values at user_id level in the filter_impressions CTE. Please refer to comments in the query to understand more. */


WITH impressions AS
(
SELECT 
    -- OPTIONAL UPDATE Use the following code if you want to rename your campaigns. Update campaign_id and campaign group name. Make sure to comment out the campaign in the next line
    /*  CASE campaign_id 
     WHEN 11111111111111 THEN 'Display_Awareness'
     WHEN (222222222222) THEN 'Display_Conversion'
     ELSE 'Other' 
     END AS campaign,
     */
    --OPTIONAL UPDATE remove the campaign row below if you are using the campaign grouping code above.
    campaign,
    user_id,
    min(impression_dt) impression_dt_first,
    SUM(impressions) AS impressions,
    SUM(total_cost) AS total_cost
FROM dsp_impressions
WHERE user_id IS NOT NULL -- AND campaign_id IN (11111111111111,222222222222)
GROUP BY 1,2
)

-- gather conversions --
, converted AS
(
    SELECT
    -- OPTIONAL UPDATE Use the following code if you want to rename your campaigns. Update campaign_id and campaign group name. Make sure to comment out the campaign in the next line
    /*  CASE campaign_id 
     WHEN 11111111111111 THEN 'Display_Awareness'
     WHEN 222222222222 THEN 'Display_Conversion'
     ELSE 'Other' 
     END AS campaign,
     */
    --OPTIONAL UPDATE remove the campaign row below if you are using the campaign grouping code above.
        campaign,
        user_id,
        max(conversion_event_dt) AS conversion_event_dt_last,
        SUM(product_sales) AS product_sales,
        SUM(purchases) AS purchases
FROM amazon_attributed_events_by_traffic_time
WHERE purchases > 0 and user_id is not null -- AND campaign_id IN (11111111111111,222222222222)
GROUP BY 1,2
)

-- only include impressions that happened before conversion event time -- 
, filter_impressions AS 
( 
    SELECT i.user_id AS imp_user_id,
           c.user_id AS pur_user_id, 
           i.campaign,
           i.impressions,
           i.impression_dt_first,
           i.total_cost,
           c.conversion_event_dt_last,
           c.product_sales,
           c.purchases
    FROM impressions i 
    LEFT JOIN converted c ON c.user_id = i.user_id AND c.campaign = i.campaign
    WHERE ((c.user_id IS NOT NULL AND i.impression_dt_first < c.conversion_event_dt_last) 
    OR c.user_id IS NULL) 
), 

-- order campaigns based on impression event time--
 ranked AS
(
SELECT
    NAMED_ROW('order', ROW_NUMBER() OVER(PARTITION BY imp_user_id ORDER BY impression_dt_first),'campaign', campaign) AS campaign_order,
    imp_user_id
FROM filter_impressions 
WHERE imp_user_id is not null)

-- create campaign path group by user --
, assembled AS
(
SELECT
    ARRAY_SORT(COLLECT(distinct campaign_order)) AS path,
    imp_user_id
FROM ranked 
GROUP BY imp_user_id
),

/* Bug Fix: Start of Section added on 8/31/22 to dedupe values at user_id level */
filter_impressions_dedupe AS (
  SELECT
    imp_user_id                             AS imp_user_id,
    SUM(impressions)                        AS impressions,
    SUM(total_cost)                         AS total_cost,
    MAX(IF(pur_user_id IS NOT NULL,1,0))    AS pur_user_id,
    MAX(product_sales)                      AS product_sales,
    MAX(purchases)                          AS purchases
  FROM
    filter_impressions
  GROUP BY
    imp_user_id
),
/* Bug Fix: End of Section added on 8/31/22 to dedupe values at user_id level */

-- assemble impressions, clicks and conversions --
  assembled_with_imp_conv AS
    (
        SELECT path,
                count(distinct a.imp_user_id) as reach,
                SUM(b.impressions) as impressions,
                -- impression_cost and total_cost are reported in millicents in the dsp_impressions table. To calculate the cost in dollars/your currency, divide the cost value by 100,000. 
                SUM(b.total_cost)/100000 as imp_total_cost,
                SUM(b.pur_user_id) as users_that_purchased,
                SUM(b.product_sales) as sales_amount,
                SUM(b.purchases) as purchases
    FROM assembled a
    LEFT JOIN filter_impressions_dedupe b /*Bug Fix: Updated on 8/31/22 to dedupe values at user_id level*/
           ON a.imp_user_id = b.imp_user_id
    GROUP BY path
    )

    SELECT
        path,
        reach AS path_occurrences,
        impressions,
        imp_total_cost,
        users_that_purchased,
        sales_amount,
        purchases,
        (users_that_purchased/reach) as user_purchase_rate
    FROM assembled_with_imp_conv
```

### 4.2 Query Template for DSP and Sponsored Ads


```

-- Instructional Query: Path to Conversion Campaign / DSP + Sponsored Ads--
/*Bug Fix (8/31/22): A new CTE, filter_impressions_dedupe has been added to dedupe values at user_id level in the filter_impressions CTE. Please refer to comments in the query to understand more. */

-- gather impressions  -- 
WITH impressions AS
(
SELECT
-- OPTIONAL UPDATE Use the following code if you want to rename your campaigns. Update campaign_id and campaign group name. Make sure to comment out the campaign in the next line
/*  CASE campaign_id 
    WHEN 11111111111111 THEN 'Display_Awareness'
    WHEN (222222222222) THEN 'Display_Conversion'
    ELSE 'Other' 
    END AS campaign,
*/
--OPTIONAL UPDATE remove the campaign row below if you are using the campaign grouping code above.
   'DSP' as product_type,
    campaign,
    user_id,
    MIN(impression_dt) AS impression_dt_first,
    SUM(impressions) AS impressions,
    SUM(total_cost) AS total_cost
FROM dsp_impressions
WHERE user_id IS NOT NULL
-- AND campaign_id IN (11111111111111,222222222222)
GROUP BY 1,2,3

UNION ALL 

SELECT 
--OPTIONAL UPDATE Use the following code if you want to rename your campaigns. Update campaign and campaign group name Make sure to comment out the campaign 
/*  CASE campaign 
      WHEN 'SP_campaign_name1' THEN 'SP_Awareness_name'
      WHEN 'SP_campaign_name2' THEN 'SP_Conversion_name'
      WHEN 'SD_campaign_name1' THEN 'SD_Awareness_name'
      WHEN 'SD_campaign_name2' THEN 'SD_Conversion_name'
      WHEN 'SB_campaign_name1' THEN 'SB_Awareness_name'
      WHEN 'SB_campaign_name2' THEN 'SB_Conversion_name'  
    ELSE 'Other' 
      END AS campaign,
*/
--OPTIONAL UPDATE remove the campaign row below if you are using the campaign grouping code above.
    ad_product_type as product_type, 
    campaign,
    user_id,
    MIN(event_dt) AS impression_dt_first,
    SUM(impressions) AS impressions,
    SUM(spend) AS total_cost
FROM sponsored_ads_traffic 
WHERE user_id IS NOT NULL
-- AND campaign IN ('SP_campaign_name1','SP_campaign_name2','SD_campaign_name1','SD_campaign_name2','SB_campaign_name1','SB_campaign_name2')
GROUP BY 1,2,3
)

-- gather conversions --
, converted AS
(
    SELECT 
    -- OPTIONAL UPDATE Use the following code if you want to rename your campaigns. Update campaign_id and campaign group name. Make sure to comment out the campaign in the next line
    /*  CASE campaign_id 
     WHEN 11111111111111 THEN 'Display_Awareness'
     WHEN 222222222222 THEN 'Display_Conversion'
     ELSE 'Other' 
     END AS campaign,
     */
    --OPTIONAL UPDATE remove the campaign row below if you are using the campaign grouping code above.
    
        campaign,
        user_id,
        max(conversion_event_dt) AS conversion_event_dt_last,
        SUM(product_sales) AS product_sales,
        SUM(purchases) AS purchases
FROM amazon_attributed_events_by_traffic_time
WHERE purchases+total_purchases_clicks > 0 and user_id is not null
--OPTIONAL UPDATE if you want to select specific campaigns, use the following filter.
-- AND (campaign_id IN (11111111111111,222222222222)
-- OR campaign IN ('SP_campaign_name1','SP_campaign_name2','SD_campaign_name1','SD_campaign_name2','SB_campaign_name1','SB_campaign_name2'))
GROUP BY 1,2
)

-- only include impressions that happened before conversion event time -- 
, filter_impressions AS 
( 
    SELECT i.user_id AS imp_user_id,
           c.user_id AS pur_user_id, 
           i.campaign,
           i.impressions,
           i.impression_dt_first,
           -- DSP cost is reported in millicents. To calculate the cost in dollars/your currency, divide the cost value by 100,000.Sponsored Ads spend is reported as microcents. Divide by 100,000,000 to get the cost in dollars/your currency.  
           IF(i.product_type in ('sponsored_products','sponsored_display', 'sponsored_brands'),(i.total_cost/100000000),(i.total_cost/100000)) AS total_cost, 
           c.conversion_event_dt_last,
           COALESCE(c.product_sales,0) AS product_sales,
           COALESCE(c.purchases,0) as purchases
    FROM impressions i 
    LEFT JOIN converted c ON c.user_id = i.user_id AND c.campaign = i.campaign 
    WHERE 
    ((c.user_id IS NOT NULL AND i.impression_dt_first < c.conversion_event_dt_last) 
    OR c.user_id IS NULL) 
), 
-- order campaigns based on impression event time--
 ranked AS
(
SELECT
    NAMED_ROW('order', ROW_NUMBER() OVER (PARTITION BY f.imp_user_id ORDER BY f.impression_dt_first),'campaign', f.campaign) AS campaign_order,
    imp_user_id
FROM filter_impressions f
WHERE f.imp_user_id is not null)

-- create campaign path group by user --
, assembled AS
(
SELECT
    ARRAY_SORT(COLLECT(distinct a.campaign_order)) AS path,
    a.imp_user_id
FROM ranked a
GROUP BY a.imp_user_id)


/* Bug Fix: Start of Section added on 8/31/22 to dedupe values at user_id level */
, filter_impressions_dedupe AS (
  SELECT
    imp_user_id                             AS imp_user_id,
    SUM(impressions)                        AS impressions,
    SUM(total_cost)                         AS total_cost,
    MAX(IF(pur_user_id IS NOT NULL,1,0))    AS pur_user_id,
    MAX(product_sales)                      AS product_sales,
    MAX(purchases)                          AS purchases
  FROM
    filter_impressions
  GROUP BY
    imp_user_id
),
/* Bug Fix: End of Section added on 8/31/22 to dedupe values at user_id level */

-- assemble impressions, clicks and conversions -- 
 assembled_with_imp_conv AS
    (
        SELECT path,
                count(distinct a.imp_user_id) as reach,
                SUM(b.impressions) as impressions,
                SUM(b.total_cost) as imp_total_cost,
                SUM(b.pur_user_id) as users_that_purchased, 
                SUM(b.product_sales) as sales_amount,
                SUM(b.purchases) as purchases
    FROM assembled a
    LEFT JOIN filter_impressions_dedupe b /*Bug Fix: Updated on 8/31/22 to dedupe values at user_id level*/
           ON a.imp_user_id = b.imp_user_id 
    GROUP BY path
    )

    SELECT
        path,
        reach AS path_occurrences,
        impressions,
        imp_total_cost,
        users_that_purchased,
        sales_amount,
        purchases,
        (users_that_purchased/reach) as user_purchase_rate
    FROM assembled_with_imp_conv
```
