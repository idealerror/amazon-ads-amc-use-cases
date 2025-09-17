## Metadata

**id:** 981804ab0a66e2bf0b9f2a2ae5ab6e58b28583a259aa596a3742662557a78e70
**collection:** INSTRUCTIONAL
**state:** LIVE
**deprecation_metadata:** None
**description:** Compare the performance of your first-party audience to audiences unknown to Amazon channels.
**tags:** ['Advertiser data upload']
**use_case_categories:** ['Audience measurement']
**advertiser_types:** []
**ad_product_types:** None
**shared_with_accounts:** None

---

## Introduction 

### Purpose 

This instructional query (IQ) helps you analyze the reach and performance of your Amazon Ads campaigns in the context of your known first-party (1P) customer set. You can use this template to understand how your Amazon Ads media delivery is distributed across 1P (known) and other (unknown) audiences, as well as compare performance between the two groups. In this context, your 1P known audience could be, for example, those who have transacted via your retail site or with whom you have a pre-existing customer relationship; everyone outside of this 1P audience would be classified as unknown. These insights may be helpful if you are looking to assess how well your campaigns are reaching your known customers, or (if your campaign is more focused on growth of your customer set) how well your campaigns are reaching net-new customers.

### Requirements 

To use this query, you must have uploaded 1P inputs, such as an audience table, to AMC. You can use 1P inputs in conjunction with Amazon Ads events for a variety of measurement and audience use cases. For more details, see [Instructional query: Introduction to using first-party data in AMC](/marketing-cloud/instructional-queries/f301989fa6543b1b32301fff3b361b45f7e4507d9e214218f81fc187edc10e6b). 

You must also have had at least one active Amazon Ads campaign during the time frame you choose for your analysis. However, while the query does include `ad_product_type` as a dimension to differentiate results based on Amazon Ads channel (Amazon DSP, Sponsored Products, Sponsored Display, and Sponsored Brands), note that you do not need to be running campaigns in more than one channel to use this query.

## Data query instructions 

### Data returned

The query returns delivery and performance metrics for known customers (the audience that you uploaded to AMC) vs. all other (unknown) customers. You can customize which campaigns and ad product types you include, along with which type of conversion events are relevant for you.

### Tables used

- **first_party_audience_table** is a placeholder table name; replace this table name with the name of the audience table you have uploaded to AMC.
  - Note: 1P tables show up under the **Advertiser uploaded** section of the AMC UI’s **Schema explorer**. Refer to [Instructional query: Introduction to using first-party data in AMC](/marketing-cloud/instructional-queries/f301989fa6543b1b32301fff3b361b45f7e4507d9e214218f81fc187edc10e6b) to learn more about how you can use first-party inputs within AMC.
- **[dsp_impressions](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/dsp_impressions)** and/or **[sponsored_ads_traffic](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/sponsored_ads_traffic)** tables are used to gather the impression exposure events.
- **[amazon_attributed_events_by_traffic_time](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/amazon_attributed_events_by_conversion_time)** is used to join in-conversion events.

## Data interpretation instructions 

### Example query results

This data is for instructional purposes only. Your results will differ.

| ad_product_type    | customer_set | audience_size | unique_reach | audience_reach | spend      | percent_of_spend | impressions | users_that_purchased | purchases | user_purchase_rate |
| ------------------ | ------------ | ------------- | ------------ | -------------- | ---------- | ---------------- | ----------- | -------------------- | --------- | ------------------ |
| amazon_dsp         | known        | 1,234,567     | 654,321      | 53.00\%         | \$4,907.41  | 21.81\%           | 1,635,803   | 2,814                | 2,842     | 0.43\%              |
| amazon\_dsp         | unknown      | NA            | 2,345,678    | NA             | \$17,592.59 | 78.19\%           | 5,864,195   | 4,926                | 4,975     | 0.21\%              |
| sponsored_products | known        | 1,234,567     | 432,210      | 35.01\%         | \$3,241.58  | 11.11\%           | 1,080,525   | 2,809                | 2,837     | 0.65\%              |
| sponsored_products | unknown      | NA            | 3,456,789    | NA             | \$25,925.92 | 88.89\%           | 8,641,973   | 14,864               | 15,013    | 0.43\%              |
| sponsored_display  | known        | 1,234,567     | 321,098      | 26.01\%         | \$2,408.24  | 6.57\%            | 802,745     | 1,028                | 1,038     | 0.32\%              |
| sponsored_display  | unknown      | NA            | 4,567,890    | NA             | \$34,259.18 | 93.43\%           | 11,419,725  | 5,025                | 5,075     | 0.11\%              |
| sponsored_brands   | known        | 1,234,567     | 210,987      | 17.09\%         | \$1,582.40  | 5.75\%            | 527,468     | 696                  | 703       | 0.33\%              |
| sponsored_brands   | unknown      | NA            | 5,678,901    | NA             | \$25,925.92 | 94.25\%           | 8,641,973   | 6,247                | 6,309     | 0.11\%              |

### Metrics defined

| Dimensions and metrics | Definition                                                   |
| ---------------------- | ------------------------------------------------------------ |
| customer_set           | Differentiates between known customers (the audience that you uploaded to AMC) vs. all other (unknown) customers. |
| audience_size          | If customer_set = known, size of the known customer set. Otherwise, NA. |
| unique_reach           | Number of distinct users reached. There is no duplication within each ad_product_type but there may be duplication across ad_product_type values. |
| audience_reach         | If customer_set = known, percentage of known customer set reached via the ad_product_type. Otherwise, NA. |
| spend                  | Spend in dollars or your local currency.                     |
| percent_of_spend       | Percent of ad_product_type spend per customer set.           |
| impressions            | Impressions delivered.                                       |
| users_that_purchased   | Number of unique users who purchased at least one time.      |
| purchases              | Total purchase events.                                       |
| user_purchase_rate     | users_that_purchased divided by unique_reach, to understand what percent of users reached went on to make at least one purchase. |

### Insights and data interpretation

This IQ will help you understand how performance compares from your known customers (the audience that you uploaded to AMC) versus all other (unknown) customers, per ad product.

**What is the purchase rate for known customers versus unknown customers, per ad product?**

For my Amazon DSP campaigns, known customers purchased at a rate of 0.43%, while unknown customers purchased at a rate of 0.21%. My known customers are more likely to purchase than unknown customers.

**What percent of my known customer set have I been able to reach per ad product type?**

I was able to reach 53% of my known customers on Amazon DSP, 35.01% on Sponsored Products, 26.01% on Sponsored Display, and 17.09% on Sponsored Brands. This can be a helpful insight depending on your business goals and your campaign set up: Are you trying to reach existing, known customers, or are you trying to grow your brand by reaching net-new audiences? 

**How much am I spending, per ad product, on known vs. unknown customers?**

For my Sponsored Brand campaigns, 5.75% of my total SB spend was on reaching known customers, vs. 94.25% on reaching unknown customers. My tactics are doing well at reaching net-new customers.

## Queries

### Exploratory query: Identify campaign filters (ASIN conversions)

To identify the campaigns you want to include in the final query, use the following exploratory query. Note that filtering to specific campaigns in the final query is optional.



```
-- Instructional query: Exploratory query for Compare Known and Unknown Customer Performance (ASIN conversions) -- 
-- Search for 'UPDATE' to find the clauses where you can optionally customize this query for your use-case.
-- 1) Optional Update to only retrieve campaigns that tracked a particular set of ASINs.

WITH impressions AS (
  SELECT
    campaign,
    campaign_id_string,
    campaign_start_date,
    campaign_end_date,
    SUM(impressions) AS impressions,
    SUM(total_cost / 100000) AS ad_spend
  FROM
    dsp_impressions
  GROUP BY
    1,
    2,
    3,
    4
  UNION ALL
  SELECT
    campaign,
    campaign_id_string,
    campaign_start_date,
    campaign_end_date,
    SUM(impressions) AS impressions,
    SUM(spend / 100000000) AS ad_spend
  FROM
    sponsored_ads_traffic
  GROUP BY
    1,
    2,
    3,
    4
),
combined AS (
  SELECT
    i.campaign,
    i.campaign_id_string,
    c.ad_product_type,
    i.campaign_start_date,
    i.campaign_end_date,
    SUM(i.impressions) AS impressions,
    SUM(c.product_sales) AS promoted_sales,
    SUM(c.total_product_sales) AS total_sales,
    SUM(i.ad_spend) AS ad_spend
  FROM
    impressions AS i
    LEFT JOIN amazon_attributed_events_by_traffic_time AS c ON i.campaign_id_string = c.campaign_id_string
  GROUP BY
    1,
    2,
    3,
    4,
    5
)
SELECT
  campaign,
  campaign_id_string,
  COALESCE(ad_product_type, 'Amazon DSP') AS ad_product_type,
  campaign_start_date,
  campaign_end_date,
  SUM(impressions) AS impressions,
  SUM(promoted_sales) AS promoted_sales,
  SUM(total_sales) AS total_sales,
  SUM(ad_spend) AS ad_spend
FROM
  combined
GROUP BY
  1,
  2,
  3,
  4,
  5
```

### Query template: Compare known and unknown customer performance (ASIN conversions) 

**Note:** The query below uses 1P table name placeholders. Copy and paste the code into the query editor and follow the instructions within the query to include your specific 1P table name.



```
-- Instructional Query: Compare Known and Unknown Customer Performance (ASIN conversions) --
/*
 ------- Customization Instructions -------
 0) (OPTIONAL) Run the exploratory query first to identify the appropriate campaign filters, if any.
 1) (OPTIONAL) Update the dsp_campaigns and sa_campaigns CTEs with your chosen campaigns. 
 2) (REQUIRED) Adjust known_customers and known_customers_count CTEs to reflect the name of your first party audience table.
 3) (OPTIONAL) Adjust impressions and conversions CTEs to reflect whether you are limiting the query to 
 */
WITH dsp_campaigns (campaign_id_string) AS (
  VALUES
    /* OPTIONAL UPDATE [1 of 5]: Define the group of Amazon DSP campaigns that will be included in this analysis. */
    (1111111111111),
    (2222222222222)
),
sa_campaigns (campaign_id_string) AS (
  VALUES
    /* OPTIONAL UPDATE [2 of 5]: Define the group of sponsored ads campaigns that will be included in this analysis. */
    (3333333333333),
    (4444444444444),
    (5555555555555)
),
known_customers AS (
  SELECT
    DISTINCT(user_id),
    'known' AS customer_set
  FROM
    /*first_party_audience_table -- REQUIRED UPDATE [1 of 1]: Edit to reflect the name of your first-party audience table and uncomment the line. */
  WHERE
    user_id IS NOT NULL
),
known_customer_count AS (
  SELECT
    'known' AS customer_set,
    COUNT(DISTINCT user_id) AS audience_size
  FROM
    known_customers
),
impressions AS (
  SELECT
    user_id,
    'amazon_dsp' AS ad_product_type,
    SUM(impressions) AS impressions,
    (SUM(total_cost) / 100000) AS spend
  FROM
    dsp_impressions
    /* OPTIONAL UPDATE [3 of 5]: Uncomment this line to enable Amazon DSP campaign filter. Leave the line commented to skip filters. */
    -- WHERE campaign_id_string IN (SELECT campaign_id_string FROM dsp_campaigns)
    AND user_id IS NOT NULL
  GROUP BY
    1,
    2
  UNION ALL
  SELECT
    user_id,
    ad_product_type,
    SUM(impressions) AS impressions,
    (SUM(spend) / 100000000) AS spend
  FROM
    sponsored_ads_traffic
    /* OPTIONAL UPDATE [4 of 5]: Uncomment this line to enable sponsored ads campaign filter. Leave the line commented to skip filters.*/
    -- WHERE campaign_id_string IN (SELECT campaign_id_string FROM sa_campaigns)
    AND user_id IS NOT NULL
  GROUP BY
    1,
    2
),
impressions_by_customer_set AS (
  SELECT
    i.user_id,
    COALESCE (kc.customer_set, 'unknown') AS customer_set,
    i.ad_product_type,
    i.impressions,
    i.spend
  FROM
    impressions AS i
    LEFT JOIN known_customers AS kc ON i.user_id = kc.user_id
),
conversions AS (
  SELECT
    user_id,
    COALESCE (ad_product_type, 'amazon_dsp') AS ad_product_type,
    SUM(purchases) AS purchases
  FROM
    amazon_attributed_events_by_traffic_time
    /* OPTIONAL UPDATE [5 of 5]: Uncomment these lines to enable campaign filters. Leave the lines commented to skip filters. */
    -- WHERE campaign_id_string IN (SELECT campaign_id_string FROM dsp_campaigns)
    -- OR campaign_id_string IN (SELECT campaign_id_string FROM sa_campaigns)
    AND purchases > 0
    AND user_id IS NOT NULL
  GROUP BY
    1,
    2
),
combined AS (
  SELECT
    i.user_id AS imp_user_id,
    c.user_id AS conv_user_id,
    i.ad_product_type,
    i.customer_set,
    i.spend,
    i.impressions,
    CASE
      WHEN c.user_id IS NOT NULL
      AND purchases > 0 THEN 1
      ELSE 0
    END AS users_that_purchased,
    COALESCE (c.purchases, 0) AS purchases
  FROM
    impressions_by_customer_set AS i
    LEFT JOIN conversions AS c ON i.user_id = c.user_id
    AND i.ad_product_type = c.ad_product_type
),
add_customer_count AS (
  SELECT
    ad_product_type,
    c.customer_set,
    CASE
      WHEN c.customer_set = 'known' THEN kc.audience_size
      ELSE 'NA'
    END AS audience_size,
    COUNT(DISTINCT imp_user_id) AS unique_reach,
    SUM(spend) AS spend,
    SUM(impressions) AS impressions,
    SUM(users_that_purchased) AS users_that_purchased,
    SUM(purchases) AS purchases
  FROM
    combined c
    LEFT JOIN known_customer_count AS kc ON c.customer_set = kc.customer_set
  GROUP BY
    1,
    2,
    3
),
ad_product_cost AS (
  SELECT
    ad_product_type,
    SUM(spend) AS ad_product_type_spend
  FROM
    combined
  GROUP BY
    1
)
SELECT
  ft.ad_product_type,
  customer_set,
  audience_size,
  unique_reach,
  CASE
    WHEN customer_set = 'known' THEN ROUND((unique_reach / audience_size), 4)
    ELSE 'NA'
  END AS audience_reach,
  ROUND(spend, 2) AS spend,
  ROUND(spend / ad_product_type_spend, 4) AS percent_of_spend,
  impressions,
  users_that_purchased,
  purchases,
  ROUND((users_that_purchased / unique_reach), 4) AS user_purchase_rate
FROM
  add_customer_count ft
  LEFT JOIN ad_product_cost ap ON ft.ad_product_type = ap.ad_product_type
```

