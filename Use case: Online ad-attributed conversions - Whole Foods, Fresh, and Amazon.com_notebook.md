## Metadata

**id:** be6b3da8e05af20de0ac494f855b445f3e206a3615df3829e3da4d33693cf9e6
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Measure online purchases across Amazon Fresh, Whole Foods Market, and Amazon.com.
**tags:** []
**use_case_categories:** ['How to', 'Grocery analysis']
**advertiser_types:** ['On-Amazon conversions', 'Physical stores']
**ad_product_types:** None
**shared_with_accounts:** None

---

## Notice:

As of August 31, 2022, a more detalied version of this IQ has been made available in AMC. Advertisers who wish to utilize the differences described in the "New IQ key differences" section may use the new version: [Amazon.com, Amazon Fresh, and Whole Foods Market - Online and In-Store Ad-Attributed Conversions by ASIN](/marketing-cloud/instructional-queries/7c7c2de7bc59865d93f51bc9d4026c43a527f66a39f50e4dfff4f263b5555caf).
#### New IQ key differences:
* Improved output data structuring: Users can now see the breakdown of purchases for ASINs in a single row.
* Option to add filters to the query
* Addition of brand-halo conversions
* Addition of new-to-brand (NTB) conversions


## 1. Introduction
### 1.1 Purpose 
Measure your online purchases across 1) [Amazon Fresh](https://www.amazon.com/alm/storefront?almBrandId=QW1hem9uIEZyZXNo&ref_=nav_em__afs_aaf_0_2_11_2), 2) [Whole Foods Market](https://www.amazon.com/alm/storefront?almBrandId=VUZHIFdob2xlIEZvb2Rz&ref_=nav_em_navm_whole_foods_storefront_0_2_27_2), and 3) [Amazon.com](https://www.amazon.com/). This instructional query allows CPG advertisers to break out online sales on Whole Foods and Fresh from Amazon.com. Since Advertisers tend to have separate budget allocations and strategies for promoting online vs physical store purchases, this new column will provide the sales granularity needed to measure the impact of your advertising. This breakout of Fresh and Whole Foods orders on Amazon.com is only available in AMC and not in the Amazon DSP UI or the Sponsored Ads console. 


### 1.2 Requirements 
To use this query template, advertisers must have had DSP or Sponsored Ads campaigns running over the time period queried with ASINs that were sold on Amazon.com through Whole Foods or Fresh. This breakout is only available in the US. 


## 2. Data Query Instructions 

### 2.1 Tables Used
- **amazon_attributed_events_by_conversions_time**: Due to the 14-day attribution window, wait two weeks past the end of the campaign to run this query to capture all conversions. 


### 2.2 Data Explanation
The column conversion_event_marketplace_name combined with the new column purchase_retail_program allows you to measure online purchases that happened both online and in-store. 


* When the field `conversion_event_marketplace_name` is 'AMAZON.COM' and when the `purchase_retail_program` is 'Amazon Fresh', the purchase is a Fresh order that was placed on Amazon.com. 
* When the field `conversion_event_marketplace_name` is 'AMAZON.COM' and when the `purchase_retail_program` is 'Whole Foods Market', the purchase is a Whole Foods order that was placed on Amazon.com. 
* When the field `conversion_event_marketplace_name` is 'AMAZON.COM' and when the `purchase_retail_program` is NULL, the purchase is an Amazon.com order that was not placed on Fresh or Whole Foods.
* For completeness, the query will also return other types of orders, such as in-store orders and online orders from other online marketplaces other than AMAZON.COM. For a deep dive on in-store orders, view the Instructional Query [How to Measure In-Store Conversions](https://advertising.amazon.com/marketing-cloud/instructional-queries/17bfafa195ee45619bb19cfbe066411f8b8bfa10e91ce2d96bdbea92e85dbb81). 




## 3. Query Template



```
-- Instructional Query: How to Measure Online Ad-attributed Purchases across Whole Foods Market, Fresh, and Amazon.com --
    
SELECT
    coalesce(ad_product_type, 'dsp') as ad_product_type,
    advertiser,
    campaign,
    tracked_asin as asin,
    case
    when purchase_retail_program = 'Amazon Fresh' then concat('Amazon Fresh online order through ', conversion_event_marketplace_name)
    when purchase_retail_program = 'Whole Foods Market' then concat('Whole Foods Market online order through ', conversion_event_marketplace_name)
   
    -- the statement below will caputure 1) orders from AMAZON.COM that are not from Fresh or Whole Foods, 2) orders from another online marketplace, such as AMAZON.CO.UK, or 2) an in-store marketplace, such as WHOLE_FOODS_MARKET_US or AMAZON_FRESH_STORES_US
    when purchase_retail_program IS NULL THEN conversion_event_marketplace_name 
    
    -- the statement below will catch any other combinations. 
    else concat(purchase_retail_program, ' through ', conversion_event_marketplace_name) 
    end as purchase_type,
    count(distinct user_id) as users_that_purchased,
    sum(purchases) as purchases
FROM amazon_attributed_events_by_conversion_time
WHERE purchases > 0
GROUP BY 1,2,3,4,5
;
```

## 4. Example Query Results
Example results from an advertiser who only has sales within the Amazon.com marketplace. 



|ad_product_type|advertiser|campaign|asin|conversion_marketplace_and_retail_program|users_that_purchased|purchases|
|---|---|---|---|---|---|---|
|sponsored_products|Nutrition Co|Nutrition_Bars_Q4_Branded_Keywords|ASIN1234|AMAZON.COM|1245|1587|
|sponsored_products|Nutrition Co|Nutrition_Bars_Q4_Branded_Keywords|ASIN1234|Amazon Fresh online order through AMAZON.COM|153|174|
|sponsored_brands|Nutrition Co|Nutrition_Brands_Q4_Awareness|ASIN7890|AMAZON.COM|180|196|
|sponsored_display|Nutrition Co|Health_Products_Q4_Sponsored_Display|ASIN3456|Amazon Fresh online order through AMAZON.COM|356|380|
|dsp|Nutrition Co|Health_Products_Q4_Display|ASIN5678|AMAZON.COM|3685|4523|
|dsp|Nutrition Co|Health_Products_Q4_Display|ASIN6789|Amazon Fresh online order through AMAZON.COM|542|689|
|dsp|Nutrition Co|Health_Products_Q4_Display|ASIN6789|Whole Foods Market online order through AMAZON.COM|828|1025|

