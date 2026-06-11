
### **Category 1: DDL, DML & Constraints**

#### **1. Create the `Customers` table**

SQL

```
CREATE TABLE Customers (
    customer_id INT AUTO_INCREMENT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    join_date DATE DEFAULT (CURRENT_DATE),
    city VARCHAR(50)
);
```

#### **2. Add a `phone_number` column**

SQL

```
ALTER TABLE Customers 
ADD phone_number VARCHAR(15);
```

#### **3. Insert a new record into `Products`**

SQL

```
INSERT INTO Products (product_name, category, price, stock_quantity)
VALUES ('Laptop', 'Electronics', 1200.50, 15);
```

#### **4. Increase price of 'Electronics' by 10%**

SQL

```
UPDATE Products
SET price = price * 1.10
WHERE category = 'Electronics';
```

#### **5. Delete cancelled orders**

SQL

```
DELETE FROM Orders
WHERE status = 'Cancelled';
```

### **Category 2: Basic Querying**

#### **6. Filter by cities**

SQL

```
SELECT first_name, last_name, city
FROM Customers
WHERE city IN ('New York', 'Los Angeles');
-- Note: WHERE city = 'New York' OR city = 'Los Angeles' is also correct.
```

#### **7. Price range sorting**

SQL

```
SELECT * FROM Products
WHERE price BETWEEN 50 AND 200
ORDER BY price DESC;
```

#### **8. Filter matching Gmail domains**

SQL

```
SELECT * FROM Customers
WHERE email LIKE '%@gmail.com';
```

#### **9. Distinct categories**

SQL

```
SELECT DISTINCT category 
FROM Products;
```

#### **10. Top 5 most expensive products**

SQL

```
SELECT * FROM Products
ORDER BY price DESC
LIMIT 5;
```

### **Category 3: Intermediate Querying (Aggregation)**

#### **11. Count orders per customer**

SQL

```
SELECT customer_id, COUNT(order_id) AS total_orders
FROM Orders
GROUP BY customer_id;
```

#### **12. Total revenue from completed orders**

SQL

```
SELECT SUM(total_amount) AS total_revenue
FROM Orders
WHERE status IN ('Shipped', 'Delivered');
```

#### **13. Categories with average price > $100**

SQL

```
SELECT category, AVG(price) AS average_price
FROM Products
GROUP BY category
HAVING AVG(price) > 100; 
-- Remember: WHERE filters rows before grouping; HAVING filters groups after aggregation!
```

#### **14. Min and Max price per category**

SQL

```
SELECT category, MIN(price) AS minimum_price, MAX(price) AS maximum_price
FROM Products
GROUP BY category;
```

### **Category 4: Advanced Querying (Joins, Subqueries & Views)**

#### **15. Inner Join for customer orders**

SQL

```
SELECT c.first_name, c.last_name, o.order_id, o.order_date
FROM Customers c
INNER JOIN Orders o ON c.customer_id = o.customer_id;
```

#### **16. Left Join to include non-ordering customers**

SQL

```
SELECT c.first_name, c.last_name, o.order_id
FROM Customers c
LEFT JOIN Orders o ON c.customer_id = o.customer_id;
```

#### **17. Multi-table Join for specific product names**

SQL

```
SELECT p.product_name
FROM Orders o
INNER JOIN Order_Items oi ON o.order_id = oi.order_id
INNER JOIN Products p ON oi.product_id = p.product_id
WHERE o.customer_id = 101;
```

#### **18. Subquery for products above average price**

SQL

```
SELECT * FROM Products
WHERE price > (SELECT AVG(price) FROM Products);
```

#### **19. Subquery with `IN` for May 2026 orders**

SQL

```
SELECT * FROM Customers
WHERE customer_id IN (
    SELECT customer_id 
    FROM Orders 
    WHERE order_date BETWEEN '2026-05-01' AND '2026-05-31'
);
```

#### **20. Create a View with concatenated names**

SQL

```
CREATE VIEW OrderSummary AS
SELECT 
    o.order_id, 
    CONCAT(c.first_name, ' ', c.last_name) AS customer_name, 
    o.total_amount
FROM Orders o
INNER JOIN Customers c ON o.customer_id = c.customer_id;
```

### **Final Checklist Before the Test:**

1. Do you know the exact execution order of an SQL query? (`FROM` $\rightarrow$ `JOIN` $\rightarrow$ `WHERE` $\rightarrow$ `GROUP BY` $\rightarrow$ `HAVING` $\rightarrow$ `SELECT` $\rightarrow$ `ORDER BY` $\rightarrow$ `LIMIT`).
    
2. Do you remember when to use `WHERE` versus `HAVING`?
    
3. Are you clear on the difference between `DELETE` (DML, can be rolled back) and `TRUNCATE` (DDL, fast, resets auto-increment)?