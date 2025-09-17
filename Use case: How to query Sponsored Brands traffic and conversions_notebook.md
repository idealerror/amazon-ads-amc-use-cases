## Metadata

**id:** d3dc6674-f168-4ce8-ae2a-85672245c808
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Query Sponsored Brands traffic and conversion insights across all ad formats.
**tags:** []
**use_case_categories:** ['How to']
**advertiser_types:** ['On-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## 1. Introduction

### 1.1 Purpose

This instructional query (IQ) will help you understand how to query Sponsored Brands (SB) campaigns, which includes product collection, store spotlight, and video ad formats. It also helps to understand how to query targeting, ad placement, and budget type information.

### 1.2 Requirements

Advertisers must run Sponsored Brands campaigns to use this IQ. 



## 2. Data Query Instructions

### 2.1 Data Returned

Ad Console reporting for individual SB campaigns (without ad-groups) should match the query results based on `amazon_attributed_events_by_conversion_time` table, while AMS reporting for new campaigns with ad-groups should match the query results based on `amazon_attributed_events_by_traffic_time` table.

Please note, results from AMS reporting may or may not match 100% with AMC outputs, based on various factors such as time-zone selected to run the reports, aggregation threshold rules, reinstatement window etc. AMC brings in reinstatements for the past 28 days, and any changes made in the source (Sponsored Brands) events after 28 days will not be captured in AMC. To learn more about aggregation threshold rules, and how to upload files to AMC, please refer to [AMC content in Support Center](https://advertising.amazon.com/help/G9H78BXVT5NCEGUM). 

Section 3.2, and 3.3 describe in detail, the dimensions and metrics defined in the instructional query, and example values.

This IQ takes into consideration only the traffic and Amazon attributed conversion events. Users can also perform custom attribution analysis using Sponsored Brands data. To know more on how to perform custom attribution on Sponsored Brands data, please refer to the IQ: [Documentation - Custom Attribution Overview](https://advertising.amazon.com/marketing-cloud/instructional-queries/72313993a488c7c1821e67c6fdd7a366cd434ef65ea14a2aae4d96fd16a2e627).

### 2.2 Tables Used 

* [amazon_attributed_events_by_traffic_time](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/amazon_attributed_events_by_conversion_time): This table contains all conversion events with Amazon attribution information based on the traffic time, including events from Sponsored Brands

* [amazon_attributed_events_by_conversion_time](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/amazon_attributed_events_by_conversion_time): This table contains all conversion events with Amazon attribution information based on the conversion time, including events from Sponsored Brands

* [sponsored_ads_traffic](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/sponsored_ads_traffic): This table contains traffic events for all Sponsored Ads products, including events from Sponsored Brands




## 3. Data Interpretation Instructions

### 3.1 Example Query Results

#### 3.1.1 Sponsored Brands Traffic Query Output

|campaign|ad_group|campaign_type|campaign_budget_type|campaign_end_dt|creative_type|impressions|viewable_impressions|clicks|
|---|---|---|---|---|---|---|---|---|
|Kitchen Smart &#x7c; SB &#x7c; All Products &#x7c; Awareness|My ad group|SB - Video|DAILY_BUDGET||Video|157466|56780|759|
|Kitchen Smart &#x7c; SB &#x7c; Toasters &#x7c; Awareness|My ad group|SB - Video|DAILY_BUDGET|2022-12-31T23:59:59.000Z||23115|7964|33|
|Kitchen Smart &#x7c; SB &#x7c; Lifestyle&#x7c; vCPM|My ad group|SB - Video|DAILY_BUDGET||Video|149995|42152|441|
|Kitchen Smart 2022&#x7c;Prospecting|My ad group|SB - Non Video|DAILY_BUDGET|||1209372|0|1533|
|Kitchen Smart&#x7c;Blenders &#x7c;Remarketing|My ad group|SB - Non Video|DAILY_BUDGET||ProductList|62286|0|294|

#### 3.1.2 Sponsored Brands Video Traffic Query Output

|campaign|ad_group|five_sec_views|video_complete_views|video_first_quartile_views|video_midpoint_views|video_third_quartile_views|video_unmutes|impressions|viewable_impressions|
|---|---|---|---|---|---|---|---|---|---|
|Kitchen Smart &#x7c; SB &#x7c; All Products &#x7c; Awareness|My ad group|18908|2048|16094|6780|3300|0|190713|71444|
|Kitchen Smart &#x7c; SB &#x7c; Toasters &#x7c; Awareness|My ad group|3764|209|2793|971|487|0|72240|18528|
|Kitchen Smart &#x7c; SB &#x7c; Lifestyle&#x7c; vCPM|My ad group|5196|297|2600|872|479|8|58122|22604|
|Kitchen Smart 2022&#x7c;Prospecting|My ad group|4956|957|6769|3097|1681|3|82234|21912|
|Kitchen Smart&#x7c;Blenders &#x7c;Remarketing|My ad group|12500|1452|12332|4821|2464|0|157466|56780|


#### 3.1.3 Sponsored Brands Conversions Query Output

|campaign|targeting|match_type|placement_type|customer_search_term_list|total_detail_page_view|total_detail_page_view_clicks|total_detail_page_view_views|total_add_to_cart|
|:--|:--|:--|:--|:--|:--|:--|:--|:--|
|Campaign111|ABC Brand|EXACT|Detail Page on-Amazon|[ABC Brand]|9109|9109|0|0|
|Campaign111|ABC Brand Product111|EXACT|Gateway on-Amazon|[*]|1449|1449|0|0|
|Campaign444|category="11111111"|TARGETING_EXPRESSION|Other on-Amazon|[Product222]|9860|9860|0|0|
|Campaign555|ABC Brand Product333 shampoo and conditioner|BROAD|Other on-Amazon|[Product333 , Shampoo and Conditioner]|4396|4396|0|0|
|Campaign666|Product444|EXACT|Top of Search on-Amazon|[Product444]|7398|7398|0|0|
|Campaign777|Toothpaste|PHRASE|Top of Search on-Amazon|[teeth whitening toothpaste , BrandABC Toothpaste]|6851|6851|0|0|
|Campaign888|category="22222222"|TARGETING_EXPRESSION_PREDEFINED|Top of Search on-Amazon|[weight loss supplements]|6625|6625|0|0|


### 3.2 Dimensions and Metrics Defined

|Type|Name|Description|
|:--|:--|:--|
|Dimension|`customer_search_term`|Applicable to Sponsored Brands & Sponsored Products only. This field contains the search term used by customer, that resulted in the impression.|
|Dimension|`targeting`|Applicable to Sponsored Brands & Sponsored Products only. This field contains the keyword used by the advertiser for targetting during campaign set up.|
|Dimension|`match_type`|Applicable to Sponsored Brands & Sponsored Products only. This field contains the match type between targeting and customer_search_term.|
|Dimension|`placement_type`|Applicable to all Sponsored Ads products. The field contain information on where the ad was placed. (eg. detailed page view).|
|Dimension|`campaign_budget_type`|Applicable to all Sponsored Ads products. Advertiser selected campaign budget type while setting up the campaign|
|Dimension|`campaign_end_dt`|Applicable to all Sponsored Ads products. Represents campaign end datetimestamp. Note that this metric is different from `campaign_end_date` metric whcich represents the campaign end date (without timestamp).|
|Metric|`five_sec_views`|Applicable to Sponsored Brands Video Ads only. This field contains the impressions where the video ad was viewed for 5 seconds, or 100% if the total length of video is less than 5 seconds.|
|Metric|`video_complete_views`|Applicable to Sponsored Brands Video Ads only. This field contains the impressions where the video ad was viewed to 100%|
|Metric|`video_first_quartile_views`|Applicable to Sponsored Brands Video Ads only. This field contains the impressions where the video ad was viewed to 25%|
|Metric|`video_midpoint_views`|Applicable to Sponsored Brands Video Ads only. This field contains the impressions where the video ad was viewed to 50%|
|Metric|`video_third_quartile_views`|Applicable to Sponsored Brands Video Ads only. This field contains the impressions where the video ad was viewed to 75%|
|Metric|`video_unmutes`|Applicable to Sponsored Brands Video Ads only. This field contains the impressions where the video ad was unmuted by customers|
|Metric|`total_detail_page_view`|Applicable to all Sponsored Ads products. This fields represents if the impression resulted in a detailed page view. `total_detail_page_view` = `total_detail_page_view_clicks` + `total_detail_page_view_views`. For Sponsored Brands, detail_page_view_views are always 0 as it is a CPC product.|
|Metric|`total_detail_page_view_clicks`|Applicable to all Sponsored Ads products. This fields represents if the impression resulted in a detailed page view, attributed to click. `total_detail_page_view_clicks` will always be equal to `total_detail_page_view` for SB as it is a CPC product.|
|Metric|`total_detail_page_view_views`|Applicable to all Sponsored Ads products. This fields represents if the impression resulted in a detailed page view, attributed to view. For Sponsored Brands, total_detail_page_view_views are always 0 as it is a CPC product.|
|Metric|`total_add_to_cart`|Applicable to all Sponsored Ads products. This fields represents if the impression resulted in an add-to-cart conversion. For Sponsored Brands, total_add_to_cart is always 0 as the product doesn't currently track add to cart conversions.|


### 3.3 Insights and data interpretation

Sponsored Brands supports multiple ad formats such as Retail (Product Collection, and Store Spotlight), and Video (SBV). To differentiate between these ad formats, use the video viewership metrics (metrics starting with “video_”) mentioned in the above table. These metrics will be populated only for Sponsored Brands Video (SBV) ads, while it will be shown as "NULL" for other ad formats.

The `match_type` dimension describes how the `customer_search_term` matched the `targeting` keyword. The field contains values such as "PHRASE", "EXACT", "BROAD", "TARGETING_EXPRESSION", and "TARGETING_EXPRESSION_PREDEFINED". To know more about these match types, please refer to the IQ: [How to query Sponsored Ads keywords](/marketing-cloud/instructional-queries/d0318a42-d23b-4b8d-a8fb-eb5c46661b2e).

The `placement_type` field contains details on where the ad was placed in Amazon. For Sponsored Brands, it can contain values such as "Top of Search on-Amazon", "Detail Page on-Amazon", "Other on-Amazon", "Gateway on-Amazon", " Gateway Page on-Amazon", "NULL" etc.

The `campaign_budget_type` field represents how the campaign budget was set up while configuring the Sponsored Brands ad, and it contains values such as "DAILY_BUDGET", and "TOTAL_FAST".

To learn more about Sponsored Brands Campaigns, please refer to the guide: [Your-Complete-Guide-to-Sponsored-Brands](https://amazonadvertising.turtl.co/story/your-complete-guide-to-sponsored-brands-en_us/).



## 4. Queries

### 4.1 Query Template: Sponsored Brands Traffic




```
/*Query Template : Sponsored Brands Traffic*/
-- Optional Update: To add campaign filters, add the campaign names in the sb_campaigns CTE and uncomment line [1 of 1] to enable filter
WITH sb_campaigns (campaign) AS (
  VALUES
    -- [Optional Update]: Add SB campaign names below without duplicates.
    ('campaign111'),
    ('campaign222'),
    ('campaign333')
),
sbv_campaigns AS (
  SELECT
    DISTINCT campaign
  FROM
    sponsored_ads_traffic
  WHERE
    five_sec_views = 1
    OR video_complete_views = 1
    OR video_first_quartile_views = 1
    OR video_midpoint_views = 1
    OR video_third_quartile_views = 1
    OR video_unmutes = 1
)
SELECT
  sat.campaign,
  sat.ad_group,
  CASE
    WHEN campaign IN (
      SELECT
        campaign
      FROM
        sbv_campaigns
    ) THEN 'SB - Video'
    ELSE 'SB - Non Video'
  END AS campaign_type,
  sat.campaign_budget_type,
  campaign_end_dt,
  creative_type,
  SUM(impressions) AS impressions,
  SUM(viewable_impressions) AS viewable_impressions,
  SUM(clicks) AS clicks
FROM
  sponsored_ads_traffic sat
  /*INNER JOIN sb_campaigns sbc ON sat.campaign = sbc.campaign -- Optional Update [1 of 1] uncomment this line  to enable campaign  filters*/
WHERE
  sat.ad_product_type = 'sponsored_brands'
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6
```

### 4.2 Query Template: Sponsored Brands Video Traffic



```
/*Query Template : Sponsored Brands Video Traffic*/
WITH sbv_campaigns AS (
 SELECT
 DISTINCT campaign
 FROM
 sponsored_ads_traffic
 WHERE
 five_sec_views = 1
 OR video_complete_views = 1
 OR video_first_quartile_views = 1
 OR video_midpoint_views = 1
 OR video_third_quartile_views = 1
 OR video_unmutes = 1
)
SELECT
 campaign,
 ad_group,
 SUM(five_sec_views) AS five_sec_views,
 SUM(video_complete_views) AS video_complete_views,
 SUM(video_first_quartile_views) AS video_first_quartile_views,
 SUM(video_midpoint_views) AS video_midpoint_views,
 SUM(video_third_quartile_views) AS video_third_quartile_views,
 SUM(video_unmutes) AS video_unmutes,
 SUM(impressions) AS impressions,
 SUM(viewable_impressions) AS viewable_impressions
FROM
 sponsored_ads_traffic
WHERE
 campaign IN (
 SELECT
 campaign
 FROM
 sbv_campaigns
 )
GROUP BY
 1,
 2
```

### 4.3 Query Template: Sponsored Brands Conversions


```
/*Query Template : Sponsored Brands Conversions*/
-- [Optional Update]: To add campaign filters, add the campaign names in the sb_campaigns CTE and uncomment line [1 of 1] to enable filter
WITH sb_campaigns (campaign) AS (
  VALUES
    -- Optional Update: Add SB campaign names below without duplicates.
    ('campaign111'),
    ('campaign222'),
    ('campaign333')
),
initial_aggregation AS (
  SELECT
    aae.campaign AS campaign,
    aae.targeting AS targeting,
    aae.match_type AS match_type,
    aae.placement_type AS placement_type,
    aae.customer_search_term AS customer_search_term_list,
    SUM(total_detail_page_view) AS total_detail_page_view,
    SUM(total_detail_page_view_clicks) AS total_detail_page_view_clicks,
    SUM(total_detail_page_view_views) AS total_detail_page_view_views,
    SUM(total_add_to_cart) AS total_add_to_cart
  FROM
    amazon_attributed_events_by_conversion_time AS aae -- [Optional Update] To query based on conversion time, uncomment this line and keep the line below commented
    /*amazon_attributed_events_by_traffic_time AS aae -- [Optional Update] To query based on traffic time, uncomment this line and keep the line above commented*/
    --INNER JOIN sb_campaigns sbc ON aae.campaign = sbc.campaign -- [Optional Update] [1 of 1] uncomment this line to enable campaign filters
  WHERE
    ad_product_type = 'sponsored_brands'
  GROUP BY
    1,
    2,
    3,
    4,
    5
)
SELECT
  IA.campaign AS campaign,
  IA.targeting AS targeting,
  IA.match_type AS match_type,
  IA.placement_type AS placement_type,
  COLLECT(distinct IA.customer_search_term_list) AS customer_search_term_list,
  SUM(IA.total_detail_page_view) AS total_detail_page_view,
  SUM(IA.total_detail_page_view_clicks) AS total_detail_page_view_clicks,
  SUM(IA.total_detail_page_view_views) AS total_detail_page_view_views,
  SUM(IA.total_add_to_cart) AS total_add_to_cart
FROM
  initial_aggregation IA
GROUP BY
  1,
  2,
  3,
  4
```
