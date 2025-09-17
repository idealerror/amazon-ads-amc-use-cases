## Metadata

**id:** e506a3b92ab299d07cdd9267f9071d9cc54c7cda2d340e74ab5c8a3f2a9ce95c
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Analyze purchasing patterns to identify ASINs commonly purchased together.
**tags:** []
**use_case_categories:** ['Performance deep dive', 'ASIN analysis']
**advertiser_types:** ['On-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## Introduction 

### Purpose 

Cross-purchasing refers to when customers purchase multiple related products. If you are running sponsored ads campaigns, you can analyze cross-purchase patterns to identify the most commonly purchased ASIN pairs, the time gap between their purchases, and segment the analysis by user cohorts (for example, new vs. repeat buyers). With these insights, you can understand how your customers discover and buy complementary items, as well as reveal cross-sell opportunities. 

By leveraging these cross-purchase insights, you can implement:

* Product assortment and bundling recommendations based on real customer purchase affinities
* Tailored marketing campaigns highlighting complementary products
* Inventory forecasting and planning aligned with cross-purchase patterns
* Advertising bid optimization on ASINs with high cross-purchase potential


### Requirements

This query relies on ASIN conversions on Amazon. It is therefore limited to advertisers with on-Amazon conversions. 

To use this IQ, advertisers who sell products on Amazon must invest in sponsored ads product types: Sponsored Brands, Sponsored Products, Sponsored Display or Sponsored TV. To gain comprehensive cross-purchase insights, invest in multiple sponsored ads product types and track conversions across a diverse range of ASINs. 

**Tip:** While analysis can be done with a single ad type or limited ASINs, the insights become more valuable when combining data from multiple sponsored ads tactics and a broader set of products. This provides a holistic view of how customers discover and purchase complementary items through different touchpoints.

## Data query instructions 

### Data returned

The query returns the most commonly purchased ASIN pairs (lead and follow-up ASINs), along with the number of cross-purchased users, total purchases for each ASIN, user reach, and the average number of days between these cross-purchases. 

The data is also segmented by user cohort of new vs. repeat customers. By leveraging these metrics, you can identify prominent product affinities based on real customer journeys driven by sponsored ads, quantify the cross-purchase behavior influenced by your advertising efforts, and gauge the time windows for marketing complementary products. 


### Tables used 

[amazon_attributed_events_by_conversion_time](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/amazon_attributed_events_by_conversion_time): This table contains ad-attributed conversions based on Amazon’s 14-day last touch attribution rule. Wait two weeks past the end of the campaign to run this query to capture all conversions. 
 * Key dimensions used are `user_id`, `tracked_asin`, `ad_product_type`, and `conversion_event_date` to identify purchases influenced by sponsored ads campaigns.
 * The `ad_product_type` dimension is used to differentiate between different sponsored ads products such as Sponsored Brands, Sponsored Products, Sponsored Display, and Sponsored Television.


## Data interpretation instructions 

### Example query results

**This data is for instructional purposes only. Your results will differ.**


| lead_asin | follow_up_asin | lead_ad_product_type | follow_up_ad_product_type | cross_purchased_users | user_cohort     | lead_asin_purchases | lead_asin_user_reach | follow_up_asin_purchases | follow_up_asin_user_reach | avg_days_between_purchases | cross_purchase_rank | 
|-----------|----------------|----------------------|---------------------------|-----------------------|-----------------|---------------------|----------------------|--------------------------|---------------------------|----------------------------|---------------------| 
| ASIN1234  | ASIN7627       | sponsored_products   | sponsored_products        | 558                   | New Customer    | 9642                | 8904                 | 2306                     | 2068                      | 31.24                      | 1                   | 
| ASIN9898  | ASIN2253       | sponsored_display    | sponsored_television      | 417                   | New Customer    | 5546                | 34081                | 6106                     | 23135                     | 17.24                      | 2                   | 
| ASIN9876  | ASIN4326       | sponsored_products   | sponsored_display         | 314                   | New Customer    | 11980               | 21627                | 3024                     | 12034                     | 37.87                      | 3                   | 
| ASIN7620  | ASIN4579       | sponsored_television | sponsored_products        | 298                   | New Customer    | 10390               | 7930                 | 9839                     | 24038                     | 16.98                      | 4                   | 
| ASIN5682  | ASIN1839       | sponsored_brands     | sponsored_products        | 243                   | New Customer    | 8223                | 19065                | 6964                     | 14536                     | 20.78                      | 5                   | 
| ASIN1234  | ASIN2283       | sponsored_products   | sponsored_products        | 202                   | Repeat Customer | 9642                | 8904                 | 464                      | 9407                      | 31.62                      | 6                   | 
| ASIN7620  | ASIN5682       | sponsored_television | sponsored_brands          | 179                   | Repeat Customer | 10390               | 7930                 | 8223                     | 19065                     | 42.56                      | 7                   | 
| ASIN1234  | ASIN8970       | sponsored_products   | sponsored_products        | 158                   | Repeat Customer | 9642                | 8904                 | 1460                     | 1388                      | 52.4                       | 8                   | 
| ASIN5682  | ASIN2492       | sponsored_brands     | sponsored_display         | 129                   | New Customer    | 8223                | 19065                | 3666                     | 9260                      | 21                         | 9                   | 
| ASIN1234  | ASIN7970       | sponsored_products   | sponsored_brands          | 99                    | New Customer    | 9642                | 8904                 | 2570                     | 8289                      | 46.68                      | 10                  | 
| ASIN2492  | ASIN9876       | sponsored_display    | sponsored_products        | 26                    | Repeat Customer | 3666                | 9260                 | 11980                    | 21627                     | 8                          | 11                  | 



### Metrics defined

| Dimensions and metrics     | Definition                                                                                                            |
|----------------------------|-----------------------------------------------------------------------------------------------------------------------|
| `lead_asin`                  | The initial product ASIN that was purchased.                                                                          |
| `follow_up_asin`             | The ASIN that was purchased after the lead ASIN.                                                                      |
| `lead_ad_product_type`       | The sponsored ads product type (Brands, Products, Display, Television) that influenced the purchase of the lead ASIN. |
| `follow_up_ad_product_type`  | The sponsored ads product type that influenced the purchase of the follow-up ASIN.                                    |
| `cross_purchased_users`      | The distinct number of users who purchased both the lead and follow-up ASINs.                                         |
| `user_cohort`                | Segment identifying if the user was a new or repeat customer based on their purchase history.                         |
| `lead_asin_purchases`        | The total purchases for the lead ASIN (initial product purchased).                                                    |
| `follow_up_asin_purchases`   | The total purchases for the follow-up ASIN (product purchased after the lead ASIN).                                   |
| `lead_asin_user_reach`       | The distinct number of users who purchased the lead ASIN.                                                             |
| `follow_up_asin_user_reach`  | The distinct number of users who purchased the follow-up ASIN.                                                        |
| `avg_days_between_purchases` | The average number of days between when users purchased the lead and follow-up ASINs.                                 |
| `cross_purchase_rank`        | The rank assigned to each ASIN pair based on the cross_purchased_users metric in descending order.                    |




### Insights and data interpretation

* **What are the top 3 ASIN pairs that customers purchase together?** 

    The top 3 ASIN pairs ranked by the highest number of cross-purchased users are ASIN1234 and ASIN7627 with 558 users, ASIN9898 and ASIN2253 with 417 users, and ASIN9876 and ASIN4326 with 314 users. These product combinations represent the most prominent cross-purchase affinities exhibited by customers. 

    **Action:** For the top cross-purchased ASIN pairs, implement product bundling and marketing campaigns highlighting these complementary items together.

* **For the ASIN pair ranked #1 in cross-purchased users, what was the average number of days between purchases?**

    For the top ranking ASIN pair (ASIN1234 and ASIN7627), the average number of days between when a customer purchased the lead ASIN (ASIN1234) and the follow-up ASIN (ASIN7627) was 31.24 days (`avg_days_between_purchases`).

    **Action:** For top cross-purchased ASIN pairs, set up retargeting campaigns on the follow-up ASIN timed to go live around the `avg_days_between_purchases` window after buyers purchase the lead ASIN. This improves ad relevance and likelihood of conversion by aligning to the typical customer journey.


* **What percentage of cross-purchased users were identified as new customers?**

    A high percentage (78.5% of the total 2,623 cross-purchased users) were identified as "New Customer" in the `user_cohort` dimension. This highlights the importance of attracting new buyers, as a significant portion of valuable cross-purchase behavior is driven by first-time customers. 

    **Action:** Tailor assortment and inventory strategy for new vs repeat buyers based on differing cross-purchase behavior.

* **Which sponsored ads product type had the highest total `lead_asin_purchases` across all ASIN pairs?**

    When analyzing by ad product type, Sponsored Products campaigns drove the highest total of 21,622 lead ASIN purchases across all cross-purchased ASIN pairs. This outpaced Sponsored Display at 9,212, Sponsored Brands at 8,223, and Sponsored Television at 10,390 total lead purchases. This shows the effectiveness of Sponsored Products ads for influencing the initial purchases that kickstart cross-purchase sequences. 

    **Action:** Prioritize ad investment in the channels or products driving the most valuable cross-purchases to maximize customer lifetime value.

## Queries

### Exploratory query

Use this exploratory query to view the list of `ad_product_type` and ASIN purchases in your conversion data. This can help you decide which ad products or ASIN filters you may want to apply to the main ASIN cross-purchase analysis query.



```
-- Exploratory Query: Sponsored ads ASIN cross-purchase --
SELECT
  campaign_id_string,
  ad_product_type,
  tracked_asin,
  SUM(purchases) AS total_purchases
FROM amazon_attributed_events_by_conversion_time
WHERE ad_product_type IN ('sponsored_products','sponsored_brands','sponsored_display','sponsored_television')
GROUP BY 1, 2, 3
```

### Query template: Sponsored ads ASIN cross-purchase



```
-- Instructional Query: Sponsored ads ASIN cross-purchase -- 
/*
 ------- Customization Instructions -------
 1) We recommend running the exploratory query first to analyze the sponsored ads signals and identify campaign ID string to filter the results
 2) REQUIRED UPDATE [1 OF 1]: Update the campaign_id_string custom parameter for SA campaigns
 */

-- Filter and select relevant data from the main attribution events table
WITH relevant_attributed_events AS
(
    SELECT  
        user_id,
        tracked_asin,
        ad_product_type,
        conversion_event_date,
        new_to_brand,
        purchases
    FROM amazon_attributed_events_by_conversion_time
    WHERE 
        ARRAY_CONTAINS(CUSTOM_PARAMETER('campaign_id_string'), campaign_id_string)
        AND 
        ad_product_type IN (
            'sponsored_products',
            'sponsored_brands',
            'sponsored_display',
            'sponsored_television'
            )
),

sponsored_ads_conversions AS
(
    -- Capture user, ASIN, ad product type, and new_to_brand from 
    -- the relevant_attributed_events cte    
    SELECT  
        user_id,
        tracked_asin,
        ad_product_type,
        conversion_event_date,
        new_to_brand
    FROM relevant_attributed_events
    WHERE purchases > 0
    AND user_id IS NOT NULL 
), 

asin_metrics AS
(
    -- Calculate ASIN-level metrics (purchases AND user reach)from 
    -- the relevant_attributed_events cte    
    SELECT  
        tracked_asin,
        SUM(purchases) AS asin_purchases,
        COUNT(DISTINCT user_id) AS user_reach
    FROM relevant_attributed_events
    GROUP BY  tracked_asin
), 

cross_purchase_data AS
(
    -- Join the sponsored ads conversions and ASIN metrics to get the cross-purchase data
    SELECT  
        s1.tracked_asin AS lead_asin,
        s2.tracked_asin AS follow_up_asin, 
        s1.ad_product_type AS lead_ad_product_type, 
        s2.ad_product_type AS follow_up_ad_product_type,
        am1.asin_purchases AS lead_asin_purchases,
        am1.user_reach AS lead_asin_user_reach,
        am2.asin_purchases AS follow_up_asin_purchases,
        am2.user_reach AS follow_up_asin_user_reach,
        COUNT(DISTINCT s1.user_id) AS cross_purchased_users,
        AVG(SECONDS_BETWEEN(s1.conversion_event_date,s2.conversion_event_date)/86400) 
            AS avg_days_between_purchases,
        CASE WHEN MAX(s1.new_to_brand) = 1 THEN 'New Customer'  
            ELSE 'Repeat Customer' END AS user_cohort
    FROM sponsored_ads_conversions s1
    INNER JOIN sponsored_ads_conversions s2
    ON s1.user_id = s2.user_id AND s2.conversion_event_date > s1.conversion_event_date
    LEFT JOIN asin_metrics am1
    ON s1.tracked_asin = am1.tracked_asin
    LEFT JOIN asin_metrics am2
    ON s2.tracked_asin = am2.tracked_asin
    WHERE s1.tracked_asin <> s2.tracked_asin
    GROUP BY 1,2,3,4,5,6,7,8
)
-- Final query that selects the cross-purchase data and ranks the results
SELECT  
    lead_asin,
    follow_up_asin,
    lead_ad_product_type,
    follow_up_ad_product_type,
    cross_purchased_users,
    user_cohort,
    lead_asin_purchases,
    lead_asin_user_reach,
    follow_up_asin_purchases,
    follow_up_asin_user_reach,
    avg_days_between_purchases,
    DENSE_RANK() OVER (ORDER BY  cross_purchased_users DESC) AS cross_purchase_rank
FROM cross_purchase_data

```
