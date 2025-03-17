# Ecommerce-project
Analyzed an eCommerce dataset using SQL to query and process necessary information to optimize business strategies and improve decision-making.
# Table of Contents:
1. Introduction and Motivation
2. The goal of creating this project
3. Import raw data
4. Read and explain dataset
5. Data Processing & Exploratory Data Analysis
6. Answer questions and give explain
7. Conclusion

***
# 1. Introduction and Motivation
This project explores an eCommerce dataset stored in Google BigQuery, which contains Google Analytics data from 2017. By writing and executing SQL queries, the analysis focuses on key website metrics such as bounce rate, high-revenue days, user navigation patterns, and product performance.

The objective is to gain insights into business performance, marketing effectiveness, and user behavior to help optimize strategies and improve decision-making. Google BigQuery is used as the primary tool to handle large-scale data efficiently.
# 2. The goal of creating this project
- Overview of website activity
- Bounce rate analysis
- Revenue analysis
- Transactions analysis
- Products analysis
# 3. Import raw data
The eCommerce dataset is available in a public Google BigQuery dataset. To access it:
- Log in to Google Cloud Platform and create a new project.
- Open the BigQuery console and select your project.
- Click "Add Data" > "Search a project" in the navigation panel.
- Enter the project ID: "bigquery-public-data.google_analytics_sample.ga_sessions" and press Enter.
- Open the "ga_sessions_" table to explore the dataset.
# 4. Read and explain dataset
https://support.google.com/analytics/answer/3437719?hl=en

## eCommerce Dataset Schema

| **Field Name**                      | **Data Type** | **Description** |
|-------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `fullVisitorId`                     | STRING       | The unique visitor ID. |
| `date`                               | STRING       | The date of the session in `YYYYMMDD` format. |
| `totals`                             | RECORD       | Contains aggregate session values. |
| `totals.bounces`                     | INTEGER      | Total bounces (1 for a bounced session, otherwise null). |
| `totals.hits`                        | INTEGER      | Total number of hits within the session. |
| `totals.pageviews`                   | INTEGER      | Total number of pageviews within the session. |
| `totals.visits`                      | INTEGER      | Number of sessions (1 for sessions with interaction events, null otherwise). |
| `totals.transactions`                | INTEGER      | Total number of eCommerce transactions within the session. |
| `trafficSource.source`               | STRING       | Traffic source (e.g., search engine, referring hostname, or `utm_source` parameter). |
| `hits`                               | RECORD       | Contains details for any and all types of hits. |
| `hits.eCommerceAction`               | RECORD       | Contains all eCommerce-related hits during the session. |
| `hits.eCommerceAction.action_type`   | STRING       | Action type: `1` = Click through product lists, `2` = Product detail views, `3` = Add to cart, `4` = Remove from cart, `5` = Checkout, `6` = Purchase, `7` = Refund, `8` = Checkout options, `0` = Unknown. |
| `hits.product`                       | RECORD       | Contains Enhanced eCommerce product data. |
| `hits.product.productQuantity`       | INTEGER      | Quantity of the product purchased. |
| `hits.product.productRevenue`        | INTEGER      | Product revenue (`value passed to Analytics * 10^6`, e.g., 2.40 is stored as `2400000`). |
| `hits.product.productSKU`            | STRING       | Product SKU. |
| `hits.product.v2ProductName`         | STRING       | Product Name. |
# 5. Data Processing & Exploratory Data Analysis
```sql
SELECT COUNT(fullVisitorId) row_num,
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`
````
| **row_num**                      | 
|-------------------------------------|
| `467260`| 

## UNNEST hits and products
```sql
SELECT date, 
fullVisitorId,
eCommerceAction.action_type,
product.v2ProductName,
product.productRevenue,
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`,
UNNEST(hits) AS hits,
UNNEST(hits.product) as product
````
| **Row** | **Date**   | **Full Visitor ID**        | **Action Type** | **Product Name**                                    | **Product Revenue** |
|--------|-----------|-------------------------|--------------|---------------------------------------------------|------------------|
| 39     | 2017-07-23 | 7582901463583803669     | 0            | YouTube Men's Vintage Tank                        | null             |
| 40     | 2017-07-23 | 7582901463583803669     | 0            | YouTube Men's Fleece Hoodie Black                | null             |
| 41     | 2017-07-23 | 7582901463583803669     | 0            | YouTube Women's Short Sleeve Tri-blend Badge Tee Grey | null       |
| 42     | 2017-07-23 | 7582901463583803669     | 0            | YouTube Women's Fleece Hoodie Black              | null             |
| 43     | 2017-07-23 | 9097465012770697796     | 0            | Electronics Accessory Pouch                      | null             |
| 44     | 2017-07-23 | 9097465012770697796     | 0            | Recycled Mouse Pad                               | null             |
| 45     | 2017-07-23 | 9097465012770697796     | 0            | Galaxy Screen Cleaning Cloth                    | null             |
| 46     | 2017-07-23 | 9097465012770697796     | 0            | Google Device Stand                             | null             |
| 47     | 2017-07-23 | 7367221886933080170     | 0            | Google Rucksack                                 | null             |
| 48     | 2017-07-23 | 7367221886933080170     | 0            | 25L Classic Rucksack                            | null             |
| 49     | 2017-07-23 | 6870295410177646230     | 0            | Google Snapback Hat Black                       | null             |
| 50     | 2017-07-23 | 6870295410177646230     | 0            | YouTube Twill Cap                               | null             |

# 6. Answer questions and give explain

6.1 Calculate total visit, pageview, transaction for Jan, Feb and March 2017 (order by month)
```sql
SELECT
  FORMAT_DATE('%Y%m', PARSE_DATE('%Y%m%d', date)) AS month 
  ,COUNT(totals.visits) as visits
  ,SUM(totals.pageviews) as pageviews
  ,SUM(totals.transactions) as transactions
FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`
WHERE _table_suffix between '0101' and '0331'
GROUP BY month
ORDER BY month;
````
| **Row** | **Month** | **Visits** | **Pageviews** | **Transactions** |
|--------|---------|----------|-------------|---------------|
| 1      | 2017-01 | 64,694   | 257,708     | 713           |
| 2      | 2017-02 | 62,192   | 233,373     | 733           |
| 3      | 2017-03 | 69,931   | 259,522     | 993           |


The data highlights key trends in website traffic, engagement, and revenue growth over the first quarter of 2017.

### Traffic & Engagement Trends
- Visits and pageviews increased steadily from January (201701) to March (201703), indicating growing user interest.
- The rise in pageviews suggests users are engaging with multiple pages, reflecting strong content relevance and usability.

### Conversion & Revenue Growth
- Transactions and total revenue showed a consistent upward trend.
- A notable spike in March suggests that strategic efforts during this period effectively boosted conversions.

### Potential Growth Influences
- The steady rise in transactions and revenue may be attributed to seasonal trends, marketing campaigns, or website optimizations.
- Identifying the key drivers behind this growth can help replicate success in future initiatives.

### Opportunities for Optimization
- While growth is positive, there are opportunities to enhance user engagement and conversion rates.
- Analyzing user behavior, such as entry and exit points, can help refine the shopping experience.

### Strategic Recommendations
- Further investigate the strategies and optimizations implemented between January and March.
- Leverage insights from this period to guide future marketing and website enhancement efforts.

6.2 Bounce rate per traffic source in July 2017 
````sql
WITH visits_and_bounces AS (
  SELECT
    trafficSource.source AS source
    ,COUNT(totals.visits) AS total_visits
    ,COUNT(totals.bounces) AS total_no_of_bounces
  FROM
    `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`
  GROUP BY
    trafficSource.source)

SELECT
  source
  ,total_visits
  ,total_no_of_bounces
  ,ROUND(CAST(total_no_of_bounces AS DECIMAL)*100.00 / CAST(total_visits AS DECIMAL), 3) AS bounce_rate
FROM
  visits_and_bounces
ORDER BY
  total_visits DESC;
````
| Row | Source                  | Total Visits | Total No. of Bounces | Bounce Rate (%) |
|-----|-------------------------|--------------|----------------------|----------------|
| 1   | google                  | 38,400       | 19,798               | 51.557         |
| 2   | (direct)                | 19,891       | 8,606                | 43.266         |
| 3   | youtube.com             | 6,351        | 4,238                | 66.73          |
| 4   | analytics.google.com    | 1,972        | 1,064                | 53.955         |
| 5   | Partners                | 1,788        | 936                  | 52.349         |
| 6   | m.facebook.com          | 669          | 430                  | 64.275         |
| 7   | google.com              | 368          | 183                  | 49.728         |
| 8   | dfa                     | 302          | 124                  | 41.06          |
| 9   | sites.google.com        | 230          | 97                   | 42.174         |
| 10  | facebook.com            | 191          | 102                  | 53.403         |
| 11  | reddit.com              | 189          | 54                   | 28.571         |
| 12  | qiita.com               | 146          | 72                   | 49.315         |
| 13  | baidu                   | 140          | 84                   | 60             |
| 14  | quora.com               | 140          | 70                   | 50             |
| 15  | bing                    | 111          | 54                   | 48.649         |
| 16  | mail.google.com         | 101          | 25                   | 24.752         |
| 17  | yahoo                   | 100          | 41                   | 41             |
| 18  | blog.golang.org         | 65           | 19                   | 29.231         |
| 19  | l.facebook.com          | 51           | 45                   | 88.235         |
| 20  | groups.google.com       | 50           | 22                   | 44             |
| 21  | t.co                    | 38           | 27                   | 71.053         |
| 22  | google.co.jp            | 36           | 25                   | 69.444         |
| 23  | m.youtube.com           | 34           | 22                   | 64.706         |
| 24  | dealspotr.com           | 26           | 12                   | 46.154         |
| 25  | productforums.google.com| 25           | 21                   | 84             |
| 26  | ask                     | 24           | 16                   | 66.667         |
| 27  | support.google.com      | 24           | 16                   | 66.667         |
| 28  | int.search.tb.ask.com   | 23           | 17                   | 73.913         |
| 29  | optimize.google.com     | 21           | 10                   | 47.619         |
| 30  | docs.google.com         | 20           | 8                    | 40             |
| 31  | lm.facebook.com         | 18           | 9                    | 50             |
| 32  | l.messenger.com         | 17           | 6                    | 35.294         |
| 33  | adwords.google.com      | 16           | 7                    | 43.75          |
| 34  | duckduckgo.com          | 16           | 14                   | 87.5           |
| 35  | google.co.uk            | 15           | 7                    | 46.667         |
| 36  | sashihara.jp            | 14           | 8                    | 57.143         |
| 37  | lunametrics.com         | 13           | 8                    | 61.538         |
| 38  | search.mysearch.com             | 12           | 11                   | 91.667         |
| 39  | outlook.live.com                | 10           | 7                    | 70             |
| 40  | tw.search.yahoo.com             | 10           | 8                    | 80             |
| 41  | phandroid.com                   | 9            | 7                    | 77.778         |
| 42  | connect.googleforwork.com       | 8            | 5                    | 62.5           |
| 43  | plus.google.com                 | 8            | 2                    | 25             |
| 44  | m.yz.sm.cn                      | 7            | 5                    | 71.429         |
| 45  | search.xfinity.com              | 6            | 6                    | 100            |
| 46  | google.co.in                    | 6            | 3                    | 50             |
| 47  | google.ru                       | 5            | 1                    | 20             |
| 48  | hangouts.google.com             | 5            | 1                    | 20             |
| 49  | online-metrics.com              | 5            | 2                    | 40             |
| 50  | s0.2mdn.net                     | 5            | 3                    | 60             |
| 51  | m.sogou.com                     | 4            | 3                    | 75             |
| 52  | googleads.g.doubleclick.net     | 4            | 1                    | 25             |
| 53  | in.search.yahoo.com             | 4            | 2                    | 50             |
| 54  | away.vk.com                     | 4            | 3                    | 75             |
| 55  | getpocket.com                   | 3            | 0                    | 0              |
| 56  | m.baidu.com                     | 3            | 2                    | 66.667         |
| 57  | siliconvalley.about.com         | 3            | 2                    | 66.667         |
| 58  | google.it                       | 2            | 1                    | 50             |
| 59  | google.co.th                    | 2            | 1                    | 50             |
| 60  | wap.sogou.com                   | 2            | 2                    | 100            |
| 61  | msn.com                         | 2            | 1                    | 50             |
| 62  | calendar.google.com             | 2            | 1                    | 50             |
| 63  | myactivity.google.com           | 2            | 1                    | 50             |
| 64  | github.com                      | 2            | 2                    | 100            |
| 65  | centrum.cz                      | 2            | 2                    | 100            |
| 66  | plus.url.google.com             | 2            | 0                    | 0              |
| 67  | amp.reddit.com                  | 2            | 1                    | 50             |
| 68  | uk.search.yahoo.com             | 2            | 1                    | 50             |
| 69  | search.1and1.com                | 2            | 2                    | 100            |
| 70  | google.cl                       | 2            | 1                    | 50             |
| 71  | moodle.aurora.edu               | 2            | 2                    | 100            |
| 72  | m.sp.sm.cn                      | 2            | 2                    | 100            |
| 73  | au.search.yahoo.com             | 2            | 2                    | 100            |
| 74  | earth.google.com                | 1            | 0                    | 0              |
| 75  | google.nl                       | 1            | 0                    | 0              |
| 76  | kidrex.org                      | 1            | 1                    | 100            |
| 77  | gophergala.com                  | 1            | 1                    | 100            |
| 78  | newclasses.nyu.edu              | 1            | 0                    | 0              |
| 79  | google.es                       | 1            | 1                    | 100            |
| 80  | malaysia.search.yahoo.com       | 1            | 1                    | 100            |
| 81  | aol                              | 1            | 0                    | 0              |
| 82  | google.ca                       | 1            | 0                    | 0              |
| 83  | kik.com                          | 1            | 1                    | 100            |
| 84  | es.search.yahoo.com             | 1            | 1                    | 100            |
| 85  | google.bg                       | 1            | 1                    | 100            |
| 86  | news.ycombinator.com            | 1            | 1                    | 100            |
| 87  | web.facebook.com                | 1            | 1                    | 100            |
| 88  | ph.search.yahoo.com             | 1            | 0                    | 0              |
| 89  | web.mail.comcast.net            | 1            | 1                    | 100            |
| 90  | online.fullsail.edu             | 1            | 1                    | 100            |
| 91  | mx.search.yahoo.com             | 1            | 1                    | 100            |
| 92  | images.google.com.au            | 1            | 1                    | 100            |
| 93  | search.tb.ask.com               | 1            | 0                    | 0              |
| 94  | it.pinterest.com                | 1            | 1                    | 100            |
| 95  | arstechnica.com                 | 1            | 0                    | 0              |
| 96  | suche.t-online.de               | 1            | 1                    | 100            |
| 97  | google.com.br                   | 1            | 0                    | 0              |

The table provides an overview of website traffic sources and key engagement metrics, focusing on total visits, bounces, and bounce rate.

Google generated the highest traffic with 38,400 visits, of which 19,798 were single-page visits, resulting in a 51.56% bounce rate. Direct traffic followed with 19,891 visits and 8,606 bounces, yielding a 43.27% bounce rate. YouTube ranked third with 6,351 visits but had a higher bounce rate (66.73%) than Google and Direct, indicating that a significant portion of users left after viewing just one page.

On the other hand, search.mysearch.com had the fewest visits (12) but the highest bounce rate (91.67%), suggesting that nearly all users from this source left without further interaction. Such a high bounce rate often indicates a mismatch between user expectations and website content or a lack of engagement on the landing page.

Understanding bounce rates in context is crucial. While a lower bounce rate generally signifies better user engagement, a high bounce rate isn’t always negative—it may depend on the intent of the page and the quality of traffic from specific sources. Analyzing these trends helps refine content strategy and improve user retention.

6.3 Revenue by traffic source by week, by month in June 2017
```` sql
WITH monthly_revenue AS (
    SELECT
    'Month' as time_type
    ,FORMAT_DATE('%Y%m', PARSE_DATE('%Y%m%d', date)) AS time
    ,trafficSource.source AS source
    ,round(sum(product.productRevenue)/1000000,4) as revenue  
    FROM
    `bigquery-public-data.google_analytics_sample.ga_sessions_201706*`
    ,UNNEST (hits) as hits
    ,UNNEST (hits.product) as product
    WHERE product.productRevenue is not null
    GROUP BY
    trafficSource.source
    ,time
    ORDER BY revenue DESC)

,weekly_revenue as (
    SELECT
    'Week' as time_type
    ,FORMAT_DATE('%Y%W', PARSE_DATE('%Y%m%d', date)) AS time
    ,trafficSource.source AS source
    ,round(sum(product.productRevenue)/1000000,4) as revenue  
    FROM
    `bigquery-public-data.google_analytics_sample.ga_sessions_201706*`
    ,UNNEST (hits) as hits
    ,UNNEST (hits.product) as product
    WHERE product.productRevenue is not null
    GROUP BY
    trafficSource.source
    ,time
    ORDER BY revenue desc)

SELECT
  time_type
  ,time
  ,source
  ,revenue
FROM
  monthly_revenue
UNION ALL
SELECT
  time_type
  ,time
  ,source
  ,revenue
FROM
  weekly_revenue
ORDER BY
  revenue DESC;
````
| Row | Time Type | Time   | Source             | Revenue ($)     |
|-----|----------|--------|--------------------|----------------|
| 1   | Month    | 201706 | (direct)           | 97,333.62      |
| 2   | Week     | 201724 | (direct)           | 30,908.91      |
| 3   | Week     | 201725 | (direct)           | 27,295.32      |
| 4   | Month    | 201706 | google             | 18,757.18      |
| 5   | Week     | 201723 | (direct)           | 17,325.68      |
| 6   | Week     | 201726 | (direct)           | 14,914.81      |
| 7   | Week     | 201724 | google             | 9,217.17       |
| 8   | Month    | 201706 | dfa                | 8,862.23       |
| 9   | Week     | 201722 | (direct)           | 6,888.90       |
| 10  | Week     | 201726 | google             | 5,330.57       |
| 11  | Week     | 201726 | dfa                | 3,704.74       |
| 12  | Month    | 201706 | mail.google.com    | 2,563.13       |
| 13  | Week     | 201724 | mail.google.com    | 2,486.86       |
| 14  | Week     | 201724 | dfa                | 2,341.56       |
| 15  | Week     | 201722 | google             | 2,119.39       |
| 16  | Week     | 201722 | dfa                | 1,670.65       |
| 17  | Week     | 201723 | dfa                | 1,145.28       |
| 18  | Week     | 201723 | google             | 1,083.95       |
| 19  | Week     | 201725 | google             | 1,006.10       |
| 20  | Month    | 201706 | search.myway.com   | 105.94         |
| 21  | Week     | 201723 | search.myway.com   | 105.94         |
| 22  | Month    | 201706 | groups.google.com  | 101.96         |
| 23  | Week     | 201725 | mail.google.com    | 76.27          |
| 24  | Month    | 201706 | chat.google.com    | 74.03          |
| 25  | Week     | 201723 | chat.google.com    | 74.03          |
| 26  | Month    | 201706 | dealspotr.com      | 72.95          |
| 27  | Week     | 201724 | dealspotr.com      | 72.95          |
| 28  | Week     | 201725 | mail.aol.com       | 64.85          |
| 29  | Month    | 201706 | mail.aol.com       | 64.85          |
| 30  | Week     | 201726 | groups.google.com  | 63.37          |
| 31  | Month    | 201706 | phandroid.com      | 52.95          |
| 32  | Week     | 201725 | phandroid.com      | 52.95          |
| 33  | Month    | 201706 | sites.google.com   | 39.17          |
| 34  | Week     | 201725 | groups.google.com  | 38.59          |
| 35  | Week     | 201725 | sites.google.com   | 25.19          |
| 36  | Week     | 201725 | google.com         | 23.99          |
| 37  | Month    | 201706 | google.com         | 23.99          |
| 38  | Month    | 201706 | yahoo              | 20.39          |
| 39  | Week     | 201726 | yahoo              | 20.39          |
| 40  | Month    | 201706 | youtube.com        | 16.99          |
| 41  | Week     | 201723 | youtube.com        | 16.99          |
| 42  | Week     | 201722 | sites.google.com   | 13.98          |
| 43  | Week     | 201724 | bing               | 13.98          |
| 44  | Month    | 201706 | bing               | 13.98          |
| 45  | Month    | 201706 | l.facebook.com     | 12.48          |
| 46  | Week     | 201724 | l.facebook.com     | 12.48          |

This table represents revenue data collection with various attributes, including time type, time period, source, and revenue amount. Each row corresponds to a specific time period (week or month) and provides information about the revenue generated from different sources during that time period. The author found the insights bellow through the table above.

Direct Revenue and Source Breakdown: The dataset includes revenue from various sources, such as (direct), Google, DFA, mail.google.com, and search engines like myway.com. Revenue comes from different sources, including direct website visits, organic search traffic, and referrals from various websites.

Time Period Analysis: Revenue is reported on a weekly and monthly basis. It seems like the data spans multiple months, including the month labeled "201706."

6.4 Average number of pageviews by purchaser type in June, July 2017
```` sql
WITH purchased_pv as (
  SELECT 
    FORMAT_DATE('%Y%m', PARSE_DATE('%Y%m%d', date)) AS month
    ,sum(totals.pageviews) as purchased_tlpv
  FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`,
      UNNEST(hits) AS hits,
      UNNEST(hits.product) AS product
    WHERE 
      _TABLE_SUFFIX BETWEEN '0601' AND '0731'AND totals.transactions >= 1 AND product.productRevenue IS NOT NULL
      GROUP BY month
          ORDER BY month  
)
,purchased_user_cnt as (
SELECT 
  FORMAT_DATE('%Y%m', PARSE_DATE('%Y%m%d', date)) AS month
  ,COUNT(DISTINCT fullVisitorId) as purchased_user
      FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`
        ,UNNEST(hits) AS hits
        ,UNNEST(hits.product) AS product
      WHERE _TABLE_SUFFIX BETWEEN '0601' AND '0731' AND totals.transactions >= 1 AND product.productRevenue IS NOT NULL
  GROUP BY month
)
,avg_pc as (
  SELECT 
  purchased_pv.month
  ,CAST((purchased_pv.purchased_tlpv/purchased_user_cnt.purchased_user) as DECIMAL) as avg_pageviews_purchase
  FROM purchased_pv
  INNER JOIN purchased_user_cnt ON purchased_pv.month = purchased_user_cnt.month
)

,not_purchased_pv as (
  SELECT 
    FORMAT_DATE('%Y%m', PARSE_DATE('%Y%m%d', date)) AS month
    ,sum(totals.pageviews) as not_purchased_tlpv
  FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`,
      UNNEST(hits) AS hits,
      UNNEST(hits.product) AS product
    WHERE 
      _TABLE_SUFFIX BETWEEN '0601' AND '0731'AND totals.transactions is null AND product.productRevenue IS NULL
      GROUP BY month
      ORDER BY month
)
,not_purchased_user_cnt as (
  SELECT 
  FORMAT_DATE('%Y%m', PARSE_DATE('%Y%m%d', date)) AS month
  ,COUNT(DISTINCT fullVisitorId) as not_purchased_user
      FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`
        ,UNNEST(hits) AS hits
        ,UNNEST(hits.product) AS product
      WHERE _TABLE_SUFFIX BETWEEN '0601' AND '0731' AND totals.transactions is null AND product.productRevenue IS NULL
  GROUP BY month
)

,avg_not_pc as (
  SELECT 
  not_purchased_pv.month
  ,CAST((not_purchased_tlpv/not_purchased_user) as DECIMAL) as avg_pageviews_non_purchase
  FROM not_purchased_pv
  INNER JOIN not_purchased_user_cnt ON not_purchased_pv.month = not_purchased_user_cnt.month
  ORDER BY month
)

SELECT 
avg_pc.month
,avg_pc.avg_pageviews_purchase
,avg_not_pc.avg_pageviews_non_purchase
FROM avg_pc 
JOIN avg_not_pc
ON avg_pc.month = avg_not_pc.month
ORDER BY month;
````
| Row | Month  | Avg. Pageviews (Purchase) | Avg. Pageviews (Non-Purchase) |
|-----|--------|---------------------------|-------------------------------|
| 1   | 201706 | 94.02                      | 316.87                        |
| 2   | 201707 | 124.24                     | 334.06                        |

Pageviews for Users Making a Purchase vs. Non-Purchasers
The data highlights a significant difference in average pageviews between users who made a purchase and those who did not. Interestingly, non-purchasers have a much higher average pageview count compared to purchasing users.

In June 2017, the average pageviews for purchasers was 94.02, while non-purchasers had a much higher average of 316.87.
In July 2017, purchasing users viewed 124.24 pages on average, whereas non-purchasers continued to explore more pages (334.06).
Engagement Patterns & Behavioral Insights
The fact that non-purchasing users visit more pages than purchasers suggests that they might be struggling to find what they need, comparing multiple products, or experiencing friction in the purchasing process.
Conversely, purchasing users may have a clearer intent, navigating efficiently toward a transaction.
This highlights the need for better content organization, improved product recommendations, and an optimized checkout process to reduce unnecessary browsing and encourage purchases.
Conversion Optimization Strategies
Simplifying the Buying Journey: If non-purchasers are browsing excessively, the website may need better navigation, improved search functionality, and clear CTAs.
Personalization & Retargeting: Since non-purchasers exhibit high engagement, personalized product suggestions and targeted marketing campaigns could help convert them.
Reducing Friction in Checkout: If users abandon after viewing many pages, there may be concerns regarding pricing, payment methods, or shipping costs—these should be evaluated through A/B testing and user feedback.
Conclusion
The analysis reveals that while purchasing users browse extensively, non-purchasing users engage even more, suggesting potential challenges in decision-making or usability. By addressing friction points, improving content clarity, and guiding users more efficiently toward purchases, businesses can enhance conversion rates and overall user experience.

6.5 Average number of transactions per user that made a purchase in July 2017
```` sql
WITH raw_data AS (
  SELECT 
    FORMAT_DATE('%Y%m', PARSE_DATE('%Y%m%d', date)) AS month
    ,fullVisitorId
    ,SUM(totals.transactions) as purchased_trans_count
  FROM `bigquery-public-data.google_analytics_sample.ga_sessions_201707*`,
      UNNEST(hits) AS hits,
      UNNEST(hits.product) AS product
  WHERE totals.transactions >=1 and product.productRevenue is not null
  GROUP BY fullVisitorId, FORMAT_DATE('%Y%m', PARSE_DATE('%Y%m%d', date))
  )

SELECT
  month
  ,round(avg(purchased_trans_count),9) as Avg_total_transactions_per_user
FROM raw_data
GROUP BY month;
````


