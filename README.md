# Customer Segmentation Analysis

> [Introduction](https://github.com/azeezat123/Customer-Segmentation-Analysis/edit/main/README.md#introduction) <br>
> [Data Assesment](https://github.com/azeezat123/Customer-Segmentation-Analysis/edit/main/README.md#data-assessment)<br>
> [Data Analysis](https://github.com/azeezat123/Customer-Segmentation-Analysis/edit/main/README.md#data-analysis) <br>
> [Data Visualization](https://github.com/azeezat123/Customer-Segmentation-Analysis/edit/main/README.md#data-visualization) <br>
> [Insights](https://github.com/azeezat123/Customer-Segmentation-Analysis/blob/main/README.md#insights) <br>
> [Recommendations](https://github.com/azeezat123/Customer-Segmentation-Analysis/blob/main/README.md#recommendations) <br>


## Introduction

I recently had the opportunity to participate in my first hackathon, Pickl.AI’s Datathon-4. During the final phase of the competition, I was tasked with analyzing a company's dataset to solve a specific problem statement. 

The problem statement was centered around the growing complexities of customer demand, which have made it mandatory for companies to focus on customer satisfaction. The solution proposed was to segment customers based on their preferences, age groups, and geographical locations. This approach can help organizations create a personalized experience for each user group, leading to increased brand loyalty.

For instance, if an e-commerce business segments its customers by age and preferences, then it can offer personalized product recommendations based on these preferences. This kind of personalization demonstrates to customers that their individual needs are a priority for the organization, leading to repeat purchases and increased customer satisfaction.

The significance of customer segmentation cannot be overstated, as it is a powerful tool that can help businesses generate more customers and increase revenue. As part of the competition, I was required to come up with various insights to achieve these goals and build an interactive dashboard.

Overall, the challenge was an enriching experience, and I learned a great deal about the importance of customer segmentation in today's competitive business landscape.


***

## Data Assessment

To ensure that the original dataset was not altered during the assessment process, I started by duplicating the sheet in Excel. This was done to avoid losing the original dataset in case it was needed for future referencing or cross-checking. The dataset consisted of 7000 rows and 23 columns.

The following steps were taken to assess the data quality in Excel:

* I used the COUNTIF function in combination with the IF function to check for missing or null values. The formula used was: =IF(COUNTIF(A2:W7001, " ") >0, "Null value", "No null value"). Fortunately, no missing or null values were found.
* Since each customer has a unique ID, I utilized conditional formatting to identify and highlight any duplicate ID numbers that may have occurred as a result of data collection or entry errors. However, no duplicates were found.
* I used the data validation tool to check for inconsistent data types across all columns and ensure that the data was accurate and consistent.

During the assessment, the only issue found was related to the naming convention of the attribute names. Therefore, I made changes to the attributes names to conform to a suitable SQL naming format, considering that my analysis would be done using Postgres.


***

## Data Analysis

After assessing the dataset, I identified that some attributes had repeating groups of values, which could lead to data redundancy and inconsistencies. Therefore, to avoid these issues, the dataset was normalized and broken down into two tables - Customers and Products - each with a single theme or entity.

- **To create the Customers table, I used the following SQL query:**

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

- **To create the Products table, I used the following SQL query:**
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


***

## Data Visualization 
The data visualization was created using Power BI. 

![Image](https://github.com/azeezat123/Customer-Segmentation-Analysis/blob/main/Customer%20Demographics%20and%20Purchasing%20Behavior.png)

interact with the dashboard [here](https://github.com/azeezat123/Customer-Segmentation-Analysis/edit/main/README.md#data-visualization)

*** 

## Insights 
1. I noticed that 83.83% of customers made returns, while the remaining 16.17% did not make any returns. This high return rate could indicate issues with product quality or dissatisfaction with the purchasing experience.
2. The purchasing behavior of customers is relatively similar across different age groups. However, customers in the 18-24 age group have a slightly lower total purchase value compared to the other age groups. This could potentially indicate that the purchasing power of customers in the 18-24 age group is relatively lower, or that they have different preferences in terms of product categories or brands.
3. Both genders have nearly similar purchase counts across all product categories, showing that the products are gender-neutral and have the potential to appeal to a larger customer base, regardless of gender.
4. From the analysis, customers with "Excellent" credit scores are the most valuable to the company, while those with "Bad" credit scores are the least valuable. This may indicate that they are not as financially stable and may require different marketing strategies to target their purchasing behavior. However, customers with a "Poor" credit score have made a higher total purchase value than those with a "Fair" or "Good" credit score, indicating that they may be more willing to make purchases despite financial constraints. Therefore, they are a possible target market for our marketing efforts.
5. Based on the data, there does not seem to be a significant correlation between income level and total purchase value.


***

## Recommendations 
1. Conduct a customer survey to identify issues causing high return rates and make necesary improvements to the product or purchasing experience, Additionally, the return policy should be reviewed to ensure that it strikes a balance between being customer-friendly and sustainable for the business. These steps can reduce return rates, improve customer satisfaction and retention, and enhance overall performance.
2. the business could explore offering targeted promotions or discounts to customers in the 18-24 age group to incentivize them to make more purchases. Additionally, they could consider partnering with influencers or social media platforms to reach a younger demographic and increase brand awareness.
3. To further engage customers, the company may want to consider offering gender-specific promotions or product bundles.
4. The company should consider tailoring their marketing strategies to cater to customers with different credit score ranges. For customers with "Excellent" credit scores, the focus should be on maintaining customer loyalty and offering premium products or services. For customers with "Poor" credit scores, the company should offer promotions or product bundles that cater to their budget constraints while still providing value.
5. While income level does not seem to affect purchasing behavior significantly, the company may still want to offer promotions or products that appeal to customers with higher income levels to increase their purchases.


***

Thank you for taking the time to read through this project. If you've made it this far, I truly appreciate your interest and attention. If you have any contributions or suggestions, please don't hesitate to reach out to me. I would love to hear from you and discuss any ideas you may have. You can find me on 
[Twitter](https://twitter.com/zezaht) or on [LinkedIn](https://www.linkedin.com/in/sogbesanteni)
