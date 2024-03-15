## Triggers and Events

-   **Triggers**
    -   Trigger is a block of SQL code the automatically gets executed before or after an insert, update, or delete statement.
    -   Trigger naming convention `[table_name]_[after | before]_[type of SQL statement]`

```sql
DELIMITER $$

-- NOTE: In this trigger we can modify data in any table, except the table that this trigger is for
CREATE TRIGGER payments_after_insert
	AFTER INSERT ON payments
    FOR EACH ROW -- This mean this trigger gets fired for each row that affected.

BEGIN
	UPDATE invoices i
    SET payment_total = payment_total + NEW.amount  -- NEW | OLD: return the new recored that was inserted
	WHERE invoice_id = NEW.invoice_id;
END$$

DELIMITER ;
```

```sql
DELIMITER $$

CREATE TRIGGER payments_after_delete
	AFTER DELETE ON payments
    FOR EACH ROW

BEGIN

	UPDATE invoices i
    SET payment_total = payment_total - OLD.amount
	WHERE invoice_id = OLD.invoice_id;

END$$

DELIMITER ;
```

-   **Viewing Triggers**

```sql
SHOW TRIGGERS LIKE 'payments%'
```

-   **Dropping Triggers**

```SQL
DROP TRIGGER IF EXISTS payments_after_insert;
```

-   **Using Triggers for Auditing**
    -   Another common use cases for triggers is logging changes to the data for auditing.

```SQL
DELIMITER $$

DROP TRIGGER IF EXISTS payments_after_insert;

CREATE TRIGGER payments_after_insert
	AFTER INSERT ON payments
    FOR EACH ROW

BEGIN
	UPDATE invoices i
    SET payment_total = payment_total + NEW.amount
	WHERE invoice_id = NEW.invoice_id;

    -- This will insert a new record into payments_audit table to log the new changes on payments table
    INSERT INTO payments_audit
    VALUES(NEW.client_id, NEW.date, NEW.amount, 'Insert', NOW());
END$$

DELIMITER ;
```

```SQL
DELIMITER $$

DROP TRIGGER IF EXISTS payments_after_delete;

CREATE TRIGGER payments_after_delete
	AFTER DELETE ON payments
    FOR EACH ROW

BEGIN

	UPDATE invoices i
    SET payment_total = payment_total - OLD.amount
	WHERE invoice_id = OLD.invoice_id;

    INSERT INTO payments_audit
    VALUES(OLD.client_id, OLD.date, OLD.amount, 'Delete', NOW());
END$$

DELIMITER ;
```

-   **Events**
    -   **EVENT** is a task or (block of SQL code ) that gets executed according to schedule. it executed once or on a regular basis like every day at 10 AM, or once a month.
    -   First we need to turn on MySQL event scheduler `SHOW variables LIKE 'even%';`
        -   To set it on `SET GLOBAL event_scheduler = ON;`

```sql
DELIMITER $$

-- Best practice to start the event name with the interval e.g. hourly, monthly, daily
CREATE EVENT yearly_delete_stale_audit_rows
ON SCHEDULE -- How often do we want to execute this task, once or on a regular basis
	-- AT '2019-05-01' -- This will execut it only once at this date.
    EVERY 1 YEAR STARTS '2019-01-01' ENDS '2029-01-01' -- execute on a regular basis, e.g. EVERY 1 YEAR, EVERY 1 HOUR, EVERY 2 DAY, Notice that starts and ends are optional
DO BEGIN
    DELETE FROM payments_audit
    WHERE action_date < NOW() - INTERVAL 1 YEAR;
END$$

DELIMITER ;
```

-   **Viewing, Dropping, and Altering Events**

    -   To show all events `SHOW EVENTS;`
    -   To drop an event `DROP EVENT IF EXISTS yearly_delete_stale_audit_rows;`
    -   Altering an event:
        -   Use ALTER clause to enable or disable an event `ALTER EVENT yearly_delete_stale_audit_rows ENABLE || DISABLE`
        -   Use ALTER keyword to update the event

```sql
DELIMITER $$

ALTER EVENT yearly_delete_stale_audit_rows
ON SCHEDULE
    EVERY 1 YEAR STARTS '2019-01-01' ENDS '2029-01-01'
    YEAR, EVERY 1 HOUR, EVERY 2 DAY, Notice that starts and ends are optional
DO BEGIN
    DELETE FROM payments_audit
    WHERE action_date < NOW() - INTERVAL 1 YEAR;
END$$

DELIMITER ;
```
