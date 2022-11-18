## Postgresql Masking Function

```sql
CREATE OR REPLACE FUNCTION ew_mask(plain_data VARCHAR) 
    RETURNS VARCHAR
    LANGUAGE plpgsql
AS 
$$
DECLARE                                                   
    result VARCHAR;    
BEGIN   
    result = CONCAT(SUBSTRING(plain_data, 1, CAST (LENGTH(plain_data)-FLOOR(LENGTH(plain_data)*0.75) AS INTEGER)), 
        REPEAT('*', CAST (FLOOR(LENGTH(plain_data)*0.75) AS INTEGER)-1), 
        SUBSTRING(plain_data, LENGTH(plain_data), LENGTH(plain_data)+1));
    RETURN result;                            
END;  
$$;
```

### Usage
This function will convert 75% of the content into the specified mask character. EG *

```shell
haha=> select ew_mask('085662772142');
   ew_mask
--------------
 085********2
 ```
