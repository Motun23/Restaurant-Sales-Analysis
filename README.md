
# **Restaurant Sales Report**

## Project Overview

A quarter's worth of orders from a fictitious restaurant serving international cuisine, including the date and time of each order, the items ordered, and additional details on the type, name and price of the items. 

![Dashboard](https://github.com/Motun23/Restaurant-Sales-Analysis/assets/146113418/96ba0e69-ae31-47f4-93d3-224c62d09432)

## Data Source

The dataset used for this analysis is the Restaurant data file containing information about the sales made by a restaurant in the US.

## Tools

- SQL
- Power BI

## Data Cleaning / Preparation

1. Data loading and Inspection
2. Handling missing values
3. Data cleaning and formmating

## Business Questions

1. What were the least and most ordered items? What categories were they in?

2. What do the highest spend orders look like? Which items did they buy and how much did they spend?

3. Were there certain times that had more or less orders?

4. Which cuisines should we focus on developing more menu items for based on the data?

## Data Analysis using SQL

1. View the menu_items table and write a query to find the number of items on the menu

``` Select COUNT(DISTINCT menu_item_id) as Number_of_Menu_Items from menu_items;```

![Question 1](https://github.com/Motun23/Restaurant-Sales-Analysis/assets/146113418/f0daff8a-e81e-4375-ba8d-b00d875a1ac7)

2.  What are the least and most expensive items on the menu

The least expensive item
```
Select TOP 1 item_name, price
FROM menu_items
ORDER BY price ASC;
```
![Question 2a](https://github.com/Motun23/Restaurant-Sales-Analysis/assets/146113418/6de3bbd3-6e70-4fc0-abca-513d2cc735ce)

The most expensive item

```
SELECT TOP 1 item_name, price
FROM menu_items
ORDER BY price DESC;
```
![Question 2b](https://github.com/Motun23/Restaurant-Sales-Analysis/assets/146113418/64e66a4a-b3e7-4579-be6d-2fae4b8e56c6)

3. How many Italian dishes are on the menu? What are the least and most expensive Italian dishes on the menu?

```
SELECT COUNT(*) AS Italian_Dish_Count
FROM menu_items
WHERE category = 'Italian';
```
![Question 3a](https://github.com/Motun23/Restaurant-Sales-Analysis/assets/146113418/0c40da1c-2268-4e4c-8c3a-0cc1e15cbb14)

Least Expensive Italian Dish

```
SELECT TOP 1 item_name, price
FROM menu_items
WHERE category = 'Italian'
ORDER BY price ASC;
```
![Question 3b](https://github.com/Motun23/Restaurant-Sales-Analysis/assets/146113418/722e7c37-e960-4f28-a2e6-c63cc76ab421)

Most Expensive Italian Dish

```
SELECT TOP 1 item_name, price
FROM menu_items
WHERE category = 'Italian'
ORDER BY price DESC;
```
![Question 3c](https://github.com/Motun23/Restaurant-Sales-Analysis/assets/146113418/c6066d5c-26c7-4136-92af-d70202c5cf39)

4. How many dishes are in each category? What is the average dish price within each category?

```
SELECT
    category,
    COUNT(*) AS dish_count,
    AVG(price) AS average_price
FROM
    menu_items
GROUP BY
    category;
```
![Question 4](https://github.com/Motun23/Restaurant-Sales-Analysis/assets/146113418/f16637f4-a446-439f-b264-83084fa5d64c)

5. View the order_details table. What is the date range of the table?

```
SELECT
    MIN(order_date) AS Earliest_Date,
    MAX(order_date) AS Latest_Date
FROM
    order_details;
```
![Question 5](https://github.com/Motun23/Restaurant-Sales-Analysis/assets/146113418/413c72b7-2dfa-4650-97da-3907bfd7957a)



6. How many orders were made within this date range? How many items were ordered within this date range?

``` 
SELECT COUNT(DISTINCT order_id) AS Total_Orders
FROM order_details
WHERE order_date BETWEEN '2023-01-01' AND '2023-03-31';
```
![Question 6a](https://github.com/Motun23/Restaurant-Sales-Analysis/assets/146113418/32ab7131-2da3-43df-b3cf-1ba2c185ba5c)

How many items were ordered within this date range?

```
SELECT COUNT(item_id) AS Total_Items
FROM order_details
WHERE order_date BETWEEN '2023-01-01' AND '2023-03-31';
```
![Question 6b](https://github.com/Motun23/Restaurant-Sales-Analysis/assets/146113418/9af126ba-ba4e-4b6f-afcc-868011a79636)

7. Which orders had the most number of items?

```
SELECT menu_item_id, COUNT(item_id)
FROM menu_items m
JOIN order_details o
ON m.menu_item_id = o.item_id
GROUP BY menu_item_id;

```
![Question 7](https://github.com/Motun23/Restaurant-Sales-Analysis/assets/146113418/81bba599-fc7c-4df1-812e-c0b7d489cebc)

8. How many orders had more than 12 items?

```
WITH OrderItemCounts AS (
    SELECT
        order_id,
        COUNT(item_id) AS item_count
    FROM
        order_details
    GROUP BY
        order_id)
SELECT
    COUNT(*) AS orders_with_more_than_12_items
FROM
    OrderItemCounts
WHERE
    item_count > 12;
 
```
![Question 8](https://github.com/Motun23/Restaurant-Sales-Analysis/assets/146113418/8349d51d-e25f-4afd-92b6-7432ac2cd363)

9. Combine the menu_items and order_details tables into a single table
```
SELECT
    o.order_id,
    o.order_date,
    o.item_id,
    m.item_name,
    m.price,
    m.category
FROM
    order_details o
INNER JOIN
    menu_items m
ON
    o.item_id = m.menu_item_id;
```
![Question 9](https://github.com/Motun23/Restaurant-Sales-Analysis/assets/146113418/4b8e3a13-f4a2-48ce-a40b-80fa690132a4)

10. What were the least and most ordered items? What categories were they in?

```
WITH ItemOrderCounts AS (
    SELECT
        item_id,
        COUNT(*) AS order_count
    FROM
        order_details
    GROUP BY
        item_id
)
SELECT
    m.menu_item_id,
    m.item_name,
    m.category,
    i.order_count
FROM
    ItemOrderCounts i
INNER JOIN
    menu_items m
ON
    i.item_id = m.menu_item_id
WHERE
    i.order_count = (SELECT MIN(order_count) FROM ItemOrderCounts)
    OR i.order_count = (SELECT MAX(order_count) FROM ItemOrderCounts);
```
![Question 10](https://github.com/Motun23/Restaurant-Sales-Analysis/assets/146113418/a8b7754d-9c97-45b2-b339-d00e2206ce75)

11.What were the top 5 orders that spent the most money?

```
WITH OrderTotals AS (
    SELECT
        o.order_id,
        SUM(m.price) AS Total_Spent
    FROM
        order_details o
    INNER JOIN
        menu_items m
    ON
        o.item_id = m.menu_item_id
    GROUP BY
        o.order_id)
SELECT TOP 5
    order_id,
    Total_Spent
FROM
    OrderTotals
ORDER BY
    Total_Spent DESC;
```
![Question 11](https://github.com/Motun23/Restaurant-Sales-Analysis/assets/146113418/13abe1b6-8117-4c2f-8534-ca5a6acebcd3)

12. View the details of the highest spend order. Which specific items were purchased?

```
WITH OrderTotals AS (
    SELECT
        o.order_id,
        SUM(m.price) AS total_spent
    FROM
        order_details o
    INNER JOIN
        menu_items m
    ON
        o.item_id = m.menu_item_id
    GROUP BY
        o.order_id
),
HighestSpendOrder AS (
    SELECT TOP 1
        order_id
    FROM
        OrderTotals
    ORDER BY
        total_spent DESC
)
SELECT
    o.order_id,
    o.order_date,
    m.menu_item_id,
    m.item_name,
    m.price,
    m.category
FROM
    order_details o
INNER JOIN
    menu_items m
ON
    o.item_id = m.menu_item_id
WHERE
    o.order_id = (SELECT order_id FROM HighestSpendOrder);
```

![Question 12](https://github.com/Motun23/Restaurant-Sales-Analysis/assets/146113418/bf3faabd-7753-4a56-b684-98e3bc8bf582)

13. View the details of the top 5 highest spend orders

```
WITH OrderTotals AS (
    SELECT
        o.order_id,
        SUM(m.price) AS total_spent
    FROM
        order_details o
    INNER JOIN
        menu_items m
    ON
        o.item_id = m.menu_item_id
    GROUP BY
        o.order_id
),
Top5Orders AS (
    SELECT TOP 5
        order_id,
        total_spent
    FROM
        OrderTotals
    ORDER BY
        total_spent DESC
)
SELECT
    o.order_id,
    o.order_date,
    m.menu_item_id,
    m.item_name,
    m.price,
    m.category
FROM
    order_details o
INNER JOIN
    menu_items m
ON
    o.item_id = m.menu_item_id
WHERE
    o.order_id IN (SELECT order_id FROM Top5Orders)
ORDER BY
    o.order_id, m.item_name;
```
![Question 13](https://github.com/Motun23/Restaurant-Sales-Analysis/assets/146113418/610ff4d7-f3b7-421e-b4f2-716644d848a5)

