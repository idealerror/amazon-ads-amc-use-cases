## Metadata

**id:** 5a6de2fce3fa27925e7286cabbf80833b4df400735bbe0cc074f9f2c4feff761
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Learn the differences between tracked_item and track_asin conversion metrics for ASINs.
**tags:** []
**use_case_categories:** ['How to', 'ASIN analysis']
**advertiser_types:** ['On-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## 1. Introduction 

There are two broad types of conversion events associated with ASINs. 

First, some conversions associated with ASINs are related to a purchase event on Amazon; examples include 'purchases' and 'new_to_brand_purchases'. For conversion events related to a purchase, the ASIN is stored under both 'tracked_asin' and 'tracked_item'.

Second, other conversions associated with ASINs do not require a purchase event on Amazon, but require other activities on Amazon; examples include 'detail_page_view' and 'add_to_cart'. For conversion events that do not involve a purchase, the ASIN is stored under 'tracked_item' only. For advertisers who use pixel conversions, the pixel conversion is also recorded under tracked_item; it will return the UUID of the pixel, which is a long string. 



## 2. `tracked_item` returns total conversions by ASIN

This query will return all conversions by ASIN from 1) ASINs that were promoted and 2) ASINs that are from the same brands as the ASINs promoted.

The query uses `tracked_item` to select the ASIN because every conversion type has an ASIN stored under `tracked_item`. Types of conversion that might appear under `conversion_event_type_description` include: product purchased, product detail page viewed, add to shopping cart, customer reviews page, add to wedding registry and add to wishlist.

Note that if your advertiser also uses pixel conversions, 'tracked_item' will also return pixels in this query, which is a long string.



```
-- Instructional Query: ASIN conversions tracked item vs. tracked asin -- 

SELECT tracked_item AS ASIN,
       conversion_event_type_description,
       SUM(conversions) AS conversions
FROM amazon_attributed_events_by_conversion_time 

-- The filter below removes branded search conversions.
WHERE tracked_item NOT SIMILAR TO '^keyword.'
  AND tracked_item IS NOT NULL
GROUP BY 1,2 
```

## 3. `tracked_item` returns detail page views and add-to-cart conversions by ASIN

This query provides a view of promoted vs. total conversions. The example only includes four metrics: detail page view, total detail page view, add to cart and total add to cart. However, you can modify this query to include other metrics such as new-to-brand, or purchase info. Refer to the `amazon_attributed_events_by_conversion_time` and `amazon_attributed_events_by_traffic_time` under schema explorer for a complete list of metrics. 


```
-- Instructional Query: ASIN conversions tracked item vs. tracked asin -- 

SELECT tracked_item AS ASIN,
       
       -- detail page views from promoted ASINs
       sum(detail_page_view) AS detail_page_view,
    
       -- detail page views from promoted ASINs + ASINs from the same brand as the ASINs promoted.
       sum(total_detail_page_view) AS total_detail_page_view,

       -- The number of times a promoted ASIN is added to a customer''s cart.--
       sum(add_to_cart) AS add_to_cart,
       
       -- The number of times promoted ASINs + ASINs from the same brand are added to a customer''s cart.--
       sum(total_add_to_cart) AS total_add_to_cart 
FROM amazon_attributed_events_by_conversion_time 

-- The filter below removes branded search conversions.--
WHERE tracked_item NOT SIMILAR TO '^keyword.'
  AND tracked_item IS NOT NULL
GROUP BY 1
```

## 4. tracked_asin for conversion events related to a purchase on Amazon

The query below returns conversion events that are related to a purchase on Amazon; examples include `purchases` and `new_to_brand_purchases`. You don't need to remove ad-attributed branded searches from the result below because `tracked_asin` only includes conversions that are related to a purchase on Amazon. Refer to the `amazon_attributed_events_by_conversion_time` and `amazon_attributed_events_by_traffic_time` tables under schema explorer for a complete list of purchase related conversion metrics.


```
-- Instructional Query: ASIN conversions tracked item vs. tracked asin -- 

SELECT tracked_asin,
       sum(purchases) AS purchases,
       sum(total_purchases) AS total_purchases,
       sum(new_to_brand_purchases) AS new_to_brand_purchases,
       sum(new_to_brand_total_purchases) AS new_to_brand_total_purchases
FROM amazon_attributed_events_by_conversion_time
WHERE tracked_asin IS NOT NULL
GROUP BY 1
```
