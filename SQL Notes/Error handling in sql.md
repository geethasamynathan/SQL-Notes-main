```sql
-- Create a procedure to handle errors
CREATE PROCEDURE usp_InsertProduct
    @product_name NVARCHAR(50),
    @brand_id INT,
    @category_id INT,
    @model_year INT,
    @list_price DECIMAL(10, 2)
AS
BEGIN
    BEGIN TRY
        -- Attempt to insert a new product
        INSERT INTO production.products (product_name, brand_id, category_id, model_year, list_price)
        VALUES (@product_name, @brand_id, @category_id, @model_year, @list_price);

        -- If successful, print a message
        PRINT 'Product inserted successfully.';
    END TRY
    BEGIN CATCH
        -- If an error occurs, handle it here
        DECLARE @ErrorMessage NVARCHAR(4000);
        DECLARE @ErrorSeverity INT;
        DECLARE @ErrorState INT;

        -- Retrieve error information
        SELECT 
            @ErrorMessage = ERROR_MESSAGE(),
            @ErrorSeverity = ERROR_SEVERITY(),
            @ErrorState = ERROR_STATE();

        -- Print the error message
        PRINT 'Error Occurred: ' + @ErrorMessage;

        -- Optionally, you could raise the error again
        -- RAISERROR (@ErrorMessage, @ErrorSeverity, @ErrorState);
    END CATCH
END
GO

-- Example usage
EXEC usp_InsertProduct 'New Bike', 1, 2, 2023, 299.99;
```

We create a stored procedure usp_InsertProduct to insert a new product into the products table.

The BEGIN TRY block contains the code that might generate an error—inserting a new product.

The BEGIN CATCH block handles any errors that occur in the TRY block.

Inside the CATCH block, we retrieve error information using the ERROR_MESSAGE(), ERROR_SEVERITY(), and ERROR_STATE() functions.

We print the error message. Optionally, we could use the RAISERROR statement to re-raise the error.