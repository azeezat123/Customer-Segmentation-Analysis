# Customer-Purchasing-Behaviour-Analysis
## Tables Creation in postgreSQL

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

