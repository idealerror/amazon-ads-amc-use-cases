## Metadata

**id:** cdf597257dcf43b65c1af8dd14480433e4dbe513469575e8b064f62029c94e9b
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Analyze the performance of your campaigns based on position based attribution model.
**tags:** []
**use_case_categories:** ['Custom attribution']
**advertiser_types:** ['On-Amazon conversions', 'Off-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

# Instructional Query Revision: Custom attribution - Position Based

## 1. Introduction

### 1.1 Purpose

On the path to conversion, customers may interact with multiple ads from the same advertiser. Attribution models let you choose how much credit each ad interaction gets for your conversions. Different attribution models can provide you with a better understanding of how your ads perform and help you optimize across conversion journeys. 

This instructional query (IQ) provides insights on customers’ first and last touchpoints by splitting the credit between both touchpoints on your customers’ conversion path. Specifically, the position-based query gives 30% credit to the first touchpoint and 70% credit to the last touchpoint. The conversion counts reported per campaign are weighted sums, with 30% assigned to the first touchpoint and 70% assigned to the last touchpoint by default. Weights assigned to these two touchpoints are customizable. For example, you can split the credit evenly (50%/50%) or assign more credit to the first touch point. Work with your campaign or media manager to understand the objective of each campaign or media group and assign the appropriate credit weight to each touchpoint. Additionally, this IQ also provides you with the flexibility to customize the lookback window vs. the default 14 day attribution window.

Please note that position-based custom attribution does not provide the number of times each campaign received credit as the first or last touchpoint. If you are interested in this level of detail, consider running the first and last touch queries linked in section 1.2. Additionally, position-based attribution is different from Amazon’s Standard Attribution Model from the `amazon_attributed_events_by_traffic_time` and `amazon_attributed_events_by_conversion_time` tables. For more details on the similarities and differences between the two data sources, please refer to the [Custom Attribution Documentation](/marketing-cloud/instructional-queries/72313993a488c7c1821e67c6fdd7a366cd434ef65ea14a2aae4d96fd16a2e627).

### 1.2 Standard reporting vs custom attribution in AMC

| Category | Standard Reporting for Amazon DSP and Sponsored Ads | Custom Attribution in AMC | To Align with Standard Reporting |
|----------|--------------------------------------------------|------------------------|--------------------------------|
| Eligible traffic events | Amazon DSP campaigns allow attribution to clicks and views. sponsored ads allows only clicks for CPC billing (i.e., most sponsored ads campaigns), and clicks and views for vCPM or Fixed Price. | Default is clicks only for Sponsored Products, Sponsored Brands, and Sponsored Display. Default is clicks and views for Amazon DSP. For non-view-aware placements, impressions are included by default. Traffic events can be customized for each ad type to include clicks, impressions, or both. | Retain the default of click-only attribution for sponsored ads and click and view attribution for Amazon DSP. sponsored ads CPC placements typically appear in high-intent shopping contexts, where ads are highly viewable and shoppers are already close to converting. Attributing conversions to views in these cases can lead to overcrediting and will shift credit away from ads that directly influenced the purchase. |
| Eligible conversion events | Eligible conversion events are determined by campaign configuration. Conversions for all tracked off-Amazon events, tracked product ASINs, and other ASINs from the same brands can be attributed. "Tracked" refers to events and products associated with the campaign during setup. | Default is all ASINs or Off-Amazon Conversion events relevant to the campaigns. Filtering to fewer ASINs or events is customizable. | Retain default inclusion of all relevant ASINs and events manager signals. The default template in the IQ will capture conversions that were indicated as relevant during campaign setup. |
| Attribution window | 14 days (7 days for Sponsored Products for Sellers) | Default is 28 days. Up to 28 days is customizable | 14 days |
| Credit assignment | Amazon's last touch attribution considers each campaign's relevance to the converted product, then uses a last-touch model that prioritizes the last click over the last view. Amazon uses this ranking for last-touch attribution: \* Promoted ASIN ad clicks, \* Highly relevant\*\* brand halo ad clicks, \* Promoted ASIN ad views, \* Highly relevant\*\* brand halo ad views, \* Brand halo ad clicks, \* Brand halo ad views | Default is clicks prioritized over views (or impressions for non-view-aware placements). Customizable to choose last touch, position based, linear, or first touch | Last touch with default of clicks prioritized over views/impressions aligns closely with standard attribution outcomes. |

** Highly relevant refers to ASINs from the same brand and product subcategory as promoted ASINs

### 1.3  Other Custom Attribution IQs - Quick links

[First Touch Attribution](/marketing-cloud/instructional-queries/578a0a9ec3875507beade48f562e1ae6189fbf6ea203a7d34508cb2e24c6d875) | [Last Touch Attribution](/marketing-cloud/instructional-queries/e793be327be2aa9f4fbdbdcaf3f3f822ed8819440beb15f94b377b07b08cf0bf) | [Linear Attribution](/marketing-cloud/instructional-queries/95212a0253ea4d060cd94db10de7d3dfd542232fa786e1d74852ae3d8bd58139)

### 1.4 Requirements

Advertisers should preferably have data from multiple ad campaigns. If you only have one active ad campaign, the query will return results, but they will be limited to the single ad campaign and all the credit will go toward that single ad campaign. The query templates can be used for both ASIN and off-Amazon conversion advertisers.


## 2. Query Instructions

### 2.1 Tables used

* [dsp_impressions](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/dsp_impressions): We select Amazon DSP impressions from this table.
* [dsp_clicks](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/dsp_impressions): We select Amazon DSP clicks from this table.
* [sponsored_ads_traffic](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/sponsored_ads_traffic): We select sponsored ads impressions and clicks from this table. If you do not have sponsored ads campaigns, the query template has instructions to remove this section to run the query over your Amazon DSP campaigns only.
* [conversions_with_relevance](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/conversions_with_relevance): This table contains combinations of conversion events and campaigns that the conversion was considered relevant to. This data source contains the same conversion events from the `conversions` data source but is expanded such that each row represents a unique combination of conversion event, advertiser, campaign, and relevance. Please note that a conversion could be repeated multiple times if it is relevant to multiple campaigns. We are using this table because it contains the campaign relevance information needed to join traffic events to conversion events and to prioritize traffic events. Please refer to the [Custom Attribution Documentation](/marketing-cloud/instructional-queries/72313993a488c7c1821e67c6fdd7a366cd434ef65ea14a2aae4d96fd16a2e627) for more information on this table.
* [dsp_views](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/dsp_views): This table is used by default to attribute based on Amazon DSP views. The query template has instructions to make the query non-view aware.

### 2.2 Query template

Query templates are located Sections 4 and 5. Exploratory queries are provided in Section 4 guide you through the process of selecting appropriate query filters for campaigns (required) and ASINs (optional, but recommended for ASIN conversion advertisers).
The custom attribution query template in Section 5 has detailed instructions on changing the following settings:

* **Customize weights:** You may customize the weights assigned to the two touchpoints by following the instructions in the query template. The template assigned 30% of credit to the first touchpoint and 70% to the last touchpoint. You can work with your campaign or media manager to understand the objective of each campaign or media group and assign the appropriate credit weight to each touchpoint.
* **Ad product type combinations**: The query template by default considers Amazon DSP and sponsored ads campaigns. Instructions are provided to focus on Amazon DSP only, Amazon DSP and sponsored ads, or sponsored ads only.
* **Off-Amazon conversions**: There is an option to change the conversion metric to off-Amazon conversions.
* **Adjust the lookback window**: You have the option to include impression and click events from up to X days before conversion events. The default setting is 28 days. It's not recommended to extend the window beyond 28 days for sponsored ads or for Amazon DSP campaigns that promote ASINs, but you can extend this for off-Amazon conversions. Please refer to the [Custom Attribution Documentation](/marketing-cloud/instructional-queries/72313993a488c7c1821e67c6fdd7a366cd434ef65ea14a2aae4d96fd16a2e627) for more information.
* **Choose your traffic qualifications**: Do you want to attribute based on impressions or clicks or both? Both are considered by default for Amazon DSP. Clicks are considered by default for sponsored ads. The query template has instructions to adjust this setting.
* **Consider viewability for Amazon DSP campaigns**: The query template considers viewability by default for Amazon DSP campaigns. You have the option to remove the Amazon DSP view-aware section. If you remove this section, the query will not consider viewability. Note that the attributed conversions from `amazon_attributed_events_by_traffic_time` and `amazon_attributed_events_by_conversion_time` consider viewability. If a user was delivered an impression that was not viewable and then converted within a 14-day time period, this conversion would not meet the qualifications to be attributed to a campaign. However, this conversion would appear in the custom attribution if other requirements are satisfied.

## 3. Data interpretation instructions

### 3.1 Example query results

| attribution\_model | campaign\_id\_string | campaign | impressions | clicks | user\_reach | users\_that\_converted | conversions | conversion\_rate | total\_units\_sold | total\_product\_sales |
|-------------------|-------------------|-----------|-------------|--------|------------|---------------------|-------------|----------------|------------------|-------------------|
| Position Based | 2009197703011 | Accent Athletics\_Q2-Q3'21 - Display ads - Amazon DSP - Conversion | 4,159,755 | 2,797 | 3,546,456 | 13,254 | 15,905 | 0.37% | 9,639 | 260,253 |
| Position Based | 2456167530601 | Accent Athletics\_Q2-Q3'21 - Display ads - Amazon DSP - Awareness | 3,794,695 | 1,546 | 1,853,009 | 5,204 | 10,408 | 0.28% | 26,020 | 520,400 |
| Position Based | 7286325070001 | Accent Athletics\_Streaming TV\_Q2-Q3'21 - Streaming TV - Amazon Guarantee - Awareness | 2,643,156 | 0 | 1,750,046 | 5,102 | 12,755 | 0.29% | 19,133 | 478,313 |
| Position Based | 8697685000701 | Accent Athletics\_Q2-Q3'21 - Display ads - Amazon DSP - Consideration | 2,813,247 | 6,546 | 1,234,788 | 150 | 225 | 0.01% | 675 | 16,875 |

### 3.2 Metrics Defined

| metric                | definition |
|----------------------|------------|
| impressions          | Number of impressions each campaign received over the campaign period. |
| clicks               | Number of clicks each campaign received over the campaign period. |
| user\_reach          | Number of unique users each campaign reached. |
| users\_that\_converted | Number of unique users who converted. When event\_category = 'purchase', conversions are purchase conversions and this metric is the distinct count of users who purchased. |
| conversions          | Number of conversions over the campaign period. When event\_category = 'purchase', conversions are purchase conversions. |
| conversion\_rate     | Number of distinct users that converted divided by all users reached by the ad. When event\_category = 'purchase', conversions are purchase conversions and this conversion rate is the purchase rate. |
| total\_units\_sold   | The total quantity of promoted products and products from the same brand as promoted products purchased by customers on Amazon after delivering an ad. A campaign can have multiple units sold in a single purchase event. |
| total\_product\_sales | The total sales (in local currency) of promoted ASINs and ASINs from the same brands as promoted ASINs purchased by customers on Amazon after delivering an ad. |

### 3.3 Insights and data interpretation

The position-based model shows that the campaign that was focused on driving conversions (Accent Athletics_Q2-Q3'21 - Display ads - Amazon DSP - Conversion) had the highest conversion rate at 0.37%. It likely has the highest conversion rate because conversion-focued campaigns tend to be the last touchpoint in the conversion path. 

According to the position-based model, the last touch earns 70% of the credit for the conversion. The two awareness campaigns have conversion rates of 0.28% and 0.29%. It's likely that the awareness campaigns were more likely to be the first touch point in the conversion path and earn 30% of the credit for the conversion. 

Consider also changing the weights to 50% each in this IQ. Also, consider using the [First Touch Attribution IQ](/marketing-cloud/instructional-queries/578a0a9ec3875507beade48f562e1ae6189fbf6ea203a7d34508cb2e24c6d875) and the [Last Touch Attribution IQ](/marketing-cloud/instructional-queries/e793be327be2aa9f4fbdbdcaf3f3f822ed8819440beb15f94b377b07b08cf0bf) to supplement this analysis and compare results.

Note that the consideration campaign had the lowest number of conversions, possibly because it was most likely to be a touchpoint other than the first and the last. Consider running the [Linear Attribution IQ](/marketing-cloud/instructional-queries/95212a0253ea4d060cd94db10de7d3dfd542232fa786e1d74852ae3d8bd58139) linked to get a more balanced view of how the consideration campaign performed.


## 4. Exploratory queries

Exploratory queries help you to understand what campaigns were running during a given time period and what ASINs or off-Amazon conversions were tracked. The two queries in Section 4 do not provide the attributed results. This query is in section 5.

### 4.1 Exploratory query for advertisers with ASIN conversions

Advertisers who track ASINs should run the query below to explore the campaigns and ASINs available in your instance for analysis. This step will help you to select the appropriate filters to add to the rule-based attribution query in Section 5.



```
-- Instructional Query: custom attribution exploratory query for ASIN conversion advertisers --
WITH traffic AS (
  SELECT
    DISTINCT campaign,
    campaign_id_string,
    ad_product_type,
    user_id
  FROM
    TABLE(
      EXTEND_TIME_WINDOW('sponsored_ads_traffic', 'P28D', 'P0D')
    )
  WHERE
    user_id IS NOT NULL
  UNION ALL
  SELECT
    DISTINCT campaign,
    campaign_id_string,
    'dsp' AS ad_product_type,
    user_id
  FROM
    TABLE(
      EXTEND_TIME_WINDOW('dsp_impressions', 'P28D', 'P0D')
    )
  WHERE
    user_id IS NOT NULL
),
purchase_conversions AS (
  SELECT
    tracked_item AS ASIN,
    engagement_scope,
    campaign,
    campaign_id_string,
    user_id,
    SUM(conversions) AS purchases
  FROM
    conversions_with_relevance c
    /*filter to only include tracked items that are ASINs and exclude other tracked_item values, 
     such as Amazon DSP branded keyword searches and off-Amazon conversion UUIDs.*/
  WHERE
    halo_code IN (
      'TRACKED_ASIN',
      'VARIATIONAL_ASIN',
      'BRAND_MARKETPLACE',
      'BRAND_GL_PRODUCT',
      'BRAND_CATEGORY',
      'BRAND_SUBCATEGORY'
    )
    AND event_category = 'purchase'
    AND c.user_id IS NOT NULL
  GROUP BY
    1,
    2,
    3,
    4,
    5
),
combined AS (
  SELECT
    ASIN,
    engagement_scope,
    t.campaign,
    ad_product_type,
    SUM(purchases) AS purchases,
    COUNT(DISTINCT c.user_id) AS users_that_purchased
  FROM
    purchase_conversions c
    JOIN traffic t ON c.campaign_id_string = t.campaign_id_string
    AND c.user_id = t.user_id
  GROUP BY
    1,
    2,
    3,
    4
),
dsp_campaign_ids AS (
  SELECT
    DISTINCT campaign,
    campaign_id_string
  FROM
    TABLE(
      EXTEND_TIME_WINDOW('dsp_impressions', 'P28D', 'P0D')
    )
)
SELECT
  ASIN,
  engagement_scope,
  c.campaign,
  d.campaign_id_string,
  ad_product_type,
  purchases,
  users_that_purchased
FROM
  combined c
  LEFT JOIN dsp_campaign_ids d ON c.campaign = d.campaign
```

### 4.2 Exploratory query for advertisers with off-Amazon conversions

Advertisers who track off-Amazon conversions should run the query below to explore the campaigns available in your instance for analysis. This step will help you to select the appropriate filters to add to the position-based attribution query. The query below is for exploratory purposes only and will pull conversions from users that had both viewable and non-viewable impressions.



```
-- Instructional Query: custom attribution exploratory query for off-Amazon conversion advertisers --
WITH traffic AS (
  SELECT
    DISTINCT campaign,
    campaign_id_string,
    user_id
  FROM
    TABLE(
      /* OPTIONAL UPDATE: This exploratory query selects all campaigns that ran 60 days before the query time window. 
       You can adjust this based on your use case by changing 'P60D' to 'P30d', for example.
       Unlike ASIN conversions, off-Amazon conversions are not subject to the 28-day lookback window.*/
      EXTEND_TIME_WINDOW('dsp_impressions', 'P60D', 'P0D')
    )
  WHERE
    user_id IS NOT NULL
)
SELECT
  tracked_item AS off_amazon_ID,
  event_category,
  c.campaign,
  c.campaign_id_string,
  SUM(conversions) AS conversions,
  COUNT(DISTINCT c.user_id) AS users_that_converted
FROM
  conversions_with_relevance c
  JOIN traffic t ON t.campaign = c.campaign
  AND t.user_id = c.user_id
WHERE
  event_category = 'off-amazon'
  AND c.user_id IS NOT NULL
GROUP BY
  1,
  2,
  3,
  4
```

## 5. Custom attribution query template - Position-based attribution

The query below by default works over Sponsored Products and Amazon DSP campaigns. It can be customized for off-Amazon conversion advertisers, Amazon DSP-only advertisers, and for sponsored ads only. The technical customization instructions are included at the top of the query and are also summarized in Section 2.2.



```
-- Instructional Query: Custom attribution - position based - 30% first touch, 70% last touch --
-- Search 'UPDATE' to identify all filters to update.
/*
 ------- Customization Instructions -------
 1) All advertisers should run the exploratory query first to select the appropriate campaign filters (required) and ASIN filters 
 (optional, but recommended for ASIN advertisers).
 Campaigns are updated at the top of the query. Search 'ASIN' to find ASIN filters.
 2) To run this query across Amazon DSP and sponsored ads campaigns, search 'UPDATE' to identify all filters to update. Do not remove any sections.
 3) To run this query across Amazon DSP campaigns only, search 'sponsored ads-specific'. Delete the sponsored ads sections.
 Then search 'UPDATE' to update the remaining sections of the query.
 4) To run this query across Amazon DSP campaigns only for off-Amazon conversions, follow the instructions from #3.
 Then search for 'off-amazon' in this query to change the conversion metric to off-Amazon conversions.
 You have the option to remove the calculated value for total_units_sold and total_product_sales, 
 since they will not return values for off-Amazon conversions.
 5) This query is view-aware by default for Amazon DSP campaigns. 
 To make this query non-view-aware for Amazon DSP campaigns, search 'DSP view-aware'. Remove this section.
 6a) This query considers views and clicks for Amazon DSP.
 The query will prioritize clicks over impressions. To only consider clicks for Amazon DSP attribution, search for 'UPDATE DSP Traffic Qualifications'.
 6b) This query considers clicks only for Sponsored Products.
 If you want to consider both clicks and impressions for Sponsored Products and prioritize clicks over impressions, search for 'UPDATE SP Traffic Qualifications'.
 6c) This query considers clicks only for Sponsored Display.
 If you want to consider both clicks and impressions for Sponsored Display and prioritize clicks over impressions, search for 'UPDATE SD Traffic Qualifications'.
 6d) This query considers clicks only for Sponsored Brands.
 If you want to consider both clicks and impressions for Sponsored Brands and prioritize clicks over impressions, search for 'UPDATE SB Traffic Qualifications'.
 This query considers clicks and impressions for Sponsored TV. Note: Clicks and impressions are not deduplicated for the same Sponsored TV impression.
 6e) To change the traffic priority and make clicks and impression equal, search 'UPDATE traffic priority'.
 7) To run this query across sponsored ads campaigns only, search 'DSP-specific'. Delete the three DSP sections. 
 Then search 'UPDATE' to update the remaining sections of the query.
 8) To change the lookback window from the default setting of 28 days, search 'UPDATE Lookback Window' for instructions.
 9) The default query setting is 30% credit for the first touch and 70% credit for the last touch. 
 To change this, search 'UPDATE weights' and update both locations.
 */
WITH ---------- start DSP-specific section 1 of 3 ----------
-- REQUIRED UPDATE DSP Campaigns: Add values for the DSP campaign_id_string(s)
dsp_campaigns (campaign_id_string) AS (
  VALUES
    ('1111111111'),
    ('2222222222'),
    ('3333333333'),
    ('4444444444')
),
dsp_traffic_type (traffic_event_type) AS (
  /* OPTIONAL UPDATE DSP Traffic Qualifications: Delete '('IMP'),' below to attribute based on DSP clicks only. 
   To attribute based on the most recent traffic event (either the impression or click), do not make any changes below.*/
  VALUES
    ('IMP'),
    ('CLICK')
),
---------- end DSP-specific section 1 of 3 ----------
---------- start sponsored ads-specific section  1 of 3 ----------
sp_campaigns (campaign) AS (
  /* REQUIRED UPDATE: Add sponsored ads campaign names. Use the query below if you do not have sponsored ads campaigns.*/
  VALUES
    ('campaign_name_1'),
    ('campaign_name_2'),
    ('campaign_name_3'),
    ('campaign_name_4')
),
sd_campaigns (campaign) AS (
  /* REQUIRED UPDATE: Add sponsored ads campaign names. Use the query below if you do not have sponsored ads campaigns.*/
  VALUES
    ('campaign_name_1'),
    ('campaign_name_2'),
    ('campaign_name_3'),
    ('campaign_name_4')
),
sb_campaigns (campaign) AS (
  /* REQUIRED UPDATE: Add sponsored ads campaign names. Use the query below if you do not have sponsored ads campaigns. */
  VALUES
    ('campaign_name_1'),
    ('campaign_name_2'),
    ('campaign_name_3'),
    ('campaign_name_4')
),
st_campaigns (campaign) AS (
  /* REQUIRED UPDATE: Add sponsored ads campaign names. Use the query below if you do not have sponsored ads campaigns.*/
  VALUES
    ('campaign_name_1'),
    ('campaign_name_2'),
    ('campaign_name_3'),
    ('campaign_name_4')
),
sp_traffic_type (traffic_event_type) AS ( /* OPTIONAL UPDATE SP Traffic Qualifications: Uncomment '('IMP'),' below to attribute based on SP clicks + impressions. This is not recommended. The default setting is to keep clicks only.*/
  VALUES
    -- ('IMP'),
    ('CLICK')
),
sd_traffic_type (traffic_event_type) AS ( /* OPTIONAL UPDATE SD Traffic Qualifications: Uncomment '('IMP'),' below to attribute based on SD clicks + impressions. This is not recommended. The default setting is to keep clicks only.*/
  VALUES
    -- ('IMP'),
    ('CLICK')
),
sb_traffic_type (traffic_event_type) AS ( /* OPTIONAL UPDATE SB Traffic Qualifications: Uncomment '('IMP'),' below to attribute based on SB clicks + impressions. This is not recommended. The default setting is to keep clicks only.*/
  VALUES
    -- ('IMP'),
    ('CLICK')
),
st_traffic_type (traffic_event_type) AS ( /* OPTIONAL UPDATE ST Traffic Qualifications: Comment '('CLICK'),' below to attribute based on ST impressions only. The default setting is to keep clicks + impressions.*/
  VALUES
    ('IMP'),
    ('CLICK')
),
---------- end sponsored ads-specific section 1 of 3 ----------
traffic AS (
  ---------- start DSP-specific section 2 of 3 ----------
  -- Deduplicate DSP clicks and views for the same impression
  SELECT
    user_id,
    'dsp' AS ad_product_type,
    campaign_id_string,
    campaign,
    traffic_dt_utc,
    traffic_event_type,
    impressions,
    clicks
  FROM (
      SELECT
        user_id,
        campaign_id_string,
        campaign,
        traffic_dt_utc,
        traffic_event_type,
        impressions,
        clicks,
        -- Prioritize clicks over views for the same impression
        ROW_NUMBER() OVER (
          PARTITION BY user_id, campaign_id_string, request_tag
          ORDER BY 
            CASE 
              WHEN traffic_event_type = 'CLICK' THEN 1
              WHEN traffic_event_type = 'IMP' THEN 2
              ELSE 3
            END ASC,
            traffic_dt_utc DESC
        ) as event_priority
          FROM (
            -- Collect all DSP events
            SELECT
              user_id,
              'dsp' AS ad_product_type,
              campaign_id_string,
              campaign,
              request_tag,
              impression_dt_utc as traffic_dt_utc,
              'IMP' AS traffic_event_type,
              impressions,
              0 AS clicks
            FROM
                      /* OPTIONAL UPDATE Lookback Window: Include impression events from up to X days before conversion events. The setting below is 28 days.
          It's not recommended to extend the window beyond 28 days for ASIN conversion advertisers because the conversions in conversions_with_relevance are 
          only included if a customer was served a traffic event over the last 28 days. Search 'custom attribution' in the instructional query library to learn more.
          Off-Amazon conversion advertisers have the option extend this beyond 28 days. If updating, search 'TABLE(EXTEND_TIME_WINDOW' and update in all locations in this query. 
          And also search 'match_age BETWEEN 0 AND 28' to update this as well.*/
              TABLE(
                EXTEND_TIME_WINDOW('dsp_impressions', 'P28D', 'P0D')
                )
            WHERE
              campaign_id_string IN (
                SELECT campaign_id_string FROM dsp_campaigns
                )
              AND user_id IS NOT NULL ---------- start DSP view-aware section ----------
    -- Only include impressions for non-view-aware placements. For view-aware placements, we will use view events.
              AND NOT COALESCE(supply_source_is_view_aware, TRUE)
              AND NOT COALESCE(placement_is_view_aware, TRUE)
              AND RANDOM() < COALESCE(placement_view_rate, supply_source_view_rate, 1.0)
            
            UNION ALL
            
            SELECT
              user_id,
              'dsp' AS ad_product_type,
              campaign_id_string,
              campaign,
              impression_id AS request_tag,
              event_dt_utc as traffic_dt_utc,
              'IMP' AS traffic_event_type,
              viewable_impressions + unmeasurable_viewable_impressions AS impressions,
              0 AS clicks
            FROM
                /* OPTIONAL UPDATE Lookback Window: Include impression events from up to X days before conversion events. The setting below is 28 days.
     It's not recommended to extend the window beyond 28 days for ASIN conversion advertisers because the conversions in conversions_with_relevance are only 
     included if a customer was served a traffic event over the last 28 days. Search 'custom attribution' in the instructional query library to learn more.
     Off-Amazon conversion advertisers have the option extend this beyond 28 days. If updating, search 'TABLE(EXTEND_TIME_WINDOW' and update in all locations in this query. 
     And also search 'match_age BETWEEN 0 AND 28' to update this as well. */
              TABLE(
                EXTEND_TIME_WINDOW('dsp_views', 'P28D', 'P0D')
                )
            WHERE
                -- dsp_views contains other types of events such as measurable impressions, so we filter to just views

              (event_type = 'VIEW' OR event_type = 'SYNTHETIC_VIEW')
              AND campaign_id_string IN (SELECT campaign_id_string FROM dsp_campaigns)
              AND user_id IS NOT NULL ---------- end DSP view-aware section ----------
            
            UNION ALL
            
            SELECT
              -- Read DSP click events.
              user_id,
              'dsp' AS ad_product_type,
              campaign_id_string,
              campaign,
              request_tag,
              impression_dt_utc as traffic_dt_utc,
              'CLICK' AS traffic_event_type,
              0 AS impressions,
              clicks
            FROM
                /* OPTIONAL UPDATE Lookback Window: Include click events from up to X days before conversion events. The setting below is 28 days. 
     It's not recommended to extend the window beyond 28 days for ASIN conversion advertisers. Off-Amazon conversion advertisers have the option extend this beyond 28 days.
     If updating, search 'TABLE(EXTEND_TIME_WINDOW' and update in all locations in this query. And also search 'match_age BETWEEN 0 AND 28' to update this as well.*/
              TABLE(
                EXTEND_TIME_WINDOW('dsp_clicks', 'P28D', 'P0D')
                )
            WHERE
              campaign_id_string IN (SELECT campaign_id_string FROM dsp_campaigns)
              AND user_id IS NOT NULL  
          )
    )
  WHERE event_priority = 1 ---------- end DSP view-aware section ----------
    ---------- start sponsored ads-specific section 2 of 3 (delete the line 'UNION ALL' if your query will omit DSP)----------
  UNION ALL
  -- Read sponsored ads click and impression events.
  SELECT
    user_id,
    ad_product_type,
    campaign_id_string,
    campaign,
    event_dt_utc AS traffic_dt_utc,
    (
      CASE
        WHEN clicks < 1 THEN 'IMP'
        ELSE 'CLICK'
      END
    ) AS traffic_event_type,

    impressions,
    clicks
  FROM
    /* OPTIONAL UPDATE Lookback Window: Include impression and click events from up to X days before conversion events. The setting below is 28 days. 
     It's not recommended to extend the window beyond 28 days for sponsored ads. If updating, search 'TABLE(EXTEND_TIME_WINDOW' and update in all locations in this query. 
     And also search 'match_age BETWEEN 0 AND 28' to update this as well.*/
    TABLE(
      EXTEND_TIME_WINDOW('sponsored_ads_traffic', 'P28D', 'P0D')
    )
  WHERE
    campaign IN (
      SELECT
        campaign
      FROM
        sp_campaigns
    )
    AND user_id IS NOT NULL
  UNION ALL
  -- Read Sponsored Display click and impression events.
  SELECT
    user_id,
    ad_product_type,
    campaign_id_string,
    campaign,
    event_dt_utc AS traffic_dt_utc,
    (
      CASE
        WHEN clicks < 1 THEN 'IMP'
        ELSE 'CLICK'
      END
    ) AS traffic_event_type,
    impressions,
    clicks
  FROM
    /*OPTIONAL UPDATE Lookback Window: Include impression and click events from up to X days before conversion events. The setting below is 28 days. 
     It's not recommended to extend the window beyond 28 days for sponsored ads.
     If updating, search 'TABLE(EXTEND_TIME_WINDOW' and update in all locations in this query. 
     And also search 'match_age BETWEEN 0 AND 28' to update this as well.*/
    TABLE(
      EXTEND_TIME_WINDOW('sponsored_ads_traffic', 'P28D', 'P0D')
    )
  WHERE
    campaign IN (
      SELECT
        campaign
      FROM
        sd_campaigns
    )
    AND user_id IS NOT NULL
  UNION ALL
  -- Read Sponsored Brands click and impression events.
  SELECT
    user_id,
    ad_product_type,
    -- sponsored ads do not have customer-facing campaign ids.
    campaign_id_string,
    campaign,
    event_dt_utc AS traffic_dt_utc,
    (
      CASE
        WHEN clicks < 1 THEN 'IMP'
        ELSE 'CLICK'
      END
    ) AS traffic_event_type,
    impressions,
    clicks
  FROM
    /*OPTIONAL UPDATE Lookback Window: Include impression and click events from up to X days before conversion events. The setting below is 28 days. 
     It's not recommended to extend the window beyond 28 days for sponsored ads.
     If updating, search 'TABLE(EXTEND_TIME_WINDOW' and update in all locations in this query. 
     And also search 'match_age BETWEEN 0 AND 28' to update this as well.*/
    TABLE(
      EXTEND_TIME_WINDOW('sponsored_ads_traffic', 'P28D', 'P0D')
    )
  WHERE
    campaign IN (
      SELECT
        campaign
      FROM
        sb_campaigns
    )
    AND user_id IS NOT NULL 
  UNION ALL
  -- Read Sponsored Brands click and impression events.
  SELECT
    user_id,
    ad_product_type,
    -- sponsored ads do not have customer-facing campaign ids.
    campaign_id_string,
    campaign,
    event_dt_utc AS traffic_dt_utc,
    (
      CASE
        WHEN clicks < 1 THEN 'IMP'
        ELSE 'CLICK'
      END
    ) AS traffic_event_type,
    impressions,
    clicks
  FROM
    /*OPTIONAL UPDATE Lookback Window: Include impression and click events from up to X days before conversion events. The setting below is 28 days. 
     It's not recommended to extend the window beyond 28 days for sponsored ads.
     If updating, search 'TABLE(EXTEND_TIME_WINDOW' and update in all locations in this query. 
     And also search 'match_age BETWEEN 0 AND 28' to update this as well.*/
    TABLE(
      EXTEND_TIME_WINDOW('sponsored_ads_traffic', 'P28D', 'P0D')
    )
  WHERE
    campaign IN (
      SELECT
        campaign
      FROM
        st_campaigns
    )
    AND user_id IS NOT NULL
    ---------- end sponsored ads-specific section 2 of 3----------
),
-- Read conversion events with campaign relevance from the conversions_with_relevance data source.
cwr AS(
  SELECT
    conversion_id,
    campaign_id_string,
    campaign,
    conversions,
    total_units_sold,
    total_product_sales,
    user_id,
    event_category,
    event_dt_utc
  FROM
    conversions_with_relevance
  WHERE
    /* OPTIONAL UPDATE change event_category to 'off-amazon' for off-amazon conversion advertisers.*/
    -- event_category = 'off-amazon'
    event_category = 'purchase'
    AND user_id IS NOT NULL 
    /* OPTIONAL UPDATE the ASINs based on your exploratory query. The column tracked_item includes the ASINs tracked. */
    -- AND tracked_item in ('ASIN1234', 'ASIN5678')

    /* If including event category = 'off-amazon', if desired, you can add a filter for selecting Mobile  Measurement Partner, Conversions API, and/or Amazon Ad Tag in conversion_event_source_name.*/
    -- AND conversion_event_source_name in ('Mobile Measurement Partner', 'Conversions API', 'Amazon Ad Tag')

    /* Also, if including event_category = 'off-amazon', then include a filter for selecting a single event_subtype (potentially grouped by similarity). Below are the label keys for nubers and adjust the query template below with the corresponding value.
     event_subtype = '53' it means Add to shopping cart
     event_subtype = '7' it means Application
     event_subtype = '140' it means Checkout
     event_subtype = '136' it means Contact
     event_subtype = '141' it means Lead
     event_subtype = '54' it means Off-Amazon purchase
     event_subtype = '133' it means Other
     event_subtype = '134' it means Page View
     event_subtype = '135' it means Search
     event_subtype = '21' it means Sign-up
     event_subtype = '5' it means Subscribe
     event_subtype = '37' it means Mobile app first starts */
    -- AND conversion_event_subtype in ('53', '7', '140', '136', '141', '54', '133', '134', '135', '21', '5', '37') -- Select the numbers you wish to use

),
-- Join conversion events to traffic events based on the user ID and relevant campaign ID.
matched AS (
  ---------- start DSP-specific section 3 of 3 ----------
  -- matched DSP conversions and traffic
  SELECT
    c.conversion_id,
    c.campaign_id_string,
    c.campaign,
    IF(t.traffic_event_type = 'CLICK', 1, 2) AS match_type,
    SECONDS_BETWEEN(t.traffic_dt_utc, c.event_dt_utc) AS match_age,
    c.conversions,
    c.total_units_sold,
    c.total_product_sales,
    c.user_id
  FROM
    cwr c
    JOIN traffic t ON (
      c.user_id = t.user_id
      AND c.campaign_id_string = t.campaign_id_string
    )
  WHERE
    t.campaign_id_string IN (
      SELECT
        campaign_id_string
      FROM
        dsp_campaigns
    )
    AND t.traffic_event_type IN (
      SELECT
        traffic_event_type
      FROM
        dsp_traffic_type
    ) ---------- end DSP-specific section 3 of 3 ----------
    ---------- start sponsored ads-specific section 3 of 3 (delete the line 'UNION ALL' if your query will omit DSP). ----------
  UNION ALL
  -- matched Sponsored Products conversions and traffic
  SELECT
    c.conversion_id,
    c.campaign_id_string,
    c.campaign,
    IF(t.traffic_event_type = 'CLICK', 1, 2) AS match_type,
    SECONDS_BETWEEN(t.traffic_dt_utc, c.event_dt_utc) AS match_age,
    c.conversions,
    c.total_units_sold,
    c.total_product_sales,
    c.user_id
  FROM
    traffic t
    JOIN cwr c ON (
      c.user_id = t.user_id
      AND c.campaign = t.campaign
    )
  WHERE
    t.campaign IN (
      SELECT
        campaign
      FROM
        sp_campaigns
    )
    AND t.traffic_event_type IN (
      SELECT
        traffic_event_type
      FROM
        sp_traffic_type
    )
  UNION ALL
  -- matched Sponsored Display conversions and traffic
  SELECT
    c.conversion_id,
    c.campaign_id_string,
    c.campaign,
    IF(t.traffic_event_type = 'CLICK', 1, 2) AS match_type,
    SECONDS_BETWEEN(t.traffic_dt_utc, c.event_dt_utc) AS match_age,
    c.conversions,
    c.total_units_sold,
    c.total_product_sales,
    c.user_id
  FROM
    traffic t
    JOIN cwr c ON (
      c.user_id = t.user_id
      AND c.campaign = t.campaign
    )
  WHERE
    t.campaign IN (
      SELECT
        campaign
      FROM
        sd_campaigns
    )
    AND t.traffic_event_type IN (
      SELECT
        traffic_event_type
      FROM
        sd_traffic_type
    )
  UNION ALL
  -- matched Sponsored Brands conversions and traffic
  SELECT
    c.conversion_id,
    c.campaign_id_string,
    c.campaign,
    IF(t.traffic_event_type = 'CLICK', 1, 2) AS match_type,
    SECONDS_BETWEEN(t.traffic_dt_utc, c.event_dt_utc) AS match_age,
    c.conversions,
    c.total_units_sold,
    c.total_product_sales,
    c.user_id
  FROM
    traffic t
    JOIN cwr c ON (
      c.user_id = t.user_id
      AND c.campaign = t.campaign
    )
  WHERE
    t.campaign IN (
      SELECT
        campaign
      FROM
        sb_campaigns
    )
    AND t.traffic_event_type IN (
      SELECT
        traffic_event_type
      FROM
        sb_traffic_type
    )
  UNION ALL
  -- matched Sponsored TV conversions and traffic
  SELECT
    c.conversion_id,
    c.campaign_id_string,
    c.campaign,
    IF(t.traffic_event_type = 'CLICK', 1, 2) AS match_type,
    SECONDS_BETWEEN(t.traffic_dt_utc, c.event_dt_utc) AS match_age,
    c.conversions,
    c.total_units_sold,
    c.total_product_sales,
    c.user_id
  FROM
    traffic t
    JOIN cwr c ON (
      c.user_id = t.user_id
      AND c.campaign = t.campaign
    )
  WHERE
    t.campaign IN (
      SELECT
        campaign
      FROM
        st_campaigns
    )
    AND t.traffic_event_type IN (
      SELECT
        traffic_event_type
      FROM
        st_traffic_type
    ) 
     ---------- end sponsored ads-specific section 3 of 3 ----------
),
-- Filter out matches that were outside of the traffic lookback window.
-- For each conversion event, rank all the matching traffic events based on the custom position.
ranked AS (
  SELECT
    campaign_id_string,
    campaign,
    /*OPTIONAL UPDATE traffic priority if your query includes traffic from both clicks and impressions.
     By default, the ranking below prioritizes clicks over impressions. Remove 'match_type ASC,' to prioritize based on match_age only.*/
    ROW_NUMBER() OVER(
      PARTITION BY conversion_id
      ORDER BY
        match_type ASC,
        match_age DESC
    ) AS first_match_rank,
    ROW_NUMBER() OVER(
      PARTITION BY conversion_id
      ORDER BY
        match_type ASC,
        match_age ASC
    ) AS last_match_rank,
    conversions,
    total_units_sold,
    total_product_sales,
    user_id
  FROM
    matched
  WHERE
    --OPTIONAL UPDATE Filter out matches where the traffic event is after or more than 28 days before the conversion event.
    match_age BETWEEN 0 AND 28 * 24 * 60 * 60
),
-- Filter to only the best matching campaign for first_match_rank and last_match_rank.
-- Calculate conversion metric sums for each campaign.
attributed_conversions AS (
  SELECT
    r.campaign_id_string,
    r.campaign,
    -- OPTIONAL UPDATE weights: change the weight assigned to the first touch point (The template assigns 30% to the first touchpoint)
    COUNT(DISTINCT user_id) *.3 AS users_that_converted,
    SUM(r.conversions) *.3 AS conversions,
    SUM(r.total_units_sold) *.3 AS total_units_sold,
    SUM(r.total_product_sales) *.3 AS total_product_sales
  FROM
    ranked r
  WHERE
    -- Filter to only the best matching traffic event.
    r.first_match_rank = 1
  GROUP BY
    1,
    2
  UNION ALL
  SELECT
    a.campaign_id_string,
    a.campaign,
    -- OPTIONAL UPDATE weights: Change the weight assigned to the last touch point. The default value here is assigning 70% to the last touchpoint.
    COUNT(DISTINCT user_id) *.7 AS users_that_converted,
    SUM(a.conversions) *.7 AS conversions,
    SUM(a.total_units_sold) *.7 AS total_units_sold,
    SUM(a.total_product_sales) *.7 AS total_product_sales
  FROM
    ranked a
  WHERE
    a.last_match_rank = 1
  GROUP BY
    1,
    2
),
total_attributed_conversions AS (
  SELECT
    campaign_id_string,
    campaign,
    SUM(users_that_converted) users_that_converted,
    SUM(conversions) conversions,
    SUM(total_units_sold) total_units_sold,
    SUM(total_product_sales) total_product_sales
  FROM
    attributed_conversions
  GROUP BY
    1,
    2
),
-- Include campaign impressions, clicks and user reach info.
traffic_campaigns AS (
  SELECT
    campaign_id_string,
    campaign,
    ad_product_type,
    COUNT(DISTINCT user_id) user_reach,
    SUM(impressions) AS impressions,
    SUM(clicks) AS clicks
  FROM
    traffic
  GROUP BY
    1,
    2,
    3
)
SELECT
  'Position Based' AS attribution_model,
  c.campaign_id_string,
  c.campaign,
  t.impressions,
  t.clicks,
  round(t.user_reach) AS user_reach,
  round(c.users_that_converted) AS users_that_converted,
  round(c.conversions) AS conversions,
  c.conversions / t.user_reach AS conversion_rate,
  round(c.total_units_sold) AS total_units_sold,
  round(c.total_product_sales, 2) AS total_product_sales
FROM
  total_attributed_conversions c
  JOIN traffic_campaigns t ON c.campaign = t.campaign
```
