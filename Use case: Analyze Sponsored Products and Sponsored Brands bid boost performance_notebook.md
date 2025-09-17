## Metadata

**id:** 4fab210703d19e75001e06c53fe060f675046af5f7689e7a5027bd4b6e32505e
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Assess the performance of the bid boosted audience in your Sponsored Products or Sponsored Brands campaigns.
**tags:** []
**use_case_categories:** ['Performance deep dive', 'Reach and frequency', 'Audience measurement']
**advertiser_types:** []
**ad_product_types:** None
**shared_with_accounts:** None

---

## Introduction 

### Purpose 

Audience bid boosting for Sponsored Products and Sponsored Brands enables advertisers to reach and engage shoppers using custom-built AMC audiences. If you have created custom AMC audiences and used them for bid boosting for Sponsored Products and Sponsored Brands, use this IQ to analyze the performance of impressions for matched audiences compared to when no audience was matched. 

### Requirements 

Advertisers should be running campaigns on Sponsored Products or Sponsored Brands with AMC bid boosted audiences.

## Data query instructions 

### Data returned

For each bid boosted campaign, the query will return two rows:

* Impressions served, sum of clicks, sum of purchases for impressions with matched audiences.
* Impressions served, sum of clicks, sum of purchases for impressions without audiences matched (where `matched_behavior_segment_ids` is NULL). 

### Tables used 

* [sponsored_ads_traffic](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/sponsored_ads_traffic) - The table includes the `matched_behavior_segment_ids` column, as well as traffic events for Sponsored Products and Sponsored Brands.
* [amazon_attributed_events_by_conversion_time](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/amazon_attributed_events_by_conversion_time) - Use this table to query conversion events for the campaigns using AMC audiences for bid boosting.

## Data interpretation instructions 

### Example query results

**This data is for instructional purposes only. Your results will differ.**



| campaign           | campaign_id_string     | ad_product_type    | matched_behavior_segment_ids | impressions | clicks | purchases |
|--------------------|------------------------|--------------------|------------------------------|-------------|--------|-----------|
| SP Campaign Name 1 | A00034234234234234234  | sponsored_products | 3012928                      | 7776        | 59     | 17        |
| SP Campaign Name 1 | A00034234234234234234  | sponsored_products |                              | 23241       | 729    | 249       |
| SP Campaign Name 2 | A08046456456456456645  | sponsored_products | 3013313                      | 28369       | 112    | 6         |
| SP Campaign Name 2 | A08046456456456456645  | sponsored_products |                              | 364835      | 966    | 71        |
| SP Campaign Name 3 | A04564564564565464578  | sponsored_products | 3012142                      | 56218       | 1052   | 76        |
| SP Campaign Name 3 | A04564564564565464578  | sponsored_products |                              | 2998        | 44     | 0         |
| SP Campaign Name 4 | A027877897897897878978 | sponsored_products | 3012158                      | 49273       | 192    | 10        |
| SP Campaign Name 4 | A027877897897897878978 | sponsored_products |                              | 334423      | 845    | 28        |



### Dimensions and metrics defined

| metric                       | definition                                                                                                                                                             |
|------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `campaign`                     | Name of the campaign                                                                                                                                                   |
| `campaign_id_string`           | Identifier of the campaign                                                                                                                                             |
| `ad_product_type`              | Either sponsored_products or sponsored_brands                                                                                                                          |
| `matched_behavior_segment_ids` | The segment IDs for the audience Amazon matched an impression to when serving an ad. This field can be used to understand if when an audience bid multiplier was used. |
| `impressions`                  | Sum of impressions for both matched and unmatched audiences                                                                                                            |
| `clicks`                       | Sum of clicks for both matched and unmatched audiences                                                                                                                 |
| `purchases`                    | Sum of purchases for both matched and unmatched audiences                                                                                                              |

### Note on data interpretation

The results may not match data from reports you see in the Ads Console or Amazon API. Tables used in this query (`sponsored_ads_traffic` and `amazon_attributed_events_by_conversion_time`) apply a different methodology to filter events compared to reports available in the Ads Console or Amazon API.

When using this query, focus on comparing the relative performance between matched and unmatched audiences within the query results. This will provide valuable insights into the effectiveness of your audience bid boosting strategies, even though absolute numbers may differ from other reports.

## Query

### Instructional query: Measure the performance of bid boosting using AMC audiences

Run the following query to query performance metrics for campaigns that were bid boosted by AMC audiences. Note that this query returns results for both Sponsored Products and Sponsored Brands campaigns. 

This query uses the `matched_behavior_segment_ids` field in the `sponsored_ads_traffic` table to identify which campaigns were boosted by AMC audiences. The `matched_behavior_segment_ids` field contains `segment_id` values for the audience Amazon matched an impression to when serving an ad. If this field is NULL, the audience was not matched.

Customization options:

* Delete either `sponsored_products` or `sponsored_brands` to filter by one ad product type.


```
/* Instructional Query: Instructional query: Measure the performance of bid boosting using AMC audiences */
-- AD PRODUCTS: Sponsored ads --
    
WITH campaigns (campaign_id_string, matched_behavior_segment_ids) AS (
  SELECT
    campaign_id_string,
    matched_behavior_segment_ids
FROM
    sponsored_ads_traffic
WHERE
    matched_behavior_segment_ids IS NOT NULL
    AND
    ad_product_type IN (
    'sponsored_products',
    'sponsored_brands')
),
traffic AS (
  SELECT
    campaign,
    campaign_id_string,
    ad_product_type,
    user_id,
    matched_behavior_segment_ids,
    event_id,
    SUM(impressions) AS impressions,
    SUM(clicks) AS clicks
  FROM
    sponsored_ads_traffic
  WHERE
    ad_product_type IN (
    'sponsored_products',
    'sponsored_brands')
    and
    campaign_id_string IN (
      SELECT
        campaign_id_string
      FROM
        campaigns
    )
  GROUP BY
    1,
    2,
    3,
    4,
    5,
    6
),
attributed_events AS (
  SELECT
    campaign,
    campaign_id_string,
    ad_product_type,
    user_id,
    traffic_event_id,
    SUM(purchases) AS purchases
  FROM
    amazon_attributed_events_by_conversion_time
  WHERE
    campaign_id_string IN (
      SELECT
        campaign_id_string
      FROM
        campaigns
    )
  GROUP BY
    1,
    2,
    3,
    4,
    5
),
combined AS (
  SELECT
    t.campaign,
    t.campaign_id_string,
    t.ad_product_type,
    t.matched_behavior_segment_ids,
    t.impressions,
    t.clicks,
    t.user_id,
    a.purchases
  FROM
    traffic t
  LEFT JOIN attributed_events a
  ON  t.campaign = a.campaign
  AND t.user_id  = a.user_id
  AND t.event_id = a.traffic_event_id
)
SELECT
  campaign,
  campaign_id_string,
  ad_product_type,
  matched_behavior_segment_ids,
  SUM(impressions) AS impressions,
  SUM(clicks) AS clicks,
  SUM(purchases) AS purchases
FROM
  combined c
GROUP BY
  1,
  2,
  3,
  4
```
