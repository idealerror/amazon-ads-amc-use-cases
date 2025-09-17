## Metadata

**id:** 4d11fa83f992c80b06e8ea6da8a8a235e41955e0da4acb45de04a1b3812b8b3a
**collection:** None
**state:** None
**deprecation_metadata:** None
**description:** Determine the ideal balance of ad frequency with on-Amazon and off-Amazon performance metrics.
**tags:** []
**use_case_categories:** ['Branded searches and keywords', 'Cost analysis', 'Customer value', 'New-to-brand', 'Performance deep dive', 'Reach and frequency']
**advertiser_types:** ['On-Amazon conversions', 'Off-Amazon conversions', 'Digital subscriptions']
**ad_product_types:** None
**shared_with_accounts:** None

---

## 1. Introduction 

### 1.1 Purpose 

This instructional query (IQ) allows you to explore the optimal frequency, or the ideal ad frequency that balances number of impressions served with conversions. Use this IQ to dive deep into on-Amazon and off-Amazon conversions including click-through rate, branded search rate, detail page view rate, add to cart rate, purchase rate, new-to-brand purchase rate, pixel conversion, digital subscription, and sign-up purchases. 

With these insights, you can answer questions such as: 

* Which frequency drives the highest purchase rate? 
* Which frequencies have above-average new-to-brand purchase rates? 
* Which frequency bucket drives the most digital subscriptions? 

Understanding optimal frequency enables you to optimize campaigns and maximize effectiveness, helps you in setting an ad frequency cap to ensure you are not underexposing or overexposing your audience. Note that AMC provides several other frequency IQs, as well as the [Optimal frequency solution](https://advertising.amazon.com/marketing-cloud/instructional-queries/frequencyApp), a point-and-click interface to inform frequency decisions.

**Tip:** This template is customizable. You can keep the metrics that matter to you, and remove those you don't need. You can also increase or decrease the frequency cap.

### Requirements 

To use this report, advertisers must have data from either or both Amazon DSP and Sponsored Ads campaigns reaching the same audience. If you only have one campaign, the query will return results limited to the single campaign. 

Due to the 14-day attribution window, we advise to wait two weeks past the end of the campaign to run this query to capture all conversions.

## Data query instructions 

### Data returned

This query returns on-Amazon and off-Amazon conversion metrics against various frequencies starting from 1 to 25 (this threshold is configurable and can be updated). Anything above 25 frequencies is bucketed as 25+.

### Tables used 

* [dsp_clicks](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/dsp_clicks): This table provides associated click events for Amazon DSP campaigns.
* [dsp_impressions](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/dsp_impressions): This table provides traffic events for Amazon DSP campaigns.
* [sponsored_ads_traffic](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/sponsored_ads_traffic): This table provides traffic data for Sponsored Ads campaigns.
* [amazon_attributed_events_by_traffic_time](https://advertising.amazon.com/API/docs/en-us/guides/amazon-marketing-cloud/datasources/amazon_attributed_events_by_conversion_time): This table provides ad-attributed conversions data for both Amazon DSP and sponsored ads campaigns. As the conversions are attributed using a 14-day window, wait two weeks past the end date of the campaigns in scope to run this query to capture all conversions.

## Data interpretation instructions 

### Example query results

**This data is for illustration purposes only. Your results will differ.**

| frequency_bucket | reach   | impressions | cost       | clicks | click_through_rate | branded_searches | branded_search_rate | detail_page_views | detail_page_view_rate | add_to_carts | add_to_cart_rate | purchases | purchase_rate | new_to_brand_purchases | new_to_brand_purchase_rate | off_amazon_conversions | off_amazon_conversion_rate | pixel_conversions | pixel_conversion_rate | digital_subscription_signup_purchases | digital_subscription_signup_purchase_rate | units_sold | product_sales | new_to_brand_purchases | new_to_brand_units_sold | new_to_brand_product_sales | off_amazon_conversion_value | off_amazon_product_sales | total_detail_page_view | total_add_to_cart | total_purchases | total_units_sold | total_product_sales | new_to_brand_total_purchases | new_to_brand_total_units_sold | new_to_brand_total_product_sales |
|------------------|---------|-------------|------------|--------|--------------------|------------------|---------------------|-------------------|-----------------------|--------------|------------------|-----------|---------------|------------------------|----------------------------|------------------------|----------------------------|-------------------|-----------------------|---------------------------------------|-------------------------------------------|------------|---------------|------------------------|-------------------------|----------------------------|-----------------------------|--------------------------|------------------------|-------------------|-----------------|------------------|---------------------|------------------------------|-------------------------------|----------------------------------|
| 1                | 1194587 | 1194577     | 34800.1896 | 3726   | 0.31191            | 18993            | 1.58994             | 980               | 0.08204               | 68           | 0.00569          | 4         | 0.00033       | 6                      | 0.0005                     | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 24         | 6876.68       | 20                     | 22                      | 6421.69                    | 0                           | 0                        | 14809                  | 1405              | 22              | 24               | 6876.68             | 20                           | 22                            | 6421.69                          |
| 2                | 523788  | 1047576     | 30339.4102 | 2269   | 0.2166             | 11648            | 1.1119              | 1067              | 0.10185               | 42           | 0.00401          | 3         | 0.00029       | 11                     | 0.00105                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 13         | 3863.31       | 11                     | 12                      | 3863.31                    | 0                           | 0                        | 14010                  | 1077              | 12              | 13               | 3863.31             | 11                           | 12                            | 3863.31                          |
| 3                | 325897  | 977691      | 28155.7962 | 1713   | 0.17521            | 8043             | 0.82265             | 1008              | 0.1031                | 46           | 0.0047           | 10        | 0.00102       | 14                     | 0.00143                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 11         | 3966.92       | 9                      | 9                       | 3536.94                    | 0                           | 0                        | 15663                  | 927               | 11              | 11               | 3966.92             | 9                            | 9                             | 3536.94                          |
| 4                | 235230  | 940920      | 26955.7883 | 1338   | 0.1422             | 6775             | 0.72004             | 886               | 0.09416               | 46           | 0.00489          | 3         | 0.00032       | 3                      | 0.00032                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 5          | 1444.94       | 3                      | 3                       | 746.94                     | 0                           | 0                        | 11358                  | 867               | 5               | 5                | 1444.94             | 3                            | 3                             | 746.94                           |
| 5                | 224828  | 1124140     | 32819.9667 | 1321   | 0.11751            | 6253             | 0.55625             | 847               | 0.07535               | 38           | 0.00338          | 4         | 0.00036       | 8                      | 0.00071                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 10         | 2232.4        | 8                      | 8                       | 2065.74                    | 0                           | 0                        | 10456                  | 776               | 10              | 10               | 2232.4              | 8                            | 8                             | 2065.74                          |
| 6                | 155666  | 933996      | 26060.7039 | 1009   | 0.10803            | 5851             | 0.62645             | 838               | 0.08972               | 43           | 0.0046           | 5         | 0.00054       | 5                      | 0.00054                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 5          | 1484.99       | 5                      | 5                       | 1484.99                    | 0                           | 0                        | 9807                   | 731               | 5               | 5                | 1484.99             | 5                            | 5                             | 1484.99                          |
| 7                | 123476  | 864332      | 24431.9567 | 870    | 0.10066            | 4348             | 0.50305             | 679               | 0.07856               | 28           | 0.00324          | 5         | 0.00058       | 1                      | 0.00012                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 1          | 199.99        | 1                      | 1                       | 199.99                     | 0                           | 0                        | 8352                   | 626               | 1               | 1                | 199.99              | 1                            | 1                             | 199.99                           |
| 8                | 96241   | 769928      | 21744.6144 | 766    | 0.09949            | 4019             | 0.522               | 616               | 0.08001               | 36           | 0.00468          | 2         | 0.00026       | 4                      | 0.00052                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 7          | 1552.97       | 4                      | 5                       | 1253.97                    | 0                           | 0                        | 8032                   | 598               | 6               | 7                | 1552.97             | 4                            | 5                             | 1253.97                          |
| 9                | 80465   | 724185      | 20409.5623 | 682    | 0.09417            | 3167             | 0.43732             | 610               | 0.08423               | 28           | 0.00387          | 4         | 0.00055       | 5                      | 0.00069                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 5          | 1053.95       | 5                      | 5                       | 1053.95                    | 0                           | 0                        | 6911                   | 549               | 5               | 5                | 1053.95             | 5                            | 5                             | 1053.95                          |
| 10               | 76242   | 762420      | 21634.5443 | 650    | 0.08525            | 3167             | 0.41539             | 637               | 0.08355               | 37           | 0.00485          | 3         | 0.00039       | 4                      | 0.00052                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 5          | 1781.99       | 4                      | 5                       | 1781.99                    | 0                           | 0                        | 6670                   | 473               | 4               | 5                | 1781.99             | 4                            | 5                             | 1781.99                          |
| 11               | 60949   | 670439      | 18905.8959 | 565    | 0.08427            | 2709             | 0.40406             | 503               | 0.07503               | 39           | 0.00582          | 2         | 0.0003        | 1                      | 0.00015                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 3          | 957           | 1                      | 1                       | 499                        | 0                           | 0                        | 6394                   | 473               | 2               | 3                | 957                 | 1                            | 1                             | 499                              |
| 12               | 54191   | 650292      | 18116.0075 | 533    | 0.08196            | 2628             | 0.40413             | 515               | 0.0792                | 27           | 0.00415          | 2         | 0.00031       | 2                      | 0.00031                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 4          | 1056.99       | 2                      | 2                       | 428.99                     | 0                           | 0                        | 5924                   | 429               | 4               | 4                | 1056.99             | 2                            | 2                             | 428.99                           |
| 13               | 46763   | 607919      | 17018.5808 | 455    | 0.07485            | 2217             | 0.36469             | 525               | 0.08636               | 30           | 0.00493          | 4         | 0.00066       | 6                      | 0.00099                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 6          | 1516.97       | 6                      | 6                       | 1516.97                    | 0                           | 0                        | 5843                   | 439               | 6               | 6                | 1516.97             | 6                            | 6                             | 1516.97                          |
| 14               | 43480   | 608720      | 17005.8741 | 471    | 0.07738            | 2234             | 0.367               | 455               | 0.07475               | 26           | 0.00427          | 1         | 0.00016       | 1                      | 0.00016                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 2          | 359.98        | 1                      | 2                       | 359.98                     | 0                           | 0                        | 5829                   | 436               | 1               | 2                | 359.98              | 1                            | 2                             | 359.98                           |
| 15               | 38040   | 570600      | 15980.6949 | 425    | 0.07448            | 2064             | 0.36172             | 423               | 0.07413               | 32           | 0.00561          | 2         | 0.00035       | 2                      | 0.00035                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 4          | 1157.98       | 4                      | 4                       | 1157.98                    | 0                           | 0                        | 4973                   | 363               | 4               | 4                | 1157.98             | 4                            | 4                             | 1157.98                          |
| 16               | 33871   | 541936      | 15158.1805 | 426    | 0.07861            | 2103             | 0.38805             | 457               | 0.08433               | 26           | 0.0048           | 2         | 0.00037       | 5                      | 0.00092                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 8          | 2372.54       | 8                      | 8                       | 2372.54                    | 0                           | 0                        | 4875                   | 392               | 8               | 8                | 2372.54             | 8                            | 8                             | 2372.54                          |
| 17               | 30648   | 521016      | 14541.4184 | 371    | 0.07121            | 1970             | 0.37811             | 442               | 0.08483               | 30           | 0.00576          | 2         | 0.00038       | 1                      | 0.00019                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 3          | 777.99        | 3                      | 3                       | 777.99                     | 0                           | 0                        | 4727                   | 321               | 3               | 3                | 777.99              | 3                            | 3                             | 777.99                           |
| 18               | 27786   | 500148      | 13957.4266 | 387    | 0.07738            | 1583             | 0.31651             | 365               | 0.07298               | 24           | 0.0048           | 2         | 0.0004        | 2                      | 0.0004                     | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 4          | 954.97        | 4                      | 4                       | 954.97                     | 0                           | 0                        | 4371                   | 335               | 4               | 4                | 954.97              | 4                            | 4                             | 954.97                           |
| 19               | 25666   | 487654      | 13608.8025 | 344    | 0.07054            | 1613             | 0.33077             | 369               | 0.07567               | 28           | 0.00574          | 2         | 0.00041       | 2                      | 0.00041                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 2          | 499.98        | 2                      | 2                       | 499.98                     | 0                           | 0                        | 4310                   | 344               | 2               | 2                | 499.98              | 2                            | 2                             | 499.98                           |
| 20               | 23961   | 479220      | 13361.2409 | 338    | 0.07053            | 1241             | 0.25896             | 360               | 0.07512               | 16           | 0.00334          | 0         | 0             | 0                      | 0                          | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 0          | 0             | 0                      | 0                       | 0                          | 0                           | 0                        | 4253                   | 363               | 0               | 0                | 0                   | 0                            | 0                             | 0                                |
| 21               | 21878   | 459438      | 12806.3433 | 306    | 0.0666             | 1389             | 0.30233             | 322               | 0.07009               | 23           | 0.00501          | 3         | 0.00065       | 2                      | 0.00044                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 3          | 861.1         | 2                      | 2                       | 538.72                     | 0                           | 0                        | 3628                   | 303               | 3               | 3                | 861.1               | 2                            | 2                             | 538.72                           |
| 22               | 19976   | 439472      | 12234.9793 | 261    | 0.05939            | 1175             | 0.26737             | 328               | 0.07464               | 27           | 0.00614          | 3         | 0.00068       | 4                      | 0.00091                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 4          | 918.92        | 4                      | 4                       | 918.92                     | 0                           | 0                        | 3252                   | 255               | 4               | 4                | 918.92              | 4                            | 4                             | 918.92                           |
| 23               | 18504   | 425592      | 11875.1275 | 270    | 0.06344            | 1074             | 0.25235             | 329               | 0.0773                | 22           | 0.00517          | 2         | 0.00047       | 2                      | 0.00047                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 3          | 738.39        | 2                      | 2                       | 499.98                     | 0                           | 0                        | 3439                   | 272               | 3               | 3                | 738.39              | 2                            | 2                             | 499.98                           |
| 24               | 17092   | 410208      | 11424.901  | 280    | 0.06826            | 1021             | 0.2489              | 297               | 0.0724                | 12           | 0.00293          | 1         | 0.00024       | 1                      | 0.00024                    | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 1          | 499.99        | 1                      | 1                       | 499.99                     | 0                           | 0                        | 3078                   | 249               | 1               | 1                | 499.99              | 1                            | 1                             | 499.99                           |
| 25+              | 376931  | 25995762    | 732351.911 | 15558  | 0.05985            | 26597            | 0.10231             | 10113             | 0.0389                | 595          | 0.00229          | 89        | 0.00034       | 79                     | 0.0003                     | 0                      | 0                          | 0                 | 0                     | 0                                     | 0                                         | 95         | 26684.81      | 79                     | 83                      | 24235.91                   | 0                           | 0                        | 125786                 | 8777              | 89              | 95               | 26684.81            | 79                           | 83                            | 24235.91                         |

    
### Metrics defined

| metric                                    | definition                                                                                                                                                                        |
|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `reach`                                     | Number of distinct users in the frequency bucket                                                                                                                                  |
| `impressions`                               | Number of impressions in the frequency bucket                                                                                                                                     |
| `frequency_bucket`                          | Frequencies are grouped into different buckets from 1 - 25+ by default. "1" means users were exposed to an ad 1 time and "25+" means users were exposed to an ad 25 or more times |
| `cost`                                      | Cost of impressions                                                                                                                                                               |
| `clicks`                                    | Clicks                                                                                                                                                                            |
| `click_through_rate`                        | Click through rate defined as clicks per impression                                                                                                                               |
| `branded_searches`                          | Attributed Branded Searches                                                                                                                                                       |
| `branded_search_rate`                       | Branded Search rate defined as branded_searches per impression                                                                                                                    |
| `detail_page_views`                         | Detail page views of promoted products                                                                                                                                            |
| `detail_page_view_rate`                     | Detail page view rate defined as detail_page_views per impression                                                                                                                 |
| `add_to_carts`                              | Add to carts of promoted products                                                                                                                                                 |
| `add_to_cart_rate`                          | Add to cart rate defined as add_to_carts per impression                                                                                                                           |
| `purchases`                                 | Purchases of promoted products                                                                                                                                                    |
| `purchase_rate`                             | Purchase rate defined as purchases per impression                                                                                                                                 |
| `new_to_brand_purchases`                    | Purchases of promoted products from new to brand customers                                                                                                                        |
| `new_to_brand_purchase_rate`                | New to brand purchase rate defined as new_to_brand_purchases per impression                                                                                                       |
| `off_amazon_conversions`                    | Number of conversions that occurred off Amazon attributed to an ad view or click                                                                                                   |
| `off_amazon_conversion_rate`                | Off Amazon conversion rate defined as off_amazon_conversions per impression                                                                                                       |
| `pixel_conversions`                         | Total pixel conversions in the frequency bucket                                                                                                                                   |
| `pixel_conversion_rate`                     | Pixel conversion rate defined as pixel_conversions per impression                                                                                                                 |
| `digital_subscription_signup_purchases`     | New PVC subscription sign-ups(free and paid), for promoted ASINs, attributed to both views and clicks                                                                             |
| `digital_subscription_signup_purchase_rate` | Digital subscription signup purchase rate defined as digital_subscription_signup_purchases per impression                                                                         |
| `units_sold`                                | Quantity of promoted products purchased by customers                                                                                                                              |
| `total_units_sold`                          | Total quantity of promoted and brand halo ASINs (ASINs from the same brand) purchased by customers                                                                                |
| `new_to_brand_units_sold`                   | Quantity of promoted products purchased by new to brand customers                                                                                                                 |
| `new_to_brand_total_units_sold`             | Total quantity of promoted and brand halo ASINs (ASINs from the same brand) purchased by new to brand customers                                                                   |
| `product_sales`                             | Sales (in local currency) of promoted ASINs                                                                                                                                       |
| `total_product_sales`                       | Total sales (in local currency) of promoted and brand halo ASINs (ASINs from the same brand)                                                                                      |
| `new_to_brand_product_sales`                | Sales (in local currency) of promoted ASINs from new to brand customers                                                                                                           |
| `new_to_brand_total_product_sales`          | Total sales(in local currency) of promoted and brand halo ASINs (ASINs from the same brand) from new to brand customers                                                           |
| `off_amazon_conversion_value`               | Value of off Amazon non-purchase conversions                                                                                                                                      |
| `off_amazon_product_sales`                  | Sales amount for off-Amazon purchase conversions                                                                                                                                  |
| `total_detail_page_view`                    | Total detail page views of promoted and brand halo ASINs (ASINs from the same brand)                                                                                              |
| `total_add_to_cart`                         | Total Add to carts of promoted and brand halo ASINs (ASINs from the same brand)                                                                                                   |
| `total_purchases`                           | Total purchases of promoted and brand halo ASINs (ASINs from the same brand)                                                                                                      |
| `new_to_brand_total_purchases`              | Total purchases of promoted and brand halo ASINs (ASINs from the same brand) from new to brand customers                                                                          |


### Insights and data interpretation

Finding an optimal frequency is not a one size fits all solution and depends on various factors such as campaign goals (brand awareness vs conversion), length of a campaign, size of the audience, new product launch, and type of product. 

In this section, you can find two use cases along with instructions for how to derive insights from the query results:

* Use Case 1: Reach and impressions
* Use Case 2: On-Amazon and off-Amazon performance metrics

**Use Case 1: Reach and impressions**

Data below is for illustration purpose only. Your results will vary.

| frequency_bucket | reach   | impressions | % reach  | % impressions |
|------------------|---------|-------------|----------|---------------|
| 1                | 1194587 | 1194577     | 30.8%    | 2.8%          |
| 2                | 523788  | 1047576     | 13.5%    | 2.5%          |
| 3                | 325897  | 977691      | 8.4%     | 2.3%          |
| 4                | 235230  | 940920      | 6.1%     | 2.2%          |
| 5                | 224828  | 1124140     | 5.8%     | 2.6%          |
| 6                | 155666  | 933996      | 4.0%     | 2.2%          |
| 7                | 123476  | 864332      | 3.2%     | 2.0%          |
| 8                | 96241   | 769928      | 2.5%     | 1.8%          |
| 9                | 80465   | 724185      | 2.1%     | 1.7%          |
| 10               | 76242   | 762420      | 2.0%     | 1.8%          |
| 11               | 60949   | 670439      | 1.6%     | 1.6%          |
| 12               | 54191   | 650292      | 1.4%     | 1.5%          |
| 13               | 46763   | 607919      | 1.2%     | 1.4%          |
| 14               | 43480   | 608720      | 1.1%     | 1.4%          |
| 15               | 38040   | 570600      | 1.0%     | 1.3%          |
| 16               | 33871   | 541936      | 0.9%     | 1.3%          |
| 17               | 30648   | 521016      | 0.8%     | 1.2%          |
| 18               | 27786   | 500148      | 0.7%     | 1.2%          |
| 19               | 25666   | 487654      | 0.7%     | 1.1%          |
| 20               | 23961   | 479220      | 0.6%     | 1.1%          |
| 21               | 21878   | 459438      | 0.6%     | 1.1%          |
| 22               | 19976   | 439472      | 0.5%     | 1.0%          |
| 23               | 18504   | 425592      | 0.5%     | 1.0%          |
| 24               | 17092   | 410208      | 0.4%     | 1.0%          |
| 25+              | 376931  | 25995762    | 9.7%     | 60.9%         |
| Total            | 3876156 | 42708181    |          |               |



**What is the average frequency or impression per user?**

Create the **Total** row, which are the sums of the “reach” and “impressions” columns respectively. Next, divide the sum of impressions by sum of reach. Using the example results above, you can then calculate the average frequency or ad impressions served per user:

average frequency = sum of impressions/sum of reach = 42708181 / 3876156 = 11 impressions per user

**What was the share of users and impressions across ad frequencies?**

To calculate this, add two columns: 

* One for calculating percentage of reach across each ad frequency (% reach) 
* One for calculating percentage of impressions across each ad frequency (% impressions)

In the example results above, 30.8% of users were served 2.8% of ad impressions at an ad frequency of 1.

You can also use a bar chart to look at the distribution of users and impressions across each frequency bucket.

![share of reach chart](https://m.media-amazon.com/images/G/01/adsamciql/optimal_freq_share_of_reach-1.png)


![share of impressions chart](https://m.media-amazon.com/images/G/01/adsamciql/optimal_freq_share_of_impressions-2_sm.png)


**Use Case 2: On-Amazon and off-Amazon performance metrics**
Data below is for illustration purpose only. Your results will vary.

| frequency_bucket | reach   | impressions | cost        | clicks | click_through_rate | branded_search_rate | detail_page_view_rate | add_to_cart_rate | purchase_rate | new_to_brand_purchase_rate |
|------------------|---------|-------------|-------------|--------|--------------------|---------------------|-----------------------|------------------|---------------|----------------------------|
| 1                | 1194587 | 1194577     | 34800.18957 | 3726   | 0.31%              | 1.59%               | 0.08%                 | 0.01%            | 0.03%         | 0.17%                      |
| 2                | 523788  | 1047576     | 30339.4102  | 2269   | 0.22%              | 1.11%               | 0.10%                 | 0.00%            | 0.03%         | 0.11%                      |
| 3                | 325897  | 977691      | 28155.7962  | 1713   | 0.18%              | 0.82%               | 0.10%                 | 0.00%            | 0.10%         | 0.09%                      |
| 4                | 235230  | 940920      | 26955.78828 | 1338   | 0.14%              | 0.72%               | 0.09%                 | 0.00%            | 0.03%         | 0.03%                      |
| 5                | 224828  | 1124140     | 32819.96669 | 1321   | 0.12%              | 0.56%               | 0.08%                 | 0.00%            | 0.04%         | 0.07%                      |
| 6                | 155666  | 933996      | 26060.70388 | 1009   | 0.11%              | 0.63%               | 0.09%                 | 0.00%            | 0.05%         | 0.05%                      |
| 7                | 123476  | 864332      | 24431.95668 | 870    | 0.10%              | 0.50%               | 0.08%                 | 0.00%            | 0.06%         | 0.01%                      |
| 8                | 96241   | 769928      | 21744.61437 | 766    | 0.10%              | 0.52%               | 0.08%                 | 0.00%            | 0.03%         | 0.05%                      |
| 9                | 80465   | 724185      | 20409.5623  | 682    | 0.09%              | 0.44%               | 0.08%                 | 0.00%            | 0.06%         | 0.07%                      |
| 10               | 76242   | 762420      | 21634.54432 | 650    | 0.09%              | 0.42%               | 0.08%                 | 0.00%            | 0.04%         | 0.05%                      |
| 11               | 60949   | 670439      | 18905.89591 | 565    | 0.08%              | 0.40%               | 0.08%                 | 0.01%            | 0.03%         | 0.01%                      |
| 12               | 54191   | 650292      | 18116.00754 | 533    | 0.08%              | 0.40%               | 0.08%                 | 0.00%            | 0.03%         | 0.03%                      |
| 13               | 46763   | 607919      | 17018.58083 | 455    | 0.07%              | 0.36%               | 0.09%                 | 0.00%            | 0.07%         | 0.10%                      |
| 14               | 43480   | 608720      | 17005.8741  | 471    | 0.08%              | 0.37%               | 0.07%                 | 0.00%            | 0.02%         | 0.02%                      |
| 15               | 38040   | 570600      | 15980.69493 | 425    | 0.07%              | 0.36%               | 0.07%                 | 0.01%            | 0.04%         | 0.07%                      |
| 16               | 33871   | 541936      | 15158.18048 | 426    | 0.08%              | 0.39%               | 0.08%                 | 0.00%            | 0.04%         | 0.15%                      |
| 17               | 30648   | 521016      | 14541.41838 | 371    | 0.07%              | 0.38%               | 0.08%                 | 0.01%            | 0.04%         | 0.06%                      |
| 18               | 27786   | 500148      | 13957.42665 | 387    | 0.08%              | 0.32%               | 0.07%                 | 0.00%            | 0.04%         | 0.08%                      |
| 19               | 25666   | 487654      | 13608.80248 | 344    | 0.07%              | 0.33%               | 0.08%                 | 0.01%            | 0.04%         | 0.04%                      |
| 20               | 23961   | 479220      | 13361.24085 | 338    | 0.07%              | 0.26%               | 0.08%                 | 0.00%            | 0.00%         | 0.00%                      |
| 21               | 21878   | 459438      | 12806.3433  | 306    | 0.07%              | 0.30%               | 0.07%                 | 0.01%            | 0.07%         | 0.04%                      |
| 22               | 19976   | 439472      | 12234.97927 | 261    | 0.06%              | 0.27%               | 0.07%                 | 0.01%            | 0.07%         | 0.09%                      |
| 23               | 18504   | 425592      | 11875.12748 | 270    | 0.06%              | 0.25%               | 0.08%                 | 0.01%            | 0.05%         | 0.05%                      |
| 24               | 17092   | 410208      | 11424.90098 | 280    | 0.07%              | 0.25%               | 0.07%                 | 0.00%            | 0.02%         | 0.02%                      |
| 25+              | 376931  | 25995762    | 732351.9107 | 15558  | 0.06%              | 0.10%               | 0.04%                 | 0.00%            | 0.03%         | 0.03%                      |


**Is click-through rate (CTR) declining when a user is served an ad multiple times?**

We can plot click-through rate (CTR) across frequencies to explore CTR trends. In this case below, we see that CTR declines as frequencies increases with the highest CTR when an ad is served once and lowest CTR when ad is served 22 times to the same user.

![click through rate chart](https://m.media-amazon.com/images/G/01/adsamciql/optimal_freq_click_through_rate-3_sm.png)

**Which frequency drives the highest purchase rate?**

We can plot purchase rate (PR) across frequencies to see how it trends. In the example chart below, we see that purchase rate spikes when frequency is 3 and then declines. We see a few more spikes in purchase rate as frequency increases (for example, at frequencies 7 and 9). But, for each of these frequencies, we have less than 5% users exposed to the ad.  Keeping this in mind, we can conclude that the optimal frequency to drive highest purchase rate is at 3. When you look for optimal frequency, keep in mind the share of users in each frequency bucket. The second highest purchase rate is at frequency 22. At this frequency, however, there are only 0.5% users exposed.

![purchase rate chart](https://m.media-amazon.com/images/G/01/adsamciql/optimal_freq_purchase_rate-4_sm.png)

**How did the ad frequencies drive performance metrics across the purchase funnel for DSP campaigns?** 

Depending on the funnel you want to analyze, you can choose the metrics and plot it against the frequency buckets. To exemplify, if you want to understand how frequency drives:

* Brand awareness, you can look at `branded_search_rate`
* Consideration, you can look at `detail_page_view_rate or total_detail_page_view_rate`
* Purchase intent, you can look at `purchase_rate or total_purchase_rate`

Once you narrow down on the above, you can plot a chart with the respective metric(s) across frequencies and share of users and identify your optimal frequency.

**Which frequency helps drive both promoted and brand halo products detail page view rate?**

Total detail page views contains detail page views across promoted and brand halo products (refer to Metric definitions above). We can plot this metric across frequencies as shown below. In this case, we see that total detail page view rate spikes when frequency is 3 and then declines. Again, when you choose the optimal frequency you should also consider the percentage of users across each frequency.

![total dpv](https://m.media-amazon.com/images/G/01/adsamciql/optimal_freq_total_dpv-5_sm.png)

**Which frequencies have above average new-to-brand purchase rate?**

For this, let’s look at `new_to_brand_purchases` and `new_to_brand_purchase_rate` across frequency buckets. 

First, we calculate the average `new_to_brand_purchase_rate` by dividing the sum of `new_to_brand_purchases` over sum of impressions:
`average new_to_brand_purchase_rate = sum(new_to_brand_purchases)/sum(impressions)` = 7283/42708181 = 0.02%. 

Second, we find the frequency buckets that have `new_to_brand_purchase_rate` more than the average of 0.02%. In this example, for frequency ranges of 7-13 the New-to-brand purchase rate is above the average of 0.02%.

| frequency_bucket | reach   | impressions | % reach | new_to_brand_purchases | new_to_brand_purchase_rate |
|------------------|---------|-------------|---------|------------------------|----------------------------|
| 1                | 1194587 | 1194577     | 30.8%   | 127                    | 0.01%                      |
| 2                | 523788  | 1047576     | 13.5%   | 140                    | 0.01%                      |
| 3                | 325897  | 977691      | 8.4%    | 144                    | 0.01%                      |
| 4                | 235230  | 940920      | 6.1%    | 140                    | 0.01%                      |
| 5                | 224828  | 1124140     | 5.8%    | 276                    | 0.02%                      |
| 6                | 155666  | 933996      | 4.0%    | 231                    | 0.02%                      |
| 7                | 123476  | 864332      | 3.2%    | 729                    | 0.08%                      |
| 8                | 96241   | 769928      | 2.5%    | 881                    | 0.11%                      |
| 9                | 80465   | 724185      | 2.1%    | 920                    | 0.13%                      |
| 10               | 76242   | 762420      | 2.0%    | 689                    | 0.09%                      |
| 11               | 60949   | 670439      | 1.6%    | 628                    | 0.09%                      |
| 12               | 54191   | 650292      | 1.4%    | 580                    | 0.09%                      |
| 13               | 46763   | 607919      | 1.2%    | 555                    | 0.09%                      |
| 14               | 43480   | 608720      | 1.1%    | 121                    | 0.02%                      |
| 15               | 38040   | 570600      | 1.0%    | 117                    | 0.02%                      |
| 16               | 33871   | 541936      | 0.9%    | 112                    | 0.02%                      |
| 17               | 30648   | 521016      | 0.8%    | 92                     | 0.02%                      |
| 18               | 27786   | 500148      | 0.7%    | 106                    | 0.02%                      |
| 19               | 25666   | 487654      | 0.7%    | 91                     | 0.02%                      |
| 20               | 23961   | 479220      | 0.6%    | 98                     | 0.02%                      |
| 21               | 21878   | 459438      | 0.6%    | 110                    | 0.02%                      |
| 22               | 19976   | 439472      | 0.5%    | 105                    | 0.02%                      |
| 23               | 18504   | 425592      | 0.5%    | 91                     | 0.02%                      |
| 24               | 17092   | 410208      | 0.4%    | 72                     | 0.02%                      |
| 25+              | 376931  | 25995762    | 9.7%    | 128                    | 0.00%                      |
| Total            |         | 42708181    |         | 7283                   |                            |


![new to brand chart](https://m.media-amazon.com/images/G/01/adsamciql/optimal_freq_ntb-6_sm.png)

**At what frequency is digital subscription signup purchases highest?**

Data below is for illustration purpose only. Your results will vary.

| frequency_bucket | reach  | % of reach (users) | digital_subscription_signup_purchase_rate |
|------------------|--------|--------------------|-------------------------------------------|
| 1                | 895781 | 26.3%              | 0.04%                                     |
| 2                | 333788 | 9.8%               | 0.10%                                     |
| 3                | 343790 | 10.1%              | 0.12%                                     |
| 4                | 235230 | 6.9%               | 0.04%                                     |
| 5                | 224828 | 6.6%               | 0.04%                                     |
| 6                | 155666 | 4.6%               | 0.03%                                     |
| 7                | 123476 | 3.6%               | 0.03%                                     |
| 8                | 96241  | 2.8%               | 0.02%                                     |
| 9                | 80465  | 2.4%               | 0.05%                                     |
| 10               | 76242  | 2.2%               | 0.03%                                     |
| 11               | 60949  | 1.8%               | 0.01%                                     |
| 12               | 54191  | 1.6%               | 0.01%                                     |
| 13               | 46763  | 1.4%               | 0.01%                                     |
| 14               | 43480  | 1.3%               | 0.01%                                     |
| 15               | 38040  | 1.1%               | 0.01%                                     |
| 16               | 33871  | 1.0%               | 0.01%                                     |
| 17               | 30648  | 0.9%               | 0.01%                                     |
| 18               | 27786  | 0.8%               | 0.01%                                     |
| 19               | 25666  | 0.8%               | 0.01%                                     |
| 20               | 23961  | 0.7%               | 0.01%                                     |
| 21               | 21878  | 0.6%               | 0.01%                                     |
| 22               | 19976  | 0.6%               | 0.01%                                     |
| 23               | 18504  | 0.5%               | 0.01%                                     |
| 24               | 17092  | 0.5%               | 0.01%                                     |
| 25+              | 376931 | 11.1%              | 0.01%                                     |


We can plot digital_subscription_signup_purchase_rate across frequencies. In this case below, we see that `digital_subscription_signup_purchase_rate` peaks at a frequency of 3 with over 10.1% in share of users.

![digital subscription signups](https://m.media-amazon.com/images/G/01/adsamciql/optimal_freq_digital_sub_signup-7_sm.png)

## Queries

### Exploratory query

**Exploratory query: Identify Amazon DSP and/or sponsored ads campaigns**

Use the exploratory query supplied below to identify the campaigns you want to include for the frequency analysis. Make sure you select the same query window that you will use in the main query below. 


```
-- Instructional Query: Exploratory Query to identify campaigns ID string values for sponsored ads and Amazon DSP --
-- AD PRODUCTS: Amazon DSP and/or sponsored ads
SELECT
'DSP' AS ad_product_type,
advertiser,
campaign_id_string,
campaign,
campaign_start_date,
campaign_end_date,
SUM(impressions) AS impressions
FROM
dsp_impressions
GROUP BY
1,
2,
3,
4,
5,
6

UNION ALL

SELECT
ad_product_type,
advertiser,
campaign_id_string,
campaign,
campaign_start_date,
campaign_end_date,
SUM(impressions) AS impressions
FROM
sponsored_ads_traffic
GROUP BY
1,
2,
3,
4,
5,
6

```

### Query template: Optimal frequency deep dive


```
-- Instructional Query: Optimal Frequency Deep Dive
-- AD PRODUCTS: Amazon DSP and/or Sponsored Ads
/*
------- Customization Instructions -------
 1) Run the exploratory query to identify the campaign ID string values (for Amazon DSP and sponsored ads).
 2) REQUIRED UPDATE: Custom parameters: 
   2a) Add either Amazon DSP campaign_id_string values, or sponsored ads campaign_id_string values, or both. 
   2b) Increase or decrease the frequency cap
 3) OPTIONAL UPDATE [1 OF 1]: Choose metrics and their order for display and delete any unneeded metrics
*/
WITH 
-- collecting metrics across data sources
events AS 
(
--impressions and cost from dsp_impressions
SELECT
    user_id,
    sum(impressions) AS impressions,
    0 AS clicks,
    sum(total_cost/100000) AS total_cost,
    0 AS total_detail_page_view,
    0 AS total_add_to_cart,
    0 AS total_purchases,
    0 AS total_units_sold,
    0 AS total_product_sales,
    0 AS new_to_brand_total_purchases,
    0 AS new_to_brand_total_units_sold,
    0 AS new_to_brand_total_product_sales,
    0 AS branded_searches,
    0 AS detail_page_view,
    0 AS add_to_cart,
    0 AS purchases,
    0 AS units_sold,
    0 AS product_sales,
    0 AS new_to_brand_purchases,
    0 AS new_to_brand_units_sold,
    0 AS new_to_brand_product_sales,
    0 AS off_amazon_conversions,
    0 AS off_amazon_conversion_value,
    0 AS off_amazon_product_sales,
    0 AS pixel_conversions,
    0 AS digital_subscription_signup_purchases
FROM
    dsp_impressions
    where user_id IS NOT NULL 
    and
    (
     ARRAY_CONTAINS(CUSTOM_PARAMETER('dsp_campaign_ids'), campaign_id_string)
     OR CUSTOM_PARAMETER('dsp_campaign_ids') IS NULL
     OR CARDINALITY(CUSTOM_PARAMETER('dsp_campaign_ids')) = 0
     )  
  GROUP BY
    1
 
UNION ALL 
--clicks from dsp_clicks
SELECT
    user_id,
    0 AS impressions,
    sum(clicks) AS clicks,
    0 AS total_cost,
    0 AS total_detail_page_view,
    0 AS total_add_to_cart,
    0 AS total_purchases,
    0 AS total_units_sold,
    0 AS total_product_sales,
    0 AS new_to_brand_total_purchases,
    0 AS new_to_brand_total_units_sold,
    0 AS new_to_brand_total_product_sales,
    0 AS branded_searches,
    0 AS detail_page_view,
    0 AS add_to_cart,
    0 AS purchases,
    0 AS units_sold,
    0 AS product_sales,
    0 AS new_to_brand_purchases,
    0 AS new_to_brand_units_sold,
    0 AS new_to_brand_product_sales,
    0 AS off_amazon_conversions,
    0 AS off_amazon_conversion_value,
    0 AS off_amazon_product_sales,
    0 AS pixel_conversions,
    0 AS digital_subscription_signup_purchases
FROM
    dsp_clicks
    where user_id IS NOT NULL 
    and
    (
     ARRAY_CONTAINS(CUSTOM_PARAMETER('dsp_campaign_ids'), campaign_id_string)
     OR CUSTOM_PARAMETER('dsp_campaign_ids') IS NULL
     OR CARDINALITY(CUSTOM_PARAMETER('dsp_campaign_ids')) = 0
     )  
  GROUP BY
    1
 
UNION ALL
--impressions,clicks and cost from sponsored_ads_traffic
SELECT
    user_id,
    sum(impressions) AS impressions,
    sum(clicks) AS clicks,
    sum(spend/100000000) AS total_cost,
    0 AS total_detail_page_view,
    0 AS total_add_to_cart,
    0 AS total_purchases,
    0 AS total_units_sold,
    0 AS total_product_sales,
    0 AS new_to_brand_total_purchases,
    0 AS new_to_brand_total_units_sold,
    0 AS new_to_brand_total_product_sales,
    0 AS branded_searches,
    0 AS detail_page_view,
    0 AS add_to_cart,
    0 AS purchases,
    0 AS units_sold,
    0 AS product_sales,
    0 AS new_to_brand_purchases,
    0 AS new_to_brand_units_sold,
    0 AS new_to_brand_product_sales,
    0 AS off_amazon_conversions,
    0 AS off_amazon_conversion_value,
    0 AS off_amazon_product_sales,
    0 AS pixel_conversions,
    0 AS digital_subscription_signup_purchases
FROM
    sponsored_ads_traffic
    where user_id IS NOT NULL 
    and
    (
  ARRAY_CONTAINS(CUSTOM_PARAMETER('sa_campaign_ids'), campaign_id_string)
  OR CUSTOM_PARAMETER('sa_campaign_ids') IS NULL
  OR CARDINALITY(CUSTOM_PARAMETER('sa_campaign_ids')) = 0
 )  
  GROUP BY
    1
UNION ALL
--dpv from amazon_attributed_events_by_traffic_time
SELECT
    user_id,
    0 AS impressions,
    0 AS clicks,
    0 AS total_cost,
    sum(total_detail_page_view) AS total_detail_page_view,
    0 AS total_add_to_cart,
    0 AS total_purchases,
    0 AS total_units_sold,
    0 AS total_product_sales,
    0 AS new_to_brand_total_purchases,
    0 AS new_to_brand_total_units_sold,
    0 AS new_to_brand_total_product_sales,
    0 AS branded_searches,
    sum(detail_page_view) AS detail_page_view,
    0 AS add_to_cart,
    0 AS purchases,
    0 AS units_sold,
    0 AS product_sales,
    0 AS new_to_brand_purchases,
    0 AS new_to_brand_units_sold,
    0 AS new_to_brand_product_sales,
    0 AS off_amazon_conversions,
    0 AS off_amazon_conversion_value,
    0 AS off_amazon_product_sales,
    0 AS pixel_conversions,
    0 AS digital_subscription_signup_purchases
FROM
    amazon_attributed_events_by_traffic_time
    where user_id IS NOT NULL 
    and
    (
      (
       ARRAY_CONTAINS(CUSTOM_PARAMETER('dsp_campaign_ids'), campaign_id_string)
       OR CUSTOM_PARAMETER('dsp_campaign_ids') IS NULL
       OR CARDINALITY(CUSTOM_PARAMETER('dsp_campaign_ids')) = 0
       )  
    OR
       (
       ARRAY_CONTAINS(CUSTOM_PARAMETER('sa_campaign_ids'), campaign_id_string)
       OR CUSTOM_PARAMETER('sa_campaign_ids') IS NULL
       OR CARDINALITY(CUSTOM_PARAMETER('sa_campaign_ids')) = 0
       )  
    )
    
    and total_detail_page_view >0
   GROUP BY
    1
UNION ALL
--atc from amazon_attributed_events_by_traffic_time
SELECT
    user_id,
    0 AS impressions,
    0 AS clicks,
    0 AS total_cost,
    0 AS total_detail_page_view,
    sum(total_add_to_cart) AS total_add_to_cart,
    0 AS total_purchases,
    0 AS total_units_sold,
    0 AS total_product_sales,
    0 AS new_to_brand_total_purchases,
    0 AS new_to_brand_total_units_sold,
    0 AS new_to_brand_total_product_sales,
    0 AS branded_searches,
    0 AS detail_page_view,
    sum(add_to_cart) AS add_to_cart,
    0 AS purchases,
    0 AS units_sold,
    0 AS product_sales,
    0 AS new_to_brand_purchases,
    0 AS new_to_brand_units_sold,
    0 AS new_to_brand_product_sales,
    0 AS off_amazon_conversions,
    0 AS off_amazon_conversion_value,
    0 AS off_amazon_product_sales,
    0 AS pixel_conversions,
    0 AS digital_subscription_signup_purchases
FROM
    amazon_attributed_events_by_traffic_time
    where user_id IS NOT NULL 
    and
    (
      (
       ARRAY_CONTAINS(CUSTOM_PARAMETER('dsp_campaign_ids'), campaign_id_string)
       OR CUSTOM_PARAMETER('dsp_campaign_ids') IS NULL
       OR CARDINALITY(CUSTOM_PARAMETER('dsp_campaign_ids')) = 0
       )  
    OR
       (
       ARRAY_CONTAINS(CUSTOM_PARAMETER('sa_campaign_ids'), campaign_id_string)
       OR CUSTOM_PARAMETER('sa_campaign_ids') IS NULL
       OR CARDINALITY(CUSTOM_PARAMETER('sa_campaign_ids')) = 0
       )  
    )
    and total_add_to_cart>0
  GROUP BY
    1
 
 UNION ALL
--conversions-purchases from amazon_attributed_events_by_traffic_time
SELECT
    user_id,
    0 AS impressions,
    0 AS clicks,
    0 AS total_cost,
    0 AS total_detail_page_view,
    0 AS total_add_to_cart,
    sum(total_purchases) AS total_purchases,
    sum(total_units_sold) AS total_units_sold,
    sum(total_product_sales) AS total_product_sales,
    0 AS new_to_brand_total_purchases,
    0 AS new_to_brand_total_units_sold,
    0 AS new_to_brand_total_product_sales,
    0 AS branded_searches,
    0 AS detail_page_view,
    0 AS add_to_cart,
    sum(purchases) AS purchases,
    sum(units_sold) AS units_sold,
    sum(product_sales) AS product_sales,
    0 AS new_to_brand_purchases,
    0 AS new_to_brand_units_sold,
    0 AS new_to_brand_product_sales,
    0 AS off_amazon_conversions,
    0 AS off_amazon_conversion_value,
    0 AS off_amazon_product_sales,
    0 AS pixel_conversions,
    0 AS digital_subscription_signup_purchases
FROM
    amazon_attributed_events_by_traffic_time
    where user_id IS NOT NULL 
    and
    (
      (
       ARRAY_CONTAINS(CUSTOM_PARAMETER('dsp_campaign_ids'), campaign_id_string)
       OR CUSTOM_PARAMETER('dsp_campaign_ids') IS NULL
       OR CARDINALITY(CUSTOM_PARAMETER('dsp_campaign_ids')) = 0
       )  
    OR
       (
       ARRAY_CONTAINS(CUSTOM_PARAMETER('sa_campaign_ids'), campaign_id_string)
       OR CUSTOM_PARAMETER('sa_campaign_ids') IS NULL
       OR CARDINALITY(CUSTOM_PARAMETER('sa_campaign_ids')) = 0
       )  
    )
    and total_purchases>0
  GROUP BY
    1
 
UNION ALL
--NTB:conversions from amazon_attributed_events_by_traffic_time
SELECT
    user_id,
    0 AS impressions,
    0 AS clicks,
    0 AS total_cost,
    0 AS total_detail_page_view,
    0 AS total_add_to_cart,
    0 AS total_purchases,
    0 AS total_units_sold,
    0 AS total_product_sales,
    sum(new_to_brand_total_purchases) AS new_to_brand_total_purchases,
    sum(new_to_brand_total_units_sold) AS new_to_brand_total_units_sold,
    sum(new_to_brand_total_product_sales) AS new_to_brand_total_product_sales,
    0 AS branded_searches,
    0 AS detail_page_view,
    0 AS add_to_cart,
    0 AS purchases,
    0 AS units_sold,
    0 AS product_sales,
    sum(new_to_brand_purchases) AS new_to_brand_purchases,
    sum(new_to_brand_units_sold) AS new_to_brand_units_sold,
    sum(new_to_brand_product_sales) AS new_to_brand_product_sales,
    0 AS off_amazon_conversions,
    0 AS off_amazon_conversion_value,
    0 AS off_amazon_product_sales,
    0 AS pixel_conversions,
    0 AS digital_subscription_signup_purchases
FROM
    amazon_attributed_events_by_traffic_time
    where user_id IS NOT NULL  
    and
    (
      (
       ARRAY_CONTAINS(CUSTOM_PARAMETER('dsp_campaign_ids'), campaign_id_string)
       OR CUSTOM_PARAMETER('dsp_campaign_ids') IS NULL
       OR CARDINALITY(CUSTOM_PARAMETER('dsp_campaign_ids')) = 0
       )  
    OR
       (
       ARRAY_CONTAINS(CUSTOM_PARAMETER('sa_campaign_ids'), campaign_id_string)
       OR CUSTOM_PARAMETER('sa_campaign_ids') IS NULL
       OR CARDINALITY(CUSTOM_PARAMETER('sa_campaign_ids')) = 0
       )  
    )
    and total_purchases>0
    and new_to_brand = TRUE
  GROUP BY
    1
 
UNION ALL
--branded_searches from amazon_attributed_events_by_traffic_time
SELECT
    user_id,
    0 AS impressions,
    0 AS clicks,
    0 AS total_cost,
    0 AS total_detail_page_view,
    0 AS total_add_to_cart,
    0 AS total_purchases,
    0 AS total_units_sold,
    0 AS total_product_sales,
    0 AS new_to_brand_total_purchases,
    0 AS new_to_brand_total_units_sold,
    0 AS new_to_brand_total_product_sales,
    sum(conversions) AS branded_searches,
    0 AS detail_page_view,
    0 AS add_to_cart,
    0 AS purchases,
    0 AS units_sold,
    0 AS product_sales,
    0 AS new_to_brand_purchases,
    0 AS new_to_brand_units_sold,
    0 AS new_to_brand_product_sales,
    0 AS off_amazon_conversions,
    0 AS off_amazon_conversion_value,
    0 AS off_amazon_product_sales,
    0 AS pixel_conversions,
    0 AS digital_subscription_signup_purchases
FROM
    amazon_attributed_events_by_traffic_time
    where user_id IS NOT NULL 
    and tracked_item SIMILAR TO '^keyword.*'
    and
    (
      (
       ARRAY_CONTAINS(CUSTOM_PARAMETER('dsp_campaign_ids'), campaign_id_string)
       OR CUSTOM_PARAMETER('dsp_campaign_ids') IS NULL
       OR CARDINALITY(CUSTOM_PARAMETER('dsp_campaign_ids')) = 0
       )  
    OR
       (
       ARRAY_CONTAINS(CUSTOM_PARAMETER('sa_campaign_ids'), campaign_id_string)
       OR CUSTOM_PARAMETER('sa_campaign_ids') IS NULL
       OR CARDINALITY(CUSTOM_PARAMETER('sa_campaign_ids')) = 0
       )  
    )
    and conversions>0  
  GROUP BY
    1
 
UNION ALL
--off Amazon conversions from amazon_attributed_events_by_traffic_time
SELECT
    user_id,
    0 AS impressions,
    0 AS clicks,
    0 AS total_cost,
    0 AS total_detail_page_view,
    0 AS total_add_to_cart,
    0 AS total_purchases,
    0 AS total_units_sold,
    0 AS total_product_sales,
    0 AS new_to_brand_total_purchases,
    0 AS new_to_brand_total_units_sold,
    0 AS new_to_brand_total_product_sales,
    0 AS branded_searches,
    0 AS detail_page_view,
    0 AS add_to_cart,
    0 AS purchases,
    0 AS units_sold,
    0 AS product_sales,
    0 AS new_to_brand_purchases,
    0 AS new_to_brand_units_sold,
    0 AS new_to_brand_product_sales,
    sum(conversions) AS off_amazon_conversions,
    sum(off_amazon_conversion_value) AS off_amazon_conversion_value,
    sum(off_amazon_product_sales) AS off_amazon_product_sales,
    0 AS pixel_conversions,
    0 AS digital_subscription_signup_purchases
FROM
    amazon_attributed_events_by_traffic_time
    where user_id IS NOT NULL
    and
      (
       ARRAY_CONTAINS(CUSTOM_PARAMETER('dsp_campaign_ids'), campaign_id_string)
       OR CUSTOM_PARAMETER('dsp_campaign_ids') IS NULL
       OR CARDINALITY(CUSTOM_PARAMETER('dsp_campaign_ids')) = 0
       )  
    and conversion_event_category IN ('off-Amazon') 
    and conversions>0  
  GROUP BY
    1
 
UNION ALL
--Pixel conversions from amazon_attributed_events_by_traffic_time
SELECT
    user_id,
    0 AS impressions,
    0 AS clicks,
    0 AS total_cost,
    0 AS total_detail_page_view,
    0 AS total_add_to_cart,
    0 AS total_purchases,
    0 AS total_units_sold,
    0 AS total_product_sales,
    0 AS new_to_brand_total_purchases,
    0 AS new_to_brand_total_units_sold,
    0 AS new_to_brand_total_product_sales,
    0 AS branded_searches,
    0 AS detail_page_view,
    0 AS add_to_cart,
    0 AS purchases,
    0 AS units_sold,
    0 AS product_sales,
    0 AS new_to_brand_purchases,
    0 AS new_to_brand_units_sold,
    0 AS new_to_brand_product_sales,
    0 AS off_amazon_conversions,
    0 AS off_amazon_conversion_value,
    0 AS off_amazon_product_sales,
    sum(conversions) AS pixel_conversions,
    0 AS digital_subscription_signup_purchases
FROM
    amazon_attributed_events_by_traffic_time
    where user_id IS NOT NULL 
    and
    (
       ARRAY_CONTAINS(CUSTOM_PARAMETER('dsp_campaign_ids'), campaign_id_string)
       OR CUSTOM_PARAMETER('dsp_campaign_ids') IS NULL
       OR CARDINALITY(CUSTOM_PARAMETER('dsp_campaign_ids')) = 0
       )  
    and conversion_event_category IN ('pixel') 
    and conversions>0  
  GROUP BY
    1
UNION ALL
--Digital signups from amazon_attributed_events_by_traffic_time
SELECT
    user_id,
    0 AS impressions,
    0 AS clicks,
    0 AS total_cost,
    0 AS total_detail_page_view,
    0 AS total_add_to_cart,
    0 AS total_purchases,
    0 AS total_units_sold,
    0 AS total_product_sales,
    0 AS new_to_brand_total_purchases,
    0 AS new_to_brand_total_units_sold,
    0 AS new_to_brand_total_product_sales,
    0 AS branded_searches,
    0 AS detail_page_view,
    0 AS add_to_cart,
    0 AS purchases,
    0 AS units_sold,
    0 AS product_sales,
    0 AS new_to_brand_purchases,
    0 AS new_to_brand_units_sold,
    0 AS new_to_brand_product_sales,
    0 AS off_amazon_conversions,
    0 AS off_amazon_conversion_value,
    0 AS off_amazon_product_sales,
    0 AS pixel_conversions,
    sum(digital_subscription_signup_purchases) AS digital_subscription_signup_purchases
FROM
    amazon_attributed_events_by_traffic_time
    where user_id IS NOT NULL 
    and
      (
       ARRAY_CONTAINS(CUSTOM_PARAMETER('dsp_campaign_ids'), campaign_id_string)
       OR CUSTOM_PARAMETER('dsp_campaign_ids') IS NULL
       OR CARDINALITY(CUSTOM_PARAMETER('dsp_campaign_ids')) = 0
       )  
    and digital_subscription_signup_purchases>0  
  GROUP BY
    1
),
events_by_user AS (
  SELECT
    user_id,
    IF(
      SUM(impressions) > 0,
      SUM(impressions),
      SUM(purchases)
    ) AS impressions,
    LEAST(
    CASE WHEN SUM(impressions) > 0 
         THEN SUM(impressions) 
         ELSE SUM(purchases) 
    END,
    (CUSTOM_PARAMETER('frequency_cap'))) AS frequency_bucket,
    sum(clicks) AS clicks,
    sum(total_cost) AS total_cost,
    sum(total_detail_page_view) AS total_detail_page_view,
    sum(total_add_to_cart) AS total_add_to_cart,
    sum(total_purchases) AS total_purchases,
    sum(total_units_sold) AS total_units_sold,
    sum(total_product_sales) AS total_product_sales,
    sum(new_to_brand_total_purchases) AS new_to_brand_total_purchases,
    sum(new_to_brand_total_units_sold) AS new_to_brand_total_units_sold,
    sum(new_to_brand_total_product_sales) AS new_to_brand_total_product_sales,
    sum(branded_searches) AS branded_searches,
    sum(detail_page_view) AS detail_page_view,
    sum(add_to_cart) AS add_to_cart,
    sum(purchases) AS purchases,
    sum(units_sold) AS units_sold,
    sum(product_sales) AS product_sales,
    sum(new_to_brand_purchases) AS new_to_brand_purchases,
    sum(new_to_brand_units_sold) AS new_to_brand_units_sold,
    sum(new_to_brand_product_sales) AS new_to_brand_product_sales,
    sum(off_amazon_conversions) AS off_amazon_conversions,
    sum(off_amazon_conversion_value) AS off_amazon_conversion_value,
    sum(off_amazon_product_sales) AS off_amazon_product_sales,
    sum(pixel_conversions) AS pixel_conversions,
    sum(digital_subscription_signup_purchases) AS digital_subscription_signup_purchases
  FROM
    events
  GROUP BY
    user_id
)
-- OPTIONAL UPDATE [1 OF 2]: Choose metrics and their order for display
SELECT
    -- FREQUENCY BUCKETS
    CONCAT(
    frequency_bucket,
    CASE WHEN frequency_bucket = (CUSTOM_PARAMETER('frequency_cap')) THEN '+' ELSE '' END --OPTIONAL UPDATE [2 OF 2]: change the frequency cap as per your use case
    ) AS frequency_bucket,
    -- REACH, IMPRESSIONS, COST
    count(distinct user_id) as reach,
    SUM(impressions) AS impressions,
    SUM(total_cost) AS cost,
    
    --CLICKS and CTR
    SUM(clicks) AS clicks,
    (100.0 * SUM(clicks)/SUM(impressions)) AS click_through_rate,
    --BRANDED SEARCHES
    SUM(branded_searches) AS branded_searches,
    (100.0 * SUM(branded_searches)/SUM(impressions)) AS branded_search_rate,
    --DPVR
    SUM(detail_page_view) AS detail_page_views,
    (100.0 * SUM(detail_page_view)/SUM(impressions)) AS detail_page_view_rate,
    --ATC
    SUM(add_to_cart) AS add_to_carts,
    (100.0 * SUM(add_to_cart)/SUM(impressions)) AS add_to_cart_rate,
    --PR
    SUM(purchases) AS purchases,
    (100.0 * SUM(purchases)/SUM(impressions)) AS purchase_rate,
    --NTB PR
    SUM(new_to_brand_purchases) AS new_to_brand_purchases,
    (100.0 * SUM(new_to_brand_purchases)/SUM(impressions)) AS new_to_brand_purchase_rate,    
    --OFF AMAZON CONVERSIONS
    SUM(off_amazon_conversions) as off_amazon_conversions, 
    (100.0 * SUM(off_amazon_conversions)/SUM(impressions)) AS off_amazon_conversion_rate,
    --PIXEL CONVERSIONS
    SUM(pixel_conversions) as pixel_conversions, 
    (100.0 * SUM(pixel_conversions)/SUM(impressions)) AS pixel_conversion_rate,
    --DIGITAL SUBSCRIPTIONS
    SUM(digital_subscription_signup_purchases) AS digital_subscription_signup_purchases,
    (100.0 * SUM(digital_subscription_signup_purchases)/SUM(impressions)) AS digital_subscription_signup_purchase_rate,    
   
    SUM(units_sold) AS units_sold,
    SUM(product_sales) AS product_sales,
    SUM(new_to_brand_units_sold) AS new_to_brand_units_sold,
    SUM(new_to_brand_product_sales) AS new_to_brand_product_sales,
    SUM(off_amazon_conversion_value) as off_amazon_conversion_value,
    SUM(off_amazon_product_sales) as off_amazon_product_sales,
    
   -- performance metrics for brand halo+promoted asins
    SUM(total_detail_page_view) AS total_detail_page_view,
    SUM(total_add_to_cart) AS total_add_to_cart,
    SUM(total_purchases) AS total_purchases,
    SUM(total_units_sold) AS total_units_sold,
    SUM(total_product_sales) AS total_product_sales,
    SUM(new_to_brand_total_purchases) AS new_to_brand_total_purchases,
    SUM(new_to_brand_total_units_sold) AS new_to_brand_total_units_sold,
    SUM(new_to_brand_total_product_sales) AS new_to_brand_total_product_sales
    
FROM
    events_by_user
    WHERE
        frequency_bucket <> 0
        GROUP BY
        frequency_bucket
```
