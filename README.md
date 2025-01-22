# E-Commerce Sales Analysis

## Table of Contents

- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Data Analysis](#data-analysis)
- [Reports](#reports)
- [Recommendations](#recommendations)

### Project Overview

This data analysis project aims to provide insights into the sales performance of an e-commerce company in the period of 2016 to 2018. By analyzing various aspects of the sales data, we seek to identify trends, make data-driven recommendations and gain a deeper understanding of the company's performance.

### Data Sources

Sales Data: The primary dataset used for this analysis is inside the Load Data code, downloaded from SQLservertutorial.net, contained detailed information about each sale made by the company.

### Tools

- MS SQL Server - Data Inspection and Analysis [Download here](https://microsoft.com)
- Microsoft Excel - Exploratory Data Analysis [Download here](https://microsoft.com)
- Tableau - Creating reports [Download here](https://www.tableau.com/es-es)

### Data Preparation

In the initial data preparion phase, we performed the following tasks:
1. Data loading and inspection.
2. Data Analysis
3. Data extraction.

### Exploratory Data Analysis

EDA involved exploring the sales data to answer key questions, such as:

- What is the overall sales trend across the different years?
- Which category of products are top sellers?
- What are the revenue by distinct metrics like customers, brands, sales representants?
- Which state where the most profitables? And which stores?

### Data Analysis

In the Data Analysis we did some queries to answer questions like:
- Top ten products with more purchases historically
``` sql
SELECT TOP 10
	pro.product_name, ord.list_price,
	SUM(ord.quantity) as units_sold,
	SUM(ord.list_price * ord.quantity) as revenue
FROM production.products as pro
JOIN sales.order_items as ord ON pro.product_id = ord.product_id
GROUP BY pro.product_id, pro.product_name, ord.list_price
ORDER BY units_sold DESC;
```
![image](https://github.com/user-attachments/assets/36609bd8-0030-4283-a7fe-abb9ced3d34c)

- Category with the max revenue for 2018
``` sql
SELECT TOP 3
	cat.category_id, cat.category_name,
	SUM(ord.quantity) as units_sold,
	SUM(ord.list_price * ord.quantity) as revenue
FROM sales.order_items as ord
JOIN production.products as pro ON ord.product_id = pro.product_id
JOIN production.categories as cat ON pro.category_id = cat.category_id
JOIN sales.orders as sord ON ord.order_id = sord.order_id
WHERE sord.order_date >= '2018-01-01'
GROUP BY cat.category_id, cat.category_name
ORDER BY revenue DESC;
```
![image](https://github.com/user-attachments/assets/890ad466-ccf2-4635-a042-7be6ee581425)

- More profitable products historically
``` sql
SELECT TOP 10 
	pro.product_id, pro.product_name, ord.list_price,
	SUM(ord.quantity) as units_sold, 
	SUM(ord.list_price * ord.quantity) as revenue,
	AVG(ord.list_price / ord.quantity) as average_revenue_per_unit
FROM production.products as pro
JOIN sales.order_items as ord ON pro.product_id = ord.product_id
GROUP BY pro.product_id, pro.product_name, ord.list_price
ORDER BY revenue DESC;
```
![image](https://github.com/user-attachments/assets/51a1a280-7f1b-4c6e-9a26-faeea71f6fc2)

- Top ten clients with more purchases for 2018
``` sql
SELECT TOP 10
	(cust.first_name + ' ' + cust.last_name) as customer,
	cust.phone, cust.email, cust.city, cust.state, YEAR(sord.order_date) as years,
	COUNT(sord.order_id) as number_of_purchases, SUM(ord.list_price * ord.quantity) as revenue
FROM sales.orders as sord
JOIN sales.customers as cust ON sord.customer_id = cust.customer_id
JOIN sales.order_items as ord ON ord.order_id = sord.order_id
WHERE YEAR(sord.order_date) = 2018
GROUP BY YEAR(sord.order_date), cust.first_name, cust.last_name, cust.phone, cust.city, cust.state, cust.email
ORDER BY number_of_purchases DESC;
```
![image](https://github.com/user-attachments/assets/5cdaf35e-cb0a-4d87-9630-c28f20d7b769)


- Store with the most sales in 2018
``` sql
SELECT 
	sord.store_id, stor.store_name, COUNT(sord.order_id) as quantity_of_sales
FROM sales.orders as sord
JOIN sales.stores as stor ON sord.store_id = stor.store_id
JOIN sales.staffs as staf ON sord.staff_id = staf.staff_id
WHERE YEAR(sord.order_date) = 2018
GROUP BY sord.store_id, stor.store_name
ORDER BY quantity_of_sales DESC;
```
![image](https://github.com/user-attachments/assets/350adf13-07fb-48da-a392-820f1dd1c0ad)

- Quantity of orders shipped late in 2018
``` sql
SELECT 
	stor.store_id, stor.store_name, COUNT(sord.order_id) as quantity_of_order_late_shipped
FROM sales.orders as sord
JOIN sales.stores as stor ON sord.store_id = stor.store_id
WHERE DATEDIFF(day,shipped_date, required_date) < 0 --- AND YEAR(sord.order_date) = 2018
GROUP BY stor.store_id, stor.store_name
ORDER BY quantity_of_order_late_shipped DESC;
```
![image](https://github.com/user-attachments/assets/25e91b23-5192-42d0-b428-4609ae91b1d7)

### Reports

[Tableau Dashboard](https://public.tableau.com/app/profile/fxkundo/viz/Bikestore_17319773047180/Dashboard1?publish=yes)

### Results/Findings

The analysis results are sumarized as follows:
1. The company's sales have been decreasing over the year 2018, with a noticeable peak during the start of the summer which resulted in a better annual report.
2. Product category Road Bikes is the best-performing category in terms of sales and revenue.

### Recommendations

Based on the analysis, we recommend the following actions:
- Focus on expanding and promoting products in Category Road Bikes.
- Investigate the reasons behind the low sales for the Rowlett Bikes store and see if we can work with the sales representant to find the difficulties they encounter everyday with their sales.
- Offer discount or promotions to our best customers to increase the sales and add a recommendation system so they can promote our different stores in social media to receive valuable features.

### Limitations
We observed a sales peak in 2018 between March and June that require further investigation of the reasons of why it happened. This may be of various reasons like the start of the summer, but that didn't happen in prior years.


