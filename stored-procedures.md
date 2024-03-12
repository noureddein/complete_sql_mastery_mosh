## Stored Procedures

-   **What is a Stored Procedure?**

    -   A stored procedure is a prepared SQL code that you can save, so the code can be reused over and over again.
    -   So if you have an SQL query that you write over and over again, save it as a stored procedure, and then just call it to execute it.
    -   You can also pass parameters to a stored procedure, so that the stored procedure can act based on the parameter value(s) that is passed.

-   **Creating a Stored Procedure?**
    -   We need to change the delimiter to execute the procedure as one block
    -   We can call the stored procedure with the _CALL_ statement `CALL get_clients();`

```sql
DELIMITER $$
CREATE PROCEDURE get_clients()
BEGIN
-- What we have between begin and end is called the body of stored procedure
SELECT * FROM sql_invoicing.clients;
END$$
DELIMITER ;
```

```sql

DELIMITER $$
CREATE PROCEDURE get_invoices_with_balance()
BEGIN
	SELECT * FROM
        invoices_with_balance -- from views
    WHERE
        balance > 0
    ORDER BY balance DESC;
END$$
DELIMITER ;
```

-   **Creating a Stored Procedure with MySQL Workbench?**

    -   From the schema side view right click on Stored Procedure select Create Stored Procedure

-   **Dropping a Stored Procedure**

```sql
DROP PROCEDURE IF EXISTS get_clients;

DELIMITER &&

CREATE PROCEDURE get_clients()
BEGIN
    SELECT * FROM clients;
END&&

DELIMITER ;
```

-   **Parameters**
    -   Parameters required specifying the type.

```sql
CREATE PROCEDURE get_invoices_by_client_id(id INT)
BEGIN
    SELECT * FROM 	invoices i where i.client_id = id;
END
```

-   **Parameters with Default values**

```sql
CREATE PROCEDURE `get_clients_by_state`(state VARCHAR(2))
BEGIN
		SELECT * FROM clients c WHERE c.state = IFNULL(state, c.state);
END
```

```sql
CREATE PROCEDURE `get_payments`(
	client_id INT,
	payment_method_id TINYINT
)
BEGIN
	SELECT * FROM payments p
		WHERE p.client_id = IFNULL(client_id, p.client_id)
        AND p.payment_method = IFNULL(payment_method_id, p.payment_method);
END
```

-   **Parameters Validation**
    -   With Stored Procedure we can Insert, Update, and Delete data.
    -   **SIGNAL**: is the way to “return” an error. SIGNAL provides error information to a handler, to an outer portion of the application, or to the client. Also, it provides control over the error's characteristics (error number, SQLSTATE value, message).
    -   **SET MESSAGE_TEXT**: is an optional to set a clear message of the error.
    -   Error code must be a string not a number.

```sql
CREATE PROCEDURE update_payment(
	invoice_id INT,
    payment_amount DECIMAL(9, 2),
    payment_date DATE
)
BEGIN
	IF payment_amount <= 0 THEN
		SIGNAL SQLSTATE '22003' SET MESSAGE_TEXT = 'Invalid payment amount.';
	END IF ;
	UPDATE invoices i
	SET i.payment_total = payment_amount, i.payment_date = payment_date
	WHERE i.invoice_id = invoice_id;
END
```

-   **Output Parameters**
    -   invoices_count, invoices_total called user defined variables, a variable is basically an object that we can to store a single value in memory. these values can be used outside the procedure.

```sql
CREATE PROCEDURE get_unpaid_invoices_for_client(
	client_id INT,
    OUT invoices_count INT, -- OUT: Marks the parameters as output parameters, so we can use these parameters out of this procedure
    OUT invoices_total DECIMAL(9, 2)
)
BEGIN

	SELECT COUNT(*), SUM(invoice_total)
    INTO invoices_count, invoices_total
    FROM invoices i
    WHERE i.client_id = client_id AND payment_total = 0;

END
```

-   **Variables**
    -   Variables stay in memory during the user session, when client disconnect from database, these values freed up.
    -   Types of variables:
        -   User or session variables.
        -   Local variables : The variables that defined in stored procedure or a function

```SQL
CREATE PROCEDURE get_risk_factor()
BEGIN

	DECLARE risk_factor decimal(9, 2) default 0; -- Local variable
    declare invoices_total decimal(9,2) ; -- Local variable
    declare invoice_count INT; -- Local variable

    select COUNT(*), SUM(invoice_total)
    INTO invoice_count, invoices_total -- To set the values of COUNT and SUM into invoice_total, invoice_count
    from invoices;

    SET risk_factor =  invoices_total / invoice_count * 5;

	SELECT risk_factor;
END
```

-   **Functions**
    -   Functions unlike stored procedure, it returns only a single value
    -   Each function must have at least one attribute
        -   **DETERMINISTIC**: mean same input will return the same output, like calculating the tax of total invoice, same total return same tax amount
        -   **READS SQL DATA**: Which mean the function will have a select statement to read some data from database.
        -   **MODIFIES SQL DATA**: Which mean the function have a update, delete, or insert statement.

```SQL
CREATE FUNCTION get_risk_factor_for_client( client_id INT)
RETURNS int
READS SQL DATA
BEGIN
	DECLARE risk_factor decimal(9, 2) default 0;
    declare invoices_total decimal(9,2) ;
    declare invoice_count INT;

    select COUNT(*), SUM(invoice_total)
    INTO invoice_count, invoices_total
    from invoices i
    WHERE i.client_id = client_id;

    SET risk_factor =  invoices_total / invoice_count * 5;

	RETURN IFNULL(risk_factor, 0);
END
```

```sql
-- To call the function
SELECT
	client_id,
    name,
    get_risk_factor_for_client(client_id)
FROM clients;

-- To drop function
DROP FUNCTION IF EXISTS get_risk_factor_for_client;
```
