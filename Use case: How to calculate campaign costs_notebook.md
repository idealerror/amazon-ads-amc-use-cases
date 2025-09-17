## Metadata

**id:** 12695f72368210088103b0421a41aa86ff4730a59fd5bad5f113c6f231b1fc7a
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Calculate the costs of Amazon DSP and sponsored ads campaigns.
**tags:** []
**use_case_categories:** ['How to', 'Cost analysis']
**advertiser_types:** ['On-Amazon conversions', 'Off-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## Introduction

This instructional query provides examples of how to calculate campaign costs using traffic-specific tables. The below tables contain impression and click events decorated with all costs and fees related to each event:

* [dsp_impressions](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/dsp_impressions) for Amazon DSP ads
* [sponsored_ads_traffic](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/sponsored_ads_traffic) for sponsored ads

If you are looking to calculate your total spend, it is critical to leverage these tables, as they contain the full universe of traffic events (i.e. impression and click events) for your campaigns, and therefore include the full cost information for your campaigns. You should not use other tables (such as the `amazon_attributed_events_*` tables) to calculate campaign costs.

## Convert microcents and millicents to your currency

AMC includes a variety of cost and fee fields; some of these fields are reported in microcents, and some are reported in millicents. This has implications for what formula you should use to get AMC costs in dollars/your currency. For reference:

To convert **microcent** fields into dollars/your currency:
  `SUM([field] / 100000000)`
  
To convert **millicent** fields into dollars/your currency:
   `SUM([field] / 100000)`

## How to calculate costs for Amazon DSP campaigns

See below for the cost and fee fields that are available in AMC for Amazon DSP. Note that you might not see values for all fields; what cost information you see in AMC is dependent on your campaign setup in Amazon DSP.

| Field | Description |
|--------|-------------|
| supply_cost | The cost (in microcents) that Amazon DSP pays a publisher or publisher ad tech platform (such as an SSP or exchange) for an impression. |
| platform_fee | The fee (in microcents) that Amazon DSP charges customers to access and use Amazon DSP. The platform fee (also known as the technology fee or console fee in other contexts) is calculated as a percentage of the supply cost. |
| managed_service_fee | The fee (in microcents) that Amazon DSP charges to customers for campaign management services provided by Amazon's in-house service team. The managed service fee is calculated as a percentage of the supply cost. |
| audience_fee | The fee (in microcents) that Amazon DSP charges to customers to utilize Amazon audiences. |
| ocm_fee | The fee (in microcents) that Amazon DSP charges for the use of omnichannel measurement studies. The omnichannel metrics fee is calculated as a percentage of the supply cost. |
| third_party_fees | The sum of all third-party fees charged for the impression (in microcents). |
| impression_cost | Impression cost (in millicents). |
| total_cost | Total cost (in millicents). |


Some additional notes on these fields and relevant formulas:

* `impression_cost` and `total_cost` are usually the same for Amazon DSP campaigns, since most campaigns are charged on a CPM (cost per mille) basis, which means cost per thousand impressions. They may not match if there is a configured agency fee.
* `impression_cost = supply_cost + platform_fee + managed_service_fee + audience_fee + third_party_fees + ocm_fee`
    * Reminder that you will need to apply appropriate microcent/millicent conversions to calculate these metrics in dollars, as shown in the query below.

See the query below to see how to leverage Amazon DSP cost and fee fields in AMC:


```
-- Instructional Query: How to calcute costs for Amazon DSP campaigns
SELECT
  CONCAT('cid_', campaign_id_string) AS campaign_id,
  campaign,
  currency_name,
  currency_iso_code,
  -- Impression_cost and total_cost are reported in millicents; divide by 100,000 to get the cost in dollars/your currency
  SUM(impression_cost / 100000) AS impression_cost,
  SUM(total_cost / 100000) AS total_cost,
  -- The fees / costs below are reported in microcents; divide by 100,000,000 to get the cost in dollars/your currency
  SUM(supply_cost / 100000000) AS supply_cost,
  SUM(platform_fee / 100000000) AS platform_fee,
  COALESCE(SUM(managed_service_fee) / 100000000, 0) AS managed_service_fee,
  SUM(audience_fee / 100000000) AS audience_fee,
  SUM(third_party_fees / 100000000) AS third_party_fees,
  SUM(ocm_fee / 100000000) AS ocm_fee,
  -- The below formula totals all impression costs and fees and will typically match the total_cost field; however, it may not match if there is a configured agency fee
  (
    COALESCE(SUM(supply_cost) / 100000000, 0) + 
    COALESCE(SUM(platform_fee) / 100000000, 0) + 
    COALESCE(SUM(managed_service_fee) / 100000000, 0) + 
    COALESCE(SUM(audience_fee) / 100000000, 0) + 
    COALESCE(SUM(third_party_fees) / 100000000, 0) + 
    COALESCE(SUM(ocm_fee) / 100000000, 0)
) AS impression_cost_calculated
FROM
  dsp_impressions
GROUP BY
  1,
  2,
  3,
  4
```

## How to calculate costs for sponsored ads campaigns

AMC includes a single cost field for sponsored ads campaigns: `spend`. This field is reported in microcents, and so must be divided by 100,000,000 to convert to dollars/your currency.

The types of sponsored ads traffic events that incur costs will vary based on your price type (e.g. CPC or vCPM). The price type can be determined using the `line_item_price_type` field. More information on the implications of price type on costs can be found below.

### How to calculate costs for sponsored ads CPC campaigns 

Sponsored Products campaigns, Sponsored Brands CPC campaigns, and Sponsored Display CPC campaigns are purchased on a cost-per-click (CPC) basis, which means that there is no cost for impression events that do not result in a click event.  For this reason, you may observe that only click records from these campaigns have nonzero spend. This field will be zero for impression and viewable impression records.

Note that there is no need to filter to JUST click events in order to accurately calculate costs.



```
--- Instructional Query: How to calculate costs for sponsored ads CPC campaigns --
SELECT
  ad_product_type,
  CONCAT ('cid_', campaign_id_string) AS campaign_id,
  campaign,
  line_item_price_type AS price_type,
  currency_iso_code,
  currency_name,
  SUM(impressions) AS impressions,
  SUM(clicks) AS clicks,
  SUM(viewable_impressions) AS viewable_impressions,
  -- Spend is reported as microcents; divide by 100,000,000 to get the cost in dollars/your currency
  SUM(spend / 100000000) AS spend,
  -- Calculate cost per click by dividing spend in dollars/your currency by click total
  (SUM(spend / 100000000) / SUM(clicks)) AS cost_per_click
FROM
  sponsored_ads_traffic
WHERE
  line_item_price_type = 'CPC'
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6
```

### How to calculate costs for sponsored ads vCPM campaigns 

Sponsored Brands vCPM campaigns and Sponsored Display vCPM campaigns are purchased on a cost-per-thousand-viewable-impressions (vCPM) basis, which means that there is no cost for impression events that do not result in a view event. For this reason, you may observe that only viewable impression records from these campaigns have nonzero spend. This field will be zero for impression and click records.

Note that there is no need to filter to JUST viewable impressions in order to accurately calculate costs.



```
-- Instructional Query: How to calculate costs for sponsored ads vCPM campaigns --
SELECT
  ad_product_type,
  CONCAT ('cid_', campaign_id_string) AS campaign_id,
  campaign,
  line_item_price_type AS price_type,
  currency_iso_code,
  currency_name,
  SUM(impressions) AS impressions,
  SUM(clicks) AS clicks,
  SUM(viewable_impressions) AS viewable_impressions,
  -- Spend is reported as microcents; divide by 100,000,000 to get the cost in dollars/your currency
  SUM(spend / 100000000) AS spend,
  -- Calculate vCPM by dividing spend in dollars/your currency by the thousands of viewable impressions
  (SUM(spend / 100000000) / (SUM(viewable_impressions)/1000)) AS vcpm
FROM
  sponsored_ads_traffic
WHERE
  line_item_price_type = 'VCPM'
GROUP BY
  1,
  2,
  3,
  4,
  5,
  6
```
