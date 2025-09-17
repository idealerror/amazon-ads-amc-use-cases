## Metadata

**id:** f292ef87c4d2628bf56814973ad7657dfd29116b78a0370d3f5ce71c962c8668
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** JOIN or UNION sponsored ads traffic events to conversion events.
**tags:** []
**use_case_categories:** ['How to']
**advertiser_types:** ['On-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## 1. Introduction
### 1.1 Purpose 
This instructional query (IQ) will teach you how to JOIN or UNION sponsored ads (Sponsored Display, Sponsored Brands, Sponsored Products, and Sponsored Television) traffic events to the conversion events in the attributed and custom attribution data sources. 

The **sponsored_ads_traffic** table contains traffic events such as:

* impressions
* clicks

The **amazon_attributed_events_by_traffic_time** table contains conversion events such as:

* purchases
* new_to_brand_purchases
* detail page views

This IQ shows you how to JOIN or UNION these two tables by:

* Joining on `user_id` when you are interested in understanding user behavior. 
* Joining on other fields (not `user_id`) when user journey insights are not required.

### 1.2 Requirements 
Advertisers must run sponsored ads campaigns to use this IQ. 


## 2. Tables Used

* **[sponsored_ads_traffic](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/sponsored_ads_traffic):** This IQ includes impressions and clicks for all sponsored ads ad products, with instructions above the queries to focus on just one or two ad products. 

* **[amazon_attributed_events_by_traffic_time](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/amazon_attributed_events_by_conversion_time):** Sponsored ads uses the date of the traffic and not the date of the conversion to report attributed conversions. Refer to [Attributed events and conversion tables](https://advertising.amazon.com/help/GHNTMYL5DJBJEQC9) in Support Center to learn more. Since the query uses the traffic_time attributed table, run the workflow at least 14 full days after the query’s end date. For example, if the time period you select for the workflow has a start date of 11/1 and an end date of 11/30, you should wait until 12/15 or later to run the query. If you decided to run this workflow to evaluate performance during the month of November on 12/1, then query will run, but you will exclude ad-attributed sales from customers that were exposed to the ad during the last two weeks of the flight who will go on to purchase up until 12/14.

* **[conversions_with_relevance](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/conversions_with_relevance)**: Use this data source to identify all users who converted and were delivered a sponsored ads impression within 28 days of the conversion. To learn more about this data source, view the [custom attribution documentation IQ](/marketing-cloud/instructional-queries/72313993a488c7c1821e67c6fdd7a366cd434ef65ea14a2aae4d96fd16a2e627). 





## 3. JOIN based on the user_id
The query templates in this section should be used when you are interested in understanding user behavior between multiple campaigns or ad product types. 

### 3.1 JOIN traffic with attributed events
The overlap queries are examples of joining sponsored ads traffic with the `amazon_attributed_events_by_traffic_time` using the `user_id`. View examples here:

* [Sponsored Ads and DSP Overlap](/marketing-cloud/instructional-queries/dfc41258bf5adb1ac1b1d7aa5b734b8af4a2e9b3487468ef84ebafbff096ad22)
* [DSP Display and Streaming TV Overlap](/marketing-cloud/instructional-queries/8176bcc97eb7cdac4345ae62bf539dca86ec877af19a7a43d340b012a530502f)
* [Sponsored Products and DSP Overlap](/marketing-cloud/instructional-queries/5f82ae6822efd44936e51034147238712b0f7fd945507aad3a9d2f5a7d0ce580)
* [Sponsored Products and Sponsored Display Overlap](/marketing-cloud/instructional-queries/b393fd9fa60bae71476e111bd8e122adba65525c48a9889fe08a08d37ba13bff)
* [Sponsored Display and DSP Overlap](/marketing-cloud/instructional-queries/27dd225a890c2cfa2d8d2b733ecbf3075df94fd0866b08a3f9af31fd0b2d9392)




### 3.2. JOIN traffic with custom attribution events

The [Custom Attribution Documentation](/marketing-cloud/instructional-queries/72313993a488c7c1821e67c6fdd7a366cd434ef65ea14a2aae4d96fd16a2e627) IQ provide examples of joining `sponsored_ads_traffic` with `conversions_with_relevance`. 

1. First, view the example query in **section 3.1** of the Custom Attribution Documentation IQ for a simple example of joining sponsored_ads_traffic to conversions_with_relevance. 

2. Then view each of the four custom attribution IQs for more examples of JOINs with `sponsored_ads_traffic` with `conversions_with_relevance`:
    * [Custom Attribution - First Touch](/marketing-cloud/instructional-queries/578a0a9ec3875507beade48f562e1ae6189fbf6ea203a7d34508cb2e24c6d875)
    * [Custom Attribution - Last Touch](/marketing-cloud/instructional-queries/e793be327be2aa9f4fbdbdcaf3f3f822ed8819440beb15f94b377b07b08cf0bf)
    * [Custom Attribution - Linear](/marketing-cloud/instructional-queries/95212a0253ea4d060cd94db10de7d3dfd542232fa786e1d74852ae3d8bd58139)
    * [Custom Attribution - Custom Attribution - Position Based](/marketing-cloud/instructional-queries/cdf597257dcf43b65c1af8dd14480433e4dbe513469575e8b064f62029c94e9b)



## 4. Other examples (not joining on user_id)

The query templates below are helpful when your insights do not require user journey signals. The queries should be used for single-dimension insights where your desired output is the sum of impressions, clicks, and attributed purchases by campaign, for example. Unlike the query templates provided in section 3, the query templates below do not JOIN on the `user_id`. 

### 4.1 Query template: UNION ALL to combine tables
A UNION is the simplest way to combine sponsored ads traffic and attributed events when user journey insights, such as overlap insights, are not required. The example below includes two traffic events (impressions and clicks) and two attributed events (purchases and new_to_brand_purchases). 

By default, all sponsored ads ad products will be queried. Search for 'OPTIONAL UPDATE' exclude or two ad products. 


```
-- Instructional Query: UNION Sponsored Display and Sponsored Products traffic with attributed events --
-- AD PRODUCTS: Sponsored ads

WITH ad_products (ad_product_type) AS (
  VALUES
  /*OPTIONAL UPDATE: Remove ad product(s) to focus on just one or two Sponsored Ads ad products.*/
    ('sponsored_products'),
    ('sponsored_brands'),
    ('sponsored_display'),
    ('sponsored_television')
),

traffic AS (
  SELECT
    campaign,
    campaign_id_string,
    ad_product_type,
    SUM(impressions) AS impressions,
    SUM(clicks) AS clicks,
    0 AS purchases,
    0 AS new_to_brand_purchases
  FROM
    sponsored_ads_traffic
  WHERE
    ad_product_type in (select ad_product_type from ad_products)
  GROUP BY
    1,
    2,
    3
),
attributed_events AS (
  SELECT
    campaign,
    campaign_id_string,
    ad_product_type,
    0 AS impressions,
    0 AS clicks,
    SUM(purchases) AS purchases,
    SUM(new_to_brand_purchases) AS new_to_brand_purchases
  FROM
    amazon_attributed_events_by_traffic_time
  WHERE
    ad_product_type in (select ad_product_type from ad_products)
  GROUP BY
    1,
    2,
    3
),
combined AS (
  SELECT
    campaign,
    campaign_id_string,
    ad_product_type,
    impressions,
    clicks,
    purchases,
    new_to_brand_purchases
  FROM
    traffic
  UNION ALL
  SELECT
    campaign,
    campaign_id_string,
    ad_product_type,
    impressions,
    clicks,
    purchases,
    new_to_brand_purchases
  FROM
    attributed_events
)
SELECT
  campaign,
  campaign_id_string,
  ad_product_type,
  SUM(impressions) AS impressions,
  SUM(clicks) AS clicks,
  SUM(purchases) AS purchases,
  SUM(new_to_brand_purchases) AS new_to_brand_purchases
FROM
  combined
GROUP BY
  1,
  2,
  3
```

### 4.2 Query template - JOIN example

It’s also possible to JOIN sponsored ads traffic to the attributed events using the `event_id` / `traffic_event_id`. The query below will produce the same output as the query above. There is not an advantage to using one approach over the other. 

However, we recommend the UNION approach, since the `event_id` / `traffic_event_id` might be duplicated. JOINING without first grouping by the `event_id` / `traffic_event_id` could produce inflated results. The query below accounts for this by creating two CTEs that group by the `event_id` / `traffic_event_id`. 

By default, all sponsored ads ad products will be queried. Search for 'OPTIONAL UPDATE' exclude or two ad products. 



```
-- Instructional Query: JOIN sponsored ads traffic with attributed events USING event_id / traffic_event_id --
-- AD PRODUCTS: Sponsored ads
-- Create a CTE that groups by the event_id and campaign.

WITH ad_products (ad_product_type) AS (
  VALUES
  /*OPTIONAL UPDATE: Remove one ad product type to only focus on one.*/
    ('sponsored_products'),
    ('sponsored_brands'),
    ('sponsored_display'),
    ('sponsored_television')
),

traffic AS (
  SELECT
    campaign,
    campaign_id_string,
    ad_product_type,
    event_id,
    SUM(impressions) AS impressions,
    SUM(clicks) AS clicks
  FROM
    sponsored_ads_traffic
  WHERE
      ad_product_type in (select ad_product_type from ad_products)
  GROUP BY
    1,
    2,
    3,
    4
) 
-- Create a CTE that groups by the traffic_event_id and campaign.
,
attributed_events AS (
  SELECT
    campaign,
    campaign_id_string,
    ad_product_type,
    traffic_event_id,
    SUM(purchases) AS purchases,
    SUM(new_to_brand_purchases) AS new_to_brand_purchases
  FROM
    amazon_attributed_events_by_traffic_time
  WHERE
    ad_product_type in (select ad_product_type from ad_products)
  GROUP BY
    1,
    2,
    3,
    4
)
SELECT
  COALESCE(t.campaign, a.campaign) AS campaign,
  COALESCE(t.campaign_id_string, a.campaign_id_string) AS campaign_id_string,
  COALESCE(t.ad_product_type, a.ad_product_type) AS ad_product_type,
  COALESCE(SUM(impressions), 0) AS impressions,
  COALESCE(SUM(clicks), 0) AS clicks,
  COALESCE(SUM(purchases), 0) AS purchases,
  COALESCE(SUM(new_to_brand_purchases), 0) AS new_to_brand_purchases
FROM
  traffic t
  FULL OUTER JOIN attributed_events a ON event_id = traffic_event_id
GROUP BY
  1,2, 3
```

### Example results from queries 4.1 and 4.2
*This data is for instructional purposes only. Your results will differ.*

Results from both query templates will be identical. 

|campaign| campaign_id_string |ad_product_type|impressions|clicks|purchases|new_to_brand_purchases|
|:---|:---|:---|:---|:---|:---|:---|
|Kitchen Smart &#x7c; SD &#x7c; All Products &#x7c; Awareness|A00011111111111111111|sponsored_display|943,757|3,187|944|920|
|Kitchen Smart &#x7c; SD &#x7c; Toasters &#x7c; Awareness|A000222222222222|sponsored_display|1,522,365|3,806|1,066|1,002|
|Kitchen Smart &#x7c; SP &#x7c; Blenders &#x7c; Category Views &#x7c; ASIN56789|A000333333333333|sponsored_products|1,830,030|2,629|732|620|
|Kitchen Smart &#x7c; SP &#x7c; Blenders &#x7c; Remarketing &#x7c; ASIN12345|A000444444444444|sponsored_products|3,812,034|4,295|3,812|101|
|Kitchen Smart &#x7c; SB &#x7c; Blenders &#x7c; Remarketing &#x7c; ASIN12345|A0005555555555555|sponsored_brands|4,811,022|5,991|4,801|205|

