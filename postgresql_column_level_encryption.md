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
```sql
CREATE TABLE public.USERS (
    id serial primary key,
    email varchar not null unique,
    credit_card varchar not null
);
```

- insert sample data
```sql
INSERT INTO USERS (EMAIL, CREDIT_CARD) VALUES ((pgp_sym_encrypt('alex@gmail.com', 'verystrongkey123')), (pgp_sym_encrypt('4797459275128533', 'verystrongkey123')));
INSERT INTO USERS (EMAIL, CREDIT_CARD) VALUES ((pgp_sym_encrypt('bony@gmail.com', 'verystrongkey123')), (pgp_sym_encrypt('4455778542145936', 'verystrongkey123')));
INSERT INTO USERS (EMAIL, CREDIT_CARD) VALUES ((pgp_sym_encrypt('sisy@gmail.com', 'verystrongkey123')), (pgp_sym_encrypt('4797472753193994', 'verystrongkey123')));
```

- read data
```sql
SELECT pgp_sym_decrypt(EMAIL::bytea, 'verystrongkey123') as EMAIL_D, pgp_sym_decrypt(CREDIT_CARD::bytea, 'verystrongkey123') as CC_D FROM USERS WHERE pgp_sym_decrypt(EMAIL::bytea, 'verystrongkey123') LIKE 'a%';

SELECT pgp_sym_decrypt(EMAIL::bytea, 'verystrongkey123') as EMAIL_D, pgp_sym_decrypt(CREDIT_CARD::bytea, 'verystrongkey123') as CC_D FROM USERS ORDER BY pgp_sym_decrypt(EMAIL::bytea, 'verystrongkey123') ASC;

SELECT pgp_sym_decrypt(EMAIL::bytea, 'verystrongkey123') as EMAIL_D, pgp_sym_decrypt(CREDIT_CARD::bytea, 'verystrongkey123') as CC_D FROM USERS ORDER BY pgp_sym_decrypt(EMAIL::bytea, 'verystrongkey123') DESC;
```