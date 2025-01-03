# Case Study: Analyzing Target's Operations in Brazil (2016-2018)

## Introduction
Target is a globally renowned brand and a prominent retailer in the United States. Known for its outstanding value, innovation, and exceptional guest experience, Target aims to be a preferred shopping destination. This case study focuses on Target's operations in Brazil, analyzing a dataset of 100,000 orders placed between 2016 and 2018. The dataset includes various dimensions such as order status, pricing, payment and freight performance, customer location, product attributes, and customer reviews. By exploring this data, we can gain valuable insights into Target's Brazilian operations and provide actionable recommendations for improvement.

---

## Dataset Description
The dataset is available [here](https://drive.google.com/drive/folders/1oZOQW_jnaz-yhm5HAQJhIeOqezjl6BRf?usp=sharing) and consists of 8 CSV files:

1. **customers.csv**: Information about the customers.
2. **sellers.csv**: Information about the sellers.
3. **order_items.csv**: Details of the order items.
4. **geolocation.csv**: Geographical information.
5. **payments.csv**: Payment details.
6. **reviews.csv**: Customer reviews.
7. **orders.csv**: Order details.
8. **products.csv**: Product details.

---

## Schema
### **customers.csv**
- `customer_id`: ID of the consumer who made the purchase.
- `customer_unique_id`: Unique ID of the consumer.
- `customer_zip_code_prefix`: Zip Code of the consumer’s location.
- `customer_city`: Name of the city from where the order was made.
- `customer_state`: State code from where the order was made.

### **sellers.csv**
- `seller_id`: Unique ID of the seller.
- `seller_zip_code_prefix`: Zip Code of the seller’s location.
- `seller_city`: Name of the city of the seller.
- `seller_state`: State code.

### **order_items.csv**
- `order_id`: Unique ID of the order.
- `order_item_id`: Unique ID of each item ordered.
- `product_id`: Unique ID of each product.
- `seller_id`: Unique ID of the seller.
- `shipping_limit_date`: Date before which the product must be shipped.
- `price`: Price of the products ordered.
- `freight_value`: Freight cost for delivery.

### **geolocation.csv**
- `geolocation_zip_code_prefix`: First 5 digits of Zip Code.
- `geolocation_lat`: Latitude.
- `geolocation_lng`: Longitude.
- `geolocation_city`: City.
- `geolocation_state`: State.

### **payments.csv**
- `order_id`: Unique ID of the order.
- `payment_sequential`: Sequences of payments in case of EMI.
- `payment_type`: Mode of payment used.
- `payment_installments`: Number of installments for EMI purchase.
- `payment_value`: Total amount paid for the purchase order.

### **orders.csv**
- `order_id`: Unique ID of the order.
- `customer_id`: ID of the consumer who made the purchase.
- `order_status`: Status of the order.
- `order_purchase_timestamp`: Timestamp of the purchase.
- `order_delivered_carrier_date`: Delivery date at which carrier made the delivery.
- `order_delivered_customer_date`: Date at which customer received the product.
- `order_estimated_delivery_date`: Estimated delivery date.

### **reviews.csv**
- `review_id`: ID of the review.
- `order_id`: Unique ID of the order.
- `review_score`: Review score given by the customer.
- `review_comment_title`: Title of the review.
- `review_comment_message`: Review comments.
- `review_creation_date`: Timestamp of the review creation.
- `review_answer_timestamp`: Timestamp of the review answered.

### **products.csv**
- `product_id`: Unique identifier for the product.
- `product_category_name`: Name of the product category.
- `product_name_length`: Length of the product name.
- `product_description_length`: Length of the product description.
- `product_photos_qty`: Number of photos of each product.
- `product_weight_g`: Weight of the product in grams.
- `product_length_cm`: Length of the product in centimeters.
- `product_height_cm`: Height of the product in centimeters.
- `product_width_cm`: Width of the product in centimeters.

---

## Problem Statement
As a data analyst/scientist at Target, your task is to analyze the dataset to extract valuable insights and provide actionable recommendations.

---

## Evaluation Criteria (100 points)

### **Initial Exploration (15 points)**
1. Check the structure & characteristics of the dataset.
   - **Answer:**
     - The dataset includes 8 CSV files covering multiple dimensions such as customers, orders, products, and payments. Each file provides essential attributes for analysis.

2. Data type of all columns in the "customers" table.
   - **Answer:**
    ```sql
   - SELECT column_name, data_type
FROM INFORMATION_SCHEMA.COLUMNS
WHERE table_schema = 'customers';
```
     - The `customers.csv` file contains columns such as `customer_id` (string), `customer_unique_id` (string), `customer_zip_code_prefix` (integer), `customer_city` (string), and `customer_state` (string).

3. Get the time range between which the orders were placed.
   - **Answer:**
     - Using the query:  
       ```sql
       select min(order_purchase_timestamp) as First_order_stamp, max(order_purchase_timestamp) as last_order_stamp 
from analyzing.orders
       ```
     - Insights: The first order was placed in September 2016, and the last order in October 2018.

4. Count the cities & states of customers who ordered during the given period.
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT COUNT(DISTINCT geolocation_city) AS "Number of Cities",
       COUNT(DISTINCT geolocation_state) AS "Number of States"
FROM analyzing.geolocation;

       ```
     - Insights: There were **400+ unique cities** and **27 states** involved in placing orders during the given period.

### **In-depth Exploration (15 points)**
1. Identify trends in the number of orders placed over the years.
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT EXTRACT(YEAR FROM order_purchase_timestamp) AS
Order_Year,
EXTRACT(MONTH FROM order_purchase_timestamp) AS
Order_Month,COUNT(order_id) AS Number_of_Orders
From analyzing.orders
GROUP BY Order_Year,Order_Month
ORDER BY Order_Year,Order_Month
       ```
     - Insights: There was a steady increase in the number of orders from 2016 to 2017. A peak was observed in late 2017, followed by a decline in 2018.

2. Detect any monthly seasonality in the number of orders.
   - **Answer:**
     - Using the query:  
       ```sql
      select Order_year,Order_Month,Number_of_Orders,
ntile(5) over(order by Number_of_orders desc) as
Months_Seasonality
from
(SELECT EXTRACT(YEAR FROM order_purchase_timestamp) AS
Order_Year,
EXTRACT(MONTH FROM order_purchase_timestamp) AS
Order_Month,COUNT(order_id) AS Number_of_Orders
from analyzing.orders
GROUP BY order_year,order_month
ORDER BY order_year,order_month) tbl
order by Number_of_Orders desc;

       ```
     - Insights: November consistently showed high order volumes, indicating seasonal peaks. January through March were relatively low.

3. Determine the time of day when Brazilian customers mostly place orders (Dawn, Morning, Afternoon, or Night).
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT 	
	CASE
		WHEN EXTRACT(HOUR FROM order_purchase_timestamp) BETWEEN 0 AND 6 THEN 'DAWN 0-6'
        WHEN EXTRACT(HOUR FROM order_purchase_timestamp) BETWEEN 7 AND 12 THEN 'MORNING 7-12'
        WHEN EXTRACT(HOUR FROM order_purchase_timestamp) BETWEEN 13 AND 18 THEN 'AFTERNOON 13-18'
        WHEN EXTRACT(HOUR FROM order_purchase_timestamp) BETWEEN 19 AND 23 THEN 'NIGHT 19-23'
	END AS Order_time_of_day,
    count(*) as Number_of_orders
    from analyzing.orders
group by Order_time_of_day     
order by Order_time_of_day
   
    
       ```
     - Insights: Most orders were placed during the afternoon (13-18 hrs), followed by night (19-23 hrs). Dawn (0-6 hrs) was the least active period.

### **Evolution of E-commerce Orders in Brazil (10 points)**
1. Month-on-month number of orders placed in each state.
   - **Answer:**
     - Using the query:  
       ```sql
       select c.customer_state, 
	extract(Year from o.order_purchase_timestamp) as Order_year, extract(Month from o.order_purchase_timestamp) as Order_month, count(o.order_id) as Number_of_orders
    from analyzing.orders o join analyzing.customers c
    on o.customer_id=c.customer_id 
    group by c.customer_state,Order_year, Order_month
    order by c.customer_state,Order_year, Order_month
       ```
     - Insights: SP consistently showed the highest number of orders monthly, while smaller states like RR had minimal orders. Seasonal spikes in November were observed across most states.

2. Distribution of customers across all states.
   - **Answer:**
     - Using the query:  
       ```sql
      select customer_state, count(customer_id) as number_of_customers from analyzing.customers
group by customer_state
order by number_of_customers desc
       ```
     - Insights: State SP has the maximum number of customers, capturing nearly 40% of the customer base. States like RR and AP showed minimal engagement, indicating potential areas for growth and targeted campaigns.

### **Impact on Economy (20 points)**
1. Analyze money movement by looking at order prices, freight, and other factors.
   - **Answer:**
     - Using the query:  
       ```sql
     SELECT 
    c.customer_state, 
    CAST(ROUND(SUM(oi.price), 0) AS FLOAT) AS total_order_price, 
    CAST(ROUND(AVG(oi.price), 0) AS FLOAT ) AS avg_order_price
from analyzing.customers c
join analyzing.orders o on c.customer_id=o.customer_id 
join analyzing.order_items oi on o.order_id=oi.order_id
group by c.customer_state
order by c.customer_state
       ```
     - Insights: Freight costs account for nearly 20% of the total transaction value. This suggests a need for freight optimization strategies to improve profit margins.

2. Calculate the percentage increase in the cost of orders from 2017 to 2018 (Jan-Aug).
   - **Answer:**
     - Using the query:  
       ```sql
       with y17 as (
select round (sum(p.payment_value)) as cost_of_2017 from analyzing.orders as o join analyzing.payments as p
on p.order_id=o.order_id
where extract(year from o.order_purchase_timestamp) = 2017 AND extract(month from o.order_purchase_timestamp) BETWEEN 1 AND 8
),
 y18 as (
select round (sum(p.payment_value)) as cost_of_2018 from analyzing.orders as o join analyzing.payments as p
on p.order_id=o.order_id
where extract(year from o.order_purchase_timestamp) = 2018 AND extract(month from o.order_purchase_timestamp) BETWEEN 1 AND 8
)
SELECT  y17.cost_of_2017,
    y18.cost_of_2018, concat(ROUND(((y18.cost_of_2018 - y17.cost_of_2017) / y17.cost_of_2017) * 100), 
        '%') AS Percentage_Increase
	 from y17, y18
       ```
     - Insights: The cost of orders increased by 137% between Jan-Aug 2017 and Jan-Aug 2018. This growth highlights the effectiveness of marketing strategies and expanding customer adoption of e-commerce.

3. Calculate the total & average value of order prices and freight for each state.
   - **Answer:**
     - Using the query:  
       ```sql
      SELECT c.customer_state, 
    CAST(ROUND(SUM(oi.freight_value), 0) AS FLOAT) AS total_freight_value, 
    CAST(ROUND(AVG(oi.freight_value), 0) AS FLOAT) AS avg_freight_value
FROM analyzing.customers c JOIN analyzing.orders o  ON c.customer_id = o.customer_id 
JOIN analyzing.order_items oi ON o.order_id = oi.order_id
GROUP BY c.customer_state
ORDER BY c.customer_state;

       ```
     - Insights: States like PB have the highest average order prices, indicating strong purchasing power. SP has the highest order volumes but lower average prices, reflecting its broader demographic and affordability.

### **Analysis on Sales, Freight, and Delivery Time (20 points)**
1. Calculate the delivery time and the difference between estimated and actual delivery dates.
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT order_id, datediff(order_delivered_customer_date,order_purchase_timestamp) AS Time_to_deliver_days,
datediff(order_estimated_delivery_date,order_delivered_customer_date) AS diff_estimated_delivery
from analyzing.orders
order by order_id
 
       ```
     - Insights: Several regions reported significant delays, especially in rural areas. Efficient urban logistics hubs outperformed, delivering closer to or earlier than estimated dates.

2. Identify the top 5 states with the highest & lowest average freight values.
   - **Answer:**
     - Using the query:  
       ```sql
       with ranked_data as (
	select 
		c.customer_state,round(AVG(oi.freight_value)) as avg_freight_value, ROW_NUMBER() OVER (ORDER BY AVG(oi.freight_value) DESC) AS rank_high,
        ROW_NUMBER() OVER (ORDER BY AVG(oi.freight_value) asc) AS rank_low
        from analyzing.customers c
        join analyzing.orders o on c.customer_id=o.customer_id
        join analyzing.order_items oi on o.order_id=oi.order_id
        GROUP BY 
        c.customer_state
        )
        select  rd_high.customer_state AS highest_state,
    rd_high.avg_freight_value AS highest_avg_freight,
    rd_low.customer_state AS lowest_state, 
    rd_low.avg_freight_value AS lowest_avg_freight
    from ranked_data rd_high
    JOIN ranked_data rd_low ON rd_high.rank_high = rd_low.rank_low
    WHERE rd_high.rank_high <= 5
    ORDER BY rd_high.rank_high;
        
        
       ```
     - Insights: PB and RR have the highest freight values, highlighting logistics inefficiencies in these states. SP and RJ benefit from streamlined logistics, with the lowest freight costs.

3. Identify the top 5 states with the highest & lowest average delivery times.
   - **Answer:**
     - Using the query:  
       ```sql
      WITH delivery_data AS (
    SELECT customer_id,DATEDIFF(order_delivered_customer_date, order_purchase_timestamp) AS delivery_time
    FROM analyzing.orders
),
ranked_data AS (
    SELECT 
        c.customer_state, 
        ROUND(AVG(d.delivery_time)) AS avg_delivery_time,
        ROW_NUMBER() OVER (ORDER BY ROUND(AVG(d.delivery_time)) DESC) AS rank_high,
        ROW_NUMBER() OVER (ORDER BY ROUND(AVG(d.delivery_time)) ASC) AS rank_low
    FROM analyzing.customers c JOIN delivery_data d ON c.customer_id = d.customer_id
    GROUP BY c.customer_state
)
SELECT 
    rd_high.customer_state AS highest_state,
    rd_high.avg_delivery_time AS highest_avg_delivery_time,
    rd_low.customer_state AS lowest_state,
    rd_low.avg_delivery_time AS lowest_avg_delivery_time
FROM ranked_data rd_high JOIN ranked_data rd_low ON rd_high.rank_high = rd_low.rank_low
WHERE rd_high.rank_high <= 5
ORDER BY rd_high.rank_high;

       ```
     - Insights: States with high delivery times require improved warehousing and transportation networks. Low-performing states like RR can learn from faster delivery models seen in SP.

4. Identify the top 5 states where delivery is faster than the estimated date.
   - **Answer:**
     - Using the query:  
       ```sql
       WITH cte AS (
    SELECT 
        customer_id, order_id, DATEDIFF(order_estimated_delivery_date, order_delivered_customer_date) AS diff_estimated_delivery
    FROM analyzing.orders
    WHERE order_delivered_customer_date IS NOT NULL AND order_status = 'delivered'
)
SELECT c.customer_state, ROUND(AVG(ct.diff_estimated_delivery), 1) AS avg_delivery_time_day
FROM cte ct JOIN analyzing.customers c ON c.customer_id = ct.customer_id
GROUP BY c.customer_state
ORDER BY avg_delivery_time_day ASC
LIMIT 5;

       ```
     - Insights: Efficient delivery practices in states like SP and RJ should be analyzed and replicated to improve logistics across all regions.

### **Analysis Based on Payments (10 points)**
1. Month-on-month number of orders placed using different payment types.
   - **Answer:**
     - Using the query:  
       ```sql
      SELECT extract(YEAR FROM order_purchase_timestamp) AS Order_year,
extract(MONTH From order_purchase_timestamp) as Order_month,
p.payment_type as Payment_method, count(p.order_id) as Number_of_orders 
from analyzing.orders o join analyzing.payments p
on o.order_id=p.order_id
group by Order_year,Order_month,Payment_method
order by Order_year,Order_month,Payment_method
       ```
     - Insights: Credit cards dominate payment methods, with a slight seasonal uptick in installment-based options during high-demand periods.

2. Number of orders based on payment installments.
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT payment_installments, 
    COUNT(order_id) AS Number_of_orders FROM analyzing.payments
WHERE payment_installments >= 1
GROUP BY payment_installments
ORDER BY payment_installments;

       ```
     - Insights: Single-installment payments are preferred, accounting for 80% of orders. This suggests a low dependency on credit, with customers opting for straightforward transactions.

### **Actionable Insights & Recommendations (10 points)**
Actionable Insights
Marketing Focus
States like SP and RJ exhibit high order volumes and significant customer engagement. Continuing and expanding marketing campaigns in these regions is essential for sustained revenue growth.
States like RR and AP have minimal engagement, signaling untapped potential. Targeted campaigns such as discounts or exclusive deals in these regions could increase customer base and order frequency.

Logistics Optimization
Freight costs in states like PB and RR are disproportionately high. Optimizing warehousing and establishing local distribution centers in these regions could reduce costs.
Rural areas experiencing delays require improved transportation infrastructure or partnerships with local courier services.

Seasonal Strategy
Seasonal spikes, especially in November, should be capitalized on by preparing adequate inventory levels, ramping up marketing efforts, and ensuring logistic readiness.
Low order periods (January-March) can be targeted with sales promotions or loyalty programs to balance seasonal fluctuations.

Payment Incentives
Credit cards dominate payment methods, but installment-based options see a seasonal rise. Promoting installment payment options during peak seasons could encourage larger purchases.
Simplifying access to other payment methods such as vouchers or UPI could help diversify customer payment preferences.

Customer Engagement
States with lower customer satisfaction, identified through review scores, require immediate attention. Analyzing review feedback and addressing concerns will help build trust.
Proactively gathering feedback through surveys or loyalty programs can identify service gaps and improve overall customer experience.

Expanding Efficient Models
States like SP and RJ with early delivery and lower freight costs should serve as benchmarks. Insights from these regions can be applied to optimize operations in slower-performing areas.

Recommendations
Expand Presence in Untapped Regions
Focus on underperforming states like RR and AP through targeted marketing campaigns and establishing local warehouses.

Optimize Logistics Operations
Reduce high freight costs in PB and RR by improving transportation networks and establishing more distribution centers.
Partner with local logistics providers in rural areas to minimize delivery delays.

Leverage Seasonal Opportunities
Prepare for November peaks with promotions, inventory planning, and logistical readiness.
Use slow months like January-March to introduce sales or loyalty programs to sustain order volumes.

Promote Flexible Payment Options
Offer incentives for installment payments, especially during peak periods, to encourage higher-value purchases.
Diversify and simplify payment methods to attract a broader customer base.

Enhance Customer Satisfaction
Address low review scores by analyzing customer feedback and resolving pain points.
Implement proactive strategies like surveys and loyalty rewards to improve customer retention and satisfaction.

Benchmark and Scale Efficient Operations
Study operations in SP and RJ to identify strategies for reducing delivery times and freight costs, then replicate these in underperforming regions.
---

