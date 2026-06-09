
##  The Real-World Scenario: E-Commerce Platform

Imagine we run an online store. We need to track our clients, the products we sell, and the orders they place. Let's first set up our database blueprint so we have a clean playground to write our code.

### Database Setup

SQL

```
CREATE DATABASE IF NOT EXISTS ecommerce_db;
USE ecommerce_db;

-- 1. Customers Table
CREATE TABLE customers (
    customer_id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100),
    city VARCHAR(50)
);

-- 2. Products Table
CREATE TABLE products (
    product_id INT AUTO_INCREMENT PRIMARY KEY,
    product_name VARCHAR(100),
    price DECIMAL(10, 2),
    stock_quantity INT
);

-- 3. Orders Table
CREATE TABLE orders (
    order_id INT AUTO_INCREMENT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    total_amount DECIMAL(10, 2),
    FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
);

-- Insert Sample Data
INSERT INTO customers (name, email, city) VALUES
('Rahul Sharma', 'rahul@gmail.com', 'Mumbai'),
('Anjali Desai', 'anjali@yahoo.com', 'Bangalore'),
('Vikram Singh', 'vikram@gmail.com', 'Delhi'),
('Sneha Reddy', 'sneha@gmail.com', 'Hyderabad');

INSERT INTO products (product_name, price, stock_quantity) VALUES
('Mechanical Keyboard', 4500.00, 15),
('Wireless Gaming Mouse', 2500.00, 30),
('Glassmorphism Desk Mat', 1200.00, 0), -- Out of stock
('RGB Led Strip', 800.00, 50);

INSERT INTO orders (customer_id, order_date, total_amount) VALUES
(1, '2026-06-01', 4500.00),
(1, '2026-06-05', 2500.00),
(2, '2026-06-06', 1200.00),
(3, '2026-06-08', 800.00);
-- Note: Sneha (customer_id 4) has placed 0 orders so far.
```

## 1. MySQL JOINs

### The Concept

In a normalized database, data is split into multiple tables to avoid redundancy. **JOINs** are used to link records from two or more tables together based on a related column between them.

- **INNER JOIN:** Returns records that have matching values in _both_ tables.
    
- **LEFT JOIN (or Left Outer Join):** Returns _all_ records from the left table, and the matched records from the right table. If no match is found, `NULL` values are returned for the right table's columns.
    

### Real-World Business Need

The marketing team wants a comprehensive report of **all customers and their order details**, including customers who registered but haven't bought anything yet, so they can send targeted follow-up emails.

### The Code

SQL

```
-- Fetching order history using INNER JOIN and LEFT JOIN
-- Business Case: See who bought what, and find inactive users.

-- Query A: Get only customers who have placed orders
SELECT 
    c.customer_id, c.name, o.order_id, o.order_date, o.total_amount
FROM customers c
INNER JOIN orders o ON c.customer_id = o.customer_id;

-- Query B: Get ALL customers, including those with zero orders
SELECT 
    c.customer_id, c.name, o.order_id, o.order_date, o.total_amount
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id;
```

### Detailed Code Explanation

- `FROM customers c`: We designate `customers` as our left table and give it an alias `c` to keep the code clean.
    
- `INNER JOIN orders o ON c.customer_id = o.customer_id`: This tells MySQL to check the `orders` table (`o`), match it via the `customer_id` primary/foreign key relationship, and **only** return rows where a match exists. Rahul, Anjali, and Vikram will show up.
    
- In the `LEFT JOIN` variant, **Sneha Reddy** will also appear in the results. Because she hasn't placed an order, her `order_id`, `order_date`, and `total_amount` columns will seamlessly output as `NULL`.
    

## 2. Subqueries

### The Concept

A **Subquery** (or Inner Query) is a query nested inside another SQL query (the Outer Query). The inner query executes first and passes its results to the outer query, which uses them to filter or manipulate data.

### Real-World Business Need

The inventory manager wants to know which products are priced **above the average price** of all items in the store so they can run a premium discount campaign.

### The Code

SQL

```
-- Business Case: Find products that cost more than the store average.
SELECT 
    product_name, 
    price 
FROM products 
WHERE price > (
    SELECT AVG(price) FROM products
);
```

### Detailed Code Explanation

- **The Inner Query:** `(SELECT AVG(price) FROM products)` runs first. It calculates the mean cost of all products ($(4500 + 2500 + 1200 + 800) / 4 = 2250$).
    
- **The Outer Query:** MySQL replaces the brackets with the scalar value `2250`. The query effectively evaluates to: `WHERE price > 2250`.
    
- **Result:** It yields the _Mechanical Keyboard_ (4500) and _Wireless Gaming Mouse_ (2500).
    

## 3. Views

### The Concept

A **View** is essentially a _virtual table_. It does not store physical data itself. Instead, it stores a predefined SQL query blueprint. When you query a view, it runs the underlying query on the fly.

>  **Why use them?** They simplify complex joins for junior developers, hide sensitive data columns (like user passwords), and ensure consistent reporting metrics across applications.

### Real-World Business Need

The finance team regularly asks for a sales dashboard showing customer names alongside their total spent amount. Instead of making them write complex `JOIN` and `SUM()` syntax every time, we will build a reusable View.

### The Code

SQL

```
-- Business Case: Create a reusable Sales Summary Dashboard
CREATE OR REPLACE VIEW view_customer_sales_summary AS
SELECT 
    c.customer_id,
    c.name,
    c.email,
    COUNT(o.order_id) AS total_orders_placed,
    IFNULL(SUM(o.total_amount), 0.00) AS total_money_spent
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id
GROUP BY c.customer_id, c.name, c.email;

-- How the frontend or analyst queries it:
SELECT * FROM view_customer_sales_summary WHERE total_money_spent > 2000;
```

### Detailed Code Explanation

- `CREATE OR REPLACE VIEW view_customer_sales_summary AS`: Instructs MySQL to save this entire query layout under a virtual table name.
    
- `COUNT(o.order_id)` & `SUM(o.total_amount)`: Aggregates order data per user.
    
- `IFNULL(..., 0.00)`: Cleanliness trick! If a user has zero orders, instead of showing a blank `NULL`, it converts it to a clean `0.00`.
    
- `GROUP BY`: Mandated because we are mixing raw columns (`c.name`) with aggregate calculations.
    
- Once compiled, anyone can treat `view_customer_sales_summary` like an ordinary table using basic `SELECT` statements!
    

## 4. Stored Procedures

### The Concept

A **Stored Procedure** is a batch of SQL statements compiled and saved directly on the database server. Think of it like a **backend function** for your database. It can accept input parameters, execute conditional business logic (like `IF/ELSE`), and perform write operations like insertions or updates.

### Real-World Business Need

When a customer buys an item, multiple things must happen safely: an order record must be generated, and the product's inventory stock must decrease. We need a secure, automated routine to handle this.

### The Code

SQL

```
-- Business Case: Safely process a new purchase and update stock levels
DELIMITER $$

CREATE PROCEDURE sp_process_customer_purchase(
    IN p_customer_id INT,
    IN p_product_id INT,
    IN p_order_amount DECIMAL(10,2)
)
BEGIN
    DECLARE current_stock INT;

    -- 1. Check current inventory stock levels
    SELECT stock_quantity INTO current_stock 
    FROM products 
    WHERE product_id = p_product_id;

    -- 2. Business Logic Check
    IF current_stock > 0 THEN
        -- Insert into orders table
        INSERT INTO orders (customer_id, order_date, total_amount)
        VALUES (p_customer_id, CURDATE(), p_order_amount);

        -- Deduct inventory by 1 unit
        UPDATE products 
        SET stock_quantity = stock_quantity - 1
        WHERE product_id = p_product_id;
        
        SELECT 'SUCCESS: Order Processed and Stock Updated!' AS status_message;
    ELSE
        SELECT 'FAILED: Sorry, this product is currently out of stock.' AS status_message;
    END IF;
    
END$$

DELIMITER ;
```

#### How to execute the procedure:

SQL

```
-- Scenario A: Rahul tries to buy the out-of-stock 'Glassmorphism Desk Mat' (Product ID 3)
CALL sp_process_customer_purchase(1, 3, 1200.00);

-- Scenario B: Rahul successfully buys a 'Mechanical Keyboard' (Product ID 1)
CALL sp_process_customer_purchase(1, 1, 4500.00);
```

### Detailed Code Explanation

- `DELIMITER $$`: Standard SQL commands end with a semicolon `;`. Because a procedure has multiple statements inside it, we temporarily switch the end-of-statement symbol to `$$` so MySQL doesn't cut the compilation short.
    
- `IN p_customer_id INT...`: Defines input parameters passing dynamic parameters from our backend application (Node.js/MERN stack controller, for example).
    
- `DECLARE current_stock INT;`: Creates a local variable to hold our inventory status on the fly.
    
- `SELECT stock_quantity INTO current_stock`: Reads data directly from our database and assigns it to our variable.
    
- `IF... ELSE... END IF`: Controls application workflow on the database layer. If stock is available, it commits both the `INSERT` and the `UPDATE` securely. If stock is zero, it completely bypasses the purchase and returns a warning message.
    

###  Summary Cheat Sheet

|**Concept**|**What it is**|**When to use it**|
|---|---|---|
|**JOIN**|Linking disparate tables together on shared keys.|Standard data retrieval across tables.|
|**Subquery**|A query inside another query.|Dynamic calculations or sub-filtering criteria.|
|**View**|A bookmarked, virtual query table.|Simplifying complex code structures and securing data.|
|**Stored Procedure**|A compiled database script/function.|Executing complex multi-step data changes safely.|
