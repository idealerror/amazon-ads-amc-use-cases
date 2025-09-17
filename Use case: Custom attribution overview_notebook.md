## Metadata

**id:** 72313993a488c7c1821e67c6fdd7a366cd434ef65ea14a2aae4d96fd16a2e627
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Apply custom attribution models with lookback windows up to 28 days.
**tags:** []
**use_case_categories:** ['Custom attribution']
**advertiser_types:** ['On-Amazon conversions', 'Off-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## 1. About this Documentation
This documentation provides an overview of two data sources that you can use to perform custom attribution: `conversions` and `conversions_with_relevance`. Queries provided in this documentation are exploratory to help you to understand the data sources. Custom attribution is supported for pixel conversion events and ASIN conversion events, such as purchases and detail page views. Additionally, this Instructional Query (IQ) also explains how to query Subscribe and Save (SnS) events. 

### 1.1 Standard reporting vs custom attribution in AMC

| Category | Standard Reporting for Amazon DSP and Sponsored Ads | Custom Attribution in AMC | To Align with Standard Reporting |
|----------|--------------------------------------------------|-------------------------|--------------------------------|
| Eligible traffic events | DSP campaigns allow attribution to clicks and views. Sponsored ads allows only clicks for CPC billing (i.e., most sponsored ads campaigns), and clicks and views for vCPM or Fixed Price. | Default is clicks only for Sponsored Products, Sponsored Brands, and Sponsored Display. Default is clicks and views for Amazon DSP. For non-view-aware placements, impressions are included by default. Traffic events can be customized for each ad type to include clicks, impressions, or both. | Retain the default of click-only attribution for Sponsored Ads and click and view attribution for Amazon DSP. Sponsored Ads CPC placements typically appear in high-intent shopping contexts, where ads are highly viewable and shoppers are already close to converting. Attributing conversions to views in these cases can lead to overcrediting and will shift credit away from ads that directly influenced the purchase. |
| Eligible conversion events | Eligible conversion events are determined by campaign configuration. Conversions for all tracked off-Amazon events, tracked product ASINs, and other ASINs from the same brands can be attributed. "Tracked" refers to events and products associated with the campaign during setup. | Default is all ASINs or Off-Amazon Conversion events relevant to the campaigns. Filtering to fewer ASINs or events is customizable. | Retain default inclusion of all relevant ASINs and events manager signals. The default template in the IQ will capture conversions that were indicated as relevant during campaign setup. |
| Attribution window | 14 days (7 days for Sponsored Products for Sellers) | Default is 28 days. Up to 28 days is customizable | 14 days |
| Credit assignment | Amazon's last touch attribution considers each campaign's relevance to the converted product, then uses a last-touch model that prioritizes the last click over the last view. Amazon uses this ranking for last-touch attribution: * Promoted ASIN ad clicks, * Highly relevant** brand halo ad clicks, * Promoted ASIN ad views, * Highly relevant** brand halo ad views, * Brand halo ad clicks, * Brand halo ad views  | Default is clicks prioritized over views (or impressions for non-view-aware placements). Customizable to choose last touch, position based, linear, or first touch | Last touch with default of clicks prioritized over views/impressions aligns closely with standard attribution outcomes. |

** Highly relevant refers to ASINs from the same brand and product subcategory as promoted ASINs


### 1.2 Custom Attribution Instructional Queries - Quick Links

[First Touch Attribution](/marketing-cloud/instructional-queries/578a0a9ec3875507beade48f562e1ae6189fbf6ea203a7d34508cb2e24c6d875)
| [Last Touch Attribution](/marketing-cloud/instructional-queries/e793be327be2aa9f4fbdbdcaf3f3f822ed8819440beb15f94b377b07b08cf0bf)
| [Linear Attribution](/marketing-cloud/instructional-queries/95212a0253ea4d060cd94db10de7d3dfd542232fa786e1d74852ae3d8bd58139)
| [Position Based Attribution](/marketing-cloud/instructional-queries/cdf597257dcf43b65c1af8dd14480433e4dbe513469575e8b064f62029c94e9b)


## 2. Attribution Introduction

### 2.1 What is an Attribution Model?
When you invest in multiple ad products and advertising tactics with Amazon Ads, it’s likely that your users are exposed to multiple campaigns before they convert. Attribution is the systematic process of determining how credit should be assigned to conversions from users exposed to your campaigns. An attribution model is the rule, or set of rules, that determines how credit for sales or conversions is assigned to touchpoints in conversion paths. There are two types of attribution models: rule-based and algorithmic. 

As the name suggests, rule-based models assign credit to campaigns based on pre-defined rules. For example, the last touch attribution model assigns 100% credit to the final touchpoints that immediately precede sales or conversions. In contrast, the first touch attribution model assigns credit only to the first touchpoints after a specified date in the conversion path. Rule-based models can be created using SQL only and do not require advanced statistical modeling. In AMC, there is no separate feature or special designation for performing attribution. Custom attribution is performed by running SQL queries that join data from the custom attribution datasets to the existing traffic datasets. Because SQL must be used over AMC’s event-level data, the query templates we provide in the Instructional Query Library are exclusively rule-based. 

Algorithmic attribution models (i.e. Markovian Chain, logistic regression, or linear regression) require advanced statistical techniques. Many algorithmic models use R or Python programming languages to test and train the models. AMC users with experience in creating algorithmic models have written their own AMC SQL queries to generate an input file for use in an algorithmic model. AMC does not provide template examples or support for these use cases today. 


### 2.2 Amazon Ads Standard Attribution Models
If you have received a report with attributed conversions by campaign from Amazon Ads or have used one of the `attributed_` data sources in AMC (`amazon_attributed_events_by_traffic_time` or `amazon_attributed_events_by_conversion_time`), you are already familiar with rule-based models for ASIN conversions and pixel conversions. ASIN conversions use either a brand-based model or a merchant-based model. Brand-based models are used for advertisers investing in Sponsored Brands, Sponsored Display, Sponsored Products for Vendors, and the Amazon DSP. Merchant-based models are used for Sellers who invest in Sponsored Products. 

The information below has been provided for background purposes only as an introduction to attribution. It is not possible to use the AMC custom attribution data sources (`conversions` and `conversions_with_relevance`) to generate the same attributed conversions in the `attributed_` data sources.

#### 2.2.1 Amazon Ads Standard Attribution Model for Merchant-Based Ad Products (ASIN conversions)
Sponsored Products for Sellers uses last click attribution based on the most recent click. Sponsored Products for Sellers uses a 7-day attribution window. The programmatic SSPA API offers 1, 7, 14, 30 day windows for attribution.

#### 2.2.2 Amazon Ads Standard Attribution Model for Brand-Based Ad Products (ASIN conversions)
All Amazon Advertising products for brands (DSP, Sponsored Display, Sponsored Brands, and Sponsored Products for Vendors) compete with each other for attribution. Ads are eligible for attribution if the shopper clicked or viewed them within 14 days prior to conversion. If the shopper interacted with multiple ads, attribution considers each campaign's relevance to the sold product, then uses a last-touch model that prioritizes the last click over the last view for promoted and highly relevant brand halo ASINs. Clicks are prioritized over views for promoted and highly relevant products because they are a stronger engagement signal. Views from promoted and highly relevant brand halo products are prioritized over clicks from brand halo products that are not considered to be highly relevant. View Appendix 1 for additional information.

#### 2.2.3 Amazon Ads Standard Attribution Model for Pixel Conversions (Amazon DSP only)
Pixel conversions are eligible for attribution if a valid pixel was tracked to a campaign. Ads that track valid pixels are eligible for attribution if the shopper clicked or viewed them within 14 days prior to conversion. If the shopper interacted with multiple ads that tracked the same pixel, a last-touch model prioritizes the last click over the last view. Clicks are prioritized over views because they are a stronger engagement signal.



### 2.3 Custom Attribution Rule-Based Models with AMC

Custom Attribution with AMC gives you control over 1) the lookback window (up to 28 days) and 2) the attribution model, which determines how to assign credit to touchpoints on the conversion path. We have published instructional queries on the different rule-based attribution models. You can run these queries to compare how different attribution models impact the valuation of your media channels. There are pros and cons of using each of the custom attribution models. Select an attribution model based on your advertising objective and use case. The AMC Instructional Query Library has examples of [First Touch](/marketing-cloud/instructional-queries/578a0a9ec3875507beade48f562e1ae6189fbf6ea203a7d34508cb2e24c6d875), [Last Touch](/marketing-cloud/instructional-queries/e793be327be2aa9f4fbdbdcaf3f3f822ed8819440beb15f94b377b07b08cf0bf), [Linear](/marketing-cloud/instructional-queries/95212a0253ea4d060cd94db10de7d3dfd542232fa786e1d74852ae3d8bd58139), and [Position Based](/marketing-cloud/instructional-queries/cdf597257dcf43b65c1af8dd14480433e4dbe513469575e8b064f62029c94e9b) Models, which are outlined below. 

The custom attribution models are compared in the table below with this Amazon Ads scenario:

> You reach a cohort of customers through your Streaming TV ad. The cohort of customers that saw your ad go to Amazon.com on the same day and they are exposed to your display ad. One week later, they search for your brand on Amazon, click on your sponsored products ad, and purchase your product.

**The path to conversion of this cohort of customers is:**
Streaming TV → Display → Sponsored Products → Purchase


### 2.4 Rule-Based Attribution Models Comparison Table
The table below compares the attribution models. To view example output, refer to the instructional queries in Section 2.5. A common misconception is that custom attribution provides path to conversion insights as the output. While the sequence of ad exposures is used to derive the insights in the SQL query, custom attribution does not produce path to conversion insights. Search the instructional query library for "path" to view path to conversion queries. The two models that split credit (linear and position-based) can be thought of as reporting weighted sums of attributed coversions by campaign. 




Model                                                                                                                                                                                                                                                                         | How credit is assigned                                                                                                                                                                                            | Pros                                                                                                                                                                                                                                                                                                        | Cons                                                                                                                                                                                                                                                                                       
:----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
**First Touch** attribution gives full credit to the first touch point (impression or click) of an ad interaction.                                                                                                                                                                | The final output from the instructional query will include one row for each campaign with key metrics such as as the attributed conversions and the conversion rate. Conversions are only attributed to the campaign when the given campaign was the first touchpoint. In the example above, Streaming TV receives 100% of the credit for the purchases.                                                                                                                           | The first touch model helps you to understand how shoppers are discovering your brand. If your goal is to generate awareness and expand reach, then this attribution model will allow you to see which ad product / campaign is best at attracting shoppers.                                                | First touch doesn’t show the full picture. Other campaigns do not receive any credit. In the multi-touch conversion process, any touch that follows the first touch will not be considered.                                                                                                       
**Last Touch** attribution assigns full credit to the last ad a user saw or clicked.                                                                                                                                                                                              | The final output from the instructional query will include one row for each campaign with key metrics such as as the attributed conversions and the conversion rate. Conversions are only attributed to the campaign when the given campaign was the last touchpoint. In the example above, Sponsored Products receives full credit for the purchases.                                                                                                                                | Last Touch allows you to see which touchpoint was last seen by a shopper before converting. If you are focusing on driving conversions, Last Touch will allow you to see which ad product / campaign is best at converting shoppers. This model may be a good fit for you if you have a short buying cycle. | Last Touch ignores all the touch points before the final interaction. Many of the touchpoints prior to that last interaction will be just as important at attracting relevant shoppers and bringing them down the funnel.                                                                  
**Linear** attribution assigns each touchpoint in the conversion path with equal credit for the conversion.                                                                                                                                                                 | The final output from the instructional query will include one row for each campaign with key metrics such as as the attributed conversions and the conversion rate. Conversions are attributed all campaigns with a touchpoint and credit is split evenly. In the example above, there are 3 touchpoints. Each touch receives 33.3% of the credit.                                                                                                                   | Linear Attribution provides you with a more balanced look at your whole advertising strategy when compared to a single-event attribution model. It helps to demonstrate the value of a multichannel or multi-touch strategy.                                                                          | Linear attribution assumes all touchpoints are equally important. However, some advertising strategies are more effective than others, and this model will not surface that insight. This model can lead you to overvalue some channels and undervalue others based on the way it assigns credit.
**Position-based** attribution splits credit between the first and the last touchpoints on your customer’s conversion path. Weights assigned to these two touchpoints are customizable. Work with your media / campaign manager to determine the weights for the two touchpoints. | The final output from the instructional query will include one row for each campaign with key metrics such as as the attributed conversions and the conversion rate. Conversions are attributed to the campaign when the given campaign was either the first or the last touchpoint based on weights you assign. The first touch point receives credit (such as 30%) and the last touch point receives credit (such as 70%). For the example above, if you assign 30% credit to the first touch (Streaming TV) and 70% credit to the last touch (Sponsored Products), your measurement values the last touch more than the first touch. | This model captures the impact of top and bottom-of-funnel activities, both of which are critical to the conversion path.                                                                                                                                                                                    | This model assume that the two most important interactions a customer has with your business will be the very first and the very last before conversion, while ignore all the touchpoints in between.  










Sections 3-5 describe the two custom attribution datasets and compare/contrast them to other conversion datasets from AMC and Amazon Ads. 


### 2.5 Custom Attribution Instructional Queries
Access the custom attribution instructional queries below to view more details on each model and example output. 

- [First Touch Attribution](/marketing-cloud/instructional-queries/578a0a9ec3875507beade48f562e1ae6189fbf6ea203a7d34508cb2e24c6d875)

- [Last Touch Attribution](/marketing-cloud/instructional-queries/e793be327be2aa9f4fbdbdcaf3f3f822ed8819440beb15f94b377b07b08cf0bf)

- [Linear Attribution](/marketing-cloud/instructional-queries/95212a0253ea4d060cd94db10de7d3dfd542232fa786e1d74852ae3d8bd58139)

- [Position Based Attribution](/marketing-cloud/instructional-queries/cdf597257dcf43b65c1af8dd14480433e4dbe513469575e8b064f62029c94e9b)



## 3. Custom Attribution Datasets - Overview
AMC provides two data sources (`conversions`, and `conversions_with_relevance`) and instructional queries that will allow you to create custom attribution models. The data sources have both ASIN and pixel conversions. Sections 3.1 - 3.3 apply to ASIN conversion advertisers. Sections 3.4 - 3.6 apply to pixel conversion advertisers.  

### 3.1 ASIN Conversions - Two Custom Attribution Datasets
This section will compare these two data sources, which are referred to together as ‘custom attribution data sources’. Sections 3.2 and 3.3 will compare the custom attribution data sources to the `attributed_` data sources. 

The data source conversions contains the subset of relevant conversions, such as purchases, detail page views, add-to-cart, and the first subscribe and save events. See **Custom Attribution Datasets - Column Definitions** for definitions of all columns. A conversion is deemed to be relevant to your AMC instance if conditions are met related to traffic and ASINs. First, the user who converted was served an impression or had a click event within the 28-day period before the conversion event occurred. Traffic events that are considered include: your impressions and clicks from `sponsored_ads_traffic`, and impressions and clicks from `dsp_impressions`. Second, the conversion is from an ASIN that was either promoted or from the brand family of an ASIN that was promoted (brand halo) from an Amazon DSP or Sponsored Ads account in your instance. The 28-day period doubles the window from the brand-based standard attribution data in the data sources `amazon_attributed_events_by_conversion_time` and `amazon_attributed_events_by_traffic_time`. Third, conversions from ASINs are relevant if there was at least one attributed conversion for the ASIN in `amazon_attributed_events_by_conversion_time` during the 28-day period before the conversion event. Even though the custom attribution and `attributed_` data sources have the same ASINs, there is generally a higher volume of conversions per ASIN in the custom attribution data sources when compared to `amazon_attributed_events_by_conversion_time`. This is because the custom attribution data sources have 1) a longer lookback window, 2) are not competition-aware, and 3) include relevant conversions from both viewable and non-viewable impressions.

The data source `conversions_with_relevance` has the same conversions as the `conversions` data source, but it also includes the campaign and other columns that are campaign- or advertiser-dependent. Since it’s possible for a conversion to be relevant to more than one campaign, each conversion event in this data source could be represented on multiple rows, one per relevant campaign. For example, if user A is exposed to both campaign_a and campaign_b on the same day and then purchases, the purchase conversion will be recorded on two rows, once for campaign_a and once for campaign_b. For illustration purposes, the conversion_id is 123456. 

#### single conversion example from `conversions_with_relevance`

| conversion\_id | conversion | event\_category | campaign    |
| :------------------------ | :-------------------- | :------------------------- | :-------------------------------------------------- |
| 123456         | 1          | purchase        | campaign\_a |
| 123456         | 1          | purchase        | campaign\_b |



#### single conversion example from `conversions`
 
| conversion\_id | conversion | event\_category |
| -------------- | ---------- | --------------- |
| 123456         | 1          | purchase        |



When querying conversions_with_relevance for your insights, please consider the following. 

Campaigns are eligible to be relevant to conversions for up to 60 days after the campaign end date if the user had a qualifying traffic event from any other campaign in the instance during the previous 28-day period.
The “conversions” and “conversions_with_relevance” tables are updated to contain signals related to ad-attributed and pixel conversion analysis only, to build dedicated queries to measure only ad-attributed conversions and present refined attribution results. 

By default, only conversions that have a traffic events associated in the last 28 days are returned. If you yet want to further filter by traffic, we suggest you JOIN traffic events as part of your query, as indicated below. 


```
-- Instructional Query: Custom Attribution for ASIN conversions - Exploratory Query `conversions_with_relevance`  --
WITH traffic AS (
  SELECT
    DISTINCT campaign,
    ad_product_type,
    user_id
  FROM
    -- gets all sponsored ads campaigns from the 28-day period before the start of the query time window you select.
    TABLE(
      EXTEND_TIME_WINDOW('sponsored_ads_traffic', 'P28D', 'P0D')
    )
  WHERE
    user_id IS NOT NULL
  UNION ALL
  SELECT
    DISTINCT campaign,
    'dsp' AS ad_product_type,
    user_id
  FROM
    -- gets all dsp campaigns from the 28-day period before the start of the query time window you select.
    TABLE(
      EXTEND_TIME_WINDOW('dsp_impressions', 'P28D', 'P0D')
    )
  WHERE
    user_id IS NOT NULL
),
conv AS (
  SELECT
    DISTINCT campaign,
    user_id
  FROM
    conversions_with_relevance c
  WHERE
    c.user_id IS NOT NULL
)
SELECT
  c.campaign,
  ad_product_type,
  COUNT(DISTINCT c.user_id) AS count_distinct_users_with_conversion
FROM
  conv c
  JOIN traffic t ON c.campaign = t.campaign
  AND c.user_id = t.user_id
GROUP BY
  1,
  2
```

If your use case does not require the campaign and you are only interested in the sum of purchases used in custom attribution use cases, it’s best to use `conversions`, where each row is a unique conversion event. If your use case requires information about the campaign or if the conversion was considered to be from a promoted or brand halo ASIN, only `conversions_with_relevance` has this information. While it’s possible to use either data source for custom attribution queries, our instructional queries use `conversions_with_relevance` for rule-based models. This gives you the convenience of having the campaign and brand halo information included in the same data source with the conversions.


### 3.2 The Attributed vs Custom Attribution Datasets for ASIN conversions

The introduction of this document reviewed the concept of rule-based attribution models. The standard attribution model used at Amazon Ads allows only one campaign to officially receive credit for a single conversion event. These conversions are referred to as 'attributed conversions’ included in the ‘attributed data sources’, which are `amazon_attributed_events_by_conversion_time` and `amazon_attributed_events_by_traffic_time`. See Section 2.2 for more information. 

#### How are the attributed data sources similar to the custom attribution data sources for ASIN conversions?
**ASINs**: Both datasets have conversions from the same ASINs. The conversions included in the datasets are from the brand of ASINs that were tracked to a campaign - either promoted or brand halo ASINs. A brand halo ASIN is defined as an ASIN that was not promoted, but belongs to the same brand of a promoted ASIN. Conversions from brands that were never tracked to a campaign are not included in the datasets even if they are owned by the company that is running ads. 

**Amazon Ads traffic is required**: Conversions from users who were not served an ad from one of the campaigns in the AMC instance are never included in the datasets. The datasets only have conversions from users who were delivered an impression or clicked an ad from any advertising campaigns in your AMC instance. They differ on the specific detail of the traffic requirements, which will be reviewed in the next section. 

#### How are the attributed data sources different from the custom attribution data sources for ASIN conversions?
**Amazon Ads traffic type**: The conversions in the attributed data sources have traffic requirements that differ based on ad product type. For example, DSP campaigns require a viewable impression as one requirement for a conversion to be considered as attributed to the campaign, whereas Sponsored Products requires a click. Sponsored Display requires a click for CPC campaigns or a viewable impression for vCPM campaigns. Conversions in the custom attribution data source require an impression event or a click event (if the campaign does not report impressions). Conversions from users who were delivered an impression only, but did not click a Sponsored Products ad, would never be attributed to a Sponsored Products campaign in the attributed datasets. However, these conversions are included in the custom attribution datasets when other requirements for relevancy are satisfied. 

**Viewability**: The custom attribution conversions do not consider viewability, but the attributed conversions do. If a user was delivered an impression that was not viewable and then converted within a 14-day time period, this conversion would not meet the qualifications to be attributed to a campaign. However, this conversion would appear in the custom attribution dataset if other requirements for relevancy are satisfied. While the custom attribution data sources provide conversions from users who viewed and did not view an Amazon DSP ad, all custom attribution queries, by default, only attribute a conversion to a DSP viewable impression. 

**Lookback window**: The lookback window is extended to 28 days with the custom attribution data. For the `attributed_` data sources, the lookback window differs for Brand-Based Ad Products and Merchant-Based Ad Products. For Brand-Based Ad Products, conversions must take place within 14 days of an eligible traffic event to meet attributed conversions eligibility requirements. Merchant-Based Ad Products (Sponsored Products for Sellers) uses a 7-day attribution window; the programmatic SSPA API offers 1, 7, 14, 30 day windows for attribution for Sellers.

**Competition awareness**: The attributed conversions are de-duped against any competitors advertising the same ASINs. For example, an agency and a direct advertiser are both promoting ASIN123 in separate Amazon Ads accounts. A customer clicks the direct advertiser’s ad on Monday and the agency’s ad on Tuesday and converts on Wednesday. In this scenario, only the agency’s campaign will receive credit for the conversion in the attributed dataset, since the ad had the last click. The custom attribution dataset does not take into account the competition, so this conversion is relevant to both campaigns. 

**Alignment with DSP UI and Sponsored Ads console reporting**: The custom attribution dataset is unique to AMC and a comparable dataset cannot be found in any other Amazon reporting from Ads or retail. As a result, there is no data source outside of AMC that it is expected to match. The attributed datasets align to reporting from the DSP UI or the Sponsored Ads console. For details, refer to [Attributed events and conversion tables](https://advertising.amazon.com/help/GHNTMYL5DJBJEQC9) in Support Center.




### 3.3 Comparison of the Four AMC Data Sources with Conversion Events for ASIN conversions

Attributed data sources are `amazon_attributed_events_by_conversion_time` and `amazon_attributed_events_by_traffic_time`.
Custom attribution data sources are `conversions_with_relevance` and `conversions`. 

#### 3.3.1 Comparison of Use Cases and ASIN Conversions


|\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_  | **Attributed_Data_Source_Name:**  `amazon_attributed_events_by_conversion_time`                                                                                                                                                                                                                                                                                                                    | **Attributed_Data_Source_Name:**   `amazon_attributed_events_by_traffic_time`            | **Custom_Attribution_Data_Source_Name:** `conversions_with_relevance`                                                                                                                                                                                                                                                                                                                                                                                           | **Custom_Attribution_Data_Source_Name:** `conversions`                                                                                                                                                                                                                                                                                 |
| :------------------------------------------------------------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|Example use case for ASIN conversion advertisers |How many ad-attributed users purchased my product during the first week of December?|How many ad-attributed purchases were from ads I served during Prime Day?|Custom attribution insights where the campaign and promoted/halo status are required for insights.  Example insights include multi-touch attribution across multiple campaigns / campaign types from promoted ASINs only.|What is the average 28-day customer value for my brand?|
|What ASIN conversions are included?|Ad-attributed conversions based on the Amazon Ads Standard Attribution Model from ASINs that were promoted + ASINs from the same brand family as those promoted (brand halo).|Same as `amazon_attributed_events_by_conversion_time`.|Conversions that are relevant to any campaign in an AMC instance are included.  A conversion is relevant to if conditions are met related to traffic and ASINs. First, the user who converted was served an impression or had a click event within the 28-day period before the conversion event occurred. Second, the conversion is from an ASIN that was either promoted or from the brand family of an ASIN that was promoted (brand halo).|Same as `conversions_with_relevance`.|
|Eligible ASINs|The conversions included in the `attributed_` datasets are from the brands of ASINs that were tracked to a campaign - either promoted or brand halo ASINs. A brand halo ASIN was not promoted, but belongs to the same brand of a promoted ASIN. Conversions from brands that were never tracked to a campaign are not included in the datasets, even if they are owned by the company that is running ads.|Same as `amazon_attributed_events_by_conversion_time`.|The custom attribution data sources have the same ASINs as the `attributed_` data sources. Conversions from ASINs are eligible to be included if there was at least one attributed conversion for the ASIN in `amazon_attributed_events_by_conversion_time` during the 28-day period before the conversion event. Even though the custom attribution and `attributed_` data sources have the same ASINs, there is generally a higher volume of conversions per ASIN in the custom attribution data sources when compared to `amazon_attributed_events_by_conversion_time`. This is because the custom attribution data sources have 1) a longer lookback window, 2) are not competition-aware, and 3) include relevant conversions from both viewable and non-viewable impressions.|Same as `conversions_with_relevance`.|
|Includes 'engagement scope' info for ASINs? For ex, was this ASIN promoted or part of the brand halo?|Yes|Yes|Yes|No. The purpose of the conversions table is to de-dup the relevant conversions by excluding the relevant campaign names and IDs. There are potentially multiple relevant campaigns for every row in the conversions table. As a result, there are potentially multiple engagement_scope / halo_code combinations.|
|What type of brand halo conversions are included for ASINs?|Halo conversions are conversions from the same brand as the promoted ASIN. Conversions are either 'promoted' or 'brand halo'. Unlike the `conversions_with_relevance` data source, 'brand halo' conversions from the `attributed_` tables are not segmented by type of brand halo conversion.|Same as `amazon_attributed_events_by_conversion_time`.|Yes and brand halo conversions are segmented by type. See the campaign relevance details below.|Yes and brand halo conversions are segmented by type. See the campaign relevance details below.|
|ASIN Conversion (De)Duplication|Conversions are always de-duped across campaigns. If a user was exposed to two campaigns, only  one campaign gets credit according to the Amazon Ads Standard Attribution Model.|Same as `amazon_attributed_events_by_conversion_time`.|Includes the same conversions from conversions, but also includes the  relevant campaigns and conversions that could be duplicated.|Conversions are de-duped. The campaign, 'engagement scope' and 'halo code' are omitted.|
|Includes ASIN conversions from customers not served ads?|No|No|No|No|


#### 3.3.2 Comparison of Time and Other Attributes for campaigns that promote ASINs


|\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_  | **Attributed_Data_Source_Name:**  `amazon_attributed_events_by_conversion_time`                                                                                                                                                                                                                                                                                                                    | **Attributed_Data_Source_Name:**   `amazon_attributed_events_by_traffic_time`            | **Custom_Attribution_Data_Source_Name:** `conversions_with_relevance`                                                                                                                                                                                                                                                                                                                                                                                           | **Custom_Attribution_Data_Source_Name:** `conversions`                                                                                                                                                                                                                                                                                 |
| :------------------------------------------------------------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :------------------------------------------------------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|Maximum Lookback (ASIN conversion date - impression date)|14 days for Brands; 7 days for Sellers |14 days for Brands; 7 days for Sellers |28 days|28 days|
|Can the lookback window be adjusted to less than the maximum?|Yes|Yes|Yes|Yes|
|Time window corresponds to|Conversion time|Traffic time (the impression for DSP or the click for SP)|Conversion time|Conversion time|
|Corresponds to other non-AMC Amazon sources|ADSP UI/API|Sponsored Products and Sponsored Display UI/API|n/a|n/a|
|Maximum number of campaigns per ASIN conversion event|1|1|no set limit|no set limit|
|Campaign information included?|Yes|Yes|Yes|No|
|Contains campaign setup dimensions under the campaign (line item, ad group, ad).|Yes|Yes|No|No|
|Viewability|Conversions are only included if a user viewed  an ad.|Conversions are only included if a user viewed  an ad.|Relevant conversions from both viewable and non-viewable impressions are included with the option to exclude non-viewable impressions using the dsp_views table, which only contains viewability signals for DSP campaigns. Sponsored Ads viewability signals not provided.|Relevant conversions from both viewable and non-viewable impressions are included. Non-viewable conversions cannot be separated from viewable conversions.|
|Includes the request_tag?|Yes, the request_tag in the `attributed_` datasets for DSP campaigns acts as an impression ID to map the attributed conversion back to the impression that is attributed to the conversion event. The reason why there is a request_tag in the `attributed_` datasets is because there is a 1:1 relationship between the attributed conversion and the associated traffic event.|Same as `amazon_attributed_events_by_conversion_time`.|No, the custom attribution datasets do not include the request_tag. There could be multiple traffic events that are led to the conversion being relevant to the campaign. The Instructional Queries help you to understand the relationship between the conversion events and the traffic events based on the user's journey across campaigns.|Same as `conversions_with_relevance`.|


### 3.4 Pixel Conversions - Two Custom Attribution Datasets

While pixel and ASIN conversions are included in the same data sources, the campaign relevancy rules are different, since pixel conversions have fewer restrictions. Most notably, pixel conversions are not subject to user-level filtering based on the 28-day lookback window. If a pixel was tracked to a DSP campaign, conversions for this pixel from all users will appear in the custom attribution data sources, even if the user was not delivered a DSP ad impression. The custom attribution data sources do not require an ad impression or click to be relevant. The custom attribution data sources provide all pixel conversions from users who were ad exposed and not ad exposed. This section will compare these two data sources, which are referred to together as ‘custom attribution data sources’. 

The data source `conversions` contains all pixel conversions from pixels that were tracked to your DSP campaigns. A pixel conversion is relevant if it was tracked to a campaign in your instance. Even though the custom attribution and `attributed_` data sources have the same pixel IDs, there is generally a higher volume of conversions per pixel ID in the custom attribution data sources when compared to `amazon_attributed_events_by_conversion_time` and there are three explanations. First, the custom attribution data sources do not require an ad impression or click to be relevant. Second, the lookback window for attributed pixels is 14 days, while the lookback window for pixel conversions in the custom attribution data is only constrained by the time when the pixel ID was tracked to the campaign or the time window of your query. For example, if you started tracking a pixel ID to a campaign on February 1, you will have pixel conversions from this date going forward. Third, pixel conversions include relevant conversions from both viewable and non-viewable impressions.

The data source `conversions_with_relevance` has the same conversions as the `conversions` data source, but it also includes the campaign and other columns that are campaign- or advertiser-dependent. Since it’s possible for a pixel conversion to be relevant to more than one campaign, each conversion event in this data source could be represented on multiple rows, one per relevant campaign. For example, if user C is exposed to both campaign_c and campaign_d on the same day and then has a pixel conversion. This pixel conversion will be recorded on two rows, once for campaign_c and once for campaign_d. For illustration purposes, the conversion_id is 56789. 

#### single pixel conversion example from `conversions_with_relevance`

| conversion\_id | conversion | event\_category | campaign    |
| :------------------------ | :-------------------- | :------------------------- | :-------------------------------------------------- |
| 56789         | 1          | pixel        | campaign\_c |
| 56789         | 1          | pixel        | campaign\_d |


#### single pixel conversion example from `conversions`
 
| conversion\_id | conversion | event\_category |
| -------------- | ---------- | --------------- |
| 56789         | 1          | pixel        |



When querying `conversions_with_relevance` for your insights, please consider the following. 
- Campaigns are eligible to be relevant to conversions after the campaign end date if the pixel is still active. 
- The conversions_with_relevance contains tables to contain signals related to ad-attributed and pixed conversion analysis only. 
As an example, use the query below to get a count of users who 1) were delivered an impression from the campaign during the previous 28-day period and 2) had any conversion event that was relevant to the campaign. The query below is for exploratory purposes only and will pull conversions from users with only viewable and impressions. See the links in Section 2.5.
   


```
-- Instructional Query: Custom Attribution for pixel conversions - Exploratory Query `conversions_with_relevance`  --
WITH traffic AS (
  SELECT
    DISTINCT campaign,
    campaign_id,
    user_id
  FROM
    TABLE(
      /* OPTIONAL UPDATE: This exploratory query selects all campaigns that ran 60 days before the query time window. 
       You can adjust this based on your use case by changing 'P60D' to 'P30d', for example. 
       Unlike ASIN conversions, pixel conversions are not subject to the 28-day lookback window.*/
      EXTEND_TIME_WINDOW('dsp_impressions', 'P60D', 'P0D')
    )
  WHERE
    user_id IS NOT NULL
)
SELECT
  tracked_item AS pixel_ID,
  event_category,
  c.campaign,
  c.campaign_id,
  SUM(conversions) AS conversions,
  COUNT(DISTINCT c.user_id) AS users_that_converted
FROM
  conversions_with_relevance c
  JOIN traffic t ON t.campaign = c.campaign
  AND t.user_id = c.user_id
WHERE
  event_category = 'pixel'
  AND c.user_id IS NOT NULL
GROUP BY
  1,
  2,
  3,
  4
```

### 3.5 The Attributed vs Custom Attribution Datasets for Pixel Conversions
#### How are the attributed data sources similar to the custom attribution data sources for pixel conversions?
**Pixel IDs**: Both datasets have conversions from the same Pixel IDs. The conversions included in the datasets are from the brand of pixel IDs that were tracked to a campaign. 

#### How are the attributed data sources different from the custom attribution data sources for pixel conversions?
**Amazon Ads traffic type**: The conversions in the attributed data sources have traffic requirements. Amazon DSP campaigns require a viewable impression as one requirement for a conversion to be considered as attributed to the campaign. The custom attribution data sources provide all pixel conversions from users who were both ad-attributed and not ad-attributed. 

**Viewability**: The custom attribution conversions do not consider viewability, but the attributed conversions do. If a user was delivered an impression that was not viewable and then converted within a 14-day time period, this conversion would not meet the qualifications to be attributed to a campaign. However, this conversion would appear in the custom attribution dataset for pixel conversions. 

**Lookback window**: Conversions must take place within 14 days of an eligible traffic event to meet attributed conversions eligibility requirements. The lookback window for pixel conversions in the custom attribution data is only constrained by the time when the pixel ID was tracked to the campaign or the time window of your query. For example, if you started tracking a pixel ID to a campaign on February 1, you will have pixel conversions from this date going forward. 

**De-duplication across advertising accounts or campaigns**: The attributed conversions are de-duped against any other campaigns tracking the same pixel. For example, you track the same pixel across multiple advertising accounts or campaigns. A customer clicks campaign_c on Monday and campaign_d on Tuesday and converts on Wednesday, and both are tracking the same pixel. In this scenario, only the campaign_d will receive credit for the conversion in the attributed dataset, since the ad had the last click. The custom attribution dataset does not de-duplicate the conversions, so they are relevant to both campaigns. 

**Alignment with DSP UI**: The custom attribution dataset for pixel conversions is unique to AMC and a comparable dataset cannot be found in any other Amazon Ads reporting. As a result, there is no data source outside of AMC that it is expected to match. The attributed datasets align to reporting from the DSP UI for pixel conversions. For details, refer to [Attributed events and conversion tables](https://advertising.amazon.com/help/GHNTMYL5DJBJEQC9) in Support Center.


### 3.6 Comparison of the Four AMC Data Sources with Pixel Conversion Events

Attributed data sources are `amazon_attributed_events_by_conversion_time` and `amazon_attributed_events_by_traffic_time`.
Custom attribution data sources are `conversions_with_relevance` and `conversions`. 

#### 3.6.1 Comparison of Pixel Conversion Use Cases and Pixel Conversions Included


|\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_  |Attributed_Data_Source_Name: `amazon_attributed_events_by_conversion_time`|Attributed_Data_Source_Name: `amazon_attributed_events_by_traffic_time`|Custom_Attribution_Data_Source_Name: `conversions_with_relevance`|Custom_Attribution_Data_Source_Name: `conversions`|
|:---|:---|:---|:---|:---|
|Example use case for pixel conversion advertisers|How many users converted on my website, as measured by pixel conversions tracked to my Amazon DSP campaigns?|How many users converted on my website from ads I served during Prime Day, as measured by pixel conversions tracked to my Amazon DSP campaigns?|Custom attribution insights where the campaigns are required for insights. Example insights include multi-touch attribution across multiple campaigns / campaign types.|I track pixels to multiple campaigns and want to understand this pixel’s performance de-duped across campaigns from users served and not served ad impressions.|
|What pixel conversions are included?|Ad-attributed conversions based on the 14-day last touch model from pixels that were tracked to a campaign. See section 2.2.3 for more info. |Same as `amazon_attributed_events_by_conversion_time`.|Pixel conversions that are relevant to any campaign in an AMC instance are included. A pixel conversion is relevant if it was tracked to the campaign.|Same as `conversions_with_relevance`.|
|Pixel Conversion (De)Duplication|Conversions are always de-duped across campaigns. If a user was exposed to two campaigns, only one campaign gets credit according to the 14-day last touch model. See section 2.2.3 for more info. |Same as `amazon_attributed_events_by_conversion_time`.|Includes the same conversions from` conversions`, but also includes the relevant campaigns and conversions that could be duplicated.|Conversions are de-duped.|
|Includes pixel conversion from customers not served ads?|No|No|Yes|Yes|



#### 3.6.2 Comparison of Time and Other Attributes for campaigns that track pixels


|\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_  |Attributed_Data_Source_Name: `amazon_attributed_events_by_conversion_time`|Attributed_Data_Source_Name: `amazon_attributed_events_by_traffic_time`|Custom_Attribution_Data_Source_Name: `conversions_with_relevance`|Custom_Attribution_Data_Source_Name: `conversions`|
|:---|:---|:---|:---|:---|
|Maximum Lookback for pixel conversions (conversion date - impression date)|14 days|14 days|The lookback window for pixel conversions in the custom attribution data is only constrained by the time when the pixel ID was tracked to the campaign or your query settings. For example, if you started tracking a valid pixel ID to a campaign on February 1, you will have pixel conversions from this date until the pixel is no longer valid (if it was removed from your website, for example).|Same as `conversions_with_relevance`.|
|Can the lookback window for pixel conversions be adjusted to less than the maximum?|Yes|Yes|Yes|Yes|
|Time window for pixel conversions corresponds to|Conversion time|Impression time for DSP|Conversion time|Conversion time|
|Corresponds to other non-AMC Amazon sources for pixel conversions|ADSP UI/API|n/a|n/a|n/a|
|Maximum number of campaigns per pixel conversion event|1|1|no set limit|no set limit|
|Campaign information included for the pixel conversion?|Yes|Yes|Yes|No|
|Contains campaign setup dimensions under the campaign (line item, ad group, ad).|Yes|Yes|No|No|
|Viewability|Conversions are only included if a user viewed an ad.|Conversions are only included if a user viewed an ad.|Relevant conversions from both viewable and non-viewable impressions are included with the option to exclude non-viewable impressions using the dsp_views table.|Relevant conversions from both viewable and non-viewable impressions are included. Non-viewable conversions cannot be separated from viewable conversions.|
|Includes the request_tag?|Yes, the request_tag in the `attributed_` datasets for DSP campaigns acts as an impression ID to map the attributed conversion back to the impression that is attributed to the conversion event. The reason why there is a request_tag in the `attributed_` datasets is because there is a 1:1 relationship between the attributed conversion and the associated traffic event.|Same as `amazon_attributed_events_by_conversion_time`.|No, the custom attribution datasets do not include the request_tag. There could be multiple traffic events that are led to the conversion being relevant to the campaign. The Instructional Queries help you to understand the relationship between the conversion events and the traffic events based on the user's journey across campaigns.|Same as `conversions_with_relevance`.|


## 4. Custom Attribution Datasets - Column Definitions for ASIN and Pixel Conversion Advertisers

### 4.1 Columns that are in both `conversions` and `conversions_with_relevance`

As reviewed in the Section 3, all conversion events must be ‘relevant’ to be included in the instance. The table below describes the columns. The information applies to both ASIN and pixel conversion advertisers. 


| Column Name              | Data Type       | Column Type | Sensitivity | Column Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| :------------------------ | :--------------- | :----------- | :----------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| event\_type              | STRING          | DIMENSION   | LOW         | Always 'CONVERSION' in the data sources `conversions` and `conversions_with_relevance`.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| event\_type\_class       | STRING          | DIMENSION   | LOW         | For ASIN conversions, the event\_type\_class is the High level classification of the event type, e.g. consideration,  conversion, etc. This field will be blank for pixel conversions (when event_category = 'pixel') and search conversions (when event_subtype = 'searchConversion').                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| event\_category          | STRING          | DIMENSION   | LOW         | For ASIN conversions, the event\_category is the high level category of the conversion event, either 'purchase' or 'website'. Examples of ASIN conversions when event\_category = 'website' include: detail page views, add to wishlist, or the first Subscribe and Save order. For search conversions (when event_subtype = 'searchConversion'), the event\_category is always 'website'. For pixel conversions, this field is always 'pixel'.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| event\_subtype           | STRING          | DIMENSION   | LOW         | Subtype of the conversion event. For ASIN conversions, the examples of event\_subtype include: 'alexaSkillEnable', 'babyRegistry', 'customerReview', 'detailPageView', 'order', 'shoppingCart', 'snsSubscription', 'weddingRegistry', 'wishList'. For search conversions, event\_subtype is always 'searchConversion'. For pixel conversions, the numeric ID of the event\_subtype is provided. For additional information on the interpretation of this numeric id for pixel conversions, refer to event\_type\_description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| event\_type\_description | STRING          | DIMENSION   | LOW         | Human-readable description of the conversion event. For ASIN conversions, examples include: 'add to baby registry', 'Add to Shopping Cart', 'add to wedding registry', 'add to wishlist', 'Customer Reviews Page', 'New SnS Subscription', 'Product detail page viewed', 'Product purchased'. This field will be blank for search conversions (when event_subtype = 'searchConversion'). For pixel conversions (when event_category = 'pixel'), the event\_type\_description will include additional information about the pixel based on information provided as part of the campaign setup, such as 'Homepage visit', 'Add to Shopping Cart' or 'Brand store engagement 2'. Before using these fields to evaluate pixel conversions, we recommend that you consult with the team that set up the advertiser's campaign to determine how the event\_type\_description values for pixel conversions should be interpreted.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| tracked\_item            | STRING          | DIMENSION   | LOW         | Each campaign can track zero or more items. Depending on the type of campaign, these items might be ASINs, pixel IDs, DSP ad-attributed branded search keywords or apps. The tracked items for a campaign are the basis for which we determine which conversion events are sent to each AMC instance. In the custom attribution dataset, the tracked\_item is the identifier of an item of interest for the campaign, such as the ASIN tracked to the campaign, the brand halo ASIN, or the ad-attributed branded keyword. Attribution algorithms match traffic and conversion event by the user identity and tracked item. Matching rules for tracked items may include expansion algorithms such as brand halo when the `conversions_with_relevance dataset` is used. If tracked\_asin has a value populated, the same value for tracked\_item will match tracked\_asin. See also tracked\_asin. |
| tracked\_asin            | STRING          | DIMENSION   | LOW         | The dimension tracked\_asin is the tracked Amazon Standard Identification Number, which is either the promoted or brand halo ASIN. When the tracked_item results in a purchase conversion, the tracked_asin will be populated in this column, in addition to being tracked in the tracked\_item column with the same ASIN value. The tracked_asin is populated only if the event_category = 'purchase'. For the first SnS order and non-purchase conversions, such as detail page views, the ASIN is populated under tracked\_item. See also 'tracked\_item'.                                                                                                                                                                                                                                     |
| event\_date\_utc         | DATE            | DIMENSION   | LOW         | Date of the conversion event in UTC.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| event\_day\_utc          | INTEGER         | DIMENSION   | LOW         | Day of the month of the conversion event in UTC.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| event\_dt\_hour\_utc     | TIMESTAMP       | DIMENSION   | LOW         | Timestamp of the conversion event in UTC truncated to the hour.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| event\_dt\_utc           | TIMESTAMP       | DIMENSION   | MEDIUM      | Timestamp of the conversion event in UTC.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| event\_hour\_utc         | INTEGER         | DIMENSION   | LOW         | Hour of the day of the conversion event in UTC.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| user\_id                 | STRING          | DIMENSION   | VERY\_HIGH  | User ID.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| conversion\_id           | STRING          | DIMENSION   | VERY\_HIGH  | Unique identifier of the conversion event. In the dataset `conversions`,  each row has a unique conversion\_id. In the dataset  `conversions_with_relevance`, the same conversion event will appear multiple  times if a conversion is determined to be relevant to multiple campaigns,  engagement scopes, or brand halo types.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| conversions              | LONG            | METRIC      | NONE        | Conversion count.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| purchase\_currency       | STRING          | DIMENSION   | LOW         | ISO currency code of the purchase order.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| purchase\_unit\_price    | DecimalDataType | METRIC      | NONE        | Unit price of the product sold.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| total\_product\_sales    | DecimalDataType | METRIC      | NONE        | Total sales (in local currency) of promoted ASINs and ASINs from the same  brands as promoted ASINs purchased by customers on Amazon.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| total\_units\_sold       | LONG            | METRIC      | NONE        | Total quantity of promoted products and products from the same brand as  promoted products purchased by customers on Amazon. A campaign can have  multiple units sold in a single purchase event.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| new\_to\_brand           | BOOLEAN         | DIMENSION   | LOW         | True if the user was new to the brand.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| event\_source            | STRING          | DIMENSION   | VERY\_HIGH  | System that generated the conversion event.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |


### 4.2 Exploratory Queries: Conversion Events

#### 4.2.1 Conversion Events Query for ASIN and Pixel Conversion Advertisers

The query below has been provided to help you to explore the types of conversion events in your AMC instance, which can be used to customize Instructional Queries. Example results are provided below for both ASIN and pixel conversion advertisers.



```
-- Instructional Query: Custom Attribution - Conversion Events Exploratory Query --
SELECT
  event_type,
  event_type_class,
  event_category,
  event_subtype,
  event_type_description,
  SUM(conversions) AS conversions
FROM
  conversions
GROUP BY
  1,
  2,
  3,
  4,
  5
```

##### Conversion Events - Example Results for an ASIN conversion advertiser

The list of conversion events below will vary based on your advertiser. Run the query above in your instance to get a list of all conversion events in your instance. As reviewed in the Sections 3.1 - 3.3, all conversion events must be ‘relevant’ to be included in the instance. 


| event_type | event_type_class | event_category | event_subtype    | event_type_description     | conversions | definition of query results                                                                                                                                                                                                                                                                                                                                         |
| :---------- | :---------------- | :-------------- | :---------------- | :-------------------------- | :----------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CONVERSION | conversion       | purchase       | order            | Product purchased          | 1108246     | The sum of purchase conversions from promoted and brand halo ASINs, including the first subscribe and save (SnS) purchase, but not repeat purchase conversions.                                                                                                                                                                                                     |
| CONVERSION | consideration    | website        | customerReview   | Customer Reviews Page      | 25017       | The sum of product review pages viewed from promoted and brand halo ASINs.                                                                                                                                                                                                                                                                                          |
| CONVERSION | consideration    | website        | detailPageView   | Product detail page viewed | 4832923     | The sum of product detail pages viewed from promoted and brand halo ASINs.                                                                                                                                                                                                                                                                                          |
| CONVERSION | conversion       | website        | babyRegistry     | add to baby registry       | 447         | The number of times promoted or brand halo ASINs were added to a baby registry.                                                                                                                                                                                                                                                                                     |
| CONVERSION | conversion       | website        | shoppingCart     | Add to Shopping Cart       | 1629821     | The number of times promoted or brand halo ASINs were added to a shopping cart.                                                                                                                                                                                                                                                                                     |
| CONVERSION | conversion       | website        | weddingRegistry  | add to wedding registry    | 288         | The number of times promoted or brand halo ASINs were added to a wedding registry.                                                                                                                                                                                                                                                                                  |
| CONVERSION | conversion       | website        | wishList         | add to wishlist            | 2331        | The number of times promoted or brand halo ASINs were added to an Amazon wish list.                                                                                                                                                                                                                                                                                 |
| CONVERSION |                  | website        | searchConversion |                            | 775983      | The number of branded search conversions from Amazon DSP campaigns.                                                                                                                                                                                                                                                                                                 |
| CONVERSION | conversion       | website        | snsSubscription  | New SnS Subscription       | 75702       | The number of new SnS conversions. Note that only the conversion count and the ASIN and not the product sales or units sold are reported for the first SnS conversion events. SnS conversion events should not be added to the conversion events where event\_subtype = 'order' because doing so would result to counting the first SnS conversion event two times. |


##### Conversion Events - Example Results for a Pixel Conversion Advertiser

The list of conversion events below will vary based on your advertiser. Run the query above in your instance to get a list of all pixel conversion events in your instance. For pixel conversions, the events included in the custom attribution data are from users who were delivered an impression and also from users who were not delivered an impression. Refer to Sections 3.4 - 3.6 for additional information. 


|event_type|event_type_class|event_category|event_subtype|event_type_description|conversions|
|:---|:---|:---|:---|:---|:---|
|CONVERSION|conversion|pixel|53|Add to Shopping Cart|346|
|CONVERSION|conversion|pixel|44|Brand store engagement 2|31244|
|CONVERSION|conversion|pixel|45|Brand store engagement 3|5203|
|CONVERSION|conversion|pixel|8|Game load|385|
|CONVERSION|conversion|pixel|55|Homepage visit|116501|
|CONVERSION|conversion|pixel|4|Marketing landing page|221367|
|CONVERSION|conversion|pixel|81|Referral|1520|
|CONVERSION|conversion|pixel|32|Registration Confirmation Page|1720|
|CONVERSION|conversion|pixel|31|Registration Form|112016|
|CONVERSION|conversion|pixel|36|Store Locator Page|81|
|CONVERSION|conversion|pixel|30|Thank You Page|12547|
|CONVERSION|conversion|pixel|13|Widget interaction|59249|
|CONVERSION|conversion|pixel|9|Widget load|6832|


#### 4.2.2 SnS Conversion Events Query for ASIN Conversion Advertisers
Use this query to get a sum of all first SnS conversion events by ASIN from relevant conversion events. 



```
-- Instructional Query: Custom Attribution - SnS Conversion Events Exploratory Query --
SELECT
  tracked_item AS ASIN,
  SUM(conversions) AS first_SnS_conversions
FROM
  conversions
WHERE
  event_subtype = 'snsSubscription'
GROUP BY
  1
```

### 4.3 Columns that are only in `conversions_with_relevance`

The columns below are excluded from the conversions dataset and are only included in `conversions_with_relevance`. As reviewed in Section 3, all conversion events must be 'relevant' to be included in the instance. The dimensions below are dependent upon the campaign or advertiser, which is why they are only included in `conversions_with_relevance`. The most important columns that are exclusive to `conversions_with_relevance` are `engagement_scope` and `halo_code`, since they explain how the ASIN or other tracked item was relevant to the campaign. While the columns `engagement_scope` and `halo_code` only apply to ASIN conversion advertisers, the other columns apply to both ASIN and pixel conversion advertisers. 




| Column Name                | Data Type | Column Type | Sensitivity | Column Description                                                                                                                                                                                                                                                                                                     |
| :-------------------------- | :--------- | :----------- | :----------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| engagement\_scope          | STRING    | DIMENSION   | LOW         | The engagement scope between the campaign setup and the conversion. Possible values  for this column are PROMOTED, BRAND\_HALO, and null. See also the definition for halo\_code. The engagement\_scope will always be 'PROMOTED' for pixel conversions (when event\_category = 'pixel').                                                                                                   |
| halo\_code                 | STRING    | DIMENSION   | LOW         | The halo code between the campaign and conversion. Possible values for  this column are TRACKED\_ASIN, VARIATIONAL\_ASIN, BRAND\_KEYWORD,  CATEGORY\_KEYWORD, BRAND\_MARKETPLACE, BRAND\_GL\_PRODUCT, BRAND\_CATEGORY,  BRAND\_SUBCATEGORY, and null.  See also the definition for engagement\_scope. The halo\_code will be NULL for pixel conversions (when event\_category = 'pixel'). |
| advertiser                 | STRING    | DIMENSION   | LOW         | Advertiser name.                                                                                                                                                                                                                                                                                                       |
| advertiser\_id             | LONG      | DIMENSION   | LOW         | Advertiser ID.                                                                                                                                                                                                                                                                                                         |
| advertiser\_timezone       | STRING    | DIMENSION   | LOW         | Time zone of the advertiser.                                                                                                                                                                                                                                                                                           |
| campaign                   | STRING    | DIMENSION   | LOW         | Campaign name.                                                                                                                                                                                                                                                                                                         |
| campaign\_budget\_amount   | LONG      | DIMENSION   | LOW         | Campaign budget amount (millicents).                                                                                                                                                                                                                                                                                   |
| campaign\_end\_date        | DATE      | DIMENSION   | LOW         | Campaign end date in the advertiser time zone.                                                                                                                                                                                                                                                                         |
| campaign\_end\_date\_utc   | DATE      | DIMENSION   | LOW         | Campaign end date in UTC                                                                                                                                                                                                                                                                                               |
| campaign\_end\_dt          | TIMESTAMP | DIMENSION   | LOW         | Campaign end timestamp in the advertiser time zone.                                                                                                                                                                                                                                                                    |
| campaign\_end\_dt\_utc     | TIMESTAMP | DIMENSION   | LOW         | Campaign end timestamp in UTC                                                                                                                                                                                                                                                                                          |
| campaign\_id               | LONG      | DIMENSION   | LOW         | Campaign ID.                                                                                                                                                                                                                                                                                                           |
| campaign\_id\_internal     | LONG      | DIMENSION   | INTERNAL    | The globally unique internal campaign ID.                                                                                                                                                                                                                                                                              |
| campaign\_sales\_type      | STRING    | DIMENSION   | LOW         | Campaign sales type                                                                                                                                                                                                                                                                                                    |
| campaign\_source           | STRING    | DIMENSION   | LOW         | Campaign source.                                                                                                                                                                                                                                                                                                       |
| campaign\_start\_date      | DATE      | DIMENSION   | LOW         | Campaign start date in the advertiser time zone.                                                                                                                                                                                                                                                                       |
| campaign\_start\_date\_utc | DATE      | DIMENSION   | LOW         | Campaign start date in UTC.                                                                                                                                                                                                                                                                                            |
| campaign\_start\_dt        | TIMESTAMP | DIMENSION   | LOW         | Campaign start timestamp in the advertiser time zone.                                                                                                                                                                                                                                                                  |
| campaign\_start\_dt\_utc   | TIMESTAMP | DIMENSION   | LOW         | Campaign start timestamp in UTC.                                                                                                                                                                                                                                                                                       |
| event\_date                | DATE      | DIMENSION   | LOW         | Date of the conversion event in the advertiser time zone.                                                                                                                                                                                                                                                              |
| event\_day                 | INTEGER   | DIMENSION   | LOW         | Day of the month of the conversion event in the advertiser time zone.                                                                                                                                                                                                                                                  |
| event\_dt                  | TIMESTAMP | DIMENSION   | MEDIUM      | Timestamp of the conversion event in the advertiser time zone.                                                                                                                                                                                                                                                         |
| event\_dt\_hour            | TIMESTAMP | DIMENSION   | LOW         | Timestamp of the conversion event in the advertiser time zone truncated  to the hour.                                                                                                                                                                                                                                  |
| event\_hour                | INTEGER   | DIMENSION   | LOW         | Hour of the day of the conversion event in the advertiser time zone.                                                                                                                                                                                                                                                   |


### 4.4 Engagement Scope x Halo Code Concept and Definitions for ASIN and Search Conversions

The PROMOTED engagement scope indicates that the campaign was directly promoting the item in the conversion event. The BRAND_HALO engagement scope indicates that the conversion event was for an ASIN that was from the same brand as the promoted ASIN, but was not a promoted ASIN. There are multiple halo codes that will be used depending on how similar the product types of the promoted ASIN and conversion ASIN were. 

#### 4.4.1 Halo Codes for Promoted Item Conversions









| 'halo\_code' value                   | Definition when engagement scope = PROMOTED                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| :------------------------------------ | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| TRACKED\_ASIN                        | The campaign of the traffic event was directly tracking the ASIN in the conversion event. For example, if a campaign was is up to track ASIN B01234ABCD, and a customer sees an ad for that campaign and then places an order for ASIN B01234ABCD, this halo code will be used.                                                                                                                                                                                                                                                                                                                          |
| VARIATIONAL\_ASIN                    | The campaign of the traffic event was tracking a variation (different size, color, etc.) of the ASIN in the conversion event. For example, if a campaign is set up to track ASIN B01234ABCD running shoes, and a customer sees an ad from that campaign and then places an order for ASIN B01234ABCE, which represents the blue-colored version of those shoes, this halo code or the null halo code will be used.                                                                                                                                                                                       |
| BRAND\_KEYWORD and CATEGORY\_KEYWORD | This halo code is used for ad-attributed search conversions where a campaign is set up to track a keyword associated with a brand/category and a customer sees an ad from that campaign and then performs a search. For these halo codes, the act of performing the search is the conversion event (this will be reflected in the subtype of the conversion event as well). For example, if a campaign is set up to track the “running shoes” keyword, and a customer sees an ad for that campaign and then searches for “running shoes“, a search conversion event with this halo code will be created. |
| NULL                                 | For pixel-based or other non-ASIN-based conversions. For example, if a campaign is set up with pixel 12345678 and a customer sees an add for the campaign and performs an action which fires that pixel, this halo code will be used. For sellers, if any purchase was made from the same seller. For example, if a seller sets up a campaign and a customer sees an ad from that campaign before purchasing an item from that seller, a null halo code will be used.                                                                                                                                    |


####  4.4.2 ASIN Categorization on Amazon
ASIN conversions take place on marketplaces, such as Amazon.com or Amazon.co.uk. Each marketplace has a set of GLs, which group together similar categories and subcategories. Examples of GLs are ‘Athletic’ or ‘Grocery’. 

Hierarchy structure:
MARKETPLACE  >> GL_PRODUCT >> CATEGORY  >> SUBCATEGORY >> Brand >> ASIN

Example:
Amazon.co.uk >> Shoes >> Athletic >> Men’s Running Shoes >> Accent Athletics >>  ASIN12345 

####  4.4.3 Halo Codes for Brand Halo ASIN Conversions



| 'halo\_code' value | Definition when engagement scope = BRAND\_HALO                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| :------------------ | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| BRAND\_MARKETPLACE | The campaign of the traffic event was tracking an ASIN with the same marketplace and brand as the ASIN in the conversion event. For example, a campaign is set up to track ASIN12345 (a pair of Accent Athletics running shoes) on Amazon.co.uk within the Shoes GL. A customer sees an ad from that campaign on Amazon.co.uk. The customer places an order for ASIN ASIN12300, an Accent Athletics ASIN within the Amazon.co.uk marketplace, but outside of the Shoes GL. The BRAND\_MARKETPLACE halo\_code will be used.                                                                                                                                |
| BRAND\_GL\_PRODUCT | The campaign of the traffic event was tracking an ASIN with the same marketplace, GL, and brand as the ASIN in the conversion event. For example, a campaign is set up to track ASIN12345 (a pair of Accent Athletics running shoes) on Amazon.co.uk within the Shoes GL and the Athletic category. A customer sees an ad from that campaign on Amazon.co.uk. The customer places an order for ASIN ASIN12300, an Accent Athletics ASIN within the Amazon.co.uk marketplace and Shoes GL, but in the Fashion category as opposed to the Athletic category. The BRAND\_GL\_PRODUCT halo\_code will be used.                                                |
| BRAND\_CATEGORY    | The campaign of the traffic event was tracking an ASIN with the same marketplace, GL, category and brand as the ASIN in the conversion event. For example, a campaign is set up to track ASIN12345 (a pair of Accent Athletics running shoes) on Amazon.co.uk within the Shoes GL, the Athletic category, and the Men's Running Shoes subcategory. A customer sees an ad from that campaign on Amazon.co.uk. The customer places an order for ASIN ASIN12800, in the Women's Running Shoes subcategory, but in the same marketplace, GL, and category as the promoted ASIN. The BRAND\_CATEGORY halo\_code will be used.                                  |
| BRAND\_SUBCATEGORY | The campaign of the traffic event was tracking an ASIN with the same marketplace, GL, category, subcategory and brand as the ASIN in the conversion event. For example, a campaign is set up to track ASIN12345 (a pair of Accent Athletics running shoes) on Amazon.co.uk within the Shoes GL, the Athletic category, and the Men's Running Shoes subcategory. A customer sees an ad from that campaign on Amazon.co.uk. The customer places an order for ASIN ASIN12355, which is also in the Men's Running Shoes subcategory and from the Accent Athletics brand, but was not tracked to the campaign. The BRAND\_SUBCATEGORY halo\_code will be used. |


### 4.5 Exploratory Query: Halo Code and Engagement Scope by ASIN and Campaign

Use this query to explore campaigns’ relationships to ASINs and items tracked. 



```
-- Instructional Query: Custom Attribution - Halo Code and Engagement Scope Exploratory Query --
WITH ad_products AS (
  SELECT
    DISTINCT campaign,
    ad_product_type -- gets all sponsored ads campaigns from the 28-day period before the start of the query time window you select.
  FROM
    TABLE(
      EXTEND_TIME_WINDOW('sponsored_ads_traffic', 'P28D', 'P0D')
    )
  UNION ALL
  SELECT
    DISTINCT campaign,
    'dsp' AS ad_product_type -- gets all dsp campaigns from the 28-day period before the start of the query time window you select.
  FROM
    TABLE(
      EXTEND_TIME_WINDOW('dsp_impressions', 'P28D', 'P0D')
    )
),
purchase_conversions AS(
  SELECT
    tracked_item AS ASIN,
    engagement_scope,
    c.campaign AS campaign_name,
    ad_product_type,
    SUM(conversions) AS purchases,
    COUNT(DISTINCT user_id) AS users_that_purchased
  FROM
    conversions_with_relevance c
    JOIN ad_products a ON c.campaign = a.campaign -- optional clause to only include tracked items that are ASINs and exclude other tracked_item values, such as DSP branded keyword searches and pixel conversion UUIDs.
  WHERE
    halo_code IN (
      'TRACKED_ASIN',
      'VARIATIONAL_ASIN',
      'BRAND_MARKETPLACE',
      'BRAND_GL_PRODUCT',
      'BRAND_CATEGORY',
      'BRAND_SUBCATEGORY'
    ) -- optional clause to drill down to purchase conversions only.
    AND event_category = 'purchase'
    AND user_id IS NOT NULL
  GROUP BY
    1,
    2,
    3,
    4
)
SELECT
  ASIN,
  engagement_scope,
  campaign_name,
  ad_product_type,
  purchases,
  users_that_purchased
FROM
  purchase_conversions
```

#### Halo Code and Engagement Scope - Example Results for ASIN and Search Conversions


| tracked_item                    | halo_code           | engagement_scope | campaign_name                                     | conversions | Notes                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| :------------------------------- | :------------------- | :---------------- | :-------------------------------------------- | ----------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ASIN1234511                     | TRACKED\_ ASIN      | PROMOTED         | Accent Athletics In-Market Campaign - Shirts | 54096       | Engagement scope is dependent upon the campaign. In this example ASIN1234511 has two engagement scopes. First, ASIN1234511 was tracked first campaign, so the engagement scope is 'PROMOTED'. ASIN1234511 was not tracked to the second campaign, but a different ASIN from  the Accent Athletics brand was tracked, which is why the engagement scope is 'BRAND\_HALO'. Note that the conversion values for both are identical and they should not be added across campaigns.  Refer to Section 3 to understand why the conversions are duplicated across campaigns in the `conversions_with_relevance` data source.|
| ASIN1234511                     | BRAND\_GL\_ PRODUCT | BRAND\_HALO      | Accent Athletics Q1 US Cross Screen Shoes    | 54096       | See row above – same note applies.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| ASIN1234513                     | BRAND\_ MARKETPLACE | BRAND\_HALO      | Accent Athletics In-Market Campaign - Shirts | 83151       | There are four types of brand halo conversions. Refer to the Section 4.4.3 'Halo Codes for Brand Halo ASIN Conversions'                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ASIN1234512                     | BRAND\_GL\_ PRODUCT | BRAND\_HALO      | Accent Athletics Q1 US Cross Screen Shoes    | 97892       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ASIN1234515                     | BRAND\_ CATEGORY    | BRAND\_HALO      | Accent Athletics In-Market Campaign - Shirts | 33260       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ASIN1234516                     | BRAND\_ SUBCATEGORY | BRAND\_HALO      | Accent Athletics In-Market Campaign - Shirts | 49890       |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| keyword#accent athletics        | BRAND\_ KEYWORD     | PROMOTED         | Accent Athletics In-Market Campaign - Shirts | 22899       | This DSP campaign tracked branded search conversions for "accent athletics" and "accent athletics shirts".                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| keyword#accent athletics shirts | BRAND\_ KEYWORD     | PROMOTED         | Accent Athletics In-Market Campaign - Shirts | 5539        |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |


## 5. Amazon Attribution vs AMC Custom Attribution

[Amazon Attribution](https://advertising.amazon.com/solutions/products/amazon-attribution?ref_=a20m_us_hnav_p_attr) is our off-the-shelf measurement tool for brands to examine how their paid and organic non-Amazon marketing strategies across channels including search, social, video, display, and email impact shopping activities on Amazon. Advertisers can create and apply Amazon Attribution tags to the non-Amazon campaigns they want to measure, and access on-demand reporting containing conversion metrics at channel, publisher, campaign, creative, keyword, and product level. Amazon Attribution utilizes a 14-day, last-touch attribution model. The solution can help advertisers uncover insights on what marketing tactics resonate with their audience outside of Amazon, and identify opportunities to optimize non-Amazon media efforts and investments. Advertisers can access Amazon Attribution through the web-based self-service console, Amazon Ad Server, or tool providers integrated with the Amazon Advertising API.
 
Given AMC custom attribution focuses on assessing Amazon media’s performance, while Amazon Attribution aims to measure non-Amazon media’s effectiveness in driving on-Amazon engagement, we view these solutions as complementary and recommend using the two together for a more comprehensive view of marketing attribution. 




|                                      | AMC Custom Attribution                                | Amazon Attribution                                                                                      |
| :------------------------------------ | :----------------------------------------------------- | :------------------------------------------------------------------------------------------------------- |
| Applicable advertisers               | ASIN and pixel conversion advertisers                 | ASIN conversion advertisers                                                                             |
| Credit Amazon media touchpoints?     | yes                                                   | no                                                                                                      |
| Credit non-Amazon media touchpoints? | yes                                                 | yes                                                                                                     |
| Conversion Type                      | on-Amazon and off-Amazon conversions\*\*                             | on-Amazon conversions                                                                                   |
| Attribution model                    | custom single-touch or multi-touch  model             | last-click model                                                                                        |
| Lookback Window                      | Up to 28 days                                         | 14 days                                                                                                 |
| Reporting format                     | SQL query-based reporting from the AMC UI and/or API. | Downloadable reports from the Amazon Attribution UI. API also available.                                |
| Metrics granularity                  | event-level reporting                                 | aggregate reporting (aggregated at channel, publisher, campaign, creative, keyword, and product  level) |



** Advertisers can now upload aggregated sales data and hashed CRM records onto AMC, allowing AMC custom attribution to extend to off-Amazon conversions.




## Appendix 1: 
### Amazon Ads Standard Attribution Model for Brand-Based Ad Products (ASIN conversions)
All Amazon Advertising products for brands (DSP, Sponsored Display, Sponsored Brands, and Sponsored Products for Vendors) compete with each other for attribution. Ads are eligible for attribution if the shopper clicked or viewed them within 14 days prior to conversion. If the shopper interacted with multiple ads, attribution considers each campaign's relevance to the sold product, then uses a last-touch model that prioritizes the last click over the last view for promoted and highly relevant brand halo ASINs (1-4 from the hierarchy below). Clicks are prioritized over views for promoted and highly relevant products because they are a stronger engagement signal. Views from promoted and highly relevant brand halo products are prioritized over clicks from brand halo products that are not considered to be highly relevant (5-6 from the hierarchy below). 

Amazon uses this hierarchy (simplified) to determine which ad to credit with attribution:

1. Promoted ASIN ad clicks (For example, a shopper clicked an ad, then purchased an ASIN that the ad's campaign promoted). 
1. Highly relevant brand halo++ ad clicks (For example, a shopper clicked an ad, then purchased an ASIN that the ad's campaign did not promote, but was a highly relevant brand halo ASIN). 
1. Promoted ASIN ad views (For example, a shopper viewed an ad, then purchased an ASIN that the ad's campaign promoted). 
1. Highly relevant brand halo** ad views (For example, a shopper viewed an ad, then purchased an ASIN that the ad's campaign did not promote, but was a highly relevant brand halo ASIN).
1. Other brand halo^ ad clicks (For example, a shopper clicked an ad, then purchased an ASIN that the ad's campaign did not promote, but was a brand halo ASIN not considered to be highly relevant). 
1. Other brand halo^ ad views (For example, a shopper viewed an ad, then purchased an ASIN that the ad's campaign did not promote, but was a brand halo ASIN not considered to be highly relevant). 

++Highly relevant brand halo products are from the same brand and subcategory as a promoted ASIN. For example, the promoted product was a kitchen appliance from the Toaster Over subcategory from the brand Kitchen Smart. A highly relevant brand halo ASIN would be an ASIN from the Kitchen Smart brand within the subcategory Toaster Ovens that was not promoted to the campaign. 

**Ad views are considered for attribution only if the ad product and campaign type support view-through conversion.

^ Other brand halo products are from the same brand as the promoted ASIN, but are in a different subcategory within the promoted ASIN's marketplace (such as Amazon.com). For example, the promoted product was a kitchen appliance from the Toaster Over subcategory from the brand Kitchen Smart. An example of a product that is considered to be 'other brand halo' is an ASIN from the Kitchen Smart brand within the Blenders subcategory that was not promoted to the campaign. 

Amazon will attribute a conversion to no more than one ad interaction across all campaigns and all advertisers. A purchase will not, for example, be attributed to two campaigns in both a promoted and brand halo context, or as both a view-through and click-through conversion. It is attributed once to the most relevant ad interaction.

**Note**: Sponsored Products for sellers is a merchant-based ad program that does not normally compete for attribution with brands. However, if a conversion is attributed to the same advertiser ID or merchant ID in both programs, then we force a run-off using our prioritization hierarchy to ensure the same conversion is attributed only once per advertiser.

