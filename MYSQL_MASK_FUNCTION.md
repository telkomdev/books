## MySQL Masking Function

```sql
DROP FUNCTION IF EXISTS EW_MASK;

-- EW_MASK
DELIMITER //
CREATE FUNCTION EW_MASK(plain_data VARCHAR(255))
    RETURNS VARCHAR(255) DETERMINISTIC
BEGIN

DECLARE result VARCHAR(255);

SET result = CONCAT(substr(plain_data, 1, CHAR_LENGTH(plain_data)/2), repeat('#', CHAR_LENGTH(plain_data)/2));

return result;

END //
DELIMITER ;
```

Usage

```
> SELECT EW_MASK('wuriyanto');
+-----------------------------------------------------------------------------------------------------+
| wuriy#####                                                                                          |
+-----------------------------------------------------------------------------------------------------+
```
