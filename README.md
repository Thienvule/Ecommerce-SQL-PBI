# E-commerce Sales Decline Analysis: Reversing Trends with Advanced SQL and Power BI Visualization
## Understanding the data
Dataset details: View here
The dataset includes fields with the "RECORD" datatype, which requires a deeper understanding. In databases like Google BigQuery, a RECORD (often referred to as a struct or nested field) is a complex data type that allows for the grouping of multiple fields into a single entity. This capability is beneficial for organizing related data that can be treated as a cohesive unit.

It's important to note that while some arrays may not be explicitly labeled as "ARRAY" in the schema description, their repetitive structure indicates that they function as arrays. This distinction highlights the need to interpret the data schema carefully, as the presence of repeating records indicates nested or array-like behavior even when not explicitly shown.

## Context Summary
Current time: September 2017
August's performance at TrendyGear was notably poor, marking a decline that became evident in the second quarter. Recognizing the urgency to reverse this downward trend, the stakeholders are eager to explore effective strategies. To facilitate this discussion, they have included me, their data analyst, in the meeting to address some of the ad-hoc requests. My role will be to query immediate data, providing insights that will help guide our decision-making process.

### Problems Leading to Analysis Requests
- Declining Engagement Metrics: Stakeholders have observed a decline in user engagement metrics, such as increased bounce rates across certain traffic sources. This has led to concerns about the effectiveness of current marketing campaigns and the relevance of the landing pages.
- Need for Data-Driven Insights: The company recognizes the necessity for data-driven insights to make informed decisions. Stakeholders are seeking actionable insights based on monthly performance data to continuously refine marketing strategies and product offerings.
- Understanding Customer Journey: There is a growing imperative to understand the customer journey from product view to purchase. Stakeholders are particularly concerned with ensuring that the drop-off rates between these stages are minimized, highlighting a need for robust cohort analysis.
- Maximizing Revenue Potential: Given the competitive landscape of e-commerce, stakeholders are motivated to maximize revenue potential through strategic product placements, effective cross-selling, and upselling based on user behavior.
- User Segmentation for Tailored Strategies: The need to segment users into groups (purchasers vs. non-purchasers) is essential for creating targeted marketing strategies. This segmentation is crucial for understanding different engagement patterns and optimizing the user experience accordingly.

## Queries
Table to be feched: FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`
### Query 01: Monthly Metrics for 2017
- Stakeholder: All
- Problem: All stakeholders want to undestand overall business performance and identifying trends in traffic and conversions from the start of the year as they all felt that something is getting worse.
- Task to be done: To establish a solid baseline for performance and understand customer engagement from the start of the year to now, we will analyze the total visits, pageviews, and transactions from Jan to Aug 2017. This will help track any fluctuations and identify patterns that need addressing.
#### Code:
 
  ![image](https://github.com/user-attachments/assets/1db5f48b-833d-4f9d-be69-92154a0dfd1a)

Code explanation:
`EXTRACT(MONTH FROM PARSE_DATE('%Y%m%d', date)) AS month`
- The date column in this dataset is stored as a string in the format YYYYMMDD (e.g., "20170101" for January 1, 2017).
- PARSE_DATE('%Y%m%d', date) converts this string into a proper date format that BigQuery can understand.
- EXTRACT(MONTH FROM ...) takes the month part of the date (numbers 1 to 12 representing January to December).
- This creates a column called month (e.g., 1 for January, 2 for February, etc.).

`SUM`
`SUM(totals.pageviews) AS pageviews`: adds up all the pageviews
`SUM(totals.visits) AS visits`:  adds up all the visits/sessions 
`SUM(totals.transactions) AS transactions`: adds up all the completed transactions

GROUP BY month: Groups all the data for each month together as we use an aggregate function above
ORDER BY month: sorts the rows by months of 2017

#### Result

![image](https://github.com/user-attachments/assets/c228cd6f-0c45-489d-8568-50c89a12113f)

![image](https://github.com/user-attachments/assets/2d4785e6-a5a5-4ea3-98ae-69e3b7ee4cfe)

=> The chart highlights a significant observation: a sudden drop in all metrics during the final month, August, following a period of consistent performance. This, in a way, confirm the concern of the CMO, implying a deeper investigation regarding the following aspects: 

- Analyze Traffic Sources: Compare the traffic volume from different channels (e.g., organic search, paid ads) between August and previous months.
- Check for Technical Issues: Review site performance logs and analytics tracking setup in August.
- Assess Marketing Campaigns: Examine whether any campaigns ended, underperformed, or shifted their targeting.
- Review Customer Behavior: Look for shifts in user demographics, including geographic trends and returning vs. new customers.
- Evaluate External Factors: Check for competitor actions, regional events, or economic patterns impacting customer behavior.

### Query 02: Bounce Rate per Traffic Source in 2017
- Stakeholder: Chief Marketing Officer (CMO)
- Problem: Observing high bounce rates from certain traffic sources raises concerns about the effectiveness of marketing campaigns.
- Lead: To optimize our marketing strategies, it's essential to analyze the bounce rate for each traffic source in  2017. This data will help identify which channels are underperforming and require adjustments to enhance user engagement.

#### Code:
![image](https://github.com/user-attachments/assets/42d3a5d0-7dc3-4511-96d2-32c5e366d303)

Code explanation:
- `SELECT trafficSource.source`: This retrieves the source of traffic
- SUM codes: `SUM(totals.visits) AS total_visits/SUM(totals.bounces) AS num_bounce`: Shows sources' visits and bounces
- `ROUND(SUM(totals.bounces) * 100 / SUM(totals.visits), 2) AS bounce_rate`: Calculate bounce rates of each source

#### Result
![image](https://github.com/user-attachments/assets/cdf651d2-eba8-49b5-8e24-60159fefd66d)

To gain deeper insights, I imported the data into Power BI and followed these steps:
- Load the CSV File: Imported the CSV file from Google BigQuery into Power BI.
- Format the Bounce Rate: Adjusted the bounce_rate column to display values as percentages with a simple DAX
 ![image](https://github.com/user-attachments/assets/c2b53e1a-89e9-4684-9b83-e84669208492)

- Visualize Data: Created a visualization highlighting the top 10 traffic sources with the highest bounce rates, filtered to include only those with the highest number of visits.

![image](https://github.com/user-attachments/assets/a92f0f92-794d-4d9c-b228-0d74bb1c3ea7)

#### Result
![image](https://github.com/user-attachments/assets/1acf2421-e0cf-4005-822c-1000beea2411)

=> From this chart, the CMO can direct her investigation towards the top 10 traffic sources that exhibit underperformance.

### Query 03: Revenue by Traffic Source 
Stakeholder Context: Chief Financial Officer (CFO)

Problem: Understanding which marketing channels are driving revenue and which are underperforming is critical for budget allocation and optimization.

Lead: To identify effective traffic sources, we will analyze revenue generated by each traffic source in June 2017. This will allow the CFO to make data-driven decisions about where to focus marketing investments.
#### Code:
![image](https://github.com/user-attachments/assets/30610b6a-5d7d-413a-8c12-e177877e0fa4)

Code explanation:
- `date`: This selects the date of each session from the dataset. This will be used to build date hierarchy in PBI for deeper granularities.
- `trafficSource.source`: This selects the source of the traffic to the website, indicating where visitors originated from (e.g., social media, search engines).
- `SUM(productRevenue) AS revenue`: This calculates the total revenue generated from products sold, aggregating the productRevenue values.
- `UNNEST(hits) AS hits`/`UNNEST(product) AS product`: This function flattens the hits AND product array, which contains multiple hit records per session, converting them into individual rows allow us to access their properties directly.
- `productRevenue IS NOT NULL`: This condition filters out any records where productRevenue is null. It ensures that only entries with actual revenue values are included in the results.
  
For better insights gathering, I utilize PBI and:

- Edit data type using Power Query

![image](https://github.com/user-attachments/assets/7ae3975e-fb4e-429d-9950-2ebdf8d7e171)

- Build Hierarchy:
  
![image](https://github.com/user-attachments/assets/25f06c44-318a-49a4-aeea-3754ac6820bf)
![image](https://github.com/user-attachments/assets/9f8f59cf-c571-44e0-8ddd-70bd1de15a95)

#### Result

- Revenue by Month:
![image](https://github.com/user-attachments/assets/183a7751-12b8-49df-9c06-8494c7b7852e)

- Revenue by Weekday:

![image](https://github.com/user-attachments/assets/38753dd2-7a81-429e-8f64-ef95c3e8bdee)

Based on these insights, the CFO can identify revenue spikes in April and July. She can then collaborate with the CMO to investigate the reasons behind the increases occurring on specific weekdays, such as Tuesday and Wednesday.


### Query 04: Average Pageviews by Purchaser Type in 2017
Stakeholder Context: User Experience (UX) Designer

Situation: Understanding how user types engage with the site can provide insights for enhancing personalization and navigation.

Action: To improve the user experience, we will examine the average number of pageviews by purchasers and non-purchasers in 2017. This information will help in crafting strategies to engage non-purchasers more effectively.

#### Code:

![image](https://github.com/user-attachments/assets/14fa29a9-6fb0-4d65-93cd-ae89d58dd05d)

Code explanation
- The `WITH cte AS (...)` defines a common table expression (CTE) named cte which is temporarily created for use in the subsequent query.
- `WHEN product.productRevenue IS NOT NULL AND totals.transactions >= 1 THEN 'Purchaser' ELSE 'Non-purchaser' END AS purchaser_type`: Label who purchased and who didn't
- `AVG(CASE WHEN purchaser_type = 'Purchaser' THEN pageviews END) AS Purchaser`: This calculates the average pageviews for visitors categorized as purchasers.
- `AVG(CASE WHEN purchaser_type = 'Non-purchaser' THEN pageviews END) AS Non_purchaser`: : This calculates the average pageviews for visitors classified as non-purchasers.

#### Result
- The final result will show a list of months (from the dataset), along with the average number of pageviews for both purchasers and non-purchasers.
- Each row represents a month, with two average values indicating how many pages were viewed on average by users in each category.

![image](https://github.com/user-attachments/assets/09c5c23a-5a58-4df3-9c1a-312bd46116bf)

=> The User Experience (UX) Designer can analyze the peak in March to determine the factors that increased pageviews among purchasers, providing insights that the CMO can utilize.

### Query 05: Average Transactions per User That Made a Purchase in March 2017
Stakeholder Context: Product Manager and CMO

Situation: The Product Manager and the CMO is interested to see if this peak in pageviews in March did generate substantial value as they were notified by the UX Designer.

Action: To discover how much value was generated from that peak, we will calculate the average number of transactions per user who made a purchase in March 2017. This information aids in developing strategies for encouraging repeat purchases.

#### Code:

![image](https://github.com/user-attachments/assets/55952eac-0874-4739-816a-4415a9dd15b6)

Code Explanation: 
- `CTE`: To identify purchasers as the last query
- `ROUND(SUM(transactions) / COUNT(DISTINCT fullVisitorId), 2) AS avg_trans_per_user`: This calculates the average number of transactions per user by dividing the total number of transactions by the count of distinct visitors (fullVisitorId). The result is rounded to two decimal places.

- `ROUND(SUM(revenue) / COUNT(DISTINCT fullVisitorId), 2) AS avg_rev_per_user`: Similarly, this calculates the average revenue generated per user by dividing the total revenue by the count of distinct visitors. The value is rounded to two decimal places.

#### Result

![image](https://github.com/user-attachments/assets/7742fb32-767e-44a8-a4e2-238e6301144f)

![image](https://github.com/user-attachments/assets/e97317a3-3904-41b1-9776-6c3596b01ff4)

![image](https://github.com/user-attachments/assets/40b94522-45e9-4021-81c7-a4455290d5cd)

=> It has been confirmed that March is an ideal starting point for our investigation.

### Query 06: Average Amount Spent per Session by Purchasers in  2017
Stakeholder Context: Chief Financial Officer (CFO) and Chief Marketing Officer (CMO)

Problem: Understanding expenditure patterns is vital for optimizing pricing strategies and promotions. Wonder if 

Lead: To determine how much customers spend on average during their sessions, we'll calculate the average amount spent per session for purchasers in July 2017. This insight can identify pricing opportunities and promotional strategies.

#### Code

![image](https://github.com/user-attachments/assets/ee72bc98-9119-4df5-b54e-1a15bfd55ab9)
Code explanation:
-`SUM(totals.visits) AS total_visits`: This calculates the total number of visits across all sessions for each month.
- `SUM(product.productRevenue) AS total_revenue`: This sums up the total revenue generated from product sales for each month.
-  `SUM(product.productRevenue)/SUM(totals.visits) AS avg_revenue_per_visit`: This calculates the average revenue per visit by dividing the total revenue by the total number of visits.
#### Result
![image](https://github.com/user-attachments/assets/91904619-10b6-4777-8bd3-4c7ef9f17d93)

While the positive correlation might lead us to believe that we should invest more in campaigns that drive higher views, a closer examination reveals a different story. 

![image](https://github.com/user-attachments/assets/91a45dd8-4599-4151-acda-3c2fff1ebbd9)

April exhibited only one peak, while the other months showed stable performance. Furthermore, the total visits in April were not significantly higher than in other months and were even lower than some. ![image](https://github.com/user-attachments/assets/b480243d-00fd-4bdc-befa-1b533ac5808d)

A clearer analysis indicates that the average revenue per visit in April was significantly higher.

![image](https://github.com/user-attachments/assets/0c8d7db4-ef6f-4409-b0f4-2e225a437ded)

=> This suggests that visitors in April were likely more inclined to make high-value purchases, leading to increased average revenue per visit. This could also imply a higher quality of traffic or effective promotional strategies during April that encouraged significant spending from quality visitors. Therefore, identifying quality visitors can do our campaigns a huge favor.

### Query 07: Other Products Purchased by Customers of "YouTube Men's Vintage Henley" in April 2017
Stakeholder Context: Product Manager

Situation: The Product Manager enters the discussion and wants to know which products/product combinations worked best in April. He wants to identify cross-selling opportunities enhances the product mix and boosts overall sales.

Task and Action: To effectively market related products, we will analyze the purchasing patterns of products bought in April 2017, driving cross-selling strategies.
#### Code
![image](https://github.com/user-attachments/assets/c288d99a-31e3-4c2c-81fe-14ee4127017d)
In case we want to identify who was the best customers in April, this should be the code: 
![image](https://github.com/user-attachments/assets/e839ab96-38f2-4f4d-9aff-e485dae97789)
Most of the functions used in this code was explained earlier, which would not be redone this time.

#### Result
Products that excel in quantity versus those that generate high revenue
![image](https://github.com/user-attachments/assets/d64f0141-3353-490a-867e-eff1860d36c5)

=> Strategies for Products with High Sales Volume (Quantity)
- Bundling Products: Offer bundled deals to encourage customers to purchase multiple items at a discounted rate. This can enhance the perceived value and increase the average transaction size.
- Promotional Discounts: Implement regular promotions and discounts to encourage high-volume purchases. Limited-time offers can create urgency and drive quick sales.
- Loyalty Programs: Establish a loyalty program that rewards customers for repeat purchases. Incentives can include points for each purchase that can be redeemed for discounts or free products.
- Cross-Selling and Upselling: Use personalized product recommendations at checkout. Encouraging customers to purchase complementary items can increase the overall quantity sold.
- Social Proof and Reviews: Highlight customer reviews and testimonials. Social proof can influence potential buyers to choose high-selling items.
- Effective Merchandising: Use eye-catching displays or website layouts to promote high-quantity items. Positioning popular items prominently can attract more attention and drive sales.
- Targeted Marketing:Utilize data analytics to identify customer preferences and market directly to segments that purchase frequently. Tailored email campaigns can boost engagement.
=> Strategies for Products with High Revenue
- Premium Positioning: Position high-revenue products as premium offerings. Highlight quality, exclusivity, and unique features to justify higher price points.
- Enhanced Customer Experience: Provide exceptional customer service and support. High-value purchasers often expect a higher level of interaction and assistance.
- Personalized Marketing: Use data to personalize marketing efforts for high-revenue products. This includes targeted ads, personalized emails, and tailored content.
- Bundling with Low-Cost Items: Bundle high-value products with low-cost items to enhance perceived value. This can encourage buyers to consider purchasing the higher-priced product.
- Content Marketing: Create detailed content (e.g., how-to videos, blogs) that highlights the benefits and use cases of the high-revenue products. This can educate customers and inspire them to purchase.
- Seasonal Promotions: Time promotions around special occasions or seasonal changes that align with high-value products. Creating a sense of urgency can encourage purchases.
- Targeted Upselling: During the purchasing process, offer upsells on complementary high-revenue items. Make it easy for customers to consider additional purchases

Another move we should make is to approach our best customers with loyal programs and the like at the time:
![image](https://github.com/user-attachments/assets/75491a03-cd2b-45f8-8661-862eee63aa39)

### Query 08: Cohort Map from Product View to Add to Cart to Purchase in April 2017
Stakeholder Context: CMO, CFO, and Product Manager

Situation: The chiefs want to understand user (in April especially) behavior from product views to purchases aids in funnel optimization.

Task: To visualize the journey from product views to purchases through a cohort map, we will analyze the conversion rates at each step in January, February, and March 2017. This will help in understanding the efficacy of the purchase funnel and informing strategies to reduce drop-offs.

#### Code:
![image](https://github.com/user-attachments/assets/8feb5aed-7052-4cf9-9e4a-231d49b679db)

Code explanation:
- Product Views: Counts instances of product views (action type 2).
- Add to Cart: Counts instances of products added to the cart (action type 3).
- Remove from Cart: Counts instances where products were removed from the cart (action type 4).
- Refunds: Counts instances of refunds (action type 7).
- Purchases: Counts instances of completed purchases where revenue is recorded (action type 6).
- Total Revenue: Sums the revenue from products sold.
- Main Query: Calculates add to cart and purchase rate by dividing these metrics by product views.

#### Result
![image](https://github.com/user-attachments/assets/d55e38e6-a22e-4969-afe3-3eeb72ad7c26)

The table indicates that there is little difference between the add-to-cart and purchase rates, yet the revenue of April significantly rose. Therefore, investigating these customers and identifying the best-selling products in April could provide valuable insights.

![image](https://github.com/user-attachments/assets/12f5b05e-cd3c-464c-96d3-8c421b9cec74)

