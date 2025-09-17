## Metadata

**id:** b1109615c3699ba0ff4c902a546fea8a0281f20b93e206effab9851c2d3daeb0
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Measure incremental reach and exposure timing across multiple campaigns.
**tags:** []
**use_case_categories:** ['Reach and frequency']
**advertiser_types:** ['On-Amazon conversions', 'Off-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## 1. Introduction
### 1.1 Purpose 
Advertisers who run multiple campaigns or use multiple ad products on Amazon can use this Instructional Query to quantify the incremental audience reached by a specific campaign. It can also help advertisers understand the timing of ad exposures among users who were exposed to both campaigns. The insights from this query will help you to determine whether to continue, stop or increase investment in a certain ad product or a type of campaign.

### 1.2 Requirements 
Advertisers must have data in their AMC instance from two campaigns that ran concurrently (e.g., DSP display vs Streaming TV). This is required to assess the additional unique reach and timing of the one campaign over the other one. Such use case is also applicable when advertisers run campaigns in two distinct ad products such as DSP vs Sponsored Products / Sponsored Display / Sponsored Brands. You must define a focus campaign and base campaign(s). The focus campaign is the campaign that is being evaluated for incremental reach when compared to the base campaign.


## 2. Data Query Instructions
### 2.1 Tables Used
The query can be adjusted for DSP or Sponsored Ads. You have the option to change the table used in the query depending on your use case. 
- **dsp_impressions:** From this table, we are using user_id and impression_dt to identify who was exposed and when, segmented by campaign_id. 
- **sponsored_ads_traffic:** From this table, we are using user_id and event_dt to identify who was exposed and when, segmented by campaign.

### 2.2 Data Returned
The query will return the number and proportion of users exposed to the new channel, grouped by the time of exposure: 1) focus campaign first, followed by the base campaign, 2) base campaign first, followed by the focus campaign, 3) focus campaign only, which measures the incremental reach of the focus campaign. 

### 2.3 Query Templates 
This query isolates all users who were delivered impressions from the focus campaign. It compares the timing between the first impression of the focus campaign to the base campaign(s) to identify the relative timing of impressions. It groups users based on the exposure timing group. See the definitions in section 3.2. The exploratory queries and the query template are located sections 4 and 5. 


## 3. Data Interpretation Instructions
### 3.1 Example Query Results
This data is for instructional purposes only. Your results will differ.


|exposure_timing_group|campaign_group_reach|focus_campaign_reach|reach_perc|
|:---|:---|:---|:---|
|exposed_to_focus_campaign_first|156,781|1,802,688|8.70%|
|exposed_to_base_campaign_first|265,651|1,802,688|14.74%|
|focus_campaign_incremental_reach|1,380,256|1,802,688|76.57%|


### 3.2 Metrics and Dimensions Defined
|metric|definition|
|:---|:---|
|campaign_group_reach|number of unique users reached by the *focus* campaign (new advertising channel), grouped by timing of exposure with respect to the *base* campaigns (all other channels)|
|focus_campaign_reach|total number of unique users reached by the *focus* campaign during campaign flight|
|reach_perc|percentage of reach by exposure timing group/total reach of focus campaign|



|exposure_timing_group dimension value|definition|
|:---|:---|
|exposed_to_focus_campaign_first|number of users who were served an impression from the focus campaign prior to an impression from any of the base campaigns|
|exposed_to_base_campaign_first|number of users who were served an impression from any of the base campaigns prior to exposure to the focus campaign|
|focus_campaign_incremental_reach|number of users who were only exposed to impressions from the focus campaign and were not reached by any of the base campaigns|


### 3.3 Insights and Data Interpretation 
We are comparing users who were exposed to the focus campaign with users exposed to base campaign to understand 1. Whether the focus campaign were able to reach additional audience beyond the base campaign 2. Among those exposed to both base and focus campaigns, what percentage of the users were exposed to the focus campaign first? 

**What is the total unique reach and the incremental reach of the focus campaign?**

The focus campaign reached 1.8 million unique shoppers, of which 1.38 million were not exposed to the base campaigns.

**What was the relative timing of exposure to the focus campaign vs. all other campaigns?**

The results indicate that ~77% of shoppers exposed to the focus campaigns have not been exposed to advertising from any other channels included in the analysis. Roughly 23% of shoppers were served impressions from both the base campaign and the focus campaign. Among those exposed to both campaigns: 1) 15% had already been exposed to the base campaign when they were reached by the focus campaign (exposed_to_base_campaign_first) and 2) 9% were first reached by the focus campaign followed by base campaign (exposed_to_focus_campaign_first).



## 4. Exploratory Queries
Two exploratory queries have been provided below: one for DSP campaigns and another for SP campaigns. 

### 4.1 Exploratory Query for DSP campaigns
Use this exploratory query to make a decision about the DSP campaign IDs to be added in the Incremental Reach with Timing of Exposure query template. 


```
-- Instructional Query: Exploratory Query for Campaign Incremental Reach with Timing of Exposure - DSP --
SELECT
  advertiser,
  campaign_id,
  campaign,
  campaign_start_date,
  campaign_end_date,
  sum(impressions) as impressions
FROM
  dsp_impressions
GROUP BY
  1,
  2,
  3,
  4,
  5
```

### 4.2 Exploratory Query for Sponsored Ads campaigns
Use this exploratory query to make a decision about the Sponsored Products, Sponsored Display and Sponsored Brands campaign names to be added in the Incremental Reach with Timing of Exposure query template. 


```
-- Instructional Query: Exploratory Query for Campaign Incremental Reach with Timing of Exposure - Sponsored Ads --
SELECT
  advertiser,
  campaign,
  ad_product_type,
  campaign_start_date,
  campaign_end_date,
  sum(impressions) as impressions
FROM
  sponsored_ads_traffic
GROUP BY
  1,
  2,
  3,
  4,
  5
```

## 5. Query Template
This query requires a campaign list. Based on the results from the exploratory query above, you can identify or confirm campaign IDs or campaign names, along with the date range to use.  

DSP-only use cases:
This query works by default with base and focus DSP campaign IDs. If you are using DSP campaigns only, update the focus and base CTEs with your DSP campaign IDs. 

Sponsored Ads use cases, with and without DSP:
Search ‘UPDATE’ to find instructions to use this query with Sponsored Ads campaigns. You have the option to make Sponsored Ads click-based or impression-based. 



```
-- Instructional Query: Campaign Incremental Reach with Timing of Exposure --
-- Search for 'UPDATE' to find the optional changes to customize this query.
WITH ---- Start: set your focus campaign(s) ------
/* Required UPDATE: Keep only one 'focus' CTE and delete the 
 other based on your use case. */
-- UPDATE with your FOCUS DSP campaign IDs or delete this CTE
focus AS (
  SELECT
    campaign_id
  FROM
    (
      VALUES
        (1111111111)
    ) AS b(campaign_id)
),
-- OR UPDATE with your SP, SD, SB campaign names and remove the comment block surrounding the CTE below.
-- If you use this CTE, the focus CTE above for DSP must be deleted or the query will not compile.
/*
 focus AS (select campaign from
 (VALUES
 ('campaign_name')
 ) as c(campaign)
 ),
 */
---- End: set your FOCUS campaigns ------
---- Start: set your BASE campaign(s) ------
/* Required UPDATE: Keep only one 'base' CTE and delete the 
 other based on your use case. */
-- UPDATE with your BASE DSP campaign IDs or delete this CTE
base AS (
  SELECT
    campaign_id
  FROM
    (
      VALUES
        (2222222222),
        (3333333333)
    ) AS c(campaign_id)
),
-- OR UPDATE with your SP, SD, SB campaign names and remove the comment block surrounding the CTE below.
-- If you use this CTE, the base CTE above for DSP must be deleted or the query will not compile.
/*
 base AS (select campaign from
 (VALUES
 ('campaign_name_1')
 ,('campaign_name_2')
 ) as c(campaign)
 ),
 */
---- End: set your BASE campaigns ------
focus_reach_timing AS (
  SELECT
    user_id
    /*OPTIONAL UPDATE: If your focus campaign is SP, SD or SB, 
     replace 'impression_dt' with 'event_dt'*/
,
    MIN(impression_dt) focus_first_imp_dt
    /*OPTIONAL UPDATE: If your focus campaign is SP, SD or SB, 
     replace 'dsp_impressions' with 'sponsored_ads_traffic'*/
  FROM
    dsp_impressions
  WHERE
    /*OPTIONAL UPDATE: If your focus campaign is SP, SD or SB, 
     replace 'campaign_id' with 'campaign'. */
    campaign_id IN (
      SELECT
        campaign_id
      FROM
        focus
    )
    /*OPTIONAL UPDATE: uncomment the filter below to make this query 
     click-based, which is a common use case for SP.*/
    -- AND clicks > 0
  GROUP BY
    1
),
base_reach_timing AS (
  SELECT
    user_id --OPTIONAL UPDATE: replace 'impression_dt' with 'event_dt' if your base campaign is SP, SD, SB
,
    MIN(impression_dt) base_first_imp_dt --OPTIONAL UPDATE: replace 'dsp_impressions' with 'sponsored_ads_traffic' if your base campaign is SP, SD, SB
  FROM
    dsp_impressions
    /*OPTIONAL UPDATE: if your base campaign is SP, SD or SB, 
     replace 'campaign_id' with 'campaign'. */
  WHERE
    campaign_id IN (
      SELECT
        campaign_id
      FROM
        base
    )
    /*OPTIONAL UPDATE: uncomment the filter below to make this query 
     click-based, which is a common use case for SP.*/
    -- AND clicks > 0
  GROUP BY
    1
),
-- Timing of first focus campaign impression vs other placements
combined AS (
  SELECT
    COALESCE(fc.user_id, base.user_id) user_id,
    fc.focus_first_imp_dt,
    base.base_first_imp_dt
  FROM
    focus_reach_timing fc
    FULL OUTER JOIN base_reach_timing base ON fc.user_id = base.user_id
  GROUP BY
    1,
    2,
    3
),
combined2 AS (
  SELECT
    'table' tb -- used for final join
,
    CASE
      WHEN base_first_imp_dt IS NULL THEN 'focus_campaign_incremental_reach'
      WHEN focus_first_imp_dt <= base_first_imp_dt THEN 'exposed_to_focus_campaign_first'
      WHEN focus_first_imp_dt > base_first_imp_dt THEN 'exposed_to_base_campaign_first'
      ELSE 'other'
    END exposure_timing_group,
    COUNT(DISTINCT user_id) reach
  FROM
    combined
  WHERE
    focus_first_imp_dt IS NOT NULL
  GROUP BY
    1,
    2
),
tot_focus_reach AS (
  SELECT
    'table' tb -- used for final join
,
    COUNT(DISTINCT user_id) tot_focus_reach
  FROM
    focus_reach_timing
  GROUP BY
    1
),
final AS (
  SELECT
    c2.exposure_timing_group,
    c2.reach,
    tot.tot_focus_reach
  FROM
    combined2 c2
    LEFT JOIN tot_focus_reach tot ON c2.tb = tot.tb
)
SELECT
  exposure_timing_group,
  reach AS campaign_group_reach,
  tot_focus_reach AS focus_campaign_reach,
  reach / tot_focus_reach AS reach_perc
FROM
  final
GROUP BY
  1,
  2,
  3,
  4

```
