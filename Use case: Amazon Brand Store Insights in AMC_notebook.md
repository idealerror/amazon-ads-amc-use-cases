## Metadata

**id:** e623c5e25dac014f42137dd71977a3ace7d78a892e68b9c504a9df4c621dcaf4
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Explore the AMC Brand Store Insights dataset and identify high-value audience segments (Amazon Brand Store Insights required).
**tags:** ['Paid features']
**use_case_categories:** ['How to', 'Path-to-conversion', 'Audience measurement']
**advertiser_types:** []
**ad_product_types:** None
**shared_with_accounts:** None

---

## Introduction 

### Summary

Use this IQ to explore the AMC Brand Store Insights (BSI) dataset and identify high-value Amazon DSP audience segments, assess the customer journey through a path to conversion analysis, and generate AMC audiences based on Brand Store engagements and dwell time. 

### What is Amazon Brand Store? 

[Amazon Brand Stores](https://advertising.amazon.com/solutions/products/stores) is a free and self-service storefront where your brand can engage, convert, and build loyalty with millions of Amazon shoppers. Advertisers can leverage Sponsored Brands and Sponsored Display campaigns to drive shoppers to a Brand Store destination; however, there is no prerequisite to advertise on Amazon to create a Brand Store.

### What is Amazon Brand Store Insights? 

AMC Brand Store Insights (BSI) allows you to tap into page visit, dwell time, and interaction signals from Amazon Brand Stores. When you enroll in Amazon Brand Store Insights, you will have access to the `amazon_brand_store_page_views` and `amazon_brand_store_engagement_events` datasets containing signals such as Brand Store attributes, page visit details, interaction events, `visit_id` and `session_id` as well as page visit URL parameter signals.

You can pair Amazon Brand Store signals with other signals available in AMC, such as advertising traffic events or Amazon Ads audience segment membership signals. This instructional query guides you through decorating AMC segment membership signals with the Amazon BSI dataset by using the AMC `user_id` as the join key across tables. Additional use-cases referenced within this instructional query include a path to conversion analysis and Brand Store page visit referral source analysis.

### Which marketplaces is this available for?

The AMC Amazon Brand Store Insights paid feature is available for enrollment within the nine AMC Account Marketplaces where AMC Paid Features is supported. For more information, refer to [AMC Paid features](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/overview#amc-paid-features).

### Requirements 

* Enrollment (trial or subscription) to Amazon Brand Store Insights is required. Amazon Brand Store Insights paid feature is available for enrollment within the AMC account marketplaces where [AMC Paid Features is supported](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/overview#amc-paid-features). If you're unable to see the Paid features tab, contact your AdTech Account Executive.
* AMC customer campaigns must have conversion events (the selected event subtype within the path to conversion analysis in this IQ). For AMC audience creation, there is no requirement of previous advertising activities (e.g. impressions or interactions).
* For some of the queries below, AMC customers must have access to Audience Segment Insights.
* AMC customers must have advertisers associated with the AMC instance in order to activate AMC audiences.

### Tables used 

* [amazon_brand_store_page_views](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/brand_store_insights_paid#amazonbrandstorepageviews-nonendemic): This data source is an AMC paid feature and provides page visit signals including dwell_time. 
* [amazon_brand_store_engagement_events](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/brand_store_insights_paid#amazonbrandstoreengagementevents-nonendemic): This data source is an AMC paid feature and provides page interaction events presented at the widget and event level.
* [audience_segments_<region>_<segment_category>](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/audience_segment_membership_paid): Amazon Audience Segment Insights enable advertisers to conduct user-to-segment analysis for all Amazon Advertising customers across all AMC datasets as well as segment-level analysis for advertiser uploaded datasets.
    * For example, `audience_segments_amer_inmarket` and `audience_segments_amer_lifestyle` are tables that contain `user_id`s present in the in-market segment category with segment details and `marketplace_id`. These table names will differ based on region the AMC instance is a part of.
* [amazon_attributed_events_by_traffic_time](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/amazon_attributed_events_by_conversion_time): This data source is used within the standard path to conversion analysis and provides ad attributed conversion events.
* [dsp_impressions](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/dsp_impressions): This data source is used within the standard path to conversion analysis and provides impression events for Amazon DSP campaigns (specifically for this IQ, DSP video and DSP display impression events).
* [sponsored_ads_traffic](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/sponsored_ads_traffic): This data source is used within the standard path to conversion analysis and provides impression events for sponsored ads campaigns (specifically for this IQ, Sponsored Brands impression events).

### Dataset specifications and schema

**Dataview name:** `amazon_brand_store_page_views`

**Dataview description:** Brand Store page view events, including dwell time. Events are at the Store page-level. 

For more information, refer to [amazon_brand_store_page_views (_non_endemic)](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/brand_store_insights_paid#amazonbrandstorepageviews-nonendemic) in the Amazon ads Advanced tools center.

**Dataview name:** `amazon_brand_store_engagement_events`

**Dataview description:** Brand Store engagement events. Provides Brand Store web engagement metrics including page views and clicks. Events are interaction based and presented at the Store widget-level.

For more information, refer to [amazon_brand_store_engagement_events (_non_endemic)](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/brand_store_insights_paid#amazonbrandstoreengagementevents-nonendemic) in the Amazon ads Advanced tools center.

**Amazon Brand Store Insights: Sample enumerated values**
The following tables list some of the most common enumerated values for reference_id, referrer_domain, widget(_sub)_type, event(_sub)_type, and device_type. 

Note: This is not an exhaustive list and is subject to change.

device_type:

| device_type |
|---------------|
| Mobile_app    |
| Desktop       |
| Mobile_web    |
| Tablet_app    |
| Tablet_web    |
| Kindle_app    |


event_type:

| event_type |
|------------|
| IMPRESSION |
| CLICK      |
| SCROLL     |
| VIDEO      |


event_sub_type:

| event_sub_type    |
|-------------------|
| ASIN_RENDER       |
| 100PPLAYED        |
| 5SPLAYED          |
| ADDTOCART         |
| BEGIN             |
| DETAILPAGE        |
| GOTODETAILPAGE    |
| NAVIGATION        |
| PREORDERADDTOCART |
| PROCEEDTOCHECKOUT |
| QUICKLOOK         |
| SHOPPINGCART      |


widget_type:

| widget_type            |
|------------------------|
| ProductGrid            |
| EditorialRow           |
| PromotionsUnifiedBadge |
| ProductCarousel        |
| Header                 |
| ProductCollection      |
| Gallery                |
| LiveVideo              |
| Share                  |


widget_sub_type:

| widget_sub_type          |
|--------------------------|
| Navigation               |
| ColorSwatches            |
| InteractiveImage         |
| BEST_SELLING             |
| ProductShowcase          |
| RECOMMENDATION_FOR_YOU   |
| CouponClipper            |
| PromotionClipper         |
| FrequentlyBoughtTogether |


referrer_domain:

| referrer_domain |
|-----------------|
| amazon.com      |
| twitch.tv       |
| amazon.co.jp    |
| amazon.de       |
| amazon.co.uk    |
| facebook.com    |
| youtube.com     |
| tiktok.com      |
| instagram.com   |
| linktr.ee       |
| snapchat.com    |


reference_id:

| reference_id                    |
|---------------------------------|
| bl_ast_dp_brandLogo_sto         |
| storeRecs_dp_aplus              |
| org_bbb_bsp_bi-shop-by-category |
| SB_CAMPAIGN-CREATIVE            |
| SBV_CAMPAIGN(-CREATIVE)         |
| org_posts_feed_rf               |



## Queries

### Use cases

This IQ provides the following use cases:

* **Section 2.2: Explore the Amazon Brand Store Insights datasets:** Explore some of the signals available in the `amazon_brand_store_page_views` and `amazon_brand_store_engagement_events` datasets.
* **Section 2.3: Path to Conversion analysis:** Analyze the customer journey through a path to conversion analysis including Amazon Brand Store page visits.
* **Section 2.4: Segment data decoration for high-value Brand Store customers and events:** Assess which audience segments overlap most heavily with high-value Brand Store customers. Incorporate campaign events to measure the impact of running campaigns alongside Brand Store activations. Specifically, it can be used to analyze the impact of ad-exposure (awareness) and Brand Store interactions (consideration) on the eventual conversion KPI (purchases).
* **Section 2.5: Amazon Brand Store referral source analysis:** Understand where traffic to your Brand Store originates. Generate AMC insights that showcase the referral sources for the highest-value Brand Store events. This includes assessment of Brand Store visit quality based on interactions, outcomes, referrer campaigns, or content partners.
* **Section 2.6: Create AMC Audiences based on customizable Brand Store interaction events:** Create an audience of Brand Store customers with more than five minutes of dwell time and Brand Store add-to-cart action.

With these queries, you can optimize your Amazon DSP campaigns - whether that means refining your audience strategy using Brand Store signals, or deriving high-value referral sources to drive customers to your Amazon Brand Store.

### Explore Amazon Brand Store Insights data

Use this query to explore the Amazon Brand Store Insights associated with your AMC instance, such as marketplace, `store_id` and page titles. This query should outline the difference between count and count distinct when converting `session_id` or `visit_id` into calculated metrics. If your AMC instance does not return back your entire expected list of Amazon Brand Store store ids, contact AMC Support.

**Query template**



```
-- Instructional Query: Exploratory Query for Amazon Brand Store Insights in AMC -- 
-- Enrollment (trial or subscription) to Amazon Brand Store Insights is required
SELECT
 marketplace_name,
 store_name,
 store_id,
 page_id,
 page_title,
 COUNT(session_id) AS count_sessions,
 COUNT(DISTINCT session_id) AS distinct_sessions,
 COUNT(visit_id) AS count_visits,
 COUNT(DISTINCT visit_id) AS distinct_visits,
 ROUND((SUM(dwell_time)/60),0) AS total_dwell_time_in_minutes_rounded
FROM amazon_brand_store_page_views 
GROUP BY 1,2,3,4,5
```

**Example query results**

**Note:** This data contains a placeholder store name and page title values and is for instructional purposes only. Your results will differ.



| marketplace_name | store_name    | store_id                           | page_id                            | page_title | count_sessions | distinct_sessions | count_visits | distinct_visits | total_dwell_time_in_minutes_rounded |
|------------------|---------------|------------------------------------|------------------------------------|------------|----------------|-------------------|--------------|-----------------|-------------------------------------|
| AMAZON.COM       | Kitchen smart | 35FE7892-123-456-87FF-A10D3FA1010F | E8E13F69-456-456-A576-9958160B0077 | Toasters   | 6              | 6                 | 6            | 6               | 1                                   |
| AMAZON.COM       | Kitchen smart | 35FE7892-123-456-87FF-A10D3FA1010F | DF6C818A-0567-456-456-384B45BA2CDA | Tumblers   | 66169          | 55422             | 66169        | 55422           | 35234                               |
| AMAZON.COM       | Kitchen smart | 35FE7892-123-456-87FF-A10D3FA1010F | E4AD1B5B-CFAA-456-456-82D4E9A5D5C8 | Stoves     | 5048           | 4522              | 5048         | 4522            | 2485                                |
| AMAZON.COM       | Kitchen smart | 35FE7892-123-456-87FF-A10D3FA1010F | 817217D3-350C-456-456-75021932F1D3 | Mixers     | 10161          | 8662              | 10161        | 8662            | 5566                                |
| AMAZON.COM       | Kitchen smart | 35FE7892-123-456-87FF-A10D3FA1010F | CE26D9E0-456-456-B7B2-982EC20DD082 | Kids       | 19870          | 16472             | 19870        | 16472           | 11208                               |
| AMAZON.COM       | Kitchen smart | 35FE7892-123-456-87FF-A10D3FA1010F | 627C827B-456-456-BC89-65A5A229E4EC | Blenders   | 567            | 521               | 567          | 521             | 116                                 |
| AMAZON.COM       | Kitchen smart | 35FE7892-123-456-87FF-A10D3FA1010F | 2A8D235B-456-456-80D3-0428459B645A | Food       | 13279          | 11221             | 13279        | 11221           | 7298                                |


### Path to Conversion analysis

To better understand the customer journey, identify the most popular paths your customers took before purchasing your products on Amazon.

Customization ideas:

* Update the DSP groupings to reflect campaign IDs or campaign objective/KPIs instead of DSP creative format.
* Update the query to include additional sponsored ads touch points including: Sponsored Products, Sponsored Television, or Sponsored Display.

**Query template**


```
-- Instructional Query: Amazon Brand Store Insights - Path to Conversion analysis --
-- Enrollment (trial or subscription) to Amazon Brand Store Insights is required-- 
-- Please follow through the instrucutions in comments to customize this insight
-- AD PRODUCTS: Sponsored Brands and Amazon DSP (Display and Video)
WITH 
/* Start with a grouping of DSP ad exposure */
relevant_dsp_impressions AS (
  SELECT
    creative_category,
    user_id,
    MIN(impression_dt) AS impression_dt_first,
    MAX(impression_dt) AS impression_dt_last,
    SUM(impressions) AS impressions,
    SUM(total_cost) AS total_cost
  FROM
    dsp_impressions i 
  WHERE
    user_id IS NOT NULL 
    AND creative_category in ('Video', 'Display')
  GROUP BY
    1,
    2
),
impressions AS (
  SELECT
    'DSP-Video' AS campaign_group,
    'DSP' AS product_type,
    user_id,
    impression_dt_first,
    impression_dt_last,
    impressions,
    total_cost
  FROM
    relevant_dsp_impressions 
  WHERE
    creative_category = 'Video' 
  -- DSP display creative ad exposure Section START
  UNION ALL
  SELECT
    'DSP-Display' AS campaign_group,
    'DSP' AS product_type,
    user_id,
    impression_dt_first,
    impression_dt_last,
    impressions,
    total_cost
  FROM
    relevant_dsp_impressions 
  WHERE
    creative_category = 'Display' -- DSP Display Section END
    -- Brand Store page visits START
  UNION ALL
  SELECT
    'BSI' AS campaign_group,
    'BSI' AS product_type,
    user_id,
    MIN(event_dt_utc) AS impression_dt_first,
    MAX(event_dt_utc) AS impression_dt_last,
    COUNT(distinct visit_id) AS impressions,
    SUM(0) AS total_cost -- there is no cost associated with a Brand Store page visit, this is earned media and not paid media
  FROM
    amazon_brand_store_page_views
  WHERE
    user_id IS NOT NULL
  GROUP BY
    1,
    2,
    3 -- Brand Store page visits END
    -- Sponsored Brands Section START
  UNION ALL
  SELECT
    'SB-Others' AS campaign_group,
    'SB' AS product_type,
    user_id,
    MIN(event_dt_utc) AS impression_dt_first,
    MAX(event_dt_utc) AS impression_dt_last,
    SUM(impressions) AS impressions,
    SUM(spend) AS total_cost
  FROM
    sponsored_ads_traffic a 
  WHERE
    user_id IS NOT NULL
    AND ad_product_type = 'sponsored_brands'
  GROUP BY
    1,
    2,
    3 -- Sponsored Brands Section END
),
-- gather conversions --
converted AS (
  SELECT
    user_id,
    MAX(conversion_event_dt) AS conversion_event_dt_last,
    -- Sales metrics
    SUM(
      IF(
        ad_product_type IS NULL,
        product_sales,
        total_product_sales
      )
    ) AS product_sales,
    -- replace product_sales with total_product_sales to include brand halo sales from DSP
    SUM(
      IF(ad_product_type IS NULL, purchases, total_purchases)
    ) AS purchases,
    -- replace purchases with total_purchases to include brand halo purchases from DSP
    -- NTB sales metrics
    MAX(IF(new_to_brand = TRUE, 1, 0)) AS ntb_cust,
    SUM(
      IF(
        ad_product_type IS NULL,
        new_to_brand_product_sales,
        new_to_brand_total_product_sales
      )
    ) AS ntb_product_sales,
    -- replace new_to_brand_product_sales with new_to_brand_total_product_sales to include brand halo sales from DSP
    SUM(
      IF(
        ad_product_type IS NULL,
        new_to_brand_purchases,
        new_to_brand_total_purchases
      )
    ) AS ntb_purchases -- replace new_to_brand_purchases with new_to_brand_total_purchases to include brand halo purchases from DSP
  FROM
    amazon_attributed_events_by_traffic_time
  WHERE
    IF(ad_product_type IS NULL, purchases, total_purchases) > 0 -- replace purchases with total_purchases to include brand halo conversions from DSP
    AND user_id IS NOT NULL 
    -- the AMC user has the option to apply inline ASIN filtering using tracked_item or tracked_asin columns if desired for the analysis
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
    i.impression_dt_last,
    i.product_type,
    -- DSP cost is reported in millicents. To calculate the cost in dollars/your currency, divide the cost value by 100,000 (1e5). sponsored ads spend is reported as microcents. Divide by 100,000,000 (1e8) to get the cost in dollars/your currency.
    IF(
      i.product_type = 'DSP',
      (i.total_cost / 1e5),
      (i.total_cost / 1e8)
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
      c.user_id IS NOT NULL
      AND i.impression_dt_first < c.conversion_event_dt_last
    )
    OR c.user_id IS NULL
),
-- order campaigns/BSI "touch points" based on impression/event time --
ranked AS (
  SELECT
    NAMED_ROW(
      'order',
      ROW_NUMBER() OVER (
        PARTITION BY f.imp_user_id
        ORDER BY
          f.impression_dt_first,
          f.impression_dt_last desc,
          campaign_group,
          product_type
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
    SUM(b.total_cost) AS total_cost,
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
  impressions AS impressions,
  total_cost AS total_cost,
  users_that_purchased AS users_that_purchased,
  sales_amount AS sales_amount,
  purchases AS purchases,
  (users_that_purchased / reach) AS user_purchase_rate,
  ntb_users_that_purchased AS ntb_users_that_purchased,
  ntb_sales_amount AS ntb_sales_amount,
  ntb_purchases AS ntb_purchases,
  (ntb_users_that_purchased / users_that_purchased) AS ntb_percentage
FROM
  assembled_with_imp_conv
  /* Query End */
```

**Sample results**

**Note:** This data is for instructional purposes only. Your results will differ.


| path                                                         | path_occurrences | impressions | total_cost | users_that_purchased | sales_amount | purchases | user_purchase_rate | ntb_users_that_purchased | ntb_sales_amount | ntb_purchases | ntb_percentage |
|--------------------------------------------------------------|------------------|-------------|------------|----------------------|--------------|-----------|--------------------|--------------------------|------------------|---------------|----------------|
| [[1, DSP-Video], [2, BSI]]                                   | 1517             | 4202        | $38.40     | 11                   | $326.68      | 16        | 1%                 | 8                        | $261.03          | 9             | 73%            |
| [[1, DSP-Display], [2, SB-Others], [3, DSP-Video]]           | 3049             | 18436       | $107.04    | 4                    | $289.97      | 8         | 0%                 | 2                        | $43.78           | 2             | 50%            |
| [[1, DSP-Video], [2, DSP-Display], [3, SB-Others], [4, BSI]] | 2                | 24          | $3.49      | 1                    | $32.94       | 2         | 50%                | 1                        | $32.94           | 2             | 100%           |
| [[1, SB-Others], [2, DSP-Display], [3, BSI]]                 | 25               | 352         | $2.25      | 2                    | $144.85      | 3         | 8%                 | 0                        | $0               | 0             | 0%             |
| [[1, DSP-Display], [2, SB-Others], [3, BSI]]                 | 576              | 5702        | $172.95    | 36                   | $1,649.49    | 57        | 6%                 | 19                       | $729.29          | 22            | 53%            |
| [[1, DSP-Display], [2, BSI], [3, DSP-Video]]                 | 36               | 169         | $1.04      | 0                    | $0           | 0         | 0%                 | 0                        | $0               | 0             |                |
| [[1, DSP-Display], [2, DSP-Video], [3, BSI]]                 | 16               | 70          | $0.32      | 0                    | $0           | 0         | 0%                 | 0                        | $0               | 0             |                |
| [[1, SB-Others], [2, BSI], [3, DSP-Video], [4, DSP-Display]] | 3                | 24          | $3.10      | 0                    | $0           | 0         | 0%                 | 0                        | $0               | 0             |                |
| [[1, SB-Others], [2, BSI], [3, DSP-Video]]                   | 9081             | 86564       | $3,227.33  | 152                  | $7,520.78    | 249       | 2%                 | 62                       | $2,212.01        | 70            | 41%            |
| [[1, DSP-Display], [2, DSP-Video], [3, BSI], [4, SB-Others]] | 3                | 28          | $0.90      | 0                    | $0           | 0         | 0%                 | 0                        | $0               | 0             |                |
| [[1, BSI]]                                                   | 48594            | 71913       | $0         | 418                  | $18,124.19   | 615       | 1%                 | 268                      | $9,861.87        | 317           | 64%            |


**Metrics defined**


| metric                   | definition                                                                                                                                                                                                                                                                                                                                                         |
|--------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `path_occurences`          | Number of unique users who were exposed to the ad campaigns in their conversion path                                                                                                                                                                                                                                                                               |
| `impressions`              | Total impressions each path received over the campaign period.                                                                                                                                                                                                                                                                                                     |
| `total_cost`               | Total cost of advertising based on how the ads were purchased. Most DSP campaigns are charged on a cost per thousand impressions (CPM) basis. Sponsored Products and Sponsored Brands are charged on a cost per click (CPC) basis. Sponsored Display campaigns have two billing strategies: cost per click (CPC) and cost per thousand viewable impressions (vCPM) |
| `users_that_purchased`     | Number of unique users who made a purchase.                                                                                                                                                                                                                                                                                                                        |
| `sales_amount`             | Sales (in local currency) on Amazon after exposed to an ad. For DSP, only ASIN sales are factored in, but for sponsored ads, ASIN and Brand Halo sales are factored in. Advertisers may choose to include Brand Halo DSP sales by following instructions in "converted" cte.                                                                                       |
| `purchases`                | Purchases by customers on Amazon after exposed to an ad. For DSP, only ASIN purchases are factored in, but for sponsored ads, ASIN and Brand Halo purchases are factored in. Advertisers may choose to include Brand Halo DSP purchases by following instructions in "converted" cte.                                                                              |
| `user_purchase_rate`       | Number of distinct users that purchased divided by all users reached by the ad.                                                                                                                                                                                                                                                                                    |
| `ntb_users_that_purchased` | Number of distinct users who made a New-To-Brand purchase.                                                                                                                                                                                                                                                                                                         |
| `ntb_sales_amount`         | New-to-Brand sales (in local currency) on Amazon after exposed to an ad. For DSP, only ASIN sales are factored in, but for sponsored ads, ASIN and Brand Halo sales are factored in. Advertisers may choose to include new-to-brand Brand Halo DSP sales by following instructions in "converted" cte.                                                             |
| `ntb_purchases`            | New-to-Brand purchases by customers on Amazon after exposed to an ad. For DSP, only ASIN purchases are factored in, but for sponsored ads, ASIN and Brand Halo purchases are factored in. Advertisers may choose to include new-to-brand Brand Halo DSP purchases by following instructions in "converted" cte.                                                    |
| `ntb_users_percentage`     | Percentage (%) of New-To-Brand purchasers (i.e. ntb_users_that_purchased/ users_that_purchased )                                                                                                                                                                                                                                                                   |
| `ntb_purchases_percentage` | Percentage (%) of New-To-Brand purchases (i.e. ntb_purchases / purchases)                                                                                                                                                                                                                                                                                          |



**Insights and interpretation**

Based on the data from BSI based path to conversion, the desired path to conversion of DSP Video, DSP Display, Sponsored Brands redirect to Brand Store only occurs twice but has a 50% user purchase rate and a 100% new to brand purchase rate, where the single NTB purchase was for 2 items. Using audience targeting in the DSP and Sponsored Brands AMC audiences can assist with re-marketing based on campaign ad exposure and should help increase the frequency that campaign audiences are pushed down through the desired marketing funnel path. For the time interval assessed there were 418 users that purchased with only exposure to Brand Store pages and no ad exposure. You can create Lookalike audiences based on that list of seed user_ids using AMC Audiences.

### Segment data decoration for high-value Brand Store customers and events

This SQL query determines the overlap between Amazon standard catalog audiences (sourced from AMC ASI and associated with subcategories - LS/IM/LE) and Amazon Brand Store signals (specifically the add-to-cart action). 

Customization ideas:

* Optionally, filter by a different `event_sub_type` (DETAILPAGE, NAVIGATION, PREORDERADDTOCART, PROCEEDTOCHECKOUT, QUICKLOOK)
* Optionally, add a `dwell_time` filter to include only users who spent a certain amount of time viewing the Brand Store.

**Query template**



```
-- Instructional Query: Amazon Brand Store Insights - Segment data decoration for high-value Brand Store customers and events --
-- Enrollment (trial or subscription) to Amazon Brand Store Insights is required --
-- Enrollment in Audience Segments Insights is required (for audience_segments_amer_lifestyle) --
/*
 ------- Customization Instructions -------
 1) OPTIONAL UPDATE [1 of 3]: Change 'ATC' to another hardcoded value that represents your use case
 2) OPTIONAL UPDATE [2 of 3]: Change event_sub_types to an item from the example array - ('DETAILPAGE','NAVIGATION','PREORDERADDTOCART','PROCEEDTOCHECKOUT','QUICKLOOK')
 3) OPTIONAL UPDATE [3 of 3]: Uncomment the dwell_time statement to filter users that had a dwell_time greater than 60 seconds.
 */
 
 /* Generate the list of user_id values associated with the conversion event for 
 the selected date range */
/* For Audience Segment Insights tables please review the best practices prior to selecting a date-range for the query 
    - https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/audience_segment_membership_paid */
WITH bsi_user_list AS (
  SELECT
    user_id,
    'ATC' AS bsi_action,
    COUNT(distinct session_id)-- OPTIONAL UPDATE [1 of 3]: Change 'ATC' to another hardcoded value that represents your use case
  FROM
    amazon_brand_store_engagement_events 
  WHERE
    event_sub_type = 'ADDTOCART' -- OPTIONAL UPDATE [2 of 3]: Alternatively this can be Change event_sub_types to an item from the example array - ('DETAILPAGE','NAVIGATION','PREORDERADDTOCART','PROCEEDTOCHECKOUT','QUICKLOOK')
   /* -- OPTIONAL UPDATE [3 of 3]: Uncomment to filter users that had dwell time greater than 60 seconds in a single page render event */
    -- OR dwell_time > 60
    GROUP BY 1,2
),
/* Generate the count of users associated with the saved bsi_action attribute 
 by using the bsi_user_list CTE  */
bsi_event_counter AS (
  SELECT
    bsi_action,
    COUNT(DISTINCT user_id) AS bsi_action_ceiling
  FROM
    bsi_user_list
  GROUP BY
    1
),
/* Use the list from bsi_user_list table and join against the regional 
 ASI table of choice (in-market or lifestyle) */
bundled AS (
  SELECT
    bsi.user_id AS bsi_user_id,
    asi.user_id AS asi_user_id,
    bsi.bsi_action,
    asi.segment_marketplace_id,
    asi.segment_name,
    asi.segment_id
  FROM
    bsi_user_list bsi
    INNER JOIN audience_segments_amer_lifestyle asi ON bsi.user_id = asi.user_id
),
/* With the segment and bsi interaction data from the bundled CTE, 
 join and count the number of user_ids by audience segment object */
grouped AS (
  SELECT
    b.bsi_action,
    b.segment_marketplace_id,
    b.segment_name,
    b.segment_id,
    COUNT(DISTINCT b.bsi_user_id) AS cd_bsi_user_id,
    COUNT(DISTINCT b.asi_user_id) AS cd_asi_user_id
  FROM
    bundled b
  GROUP BY
    1,
    2,
    3,
    4
),
/* With the aggregated audience segment and BSI interaction data from grouped 
 - join metadata about the count of BSI events so we can establish 
 the denominator for eventual percentage calculations */
joined AS (
  SELECT
    g.bsi_action,
    g.segment_marketplace_id,
    g.segment_name,
    g.segment_id,
    g.cd_bsi_user_id,
    g.cd_asi_user_id,
    bsiec.bsi_action_ceiling
  FROM
    grouped g
    INNER JOIN bsi_event_counter bsiec ON g.bsi_action = bsiec.bsi_action
)
/* Print the same data as the output of joined from above - 
 but include the percentage metric to establish the segment overlap 
 with the saved vehicle records and number of distinct users associated with the overlap */
SELECT
  j.bsi_action,
  j.segment_marketplace_id,
  j.segment_name,
  j.segment_id,
  j.cd_bsi_user_id,
  j.cd_asi_user_id,
  j.bsi_action_ceiling,
  (j.cd_asi_user_id / j.bsi_action_ceiling) * 100 AS perc_share_of_bsi_associated_users
FROM
  joined j
```

Sample results:
Note: This data is for instructional purposes only. Your results will differ.
Table: Audience Segment Membership (“lifestyle”) analysis



| bsi_action | segment_marketplace_id | segment_name                                               | segment_id | cd_bsi_user_id | cd_asi_user_id | bsi_action_ceiling | perc_share_of_bsi_associated_users |
|------------|------------------------|------------------------------------------------------------|------------|----------------|----------------|--------------------|------------------------------------|
| ATC        | 1                      | LS - Sugar Free Shoppers                                   | 4913       | 555            | 555            | 1239               | 44.79                              |
| ATC        | 1                      | LS - Sports and Outdoors Enthusiasts                       | 166        | 908            | 908            | 1239               | 73.28                              |
| ATC        | 1                      | LS - Premium Shoppers of Skin Care (US)                    | 2223389    | 392            | 392            | 1239               | 31.64                              |
| ATC        | 1                      | LS - NFL Fans                                              | 1348       | 391            | 391            | 1239               | 31.56                              |
| ATC        | 1                      | LS - Mother's Day                                          | 61         | 1097           | 1097           | 1239               | 88.54                              |
| ATC        | 1                      | LS - Green/Environmentally Conscious                       | 31         | 742            | 742            | 1239               | 59.89                              |
| ATC        | 1                      | LS - Grain Free Dog Food Shoppers                          | 17867      | 87             | 87             | 1239               | 7.02                               |
| ATC        | 1                      | LS - Father's Day Gifts                                    | 944        | 1001           | 1001           | 1239               | 80.79                              |
| ATC        | 1                      | LS - Amazon Fresh Salty Snacks Offline Customer Lookalikes | 1032402    | 86             | 86             | 1239               | 6.94                               |
| ATC        | 1                      | LS - Amazon Fashion shoppers                               | 4606       | 948            | 948            | 1239               | 76.51                              |



**Metrics defined**


| metric                             | definition                                                                                                                                                                                                                                                                                                  |
|------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `bsi_action`                         | The filtered and hardcoded Brand Store Insights interaction event referenced in the SQL where filtering criteria. This can be hardcoded or dynamic in order to reflect an event_sub_type or dwell_time threshold.                                                                                           |
| `segment_marketplace_id`             | The marketplace associated with the user-to-segment association, post-filter the export for your intended activation marketplace.                                                                                                                                                                           |
| `segment_name`                       | Name of the Amazon segment associated with the user_id within the analysis                                                                                                                                                                                                                                  |
| `segment_id`                         | Identifier of the Amazon segment associated with the user_id within the analysis                                                                                                                                                                                                                            |
| `cd_bsi_user_id`                     | Distinct count of users from the Brand Store Insights event data table (this should equal `cd_asi_user_id`)                                                                                                                                                                                                   |
| `cd_asi_user_id`                     | Distinct count of users from the ASI dimensional data table (this should equal `cd_bsi_user_id`)                                                                                                                                                                                                              |
| `bsi_action_ceiling`                 | The "ceiling" of Brand Store interaction events that met the filtering criteria for the analyzed time interval. This will be the denominator when assessing the segment overlap as a percentage of total events                                                                                             |
| `perc_share_of_bsi_associated_users` | The share of total users that meet the Brand Store Insights associated with the analysis for a listed segment object. For this IQ-based analysis, the higher percentage, the greater the overlap (consider: include target) and the lower the percentage the smaller the overlap (consider: exclude target) |



**Insights and interpretation**

**Which Amazon segments have the highest correlation with specific high-value Brand Store interactions?**

Based on the sample results above, a campaign leading up to gift giving holidays should consider promoting the notion of “give the gift of eco-friendly products for the fashionable outdoor enthusiasts in your gifting circle”. You can use this analysis to determine which Amazon Ads lifestyle segments overlap best with this historical Brand Store interaction, and then use those insights to inform your audience strategy on Amazon DSP.

Specifically, from the sample analysis export above, advertisers with a media strategy geared towards Brand Store as an add-to-cart conversion objective, reasonable Amazon DSP Lifestyle audiences to add within your line item targeting strategy and creative messaging are below:

* Fashion forward: LS - Amazon Fashion shoppers
* Gifting Planners: LS - Father's Day Gifts & Mother's Day
* Nature friendly: LS - Sports and Outdoors Enthusiasts & LS - Green/Environmentally Conscious

**I would like to influence customers that are similar to existing consumers. Does AMC have any recommendations?**

Within the Brand Store Insights datasets, “dwell_time” can be identified using the dwell_time column values. AMC audiences can be generated using SQL filtering in order to create an Amazon lookalike audience based on Amazon Brand Store customers with the longest dwell times on your Brand Store pages.


### Amazon Brand Store referral source analysis

Use this query to understand where traffic to your Brand Store originates. This query is designed to assess Brand Store event subtype outcomes based on the referral source of the Amazon Brand Store page visit. This query allows you to identify the most performant Sponsored Brands campaign and non Amazon Advertising referral sources for Brand Store page visits and high-value interactions.

Customization ideas:

* Optionally, filter by a different `event_sub_type` (DETAILPAGE, NAVIGATION, PREORDERADDTOCART, PROCEEDTOCHECKOUT, QUICKLOOK)
* Optionally, add a `dwell_time` filter to include only users who spent a certain amount of time viewing the Brand Store.



```
-- Instructional Query: Amazon Brand Store Insights - referral source analysis--
-- Enrollment (trial or subscription) to Amazon Brand Store Insights is required --
/*
 ------- Customization Instructions -------
 1) OPTIONAL UPDATE [1 of 1]: Change 'DETAILPAGE' OR 'ADDTOCART' to alternative event_sub_types within the bsi_interaction_events CTE (e.g. `PREORDERADDTOCART`, `PROCEEDTOCHECKOUT`)
 2) OPTIONAL UPDATE [2 of 2]: Change `reference_id` to `channel` assuming your Brand Store redirect URL has incorporated and populated the channel= URL parameters
 */
WITH bsi_interaction_events AS 
(
SELECT
ingress_type,
reference_id, 
referrer_domain,
IF (event_sub_type= 'DETAILPAGE',1,0) AS dp_counter,
IF (event_sub_type= 'ADDTOCART',1,0) AS atc_counter,
session_id
FROM
amazon_brand_store_engagement_events 
)
, bundled AS
(
SELECT
ingress_type,
reference_id, 
referrer_domain,
SUM(dp_counter) AS sum_dp,
SUM(atc_counter) AS sum_atc,
COUNT(distinct session_id) AS distinct_sessions
FROM bsi_interaction_events
GROUP By 1,2,3
)
SELECT
ingress_type,
reference_id, 
referrer_domain,
sum_dp,
sum_atc,
distinct_sessions,
(sum_dp/distinct_sessions) AS bsi_dp_rate,
(sum_atc/distinct_sessions) AS bsi_atc_rate
FROM bundled
```

**Sample results**

**Note:** This data is for instructional purposes only. Your results will differ.



| ingress_type | reference_id                     | referrer_domain | sum_dp | sum_atc | distinct_sessions | bsi_dp_rate | bsi_atc_rate |
|--------------|----------------------------------|-----------------|--------|---------|-------------------|-------------|--------------|
| 2            | bl_ast_dp_brandLogo_sto          | amazon.com      | 46207  | 1585    | 44899             | 103%        | 4%           |
| 4            | SB_{{campaignID}}-{{creativeID}} | amazon.com      | 9388   | 329     | 18584             | 51%         | 2%           |
| 0            |                                  | tiktok.com      | 146    | 2       | 174               | 84%         | 1%           |
| 0            |                                  | dealmoon.com    | 91     | 0       | 338               | 27%         | 0%           |
|              |                                  | dealnews.com    | 17     | 2       | 93                | 18%         | 2%           |
|              |                                  | facebook.com    | 21     | 0       | 51                | 41%         | 0%           |
|              | SBV_{{campaignID}}               | tamararubin.com | 0      | 0       | 4                 | 0%          | 0%           |
|              |                                  | tomsguide.com   | 13     | 4       | 22                | 59%         | 18%          |
| 2            | bl_ast_dp_brandLogo_sto          |                 | 8431   | 178     | 6344              | 133%        | 3%           |
| 6            | storeRecs_dp_aplus               |                 | 276    | 21      | 417               | 66%         | 5%           |



**Metrics defined**


| metric            | definition                                                                                                                                                                                                                                                                                                                                                           |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ingress_type`      | Enumerated list of store traffic source. Refer to the column descriptor in the AMC UI tool-tip or [Amazon Brand Store insights data sources documentation](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/brand_store_insights_paid#amazonbrandstoreengagementevents-nonendemic).                                           |
| `reference_id`      | Identifier for the ad campaign that is associated with the Brand Store page visit. Refer to the column descriptor in the AMC UI tool-tip or [Amazon Brand Store insights data sources documentation](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/brand_store_insights_paid#amazonbrandstoreengagementevents-nonendemic). |
| `referrer_domain`   | HTML referrer domain from which the user arrived at the Brand Store. Refer to the column descriptor in the AMC UI tool-tip or [Amazon Brand Store insights data sources documentation](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/brand_store_insights_paid#amazonbrandstoreengagementevents-nonendemic).               |
| `sum_dp`            | A counter of the number of DETAILPAGE event_sub_type events that occur within Amazon Brand Store Insights.                                                                                                                                                                                                                                                           |
| `sum_atc`           | A counter for the number of ADDTOCART event_sub_type events that occur within Amazon Brand Store Insights.                                                                                                                                                                                                                                                           |
| `distinct_sessions` | This is the count distinct of session_id column value. A session can span across multiple `visit_id`s and serves as a proxy for number of sessions that could result in a desired KPI. This will serve as a denominator for subsequent caluclated metrics.                                                                                                             |
| `bsi_dp_rate`       | The percentage of BSI sessions that resulted in a DETAILPAGE event_sub_type. A higher `bsi_dp_rate` indicates that a referral source drives consideration outcomes within Brand Stores.                                                                                                                                                                                |
| `bsi_atc_rate`      | The percentage of BSI sessions that resulted in an ADDTOCART event_sub_type. A higher `bsi_atc_rate` indicates that a referral source drives consideration outcomes within Brand Stores.                                                                                                                                                                               |



**Insights and interpretation**

**Which referral sources have the highest correlation with specific high-value Brand Store interactions?**

Based on the export of the analysis from the above, Sponsored Brands display campaigns drive a high detail page view rate but there is a significant decline prior to the add to cart event. The largest source of Brand Store interaction events is the product detail page and the associated “visit brand store” hyperlink. Brand Store traffic referrer by Tom’s Guide (a product review website) has a really strong add-to-cart rate and indicates an informed, brand aware, and primed audience from that web referral source. Consider increasin brand presence on similar product review portals in order to drive highly relevant and qualified traffic to your Brand Store pages.

### AMC audience creation: Create audiences based on customizable Brand Store interaction events

After you have explored Brand Store signals in AMC, use the following query to create an audience based on BSI engagements and dwell time. Utilizing BSI signals in AMC audiences allows you to build a more focused audience using a longer lookback window than the defaults provided by Amazon DSP Audience Builder. 

The following query creates an audience of users that had a dwell time of 5 minutes or more and added to cart.

Customization ideas:
* Optionally, change the `event_sub_type` (`DETAILPAGE`, `NAVIGATION`, `PREORDERADDTOCART`, `PROCEEDTOCHECKOUT`, `QUICKLOOK`)
* Optionally, increase or decrease dwell time (for example, `dwell_time_minutes >= 4`)

**Note:** Copy and paste this query into the Audiences query editor.



```

-- AMC Audience Query: Amazon Brand Store Insights - High-value interaction audience creation --
-- Enrollment (trial or subscription) to Amazon Brand Store Insights is required

/* List all of the BSI user_ids with an add to cart interaction AND at least 5 minutes of dwell time */

WITH bsi_interaction_events AS 
(
SELECT
user_id,
event_sub_type,
COUNT(session_id) as session_counter
FROM
amazon_brand_store_engagement_events_for_audiences 
WHERE event_sub_type= 'ADDTOCART'
AND user_id IS NOT NULL
GROUP By 1,2
)
, bsi_dwell_time AS
(
SELECT
user_id,
ROUND((SUM(dwell_time)/60),0) AS dwell_time_minutes
FROM amazon_brand_store_page_views_for_audiences 
WHERE user_id IS NOT NULL
GROUP By 1
),
grouped AS 
(
SELECT
bdt.user_id,
bie.event_sub_type,
bdt.dwell_time_minutes
FROM bsi_interaction_events bie
INNER JOIN bsi_dwell_time bdt
ON bie.user_id = bdt.user_id
)
SELECT
user_id
FROM
grouped
WHERE 
dwell_time_minutes >=5

```

