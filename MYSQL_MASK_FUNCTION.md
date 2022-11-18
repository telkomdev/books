## MySQL Masking Function

```sql
DROP FUNCTION IF EXISTS EW_MASK;

-- EW_MASK
DELIMITER //
CREATE FUNCTION EW_MASK(plain_data VARCHAR(255))
    RETURNS VARCHAR(255) DETERMINISTIC
BEGIN

DECLARE result VARCHAR(255);

SET result = CONCAT(substr(plain_data, 1, CHAR_LENGTH(plain_data)-FLOOR(CHAR_LENGTH(plain_data)*0.75)), repeat('*', FLOOR(CHAR_LENGTH(plain_data)*0.75)));

return result;

END //
DELIMITER ;
```

### Usage
This function will convert 75% of the content into the specified mask character. EG `*`
```
mysql> select EW_MASK('wuriyanto');
+----------------------+
| EW_MASK('wuriyanto') |
+----------------------+
| wur******            |
+----------------------+
```
