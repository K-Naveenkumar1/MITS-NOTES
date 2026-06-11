
### **Practice Scenario Setup**

For these practice queries, assume a standard e-commerce database schema consisting of the following tables:

- `Customers` (`customer_id`, `first_name`, `last_name`, `email`, `join_date`, `city`)
    
- `Products` (`product_id`, `product_name`, `category`, `price`, `stock_quantity`)
    
- `Orders` (`order_id`, `customer_id`, `order_date`, `total_amount`, `status`)
    
- `Order_Items` (`item_id`, `order_id`, `product_id`, `quantity`, `unit_price`)
    

### **Category 1: DDL, DML & Constraints (Concepts & Commands)**

1. **Write a query to create the `Customers` table** ensuring that `customer_id` is the Primary Key, `email` is unique and cannot be null, and `join_date` defaults to the current date.
    
2. **Write a DDL command** to add a new column named `phone_number` (VARCHAR) to the existing `Customers` table.
    
3. **Insert a new record** into the `Products` table with the values: Laptop, Electronics, 1200.50, and 15.
    
4. **Write an extension query** to increase the price of all products in the 'Electronics' category by 10%.
    
5. **Delete all orders** from the `Orders` table where the status is 'Cancelled'.
    

### **Category 2: Basic Querying (SELECT, WHERE, LIKE, ORDER BY, BETWEEN)**

6. **Write a query to select** the `first_name`, `last_name`, and `city` of all customers who live in either 'New York' or 'Los Angeles'.
    
7. **Find all products** whose prices are between $50 and $200, sorting the results from highest price to lowest price.
    
8. **Retrieve all customers** whose email addresses end with '@gmail.com'.
    
9. **List all unique product categories** available in the `Products` table, avoiding duplicates.
    
10. **Retrieve the top 5 most expensive products** from the database.
    

### **Category 3: Intermediate Querying (Aggregate Functions & Grouping)**

11. **Write a query to count** the total number of orders placed by each `customer_id` from the `Orders` table.
    
12. **Find the total revenue** generated from completed orders (i.e., the sum of `total_amount` where status is 'Shipped' or 'Delivered').
    
13. **Display the product categories** that have an average product price greater than $100.
    
14. **Find the maximum and minimum price** of a product within each category.
    

### **Category 4: Advanced Querying (Joins, Subqueries & Views)**

15. **Write an INNER JOIN query** to display the customer's `first_name`, `last_name`, `order_id`, and `order_date` for all orders placed.
    
16. **Write a LEFT JOIN query** to display all customers (`first_name`, `last_name`) and their corresponding order IDs. Include customers who have never placed an order.
    
17. **Using a Multi-table Join**, retrieve a list of product names purchased by a customer with `customer_id = 101`. (Hint: You will need to join `Orders`, `Order_Items`, and `Products`).
    
18. **Write a Subquery** to find the details of all products that have a price higher than the overall average price of all products.
    
19. **Write a Subquery using the `IN` operator** to find all customers who have placed an order in the month of May 2026.
    
20. **Create a View named `OrderSummary`** that displays the `order_id`, customer's full name (concatenated), and the `total_amount` for all active transactions.
  