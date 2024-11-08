A transaction in SQL Server Management Studio (SSMS) is a sequence of one or more SQL statements that are executed as a single unit of work. Transactions ensure that either all operations within the transaction are completed successfully, or none of them are, maintaining data integrity and consistency.

# Key Concepts of Transactions
**Atomicity:** Ensures that all operations within a transaction are completed; if any operation fails, the entire transaction is rolled back.

**Consistency:** Ensures that a transaction brings the database from one valid state to another, maintaining database invariants.

**Isolation:** Ensures that the operations within a transaction are isolated from other transactions until they are completed.

**Durability:** Ensures that once a transaction is committed, the changes are permanent, even in the event of a system failure.

# Types of Transactions
**Autocommit Transactions:** Each individual SQL statement is treated as a transaction and is automatically committed if successful.

**Explicit Transactions:** Transactions that are explicitly started with the BEGIN TRANSACTION statement and ended with a COMMIT or ROLLBACK statement.

**Implicit Transactions:** A new transaction is automatically started after the previous one is completed, but each transaction must be explicitly committed or rolled back.

**Batch-Scoped Transactions:** Transactions that are scoped to a batch of SQL statements, typically used with Multiple Active Result Sets (MARS).

# Example of an Explicit Transaction
Here’s an example of how to use an explicit transaction in SQL Server:

```sql
BEGIN TRANSACTION;

-- Insert a new order
INSERT INTO orders (order_id, customer_id, order_date) VALUES (1, 1, '2024-09-30');

-- Insert order items
INSERT INTO order_items (order_item_id, order_id, product_id, quantity) VALUES
(1, 1, 101, 2),
(2, 1, 102, 1);

-- Check for errors and commit or rollback
IF @@ERROR = 0
BEGIN
    COMMIT TRANSACTION;
    PRINT 'Transaction committed successfully.';
END
ELSE
BEGIN
    ROLLBACK TRANSACTION;
    PRINT 'Transaction rolled back due to an error.';
END;

```

In this example:

The transaction starts with BEGIN TRANSACTION.

Multiple SQL statements are executed within the transaction.

If all statements are successful (@@ERROR = 0), the transaction is committed with COMMIT TRANSACTION.

If any statement fails, the transaction is rolled back with ROLLBACK TRANSACTION.