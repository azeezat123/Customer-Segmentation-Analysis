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


### Tables Creation in postgreSQL

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
![Screenshot of the Entity Relationship Diagram (ERD) image, added in the Markdown.](https://github.com/azeezat123/Customer-Purchasing-Behaviour-Analysis/blob/main/Entity%20Relationship%20Diagram.png)

