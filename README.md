# TrendyGear ecommerce
Using SQL to answer business questions from an ecommerce dataset
## Understanding the data
Dataset details: View here
In the dataset, we could see that there are "RECORD" datatype, which requires a deeper understanding. To put it simply, in the context of databases, particularly in systems like Google BigQuery, a RECORD (also known as a struct or a nested field) is a complex data type that allows you to group multiple fields together into a single field. This is useful for organizing related data that can be characterized as a single unit.

## Context Summary
Current time: September 2017
### Company Overview
Company Name: TrendyGear
- Industry: E-commerce specializing in lifestyle, fashion, and tech accessories.
- Vision: To be a leading online destination for trendy products that resonate with a modern, urban lifestyle, offering customers a seamless shopping experience.
- Mission: To provide high-quality, curated products while leveraging data-driven insights to enhance customer satisfaction and drive sales growth.

### Stakeholders
Chief Marketing Officer (CMO):
- Concern: The CMO is focused on understanding the effectiveness of various marketing channels and their contributions to overall revenue. High bounce rates from specific traffic sources raise concerns about campaign effectiveness and messaging alignment.
- Request: Understand the current performance and wish to analyze bounce rates by traffic source to identify underperforming areas and optimize marketing strategies.

Product Manager:
- Concern: The Product Manager is interested in understanding customer behavior regarding specific items, such as the "YouTube Men's Vintage Henley." Insights into related purchases and average spend per session can inform product recommendations and inventory decisions.
- Request: Identify cross-selling opportunities and analyze average transactions and spending behavior among purchasers.

Chief Financial Officer (CFO):
- Concern: The CFO is focused on financial performance metrics, including revenue generation and cost efficiencies. Understanding which channels generate the most revenue and having a clear understanding of customer purchasing behaviors are essential for budgeting and forecasting.
- Request: Examine revenue by traffic source and overall monthly trends in transactions and pageviews, along with cost per acquisition metrics.

User Experience (UX) Designer:
- Concern: The UX Designer aims to improve website engagement and reduce bounce rates. Understanding how different user segments (purchasers vs. non-purchasers) interact with the site can lead to better design decisions.
- Request: Analyze average pageviews by purchaser type to enhance the experience for non-purchasers to encourage them toward conversion.


### Problems Leading to Analysis Requests
- Declining Engagement Metrics: Stakeholders have observed a decline in user engagement metrics, such as increased bounce rates across certain traffic sources. This has led to concerns about the effectiveness of current marketing campaigns and the relevance of the landing pages.
- Need for Data-Driven Insights: The company recognizes the necessity for data-driven insights to make informed decisions. Stakeholders are seeking actionable insights based on monthly performance data to continuously refine marketing strategies and product offerings.
- Understanding Customer Journey: There is a growing imperative to understand the customer journey from product view to purchase. Stakeholders are particularly concerned with ensuring that the drop-off rates between these stages are minimized, highlighting a need for robust cohort analysis.
- Maximizing Revenue Potential: Given the competitive landscape of e-commerce, stakeholders are motivated to maximize revenue potential through strategic product placements, effective cross-selling, and upselling based on user behavior.
- User Segmentation for Tailored Strategies: The need to segment users into groups (purchasers vs. non-purchasers) is essential for creating targeted marketing strategies. This segmentation is crucial for understanding different engagement patterns and optimizing the user experience accordingly.

## Queries
Table to be feched: FROM `bigquery-public-data.google_analytics_sample.ga_sessions_2017*`
### Query 01: Monthly Metrics for 2017
- Stakeholder Context: All
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

#### Actionable insights

![image](https://github.com/user-attachments/assets/2d4785e6-a5a5-4ea3-98ae-69e3b7ee4cfe)

=> The chart highlights a significant observation: a sudden drop in all metrics during the final month, August, following a period of consistent performance. This, in a way, confirm the concern of the CMO, implying a deeper investigation regarding the following aspects: 

- Analyze Traffic Sources: Compare the traffic volume from different channels (e.g., organic search, paid ads) between August and previous months.
- Check for Technical Issues: Review site performance logs and analytics tracking setup in August.
- Assess Marketing Campaigns: Examine whether any campaigns ended, underperformed, or shifted their targeting.
- Review Customer Behavior: Look for shifts in user demographics, including geographic trends and returning vs. new customers.
- Evaluate External Factors: Check for competitor actions, regional events, or economic patterns impacting customer behavior.

### Query 02: Bounce Rate per Traffic Source in July 2017
- Stakeholder: Chief Marketing Officer (CMO)
- Problem: Observing high bounce rates from certain traffic sources raises concerns about the effectiveness of marketing campaigns.
- Lead: To optimize our marketing strategies, it's essential to analyze the bounce rate for each traffic source in July 2017. This data will help identify which channels are underperforming and require adjustments to enhance user engagement.

