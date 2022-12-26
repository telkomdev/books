## Mysql Column Level Symmetric Encryption with Encryption Function

https://dev.mysql.com/doc/refman/8.0/en/encryption-functions.html

### install Mysql Server
```shell
$ sudo apt install mysql-server
```

### login as mysql super user
```shell
> sudo mysql
```

### show all users 
```shell
> SELECT user,authentication_string,plugin,host FROM mysql.user;
```

## create new user
```shell
> CREATE USER 'wury'@'localhost' IDENTIFIED WITH mysql_native_password BY 'haha12345';
```

### Create new user allow `remote access`
```shell
> CREATE USER 'wury'@'%' IDENTIFIED WITH mysql_native_password BY 'haha12345';
```

### create new database
```shell
> CREATE DATABASE haha;
```

### grant user we just created to the database
https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#privileges-provided-summary
```shell
> GRANT CREATE, ALTER, DROP, INSERT, UPDATE, DELETE, SELECT, REFERENCES on `haha`.* TO 'wury'@'localhost' WITH GRANT OPTION;
```

### grant user we just created to the database allow `remote access`
```shell
> GRANT CREATE, ALTER, DROP, INSERT, UPDATE, DELETE, SELECT, REFERENCES on `haha`.* TO 'wury'@'%' WITH GRANT OPTION;
```

### flush privileges
```shell
> FLUSH PRIVILEGES;
```

### showing Grants for specific user
```shell
> SHOW GRANTS FOR 'wury'@'localhost';
```

### login with new user we just created
```shell
> mysql -u wury -h localhost -p
```

### change database
```shell
> use haha;
```

### show tables
```shell
> show tables;
```

### create new table
```shell
> CREATE TABLE USERS (
    id INT(6) UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    email VARCHAR(255) NOT NULL,
    email_mac VARCHAR(255) NOT NULL,
    credit_card VARCHAR(255) NOT NULL,
    credit_card_mac VARCHAR(255) NOT NULL
);
```

### create `Index`
show indexes
```shell
> SHOW INDEXES FROM USERS;
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table | Non_unique | Key_name | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| USERS |          0 | PRIMARY  |            1 | id          | A         |           0 |     NULL | NULL   |      | BTREE      |         |               |
+-------+------------+----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
```

create `Unique Index` for `email`.  
we use the `email_mac column`, so we can index the values. if we use column email, we can't use it as index, because `email column value` is always `random` when `encrypted`
```shell
> CREATE UNIQUE INDEX idx_email ON USERS (email_mac);
```

show indexes after we add new index
```shell
mysql> SHOW INDEXES FROM USERS;
+-------+------------+-----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table | Non_unique | Key_name  | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+-------+------------+-----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| USERS |          0 | PRIMARY   |            1 | id          | A         |           0 |     NULL | NULL   |      | BTREE      |         |               |
| USERS |          0 | idx_email |            1 | email_mac   | A         |           0 |     NULL | NULL   |      | BTREE      |         |               |
+-------+------------+-----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
```

### HMACSHA256 MySQL Version
https://github.com/fish3046/MySQL-SHA256-HMAC/blob/master/HMACSHA256.sql

### HMACSHA256 (Singlestore Version https://www.singlestore.com/)
```sql
-- here val is the message generate a HMAC for
DELIMITER //
CREATE OR REPLACE FUNCTION HMACSHA256(secret_key VARCHAR(256), val VARCHAR(2048))
    RETURNS CHAR(64) AS
    DECLARE 
        ipad BINARY(64);
        opad BINARY(64);
        hexkey CHAR(128);
        hmac CHAR(64);
    BEGIN

        hexkey = RPAD(HEX(secret_key), 128,"0");

        IF LENGTH(secret_key) > 64 THEN
            hexkey = RPAD(SHA2(secret_key, '256'), 128, "0");
        END IF;

        ipad = UNHEX(CONCAT(
        LPAD(CONV(CONV( SUBSTRING(hexkey,1  ,16), 16, 10 ) ^ CONV( '3636363636363636', 16, 10 ),10,16),16,"0"),
        LPAD(CONV(CONV( SUBSTRING(hexkey,17 ,16), 16, 10 ) ^ CONV( '3636363636363636', 16, 10 ),10,16),16,"0"),
        LPAD(CONV(CONV( SUBSTRING(hexkey,33 ,16), 16, 10 ) ^ CONV( '3636363636363636', 16, 10 ),10,16),16,"0"),
        LPAD(CONV(CONV( SUBSTRING(hexkey,49 ,16), 16, 10 ) ^ CONV( '3636363636363636', 16, 10 ),10,16),16,"0"),
        LPAD(CONV(CONV( SUBSTRING(hexkey,65 ,16), 16, 10 ) ^ CONV( '3636363636363636', 16, 10 ),10,16),16,"0"),
        LPAD(CONV(CONV( SUBSTRING(hexkey,81 ,16), 16, 10 ) ^ CONV( '3636363636363636', 16, 10 ),10,16),16,"0"),
        LPAD(CONV(CONV( SUBSTRING(hexkey,97 ,16), 16, 10 ) ^ CONV( '3636363636363636', 16, 10 ),10,16),16,"0"),
        LPAD(CONV(CONV( SUBSTRING(hexkey,113,16), 16, 10 ) ^ CONV( '3636363636363636', 16, 10 ),10,16),16,"0")
        ));

        opad = UNHEX(CONCAT(
        LPAD(CONV(CONV( SUBSTRING(hexkey,1  ,16), 16, 10 ) ^ CONV( '5c5c5c5c5c5c5c5c', 16, 10 ),10,16),16,"0"),
        LPAD(CONV(CONV( SUBSTRING(hexkey,17 ,16), 16, 10 ) ^ CONV( '5c5c5c5c5c5c5c5c', 16, 10 ),10,16),16,"0"),
        LPAD(CONV(CONV( SUBSTRING(hexkey,33 ,16), 16, 10 ) ^ CONV( '5c5c5c5c5c5c5c5c', 16, 10 ),10,16),16,"0"),
        LPAD(CONV(CONV( SUBSTRING(hexkey,49 ,16), 16, 10 ) ^ CONV( '5c5c5c5c5c5c5c5c', 16, 10 ),10,16),16,"0"),
        LPAD(CONV(CONV( SUBSTRING(hexkey,65 ,16), 16, 10 ) ^ CONV( '5c5c5c5c5c5c5c5c', 16, 10 ),10,16),16,"0"),
        LPAD(CONV(CONV( SUBSTRING(hexkey,81 ,16), 16, 10 ) ^ CONV( '5c5c5c5c5c5c5c5c', 16, 10 ),10,16),16,"0"),
        LPAD(CONV(CONV( SUBSTRING(hexkey,97 ,16), 16, 10 ) ^ CONV( '5c5c5c5c5c5c5c5c', 16, 10 ),10,16),16,"0"),
        LPAD(CONV(CONV( SUBSTRING(hexkey,113,16), 16, 10 ) ^ CONV( '5c5c5c5c5c5c5c5c', 16, 10 ),10,16),16,"0")
        ));

        hmac = SHA2(CONCAT(opad, UNHEX(SHA2(CONCAT(ipad, val), '256'))), '256');

        RETURN hmac;

    END //
DELIMITER ;
```

Login to MySQL as a `root`
```shell
$ sudo mysql
mysql> use dbname
```

Create HMACSHA256 function
```shell
mysql> source /home/users/mysql-files/hmac_sha256.sql
```

Set Permission to user `wury` to execute `HMACSHA256` function
```shell
mysql> GRANT EXECUTE ON FUNCTION `haha`.`HMACSHA256` TO 'wury'@'localhost';
```

Test  `HMACSHA256` function
```shell
mysql> SELECT HMACSHA256('abc$#128djdyAgbjau&YAnmcbagryt5x', 'wuriyanto');
+------------------------------------------------------------------+
| HMACSHA256('abc$#128djdyAgbjau&YAnmcbagryt5x', 'wuriyanto')      |
+------------------------------------------------------------------+
| 9f46bcc1bdc24ff2d4b6f811c1dd7e053089e515b0525c2b2a7ff25c28eb4240 |
+------------------------------------------------------------------+
1 row in set (0.00 sec)

```

### Using CBC mode with a 256 bit key
By default `AES_ENCRYPT()` and `AES_DECRYPT()`  functions implement:
```
Default Value	aes-128-ecb
```

To use AES with  key length of 256 bits and the CBC mode:
```shell
> SET block_encryption_mode = 'aes-256-cbc';
```

### Custom AES-256-CBC ENCRYPTION and DECRYPTION function
```sql
/*
Created by: telkomdev team
Date: 14/11/2022

The MIT License (MIT)

Copyright (c) 2022 The TelkomDev Team

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
*/

DROP FUNCTION IF EXISTS EW_AES_256_CBC_ENCRYPT;
DROP FUNCTION IF EXISTS EW_AES_256_CBC_DECRYPT;

-- EW_AES_256_CBC_ENCRYPT
DELIMITER //
CREATE FUNCTION EW_AES_256_CBC_ENCRYPT(aes_key VARCHAR(64), plain_data VARCHAR(2048))
    RETURNS VARCHAR(255) NOT DETERMINISTIC
BEGIN

DECLARE iv VARCHAR(32);
DECLARE cipher_data VARCHAR(255);
DECLARE result VARCHAR(287);

-- Always force to use AES with key length of 256 bits and the CBC mode
SET block_encryption_mode = 'aes-256-cbc';

SET iv = HEX(RANDOM_BYTES(16));

SET cipher_data = HEX(AES_ENCRYPT(plain_data, aes_key, UNHEX(iv)));

SET result = CONCAT(iv, cipher_data);

return result;

END //
DELIMITER ;

-- EW_AES_256_CBC_DECRYPT
DELIMITER //
CREATE FUNCTION EW_AES_256_CBC_DECRYPT(aes_key VARCHAR(64), encrypted_data VARCHAR(2048))
    RETURNS VARCHAR(255) NOT DETERMINISTIC
BEGIN

DECLARE iv VARCHAR(32);
DECLARE plain_data VARCHAR(255);
DECLARE cipher_data VARCHAR(255);

-- Always force to use AES with key length of 256 bits and the CBC mode
SET block_encryption_mode = 'aes-256-cbc';

SET iv = SUBSTRING(encrypted_data, 1, 32);
SET cipher_data = SUBSTRING(encrypted_data, 33, LENGTH(encrypted_data) - 32);

SET plain_data = AES_DECRYPT(UNHEX(cipher_data), aes_key, UNHEX(iv));

return plain_data;

END //
DELIMITER ;
```

### Custom AES-256-CBC ENCRYPTION and DECRYPTION function (Singlestore Version https://www.singlestore.com/)
```sql
/*
Created by: telkomdev team
Date: 14/11/2022

The MIT License (MIT)

Copyright (c) 2022 The TelkomDev Team

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
*/

-- EW_AES_256_CBC_ENCRYPT
DELIMITER //
CREATE OR REPLACE FUNCTION EW_AES_256_CBC_ENCRYPT(aes_key VARCHAR(64), plain_data LONGTEXT)
    RETURNS LONGTEXT AS
    DECLARE
        iv VARCHAR(32);
        cipher_data LONGTEXT;
        result LONGTEXT;
    BEGIN

        iv = HEX(sys_guid());
        cipher_data = HEX(AES_ENCRYPT(plain_data, aes_key, UNHEX(iv), 'aes-256-cbc'));
        result = CONCAT(iv, cipher_data);
        return result;

    END //
DELIMITER ;

-- EW_AES_256_CBC_DECRYPT
DELIMITER //
CREATE OR REPLACE FUNCTION EW_AES_256_CBC_DECRYPT(aes_key VARCHAR(64), encrypted_data LONGTEXT)
    RETURNS LONGTEXT AS
    DECLARE 
        iv VARCHAR(32);
        plain_data LONGTEXT;
        cipher_data LONGTEXT;
    BEGIN

        iv = SUBSTRING(encrypted_data, 1, 32);
        cipher_data = SUBSTRING(encrypted_data, 33, LENGTH(encrypted_data) - 32);
        plain_data = AES_DECRYPT(UNHEX(cipher_data), aes_key, UNHEX(iv), 'aes-256-cbc');
        return plain_data;

    END //
DELIMITER ;
```

Save sql script above to `aes.sql` . Create `EW_AES_256_CBC_ENCRYPT` and `EW_AES_256_CBC_DECRYPT` function from above script
```shell
mysql> source /home/users/mysql-files/aes.sql
```

Set Permission to user `wury` to execute `EW_AES_256_CBC_ENCRYPT` and `EW_AES_256_CBC_DECRYPT` function
```shell
mysql> GRANT EXECUTE ON FUNCTION `haha`.`EW_AES_256_CBC_ENCRYPT` TO 'wury'@'localhost';

mysql> GRANT EXECUTE ON FUNCTION `haha`.`EW_AES_256_CBC_DECRYPT` TO 'wury'@'localhost';
```

Test  `EW_AES_256_CBC_ENCRYPT` function
```shell
mysql> SELECT EW_AES_256_CBC_ENCRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', 'alex@gmail.com');
+----------------------------------------------------------------------+
| EW_AES_256_CBC_ENCRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', 'alex@gmail.com') |
+----------------------------------------------------------------------+
| 569F9EAA1CA4F043A4BE284AAB71EC92AaO7oDIN5K6nAIbwK3W7bg==             |
+----------------------------------------------------------------------+
1 row in set (0.00 sec)

```

Test  `EW_AES_256_CBC_DECRYPT` function
```shell
mysql> SELECT EW_AES_256_CBC_DECRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', '569F9EAA1CA4F043A4BE284AAB71EC92AaO7oDIN5K6nAIbwK3W7bg==');
+----------------------------------------------------------------------------------------------------------------+
| EW_AES_256_CBC_DECRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', '569F9EAA1CA4F043A4BE284AAB71EC92AaO7oDIN5K6nAIbwK3W7bg==') |
+----------------------------------------------------------------------------------------------------------------+
| alex@gmail.com                                                                                                 |
+----------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)

```

### insert data
```shell
> INSERT INTO USERS (EMAIL, EMAIL_MAC, CREDIT_CARD, CREDIT_CARD_MAC) VALUES (EW_AES_256_CBC_ENCRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', 'alex@gmail.com'), HMACSHA256('abc$#128djdyAgbjau&YAnmcbagryt5x', 'alex@gmail.com'), EW_AES_256_CBC_ENCRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', '4797459275128533'), HMACSHA256('abc$#128djdyAgbjau&YAnmcbagryt5x', '4797459275128533'));

> INSERT INTO USERS (EMAIL, EMAIL_MAC, CREDIT_CARD, CREDIT_CARD_MAC) VALUES (EW_AES_256_CBC_ENCRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', 'bony@gmail.com'), HMACSHA256('abc$#128djdyAgbjau&YAnmcbagryt5x', 'bony@gmail.com'), EW_AES_256_CBC_ENCRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', '4455778542145936'), HMACSHA256('abc$#128djdyAgbjau&YAnmcbagryt5x', '4455778542145936'));

> INSERT INTO USERS (EMAIL, EMAIL_MAC, CREDIT_CARD, CREDIT_CARD_MAC) VALUES (EW_AES_256_CBC_ENCRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', 'sisy@gmail.com'), HMACSHA256('abc$#128djdyAgbjau&YAnmcbagryt5x', 'sisy@gmail.com'), EW_AES_256_CBC_ENCRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', '4797472753193994'), HMACSHA256('abc$#128djdyAgbjau&YAnmcbagryt5x', '4797472753193994'));
```

### read data
```shell
> SELECT EW_AES_256_CBC_DECRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', EMAIL) AS EMAIL FROM USERS;

> SELECT EW_AES_256_CBC_DECRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', EMAIL) AS EMAIL FROM USERS ORDER BY EW_AES_256_CBC_DECRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', EMAIL) DESC;

> SELECT EW_AES_256_CBC_DECRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', EMAIL) AS EMAIL FROM USERS WHERE EMAIL_MAC = HMACSHA256('abc$#128djdyAgbjau&YAnmcbagryt5x', 'alex@gmail.com');
```


