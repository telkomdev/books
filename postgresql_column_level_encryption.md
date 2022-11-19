### Postgresql Column Level Symmetric Encryption with PGCRYPTO

https://www.postgresql.org/docs/current/pgcrypto.html#id-1.11.7.35.8

- login as superuser
```shell
$ sudo --login --user postgres
```

- connect to specific Database
```shell
$ \c database_name;
```

- show installed extensions
```shell
$ select extname from pg_extension ;
```

- create pgcrypto EXTENSION
```shell
CREATE EXTENSION IF NOT EXISTS pgcrypto;
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

## Custom AES ENCRYPTION and DECRYPTION function
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
-- ew_aes_encrypt
CREATE OR REPLACE FUNCTION ew_aes_encrypt(aes_key VARCHAR, plain_data VARCHAR) 
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
    cipher_data = ENCODE(encrypt_iv(plain_data::bytea, aes_key::bytea, DECODE(iv, 'hex'), 'aes-cbc/pad:pkcs'), 'hex');
    result = CONCAT(iv, cipher_data);
    RETURN result;                            
END;  
$$;

-- ew_aes_decrypt
CREATE OR REPLACE FUNCTION ew_aes_decrypt(aes_key VARCHAR, encrypted_data VARCHAR) 
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
    result = convert_from(decrypt_iv(decode(cipher_data, 'hex'), aes_key::bytea, DECODE(iv, 'hex'), 'aes-cbc/pad:pkcs'), 'UTF8');
    RETURN result;                            
END;  
$$;
```
