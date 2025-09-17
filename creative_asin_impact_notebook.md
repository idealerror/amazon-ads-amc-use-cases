## Metadata

**id:** cdfa9c7e68d0be4bab0575256c60fab6986caf99337d17aabfd42e76c2559761
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Understand which creative ASINs within Sponsored Products and Sponsored Display campaigns are most effective in driving purchases.
**tags:** []
**use_case_categories:** ['ASIN analysis', 'Performance deep dive']
**advertiser_types:** ['On-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## Introduction 

### Purpose 

This IQ helps you better understand how creative ASINs (that is, ASINs featured in sponsored ads creative) influence purchase behaviors. By identifying which creative ASINs contribute most effectively to purchases, you can better optimize creative selections in sponsored ads campaigns. 


### Requirements

To use this query, advertisers need:

* Relevant campaign data (Sponsored Products or Sponsored Display) in a single AMC instance. 

**Note:** Only ASIN conversions will be counted as purchases. 

## Data query instructions 

### Data returned

This IQ provides one query: 

* **Creative ASIN Impact Analysis** – Returns the ASINs featured in your campaign creatives, the number of unique customers exposed to each creative ASIN, and the associated ASINs they went on to purchase.
  * For each pair of creative ASIN and purchased ASIN, the analysis includes the number of distinct purchasers and the total number of purchases, highlighting the purchase behavior following exposure to a specific creative ASIN.

### Tables used 

* [sponsored_ads_traffic](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/sponsored_ads_traffic): This table provides sponsored ads traffic data, capturing `user_id` to identify exposed audiences segmented by `creative_asin`.
* [amazon_attributed_events_by_traffic_time](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/amazon_attributed_events_by_conversion_time): This table is used to track all ASIN conversions from the exposed audience during the analysis period, enabling measurement of purchase behaviors.

## Data interpretation instructions 

### Example query results

**This data is for instructional purposes only. Your results will differ.**

Query: Creative ASIN Impact Analysis

asin\_in\_creative | unique\_customers\_exposed | asin\_purchased | occurrences | total\_purchases
------------------|--------------------------|----------------|------------|----------------
B00000000B       | 6000                     | B0000000D      | 50         | 60
B00000000B       | 6000                     | B0000000E      | 200        | 220
B00000000B       | 6000                     | B0000000F      | 100        | 140
B00000000C       | 3000                     | B0000000E      | 20         | 30
B00000000C       | 3000                     | B0000000F      | 10         | 20

### Metrics and dimensions

| metric/dimension            | definition                                                                                                                                                                                                                                                |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `asin_in_creative`       | ASINs featured in your campaign creatives                                                                                                                                                                                                                      |
| `unique_customers_exposed`| Number of unique users exposed to the campaign creative                                                                                                                                                                                                        |
| `asin_purchased`          | ASINs purchased by users who were exposed to the creative                                                                                                                                                                                                      |
| `occurrences`              | Number of unique users who purchased a given ASIN (asin\_purchased) after being exposed to a specific creative ASIN (asin\_in\_creative)                                                                                                                        |
| `total_purchases`         | Total ad-attributed purchases of a given ASIN (asin\_purchased) after exposure to a specific creative ASIN (asin\_in\_creative). This includes purchases related to both promoted ASINs and brand halo ASINs.                                                    |

**Note:**
The metrics for `occurrences` and `total_purchases` may differ from standard reporting, because the same customer may appear multiple times if they were exposed to multiple creative ASINs that are being evaluated. This helps capture the impact of each creative ASIN independently, but may lead to duplicated counts across creative ASIN and purchased ASIN pair scenarios. 

### Insights and data interpretation

**What products are users purchasing after seeing the creative?** 

By analyzing the output, it’s possible to determine which ASINs led to most purchases, and how many unique users converted. 

For example, in the sample result: 

* Creative ASIN B00000000B led to 350 unique users (5.8%) purchasing products B0000000D, B0000000E or B0000000F, out of 6000 unique customers exposed to creative featuring B00000000B.
* In contrast, creative ASIN B00000000C resulted in 30 unique users (1%) purchasing products B0000000E or B0000000F, out of 3000 unique customers exposed to the creative featuring B00000000C. 

**Sample Recommendation**: The advertiser should consider prioritizing creative ASIN B00000000B or testing a different ASIN in the creative going forward. If the campaign is still running, it may also be worth increasing the budget for high-performing creative ASINs.

## Queries

### Exploratory query for campaigns and creative ASINs


```
-- Instructional Query: Exploratory Query to identify creative ASINs used in a campaign -- 
-- AD PRODUCTS: Sponsored ads
SELECT 
    campaign
    , campaign_id_string 
    , creative_asin
    , creative 
FROM sponsored_ads_traffic 
GROUP BY 1, 2, 3, 4 
```

### Creative ASIN impact analysis 


```
-- Instructional Query: Creative ASIN Impact analysis -- 
-- AD PRODUCTS: Sponsored ads: Sponsored Products and Sponsored Display
/* 
 ------- Customization Instructions -------
 -- REQUIRED UPDATE: In the sa_campaign_group CTE, add one or more campaign names.
 -- OPTIONAL UPDATE: To filter for specific creative ASINs, uncomment and add one or more creative ASINs to the line `AND creative_asin IN ('B0CZY23DYB')`
*/


WITH sa_campaign_group (campaign_name) AS (
  VALUES
    ('ABCDEFG')
),

user_exposed AS (

  SELECT
    user_id,
    creative_asin, 
    event_dt_utc AS impression_event_dt, 
    SUM(impressions) AS impressions,
    SUM(spend) / 1e8 AS total_cost

  FROM
    sponsored_ads_traffic a  
  INNER JOIN sa_campaign_group sa 
  ON sa.campaign_name = a.campaign 

  WHERE
    user_id IS NOT NULL 
    AND ad_product_type is not null
      AND impressions >0 
    -- FILTER Specific CREATIVE_ASINS if needed 
    -- AND creative_asin IN ('B0CZY23DYB') 

  GROUP BY
    1, 2, 3
),

reach AS (
    SELECT 
        creative_asin, 
        count(distinct user_id) AS unique_customers_exposed
    FROM user_exposed 
    GROUP BY 1
),

converted AS (
    SELECT 
          user_id
        , tracked_asin
        , conversion_event_dt_utc AS conversion_event_dt
        , SUM(total_purchases) AS total_purchases  -- Sales metric

    FROM amazon_attributed_events_by_traffic_time
    WHERE user_id IN (SELECT user_id FROM user_exposed GROUP BY 1)
    AND campaign in (SELECT campaign_name FROM sa_campaign_group GROUP BY 1)
    AND total_product_sales >0 
    AND conversion_event_subtype = 'order'
    GROUP BY 1, 2, 3 
), 

together AS (
    SELECT 
        a.user_id
        , b.creative_asin 
        , a.tracked_asin 
        , SUM(total_purchases) AS total_purchases  
    FROM converted a 
    LEFT JOIN user_exposed as b 
    ON a.user_id = b.user_id 
    WHERE
    (
      a.user_id IS NOT NULL
      AND b.impression_event_dt < a.conversion_event_dt
    )
    GROUP BY 1,2,3 
)

SELECT 
    a.creative_asin AS asin_in_creative
    , b.unique_customers_exposed 
    , a.tracked_asin AS asin_purchased 
    , count(distinct a.user_id) AS occurrences
    , sum(a.total_purchases) AS total_purchases
FROM together AS a 
LEFT JOIN reach AS b 
on a.creative_asin = b.creative_asin
WHERE a.creative_asin IS NOT NULL 
GROUP BY 1,2,3
```
