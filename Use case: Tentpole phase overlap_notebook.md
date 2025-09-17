## Metadata

**id:** 2863325acae4bc8c36b5f04743be92f5f1f01e43040a7be1f416aac3e067d338
**collection:** INSTRUCTIONAL
**state:** None
**deprecation_metadata:** None
**description:** Evaluate past performance of ads before, during, and after tentpole events.
**tags:** []
**use_case_categories:** ['Overlap', 'Path-to-conversion']
**advertiser_types:** ['On-Amazon conversions', 'Off-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## Introduction 

### Purpose 

Tentpole events like Prime Day and Black Friday are key opportunities to amplify your brand’s visibility and encourage conversion. Evaluating the past performance of ad activations before, during, and after these peak events can help you maximize advertising effectiveness during future Tentpole periods. This instructional query (IQ) compares unique combinations of these ad activations across Efficiency, Awareness, Consideration, Conversion, and Growth metrics so you can identify those that worked best for your priorities.

Eligible Tentpole events include Amazon-specific events (e.g., Prime Day), holiday/retail events (e.g., Black Friday) and advertiser-specific events (e.g., product pre-sales and launches). Tentpole events can be evaluated over three distinct time periods, or **Tentpole phases**: a time period before the event (**Lead Up**), the event itself, and a time period after the event (**Lead Out**). The visual below depicts the three Tentpole phases for Prime Day, as well as a purchase event at user-level.

![TentpolePhaseUserLevel_sm.png](https://m.media-amazon.com/images/G/01/adsamciql/TentpolePhaseUserLevel_sm.png)
    
This IQ demonstrates how to leverage Amazon Marketing Cloud data to categorize shoppers into unique **Tentpole strategies**. Tentpole strategies are defined as unique combinations of Tentpole phases during which shoppers were exposed to ad impressions. This allows you to view and analyze metrics for each Tentpole strategy to identify which has been the most effective in the past. 

The visual below depicts an example using Prime Day. Three distinct phases represent the two weeks before Prime Day (Lead Up), Prime Day itself (PD), and the two weeks after Prime Day (Lead Out). The "1 - Lead Up, 2 - PD" strategy was the top-scoring strategy across the funnel. In this case, activating ads for both the Lead Up and Prime Day phases during Prime Day season will help cost-effectively boost conversions while maintaining healthy Awareness, Consideration, Conversion, and Growth metrics. See **3.3 Insights and Data Interpretation** below for more such takeaways.

![TentpoleStrategies.png](https://m.media-amazon.com/images/G/01/adsamciql/TentpoleStrategies.png)

### Requirements 

This query is available for advertisers who:

- Use ASIN conversions or use pixel conversions, and
- Want to compare campaign performance across three distinct Tentpole phases time periods, and
- Have DSP and/or sponsored ads campaigns active during these Tentpole phases — these campaigns can either be dedicated to the Tentpole event or always-on or both

## Data Query Instructions 

### Data Returned

The query will return all possible Tentpole strategies alongside the number of shoppers exposed to each and performance metrics (such as impressions, impressions cost, and conversions) associated with each strategy.

**Note**: Impressions served after the conversion event are excluded. For shoppers that did not convert, all impressions are considered.

### Tables Used 

- **dsp_impressions**: This table provides traffic data for DSP campaigns.
- **sponsored_ads_traffic**: This table provides traffic data for sponsored ads campaigns.
- **amazon_attributed_events_by_traffic_time**: This table provides ad-attributed conversions data for both DSP and sponsored ads campaigns. As the conversions are attributed using a 14-day window, wait two weeks past the end date of the campaigns in scope to run this query to capture all conversions.

## Data Interpretation Instructions 

### Example Query Results

Note that:

- This data is for instructional purposes only. Your results will differ.
- Some columns will be absent in the case of pixel conversions. Refer to **Section 3.2 Dimensions and Metrics Defined** for a glossary for both the ASIN and pixel conversions queries.


|**tentpole_strategies**|**duration_days**|**impressions**|**unique_reach_purchase**|**unique_reach_dpv**|**unique_purchasers**|**unique_ntb_purchasers**|**unique_viewers**|**dpv**|**sales**|**ntb_sales**|**ad_spend**|**roas**|**unique_reach_percentage**|**sales_percentage**|**unique_ntb_purchasers_percentage**|**average_daily_impressions**|**dpv_rate**|**purchase_rate**|**ntb_rate**|
|:---|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|---:|
|[1 - Lead Up]|11|8364430|2568747|2565352|36856|17399|10478|93532|540204.3|359354.25|41161.55|13.12|0.09|0.07|0.07|0.34|0.014|0.007|0.00408|
|[2 - Prime Day]|2|26554903|7448271|7455197|137463|47249|32633|342456|2190072.02|1679352.81|291290.74|7.51|0.27|0.28|0.23|2.09|0.018|0.006|0.00438|
|[3 - Lead Out]|18|18988467|6832524|6772039|71649|34052|22749|205899|864154.49|618248.48|80474.7|10.73|0.25|0.11|0.16|0.18|0.011|0.004|0.00333|
|[1 - Lead Up, 2 - Prime Day]|13|11938954|1304593|1347619|89991|34613|21323|269498|1735074.51|1145906|117848.59|14.72|0.04|0.23|0.15|0.82|0.066|0.026|0.01634|
|[2 - Prime Day, 3 - Lead Out]|20|40612761|4584819|4471201|105203|44256|27863|384882|1100063.48|738106.15|347677.48|3.16|0.17|0.14|0.2|0.52|0.023|0.009|0.00608|
|[1 - Lead Up, 3 - Lead Out]|29|13011469|1616874|1597355|27201|14349|8100|85424|354291.36|215777.56|50932.47|6.95|0.06|0.05|0.06|0.32|0.017|0.008|0.00501|
|[1 - Lead Up, 2 - Prime Day, 3 - Lead Out]|31|51578289|2477057|2386468|90066|34944|18736|401083|922596.2|500261.15|341535.93|2.7|0.09|0.12|0.13|0.79|0.037|0.01411|0.00756|


### Dimensions and Metrics Defined

|**metric**|**definition**|
|:---|:---|
|**ASIN Conversions**||
|tentpole_strategies|Unique combination of Tentpole phases that shoppers were exposed to|
|duration_days|Duration (in days) of Tentpole strategy|
|impressions|Total impressions served to shoppers|
|unique_reach_purchase|Unique shoppers reached by Tentpole strategy before they purchased|
|unique_reach_dpv|Unique shoppers reached by Tentpole strategy before they viewed the detail page|
|unique_purchasers|Unique shoppers who purchased|
|unique_ntb_purchasers|Unique new-to-brand customers who purchased (New-to-brand customers are defined as customers who have made a purchase of the brand for the first time in 1 year)|
|unique_viewers|Unique shoppers who viewed a product detail page|
|dpv|Total detail page views|
|sales|Total sales (in local currency)|
|ntb_sales|Total sales (in local currency) by new-to-brand customers|
|ad_spend|Total cost (in local currency) of impressions served|
|roas|The return on advertising spend (sales / ad_spend)|
|unique_reach_percentage|The percentage of total unique shoppers reached by Tentpole strategy|
|sales_percentage|The percentage of total sales accrued by Tentpole strategy|
|unique_ntb_purchasers_percentage|The percentage of total unique new-to-brand customers who purchased after being exposed to Tentpole strategy|
|average_daily_impressions|The average number of impressions a shopper was exposed to per day of Tentpole strategy|
|dpv_rate|Detail page view rate (unique_viewers / unique_reach_dpv)|
|purchase_rate|Purchase rate (unique_purchasers / unique_reach_purchase)|
|ntb_rate|New-to-brand rate (unique_ntb_purchasers / unique_reach_purchase)|
|**Pixel Conversions**||
|tentpole_strategies|Unique combination of Tentpole phases that shoppers were exposed to|
|duration_days|Duration (in days) of Tentpole strategy|
|impressions|Total impressions served to shoppers|
|unique_reach|Unique shoppers reached by Tentpole strategy before the pixel conversion|
|unique_converters|Unique shoppers who (pixel) converted|
|pixel_conversions|Total pixel conversions|
|ad_spend|Total cost (in local currency) of impressions served|
|cost_per_conversion|Cost of one conversion (in local currency) (ad_spend / cost_per_conversion)|
|unique_reach_percentage|The percentage of total unique shoppers reached by Tentpole strategy|
|pixel_conversions_percentage|The percentage of total conversions accrued by Tentpole strategy|
|average_daily_impressions|The average number of impressions a shopper was exposed to per day of Tentpole strategy|
|conversion_rate|Conversion rate (unique_converters / unique_reach)|


### Insights and Data Interpretation

- **Which Tentpole strategy had the highest reach, sales, and New-to-Brand customers?**
    - The [2 - Prime Day] strategy had the highest reach (28%), sales (28%), and New-to-Brand customers (23%). This means that 28% of shoppers saw ads during the Prime Day phase only.
    - However, the reach from the [1 - Lead Up, 2 - Prime Day] strategy had the greatest relative multiplier effect as the 5% of reach was translated into 23% of total sales and 15% of New-to-brand customers, the highest of all Tentpole strategies.
    - **Sample Recommendation**: Whilst activating ads on Prime Day helps garner a wide reach, include both the Lead Up and Prime Day phases in your Tentpole strategy to help increase conversions and New-to-Brand growth.
- **Which Tentpole strategy was the most cost-effective?**
    - The [1 - Lead Up, 2 - Prime Day] strategy was the most cost-effective with a ROAS of 14.72.
    - **Sample Recommendation**: Include both the Lead Up and Prime Day phases in your Tentpole strategy to cost-effectively boost conversions.
- **Which Tentpole strategy served the most daily impressions per user?**
    - The [2 - Prime Day] strategy served the highest average impressions per user at 2.1 impressions daily.
    - **Sample Recommendation**: Activate ads on Prime Day to leverage Amazon’s increased traffic and reach shoppers at a high frequency.
- **Which Tentpole strategy had the highest Detail Page Rate, Purchase Rate, and New-to-Brand Rate?**
    - The [1 - Lead Up, 2 - Prime Day] strategy ranked highest across the three: Detail Page Rate was 6.7%, Purchase Rate was 2.7%, and New-to-Brand Rate was 1.6%.
    - **Sample Recommendation**: Include Lead Up and Prime Day in your Tentpole strategy to encourage consideration of your products, purchase, and New-to-Brand growth.
- **How many shoppers were exposed to Tentpole strategies containing more than one phase?**
    - The single-phase strategies — [1 - Lead Up],  [2 - Prime Day], and [3 - Lead Out] — collectively reached 63% of shoppers. This means that 37% of shoppers were exposed to two or more Tentpole phases.
    - **Sample Recommendation**: Multi-phase strategies generally had higher Detail Page View Rates, Conversion Rates, and New-to-Brand Rates. Re-attract shoppers such that they are exposed to multiple phases of your Tentpole strategy to encourage consideration of your products, purchase, and New-to-Brand growth.
- **Which Tentpole strategy was the most effective across the funnel this Prime Day?**
    - The [1 - Lead Up, 2 - Prime Day] strategy was the top-scoring full-funnel strategy as it ranked first in terms of Efficiency (ROAS), Consideration (Detail Page View Rate), Conversion (Purchase Rate), and Growth (New-to-Brand Rate). It ranked second for Awareness (Average Daily Impressions).
    - **Sample Recommendation**: Include both the Lead Up and Prime Day phases in your Tentpole strategy to cost-effectively boost conversions while maintaining healthy Awareness, Consideration, Conversion, and Growth metrics.

## Queries

### Exploratory Queries

Use the exploratory query supplied below to identify the campaigns you want to include. Ensure that you choose a time window that contains all three phases you want to analyze.

To identify the campaigns you want to include, use one of the following exploratory queries:

- **4.1.1 Exploratory query for ASIN conversions**
- **4.1.2 Exploratory query for pixel conversions**

#### 4.1.1 Exploratory query for ASIN conversions


```
-- Instructional Query: Exploratory Query for Tentpole Phase Overlap (ASIN conversions) --
-- Search for 'UPDATE' to find the clauses where you can optionally customize this query for your use-case.
-- 1) Optional Update to only retrieve campaigns that tracked a particular set of ASINs.
WITH impressions AS (
  SELECT
    campaign,
    campaign_id,
    campaign_start_date,
    campaign_end_date,
    SUM(impressions) AS impressions,
    SUM(total_cost / 100000) AS ad_spend
  FROM
    dsp_impressions
  GROUP BY
    1,
    2,
    3,
    4
  UNION ALL
  SELECT
    campaign,
    -1 AS campaign_id,
    campaign_start_date,
    campaign_end_date,
    SUM(impressions) AS impressions,
    SUM(spend / 100000000) AS ad_spend
  FROM
    sponsored_ads_traffic
  GROUP BY
    1,
    2,
    3,
    4
),
initial_aggregation AS (
  SELECT
    I.campaign,
    I.campaign_id,
    C.ad_product_type,
    I.campaign_start_date,
    I.campaign_end_date,
    C.tracked_asin AS tracked_asins,
    SUM(I.impressions) AS impressions,
    SUM(C.product_sales) AS promoted_sales,
    SUM(C.total_product_sales) AS total_sales,
    SUM(I.ad_spend) AS ad_spend
  FROM
    impressions I
    LEFT JOIN amazon_attributed_events_by_traffic_time C ON I.campaign = C.campaign
    /* WHERE tracked_asin in ('ASIN1234', 'ASIN5678') -- OPTIONAL UPDATE: Uncomment this line to only retrieve campaigns that tracked a particular set of ASINs. */
  GROUP BY
    1,
    2,
    3,
    4,
    5,
    6
)
SELECT
  IA.campaign,
  IA.campaign_id,
  IA.ad_product_type,
  IA.campaign_start_date,
  IA.campaign_end_date,
  ARRAY_SORT(COLLECT(DISTINCT IA.tracked_asins)) AS tracked_asins,
  SUM(IA.impressions) AS impressions,
  SUM(IA.promoted_sales) AS promoted_sales,
  SUM(IA.total_sales) AS total_sales,
  SUM(IA.ad_spend) AS ad_spend
FROM
  initial_aggregation IA
GROUP BY
  1,
  2,
  3,
  4,
  5
```

#### 4.1.2 Exploratory query for Pixel conversions


```
-- Instructional Query: Exploratory Query for Tentpole Phase Overlap (Pixel conversions) --
-- Search for 'UPDATE' to find the clauses where you can optionally customize this query for your use-case.
-- 1) Optional Update to only retrieve campaigns that tracked a particular set of pixel IDs.
WITH impressions AS (
  SELECT
    campaign,
    campaign_id,
    campaign_start_date,
    campaign_end_date,
    SUM(impressions) AS impressions,
    SUM(total_cost / 100000) AS ad_spend
  FROM
    dsp_impressions
  GROUP BY
    1,
    2,
    3,
    4
  UNION ALL
  SELECT
    campaign,
    -1 AS campaign_id,
    campaign_start_date,
    campaign_end_date,
    SUM(impressions) AS impressions,
    SUM(spend / 100000000) AS ad_spend
  FROM
    sponsored_ads_traffic
  GROUP BY
    1,
    2,
    3,
    4
),
pixel_conversions AS (
  SELECT
    campaign,
    campaign_id,
    ad_product_type,
    tracked_item,
    SUM(conversions) AS pixel_conversions
  FROM
    amazon_attributed_events_by_traffic_time
  WHERE
    user_id IS NOT NULL
    AND conversions > 0
    AND conversion_event_category = 'pixel'
  GROUP BY
    1,
    2,
    3,
    4
),
initial_aggregation AS (
  SELECT
    I.campaign,
    I.campaign_id,
    C.ad_product_type,
    I.campaign_start_date,
    I.campaign_end_date,
    C.tracked_item AS tracked_pixel,
    SUM(I.impressions) AS impressions,
    SUM(I.ad_spend) AS ad_spend,
    SUM(C.pixel_conversions) AS pixel_conversions
  FROM
    impressions I
    LEFT JOIN pixel_conversions C ON I.campaign = C.campaign
    /* WHERE tracked_item in ('aaaaaaaa', 'bbbbbbbbb') -- OPTIONAL UPDATE: Uncomment this line to only retrieve campaigns that tracked a particular pixel ID. */
  GROUP BY
    1,
    2,
    3,
    4,
    5,
    6
)
SELECT
  IA.campaign,
  IA.campaign_id,
  IA.ad_product_type,
  IA.campaign_start_date,
  IA.campaign_end_date,
  ARRAY_SORT(COLLECT(DISTINCT IA.tracked_pixel)) AS tracked_pixel,
  SUM(IA.impressions) AS impressions,
  SUM(IA.ad_spend) AS ad_spend,
  SUM(IA.pixel_conversions) AS pixel_conversions
FROM
  initial_aggregation IA
GROUP BY
  1,
  2,
  3,
  4,
  5
```

### Tentpole Phase Overlap: Query Templates

Depending on whether you are using ASIN conversions or pixel conversions, utilize one of the two queries below (alongside their respective custom parameters template):

- For ASIN conversions, use **Instructional Query: Tentpole Phase Overlap (ASIN Conversions)** in section 4.2.1
- For Pixel conversions, use **Instructional Query: Tentpole Phase Overlap (Pixel Conversions)** in section 4.2.2


**Important:** Search for ‘UPDATE’ to find clauses where you can customize this query for your use-case. The query will not produce accurate results if the updates labeled ‘REQUIRED’ are not populated.

You will need to gather the following information to customize these queries:
- **DSP campaign IDs**
    - Refer to **4.1 Exploratory Queries** to generate a list of campaign IDs.
- **Sponsored ads campaign**
    - Refer to **4.1 Exploratory Queries** to generate a list of sponsored ads campaign names.
- **[Optional] eCost per Mille (eCPM)**
    - eCPM is defined as the cost (in local currency) per thousand impressions served. You can provide eCPM figures by campaign to substitute AMC data while computing impression cost. You may use eCPM in cases where AMC does not register cost data, such as with Share-of-Voice sponsorship packages. 
    - Use the impressions and cost columns in the Reporting Summary of the Advertising console and this formula (cost * 1000 / impressions) to calculate eCPM for your campaign.
- **Promoted versus Total (Promoted + Brand Halo) conversions (ASIN conversions only)**
    - You have the option to include both promoted and brand halo conversions, or promoted conversions alone. Promoted conversions are purchases accruing from ASINs tracked by the campaigns in scope. Brand Halo conversions are purchases accruing from ASINs of the same brand as the ASINs tracked by the campaigns in scope (but not the tracked ASINs themselves). Use promoted and brand halo conversions in your query when you want to holistically analyze the impact of your Tentpole strategy on your brand.
- **Details of Tentpole Phases: Phase {One/Two/Three} Name, Phase {One/Two/Three} Start Date, Phase {One/Two/Three} End Date**
    - How you define your three phases depends on the hypothesis you wish to evaluate. DSP and/or sponsored ads campaigns active during these three time periods are eligible — these campaigns can either be dedicated to the Tentpole event or always-on or both. For example: 
        - If you wanted to evaluate the effectiveness of advertising in the weeks in July leading up to and leading out from a Tentpole event (such as the example above), your Tentpole phases would be ‘1 - Lead Up’, ‘2 - Prime Day’, and ‘3 - Lead Out’. Lead Up would run from the beginning of July to the day before Prime Day, Prime Day would run for the two days of the Tentpole event itself, and Lead Out would run from the day after Prime Day to the end of July. (Note that the time window you run your query on is the whole month of July.) In this case, it is reasonable to include all campaigns running for the brand being analyzed — whether dedicated to Prime Day or not — for a comprehensive evaluation of what is effective during Prime Day. 
        - If you wanted to evaluate the effectiveness of running pre-sale activations before a product launch, your Tentpole phases would be ‘1 - Pre-Sale’, ‘2 - Launch’, and ‘3 - Post-Launch’. Pre-Sale would run from the activation of your first pre-sale campaign to the day before launch, Launch would run for the days of the product launch itself, and Post-Launch would run from the day after launch to the end of activations. (Note that the time window you run your query on is the whole period from the start of the Pre-Sale phase to the end of the Post-Launch phase.) In this case, it is reasonable to only include campaigns dedicated to the product launch; including all activations would obscure the measurement of pre-sale activations.
- **[Optional] ASINs**
    - You can provide ASINs to filter in the event that you want to analyze a subset of promoted ASINs.Promoted conversions are purchases accruing from ASINs tracked by the campaigns in scope. 
    - You might want to utilize the ASIN filter, for example, in a case where you are interested in evaluating the impact of your Tentpole activations on only one (and not all) of your product lines.


#### 4.2.1 Tentpole Phase Overlap (ASIN Conversions)

Before you run this query, refer to **4.1.1 Exploratory query for ASIN conversions** to identify the appropriate campaign filters and Tentpole phase details. 

**Note**: You must use the ASIN conversions custom parameters alongside this query. Click **Open in query editor** to open the query and custom parameters in the AMC Query Editor. You may have to click **Show custom parameters** to display the custom parameters.



```
-- Instructional Query: Tentpole Phase Overlap (ASIN Conversions) --
-- Search for 'UPDATE' to find the clauses where you can customize this query for your use-case. The query will not produce accurate results if the updates labeled REQUIRED are not populated.
/*
 
 ------- Customization Instructions -------
 0) All advertisers should run the exploratory query first to identify the appropriate campaign filters, ASIN filters and Tentpole phase details.
 1a) The DSP and sponsored ads campaigns to analyze are defined in the main body of the query. DSP campaigns are identified using campaign IDs and Sponsored Ads campaigns are identified using campaign names. 
 1b) For both DSP and sponsored ads campaigns, there is a provision to add eCPMs at the campaign-level. Providing an eCPM figure would substitute AMC cost data for campaigns where it has been supplied. You are advised to use eCPM in cases where AMC does not register cost data, such as with Share-of-Voice sponsorship packages. Note that you do not have to provide an eCPM for all campaigns if you have provided it for some - leaving it blank ('') will ensure usage of AMC data for campaigns where this is the case.
 1c) If your scope only includes DSP or sponsored ads campaigns, populate the unneeded clause with blanks. e.g. If you only wanted to analyze DSP campaigns, you would update your Sponsored Ads clause from (1111111111, '1.23') to (, '').
 2) All other inputs are defined via the Custom Parameters functionality. Follow the instructions in the Custom Parameters template to update these.
 2a) Promoted versus Total (Promoted + Brand Halo) conversions (ASIN conversions only): You have the option to include both promoted and brand halo conversions, or promoted conversions alone.
 2b) Details of Tentpole Phases: How you define your three phases depends on the hypothesis you wish to evaluate. DSP and/or sponsored ads campaigns active during these three time periods are eligible — these campaigns can either be dedicated to the Tentpole event or always-on or both. 
 2c) [Optional] ASINs: You can provide ASINs to filter in the event that you want to analyze a subset of promoted ASINs.

 */
WITH dsp_campaign_scope (dsp_campaign_id, ecpm) AS (
  -- REQUIRED UPDATE: Define DSP campaigns to analyze with campaign IDs populated alongside eCPM on an optional basis. Providing an eCPM figure would substitute AMC cost data for campaigns where it has been supplied. (e.g. Campaign 1111111111 below has an associated eCPM of 1.23 provided while Campaigns 2222222222 and 3333333333 do not.)
  VALUES
    (1111111111, '1.23'),
    (2222222222, ''),
    (3333333333, '')
),
sa_campaign_scope (sa_campaign_name, ecpm) AS (
  -- REQUIRED UPDATE: Define sponsored ads campaigns to analyze with campaign names populated alongside eCPM on an optional basis. Providing an eCPM figure would substitute AMC cost data for campaigns where it has been supplied. (e.g. SA Campaign 1 below has an associated eCPM of 1.23 provided while SA Campaign 2 and SA Campaign 3 do not.)
  VALUES
    ('SA Campaign 1', '1.23'),
    ('SA Campaign 2', ''),
    ('SA Campaign 3', '')
),
dsp_campaign AS (
  SELECT
    A.campaign_id,
    B.ecpm
  FROM
    dsp_impressions A
    JOIN dsp_campaign_scope B ON A.campaign_id = B.dsp_campaign_id
  GROUP BY
    1,
    2
),
sa_campaign AS (
  SELECT
    A.campaign AS campaign_name,
    B.ecpm
  FROM
    sponsored_ads_traffic A
    JOIN sa_campaign_scope B ON A.campaign = B.sa_campaign_name
  GROUP BY
    1,
    2
),
-- Gather Conversions data for DSP and sponsored ads
conversions_prep AS (
  -- Gather Conversions data for DSP campaigns
  SELECT
    C.user_id,
    C.conversion_event_dt,
    C.new_to_brand AS ntb_flag,
    IF(
      CUSTOM_PARAMETER('include_brand_halo') = 1,
      SUM(C.total_product_sales),
      SUM(C.product_sales)
    ) AS sales,
    IF(
      CUSTOM_PARAMETER('include_brand_halo') = 1,
      SUM(C.new_to_brand_total_product_sales),
      SUM(C.new_to_brand_product_sales)
    ) AS ntb_sales,
    IF(
      CUSTOM_PARAMETER('include_brand_halo') = 1,
      SUM(C.total_detail_page_view),
      SUM(C.detail_page_view)
    ) AS dpv
  FROM
    amazon_attributed_events_by_traffic_time C
    JOIN dsp_campaign D ON C.campaign_id = D.campaign_id
  WHERE
    user_id IS NOT NULL
    AND (
      total_product_sales > 0
      OR total_detail_page_view > 0
    )
    AND IF(
      CUSTOM_PARAMETER('include_asin_filter') = 1,
      ARRAY_CONTAINS(
        CUSTOM_PARAMETER('asin_filter_list'),
        tracked_asin
      ),
      1 = 1
    )
  GROUP BY
    1,
    2,
    3
  UNION ALL
  -- Gather Conversions data for sponsored ads campaigns
  SELECT
    C.user_id,
    C.conversion_event_dt,
    C.new_to_brand AS ntb_flag,
    IF(
      CUSTOM_PARAMETER('include_brand_halo') = 1,
      SUM(C.total_product_sales),
      SUM(C.product_sales)
    ) AS sales,
    IF(
      CUSTOM_PARAMETER('include_brand_halo') = 1,
      SUM(C.new_to_brand_total_product_sales),
      SUM(C.new_to_brand_product_sales)
    ) AS ntb_sales,
    IF(
      CUSTOM_PARAMETER('include_brand_halo') = 1,
      SUM(C.total_detail_page_view),
      SUM(C.detail_page_view)
    ) AS dpv
  FROM
    amazon_attributed_events_by_traffic_time C
    JOIN sa_campaign S ON C.campaign = S.campaign_name
  WHERE
    user_id IS NOT NULL
    AND (
      total_product_sales > 0
      OR total_detail_page_view > 0
    )
    AND IF(
      CUSTOM_PARAMETER('include_asin_filter') = 1,
      ARRAY_CONTAINS(
        CUSTOM_PARAMETER('asin_filter_list'),
        tracked_asin
      ),
      1 = 1
    )
  GROUP BY
    1,
    2,
    3
),
conversions AS (
  SELECT
    user_id,
    MAX(IF(sales > 0, conversion_event_dt, NULL)) AS last_purchase_dt,
    MAX(IF(dpv > 0, conversion_event_dt, NULL)) AS last_dpv_dt,
    MAX(ntb_flag) AS ntb_flag,
    SUM(sales) AS sales,
    SUM(ntb_sales) AS ntb_sales,
    SUM(dpv) AS dpv
  FROM
    conversions_prep
  GROUP BY
    1
),
-- Gather Impressions data for DSP and sponsored ads
impressions_prep AS (
  -- Gather Impressions data for DSP campaigns
  -- Note: DSP campaign cost is reported in millicents on AMC. Divide the cost value by 100,000 to convert it into local currency.
  SELECT
    I.user_id,
    I.impression_date,
    I.impression_dt,
    CASE
      WHEN I.impression_date BETWEEN CUSTOM_PARAMETER('phase_one_start_date')
      AND CUSTOM_PARAMETER('phase_one_end_date') THEN CUSTOM_PARAMETER('phase_one_name')
      WHEN I.impression_date BETWEEN CUSTOM_PARAMETER('phase_two_start_date')
      AND CUSTOM_PARAMETER('phase_two_end_date') THEN CUSTOM_PARAMETER('phase_two_name')
      ELSE CUSTOM_PARAMETER('phase_three_name')
    END AS strategy,
    CASE
      WHEN I.impression_date BETWEEN CUSTOM_PARAMETER('phase_one_start_date')
      AND CUSTOM_PARAMETER('phase_one_end_date') THEN (
        SECONDS_BETWEEN(
          CAST(CUSTOM_PARAMETER('phase_one_start_date') AS DATE),
          CAST(CUSTOM_PARAMETER('phase_one_end_date') AS DATE)
        ) / (60 * 60 * 24)
      ) + 1
      WHEN I.impression_date BETWEEN CUSTOM_PARAMETER('phase_two_start_date')
      AND CUSTOM_PARAMETER('phase_two_end_date') THEN (
        SECONDS_BETWEEN(
          CAST(CUSTOM_PARAMETER('phase_two_start_date') AS DATE),
          CAST(CUSTOM_PARAMETER('phase_two_end_date') AS DATE)
        ) / (60 * 60 * 24)
      ) + 1
      ELSE (
        SECONDS_BETWEEN(
          CAST(CUSTOM_PARAMETER('phase_three_start_date') AS DATE),
          CAST(CUSTOM_PARAMETER('phase_three_end_date') AS DATE)
        ) / (60 * 60 * 24)
      ) + 1
    END AS duration_days,
    SUM(I.impressions) AS impressions,
    SUM(
      IF(
        CAST(D.ecpm AS DECIMAL(6, 3)) >= 0,
        CAST(D.ecpm AS DECIMAL(6, 3)) / 1000 * I.impressions,
        I.total_cost / 100000
      )
    ) AS cost
  FROM
    dsp_impressions I
    JOIN dsp_campaign D ON I.campaign_id = D.campaign_id
  WHERE
    I.user_id IS NOT NULL
  GROUP BY
    1,
    2,
    3,
    4
  UNION ALL
  -- Gather Impressions data for sponsored ads campaigns
  -- Note: sponsored ads campaign cost is reported in microcents on AMC. Divide the cost value by 100,000,000 to convert it into local currency.
  SELECT
    I.user_id,
    I.event_date AS impression_date,
    I.event_dt AS impression_dt,
    CASE
      WHEN I.event_date BETWEEN CUSTOM_PARAMETER('phase_one_start_date')
      AND CUSTOM_PARAMETER('phase_one_end_date') THEN CUSTOM_PARAMETER('phase_one_name')
      WHEN I.event_date BETWEEN CUSTOM_PARAMETER('phase_two_start_date')
      AND CUSTOM_PARAMETER('phase_two_end_date') THEN CUSTOM_PARAMETER('phase_two_name')
      ELSE CUSTOM_PARAMETER('phase_three_name')
    END AS strategy,
    CASE
      WHEN I.event_date BETWEEN CUSTOM_PARAMETER('phase_one_start_date')
      AND CUSTOM_PARAMETER('phase_one_end_date') THEN (
        SECONDS_BETWEEN(
          CAST(CUSTOM_PARAMETER('phase_one_start_date') AS DATE),
          CAST(CUSTOM_PARAMETER('phase_one_end_date') AS DATE)
        ) / (60 * 60 * 24)
      ) + 1
      WHEN I.event_date BETWEEN CUSTOM_PARAMETER('phase_two_start_date')
      AND CUSTOM_PARAMETER('phase_two_end_date') THEN (
        SECONDS_BETWEEN(
          CAST(CUSTOM_PARAMETER('phase_two_start_date') AS DATE),
          CAST(CUSTOM_PARAMETER('phase_two_end_date') AS DATE)
        ) / (60 * 60 * 24)
      ) + 1
      ELSE (
        SECONDS_BETWEEN(
          CAST(CUSTOM_PARAMETER('phase_three_start_date') AS DATE),
          CAST(CUSTOM_PARAMETER('phase_three_end_date') AS DATE)
        ) / (60 * 60 * 24)
      ) + 1
    END AS duration_days,
    SUM(I.impressions) AS impressions,
    SUM(
      IF(
        CAST(S.ecpm AS DECIMAL(6, 3)) >= 0,
        CAST(S.ecpm AS DECIMAL(6, 3)) / 1000 * I.impressions,
        I.spend / 100000000
      )
    ) AS cost
  FROM
    sponsored_ads_traffic I
    JOIN sa_campaign S ON I.campaign = S.campaign_name
  WHERE
    I.user_id IS NOT NULL
  GROUP BY
    1,
    2,
    3,
    4
),
impressions AS (
  SELECT
    user_id,
    strategy,
    MAX(duration_days) AS duration_days,
    MIN(impression_dt) AS first_impression_dt,
    SUM(impressions) AS impressions,
    SUM(cost) AS cost
  FROM
    impressions_prep
  GROUP BY
    1,
    2
),
-- Combine Conversions and Impressions data at the user-level
combined_prep AS (
  SELECT
    I.user_id AS imp_user_id,
    C.user_id AS con_user_id,
    I.strategy,
    I.duration_days,
    I.first_impression_dt,
    C.last_purchase_dt,
    C.last_dpv_dt,
    C.ntb_flag,
    I.impressions,
    I.cost,
    C.dpv,
    C.sales,
    C.ntb_sales,
    CASE
      WHEN C.last_purchase_dt IS NULL THEN 'NO PURCHASE'
      WHEN C.last_purchase_dt >= I.first_impression_dt THEN 'PRE-PURCHASE'
      WHEN C.last_purchase_dt < I.first_impression_dt THEN 'POST-PURCHASE'
      ELSE 'OTHER'
    END AS impression_timing_purchase,
    CASE
      WHEN C.last_dpv_dt IS NULL THEN 'NO DPV'
      WHEN C.last_dpv_dt >= I.first_impression_dt THEN 'PRE-DPV'
      WHEN C.last_dpv_dt < I.first_impression_dt THEN 'POST-DPV'
      ELSE 'OTHER'
    END AS impression_timing_dpv
  FROM
    impressions I
    LEFT JOIN conversions C ON I.user_id = C.user_id
  ORDER BY
    1,
    2,
    3
),
-- Compute Tentpole strategy exposure at the user-level pre-purchase
user_strategy_purchase AS (
  SELECT
    imp_user_id AS imp_user_id,
    -- Note: purchase_strategy is defined as the unique path comprising Phase One, Phase Two, and Phase Three that the user was exposed to before they made a purchase.
    ARRAY_SORT(COLLECT(DISTINCT strategy)) AS purchase_strategy,
    SUM(duration_days) AS purchase_duration_days
  FROM
    combined_prep
  WHERE
    impression_timing_purchase IN ('NO PURCHASE', 'PRE-PURCHASE')
  GROUP BY
    1
),
combined_purchases AS (
  SELECT
    con_user_id AS pur_user_id,
    MAX(ntb_flag) AS ntb_flag,
    MAX(sales) AS sales,
    MAX(ntb_sales) AS ntb_sales
  FROM
    combined_prep
  WHERE
    impression_timing_purchase IN ('PRE-PURCHASE')
    AND sales > 0
  GROUP BY
    1
),
-- Compute Tentpole strategy exposure at the user-level pre-DPV
user_strategy_dpv AS (
  SELECT
    imp_user_id AS imp_user_id,
    -- Note: dpv_strategy is defined as the unique path comprising Phase One, Phase Two, and Phase Three that the user was exposed to before they viewed the product detail page.
    ARRAY_SORT(COLLECT(DISTINCT strategy)) AS dpv_strategy,
    SUM(duration_days) AS dpv_duration_days
  FROM
    combined_prep
  WHERE
    impression_timing_dpv IN ('NO DPV', 'PRE-DPV')
  GROUP BY
    1
),
combined_dpv AS (
  SELECT
    con_user_id AS dpv_user_id,
    MAX(dpv) AS dpv
  FROM
    combined_prep
  WHERE
    impression_timing_dpv IN ('PRE-DPV')
    AND dpv > 0
  GROUP BY
    1
),
combined_1 AS (
  SELECT
    C.imp_user_id,
    P.pur_user_id,
    U.purchase_strategy AS strategy,
    U.purchase_duration_days AS duration_days,
    P.ntb_flag AS ntb_flag,
    SUM(C.impressions) AS impressions,
    SUM(C.cost) AS cost,
    MAX(P.sales) AS sales,
    MAX(P.ntb_sales) AS ntb_sales
  FROM
    combined_prep C
    LEFT JOIN user_strategy_purchase U ON C.imp_user_id = U.imp_user_id
    LEFT JOIN combined_purchases P ON C.imp_user_id = P.pur_user_id
  GROUP BY
    1,
    2,
    3,
    4,
    5
),
results_prep_1 AS (
  SELECT
    strategy,
    MAX(duration_days) AS duration_days,
    COUNT(DISTINCT imp_user_id) AS unique_reach_purchase,
    COUNT(DISTINCT pur_user_id) AS unique_purchasers,
    SUM(IF(ntb_flag = TRUE, 1, 0)) AS unique_ntb_purchasers,
    SUM(impressions) AS impressions,
    SUM(sales) AS sales,
    SUM(ntb_sales) AS ntb_sales,
    SUM(cost) AS ad_spend,
    AVG(impressions) AS average_impressions
  FROM
    combined_1
  WHERE
    strategy IS NOT NULL
  GROUP BY
    1
),
combined_2 AS (
  SELECT
    C.imp_user_id,
    D.dpv_user_id,
    U.dpv_strategy AS strategy,
    U.dpv_duration_days AS duration_days,
    MAX(D.dpv) AS dpv
  FROM
    combined_prep C
    LEFT JOIN user_strategy_dpv U ON C.imp_user_id = U.imp_user_id
    LEFT JOIN combined_dpv D ON C.imp_user_id = D.dpv_user_id
  GROUP BY
    1,
    2,
    3,
    4
),
results_prep_2 AS (
  SELECT
    strategy,
    MAX(duration_days) AS duration_days,
    COUNT(DISTINCT imp_user_id) AS unique_reach_dpv,
    COUNT(DISTINCT dpv_user_id) AS unique_viewers,
    SUM(dpv) AS dpv
  FROM
    combined_2
  WHERE
    strategy IS NOT NULL
  GROUP BY
    1
),
-- Drill up to the Tentpole strategy-level and pull performance and conversion metrics
results AS (
  SELECT
    A.strategy AS tentpole_path,
    AVG(A.duration_days) AS duration_days,
    SUM(unique_reach_purchase) AS unique_reach_purchase,
    SUM(unique_reach_dpv) AS unique_reach_dpv,
    SUM(unique_viewers) AS unique_viewers,
    SUM(unique_purchasers) AS unique_purchasers,
    SUM(unique_ntb_purchasers) AS unique_ntb_purchasers,
    SUM(impressions) AS impressions,
    SUM(dpv) AS dpv,
    SUM(sales) AS sales,
    SUM(ntb_sales) AS ntb_sales,
    SUM(ad_spend) AS ad_spend,
    AVG(average_impressions) AS average_impressions
  FROM
    results_prep_1 A
    JOIN results_prep_2 B ON A.strategy = B.strategy
  GROUP BY
    1
)
SELECT
  tentpole_path,
  duration_days,
  impressions,
  unique_reach_purchase,
  unique_reach_dpv,
  unique_viewers,
  unique_purchasers,
  unique_ntb_purchasers,
  dpv,
  sales,
  ntb_sales,
  ad_spend,
  sales / ad_spend AS roas,
  unique_reach_purchase / (SUM(unique_reach_purchase) OVER ()) AS unique_reach_percentage,
  sales / (SUM(sales) OVER ()) AS sales_percentage,
  unique_ntb_purchasers / (SUM(unique_ntb_purchasers) OVER ()) AS unique_ntb_purchasers_percentage,
  average_impressions / duration_days AS average_daily_impressions,
  unique_viewers / unique_reach_dpv AS dpv_rate,
  unique_purchasers / unique_reach_purchase AS purchase_rate,
  unique_ntb_purchasers / unique_reach_purchase AS ntb_rate
FROM
  results
```

#### 4.2.2 Tentpole Phase Overlap (Pixel Conversions) 

Before you run this query, refer to **4.1.2 Exploratory query for Pixel conversions** to identify the appropriate campaign filters and Tentpole phase details. 

**Note**: You must use the Pixel conversions custom parameters alongside this query. Click **Open in query editor** to open the query and custom parameters in the AMC Query Editor. You may have to click **Show custom parameters** to display the custom parameters.



```
-- Instructional Query: Tentpole Phase Overlap (Pixel Conversions) --
-- Search for 'UPDATE' to find the clauses where you can customize this query for your use-case. The query will not produce accurate results if the updates labelled REQUIRED are not populated.
/*
 
 ------- Customization Instructions -------
 0) All advertisers should run the exploratory query first to identify the appropriate campaign filters and Tentpole phase details.
 1a) The DSP and sponsored ads campaigns to analyze are defined in the main body of the query. DSP campaigns are identified using campaign IDs and sponsored ads campaigns are identified using campaign names. 
 1b) For both DSP and sponsored ads campaigns, there is a provision to add eCPMs at the campaign-level. Providing an eCPM figure would substitute AMC cost data for campaigns where it has been supplied. You are advised to use eCPM in cases where AMC does not register cost data, such as with Share-of-Voice sponsorship packages. Note that you do not have to provide an eCPM for all campaigns if you have provided it for some - leaving it blank ('') will ensure usage of AMC data for campaigns where this is the case.
 1c) If your scope only includes DSP or sponsored ads campaigns, populate the unneeded clause with blanks. e.g. If you only wanted to analyze DSP campaigns, you would update your sponsored ads clause from ('SA Campaign 1', '1.23') to (, '').
 2) All other inputs are defined via the Custom Parameters functionality. Follow the instructions in the Custom Parameters template to update these.
 2a) Details of Tentpole Phases: How you define your three phases depends on the hypothesis you wish to evaluate. DSP and/or sponsored ads campaigns active during these three time periods are eligible — these campaigns can either be dedicated to the Tentpole event or always-on or both.
 */
WITH dsp_campaign_scope (dsp_campaign_id, ecpm) AS (
  -- REQUIRED UPDATE: Define DSP campaigns to analyze with campaign IDs populated alongside eCPM on an optional basis. Providing an eCPM figure would substitute AMC cost data for campaigns where it has been supplied. (e.g. Campaign 1111111111 below has an associated eCPM of 1.23 provided while Campaigns 2222222222 and 3333333333 do not.)
  VALUES
    (1111111111, '1.23'),
    (2222222222, ''),
    (3333333333, '')
),
sa_campaign_scope (sa_campaign_name, ecpm) AS (
  -- REQUIRED UPDATE: Define sponsored ads campaigns to analyze with campaign names populated alongside eCPM on an optional basis. Providing an eCPM figure would substitute AMC cost data for campaigns where it has been supplied. (e.g. SA Campaign 1 below has an associated eCPM of 1.23 provided while SA Campaign 2 and SA Campaign 3 do not.) If you do not want to include sponsored ads campaigns, change the values to blank (''). From example, from ('SA Campaign 1', '1.23') to ('','')
  VALUES
    ('SA Campaign 1', '1.23'),
    ('SA Campaign 2', ''),
    ('SA Campaign 3', '')
),
dsp_campaign AS (
  SELECT
    A.campaign_id,
    B.ecpm
  FROM
    dsp_impressions A
    JOIN dsp_campaign_scope B ON A.campaign_id = B.dsp_campaign_id
  GROUP BY
    1,
    2
),
sa_campaign AS (
  SELECT
    A.campaign AS campaign_name,
    B.ecpm
  FROM
    sponsored_ads_traffic A
    JOIN sa_campaign_scope B ON A.campaign = B.sa_campaign_name
  GROUP BY
    1,
    2
),
-- Gather Pixel conversions data for DSP and sponsored ads
conversions_prep AS (
  -- Gather Pixel conversions data for DSP campaigns
  SELECT
    C.user_id,
    C.conversion_event_dt,
    SUM(C.conversions) AS pixel_conversions
  FROM
    amazon_attributed_events_by_traffic_time C
    JOIN dsp_campaign D ON C.campaign_id = D.campaign_id
  WHERE
    user_id IS NOT NULL
    AND conversions > 0
    AND conversion_event_category = 'pixel'
  GROUP BY
    1,
    2
  UNION ALL
  -- Gather Pixel conversions data for sponsored ads campaigns
  SELECT
    C.user_id,
    C.conversion_event_dt,
    SUM(C.conversions) AS pixel_conversions
  FROM
    amazon_attributed_events_by_traffic_time C
    JOIN sa_campaign S ON C.campaign = S.campaign_name
  WHERE
    user_id IS NOT NULL
    AND conversions > 0
    AND conversion_event_category = 'pixel'
  GROUP BY
    1,
    2
),
conversions AS (
  SELECT
    user_id,
    MAX(IF(pixel_conversions > 0, conversion_event_dt, NULL)) AS last_conversion_dt,
    SUM(pixel_conversions) AS pixel_conversions
  FROM
    conversions_prep
  GROUP BY
    1
),
-- Gather Impressions data for DSP and sponsored ads
impressions_prep AS (
  -- Gather Impressions data for DSP campaigns
  -- Note: DSP campaign cost is reported in millicents on AMC. Divide the cost value by 100,000 to convert it into local currency.
  SELECT
    I.user_id,
    I.impression_date,
    I.impression_dt,
    CASE
      WHEN I.impression_date BETWEEN CUSTOM_PARAMETER('phase_one_start_date')
      AND CUSTOM_PARAMETER('phase_one_end_date') THEN CUSTOM_PARAMETER('phase_one_name')
      WHEN I.impression_date BETWEEN CUSTOM_PARAMETER('phase_two_start_date')
      AND CUSTOM_PARAMETER('phase_two_end_date') THEN CUSTOM_PARAMETER('phase_two_name')
      ELSE CUSTOM_PARAMETER('phase_three_name')
    END AS strategy,
    CASE
      WHEN I.impression_date BETWEEN CUSTOM_PARAMETER('phase_one_start_date')
      AND CUSTOM_PARAMETER('phase_one_end_date') THEN (
        SECONDS_BETWEEN(
          CAST(CUSTOM_PARAMETER('phase_one_start_date') AS DATE),
          CAST(CUSTOM_PARAMETER('phase_one_end_date') AS DATE)
        ) / (60 * 60 * 24)
      ) + 1
      WHEN I.impression_date BETWEEN CUSTOM_PARAMETER('phase_two_start_date')
      AND CUSTOM_PARAMETER('phase_two_end_date') THEN (
        SECONDS_BETWEEN(
          CAST(CUSTOM_PARAMETER('phase_two_start_date') AS DATE),
          CAST(CUSTOM_PARAMETER('phase_two_end_date') AS DATE)
        ) / (60 * 60 * 24)
      ) + 1
      ELSE (
        SECONDS_BETWEEN(
          CAST(CUSTOM_PARAMETER('phase_three_start_date') AS DATE),
          CAST(CUSTOM_PARAMETER('phase_three_end_date') AS DATE)
        ) / (60 * 60 * 24)
      ) + 1
    END AS duration_days,
    SUM(I.impressions) AS impressions,
    SUM(
      IF(
        CAST(D.ecpm AS DECIMAL(6, 3)) >= 0,
        CAST(D.ecpm AS DECIMAL(6, 3)) / 1000 * I.impressions,
        I.total_cost / 100000
      )
    ) AS cost
  FROM
    dsp_impressions I
    JOIN dsp_campaign D ON I.campaign_id = D.campaign_id
  WHERE
    I.user_id IS NOT NULL
  GROUP BY
    1,
    2,
    3,
    4
  UNION ALL
  -- Gather Impressions data for sponsored ads campaigns
  -- Note: Sponsored ads campaign cost is reported in microcents on AMC. Divide the cost value by 100,000,000 to convert it into local currency.
  SELECT
    I.user_id,
    I.event_date AS impression_date,
    I.event_dt AS impression_dt,
    CASE
      WHEN I.event_date BETWEEN CUSTOM_PARAMETER('phase_one_start_date')
      AND CUSTOM_PARAMETER('phase_one_end_date') THEN CUSTOM_PARAMETER('phase_one_name')
      WHEN I.event_date BETWEEN CUSTOM_PARAMETER('phase_two_start_date')
      AND CUSTOM_PARAMETER('phase_two_end_date') THEN CUSTOM_PARAMETER('phase_two_name')
      ELSE CUSTOM_PARAMETER('phase_three_name')
    END AS strategy,
    CASE
      WHEN I.event_date BETWEEN CUSTOM_PARAMETER('phase_one_start_date')
      AND CUSTOM_PARAMETER('phase_one_end_date') THEN (
        SECONDS_BETWEEN(
          CAST(CUSTOM_PARAMETER('phase_one_start_date') AS DATE),
          CAST(CUSTOM_PARAMETER('phase_one_end_date') AS DATE)
        ) / (60 * 60 * 24)
      ) + 1
      WHEN I.event_date BETWEEN CUSTOM_PARAMETER('phase_two_start_date')
      AND CUSTOM_PARAMETER('phase_two_end_date') THEN (
        SECONDS_BETWEEN(
          CAST(CUSTOM_PARAMETER('phase_two_start_date') AS DATE),
          CAST(CUSTOM_PARAMETER('phase_two_end_date') AS DATE)
        ) / (60 * 60 * 24)
      ) + 1
      ELSE (
        SECONDS_BETWEEN(
          CAST(CUSTOM_PARAMETER('phase_three_start_date') AS DATE),
          CAST(CUSTOM_PARAMETER('phase_three_end_date') AS DATE)
        ) / (60 * 60 * 24)
      ) + 1
    END AS duration_days,
    SUM(I.impressions) AS impressions,
    SUM(
      IF(
        CAST(S.ecpm AS DECIMAL(6, 3)) >= 0,
        CAST(S.ecpm AS DECIMAL(6, 3)) / 1000 * I.impressions,
        I.spend / 100000000
      )
    ) AS cost
  FROM
    sponsored_ads_traffic I
    JOIN sa_campaign S ON I.campaign = S.campaign_name
  WHERE
    I.user_id IS NOT NULL
  GROUP BY
    1,
    2,
    3,
    4
),
impressions AS (
  SELECT
    user_id,
    strategy,
    MAX(duration_days) AS duration_days,
    MIN(impression_dt) AS first_impression_dt,
    SUM(impressions) AS impressions,
    SUM(cost) AS cost
  FROM
    impressions_prep
  GROUP BY
    1,
    2
),
-- Combine Pixel conversions and Impressions data at the user-level
combined_prep AS (
  SELECT
    I.user_id AS imp_user_id,
    C.user_id AS con_user_id,
    I.strategy,
    I.duration_days,
    I.first_impression_dt,
    C.last_conversion_dt,
    I.impressions,
    I.cost,
    C.pixel_conversions,
    CASE
      WHEN C.last_conversion_dt IS NULL THEN 'NO CONVERSION'
      WHEN C.last_conversion_dt >= I.first_impression_dt THEN 'PRE-CONVERSION'
      WHEN C.last_conversion_dt < I.first_impression_dt THEN 'POST-CONVERSION'
      ELSE 'OTHER'
    END AS impression_timing
  FROM
    impressions I
    LEFT JOIN conversions C ON I.user_id = C.user_id
  ORDER BY
    1,
    2,
    3
),
-- Compute Tentpole strategy exposure at the user-level pre-pixel conversion
user_strategy AS (
  SELECT
    imp_user_id AS imp_user_id,
    -- Note: strategy is defined as the unique path comprising Phase One, Phase Two, and Phase Three that the user was exposed to before they converted.
    ARRAY_SORT(COLLECT(DISTINCT strategy)) AS strategy,
    SUM(duration_days) AS duration_days
  FROM
    combined_prep
  WHERE
    impression_timing IN ('NO CONVERSION', 'PRE-CONVERSION')
  GROUP BY
    1
),
combined_conversions AS (
  SELECT
    con_user_id AS pur_user_id,
    MAX(pixel_conversions) AS pixel_conversions
  FROM
    combined_prep
  WHERE
    impression_timing IN ('PRE-CONVERSION')
    AND pixel_conversions > 0
  GROUP BY
    1
),
combined AS (
  SELECT
    C.imp_user_id,
    P.pur_user_id,
    U.strategy AS strategy,
    U.duration_days AS duration_days,
    SUM(C.impressions) AS impressions,
    SUM(C.cost) AS cost,
    MAX(P.pixel_conversions) AS pixel_conversions
  FROM
    combined_prep C
    LEFT JOIN user_strategy U ON C.imp_user_id = U.imp_user_id
    LEFT JOIN combined_conversions P ON C.imp_user_id = P.pur_user_id
  GROUP BY
    1,
    2,
    3,
    4
),
results AS (
  SELECT
    strategy AS tentpole_path,
    MAX(duration_days) AS duration_days,
    COUNT(DISTINCT imp_user_id) AS unique_reach,
    COUNT(DISTINCT pur_user_id) AS unique_converters,
    SUM(impressions) AS impressions,
    SUM(pixel_conversions) AS pixel_conversions,
    SUM(cost) AS ad_spend,
    AVG(impressions) AS average_impressions
  FROM
    combined
  WHERE
    strategy IS NOT NULL
  GROUP BY
    1
)
SELECT
  tentpole_path,
  duration_days,
  impressions,
  unique_reach,
  unique_converters,
  pixel_conversions,
  ad_spend,
  pixel_conversions / ad_spend AS cost_per_conversion,
  unique_reach / (SUM(unique_reach) OVER ()) AS unique_reach_percentage,
  pixel_conversions / (SUM(pixel_conversions) OVER ()) AS pixel_conversions_percentage,
  average_impressions / duration_days AS average_daily_impressions,
  unique_converters / unique_reach AS conversion_rate
FROM
  results
```
