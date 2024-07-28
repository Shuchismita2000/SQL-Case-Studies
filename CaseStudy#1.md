# 🍜 Case Study #1: Danny's Diner 
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
- 1. `SELECT sales.customer_id, SUM(menu.price) AS total_sales`
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

