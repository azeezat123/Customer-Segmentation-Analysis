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

During the assessment, the only issue found was related to the naming convention of the attribute names. Therefore, I made changes to the names to conform to a suitable SQL naming format, considering that my analysis would be done using Postgres.


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

With the tables created, I loadED the data from the original dataset into these tables to start performing analyses on them.

For my analysis i came up with a set of business questions i would like to tackle 

