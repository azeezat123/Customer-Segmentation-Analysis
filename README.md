# Customer Segmentation Analysis

> *Introduction* <br>
> *Problem Statement* <br>
> *Data Assesment* <br>
> *Data Aanalysis* <br>
> Data Visualization in Power BI* <br>
> *Insights & Recommendations*


## Inroduction



## Problem Statement


## Data Assessment

To ensure that the original dataset was not altered during the assessment process, I started by duplicating the sheet in Excel. This was done to avoid losing the original dataset in case it was needed for future referencing or cross-checking. The dataset consisted of 7000 rows and 23 columns.

The following steps were taken to assess the data quality:

* I used the COUNTIF function in combination with the IF function to check for missing or null values. The formula used was: =IF(COUNTIF(A2:W7001, " ") >0, "Null value", "No null value"). Fortunately, no missing or null values were found.
* Since each customer has a unique ID, I utilized conditional formatting to identify and highlight any duplicate ID numbers that may have occurred as a result of data collection or entry errors. However, no duplicates were found.
* I used the data validation tool to check for inconsistent data types across all columns and ensure that the data was accurate and consistent.

During the assessment, the only issue found was related to the naming convention of the attribute names. Therefore, I made changes to the attributes names to conform to a suitable SQL naming format, considering that my analysis would be done using Postgres.


## Data Analysis
After assessing the dataset, I identified that some attributes had repeating groups of values, which could lead to data redundancy and inconsistencies. Therefore, to avoid these issues, the dataset was normalized and broken down into two tables - Customers and Products - each with a single theme or entity.

**To create the Customers table, I used the following SQL query:**

```sql
CREATE TABLE customers (
	customer_id varchar(10) PRIMARY KEY,
	gender varchar(50),
	age int,
	income int,
	occupation varchar(50),
	marital_status varchar(50),
	no_family_members int,	
	home_ownership varchar(50),
	home_value int,
	years_in_home int,
	credit_score int,
	no_of_credit_card int,
	total_credit_limit int,
	total_credit_balance int,
  product_id varchar(10) REFERENCES products(id)
  );
```
This query creates a table called "customers" with columns for each attribute in the dataset that pertains to customers. I specified a primary key for the table using the "customer_id" attribute to ensure that each record in the table is unique. To establish a relationship between the Customers and Products tables, I included a foreign key constraint to reference the "id" attribute in the Products table.

**To create the Products table, I used the following SQL query:**
``` sql
 CREATE TABLE products (
    id varchar(10) PRIMARY KEY,
    product_purchased varchar(50),	
    no_of_online_orders int,
    average_purchase_value float,
    last_purchase_value float,
    days_since_last_purchase int,
    number_of_returns int,
    total_value_returns float,
    locations varchar(50)
  
);
```
This query creates a table called "products" with columns for each attribute in the dataset that pertains to products. I specified a primary key for the table using the "id" attribute to ensure that each record in the table is unique.

### Entity Relationship Diagram
![Screenshot of the Entity Relationship Diagram (ERD) image, added in the Markdown.](https://github.com/azeezat123/Customer-Purchasing-Behaviour-Analysis/blob/main/Entity%20Relationship%20Diagram.png)

With the tables created, I loaded the data from the original dataset into these tables to begin my analysis

Based on the problem statement provided, I identified several key business questions to exlpore. One of the primary questions I sought to address was: "What factors are most associated with customer purchasing behavior, and how can I leverage these insights to improve sales and revenue?". The following are the business questions I identified for analysis 

1. what percentage of customers returned their purchases?
```sql
 SELECT ROUND (COUNT (DISTINCT 
	    CASE 
	  	WHEN number_of_returns >= 1 THEN product_id END) * 100.0 / COUNT (DISTINCT product_id), 2) AS percentage_returns,
       ROUND (COUNT (DISTINCT 
	   CASE 
		 WHEN number_of_returns = 0 THEN product_id END) * 100.0 / COUNT (DISTINCT product_id), 2) AS percentage_no_returns
FROM products;
```
| percentage_returns | percentage_no_returns |
|------------------|-------------|
|     83.83     |   16.17   |


***

2. Is there a relationship between age and total purchase value?
```sql
SELECT CASE 
	WHEN age between 18 AND 24 THEN '18-24'
	WHEN age between 25 AND 34 THEN '25-34'
	WHEN age between 35 AND 44 THEN '35-44'
	WHEN age between 45 AND 54 THEN '45-54'
	WHEN age between 55 AND 64 THEN '55-64'
 END AS age_group,
 ROUND(SUM(P.average_purchase_value):: numeric, 0) AS total_purchase
 FROM products p 
 JOIN customers c ON c.product_id = p.id
 GROUP BY 1
 ORDER BY 2 DESC;
```
| age_group | total_purchase                     |
|--------------|-----------                    |
|   25-34     |   152970 |
|   45-54     |  150335                  |
|    55-64   |   149281                      |
|    35-44   |   146736                    |
|     18-24  |   106114        |


***

3. What are the most popular products among male and female customers?
```sql
SELECT C.gender, p.product_purchased, COUNT (*) AS purchase_count
FROM customers c
JOIN products p ON p.id = c.product_id
GROUP BY 1, 2
ORDER BY 2;
```

| gender | product_Purchased | purchase_count |
|--------|------------------|---------------|
| Female | Beauty Products  | 509           |
| Male   | Beauty Products  | 491           |
| Male   | Books            | 496           |
| Female | Books            | 504           |
| Female | Clothing         | 484           |
| Male   | Clothing         | 516           |
| Female | Electronics      | 478           |
| Male   | Electronics      | 522           |
| Male   | Home Appliances  | 475           |
| Female | Home Appliances  | 525           |
| Female | Jewelry          | 504           |
| Male   | Jewelry          | 496           |
| Male   | Outdoor Gear     | 504           |
| Female | Outdoor Gear     | 496           |


***

4.  Does credit score impact customers' purchasing behavior? 
```sql
SELECT CASE
	WHEN credit_score < 550 THEN 'Bad'
	WHEN credit_score BETWEEN 550 AND 649 THEN 'Poor' 
	WHEN credit_score BETWEEN 650 AND 699 THEN 'Fair' 
	WHEN credit_score BETWEEN 700 AND 749 THEN 'Good'
	WHEN credit_score BETWEEN 750 AND 799 THEN 'Very Good' 
	ELSE'Excellent'
END AS credit_score_range,
ROUND(SUM(average_purchase_value)::numeric, 0) AS total_purchase_value
FROM customers c 
JOIN products p ON c.product_id = p.id
GROUP BY 1
ORDER BY 2 DESC;
```

| credit_score_range | total_purchase_value |
|--------------------|----------------|
| Excellent          | 280399         |
| Poor               | 139424         |
| Bad                | 74383          |
| Very Good          | 73445          |
| Fair               | 69911          |
| Good               | 67874          |


***

5. Which occupational groups of customers have the highest average income, and does this income level affect their purchasing behaviour?
```sql
SELECT c.occupation, 
       ROUND(AVG(c.income),0) average_income, 
       SUM(no_of_online_orders) total_purchase
FROM customers c 
JOIN products p ON c.product_id = p.id
GROUP BY 1
ORDER BY 2 DESC;
```

| occupation   | average_income | total_purchase |
|--------------|----------------|----------------|
| Technician   | 40286          | 10127          |
| Student      | 40151          | 9991           |
| Executive    | 39916          | 10463          |
| Manager      | 39822          | 9845           |
| Homemaker    | 39787          | 9982           |
| Professional | 39730          | 10066          |
| Self-employed| 39184          | 10115          |

