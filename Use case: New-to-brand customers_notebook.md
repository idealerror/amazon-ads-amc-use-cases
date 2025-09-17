## Metadata

**id:** 1d572ebe8ca7baab0d45003ced0eb34114b565eca699a17dc6463371e0e2dbc2
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Determine how effective your campaigns are with reaching new-to-brand customers.
**tags:** []
**use_case_categories:** ['New-to-brand']
**advertiser_types:** ['On-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## Introduction

### Purpose 
Customers are considered to be new-to-brand (NTB) if they purchase from your brand for the first time during the previous 365 day period on Amazon. This instructional query (IQ) demonstrates the impact of campaigns in terms of reaching NTB customers. Specifically, it answers: Of all the customers who were exposed to ads and purchased, how many and what percentage of them are NTB customers? 

### Requirements 
To use this query, your ASINs must be tracked to campaigns. Campaigns used in this query should have the goal of reaching both new and existing customers. Campaigns should target both new and existing customers, instead of one customer type exclusively, for example only existing customers or only customers who never purchased. 


## Data query instructions 

### Data returned
The query from this IQ returns the count of all customers who made a purchase, the count of NTB customers and percentage of NTB customers. 

### Tables used 
[amazon_attributed_events_by_conversion_time](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/amazon_attributed_events_by_conversion_time) : This table includes conversion types such as detail page views, add to cart and purchases. Use the filter `where purchases > 0` to include customers who have made at least one purchase. Since this query uses the `conversion_time` table, extend your query window two weeks past the end of the campaign to capture all conversions. 

### Query template 

You can add additional filters to the query if you want to look at specific campaigns or ad product.  This query uses filter `where purchases > 0` to ensure you are only pulling purchase conversions and not all conversions. Other types of conversions include detail page views, add-to-cart, pixel conversions, etc. You can remove this filter, if necessary. Most advertisers are interested in understanding the percentage of users who were NTB out of all users who purchased. Without the filter `where purchases > 0`, the query below will help you to understand the percentage of users who were NTB out of all users with any conversion, including non-purchase conversions. 


```
-- Instructional Query: New-to-brand Customers
-- AD PRODUCTS: Sponsored ads and/or Amazon DSP

WITH users AS (
  SELECT
    advertiser,
    ad_product_type,
    campaign,
    user_id,
    (IF(new_to_brand = TRUE, 1, 0)) AS ntb_users
  FROM
    amazon_attributed_events_by_conversion_time
  WHERE
    purchases > 0
  GROUP BY
    advertiser,
    ad_product_type,
    campaign,
    user_id,
    new_to_brand
)
SELECT
  advertiser,
  CASE
    WHEN ad_product_type IS NULL THEN 'DSP'
    ELSE ad_product_type
  END AS ad_product_type,
  campaign,
  COUNT(DISTINCT user_id) AS all_users_with_purchase,
  SUM(ntb_users) AS ntb_users,
  SUM(ntb_users) / COUNT(DISTINCT user_id) * 100 AS ntb_percentage
FROM
  users
GROUP BY
  advertiser,
  ad_product_type,
  campaign
```

### Example query results 
| **advertiser** | **ad_product_type** | **campaign**              | **all_users_with_purchase** | **ntb_users** | **ntb_percentage (%)** |
|----------------|---------------------|---------------------------|-----------------------------|---------------|------------------------|
| Blue Athletics | sponsored_products  | blue_athletics_BAU_Q12021 | 107                         | 37            | 34.58                  |
| Blue Athletics | sponsored_products  | blue_athletics_Acq_Q12021 | 864                         | 614           | 71.06                  |
| Blue Casual    | sponsored_display   | blue_casual_BAU_Q12021    | 132                         | 50            | 37.88                  |
| Blue Casual    | DSP                 | blue_casual_Acq_Q12021    | 1303                        | 481           | 36.91                  |


### Metrics defined 
| **metric**                      | **definition**                                  |
| --------------------------- | ------------------------------------------- |
| all\_users\_that\_purchased | Number of distinct users that purchased.     |
| ntb\_users                  | Number of distinct NTB users that purchased. |
| ntb\_percentage             | Percentage of users who were NTB.            |


## Results Interpretation (Purchase Rate) 

The example query results above show that **Blue Athletics Acq** campaign attracted the highest percentage of NTB out of all four campaigns. 

For Blue Athletics, the **blue_athletics_Acq_Q12021** campaign is `2x (71.06% / 34.58%)` more likely to drive NTB users compared to the blue_athletics_BAU_Q12021 campaign. 

For Blue Casual, **blue_casual_Acq_Q12021** attracted much more NTB users (481 vs 50), but **blue_casual_BAU_Q12021** drove slightly higher NTB percentage: `1% ((37.88% - 36.91%) / 36.91%)`


