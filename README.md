# SQL Project: Data Analysis for Dominos - A Food Company

## Overview

This project demonstrates my SQL problem-solving skills through the analysis of data for Dominos pizza sales, a popular food company. The project involves setting up the database, importing data, and solving a variety of business problems using complex SQL queries.

## Project Structure
1. Database Setup: Creation of the 'pizza_sales' database and the required tables.
2. Data Import: Inserting sample data into the tables.
3. Data Cleaning: Handling null values and ensuring data integrity.
4. Business Problems: Solving 12 specific business problems using SQL queries.

## Database Setup
```sql
CREATE DATABASE DOMINOS;
```
### 1. Dropping Existing Tables
```sql
DROP TABLE IF EXISTS orders;
DROP TABLE IF EXISTS Orders_details;
DROP TABLE IF EXISTS pizza_types;
DROP TABLE IF EXISTS pizzas;
```

### 2. Creating Tables
CREATE TABLE orders(
order_id int not null,
order_date date not null,
order_time time not null,
primary key(order_id) );

CREATE TABLE orders_details(
order_details_id int not null,
order_id int not null,
pizza_id text not null,
quantity int not null,
primary key(order_details_id) );

## Data Import

## Data Cleaning and Handling Null Values

Before performing analysis, I ensured that the data was clean and free from null values where necessary. For instance:

## Business Problems Solved
### 1.Retrieve the total number of orders placed. 
```sql
SELECT 
    COUNT(order_id) AS total_orders
FROM
    orders;
```
### 2.Calculate the total revenue generated from pizza sales
```sql
SELECT 
    ROUND(SUM(p.price * o.quantity), 2) AS total_revenue
FROM
    orders_details AS o
        JOIN
    pizzas AS p ON o.pizza_id = p.pizza_id;
```
### 3. Identify the highest-priced pizza.
```sql
SELECT 
    p.name, pp.price
FROM
    pizza_types AS p
        JOIN
    pizzas AS pp ON p.pizza_type_id = pp.pizza_type_id
ORDER BY pp.price DESC
LIMIT 1;
```
### 4. List the top 5 most ordered pizza types along with their quantities.
```sql
SELECT 
    pp.name, SUM(o.quantity) AS quantities
FROM
    pizza_types AS pp
        JOIN
    pizzas AS p ON pp.pizza_type_id = p.pizza_type_id
        JOIN
    orders_details AS o ON o.pizza_id = p.pizza_id
GROUP BY pp.name
ORDER BY quantities DESC
LIMIT 5;
```
### 5. Join the necessary tables to find the total quantity of each pizza category ordered.
```sql
SELECT 
    p.category, SUM(o.quantity) AS total_quantity
FROM
    pizza_types AS p
        JOIN
    pizzas AS a ON p.pizza_type_id = a.pizza_type_id
        JOIN
    orders_details AS o ON o.pizza_id = a.pizza_id
GROUP BY p.category
ORDER BY total_quantity DESC;
```
### 6. Determine the distribution of orders by hour of the day.
```sql
SELECT 
    HOUR(order_time), COUNT(order_id) AS orders
FROM
    orders
group by HOUR(order_time)
ORDER BY orders DESC;
```
### 7. Join relevant tables to find the category-wise distribution of pizzas.
```sql
SELECT 
    category, COUNT(name)
FROM
    pizza_types
GROUP BY category;
```
### 8. Group the orders by date and calculate the average number of pizzas ordered per day.
```sql
SELECT 
    ROUND(AVG(total_quantity))
FROM
    (SELECT 
        o.order_date, SUM(d.quantity) AS total_quantity
    FROM
        orders AS o
    JOIN orders_details AS d ON o.order_id = d.order_id
    GROUP BY o.order_date) AS avg_number
```
### 9. Determine the top 3 most ordered pizza types based on revenue.
```sql
SELECT 
    t.name, SUM(o.quantity * p.price) AS total_price
FROM
    pizzas AS p
        JOIN
    orders_details AS o ON p.pizza_id = o.pizza_id
        JOIN
    pizza_types AS t ON p.pizza_type_id = t.pizza_type_id
GROUP BY t.name
ORDER BY total_price desc
limit 3;
```
### 10. Calculate the percentage contribution of each pizza type to total revenue.
```sql
SELECT 
    t.category,
    ROUND(SUM(o.quantity * p.price) / (SELECT 
                    ROUND(SUM(o.quantity * p.price), 2) AS total_sales
                FROM
                    pizzas AS p
                        JOIN
                    orders_details AS o ON p.pizza_id = o.pizza_id) * 100,
            2) AS revenue
FROM
    orders_details AS o
        JOIN
    pizzas AS p ON o.pizza_id = p.pizza_id
        JOIN
    pizza_types AS t ON p.pizza_type_id = t.pizza_type_id
GROUP BY t.category
ORDER BY revenue DESC;
```
### 11. Analyze the cumulative revenue generated over time.
```sql
select order_date,
Round(sum(revenue) over(order by order_date),2) as cum_revenue
from
(select o.order_date, sum(d.quantity * p.price) as revenue
from orders as o join orders_details as d on o.order_id = d.order_id
join pizzas as p on p.pizza_id = d.pizza_id
group by o.order_date) as sales;
```
### 12. Determine the top 3 most ordered pizza types based on revenue for each pizza category.
```sql
select name, category, Round(revenue,2)
from 
(select name, category, revenue,
rank() over(partition by category order by revenue) as rn
from
(select p.name, p.category, sum(o.quantity * pp.price) as revenue
from pizza_types as p join pizzas as pp 
on p.pizza_type_id = pp.pizza_type_id
join orders_details as o 
on o.pizza_id = pp.pizza_id
group by p.name, p.category) as a) as b
where rn <= 3;

```

## Conclusion

This project highlights my ability to handle complex SQL queries and provides solutions to real-world business problems in the context of a company sales like Dominos. The approach taken here demonstrates a structured problem-solving methodology, data manipulation skills, and the ability to derive actionable insights from data.

## Notice 
All customer names and data used in this project are computer-generated using AI and random functions. They do not represent real data associated with Dominos or any other entity. This project is solely for learning and educational purposes, and any resemblance to actual persons, businesses, or events is purely coincidental.
