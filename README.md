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
2. Data type of all columns in the "customers" table.
3. Get the time range between which the orders were placed.
4. Count the cities & states of customers who ordered during the given period.

### **In-depth Exploration (15 points)**
1. Identify trends in the number of orders placed over the years.
2. Detect any monthly seasonality in the number of orders.
3. Determine the time of day when Brazilian customers mostly place orders (Dawn, Morning, Afternoon, or Night).

### **Evolution of E-commerce Orders in Brazil (10 points)**
1. Month-on-month number of orders placed in each state.
2. Distribution of customers across all states.

### **Impact on Economy (20 points)**
1. Analyze money movement by looking at order prices, freight, and other factors.
2. Calculate the percentage increase in the cost of orders from 2017 to 2018 (Jan-Aug).
3. Calculate the total & average value of order prices and freight for each state.

### **Analysis on Sales, Freight, and Delivery Time (20 points)**
1. Calculate the delivery time and the difference between estimated and actual delivery dates.
2. Identify the top 5 states with the highest & lowest average freight values.
3. Identify the top 5 states with the highest & lowest average delivery times.
4. Identify the top 5 states where delivery is faster than the estimated date.

### **Analysis Based on Payments (10 points)**
1. Month-on-month number of orders placed using different payment types.
2. Number of orders based on payment installments.

### **Actionable Insights & Recommendations (10 points)**
1. Provide insights and recommendations based on the analysis.

---

