## Solutions
1. What percentage of customers made returns?
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

2.  Which age group has the highest total purchase value?
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
