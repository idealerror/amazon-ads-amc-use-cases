## Metadata

**id:** 216aa23f6982ccbec0dbcc7bc7c1cee30989e94f26f7f4143928bf941dced465
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Query supported ad types in AMC and explore campaign and line item types.
**tags:** []
**use_case_categories:** ['How to']
**advertiser_types:** ['On-Amazon conversions', 'Off-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## 1. Introduction
### 1.1 Background

AMC includes data from the following advertising sources: 
* **Sponsored ads** - Sponsored Products, Sponsored Display, Sponsored Brands, and Sponsored TV
* **Amazon DSP** - Examples include streaming TV (previously OTT), Fire TV, online video, audio, display, IMDb, and Twitch campaigns.

### 1.2 Purpose
This IQ will help you understand how to query the supported ad product types in AMC and explore the types of campaigns and line items that were running during a given period. 

AMC only contains information for the advertisers and entities associated with your instance. Check the **Instance Info** tab of your instance for a list of those advertisers and entities. 

Furthermore, AMC only contains information about actual delivery. Before impressions have been delivered, an activity will not be available in AMC.


### 1.3 Tables used
There are various AMC tables used in this IQ, including but not limited to:
- [sponsored_ads_traffic](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/sponsored_ads_traffic) is used to identify sponsored ads product types (Sponsored Products, Sponsored Display, Sponsored Brands, and Sponsored Television) using the `ad_product_type` column.
- [dsp_impressions](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/dsp_impressions) is used to identify all Amazon DSP ad products using one or more of the combination of columns: `line_item_type`, `site`, and `supply_source`.
- [amazon_attributed_events_by_conversion_time](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/amazon_attributed_events_by_conversion_time) is used to identify conversions for both Amazon DSP and sponsored ads. 

For more information on AMC tables, refer to [Amazon Marketing Cloud data sources](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/overview).



## 2. Which ad types and placements are supported in AMC? 
This table will be updated periodically to incorporate new ad types and placements. Not all ad products are available for all locales, and some ad products are available via Managed Service only.  You can learn more about the ad products and their availability via the **Learn More** links  below.

### Sponsored ads
|Ad Product|Supported in AMC?|AMC Example|Learn More|Note|
|---|---|---|---|---|
|Sponsored Products (SP)|YES|YES|[Sponsored Products](https://advertising.amazon.com/solutions/products/sponsored-products?ref_=a20m_us_hnav_p_sp)|Data will be present in AMC if the Entity ID for Sponsored ads added to AMC also includes Sponsored Products.|
|Sponsored Brands (SB)|YES|YES|[Sponsored Brands](https://advertising.amazon.com/solutions/products/sponsored-brands?ref_=a20m_us_hnav_p_sb)|Data will be present in AMC if the Entity ID for Sponsored ads added to AMC also includes Sponsored Brands.|
|Sponsored Brands Video (SBV)|YES|YES|[Sponsored Brands Video](https://advertising.amazon.com/resources/ad-specs/sponsored-brands-video?ref_=a20m_us_spcs_sbv)|Data will be present in AMC if the Entity ID for Sponsored Ads added to AMC also includes Sponsored Brands.|
|Sponsored Display|YES|YES|[Sponsored Display](https://advertising.amazon.com/solutions/products/sponsored-display?ref_=a20m_us_hnav_p_sd)|Data will be present in AMC if the Entity ID for Sponsored Ads added to AMC also includes Sponsored Display.|
|Sponsored TV|YES|YES|[Amazon Sponsored TV Ads](https://advertising.amazon.com/API/docs/en-us/guides/sponsored-tv/overview)|A self-service, programmatic ad solution that helps advertisers build their brand in front of new audiences across streaming TV services like Freevee|

### Amazon DSP
|Ad Product|Supported in AMC?|AMC Example|Learn More|Note|
|---|---|---|---|---|
|Online Video|YES|YES|[Amazon DSP Video Ads](https://advertising.amazon.com/resources/ad-specs/dsp/video)|Online video ads appear in both in-stream and out-stream formats, across browser and app, on devices such as desktop, mobile, and tablet.|
|Streaming TV (Previously OTT)|YES|YES|[Amazon DSP Video Ads](https://advertising.amazon.com/resources/ad-specs/dsp/video)|Streaming TV ads are ads that cannot be skipped and appear either before, during, or after streaming content.|
|Fire TV Ads|YES|YES|[Sponsored Display on Fire TV](https://advertising.amazon.com/en-us/solutions/products/fire-tv-ads?ref_=a20m_us_spcs_ftv_p_ftv) &#x7c;[Sponsored Screensaver](https://advertising.amazon.com/en-us/resources/ad-specs/fire-tv/screensaver?ref_=a20m_us_spcs_ftv_scsvr) &#x7c;[In Line Banner](https://advertising.amazon.com/en-us/resources/ad-specs/fire-tv/inline?ref_=a20m_us_spcs_ftv_ib) &#x7c; [Feature Rotator](https://advertising.amazon.com/resources/ad-specs/fire-tv/feature-rotator?ref_=a20m_us_spcs_ftv_spcs_ftv_fr)|Fire TV ads and Streaming TV ads are different placement types.|
|Fire Tablet|YES|YES|[Fire Tablet](https://advertising.amazon.com/resources/ad-specs/fire-tablet/?ref_=a20m_us_spcs_tblt)|Fire tablet supports a wide range of creative executions on the wake screen. A wake screen ad product consists a full screen advertisement that appears when the device turns on.|
|DSP Mobile Web|YES|YES|[DSP General Specs](https://advertising.amazon.com/resources/ad-specs/dsp/?ref_=a20m_us_spcs_dsp) &#x7c; [Mobile Shopping](https://advertising.amazon.com/resources/ad-specs/mobile-shopping/?ref_=a20m_us_spcs_mbs)||
|DSP Mobile App|YES|YES|[Amazon Mobile App](https://advertising.amazon.com/resources/ad-specs/mobile-shopping/)||
|Desktop Banners|YES|YES|[Amazon DSP Desktop](https://advertising.amazon.com/resources/ad-specs/dsp/desktop/)||
|Audio Ads|YES|YES|[Audio Ads](https://advertising.amazon.com/en-us/resources/ad-specs/audio-ads?ref_=a20m_us_spcs_spcs_aa)||
|IMDb website|YES|YES|[IMDb Ads](https://advertising.amazon.com/resources/ad-specs/imdb/?ref_=a20m_us_spcs_imdb)|IMDb.com ads are different from IMDb TV ads, which are Streaming TV ads.|
|IMDb mobile web|YES|YES|[IMDb Ads](https://advertising.amazon.com/resources/ad-specs/imdb/?ref_=a20m_us_spcs_imdb)||
|IMDb App|YES|YES|[IMDb Ads](https://advertising.amazon.com/resources/ad-specs/imdb/?ref_=a20m_us_spcs_imdb)||
|Freevee (Previously IMDb TV)|YES|YES|[Streaming TV &#x7c; Freevee](https://advertising.amazon.com/solutions/products/video-ads?ref_=a20m_us_blg_introamzfrv_p_va)|IMDb TV rebranded to Amazon Freevee in April, 2022|
|Twitch Direct (via Twitch DSP, fka TAS)|YES|YES|[Twitch Ads](https://advertising.amazon.com/resources/ad-specs/twitch/ )|All Twitch Ads via Twitch DSP. Twitch has their own DSP entities per region: US, EU, APAC. Each advertiser has its own CFID in these accounts. In order to collect Twitch data in AMC, make sure to get the correct CFID added to AMC.|
|Twitch run via the Amazon DSP|YES|YES|[Twitch Ads](https://advertising.amazon.com/solutions/products/video-ads?ref_=a20m_us_hnav_ent_p_va_t1)|All Twitch Ads, both display and video, are run completely through the Amazon DSP platform. In order to collect Twitch data in AMC, make sure to get the correct CFID added to AMC.|
|Thursday Night Football (TNF) /Live Sports|YES|YES|[Thursday Night Football](https://advertising.amazon.com/thursday-night-football)|TNF or Live Sports campaigns are served through Amazon DSP. As long as the advertiser CFID is added to the AMC instance, TNF campaign data should be tracked in AMC with 'AMAZON LIVE STREAM' as supply source.|
|Alexa Home Screen Display|YES|YES|[Alexa Home Screen](https://advertising.amazon.com/resources/ad-specs/alexa-display-ads/home-screen)|Alexa Home Screen display ads are static full-screen images in rotation across the multi-modal Echo Show devices. Ads will display during ambient use and be in rotation with other content such as weather, recipes, sports and news. Customers will be able to interact with the ad by tapping/saying “Alexa, watch trailer” or "Alexa, play video" to view a video which resolves back to the static screen upon completion.|
|Prime Video ads|YES|YES|[Amazon Prime Video Ads](https://advertising.amazon.com/products/prime-video-ads?ref_=a20m_us_spcs_prvd_p_pva)|Full-screen, non-skippable video ads that appear before, during, or after video content in the Amazon Prime Video subscription-based streaming service|
|Digital Out-of-home ads ads|YES|YES|[Digital Out-of-home Ads](https://advertising.amazon.com/solutions/products/out-of-home)|Display ads shown on digital signage in Whole Foods Market and Amazon Fresh stores.|



## 3. What ad types and placements are NOT supported in AMC? 
Currently, AMC does not support the following data. This is not a exhaustive list. We will update the table periodically to reflect the latest status.

|Category|Ad Product|Supported in AMC?|AMC Example|Learn More|Note|
|---|---|---|---|---|---|
|DSP|Kindle|Attributed purchases only|NO|[Kindle](https://advertising.amazon.com/resources/ad-specs/kindle/?ref_=a20m_us_spcs_kso)|AMC brings in attributed purchases, but not impressions or clicks.|
|DSP|Kindle Lockscreen Ads|NO|NO|[Kindle Lockscreen Ads](https://advertising.amazon.com/library/guides/lockscreen-ads/?ref_=a20m_us_search_title)||
|Other|EU Thursday Night Football/Live Sports|NO|NO|[Thursday Night Football](https://advertising.amazon.com/thursday-night-football)||
|Other|Amazon Live|NO|NO|[Amazon Live](https://advertising.amazon.com/solutions/products/amazon-live?ref_=a20m_us_hnav_p_lv)||
||Out-of-Home|NO|NO|[Custom Solutions](https://advertising.amazon.com/solutions/products/custom-solutions)||
|Retail|Retail Placements|NO|NO||Retail Ads managed by Retail, not Amazon Ads.|
|Measurement Solution|Amazon Attribution|NO|NO|[Amazon Attribution](https://advertising.amazon.com/solutions/products/amazon-attribution)|Amazon Attribution is an advertising and analytics measurement solution that gives marketers insight into how their non-Amazon marketing channels perform on Amazon.|



## 4. Sponsored ads campaigns

You can identify each ad product for sponsored ads by using the `ad_product_type` column, which can have the following values: 
* `sponsored_display`
* `sponsored_products`
* `sponsored_brands`
* `sponsored_television`



### 4.1 Identify all sponsored ads campaigns


The following query allows you to identify all campaigns and campaign IDs for all sponsored ads product types.  

Customization ideas:
* You can delete one or more ad_product_type values to filter on just those ad product types (for example, only include 'sponsored_products')
* You can uncomment the tracked ASIN line return only campaigns tracked by specific ASINs.
* You can add additional metrics such as total purchases (by adding SUM(purchases) AS total_purchases) and clicks. If you do add additional metrics, the output may contain blank lines due to AMC aggregation thresholds.


```
-- Instructional Query: Identify your campaigns (Sponsored ads campaigns)
-- AD PRODUCTS: Sponsored ads
/*
 Customization instructions:
 1. Delete one or more ad_product_types to narrow down the types of campaigns returned.
 2. Uncomment and add ASINs to the filter to only retrieve campaigns that tracked a particular set of ASINs.
 */
SELECT
  campaign,
  campaign_id_string,
  ad_product_type
FROM
  amazon_attributed_events_by_traffic_time
WHERE
  ad_product_type IN (
    'sponsored_products',
    'sponsored_brands',
    'sponsored_display',
    'sponsored_television'
  ) -- AND tracked_asin in ('ASIN1234', 'ASIN5678')
GROUP BY
  1,
  2,
  3
```

| campaign | campaign_id_string | ad_product_type |
| --- | --- | --- |
| Kitchen Smart Coffee SB | 11111111111111 | sponsored_brands |
| Kitchen Smart Coffee SP | 222222222222 | sponsored_products |
| Kitchen Smart Coffee Generic | 33333333333 | sponsored_products |
| Kitchen Smart Coffee SD | 444444444444 | sponsored_display |


### 4.2 Sponsored Display 


```
-- Instructional Query: Ad Product Type - Sponsored Display
-- AD PRODUCTS: Sponsored ads
SELECT
  campaign,
  campaign_id_string,
  ad_product_type,
  SUM(impressions) AS impressions
FROM
  sponsored_ads_traffic
WHERE
  ad_product_type = 'sponsored_display'
GROUP BY
  1,
  2,
  3
```

|campaign|campaign_id_string|ad_product_type|impressions|
|---|---|---|---|
|Campaign Kitchen Smart &#x7c; SD &#x7c; All Products &#x7c; Awareness|11111111111|sponsored_display|2104759|
|Campaign Kitchen Smart &#x7c; SD &#x7c; Toasters &#x7c; Awareness|2222222222222|sponsored_display|337535|
|Campaign Kitchen Smart &#x7c; SD &#x7c; Cups &#x7c; Awareness|33333333333333|sponsored_display|23456|


### 4.3 Sponsored Products 


```
-- Instructional Query: Ad Product Type - Sponsored Products
-- AD PRODUCTS: Sponsored ads
SELECT
  campaign,
  campaign_id_string,
  ad_product_type,
  SUM(impressions) AS impressions
FROM
  sponsored_ads_traffic
WHERE
  ad_product_type = 'sponsored_products'
GROUP BY
  1,
  2,
  3
```

|campaign|campaign_id_string|ad_product_type|impressions|
|---|---|---|---|
|Campaign Kitchen Smart &#x7c; SP &#x7c; All Products &#x7c; Conversion|1111111111111|sponsored_products|170905|
|Campaign Kitchen Smart &#x7c; SP &#x7c; Toasters &#x7c; Conversion|222222222222|sponsored_products|358826|
|Campaign Kitchen Smart &#x7c; SP &#x7c; Cups &#x7c; Conversion|33333333333|sponsored_products|342826|


### 4.4 Sponsored Products Off-Amazon


```
-- Instructional Query: Ad Product Type - Sponsored Products Off-site
-- AD PRODUCTS: Sponsored ads
SELECT
  campaign,
  campaign_id_string,
  ad_product_type,
  placement_type,
  SUM(impressions) AS impressions
FROM
  sponsored_ads_traffic
WHERE
  ad_product_type = 'sponsored_products'
AND 
   placement_type = 'Off Amazon' 
GROUP BY
  1,
  2,
  3,
  4
```

| campaign                                                | campaign_id_string| ad_product_type    | placement_type | impressions |
| ------------------------------------------------------- | -------- | ------------------ | -------------- | ----------- |
| Campaign Kitchen Smart - SP - All Products - Conversion | 1111111111111111| sponsored_products | Off Amazon     | 170905      |
| Campaign Kitchen Smart - SP - Toasters - Conversion     | 2222222222222222| sponsored_products | Off Amazon     | 358826      |
| Campaign Kitchen Smart - SP - Cups - Conversion         | 3333333333333333| sponsored_products | Off Amazon     | 342826      |


### 4.5 Sponsored Brands


```
-- Instructional Query: Ad Product Type - Sponsored Brands
-- AD PRODUCTS: Sponsored ads
SELECT
  campaign,
  campaign_id_string,
  ad_product_type,
  SUM(five_sec_views) AS five_sec_views,
  SUM(video_complete_views) AS video_complete_views,
  SUM(video_first_quartile_views) AS video_first_quartile_views,
  SUM(video_midpoint_views) AS video_midpoint_views,
  SUM(video_third_quartile_views) AS video_third_quartile_views,
  SUM(video_unmutes) AS video_unmutes,
  SUM(impressions) AS impressions
FROM
  sponsored_ads_traffic
WHERE
  ad_product_type = 'sponsored_brands'
GROUP BY
  1,
  2,
  3
```

|campaign| campaign_id_string|ad_product_type|five_sec_views|video_complete_views|video_first_quartile_views|video_midpoint_views|video_third_quartile_views|video_unmutes|impressions|
|---|---|---|---|---|---|---|---|---|---|
|Campaign Kitchen Smart &#x7c; SB &#x7c; All Products &#x7c; Awareness|11111111111111|sponsored_brands|265|41|315|129|67|0|2356|
|Campaign Kitchen Smart &#x7c; SB &#x7c; Toasters &#x7c; Awareness|2222222222222|sponsored_brands|273|49|372|156|87|0|2397|
|Campaign Kitchen Smart &#x7c; SB &#x7c; Cups &#x7c; Awareness|333333333333|sponsored_brands|0|0|0|0|0|0|44329|


### 4.6 Sponsored Brands Video (SBV)

Sponsored Brands supports multiple ad formats such as Retail (Product Collection, and Store Spotlight), and Video (SBV). To differentiate between these ad formats, use the video viewership metrics (metrics starting with `video_` and `five_sec_views`) from the `sponsored_ads_traffic` table. These metrics will be populated only for Sponsored Brands Video (SBV) ads, while it will be shown as NULL for other ad formats.



```
-- Instructional Query: Ad Product Type - Sponsored Brands Video
-- AD PRODUCTS: Sponsored ads

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
  campaign_id_string,
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

|campaign|campaign_id_string|five_sec_views|video_complete_views|video_first_quartile_views|video_midpoint_views|video_third_quartile_views|video_unmutes|impressions|
|---|---|---|---|---|---|---|---|---|
|Campaign Kitchen Smart &#x7c; SB Video &#x7c; All Products &#x7c; Awareness|111111111111|320|49|435|178|85|0|2961|
|Campaign Kitchen Smart &#x7c; SB Video &#x7c; Toasters &#x7c; Awareness|2222222222222|881|151|1207|503|260|1|6839|
|Campaign Kitchen Smart &#x7c; SB Video &#x7c; Cups &#x7c; Awareness|333333333333|265|41|315|129|67|0|2356|


### 4.7 Sponsored TV


```
-- Instructional Query: Exploratory query for Sponsored TV signals in sponsored_ads_traffic table--
-- AD PRODUCTS: Sponsored ads
SELECT
  event_date,
  campaign_id_string,
  campaign,
  SUM(impressions) AS impressions,
  SUM(clicks) AS clicks,
  SUM(spend) AS spend
FROM
  sponsored_ads_traffic
WHERE
  ad_product_type = 'sponsored_television' and
  (
      CUSTOM_PARAMETER('campaign_id') IS NULL
      OR CARDINALITY(CUSTOM_PARAMETER('campaign_id')) = 0
      OR ARRAY_CONTAINS(CUSTOM_PARAMETER('campaign_id'), campaign_id_string)
  )
GROUP BY
  1,
  2,
  3
```

|event_date|campaign_id_string|campaign|impressions|clicks|spend|
|---|---|---|---|---|---|
|2024-09-26|111111111111|Campaign Kitchen Smart All Products - Awareness|320456|4944|435|
|2024-09-26|222222222222|Campaign Kitchen Smart Toasters - Awareness|881565|7515|1207|
|2024-09-26|3333333333333|Campaign Kitchen Smart Cups - Awareness|265656|4156|315|


## 5. Amazon DSP campaigns 

You can identify Amazon DSP ad products by using one or more of the following columns:

* `campaign`
* `campaign_id_string` (preferred over `campaign_id`)
* `line_item`
* `line_item_type`
* `site`
* `supply_source`

Amazon DSP campaigns do not have the `ad_product_type` field (like sponsored ads campaigns do). 

Subsequently not all inventory can be filtered unless this was considered during campaign setup. For example, including ad product type in the campaign name or line item can help identify ad products.


### 5.1 Identify all Amazon DSP campaigns

To identify Amazon DSP campaigns for traffic events, use:
* `dsp_impressions`
* `dsp_clicks`
* `dsp_views`

To identify Amazon DSP campaigns for conversion events, use:
* `amazon_attributed_events_by_conversion_time`
* `amazon_attributed_events_by_traffic_time`

For Amazon DSP campaigns, the field `ad_product_type` is not available and the value will be `'NULL'`. This allows you to query just the Amazon DSP campaigns (and not sponsored ads campaigns) by using  `ad_product_type IS NULL`.



```
-- Instructional Query: Identify your campaigns (Amazon DSP campaigns)
-- AD PRODUCTS: Amazon DSP

-- This section pulls DSP impressions by campaign --
WITH dsp_imp_purch AS (
SELECT
campaign,
campaign_id_string,
SUM(impressions) AS impressions,
0 AS purchases
FROM
dsp_impressions
GROUP BY
1,
2

UNION

-- This section pulls DSP conversions by campaign --
SELECT
campaign,
campaign_id_string,
0 AS impressions,
SUM(purchases) AS purchases
FROM
amazon_attributed_events_by_conversion_time
WHERE
ad_product_type IS NULL
GROUP BY
1,
2
)

SELECT
campaign,
campaign_id_string,
SUM(impressions) AS impressions,
SUM(purchases) AS purchases
FROM dsp_imp_purch
GROUP BY 1, 2

```

|campaign|campaign_id_string|impressions|purchases|
|---|---|---|---|
|Campaign Kitchen Smart &#x7c; DSP &#x7c; All Products &#x7c; Awareness|11111111111111|765470|2104|
|Campaign Kitchen Smart &#x7c; DSP &#x7c; Toasters &#x7c; Awareness|222222222222|98706|337|
|Campaign Kitchen Smart &#x7c; DSP &#x7c; Cups &#x7c; Awareness|33333333333|76890|234|


### 5.2 Streaming TV and Online Video

Streaming TV ads are ads that cannot be skipped and appear either before, during, or after streaming content. Online video ads appear in both in-stream and out-stream formats, across browser and app, on devices such as desktop, mobile, and tablet. Online video formats include in-feed video, in-article/in-read video, video in-banner, and interstitial video. Learn more [here](https://advertising.amazon.com/solutions/products/video-ads?ref_=a20m_us_hnav_p_va).

This query will return all video line items, including Streaming TV (STV) and Online Video (OLV).



```
-- Instructional Query: Ad Product Type - STV and OLV
-- AD PRODUCTS: Amazon DSP

SELECT
campaign,
campaign_id_string,
line_item,
line_item_id,
line_item_type,
supply_source,
device_type,
SUM(impressions) AS impressions
FROM
dsp_impressions
WHERE
line_item_type = 'AAP_VIDEO_CPM'
GROUP BY
1,
2,
3,
4,
5,
6,
7
```

|campaign|campaign_id_string|line_item|line_item_id|line_item_type|supply_source|device_type|impressions|
|---|---|---|---|---|---|---|---|
|Kitchen Smart - Video Ads - Guaranteed - Streaming TV|1111111111111|Video &#x7c; Amazon Guarantee &#x7c; _15s|222222222|AAP_VIDEO_CPM|Amazon Publisher Services|TV|118|
|Kitchen Smart - Video Ads - Guaranteed - OLV|333333333|Video &#x7c; Mobilweb &#x7c; APS PR|444444444|AAP_VIDEO_CPM|Amazon Publisher Services|Phone|880|
|Kitchen Smart - Video Ads - Guaranteed -  Streaming TV - Amazon Audience - US|5555555555|Video &#x7c; Amazon Guarantee  &#x7c; 15 sec|666666666|AAP_VIDEO_CPM|IMDbTV|TV|49|


### 5.3 Streaming TV 

If you ran both Streaming TV and Online Video campaigns, it is not possible to identify Streaming TV using the `line_item_type` alone.

We recommend addressing different inventory in different line items and using the line item name to distinguish. This will allow you to use a regular expression to identify Streaming TV campaigns. For example, if 'OTT' (case sensitive), 'streaming' (case insensitive), or STV (case sensitive) were used in the line item or campaign name, the query below will return OTT video line items only. 



```
-- Instructional Query: Ad Product Type - STV
-- AD PRODUCTS: Amazon DSP
SELECT
  campaign,
  campaign_id_string,
  line_item,
  line_item_id,
  line_item_type,
  supply_source,
  device_type,
  SUM(impressions) AS impressions
FROM
  dsp_impressions
WHERE
  line_item_type = 'AAP_VIDEO_CPM'
  AND (
    line_item SIMILAR TO 'OTT'
    OR campaign SIMILAR TO 'OTT'
    OR line_item SIMILAR TO '(?i)streaming'
    OR campaign SIMILAR TO '(?i)streaming'
    OR line_item SIMILAR TO 'STV'
    OR campaign SIMILAR TO 'STV'
  )
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6,
  7
```

|campaign|campaign_id_string|line_item|line_item_id|line_item_type|supply_source|device_type|impressions|
|---|---|---|---|---|---|---|---|
|Kitchen Smart - Video Ads - Guaranteed - Streaming TV|1111111111111|Video &#x7c; Amazon Guarantee &#x7c; _15s|222222222|AAP_VIDEO_CPM|Amazon Publisher Services|TV|32|
|Kitchen Smart - Video Ads - Guaranteed -  Streaming TV - Amazon Audience - US|333333333|Video &#x7c; Amazon Guarantee 15s|444444444|AAP_VIDEO_CPM|IMDbTV|Tablet|17386|
|Kitchen Smart - Video Ads - Guaranteed - Streaming TV|1111111111111|Video &#x7c; Amazon Guarantee &#x7c; 30 sec|222222222|AAP_VIDEO_CPM|Amazon Publisher Services|Phone|304|


### 5.4 Online Video
If you ran both Streaming TV and Online Video campaigns, it is not possible to identify Online Video campaigns using the `line_item_type` alone.

If you separate Online Video (OLV) in campaigns or line items and use ‘OLV’ (case sensitive) or ‘Online video’ (case insensitive) in the line item or campaign name, then the query below will help identify OLV traffic. 



```
-- Instructional Query: Ad Product Type - Online Video
-- AD PRODUCTS: Amazon DSP
SELECT
  campaign,
  campaign_id_string,
  line_item,
  line_item_id,
  line_item_type,
  site,
  supply_source,
  device_type,
  SUM(impressions) AS impressions
FROM
  dsp_impressions
WHERE
  line_item_type = 'AAP_VIDEO_CPM'
  AND (
    line_item SIMILAR TO 'OLV'
    OR campaign SIMILAR TO 'OLV'
    OR line_item SIMILAR TO '(?!)online video'
    OR campaign SIMILAR TO '(?!)online video'
  )
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6,
  7,
  8
```

|campaign|campaign_id_string|line_item|line_item_id|line_item_type|supply_source|impressions|
|---|---|---|---|---|---|---|
|Kitchen Smart _OLV_dpvr|1111111111111|Video &#x7c; Mobilweb &#x7c; APS PR|333333333|AAP_VIDEO_CPM|Amazon Publisher Services|629722|
|Kitchen Smart _OLV_dpvr|1111111111111|Video &#x7c; Desktop &#x7c; APS PR|444444444|AAP_VIDEO_CPM|Amazon Publisher Services|477556|
|Kitchen Smart _OLV_dpvr|1111111111111|Video &#x7c; Desktop &#x7c; APS PR|555555555|AAP_VIDEO_CPM|Amazon Publisher Services|389578|
|Kitchen Smart _OLV_linkedout_cpc|122222222|Video &#x7c; Desktop &#x7c; APS PR|666666666|AAP_VIDEO_CPM|Amazon Publisher Services|58494|
|Kitchen Smart _OLV_linkedout_cpc|122222222|Video &#x7c; Desktop &#x7c; APS List|7777777777|AAP_VIDEO_CPM|Amazon Publisher Services|327|


### 5.5 Amazon DSP Fire TV Campaigns 

This query will return all Fire TV campaigns served on Amazon DSP with supply source `MOBILEAPP_AMAZON_FIRE_TV` (case insensitive). Fire TV ads include Sponsored Display, Feature Rotator, Sponsored Screensaver, and In Line Banner. You may further filter down to the type of Fire TV ads by using `line_item` filter.



```
-- Instructional Query: Ad Product Type - DSP Fire TV 
-- AD PRODUCTS: Amazon DSP
SELECT
  campaign,
  campaign_id_string,
  line_item,
  line_item_id,
  line_item_type,
  site,
  supply_source,
  SUM(impressions) AS impressions
FROM
  dsp_impressions
WHERE
  supply_source SIMILAR TO '(?i)MOBILEAPP_AMAZON_FIRE_TV'
--Optional Update: We recommend run the query using the above supply_source filter. Then you may further filter down to the type of Fire TV ads by using the line_item filter. Here are some examples of line_item filter.
--AND line_item SIMILAR TO '(?i)Fire TV US Screensaver Unguaranteed' 
--AND line_item SIMILAR TO '(?i)Fire TV US FR Home Slot 2'
--AND line_item SIMILAR TO '(?i)AMZN US Fire TV Inline Home'
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6,
  7
```

|campaign|campaign_id_string|line_item|line_item_id|line_item_type|site|supply_source|impressions|
|---|---|---|---|---|---|---|---|
|Kitchen Smart  - Sponsorship AMZN US Fire TV Screensaver|1111111111111|Fire TV US Screensaver Unguaranteed|444444444|AAP_MOBILE|FireTV Service (ID: 6ca99247d0c04edea1baaa73fbd52f5a)|MOBILEAPP_AMAZON_FIRE_TV|16329476|
|Kitchen Smart Feature Rotator Home Guaranteed|222222222|Fire TV US FR Home Slot 2|555555555|CLASS_I_MOBILE_APP|FireTV Service (ID: 6ca99247d0c04edea1baaa73fbd52f5a)|MOBILEAPP_AMAZON_FIRE_TV|17236|
|Kitchen Smart Fire TV Inline Home|333333333|AMZN US Fire TV Inline Home|666666666|CLASS_I_MOBILE_APP|FireTV Service (ID: 6ca99247d0c04edea1baaa73fbd52f5a)|MOBILEAPP_AMAZON_FIRE_TV|4147260|


### 5.6 Amazon DSP Fire Tablet Campaigns

Use the `line_item_type` filter first to identify Fire Tablet campaigns. Additionally, you may use the `supply_source` filter (case insensitive).

This query will return all Fire Tablet campaigns served on Amazon DSP.



```
-- Instructional Query: Ad Product Type - DSP Fire Tablet
-- AD PRODUCTS: Amazon DSP
SELECT
  campaign,
  campaign_id_string,
  line_item,
  line_item_id,
  line_item_type,
  supply_source,
  device_type,
  SUM(impressions) AS impressions
FROM
  dsp_impressions
WHERE
  line_item_type = '(?!)DTCP'
  AND supply_source = '(?!)MOBILEAPP_AMAZON_FIRE_TABLET'
  
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6,
  7
```

|campaign|campaign_id_string|line_item|line_item_id|line_item_type|supply_source|device_type|impressions|
|---|---|---|---|---|---|---|---|
|Kitchen Smart - Acquisition_Fire Tablet_Wakescreen|1111111111111|Fire Tablet &#x7c; Unguaranteed|444444444|DTCP|MOBILEAPP_AMAZON_FIRE_TABLET|Tablet|1212975|
|Kitchen Smart - Growth Takeovers&#x7c;2022 - Q4 - Fire Tablet_ Wakescreen SOV 25%|222222222|Fire Tablet &#x7c; Takeover Full|555555555|DTCP|MOBILEAPP_AMAZON_FIRE_TABLET|Tablet|1727727|


### 5.7 Amazon DSP Display Mobile Campaigns 

This query will return all line items where the `line_item_type` is `AAP_Mobile`, `CLASS_I_MOBILE_APP` and `CLASS_I_MOBILE_WEB`. The results include display ads served across both mobile web and mobile app. CLASS_I Ads offers guaranteed delivery across Amazon Desktop and Mobile shopping sites/apps. You may further narrow down the mobile ad type by using the `supply_source` filter. 



```
-- Instructional Query: Ad Product Type - DSP Display Mobile Web.
-- AD PRODUCTS: Amazon DSP
SELECT
  campaign,
  campaign_id_string,
  line_item,
  line_item_id,
  line_item_type,
  site,
  supply_source,
  SUM(impressions) AS impressions
FROM
  dsp_impressions
WHERE
  line_item_type IN ('AAP_MOBILE', 'CLASS_I_MOBILE_APP', 'CLASS_I_MOBILE_WEB')
 
 /**Optional Update: Use the following example filters to further narrow down the mobile ad type. There are hundreds of supply sources, you may identify 
 all the supply sources your campaign ran on by running this query without the supply_source filter. Please note these filters are for US, these filters need to be updated for different marketplaces.
 **/
 -- AND 
 -- supply_source SIMILAR TO '(?i)m.amazon.com' -- Uncomment this line if you want to identify Display ads served on Amazon mobile web.
 -- supply_source SIMILAR TO '(?i)AMAZON_US MSHOP WEBVIEW' --Uncomment this line if you want to identify Display ads served on Amazon Shopping App.
 -- supply_source SIMILAR TO '(?i)Amazon Publisher Services' --Uncomment this line if you want to identify Display ads served on APS.
 -- supply_source SIMILAR TO '(?!)M.IMDB.COM' -- Uncomment this line if you want to identify Display ads served on IMDB mobile web.
 
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6,
  7
```

|campaign|campaign_id_string|line_item|line_item_id|line_item_type|site|supply_source|impressions|
|---|---|---|---|---|---|---|---|
|Kitchen Smart - 2023 Always On - Purchase|1111111111|Kitchen Smart - Brand Purchasers - O&O - Mobile App|4444444|AAP_MOBILE|amazon.com|AMAZON_US MSHOP WEBVIEW|23254|
|Kitchen Smart_Supplements Display  Conversions|2222222|Kitchen Smart_O&O|5555555|AAP_MOBILE|amazon.com|AMAZON_US MSHOP WEBVIEW|18588160|
|Kitchen Smart 2022 Always-On|3333333|Kitchen Smart * Always-On * O&O * Mobile App|6666666|AAP_MOBILE|amazon.com|AMAZON_US MSHOP WEBVIEW|40108|


### 5.8 Amazon DSP Display Desktop campaigns 

This query will return all DSP Display line items ran on desktop.



```
-- Instructional Query: Ad Product Type - DSP Display Desktop
-- AD PRODUCTS: Amazon DSP
SELECT
  campaign,
  campaign_id_string,
  line_item,
  line_item_id,
  line_item_type,
  site,
  device_type,
  supply_source,
  SUM(impressions) AS impressions
FROM
  dsp_impressions
WHERE
    line_item_type IN ('AAP', 'CLASS_I_WEB')
AND 
    device_type = 'PC'
    
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6,
  7,
  8
```

|campaign|campaign_id_string|line_item|line_item_id|line_item_type|site|device_type|supply_source|impressions|
|---|---|---|---|---|---|---|---|---|
|Kitchen Smart 2022 Always-On|1111111111|Kitchen Smart * Always-On  * Desktop|22222222|AAP|aaaaaaaaa.com|PC|INDEX|14876|
|Kitchen Smart 2022 Always-On|1111111111|Kitchen Smart * Always-On * Desktop|22222222|AAP|bbbbbbbbb.com|PC|PUBMATIC WEB DISPLAY|3457|
|Kitchen Smart 2022 Always-On|1111111111|Kitchen Smart * Always-On * Desktop|22222222|CLASS_I_WEB|ccccccccc.com|PC|Amazon Publisher Services|9876|


### 5.9 Amazon DSP Audio Campaigns 

Amazon DSP audio campaigns are identified using `line_item_type` with the following possible values: `AAP_AUDIO_CPM`, `TWITCH AUDIO`, and `NEWS AUDIO`. The query below will identify the audio campaigns that your instance ran. To further filter down to a specific type of audio campaign, you may use the `line_item_type` filter. 



```
-- Instructional Query: Ad Product Type - Audio
-- AD PRODUCTS: Amazon DSP
SELECT
  campaign,
  campaign_id_string,
  line_item,
  line_item_id,
  line_item_type,
  site,
  supply_source,
  SUM(impressions) AS impressions
FROM
  dsp_impressions
WHERE
  supply_source SIMILAR TO '(?i)audio'
---Optional Update: use the line_item_type filter below to identify specific types of Audio campaigns. Example of line_item_type for audio campaigns are below.
-- AND line_item_type IN ('AAP_AUDIO_CPM', 'TWITCH AUDIO', 'NEWS AUDIO')
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6,
  7
```

|campaign|campaign_id_string|line_item|line_item_id|line_item_type|site|supply_source|impressions|
|---|---|---|---|---|---|---|---|
|Kitchen Smart - Audio  Ads ‚ Guaranteed - Voice Actionable Audio Ad|1111111111|Audio Ads - Guaranteed - RON - Connected Devices Only|222222222|AAP_AUDIO_CPM|Amazon Music Free (ID: amazon-audio-ama)|AMAZON AUDIO|232|
|Kitchen Smart - Audio  Ads - Guaranteed|2222222|Audio Ads - Guaranteed - RON - US|333333333|AAP_AUDIO_CPM|Amazon Music Free (ID: amazon-audio-ama)|AMAZON AUDIO|2450|
|Kitchen Smart  - Audio Ads - Guaranteed|2222222|Audio Ads - Guaranteed - RON - US|333333333|AAP_AUDIO_CPM|Amazon Music Free (ID: amazon-audio-ama)|AMAZON AUDIO|599|


### 5.10 IMDb 

This query provides all line items where the supply source contains ‘IMDb’ (case insensitive). 



```
-- Instructional Query: Ad Product Type - IMDb
-- AD PRODUCTS: Amazon DSP
SELECT
  campaign,
  campaign_id_string,
  line_item,
  line_item_id,
  line_item_type,
  supply_source,
  site,
  SUM(impressions) AS impressions
FROM
  dsp_impressions
WHERE
  supply_source SIMILAR TO '(?i)IMDb'
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6,
  7
```

|campaign|campaign_id_string|line_item|line_item_id|line_item_type|supply_source|site|impressions|
|---|---|---|---|---|---|---|---|
|Kitchen Smart - Amazon DSP - Guaranteed - Streaming TV|111111111111|Video &#x7c; Amazon Guarantee &#x7c; 30 sec|222222222|AAP_VIDEO_CPM|IMDbTV|IMDbTV|114000|
|Kitchen Smart - Amazon DSP - Guaranteed - Streaming TV|111111111111|Video &#x7c; Amazon Guarantee 15 sec|333333333|AAP_VIDEO_CPM|IMDbTV|IMDbTV|113125|
|Kitchen Smart- Amazon DSP - Guaranteed - Streaming TV|111111111111|Video &#x7c; Amazon Guarantee &#x7c; 30 sec|444444444|AAP_VIDEO_CPM|IMDbTV|amazon.com|58220|


### 5.11 IMDb App only

This query returns line items where the supply source is IMDb mobile app. This applies to IMDb APP only.



```
-- Instructional Query: Ad Product Type - IMDb App
-- AD PRODUCTS: Amazon DSP
SELECT
  campaign,
  campaign_id_string,
  line_item,
  line_item_id,
  line_item_type,
  site,
  supply_source,
  SUM(impressions) AS impressions
FROM
  dsp_impressions
WHERE
  supply_source = 'MOBILEAPP_IMDB_APPS'
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6,
  7
```

|campaign|campaign_id_string|line_item|line_item_id|line_item_type|supply_source|site|impressions|
|---|---|---|---|---|---|---|---|
|Kitchen Smart - Display ads - Amazon DSP - Consideration package - CTR - CPC|111111111111|MOBILE &#x7c; MR &#x7c;|222222222|AAP_MOBILE|MOBILEAPP_IMDB_APPS|IMDB App (ID: 1a8baf1b38774b6082804720d76bfb9d)|54815|
|Kitchen Smart 2022 Upper Funnel - Audience Breakout|111111111111|Kitchen Smart * Upper Funnel * Upper Brand Shopper * APS * Mobile App|333333333|AAP_MOBILE|MOBILEAPP_IMDB_APPS|IMDB App (ID: 1a8baf1b38774b6082804720d76bfb9d)|8421|
|Kitchen Smart  2022 Incrementality Test|111111111111|Kitchen Smart* Test and Learn * Competitive * APS * Mobile App * Exposed|444444444|AAP_MOBILE|MOBILEAPP_IMDB_APPS|IMDB App (ID: 1a8baf1b38774b6082804720d76bfb9d)|5364|


### 5.12 IMDb Mobile Web 

This query returns line items where the supply source is IMDb mobile app. 



```
-- Instructional Query: Ad Product Type - IMDb Mobile Web
-- AD PRODUCTS: Amazon DSP

SELECT
  campaign,
  campaign_id_string,
  line_item,
  line_item_id,
  line_item_type,
  site,
  supply_source,
  SUM(impressions) AS impressions
FROM
  dsp_impressions
WHERE
  supply_source = 'M.IMDB.COM'
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6,
  7
```

|campaign|campaign_id_string|line_item|line_item_id|line_item_type|site|supply_source|impressions|
|---|---|---|---|---|---|---|---|
|Kitchen Smart - Display ads - Amazon DSP - Conversion package - ROAS|111111111111|MOBILE &#x7c; O&O &#x7c; ASIN &#x7c; RT &#x7c; Promoted + Brand|222222222|AAP_MOBILE|imdb.com|M.IMDB.COM|123367|
|Kitchen Smart 2022 Always-On|111111111111|Kitchen Smart * Always-On * Brand Shoppers * APS * Desktop|333333333|AAP|imdb.com|M.IMDB.COM|3456|
|Kitchen Smart 2022 Incrementality Test|111111111111|Kitchen Smart * Always-On * Brand Lapsed Purchasers * APS *  Desktop * Control|444444444|AAP|imdb.com|M.IMDB.COM|9876|


### 5.13 Freevee (Previously IMDb TV)

IMDb TV was rebranded to Freevee in April, 2022. Freevee is a supply source for Streaming TV. To identify all Streaming TV campaigns, use the query in section 4.3. This query will provide all line items where the supply source is Freevee / IMDb TV.



```
-- Instructional Query: Ad Product Type - IMDb TV
-- AD PRODUCTS: Amazon DSP
SELECT
  campaign,
  campaign_id_string,
  line_item,
  line_item_id,
  line_item_type,
  supply_source,
  site,
  SUM(impressions) AS impressions
FROM
  dsp_impressions
WHERE
  supply_source = 'IMDbTV'
  OR 
  supply_source = 'Freevee'
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6,
  7
```

|campaign|campaign_id_string|line_item|line_item_id|line_item_type|supply_source|site|impressions|
|---|---|---|---|---|---|---|---|
|Kitchen Smart - Video Ads - Amazon DSP - Guaranteed - Streaming TV|111111111111|Video &#x7c; Amazon Guarantee &#x7c; 30 sec|22222222|AAP_VIDEO_CPM|IMDbTV|IMDbTV|114000|
|Kitchen Smart - Video Ads - Amazon DSP - Guaranteed - Streaming TV|111111111111|Video &#x7c; Amazon Guarantee 15 sec|33333333|AAP_VIDEO_CPM|IMDbTV|IMDbTV|113125|
|Kitchen Smart - Video Ads - Amazon DSP - Guaranteed - Streaming TV|111111111111|Video &#x7c; Amazon Guarantee &#x7c; 30 sec|44444444|AAP_VIDEO_CPM|IMDbTV|amazon.com|58220|


### 5.14 Twitch DSP Campaigns / Twitch Direct via Amazon DSP

This query will identify all Twitch campaigns. Possible supply source values for Twitch include: `'TWITCH AUDIO'`, `'TWITCH MOBILEAPP VIDEO'`, and `'TWITCH WEB VIDEO'`. You can further filter down the Twitch campaigns by using the supply source values. 



```
-- Instructional Query: Ad Product Type - Twitch
-- AD PRODUCTS: Amazon DSP
SELECT
  campaign,
  campaign_id_string,
  line_item,
  line_item_id,
  line_item_type,
  site,
  supply_source,
  SUM(impressions) AS impressions
FROM
  dsp_impressions
WHERE
  supply_source SIMILAR TO '(?i)twitch'
 /*OPTIONAL UPDATE: If you want to further filter down to specific types of Twitch campaigns, use the following filters */ 
  --- AND supply_source = 'TWITCH AUDIO' 
  --- AND supply_source = 'TWITCH MOBILEAPP VIDEO'
  --- AND supply_source = 'TWITCH WEB VIDEO'
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6,
  7
```

|campaign|campaign_id_string|line_item|line_item_id|line_item_type|site|supply_source|impressions|
|---|---|---|---|---|---|---|---|
|Kitchen Smart_Q4'22 - Display ads - Amazon DSP - Conversion package -  ROAS - US|111111111111|DESKTOP &#x7c; 3P &#x7c; ASIN &#x7c; RT &#x7c; Promoted + Brand|22222222|AAP|site_111111111|TWITCH WEB DISPLAY|20046|
|Kitchen Smart_Q4'22 - Display ads - Amazon DSP - Conversion package - ROAS - US|111111111111|DESKTOP &#x7c; 3P &#x7c; ASIN &#x7c; RT &#x7c; Promoted + Brand|22222222|AAP|site_111111111|TWITCH MOBILE DISPLAY|12990|
|Kitchen Smart_Q4'22 - Video  Ads - Amazon DSP - Guaranteed - Streaming TV|111111111111|Video &#x7c; Amazon Guarantee 15s|22222222|AAP_VIDEO_CPM|Twitch iOS (ID: 22222222)|TWITCH MOBILEAPP VIDEO|269|


### 5.15 Campaigns that used Twitch Audience Segments

This query will provide segments targeted in Amazon DSP Twitch campaigns, but not necessarily ads served on Twitch. Please note that this is a filter based on the segment name containing “twitch”. As result, all segment containing "twitch" will show up here.



```
-- Instructional Query: Ad Product Type - Twitch Segments Only
-- AD PRODUCTS: Amazon DSP
SELECT
  campaign,
  campaign_id_string,
  behavior_segment_name AS segment_name,
  behavior_segment_id,
  SUM(impressions) AS impressions
FROM
  dsp_impressions_by_user_segments
WHERE
  behavior_segment_name SIMILAR TO '(?i)twitch'
GROUP BY
  1,
  2,
  3,
  4
```

|campaign|campaign_id_string|segment_name|behavior_segment_id|impressions|
|---|---|---|---|---|
|Kitchen Smart FY2022 - Retargeting [Desktop]|111111111111111|Twitch - Sports Viewers|520428|1596|
|Kitchen Smart FY2022 - Prospecting [Mobile]|222222222222222|Twitch - Non Gaming Viewers|655230|1423|
|Kitchen Smart FY2022 - Retargeting [Desktop]|33333333333333|Twitch - Sports Viewers|520428|1389|


### 5.16 Thursday Night Football (TNF) / Live Sports 

TNF/Live Sports campaigns are served through Amazon DSP. As long as the advertiser CFID is added to the AMC instance, TNF campaign data should be tracked in AMC with `AMAZON LIVE STREAM` as supply source.



```
-- Instructional Query: Ad Product Type - Thursday Night Football (TNF) / Live Sports
-- AD PRODUCTS: Amazon DSP
SELECT
  campaign,
  campaign_id_string,
  line_item,
  line_item_type,
  supply_source,
  device_type,
  SUM(impressions) AS impressions
FROM
  dsp_impressions
WHERE
  supply_source ='AMAZON LIVE STREAM'
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6
```

|campaign|campaign_id_string|line_item|line_item_type|supply_source|device_type|impressions|
|---|---|---|---|---|---|---|
|TNF 2022 Kitchen Smart|111111111111|Video &#x7c; Streaming TV &#x7c; TNF &#x7c; Kitchen Smart|AAP_VIDEO_CPM|AMAZON LIVE STREAM|PC|35234|
|TNF 2022 Kitchen Smart|111111111111|Video &#x7c; Streaming TV &#x7c; TNF &#x7c; Kitchen Smart|AAP_VIDEO_CPM|AMAZON LIVE STREAM|Phone|21234|
|TNF 2022 Kitchen Smart|111111111111|Video &#x7c; Streaming TV &#x7c; TNF &#x7c; Kitchen Smart|AAP_VIDEO_CPM|AMAZON LIVE STREAM|TV|171112|


### 5.17 Alexa Home Screen Display 

Alexa Home Screen Display campaigns are served through Amazon DSP. As long as the advertiser CFID is added to the AMC instance, Alexa Home Screen Display campaign data should be tracked in AMC with `'ALEXA_DEVICES'` as supply source.



```
-- Instructional Query: Ad Product Type - Alexa Home Screen Display
-- AD PRODUCTS: Amazon DSP
SELECT  
  campaign,
  campaign_id_string,
  line_item,
  line_item_id,
  line_item_type,
  site,
  supply_source,
  device_type,
  SUM(impressions) AS impressions
FROM
  dsp_impressions
WHERE
  supply_source = 'ALEXA_DEVICES'
  AND 
  line_item_type = 'CLASS_I_MOBILE_APP'
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6,
  7,
  8
```

|campaign|campaign_id_string|line_item|line_item_id|line_item_type|site|supply_source|device_type|impressions|
|---|---|---|---|---|---|---|---|---|
|Kitchen Smart- AMZN US Alexa Home Screen Guaranteed|111111111111|AMZN_US_Alexa_Home_Screen_Guaranteed|333333333|CLASS_I_MOBILE_APP|alexa-devices (ID: 111111111111)|ALEXA_DEVICES|PC|152373|
|Kitchen Smart - AMZN US Alexa Home Screen Guaranteed - Added Value|22222222|AMZN_US_Alexa_Home_Screen_Guaranteed|444444444|CLASS_I_MOBILE_APP|alexa-devices (ID: 111111111111)|ALEXA_DEVICES|PC|7192|


### 5.18 Prime Video ads


```
-- Instructional Query: Identifying Prime Video ads campaigns--
-- AD PRODUCTS: Amazon DSP

SELECT
  campaign,
  campaign_id_string,
  supply_source
FROM
  dsp_impressions
WHERE
  supply_source = 'Prime Video ads'
GROUP BY
  1,
  2,
  3
```

|campaign|campaign_id_string|supply_source|
|---|---|---|
|Kitchen Smart - Awareness - Prime Video|111111111111|Prime Video ads|
|Kitchen Smart - Awareness - Prime Video - Added Value|22222222|Prime Video ads|


### 5.19 Digital Out-of-home (DOOH) ads

Digital Out-of-home (DOOH) ads are display ads shown in digital signage in Amazon Own & Operated Grocery Physical Stores. For more information about DOOH ads, [click here](https://advertising.amazon.com/solutions/products/out-of-home).

* Using `dsp_impressions` and `dsp_views`, DOOH campaigns can be filtered using `supply_source_id = 702`.
* Using `amazon_attributed_events_by_*` tables, attribution from DOOH supply can be filtered using `supply_source = Digital out-of-home (DOOH)`.



```
-- Exploratory Query: Identify DOOH campaigns
-- AD PRODUCTS: Amazon DSP

SELECT
  campaign,
  campaign_id_string,
  supply_source,
  supply_source_id
FROM
  dsp_impressions
WHERE
  supply_source_id = 702
GROUP BY
  1,
  2,
  3,
  4
```

| campaign                                                    | campaign_id_string | supply_source              | supply_source_id |
|-------------------------------------------------------------|--------------------|----------------------------|------------------|
| Kitchen Smart - Awareness - Digital signage                 | 111111111111       | Digital out-of-home (DOOH) | 702              |
| Kitchen Smart - Awareness - - Digital signage - Added Value | 22222222           | Digital out-of-home (DOOH) | 702              |


