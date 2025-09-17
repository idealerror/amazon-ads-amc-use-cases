## Metadata

**id:** 7c7c2de7bc59865d93f51bc9d4026c43a527f66a39f50e4dfff4f263b5555caf
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Measure ad-attributed conversion metrics across Amazon.com, Amazon Fresh, and Whole Foods Market.
**tags:** []
**use_case_categories:** ['Performance deep dive', 'Grocery analysis']
**advertiser_types:** ['On-Amazon conversions', 'Physical stores']
**ad_product_types:** None
**shared_with_accounts:** None

---

## 1. Introduction 

### 1.1 Purpose 

This instructional query allows CPG advertisers to measure ad-attributed conversions across [Amazon Fresh](https://www.amazon.com/alm/storefront?almBrandId=QW1hem9uIEZyZXNo&ref_=nav_em__afs_aaf_0_2_11_2), [Whole Foods Market](https://www.amazon.com/alm/storefront?almBrandId=VUZHIFdob2xlIEZvb2Rz&ref_=nav_em_navm_whole_foods_storefront_0_2_27_2), and [Amazon.com](https://www.amazon.com/). The key success metrics evaluated in this report includes ad-attributed purchases, sales, NTB purchases, and NTB sales. These metrics are broken out by campaign line items, and promoted vs brand halo ASINs. For an aggregated report, refer to the instructional query [How to Measure Online Ad-attributed Purchases across Whole Foods Market, Amazon Fresh, and Amazon.com](/marketing-cloud/instructional-queries/be6b3da8e05af20de0ac494f855b445f3e206a3615df3829e3da4d33693cf9e6). 


### 1.2 Requirements

To use this insight, advertisers must have run DSP or Sponsored Ads campaign(s) over the queried time window, with ASINs sold on Amazon.com, Whole Foods Market, or Amazon Fresh. This breakout is only available in the US. 

## 2. Data Query Instructions 

### 2.1 Data Returned

This query will return advertising product type (DSP vs Sponsored Ads), advertiser name, campaign ID, campaign name, line item ID, line item name, tracked ASIN and a column to flag the ASIN as promoted versus brand halo. The metrics returned in this report include purchases, sales, NTB purchases and NTB sales for each marketplace across Amazon.com, Amazon Fresh online, Fresh physical stores, Whole Foods Market on Amazon.com, and Whole Foods Market physical stores.

### 2.2 Tables Used 

**amazon_attributed_events_by_conversions_time**: Due to the 14-day attribution window, wait two weeks after the end of the campaign, or the reporting period to capture all conversions.

## 3. Data Interpretation Instructions 

### 3.1 Example Query Results

This data is for instructional purposes only. Your results will differ.




| ad_product_type    | advertiser           | campaign_id          | campaign_name                                 | line_item_id  | line_item_name                                   | asin     | asin_promoted_or_bh | amazon_com_purchases | whole_foods_physical_store_purchases | whole_foods_on_amazon_purchases | fresh_on_amazon_purchases | amazon_fresh_physical_store_purchases | amazon_com_sales | whole_foods_physical_store_sales | whole_foods_on_amazon_sales | fresh_on_amazon_sales | amazon_fresh_physical_store_sales | amazon_com_ntb_purchases | whole_foods_physical_store_ntb_purchases | whole_foods_on_amazon_ntb_purchases | fresh_on_amazon_ntb_purchases | amazon_fresh_physical_store_ntb_purchases | amazon_com_ntb_sales | whole_foods_physical_store_ntb_sales | whole_foods_on_amazon_ntb_sales |
|--------------------|----------------------|----------------------|-----------------------------------------------|---------------|--------------------------------------------------|----------|---------------------|----------------------|--------------------------------------|---------------------------------|---------------------------|---------------------------------------|------------------|----------------------------------|-----------------------------|-----------------------|-----------------------------------|--------------------------|------------------------------------------|-------------------------------------|-------------------------------|-------------------------------------------|----------------------|--------------------------------------|---------------------------------|
| dsp                | Gordon's Chocolatier |        1111111111111 | 2022 Always On Chocolate Retargeting Campaign | 7300000000000 | Desktop + MOW \| RT \| 180 Purch Excl SnS        | ASIN1234 | Promoted            | 4654                 | 100                                  | 99                              | 108                       | 0                                     | 143069.66        | 2990                             | 2960.1                      | 3229.2                | 0                                 | 931                      | 20                                       | 20                                  | 22                            | 0                                         | 28613.93             | 598                                  | 592.02                          |
| dsp                | Gordon's Chocolatier | 2222222222222        | Coconut Chocolate - In-Market - Cross Sell    | 7620000000000 | Amazon Mobile \| IM \| GOL Cross Sell \| Coconut | ASIN5678 | Brand Halo          | 3274                 | 120                                  | 109                             | 203                       | 0                                     | 95771.75         | 3588                             | 3259.1                      | 6069.7                | 0                                 | 589                      | 24                                       | 22                                  | 41                            | 0                                         | 19154.35             | 717.6                                | 651.82                          |
| dsp                | Gordon's Chocolatier | 3333333333333        | Keto: Purchase - Q1 2022                      | 8400000000000 | Amazon Mobile \| IM \| GOL Cross Sell \| Keto    | ASIN5678 | Brand Halo          | 1492                 | 134                                  | 291                             | 312                       | 0                                     | 43906.88         | 4006.6                           | 8700.9                      | 9328.8                | 0                                 | 269                      | 27                                       | 58                                  | 62                            | 0                                         | 8781.38              | 801.32                               | 1740.18                         |
| dsp                | Gordon's Chocolatier | 4444444444444        | 2022 Always On Chocolate Retargeting Campaign | 7350000000000 | Desktop + MOW \| RT \| 180 Purch Excl SnS        | ASIN8790 | Promoted            | 1405                 | 222                                  | 873                             | 222                       | 0                                     | 47914.9          | 6637.8                           | 26102.7                     | 6637.8                | 0                                 | 253                      | 44                                       | 175                                 | 44                            | 0                                         | 9582.98              | 1327.56                              | 5220.54                         |
| dsp                | Gordon's Chocolatier | 5555555555555        | Coconut: Purchase - Q1 2022                   | 9330000000000 | Amazon Mobile \| IM \| GOL Cross Sell \| Coconut | ASIN7890 | Brand Halo          | 856                  | 167                                  | 172                             | 64                        | 0                                     | 27296.61         | 4993.3                           | 5142.8                      | 1913.6                | 0                                 | 154                      | 33                                       | 34                                  | 13                            | 0                                         | 5459.32              | 998.66                               | 1028.56                         |
| sponsored_products | Gordon's Chocolatier | 6666666666666        | Chocolate \| SP \| NYNY \| Phrase             |               | SP \| Chocolate / Keto \| NYNY \| Phrase \| BAM  | ASIN1234 | Promoted            | 3723                 | 80                                   | 79                              | 86                        | 0                                     | 114455.73        | 2392                             | 2368.08                     | 2583.36               | 0                                 | 745                      | 16                                       | 16                                  | 17                            | 0                                         | 22891.15             | 478.4                                | 473.62                          |
| sponsored_products | Gordon's Chocolatier | 6666666666666        | Chocolate \| SP \| NYNY \| Phrase             |               | SP \| Chocolate / Keto \| NYNY \| Phrase \| BAM  | ASIN7890 | Brand Halo          | 2979                 | 64                                   | 63                              | 69                        | 0                                     | 91564.58         | 1913.6                           | 1894.46                     | 2066.69               | 0                                 | 596                      | 13                                       | 13                                  | 14                            | 0                                         | 18312.92             | 382.72                               | 378.89                          |
| sponsored_display  | Gordon's Chocolatier | 8888888888888        | SD Chocolate Always On                        |               | SD \| Chocolate / Keto                           | ASIN1234 | Promoted            | 2681                 | 58                                   | 57                              | 62                        | 0                                     | 82408.12         | 1722.24                          | 1705.02                     | 1860.02               | 0                                 | 536                      | 12                                       | 11                                  | 12                            | 0                                         | 16481.62             | 344.45                               | 341                             |
| sponsored_brands   | Gordon's Chocolatier | 7777777777777        | SD Chocolate Awareness                        |               | SD \| Chocolate / Keto                           | ASIN7890 | Brand Halo          | 2145                 | 46                                   | 46                              | 22                        | 11                                    | 47914.9          | 6637.8                           | 26102.7                     | 6637.8                | 0                                 | 253                      | 44                                       | 175                                 | 36                            | 0                                         | 9582.98              | 1327.56                              | 2344                            |
| sponsored_brands   | Gordon's Chocolatier | 9999999999999        | Chocolate Retargeting Campaign                |               | Mobile \| RT \| 180 Purch Excl SnS               | ASIN5678 | Promoted            | 1223                 | 22                                   | 11                              | 22                        | 34                                    | 47914.9          | 6637.8                           | 26102.7                     | 1234                  | 0                                 | 221                      | 22                                       | 54                                  | 13                            | 0                                         | 6782                 | 3862                                 | 7833                            |


### 3.2 Columns defined

|Columns|Definition|
|:---|:---|
|`ad_product_type`|Ad product used in the campaign (e.g. DSP, SP etc)|
|`advertiser`|Advertiser Name|
|`campaign_id`|Campaign ID (same as Order ID in Rodeo) - DSP Campaigns use Campaign IDs as a filter|
|`campaign_name`|Campaign Name - SP campaigns are filtered by Campaign Names, SP does not have campaign IDs|
|`line_item_id`|Line Item ID|
|`line_item_name`|Line Item Name|
|`asin`|Tracked ASIN associated with purchase related events. Other conversion events such as ATC, DPV are not available for online marketplaces.|
|`asin_promoted_or_bh`|Dimension to distinguish between Brand Halo vs Promoted ASINs|
|`amazon_com_purchases`|Ad-attributed online purchases on Amazon core (e.g. amazon.com, amazon.fr etc)|
|`whole_foods_physical_store_purchases`|Ad-attributed in-store purchases at Whole Foods physical stores|
|`whole_foods_on_amazon_purchases`|Ad-attributed online purchases on Whole Foods Market on Amazon|
|`fresh_on_amazon_purchases`|Ad-attributed online purchases from Fresh on Amazon.com|
|`amazon_fresh_physical_store_purchases`|Ad-attributed in-store purchases at Amazon Fresh physical stores|
|`amazon_com_sales`|Ad-attributed online sales on Amazon core (e.g. amazon.com, amazon.fr etc)|
|`whole_foods_physical_store_sales`|Ad-attributed in-store sales at Whole Foods physical stores|
|`whole_foods_on_amazon_sales`|Ad-attributed online sales on Whole Foods Market on Amazon|
|`fresh_on_amazon_sales`|Ad-attributed online sales from Fresh on Amazon.com|
|`amazon_fresh_physical_store_sales`|Ad-attributed in-store sales at Amazon Fresh physical stores|
|`amazon_com_ntb_purchases`|Ad-attributed online purchases from new-to-brand customers on Amazon core (e.g. amazon.com, amazon.fr etc)|
|`whole_foods_physical_store_ntb_purchases`|Ad-attributed in-store purchases from new-to-brand customers at Whole Foods physical stores|
|`whole_foods_on_amazon_ntb_purchases`|Ad-attributed online purchases from new-to-brand customers on Whole Foods Market on Amazon|
|`fresh_on_amazon_ntb_purchases`|Ad-attributed online purchases from new-to-brand customers from Fresh on Amazon.com|
|`amazon_fresh_physical_store_ntb_purchases`|Ad-attributed in-store purchases from new-to-brand customers at Amazon Fresh physical stores|
|`amazon_com_ntb_sales`|Ad-attributed online sales from new-to-brand customers on Amazon core (e.g. amazon.com, amazon.fr etc)|
|`whole_foods_physical_store_ntb_sales`|Ad-attributed in-store sales from new-to-brand customers at Whole Foods physical stores|
|`whole_foods_on_amazon_ntb_sales`|Ad-attributed online sales from new-to-brand customers on Whole Foods Market on Amazon|
|`fresh_on_amazon_ntb_sales`|Ad-attributed online sales from new-to-brand customers from Fresh on Amazon.com|
|`amazon_fresh_physical_store_ntb_sales`|Ad-attributed in-store sales from new-to-brand customers at Amazon Fresh physical stores|


### 3.3 Insights and data interpretation

Use the report generated to evaluate the top ASINs by campaign, line item, online and in physical stores across Amazon, Fresh, and Whole Foods. Since both the ASIN and line items are selected in the final output, it’s likely that the report will contain NULL values from rows representing a single user due to aggregation thresholds. These rows should be filtered out. Increasing the time window will result in fewer NULL values. Refer to [Aggregation thresholds](https://advertising.amazon.com/help/G6ZYAPTTTE54UQPP) in Support Center to learn more. 


## 4. Exploratory Query

Use this exploratory query to identify the list of eligible campaigns which can be used in this analysis. The list of campaign/campaign_id can be used as filters in the query template.



```sql
SELECT
  COALESCE(ad_product_type, 'dsp') AS ad_product_type,
  advertiser,
  campaign,
  campaign_id,
  tracked_asin AS asin,
  CASE purchase_retail_program
        WHEN 'Amazon Fresh' THEN concat('Amazon Fresh online order through ',conversion_event_marketplace_name)
        WHEN 'Whole Foods Market' THEN concat('Whole Foods Market online order through ', conversion_event_marketplace_name) 
        ELSE concat(purchase_retail_program, ' through ', conversion_event_marketplace_name)
  END AS purchase_type,
  COUNT(DISTINCT user_id) AS users_that_purchased,
  SUM(purchases) AS purchases
FROM
  amazon_attributed_events_by_conversion_time
WHERE
  advertiser IS NOT NULL
GROUP BY
  1,2,3,4,5,6
```

## 5. Query Template

The query below helps measure the impact of campaign across Amazon.com, Amazon Fresh and Whole Foods Market at ASIN level. Query can be run with or without filters. Users who wish to run the query without any filters may ignore the Apply Filters section in the query and leave the line in Purchases CTE commented. To run the query with filters, add the campaign_id (for DSP) or campaign_name(for SA) in the filter section and uncomment the line in Purchases CTE. Please follow through the query comments for details.


```
-- Instructional Query: Campaign Impact Across Amazon.com vs Amazon Fresh vs Whole Foods Market by ASIN

/*Apply Filters Section Start*/

WITH display_campaigns (campaign_id) AS (
  VALUES -- To apply display campaign filter to the query, add campaign IDs below and uncomment line in the Puchases CTE
    ('111111111111111111'),
    ('222222222222222222'),
    ('333333333333333333')
),
sa_campaigns (campaign) AS (
  VALUES -- To apply Sponsored Products or Sponsored Display campaign filter to the query, add campaign names below and uncomment line in the Puchases CTE
    ('SP_campaign_name_1'),
    ('SD_campaign_name_2'),
    ('SB_campaign_name_3')
),

/*Apply Filters Section End*/


/*Query Start*/

Purchases AS (
    SELECT
        COALESCE(ad_product_type, 'dsp')        AS ad_product_type,
        advertiser,
        campaign_id,
        campaign                                AS campaign_name,
        line_item_id,
        line_item                               AS line_item_name,
        tracked_asin                            AS ASIN,
        IF(purchases>0,'Promoted','Brand Halo') AS ASIN_Promoted_Or_BH,
        CASE purchase_retail_program
            WHEN 'Amazon Fresh'       THEN  concat('Amazon Fresh online order through ', conversion_event_marketplace_name)
            WHEN 'Whole Foods Market' THEN  concat('Whole Foods Market online order through ', conversion_event_marketplace_name)
            ELSE                            conversion_event_marketplace_name
        END                                     AS purchase_type,
        total_purchases                         AS total_purchases,
        total_product_sales                     AS total_product_sales,
        new_to_brand_total_purchases            AS total_ntb_purchases,
        new_to_brand_total_product_sales        AS total_ntb_product_sales
    FROM
        amazon_attributed_events_by_conversion_time att
    WHERE
        total_purchases > 0 
    
        --AND (campaign_id in (Select campaign_id from display_campaigns) OR campaign in (Select campaign from sa_campaigns)) /*Keep this line commented to run the query without campaign filters. To enable campaign filters, uncomment this line.*/
)

SELECT
    ad_product_type,
    advertiser,
    campaign_id,
    campaign_name,
    line_item_id,
    line_item_name,
    ASIN,
    ASIN_Promoted_Or_BH,    
    SUM( if (purchase_type = 'AMAZON.COM'                                         , total_purchases         ,0)) AS  Amazon_com_Purchases,
    SUM( if (purchase_type = 'WHOLE_FOODS_MARKET_US'                              , total_purchases         ,0)) AS  Whole_Foods_Physical_Store_Purchases,
    SUM( if (purchase_type = 'Whole Foods Market online order through AMAZON.COM' , total_purchases         ,0)) AS  Whole_Foods_on_Amazon_Purchases,
    SUM( if (purchase_type = 'Amazon Fresh online order through AMAZON.COM'       , total_purchases         ,0)) AS  Fresh_on_Amazon_Purchases,
    SUM( if (purchase_type = 'AMAZON_FRESH_STORES_US'                             , total_purchases         ,0)) AS  Amazon_Fresh_Physical_Store_Purchases,
    SUM( if (purchase_type = 'AMAZON.COM'                                         , total_product_sales     ,0)) AS  Amazon_com_Sales,
    SUM( if (purchase_type = 'WHOLE_FOODS_MARKET_US'                              , total_product_sales     ,0)) AS  Whole_Foods_Physical_Store_Sales,
    SUM( if (purchase_type = 'Whole Foods Market online order through AMAZON.COM' , total_product_sales     ,0)) AS  Whole_Foods_on_Amazon_Sales,
    SUM( if (purchase_type = 'Amazon Fresh online order through AMAZON.COM'       , total_product_sales     ,0)) AS  Fresh_on_Amazon_Sales,
    SUM( if (purchase_type = 'AMAZON_FRESH_STORES_US'                             , total_product_sales     ,0)) AS  Amazon_Fresh_Physical_Store_Sales,
    SUM( if (purchase_type = 'AMAZON.COM'                                         , total_ntb_purchases     ,0)) AS  Amazon_com_NTB_Purchases,
    SUM( if (purchase_type = 'WHOLE_FOODS_MARKET_US'                              , total_ntb_purchases     ,0)) AS  Whole_Foods_Physical_Store_NTB_Purchases,
    SUM( if (purchase_type = 'Whole Foods Market online order through AMAZON.COM' , total_ntb_purchases     ,0)) AS  Whole_Foods_on_Amazon_NTB_Purchases,
    SUM( if (purchase_type = 'Amazon Fresh online order through AMAZON.COM'       , total_ntb_purchases     ,0)) AS  Fresh_on_Amazon_NTB_Purchases,
    SUM( if (purchase_type = 'AMAZON_FRESH_STORES_US'                             , total_ntb_purchases     ,0)) AS  Amazon_Fresh_Physical_Store_NTB_Purchases,
    SUM( if (purchase_type = 'AMAZON.COM'                                         , total_ntb_product_sales ,0)) AS  Amazon_com_NTB_Sales,  
    SUM( if (purchase_type = 'WHOLE_FOODS_MARKET_US'                              , total_ntb_product_sales ,0)) AS  Whole_Foods_Physical_Store_NTB_Sales,
    SUM( if (purchase_type = 'Whole Foods Market online order through AMAZON.COM' , total_ntb_product_sales ,0)) AS  Whole_Foods_on_Amazon_NTB_Sales,
    SUM( if (purchase_type = 'Amazon Fresh online order through AMAZON.COM'       , total_ntb_product_sales ,0)) AS  Fresh_on_Amazon_NTB_Sales,
    SUM( if (purchase_type = 'AMAZON_FRESH_STORES_US'                             , total_ntb_product_sales ,0)) AS  Amazon_Fresh_Physical_Store_NTB_Sales
FROM
    Purchases
GROUP BY
    1,2,3,4,5,6,7,8
    
 
 /*Query End*/
```
