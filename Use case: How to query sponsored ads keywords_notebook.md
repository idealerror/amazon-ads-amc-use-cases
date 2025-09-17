## Metadata

**id:** d0318a42-d23b-4b8d-a8fb-eb5c46661b2e
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Understand how customers search for your products on Amazon, and identify new keywords to target.
**tags:** []
**use_case_categories:** ['Branded searches and keywords', 'How to']
**advertiser_types:** ['On-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## 1. Introduction

### 1.1 Purpose 
[Keyword targeting](https://advertising.amazon.com/library/videos/keyword-targeting-sponsored-ads) is an important advertising tactic used by many sponsored ads advertisers. Search terms, also referred to as shopping queries, are words or phrases that customers enter into the Amazon search bar when shopping for products. Keywords are word combinations and phrases that sponsored ads advertisers bid on to match those shopping queries. Keywords, search terms, and their corresponding match types are available in AMC for advertisers who invest in Sponsored Brands or Sponsored Products. To learn more about keyword targeting, register for the on-demand webinar [Introduction to keywords and sponsored ads](https://advertising.amazon.com/library/webinars/keywords-introduction/?ref_=a20m_us_lbr_vd_rfy_3_lbr_wb).
 
Keywords and search terms in AMC allow you to gain a deeper understanding of how customers are searching for your products on Amazon and identify new keywords to target. The two introductory queries in Section 2 provide an overview of the structure of the keywords and search terms, giving you a foundation to write custom queries. Section 3 focuses on attributed purchases and provides two sets of search terms: the first and the attributed search term. This report will help you to identify search terms that did not receive credit for the attributed conversion, but played an assisting role.


### 1.2 Ad-attributed branded searches disambiguation

Note that sponsored ads keywords and search terms are different from [DSP ad-attributed branded searches](https://advertising.amazon.com/marketing-cloud/instructional-queries/41be38d47bfd6f47a73ccaf5636b0c95816e1ed3c8ba9c29c884b64c7e0ad4db), and AMC does not yet have ad-attributed branded searches for [Sponsored Brands](https://advertising.amazon.com/API/docs/en-us/info/release-notes#branded-searches-for-sponsored-brands-are-now-available-in-all-regions) and [Sponsored Display](https://advertising.amazon.com/API/docs/en-us/info/release-notes#to-help-advertisers-understand-the-impact-of-their-reach-and-awareness-campaigns-sponsored-display-launches-branded-searches-metrics-in-the-reporting-api). Sponsored ads keywords and search terms are associated with traffic events, and they are used for targeting Sponsored Brands and Sponsored Products campaigns. Ad-attributed branded searches are conversion events that help advertisers to understand how their ads influenced branded searches. For advertisers running both DSP and Sponsored Brands or Sponsored Products, it is technically possible for an ad-attributed branded conversion event to also be represented as a Sponsored Brands or Sponsored Products keyword impression, but this connection is not possible to establish in AMC. 

### 1.3 Requirements

To use this IQ, advertisers who sell products on Amazon must invest in Sponsored Brands or Sponsored Products campaigns and use keyword targeting. 


### 1.4 Backfill and data availability

All Sponsored Brands traffic events, including search terms and keywords, are available to query from `sponsored_ads_traffic` starting on December 2, 2022. Before this date, there are no Sponsored Brands traffic events in the table `sponsored_ads_traffic`.

The following keywords and search terms are available to query from the instance creation date (approximately*) or 12.5 months ago, whichever is sooner:

* Sponsored Products keywords and search terms from `sponsored_ads_traffic`
* Sponsored Brands and Sponsored Products keywords and search terms from `amazon_attributed_events_by_traffic_time` and `amazon_attributed_events_by_conversion_time`

Please note that the exact dates will vary, depending on when the backfill period. Most instances that were created less than 12.5 months ago have data from 0-28 days before the instance creation date. 

*We identified an edge case where some of the SB campaigns with no ad-groups were missing from AMC at launch for certain customers. A fix has been released and advertisers should see these campaign signals from 1/26/2023.*

## 2. Introductory Queries

### 2.1 Traffic by keyword and search term

Keywords and search terms are available as dimensions for traffic events, both impressions and clicks, in the table `sponsored_ads_traffic`. Both dimensions are provided for all impression events, including impressions from users who clicked and did not click the ad. AMC does not include the set of keywords and search terms from events that did not trigger an impression on one of the sponsored ads campaigns in the instance.

Keywords that were targeted are included in the column `targeting`. Traffic events that used keywords are identified using the `match_type` column. When the `match_type` is 'PHRASE', 'BROAD', or 'EXACT', keyword targeting was used. Learn more about match types [here](https://advertising.amazon.com/blog/how-to-start-and-improve-your-keyword-strategy?ref_=a20m_us_gw_ztw_blg_kystrt). Otherwise, a different targeting method was used for the campaign, such as [manual targeting](https://advertising.amazon.com/library/videos/understanding-manual-and-automatic-targeting-sponsored-products) or [product targeting](https://advertising.amazon.com/API/docs/en-us/bulksheets/sp/sp-entities/sp-entity-product-targeting). 





```
/* Instructional Query: Traffic by keyword and search term */
SELECT
  targeting as keyword,
  customer_search_term,
  match_type,
  campaign,
  ad_product_type,
  SUM(impressions) as impressions,
  SUM(clicks) as clicks
FROM
  sponsored_ads_traffic
  where match_type in('PHRASE', 'BROAD', 'EXACT')

GROUP BY
  1,
  2,
  3,
  4,
  5
```

|keyword|customer_search_term|match_type|campaign|ad_product_type|impressions|clicks|
|:--|:--|:--|:--|:--|:--|:--|
|coffee|coffee|EXACT|Kitchen Smart Coffee Generic|sponsored_products|52321|12033|
|coffee maker|buy coffee maker|BROAD|Kitchen Smart Coffee Makers SP|sponsored_products|26161|6017|
|coffee machines|coffee machine|BROAD|Kitchen Smart Coffee Makers SB|sponsored_brands|29300|6739|
|kitchen smart|kitchen smart coffee maker|PHRASE|Kitchen Smart Coffee Makers SB|sponsored_brands|14650|3370|


### 2.2 Attributed conversions by keyword and search term

Any keywords and search terms attributed to a conversion event are included in the tables `amazon_attributed_events_by_traffic_time` and `amazon_attributed_events_by_conversion_time`. Use the query below to measure the total purchases and detail page views by keywords and search terms.



```
/* Instructional Query: Attributed conversions by keyword and search term */

SELECT
  targeting as keyword,
  customer_search_term,
  match_type,
  campaign,
  ad_product_type,
  SUM(total_detail_page_view) as total_dpv,
  SUM(total_purchases) as total_purchases
FROM
  amazon_attributed_events_by_traffic_time
  where match_type in('PHRASE', 'BROAD', 'EXACT')

GROUP BY
  1,
  2,
  3,
  4,
  5
```

|keyword|customer_search_term|match_type|campaign|ad_product_type|total_dpv|total_purchases|
|:--|:--|:--|:--|:--|:--|:--|
|coffee|coffee|EXACT|Kitchen Smart Coffee Generic|sponsored_products|3488|802|
|coffee maker|buy coffee maker|BROAD|Kitchen Smart Coffee Makers SP|sponsored_products|1744|401|
|coffee machines|coffee machine|BROAD|Kitchen Smart Coffee Makers SB|sponsored_brands|1953|449|
|kitchen smart|kitchen smart coffee maker|PHRASE|Kitchen Smart Coffee Makers SB|sponsored_brands|977|225|


### 3. First vs attributed search terms query

Knowing the search terms that led to an ad-attributed purchase helps you to understand how customers were searching immediately before they purchased. However, they do not provide insight into how customers were searching prior to the last search that led to the attributed purchase. The query below pairs the attributed keyword with the first search, which would help you to understand if your customers had different search queries before they purchased. 

The campaign is not returned in the final select statement to cut down on the complexity of the results. However, it is considered when forming the search term pairs. For the first and attributed search terms to be paired on the same row, they must come from users exposed to the same campaign. Therefore, if your company sells multiple products in multiple categories (such as grocery and electronics), you will likely not find examples of your electronic search terms matched with the grocery search terms if you separate these keywords into different campaigns. Finally, the time window for the traffic events is extended 30 days, so searches are considered at least 30 days before the start of the time window selected. Search ‘UPDATE’ to revise the lookback window. See section 1.4 for potential constraints limitations on this lookback window. 



```
/* Instructional Query: Purchases by first vs attributed keyword and search term */
WITH searches AS (
  SELECT
    DISTINCT user_id,
    customer_search_term AS search_term,
    campaign,
    ad_product_type,
    event_dt
  FROM
    TABLE(
      extend_time_window(
        'sponsored_ads_traffic',
        -- OPTIONAL UPDATE: the lookback window from the optional 30-days.
        'P30D',
        'P0D'
      )
    )
  WHERE
    match_type IN('PHRASE', 'BROAD', 'EXACT')
    AND user_id IS NOT NULL
),
searches_ranked AS (
  SELECT
    user_id,
    search_term,
    campaign,
    ad_product_type,
    event_dt,
    RANK() OVER(
      PARTITION BY user_id
      ORDER BY
        event_dt ASC
    ) AS time_rank
  FROM
    searches
),
attributed_conversions_user AS (
  SELECT
    a.user_id,
    customer_search_term AS attributed_search_term,
    search_term,
    a.campaign,
    a.ad_product_type,
    SUM(total_purchases) AS total_purchases
  FROM
    amazon_attributed_events_by_conversion_time a
    JOIN searches_ranked s ON s.user_id = a.user_id
    AND s.campaign = a.campaign
    AND s.ad_product_type = a.ad_product_type
  WHERE
    event_dt < conversion_event_dt
    AND total_purchases > 0
    AND time_rank = 1
  GROUP BY
    1,
    2,
    3,
    4,
    5
)
SELECT
  search_term AS first_search_term,
  attributed_search_term AS attributed_search_term,
  SUM(total_purchases) AS total_purchases
FROM
  attributed_conversions_user
GROUP BY
  1,
  2
```

#### Example results

The results below can help you to identify search terms that assisted with attributed purchases. The terms in the `first_search_term` column was searched first when considering the 30-day period before the query time window. The terms in the `attributed_search_term` column were search terms attributed to the purchase. All terms below had at least one impression or click event across Sponsored Brands or Sponsored Products campaigns. 



|first_search_term|attributed_search_term|total_purchases|
|:--|:--|:--|
|coffee|coffee|220|
|coffee|buy kitchen smart coffee|1256|
|coffee maker|coffee maker|265|
|coffee maker|kitchen smart coffee maker|662|
|kitchen appliances|kitchen smart|225|


#### Interpretation

The first row shows that when customers only search for ‘coffee’ as both their first and attributed search term during the selected period, there were only 220 total purchases. The second row shows that when customers search for ‘coffee’ first, followed by ‘buy kitchen smart coffee’, there were 5.7x as many purchases. (1256 / 220 = 5.7). When evaluating the other rows, there is a general trend that the upper funnel non-branded terms, such as ‘coffee maker’ are searched first, and the branded terms, such as ‘kitchen smart coffee maker’ are more likely to be attributed. You can use this as an input to adjust keyword and bidding strategies for Sponsored Ads campaigns. 


