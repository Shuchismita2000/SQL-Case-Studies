# Case Study #1: Danny's Diner 
<img src="https://8weeksqlchallenge.com/images/case-study-designs/1.png" alt="Image" width="500" height="520">

## 📚 Table of Contents
- [Business Task](#business-task)
- [Entity Relationship Diagram](#entity-relationship-diagram)
- [Question and Solution](#question-and-solution)

Please note that all the information regarding the case study has been sourced from the following link: [here](https://8weeksqlchallenge.com/case-study-1/). 

***

## Business Task
Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. 

***

## Entity Relationship Diagram

![image](https://github.com/Shuchismita2000/SQL-Case-Studies/blob/main/E_R/%231%20E-R.png)

***

## Question and Solution


**1. What is the total amount each customer spent at the restaurant?**

````sql
SELECT 
  sales.customer_id, 
  SUM(menu.price) AS total_sales
FROM dannys_diner.sales
INNER JOIN dannys_diner.menu
  ON sales.product_id = menu.product_id
GROUP BY sales.customer_id
ORDER BY sales.customer_id ASC; 
````

#### Guideline:
1. `SELECT sales.customer_id, SUM(menu.price) AS total_sales`
This part of the query specifies the columns to be retrieved from the database:

    * sales.customer_id: This column is from the sales table and represents the ID of the customer.
    * SUM(menu.price) AS total_sales: This calculates the total sales for each customer by summing up the prices of the products they purchased. The result is labeled as total_sales.

2. `FROM dannys_diner.sales`
This specifies the sales table as the primary table from which to retrieve data. The sales table likely contains records of sales transactions, including customer_id and product_id.

3. `INNER JOIN dannys_diner.menu ON sales.product_id = menu.product_id`
This performs an inner join between the sales table and the menu table:

    * dannys_diner.menu: This specifies the menu table, which likely contains information about the products, including their prices.
    * ON sales.product_id = menu.product_id: This condition specifies that the join should be made where the product_id in the sales table matches the product_id in the menu table.

4. `GROUP BY sales.customer_id`
This groups the results by customer_id. This means that the SUM(menu.price) calculation will be performed for each unique customer_id.

5. `ORDER BY sales.customer_id ASC`
This orders the final results by customer_id in ascending order. This means the output will list the customers in order from the lowest customer_id to the highest.

#### Summary:
The query retrieves the total sales for each customer from Danny's Diner database. It does this by:

* Joining the sales table with the menu table based on the product_id.
* Summing up the prices of all products purchased by each customer.
* Grouping the results by customer_id to get a total for each customer.
* Ordering the results by customer_id in ascending order.

#### Answer:
| customer_id | total_sales |
| ----------- | ----------- |
| A           | 76          |
| B           | 74          |
| C           | 36          |

- Customer A spent $76.
- Customer B spent $74.
- Customer C spent $36.

***

**2. How many days has each customer visited the restaurant?**

````sql
SELECT 
  customer_id, 
  COUNT(DISTINCT order_date) AS visit_count
FROM dannys_diner.sales
GROUP BY customer_id;
````

#### Guideline:
1. `SELECT customer_id, COUNT(DISTINCT order_date) AS visit_count`
This part of the query specifies the columns to be retrieved from the database:

    * customer_id: This column represents the ID of the customer.
    * COUNT(DISTINCT order_date) AS visit_count: This calculates the number of distinct order_date entries for each customer. The result is labeled as visit_count. This effectively counts how many unique days each customer made a purchase, serving as a proxy for the number of visits.

2. `FROM dannys_diner.sales`
This specifies the sales table as the primary table from which to retrieve data. The sales table likely contains records of sales transactions, including customer_id and order_date.

3. `GROUP BY customer_id`
This groups the results by customer_id. This means that the COUNT(DISTINCT order_date) calculation will be performed for each unique customer_id.

#### Summary:
The query retrieves the number of distinct visit days for each customer from Danny's Diner database. It does this by:

    * Selecting the customer_id and counting the distinct order_date entries for each customer.
    * Grouping the results by customer_id to get a visit count for each customer.

#### Answer:
| customer_id | visit_count |
| ----------- | ----------- |
| A           | 4          |
| B           | 6          |
| C           | 2          |

- Customer A visited 4 times.
- Customer B visited 6 times.
- Customer C visited 2 times.

***

**3. What was the first item from the menu purchased by each customer?**

````sql
WITH ordered_sales AS (
  SELECT 
    sales.customer_id, 
    sales.order_date, 
    menu.product_name,
    DENSE_RANK() OVER (
      PARTITION BY sales.customer_id 
      ORDER BY sales.order_date) AS rank
  FROM dannys_diner.sales
  INNER JOIN dannys_diner.menu
    ON sales.product_id = menu.product_id
)

SELECT 
  customer_id, 
  product_name
FROM ordered_sales
WHERE rank = 1
GROUP BY customer_id, product_name;
````

#### Guidelines:

1. Common Table Expression (CTE): ordered_sales
This CTE creates a temporary result set that assigns a rank to each sale for each customer based on the order date.

    * `sales.customer_id`: The ID of the customer.
    * `sales.order_date`: The date of the order.
    * `menu.product_name`: The name of the product, retrieved from the menu table.
    * `DENSE_RANK() OVER (PARTITION BY sales.customer_id ORDER BY sales.order_date) AS rank`: This assigns a rank to each order per customer, ordered by the order date. `DENSE_RANK` ensures that the rank values are consecutive integers starting from 1 for each customer_id.
2. Main Query
This part of the query retrieves the product names for the first order of each customer.

    * `customer_id:` The ID of the customer.
    * `product_name:` The name of the product from the customer's first order.
    * `FROM ordered_sales:` Refers to the CTE ordered_sales defined earlier.
    * `WHERE rank = 1`: Filters the results to include only the first order for each customer (i.e., where the rank is 1).
    * `GROUP BY customer_id, product_name`: Groups the results by customer_id and product_name to ensure unique combinations of these values are returned.

#### Summary:
The query retrieves the product name(s) from the first order for each customer from Danny's Diner database. It does this by:

    * Creating a temporary result set (ordered_sales) that includes each sale with a rank based on the order date for each customer.
    * Selecting the customer_id and product_name from this temporary result set where the rank is 1, indicating the first order.
    * Grouping by customer_id and product_name to get unique combinations.

#### Answer:
| customer_id | product_name | 
| ----------- | ----------- |
| A           | curry        | 
| A           | sushi        | 
| B           | curry        | 
| C           | ramen        |

- Customer A placed an order for both curry and sushi simultaneously, making them the first items in the order.
- Customer B's first order is curry.
- Customer C's first order is ramen.

I have received feedback suggesting the use of `ROW_NUMBER()` instead of `DENSE_RANK()` for determining the "first order" in this question. 

However, since the `order_date` does not have a timestamp, it is impossible to determine the exact sequence of items ordered by the customer. 

Therefore, it would be inaccurate to conclude that curry is the customer's first order purely based on the alphabetical order of the product names. For this reason, I maintain my solution of using `DENSE_RANK()` and consider both curry and sushi as Customer A's first order.

***