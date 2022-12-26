### Postgresql Column Level Symmetric Encryption with PGCRYPTO

https://www.postgresql.org/docs/current/pgcrypto.html#id-1.11.7.35.8

- login as superuser
```shell
$ sudo --login --user postgres
```

### Start the PostgreSQL interactive terminal.
```shell
$ psql
```

### Create the database `haha`
```shell
$ postgres=# CREATE DATABASE haha;
```

- connect to specific Database
```shell
$ \c haha;
```

- show installed extensions
```shell
$ select extname from pg_extension ;
```

- create pgcrypto EXTENSION
```shell
CREATE EXTENSION IF NOT EXISTS pgcrypto;
```

### Create the user `haha` for database `haha`.
```shell
$ postgres=# CREATE USER haha WITH PASSWORD 'haha12345';
```

### Grant the user `haha` access to the `haha` database.
```shell
$ postgres=# GRANT ALL PRIVILEGES ON DATABASE haha to haha;
```

### Exit the PostgreSQL interactive terminal
```shell
$ postgres=# \q
```

### Log out of the postgres root.
```shell
$ exit
```

- login with user created
```shell
$ psql --dbname=haha --host=localhost --username=haha --password
```

- create sample table
`bytea` as an alternative if you want to store encrypted `json` data, instead of`jsonb` 
```sql
CREATE TABLE public.USERS (
    id serial primary key,
    email varchar not null unique,
    credit_card varchar not null,
    meta bytea
);
```

- insert sample data
```sql
INSERT INTO USERS (EMAIL, CREDIT_CARD, META) VALUES (encode(pgp_sym_encrypt('alex@gmail.com', 'verystrongkey123'), 'base64'), encode(pgp_sym_encrypt('4797459275128533', 'verystrongkey123'), 'base64'), pgp_sym_encrypt('{"age": 32, "phone": "081"}', 'verystrongkey123'));
INSERT INTO USERS (EMAIL, CREDIT_CARD, META) VALUES (encode(pgp_sym_encrypt('bony@gmail.com', 'verystrongkey123'), 'base64'), encode(pgp_sym_encrypt('4455778542145936', 'verystrongkey123'), 'base64'), pgp_sym_encrypt('{"age": 32, "phone": "081"}', 'verystrongkey123'));
INSERT INTO USERS (EMAIL, CREDIT_CARD, META) VALUES (encode(pgp_sym_encrypt('sisy@gmail.com', 'verystrongkey123'), 'base64'), encode(pgp_sym_encrypt('4797472753193994', 'verystrongkey123'), 'base64'), pgp_sym_encrypt('{"age": 32, "phone": "081"}', 'verystrongkey123'));

```

- read data
```sql
SELECT pgp_sym_decrypt(decode(EMAIL::text, 'base64'), 'verystrongkey123') as EMAIL_D, pgp_sym_decrypt(decode(CREDIT_CARD::text, 'base64'), 'verystrongkey123') as CC_D FROM USERS WHERE pgp_sym_decrypt(decode(EMAIL::text, 'base64'), 'verystrongkey123') LIKE 'a%';

SELECT pgp_sym_decrypt(decode(EMAIL::text, 'base64'), 'verystrongkey123') as EMAIL_D, pgp_sym_decrypt(decode(CREDIT_CARD::text, 'base64'), 'verystrongkey123') as CC_D FROM USERS ORDER BY pgp_sym_decrypt(decode(EMAIL::text, 'base64'), 'verystrongkey123') ASC;

SELECT pgp_sym_decrypt(decode(EMAIL::text, 'base64'), 'verystrongkey123') as EMAIL_D, pgp_sym_decrypt(decode(CREDIT_CARD::text, 'base64'), 'verystrongkey123') as CC_D FROM USERS ORDER BY pgp_sym_decrypt(decode(EMAIL::text, 'base64'), 'verystrongkey123') DESC;

select pgp_sym_decrypt(meta::bytea, 'verystrongkey123') from users;
```

## Custom

### using specific Algorithm

Encrypt
```
select encode(pgp_sym_encrypt('alex@gmail.com', 'verystrongkey123YjgyYmRhNTYtYjJi', 'compress-algo=1, cipher-algo=aes256'), 'base64');
```

Decrypt
```
select pgp_sym_decrypt(decode('ww0ECQMC56dtkkLZ0/Bk0kQBKvBV9UHmZK3VS8Nj85O2kkLL29D1xRxbjf1rANSBkUmdfm9rAnJpVeZcuUmcOvqeB3Q3LmLgwShgh2JyWkAqDamHug==', 'base64'), 'verystrongkey123YjgyYmRhNTYtYjJi', 'compress-algo=1, cipher-algo=aes256');
```

### Raw Encryption
Encrypt
```
SELECT encode(encrypt('exampleplaintextxxmmx', 'YjgyYmRhNTYtYjJi', 'aes-ecb/pad:pkcs'), 'base64');
 jYuiaf34desC1RXF3+ETyFFNBCpZYAOE+eEMcZPLJLY=
```

Decrypt
```
select convert_from(decrypt(decode('jYuiaf34desC1RXF3+ETyFFNBCpZYAOE+eEMcZPLJLY=', 'base64'), 'YjgyYmRhNTYtYjJi', 'aes-ecb/pad:pkcs'), 'UTF8');
 exampleplaintextxxmmx
```

# hmac
Calculates hashed MAC for data with key key. type is the same as in digest().

This is similar to digest() but the hash can only be recalculated knowing the key. This prevents the scenario of someone altering data and also changing the hash to match.

```shell
> select encode(hmac('wuriyanto', 'abc$#128djdyAgbjau&YAnmcbagryt5x', 'sha256'), 'hex');
------------------------------------------------------------------
 9f46bcc1bdc24ff2d4b6f811c1dd7e053089e515b0525c2b2a7ff25c28eb4240
(1 row)
```

# Error
### Reason Error: `ERROR:  invalid base64 end sequence`

After execute `select encode(pgp_sym_encrypt` the result will be in two line
```
select encode(pgp_sym_encrypt('alex@gmail.com', 'verystrongkey123YjgyYmRhNTYtYjJi', 'compress-algo=1, cipher-algo=aes256'), 'base64');
 ww0ECQMCsFBwOwoWwrd00kQBIQg+Ms3UknNfKNs/u+W8fC+QWc7t0QLqEn2UjWZRrA3sD0Y6jPML+
 DZGeiUVsbIxuwTVbT2amjLmDLUYvas7SH4JrKg==
 ```

Remove the new line, and remove the `+` sign, and it will be
```
ww0ECQMCsFBwOwoWwrd00kQBIQg+Ms3UknNfKNs/u+W8fC+QWc7t0QLqEn2UjWZRrA3sD0Y6jPMLDZGeiUVsbIxuwTVbT2amjLmDLUYvas7SH4JrKg==
```

## Custom AES-256-CBC ENCRYPTION and DECRYPTION function
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
CREATE OR REPLACE FUNCTION EW_AES_256_CBC_ENCRYPT(aes_key VARCHAR, plain_data VARCHAR) 
    RETURNS VARCHAR
    LANGUAGE plpgsql
AS 
$$
DECLARE
    iv VARCHAR;
    cipher_data VARCHAR;                                                   
    result VARCHAR;    
BEGIN
    iv = ENCODE(GEN_RANDOM_BYTES(16), 'hex');
    cipher_data = ENCODE(ENCRYPT_IV(CONVERT_TO(plain_data, 'UTF8')::BYTEA, aes_key::BYTEA, DECODE(iv, 'hex'), 'aes-cbc/pad:pkcs'), 'hex');
    result = CONCAT(iv, cipher_data);
    RETURN result;                            
END;  
$$;

-- EW_AES_256_CBC_DECRYPT
CREATE OR REPLACE FUNCTION EW_AES_256_CBC_DECRYPT(aes_key VARCHAR, encrypted_data VARCHAR) 
    RETURNS VARCHAR
    LANGUAGE plpgsql
AS 
$$
DECLARE
    iv VARCHAR;
    cipher_data VARCHAR;                                                   
    result VARCHAR;    
BEGIN
    iv = SUBSTRING(encrypted_data, 1, 32);
    cipher_data = SUBSTRING(encrypted_data, 33, LENGTH(encrypted_data) - 32);
    IF cipher_data IS NOT NULL AND cipher_data != '' THEN
        result = CONVERT_FROM(DECRYPT_IV(DECODE(cipher_data, 'hex'), aes_key::BYTEA, DECODE(iv, 'hex'), 'aes-cbc/pad:pkcs'), 'UTF8');
    END IF;
    RETURN result;                            
END;  
$$;
```

### Usage

```
haha=> SELECT EW_AES_256_CBC_ENCRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', 'alex@gmail.com');
                          ew_aes_encrypt
------------------------------------------------------------------
 766eef4290baa51f67d36a7c2ff937e877c7c63a48a7c7a8143cb3d6affe576e
(1 row)

haha=> SELECT EW_AES_256_CBC_DECRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', '766eef4290baa51f67d36a7c2ff937e877c7c63a48a7c7a8143cb3d6affe576e');
 ew_aes_decrypt
----------------
 alex@gmail.com
(1 row)
```

### Create New Table
```sql
CREATE TABLE public.USERS (
    id serial primary key,
    email varchar not null,
    email_mac varchar not null,
    credit_card varchar not null,
    credit_card_mac varchar not null
);
```

### Insert Data
```shell
> INSERT INTO USERS (EMAIL, EMAIL_MAC, CREDIT_CARD, CREDIT_CARD_MAC) VALUES (EW_AES_256_CBC_ENCRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', 'alex@gmail.com'), ENCODE(HMAC('alex@gmail.com', 'abc$#128djdyAgbjau&YAnmcbagryt5x', 'SHA256'), 'HEX'), EW_AES_256_CBC_ENCRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', '4797459275128533'), ENCODE(HMAC('4797459275128533', 'abc$#128djdyAgbjau&YAnmcbagryt5x', 'SHA256'), 'HEX'));

> INSERT INTO USERS (EMAIL, EMAIL_MAC, CREDIT_CARD, CREDIT_CARD_MAC) VALUES (EW_AES_256_CBC_ENCRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', 'bony@gmail.com'), ENCODE(HMAC('bony@gmail.com', 'abc$#128djdyAgbjau&YAnmcbagryt5x', 'SHA256'), 'HEX'), EW_AES_256_CBC_ENCRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', '4455778542145936'), ENCODE(HMAC('4455778542145936', 'abc$#128djdyAgbjau&YAnmcbagryt5x', 'SHA256'), 'HEX'));

> INSERT INTO USERS (EMAIL, EMAIL_MAC, CREDIT_CARD, CREDIT_CARD_MAC) VALUES (EW_AES_256_CBC_ENCRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', 'sisy@gmail.com'), ENCODE(HMAC('sisy@gmail.com', 'abc$#128djdyAgbjau&YAnmcbagryt5x', 'SHA256'), 'HEX'), EW_AES_256_CBC_ENCRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', '4797472753193994'), ENCODE(HMAC('4797472753193994', 'abc$#128djdyAgbjau&YAnmcbagryt5x', 'SHA256'), 'HEX'));
```

### Read Data
```shell
> SELECT EW_AES_256_CBC_DECRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', EMAIL) FROM USERS WHERE EW_AES_256_CBC_DECRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', EMAIL) LIKE '%al%';

>  SELECT EW_AES_256_CBC_DECRYPT('abc$#128djdyAgbjau&YAnmcbagryt5x', EMAIL) FROM USERS WHERE EMAIL_MAC = ENCODE(HMAC('sisy@gmail.com', 'abc$#128djdyAgbjau&YAnmcbagryt5x', 'SHA256'), 'HEX');
```
