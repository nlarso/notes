# SQL NOTES


#### Show Query Cost

```
SHOW STATUS LIKE 'Last_Query_Cost';
```

#### Viewing Database Index Size
> I've used this a little for checking how much memory is used when I add an index.
> I start by creating this function:
```
DELIMITER $
DROP FUNCTION IF EXISTS byteResize$
CREATE FUNCTION byteResize(bytes FLOAT(9))
RETURNS VARCHAR(50)

BEGIN
        # Unit list
        DECLARE unit INTEGER UNSIGNED DEFAULT 1;

        # Resizing
        WHILE bytes > 1024 DO
            SET bytes = bytes / 1024;
            SET unit = unit + 1;
        END WHILE;

RETURN CONCAT(ROUND(bytes, 2), ' ', ELT(unit, '', 'K', 'M', 'G', 'T'), 'b');

END$
DELIMITER ;
```
> Then I run this query to retrieve the information I want:
```
SELECT TABLE_NAME AS "Table",
       TABLE_ROWS AS "Rows",
       byteResize(DATA_LENGTH) AS "Data Size",
       byteResize(INDEX_LENGTH) AS "Index Size",
       byteResize(DATA_LENGTH+ INDEX_LENGTH) AS "Total Size",
       TRIM(TRAILING ', ' FROM CONCAT_WS(', ', ENGINE, TABLE_COLLATION, CREATE_OPTIONS)) AS "Type"
FROM information_schema.TABLES
WHERE information_schema.TABLES.table_schema = 'ess_dev';
```
