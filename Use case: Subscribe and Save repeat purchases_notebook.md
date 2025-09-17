## Metadata

**id:** bf4b57a8aecd12c19f5928b73f709fa0fb5f3ea5414dc127cdb68f13f11c1e32
**collection:** INSTRUCTIONAL
**state:** None
**deprecation_metadata:** None
**description:** Discover the SnS customer journey from subscription to repeat purchases (FSI required).
**tags:** ['Paid features']
**use_case_categories:** ['How to', 'ASIN analysis', 'Grocery analysis']
**advertiser_types:** ['On-Amazon conversions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## Introduction 

### Purpose

Amazon’s Subscribe & Save (SnS) program allows customers to set up regularly scheduled deliveries of products, as well as earn discounts on those repeat purchases. Previously, AMC Flexible Shopping Insights (FSI) provided signals for the initial SnS subscription event and the first SnS purchase. As of 02/05/2024, FSI was enhanced to include the repeat SnS purchases (that is, all purchases that occurred after the first purchase after the subscription).

| **Relevant tables**                          | **SnS subscription event (Ad-attributed)** | **SnS subscription event (Non-ad-attributed)** | **First SnS purchase (Ad-attributed)** | **First SnS purchase (Non-ad-attributed)** | **Repeat SnS purchase(s)** |
|:--------------------------------------|:------------------------------------------|:----------------------------------------------|:--------------------------------------|:------------------------------------------:|:--------------------------|
| Amazon ads (`conversions`)                              | x                                          |                                                | x                                      |                                            |                            |
| Paid features (Flexible Shopping Insights) | x                                          | x                                              | x                                      | x                                          | Added 02/05/2024           |

* Repeat SnS signals, including `sns_subscription_id`, are not available in Sandbox.



This IQ is an introduction to SnS repeat purchases signals available, and provides two queries:

* **4.1 Average spend by SnS subscribers and non-SnS subscribers** - Compare the spending patterns (average spend) of the subscribed customers vs the non-subscribed customers.
* **4.2 Number of SnS purchases by ASIN** - Discover which ASINs were most frequently part of an SnS subscription, as well as the percentage of total SnS for each ASIN (the percentage of purchases in one SnS subscription by user vs total SnS purchases for all users). This allows you to understand for each one of your ASINs, how many repeat purchases occur before drop-off and which ASINs are working well in the SnS program.

**Note:** Note that only certain product categories, such as Beauty and Grocery, are eligible for the SnS program. For more information or to get started with the Fulfillment by Amazon Subscribe & Save program, visit [FBA Subscribe and Save](https://sell.amazon.com/fulfillment-by-amazon/fba-subscribe-and-save).

### SnS event_subtypes

When a customer subscribes to SnS, the following conversion events are logged: 

1. The initial SnS website conversion that identifies the SnS subscription (event_subtype of `snsSubscription`)
2. The initial SnS purchase conversion that identifies the purchase event (event_subtype of `firstSnSOrder` in `conversions_all` and event_subtype of `order` in `conversions`)
3. Customers also select a frequency for repurchase, and subsequent SnS purchase events are now logged as repeat SnS purchase conversions (event_subtype of `repeatSnSOrder`).

| Tables          | SnS subscription event        | First SnS purchase         | Repeat SnS purchase(s)      |
|-----------------|---------------------------------|-------------------------------|--------------------------------|
| conversions     | event_subtype = snsSubscription | event_subtype = order         | -                              |
| conversions_all | event_subtype = snsSubscription | event_subtype = firstSnSOrder | event_subtype = repeatSnSOrder |

### Example SnS signals in conversions_all

The following table below illustrates how the SnS signals are recorded in the `conversions_all` table. Note that results when querying the `conversions` table will vary.

**Note:** This data is for instructional purposes only. Your results will differ.



| **user_id** | **conversion_id** | **event_category** | **event_subtype** | **event_type_description**        | **exposure_type** | **sns_subscription_id** |
|:-----------|:----------------|:------------------|:-----------------|:---------------------------------|:-----------------|:-----------------------|
| user1       | 111               | website            | snsSubscription   | New SnS Subscription              | ad-exposed        | id111111                |
| user1       | 222               | purchase           | firstSnSOrder     | Subscribe and save purchase event | ad-exposed        | id111111                |
| user1       | 333               | purchase           | repeatSnSOrder    | Subscribe and save purchase event | non-ad-exposed    | id111111                |
| user1       | 444               | purchase           | repeatSnSOrder    | Subscribe and save purchase event | non-ad-exposed    | id111111                |



### Requirements

To use these queries, you must:

* Subscribe to Flexible Amazon shopping insights. Paid Features are only available in certain regions, if you're unable to see the Paid Features tab, reach out to your AdTech Account Executive.
* Be enrolled in [Amazon’s Subscribe & Save (SnS) program](https://sell.amazon.com/fulfillment-by-amazon/fba-subscribe-and-save).

## Data query instructions

### Data returned

The following two use cases are covered in this IQ:

* Average spend by SnS subscribers and non-SnS subscriber: This query will return the average spend by customers who are subscribed to SnS vs not subscribed.
* Number of SnS purchases by ASIN: This query will return the purchase frequency of an ASIN under the SnS program and also the percentage of purchases in one SnS subscription by every user vs total SnS purchases by all users, for one ASIN.

### Tables used 

* [conversions_all](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/conversions_all_paid) - This table contains both ad-exposed and non ad-exposed conversions for tracked ASINs. Conversions are ad-exposed if a user was served a traffic event within the 28-day period prior to the conversion event. This table will be available to you only if you’ve subscribed to Amazon Flexible Shopping Insights (FSI).



## Data interpretation instructions

### Example query results

**Note:** This data is for instructional purposes only. Your results will differ.

**Example results: Average spend by SnS subscribers and non-SnS subscribers**

| **user_type**  | **total_sales** | **users_that_purchased** | **average_spend** |
|:--------------|:---------------|:------------------------|:-----------------|
| non-subscriber | 935857.77       | 3892                     | 240.45            |
| subscriber     | 79120.29        | 237                      | 333.84            |


**Example results: Number of SnS purchases by ASIN**

| **tracked_asin** | **sns_purchase_frequency** | **percent_total_sns** |
|:------------------|:--------------------------|:---------------------|
| asin111            | 1                          | 5%                    |
| asin111            | 2                          | 10%                   |
| asin111            | 3                          | 24%                   |
| asin111            | 4                          | 22%                   |
| asin111            | 5                          | 15%                   |
| asin111            | 6                          | 8%                    |
| asin111            | 7                          | 6%                    |
| asin222            | 1                          | 7%                    |
| asin222            | 2                          | 15%                   |
| asin222            | 3                          | 23%                   |
| asin222            | 4                          | 20%                   |
| asin222            | 5                          | 15%                   |
| asin222            | 6                          | 8%                    |
| asin222            | 7                          | 2%                    |


### Metrics and dimensions defined

**Metrics defined: Average spend by SnS subscribers and non-SnS subscribers**

| **metric**           | **definition**                              |
|:--------------------|:-------------------------------------------|
| `total_sales`          | total sales for all the users               |
| `users_that_purchased` | count of users who made a purchase          |
| `average_spend`        | Average spend = total_sales / users_that_purchased |


**Metrics defined: Number of SnS purchases by ASIN**

| **metric**             | **definition**                                                                             |
|:----------------------|:------------------------------------------------------------------------------------------|
| `tracked_asin`           | ASIN purchased through SnS                                                                 |
| `sns_purchase_frequency` | Number of times the ASIN was a part of one SnS subscription                                |
| `percent_total_sns`     | total SnS purchases for an ASIN/total purchases in a single SnS subscription for the ASIN |


### Insights and data interpretation

**What is the spending pattern of the subscribed customers vs the non-subscribed customers?**

Customers who are not subscribed to SnS have a 27%  lower average spend when compared to the subscribed customers. Additionally, the subscribed customers who are loyal to your brand and products provide opportunities for cross-sale via AMC Audiences. 

**What are the popular ASINs that have the most repeat purchase frequency in sales through SnS?**

The purchase frequency and the percent total SnS built from the query will help evaluate the performance of the ASINs in the SnS program. It allows you to understand for each one of your ASINs, how many repeat purchases occur before drop-off and which ASINs are working well in the SnS program.

In **Example results: Number of SnS purchases by ASIN** above, asin111 is the most popular ASIN in the SnS program. When it was purchased for the third time with an SnS subscription (the first purchase plus two repeat purchases), it represented 24% of SnS subscriptions. Consider promoting this ASIN and its variations for campaigns that are focused on attracting repeat customers.

## Queries

### Instructional query: Average spend by SnS subscribers and non-SnS subscribers

This query displays the user type, total sales, count of the users, and their average spend per user type over distinct users in the selected time window of the query. It compares the metrics of customers who have subscribed to the SnS program vs. customers who have not subscribed.



```
-- Instructional Query: Introduction to Subscribe & Save (SnS) repeat purchases (Average spend by SnS subscribers and non-SnS subscribers) -- 
/*
 ------- Customization Instructions -------
 1) Set the "Date range" in the Query Editor to define the time range. It is recommended to query at least 3 months of data to capture the SnS purchase patterns. 
 1a) To improve the query performance, it will be best to include an ASIN filter to narrow the ASIN in scope.
 1b) To improve the query performance include an ASIN filter to reduce the data queried.
 */
 SELECT
  CASE
    WHEN event_subtype IN (
      'snsSubscription',
      'firstSnSOrder',
      'repeatSnSOrder'
    ) THEN 'subscriber'
    ELSE 'non-subscriber'
  END AS user_type,
  COUNT(DISTINCT user_id) AS users_that_purchased,
  SUM(total_product_sales) AS total_sales,
  SUM(total_product_sales) / COUNT(DISTINCT user_id) AS average_spend
FROM
  conversions_all
GROUP BY
  1
```

### Instructional query: Number of SnS purchases by ASIN 

This query displays the SnS purchase frequency of the ASINs in the selected time window of the query. It compares purchase frequency of the ASIN within a single SnS subscription with the total purchases made for that ASIN as a part of overall brand SnS Program.



```
-- Instructional Query: Introduction to Subscribe & Save (SnS) repeat purchases (Number of SnS purchases by asin) --
/*
 ------- Customization Instructions -------
 1) Set the "Date range" in the Query Editor to define the time range.
 1a) It is recommended to query at least 3 months of data to capture the SnS purchase patterns. To improve the query performance include an ASIN filter to reduce the data queried.
 Search for 'UPDATE' to find the clauses where you can optionally customize this query for your use-case.
 2) Optional Update to only retrieve a particular set of ASINs.
 */
 WITH asin_sns_stats AS (
  SELECT
    tracked_asin,
    COUNT(DISTINCT conversion_id) AS first_n_repeat_purchase,
    COUNT(sns_subscription_id) AS total_sns
  FROM
    conversions_all
  WHERE
    event_category = 'purchase'
    AND sns_subscription_id IS NOT NULL
    /* AND tracked_asin IN ('ASIN1234', 'ASIN5678') -- OPTIONAL UPDATE: Uncomment this line to only retrieve a particular set of ASINs. */
  GROUP BY
    1
),
bucketized_by_frequency AS (
  SELECT
    tracked_asin,
    CASE
      WHEN first_n_repeat_purchase > 6 THEN '7+'
      ELSE first_n_repeat_purchase
    END AS sns_purchase_frequency,
    SUM(total_sns) AS total_sns_count
  FROM
    asin_sns_stats
  GROUP BY
    1,
    2
),
totals AS (
  SELECT
    SUM(total_sns_count) AS total
  FROM
    bucketized_by_frequency
)
SELECT
  tracked_asin,
  sns_purchase_frequency,
  total_sns_count,
  (total_sns_count / total) AS percent_total_sns
FROM
  bucketized_by_frequency,
  totals b
```
