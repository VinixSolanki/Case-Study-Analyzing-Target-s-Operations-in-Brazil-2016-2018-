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
     - The `customers.csv` file contains columns such as `customer_id` (string), `customer_unique_id` (string), `customer_zip_code_prefix` (integer), `customer_city` (string), and `customer_state` (string).

3. Get the time range between which the orders were placed.
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT MIN(order_purchase_timestamp) AS FirstOrder, MAX(order_purchase_timestamp) AS LastOrder
       FROM orders;
       ```
     - Insights: The first order was placed in September 2016, and the last order in October 2018.

4. Count the cities & states of customers who ordered during the given period.
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT COUNT(DISTINCT customer_city) AS Cities, COUNT(DISTINCT customer_state) AS States
       FROM customers;
       ```
     - Insights: There were **400+ unique cities** and **27 states** involved in placing orders during the given period.

### **In-depth Exploration (15 points)**
1. Identify trends in the number of orders placed over the years.
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT EXTRACT(YEAR FROM order_purchase_timestamp) AS OrderYear,
              COUNT(order_id) AS NumberOfOrders
       FROM orders
       GROUP BY OrderYear
       ORDER BY OrderYear;
       ```
     - Insights: There was a steady increase in the number of orders from 2016 to 2017. A peak was observed in late 2017, followed by a decline in 2018.

2. Detect any monthly seasonality in the number of orders.
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT EXTRACT(MONTH FROM order_purchase_timestamp) AS OrderMonth,
              COUNT(order_id) AS NumberOfOrders
       FROM orders
       GROUP BY OrderMonth
       ORDER BY NumberOfOrders DESC;
       ```
     - Insights: November consistently showed high order volumes, indicating seasonal peaks. January through March were relatively low.

3. Determine the time of day when Brazilian customers mostly place orders (Dawn, Morning, Afternoon, or Night).
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT CASE
                WHEN HOUR(order_purchase_timestamp) BETWEEN 0 AND 6 THEN 'Dawn (0-6)'
                WHEN HOUR(order_purchase_timestamp) BETWEEN 7 AND 12 THEN 'Morning (7-12)'
                WHEN HOUR(order_purchase_timestamp) BETWEEN 13 AND 18 THEN 'Afternoon (13-18)'
                ELSE 'Night (19-23)'
              END AS TimeOfDay,
              COUNT(*) AS NumberOfOrders
       FROM orders
       GROUP BY TimeOfDay
       ORDER BY NumberOfOrders DESC;
       ```
     - Insights: Most orders were placed during the afternoon (13-18 hrs), followed by night (19-23 hrs). Dawn (0-6 hrs) was the least active period.

### **Evolution of E-commerce Orders in Brazil (10 points)**
1. Month-on-month number of orders placed in each state.
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT c.customer_state, 
              EXTRACT(YEAR FROM o.order_purchase_timestamp) AS OrderYear, 
              EXTRACT(MONTH FROM o.order_purchase_timestamp) AS OrderMonth, 
              COUNT(o.order_id) AS NumberOfOrders
       FROM orders o
       JOIN customers c ON o.customer_id = c.customer_id
       GROUP BY c.customer_state, OrderYear, OrderMonth
       ORDER BY c.customer_state, OrderYear, OrderMonth;
       ```
     - Insights: SP consistently showed the highest number of orders monthly, while smaller states like RR had minimal orders. Seasonal spikes in November were observed across most states.

2. Distribution of customers across all states.
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT customer_state, COUNT(DISTINCT customer_unique_id) AS NumberOfCustomers
       FROM customers
       GROUP BY customer_state
       ORDER BY NumberOfCustomers DESC;
       ```
     - Insights: State SP has the maximum number of customers, capturing nearly 40% of the customer base. States like RR and AP showed minimal engagement, indicating potential areas for growth and targeted campaigns.

### **Impact on Economy (20 points)**
1. Analyze money movement by looking at order prices, freight, and other factors.
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT SUM(oi.price) AS TotalOrderPrice, 
              SUM(oi.freight_value) AS TotalFreightValue
       FROM order_items oi;
       ```
     - Insights: Freight costs account for nearly 20% of the total transaction value. This suggests a need for freight optimization strategies to improve profit margins.

2. Calculate the percentage increase in the cost of orders from 2017 to 2018 (Jan-Aug).
   - **Answer:**
     - Using the query:  
       ```sql
       WITH cost_2017 AS (
           SELECT SUM(p.payment_value) AS Total2017
           FROM payments p
           JOIN orders o ON p.order_id = o.order_id
           WHERE YEAR(o.order_purchase_timestamp) = 2017 
             AND MONTH(o.order_purchase_timestamp) BETWEEN 1 AND 8
       ),
       cost_2018 AS (
           SELECT SUM(p.payment_value) AS Total2018
           FROM payments p
           JOIN orders o ON p.order_id = o.order_id
           WHERE YEAR(o.order_purchase_timestamp) = 2018 
             AND MONTH(o.order_purchase_timestamp) BETWEEN 1 AND 8
       )
       SELECT (c2018.Total2018 - c2017.Total2017) / c2017.Total2017 * 100 AS PercentageIncrease
       FROM cost_2017 c2017, cost_2018 c2018;
       ```
     - Insights: The cost of orders increased by 137% between Jan-Aug 2017 and Jan-Aug 2018. This growth highlights the effectiveness of marketing strategies and expanding customer adoption of e-commerce.

3. Calculate the total & average value of order prices and freight for each state.
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT c.customer_state, 
              SUM(oi.price) AS TotalOrderPrice, 
              AVG(oi.price) AS AvgOrderPrice, 
              SUM(oi.freight_value) AS TotalFreightValue, 
              AVG(oi.freight_value) AS AvgFreightValue
       FROM order_items oi
       JOIN orders o ON oi.order_id = o.order_id
       JOIN customers c ON o.customer_id = c.customer_id
       GROUP BY c.customer_state;
       ```
     - Insights: States like PB have the highest average order prices, indicating strong purchasing power. SP has the highest order volumes but lower average prices, reflecting its broader demographic and affordability.

### **Analysis on Sales, Freight, and Delivery Time (20 points)**
1. Calculate the delivery time and the difference between estimated and actual delivery dates.
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT order_id,
              DATEDIFF(order_delivered_customer_date, order_purchase_timestamp) AS DeliveryTime,
              DATEDIFF(order_estimated_delivery_date, order_delivered_customer_date) AS DeliveryDiff
       FROM orders;
       ```
     - Insights: Several regions reported significant delays, especially in rural areas. Efficient urban logistics hubs outperformed, delivering closer to or earlier than estimated dates.

2. Identify the top 5 states with the highest & lowest average freight values.
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT c.customer_state, AVG(oi.freight_value) AS AvgFreight
       FROM order_items oi
       JOIN orders o ON oi.order_id = o.order_id
       JOIN customers c ON o.customer_id = c.customer_id
       GROUP BY c.customer_state
       ORDER BY AvgFreight DESC;
       ```
     - Insights: PB and RR have the highest freight values, highlighting logistics inefficiencies in these states. SP and RJ benefit from streamlined logistics, with the lowest freight costs.

3. Identify the top 5 states with the highest & lowest average delivery times.
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT c.customer_state, AVG(DATEDIFF(order_delivered_customer_date, order_purchase_timestamp)) AS AvgDeliveryTime
       FROM orders o
       JOIN customers c ON o.customer_id = c.customer_id
       GROUP BY c.customer_state
       ORDER BY AvgDeliveryTime DESC;
       ```
     - Insights: States with high delivery times require improved warehousing and transportation networks. Low-performing states like RR can learn from faster delivery models seen in SP.

4. Identify the top 5 states where delivery is faster than the estimated date.
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT c.customer_state, 
              AVG(DATEDIFF(order_estimated_delivery_date, order_delivered_customer_date)) AS AvgEarlyDelivery
       FROM orders o
       JOIN customers c ON o.customer_id = c.customer_id
       WHERE DATEDIFF(order_estimated_delivery_date, order_delivered_customer_date) > 0
       GROUP BY c.customer_state
       ORDER BY AvgEarlyDelivery DESC;
       ```
     - Insights: Efficient delivery practices in states like SP and RJ should be analyzed and replicated to improve logistics across all regions.

### **Analysis Based on Payments (10 points)**
1. Month-on-month number of orders placed using different payment types.
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT EXTRACT(YEAR FROM order_purchase_timestamp) AS OrderYear,
              EXTRACT(MONTH FROM order_purchase_timestamp) AS OrderMonth,
              payment_type, COUNT(*) AS NumberOfOrders
       FROM payments p
       JOIN orders o ON p.order_id = o.order_id
       GROUP BY OrderYear, OrderMonth, payment_type
       ORDER BY OrderYear, OrderMonth;
       ```
     - Insights: Credit cards dominate payment methods, with a slight seasonal uptick in installment-based options during high-demand periods.

2. Number of orders based on payment installments.
   - **Answer:**
     - Using the query:  
       ```sql
       SELECT payment_installments, COUNT(*) AS NumberOfOrders
       FROM payments
       GROUP BY payment_installments
       ORDER BY NumberOfOrders DESC;
       ```
     - Insights: Single-installment payments are preferred, accounting for 80% of orders. This suggests a low dependency on credit, with customers opting for straightforward transactions.

### **Actionable Insights & Recommendations (10 points)**
1. Provide insights and recommendations based on the analysis.
   - **Answer:**
     - **Marketing Focus:** States like SP and RJ should continue receiving marketing focus due to their high order volumes. For states like RR and AP, targeted campaigns could boost visibility.
     - **Logistics Optimization:** States with high freight costs and delays, such as PB and RR, need improved warehousing and transportation solutions.
     - **Seasonal Strategy:** Seasonal peaks in November should be leveraged with adequate inventory planning and promotional efforts.
     - **Payment Incentives:** Promote installment options during peak seasons to encourage larger purchases.
     - **Expand Efficient Models:** States with early deliveries (SP, RJ) should serve as benchmarks to refine operations in slower regions.
     - **Customer Engagement:** Utilize reviews to address low satisfaction scores in underperforming areas, fostering trust and brand loyalty.

---

