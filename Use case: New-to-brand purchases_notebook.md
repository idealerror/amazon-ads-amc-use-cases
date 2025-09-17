## Metadata

**id:** 246acd1e54dc46d5c1216bee80337856b97f548785163579894e46fa9627319e
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Calculate the percentage of ad-attributed purchases that were new-to-brand vs repeat purchases.
**tags:** []
**use_case_categories:** ['New-to-brand']
**advertiser_types:** ['On-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## Introduction 

### Purpose
Purchases are considered new-to-brand (NTB) if a customer purchased a specific ASIN for the first time during the previous 365 day period on Amazon. 

This instructional query (IQ) demonstrates the impact of campaigns in terms of generating new-to-brand (NTB) purchases. Specifically, it answers: of all the ad-attributed purchases, how much and what percentage of them are NTB purchases and repeat purchases? What is the impact of campaigns in term of generating NTB purchases? 

### Requirements
To use this query, your ASINs must be tracked to campaigns. Campaigns used in this query should preferably have the goal of reaching both new and existing customers. 

## Data query instructions

### Data returned
The query from this IQ returns the total purchases, sales and the percentage of purchases for both NTB and repeat purchases by campaign and advertiser.   

### Tables used 
- [**amazon_attributed_events_by_conversion_time**](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/amazon_attributed_events_by_conversion_time) : Due to the 14-day attribution window, please wait two weeks past the end of the campaign to run this query in order to capture all conversions. 

## Query templates 

Two templates are provided:
- New-to-brand purchases for sponsored ads
- New-to-brand purchases for Amazon DSP and/or sponsored ads

You can customize the queries in several ways:
- Uncomment the campaign filter to return results that include only specific campaigns.
- The queries below use the total purchases metrics which include purchases for promoted products as well as products from the same brands as the products tracked in the campaign (Total purchases = Purchases + Purchases Brand Halo). If you want to view purchases for promoted products only, update all the `total_purchases` to `purchases`. For example, change `new_to_brand_total_purchases` to `new_to_brand_purchases`.

### Query template: New-to-brand purchases for sponsored ads only


```
-- Instructional Query: New-to-brand Purchases --
-- AD PRODUCTS: Sponsored ads

WITH sa_campaigns (campaign_id_string) AS (
  VALUES
    -- Optional update: To apply sponsored ads campaign filters to the query, add campaign ID string values below and uncomment lines in the impressions cte and in the final select statement. Ignore section to skip filters.
    ('SP Campaign 1'),
    ('SP Campaign 2'),
    ('SD Campaign 1'),
    ('SD Campaign 2'),
    ('SB Campaign 1'),
    ('SB Campaign 2')
)
SELECT
  advertiser_id,
  advertiser,
  campaign,
  campaign_id_string,
  SUM(new_to_brand_total_purchases) AS new_to_brand_purchases,
  SUM(total_purchases) - SUM(new_to_brand_total_purchases) AS non_new_to_brand_purchases,
  (
    SUM(new_to_brand_total_purchases) / SUM(total_purchases)
  ) * 100 AS new_to_brand_purchases_percentage,
  SUM(new_to_brand_total_product_sales) AS new_to_brand_product_sales,
  SUM(total_product_sales) - SUM(new_to_brand_total_product_sales) AS non_new_to_brand_product_sales,
  (
    SUM(new_to_brand_total_product_sales) / SUM(total_product_sales)
  ) * 100 AS new_to_brand_product_sales_percentage
FROM
  amazon_attributed_events_by_conversion_time
WHERE
  total_purchases > 0
  AND user_id IS NOT NULL -- Optional update: uncomment this line to enable SA filters. Leave the line commented to skip filters
  /*AND campaign_id_string IN (SELECT campaign_id_string FROM sa_campaigns)*/
GROUP BY
  advertiser_id,
  advertiser,
  campaign,
  campaign_id_string
```

### Query template: New-to-brand purchases for Amazon DSP and/or sponsored ads


```
-- Instructional Query: New-to-brand Purchases --
-- AD PRODUCTS: Sponsored ads and/or Amazon DSP

WITH dsp_campaigns (campaign_id_string) AS (
  VALUES
    -- Optional update: To apply display campaign filter to the query, add campaign IDs below and uncomment lines in the impressions cte and in the final select statement. Ignore section to skip filters.
   ('11111111111111111'),
   ('22222222222222222'),
   ('33333333333333333')
),
sa_campaigns (campaign_id_string) AS (
  VALUES
    -- Optional update: To apply sponsored ads campaign filter to the query, add campaign names below and uncomment lines in the impressions cte and in the final select statement. Ignore section to skip filters.
    ('SP Campaign 1'),
    ('SP Campaign 2'),
    ('SD Campaign 1'),
    ('SD Campaign 2'),
    ('SB Campaign 1'),
    ('SB Campaign 2')
)
SELECT
  advertiser_id,
  advertiser,
  campaign_id_string,
  campaign,
  SUM(new_to_brand_total_purchases) AS new_to_brand_purchases,
  SUM(total_purchases) - SUM(new_to_brand_total_purchases) AS non_new_to_brand_purchases,
  (
    SUM(new_to_brand_total_purchases) / SUM(total_purchases)
  ) * 100 AS new_to_brand_purchases_percentage,
  SUM(new_to_brand_total_product_sales) AS new_to_brand_product_sales,
  SUM(total_product_sales) - SUM(new_to_brand_total_product_sales) AS non_new_to_brand_product_sales,
  (
    SUM(new_to_brand_total_product_sales) / SUM(total_product_sales)
  ) * 100 AS new_to_brand_product_sales_percentage
FROM
  amazon_attributed_events_by_conversion_time
WHERE
  total_purchases > 0
  AND user_id IS NOT NULL -- Optional update: uncomment this line to enable DSP & SA filters. Leave the line commented to skip filters
  /*AND (campaign_id_string IN (SELECT campaign_id_string FROM dsp_campaigns) OR campaign_id_string IN (SELECT campaign_id_string FROM sa_campaigns))*/
GROUP BY
  advertiser_id,
  advertiser,
  campaign_id_string,
  campaign
```

## Data Interpretation Instructions
### Example Query Results 

This data is for instructional purposes only. Your results will differ. 


| advertiser_id | advertiser            | campaign_id_string | campaign                                          | new_to_brand_purchases | non_new_to_brand_purchases | new_to_brand_purchases_percentage | new_to_brand_product_sales | non_new_to_brand_product_sales | new_to_brand_product_sales_percentage |
|---------------|-----------------------|--------------------|---------------------------------------------------|------------------------|----------------------------|-----------------------------------|----------------------------|--------------------------------|---------------------------------------|
| 8059118170401 | Gordon's Chocolatier  | 2666157226463      | Gordon's Chocolatier_test_campaign_2666157226463  | 3                      | 27                         | 10                                | 267.95                     | 1385.64                        | 16.2041                               |
| 8059118170401 | Gordon's Chocolatier  | 1367807174390      | Gordon's Chocolatier_test_campaign_1367807174390  | 25                     | 133                        | 15.82278                          | 1269.74                    | 5987.11                        | 17.4971                               |
| 909633910201  | Super Power Batteries | 5509330292276      | Super Power Batteries_test_campaign_5509330292276 | 42                     | 233                        | 15.27273                          | 2648.05                    | 10026.67                       | 20.8924                               |
| 7760300120001 | Jack & Jill           | 2130632581505      | Jack & Jill_test_campaign_2130632581505           | 6                      | 19                         | 24                                | 270.47                     | 670.9                          | 28.7315                               |
| 4002318990401 | Iris                  | 1347792328627      | Iris_test_campaign_1347792328627                  | 36                     | 200                        | 15.25424                          | 2075.09                    | 7900.54                        | 20.8016                               |
| 909633910201  | Super Power Batteries | 7958409542200      | Super Power Batteries_test_campaign_7958409542200 | 8                      | 41                         | 16.32653                          | 403.87                     | 1616.1                         | 19.9939                               |



### Metrics Defined 

| column name                                | definition                                                                                                                                            |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| new\_to\_brand\_purchases                  | Number of total new\_to\_brand purchases, including both promoted products and products from the same brands as the products tracked in the campaign. |
| non\_new\_to\_brand\_purchases             | Number of total repeat purchases, including both promoted products and products from the same brands as the products tracked in the campaign.         |
| new\_to\_brand\_purchases\_percentage      | Percentage of new\_to\_brand total purchases, including both promoted products and products from the same brands as the products tracked in the campaign.                                                                                                               |
| new\_to\_brand\_product\_sales             | Total sales of new\_to\_brand products, including both promoted products and products from the same brands as the products tracked in the campaign.   |
| non\_new\_to\_brand\_product\_sales        | Total sales of repeat products, including both promoted products and products from the same brands as the products tracked in the campaign.           |
| new\_to\_brand\_product\_sales\_percentage | Percentage of new\_to\_brand product total sales, including both promoted products and products from the same brands as the products tracked in the campaign.                                                                                                          |


### Insights and Data Interpretation
The data above shows that `Always-On_SB_Amazon_2022` (SB) campaign attracted the highest percentage of new-to-brand purchases and sales. 

For Organique, the SB campaign is 1.88x = 57.96% / 30.84% more likely to drive new-to-brand purchases compared to the `Always-On_Display_Amazon 2022` (Always On Display) campaign. 

On the other hand, `Mobile - Subscribe and Save Sponsorship Package - SOV` campaign was more likely to drive repeat purchases from customers. 

