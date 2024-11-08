# what is Trigger?

A trigger in SQL Server Management Studio (SSMS) is a **special type of stored procedure that automatically executes in response to certain events on a table or view**. Triggers are used to enforce business rules, maintain data integrity, and perform automatic actions based on changes to the data.

# Types of Triggers

# DML Triggers (Data Manipulation Language):
**AFTER Triggers:** Execute after an INSERT, UPDATE, or DELETE operation on a table.

**INSTEAD OF Triggers:** Execute in place of an INSERT, UPDATE, or DELETE operation, allowing you to override the default behavior.

# DDL Triggers (Data Definition Language):
Execute in response to CREATE, ALTER, DROP, GRANT, DENY, REVOKE, or UPDATE STATISTICS statements. These are used to enforce rules on the structure of the database.

# Logon Triggers:
Execute in response to a LOGON event, which occurs when a user session is established. These can be used for auditing or controlling logon events.

# Example of a DML Trigger

Let’s create a simple AFTER INSERT trigger that logs any new orders into an audit table.
Tables
`orders:` Stores order details.
`order_audit:` Logs details of new orders.

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE
);

CREATE TABLE order_audit (
    audit_id INT IDENTITY PRIMARY KEY,
    order_id INT,
    customer_id INT,
    order_date DATE,
    audit_date DATETIME DEFAULT GETDATE()
);
```

# Create Trigger
```sql
CREATE TRIGGER trgAfterInsertOrder
ON orders
AFTER INSERT
AS
BEGIN
    INSERT INTO order_audit (order_id, customer_id, order_date)
    SELECT order_id, customer_id, order_date
    FROM inserted;
END;
```
In this example:

The trgAfterInsertOrder trigger is defined on the orders table.

It fires after an INSERT operation.

It inserts the new order details into the order_audit table using the inserted pseudo-table, which holds the new rows being inserted.

## When to Use Triggers
**Enforcing Business Rules:** Automatically enforce complex business rules that cannot be enforced by constraints alone.

**Auditing Changes:** Track changes to data for auditing purposes.

**Maintaining Data Integrity:** Ensure data consistency across related tables.


# Scenario 2:
## example for Enforcing Business Rules using triggers
Let’s create an example where we enforce a business rule using triggers. We’ll use a hypothetical BikeStores database. The business rule we’ll enforce is: “An order cannot be placed if the total quantity of items exceeds 100.”

```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE
);

CREATE TABLE order_items (
    order_item_id INT PRIMARY KEY,
    order_id INT,
    product_id INT,
    quantity INT
);
```
# Step 2: Create the Trigger
We’ll create an AFTER INSERT trigger on the order_items table to enforce the business rule.
```sql
CREATE TRIGGER trgCheckOrderQuantity
ON order_items
AFTER INSERT, UPDATE
AS
BEGIN
    DECLARE @OrderID INT;
    DECLARE @TotalQuantity INT;

    -- Get the order_id from the inserted or updated row
    SELECT @OrderID = order_id FROM inserted;

    -- Calculate the total quantity for the order
    SELECT @TotalQuantity = SUM(quantity)
    FROM order_items
    WHERE order_id = @OrderID;

    -- Check if the total quantity exceeds 100
    IF @TotalQuantity > 100
    BEGIN
        -- Rollback the transaction and raise an error
        ROLLBACK TRANSACTION;
        RAISERROR ('Total quantity for order %d exceeds 100.', 16, 1, @OrderID);
    END
END;

```
# Explanation
**Trigger Definition:** The trigger trgCheckOrderQuantity is defined on the order_items table and fires after an INSERT or UPDATE operation.

**Get Order ID:** The trigger retrieves the order_id from the inserted pseudo-table, which holds the new or updated rows.

**Calculate Total Quantity:** It calculates the total quantity of items for the order by summing the quantity column for the given order_id.

**Check Business Rule:** If the total quantity exceeds 100, the trigger rolls back the transaction and raises an error.

# Example Usage
## Insert Data

```sql
-- Insert an order
INSERT INTO orders (order_id, customer_id, order_date) VALUES (1, 1, '2024-09-30');

-- Insert order items
INSERT INTO order_items (order_item_id, order_id, product_id, quantity) VALUES
(1, 1, 101, 50),
(2, 1, 102, 30),
(3, 1, 103, 25); -- This will trigger the error because total quantity exceeds 100

```
When you try to insert the third order item, the trigger will fire, check the total quantity, and if it exceeds 100, it will roll back the transaction and raise an error.