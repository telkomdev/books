## MySQL Masking Function

```sql
DROP FUNCTION IF EXISTS EW_MASK;

-- EW_MASK
DELIMITER //
CREATE FUNCTION EW_MASK(plain_data VARCHAR(255))
    RETURNS VARCHAR(255) DETERMINISTIC
BEGIN

DECLARE result VARCHAR(255);

SET result = CONCAT(SUBSTR(plain_data, 1, CHAR_LENGTH(plain_data)-FLOOR(CHAR_LENGTH(plain_data)*0.75)), 
    repeat('*', FLOOR(CHAR_LENGTH(plain_data)*0.75)), SUBSTR(plain_data, CHAR_LENGTH(plain_data), CHAR_LENGTH(plain_data)+1));

return result;

END //
DELIMITER ;
```

### Singlestore Version https://www.singlestore.com/
```sql
-- EW_MASK
DELIMITER //
CREATE OR REPLACE FUNCTION EW_MASK(plain_data VARCHAR(255))
    RETURNS VARCHAR(255) AS
    DECLARE 
        result VARCHAR(255);
        masker VARCHAR(255);
        masker_count int;
        i int;
    BEGIN
        masker = '';
        i = 1;
        masker_count = FLOOR(CHARACTER_LENGTH(plain_data)*0.75);
        WHILE i < masker_count LOOP
            masker = CONCAT(masker, '*');
            i += 1;
        END LOOP;
        result = CONCAT(SUBSTR(plain_data, 1, CHARACTER_LENGTH(plain_data)-FLOOR(CHARACTER_LENGTH(plain_data)*0.75)), 
            masker, SUBSTR(plain_data, CHARACTER_LENGTH(plain_data), CHARACTER_LENGTH(plain_data)+1));

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
| wur*****o            |
+----------------------+
```
