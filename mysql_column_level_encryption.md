### Mysql Column Level Symmetric Encryption with Encryption Function

https://dev.mysql.com/doc/refman/8.0/en/encryption-functions.html

install Mysql Server
```shell
$ sudo apt install mysql-server
```

login as mysql super user
```shell
> sudo mysql
```

show all users 
```shell
> SELECT user,authentication_string,plugin,host FROM mysql.user;
```

create new user
```shell
> CREATE USER 'wury'@'localhost' IDENTIFIED WITH mysql_native_password BY 'haha12345';
```

create new database
```shell
> CREATE DATABASE haha;
```

grant user we just created to the database
https://dev.mysql.com/doc/refman/8.0/en/privileges-provided.html#privileges-provided-summary
```shell
> GRANT CREATE, ALTER, DROP, INSERT, UPDATE, DELETE, SELECT, REFERENCES on haha.* TO 'wury'@'localhost' WITH GRANT OPTION;
```

flush privileges
```shell
> FLUSH PRIVILEGES;
```

showing Grants for specific user
```shell
> SHOW GRANTS FOR 'wury'@'localhost';
```

login with new user we just created
```shell
> mysql -u wury -h localhost -p
```

change database
```shell
> use haha;
```

show tables
```shell
> show tables;
```

create new table
```shell
> CREATE TABLE USERS (
    id INT(6) UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    email TEXT NOT NULL,
    credit_card TEXT NOT NULL
);
```

insert data
```shell
> INSERT INTO USERS (EMAIL, CREDIT_CARD) VALUES (TO_BASE64(AES_ENCRYPT('alex@gmail.com', 'F3229A0B371ED2D9441B830D21A390C3')), TO_BASE64(AES_ENCRYPT('4797459275128533', 'F3229A0B371ED2D9441B830D21A390C3')));
> INSERT INTO USERS (EMAIL, CREDIT_CARD) VALUES (TO_BASE64(AES_ENCRYPT('bony@gmail.com', 'F3229A0B371ED2D9441B830D21A390C3')), TO_BASE64(AES_ENCRYPT('4455778542145936', 'F3229A0B371ED2D9441B830D21A390C3')));
> INSERT INTO USERS (EMAIL, CREDIT_CARD) VALUES (TO_BASE64(AES_ENCRYPT('sisy@gmail.com', 'F3229A0B371ED2D9441B830D21A390C3')), TO_BASE64(AES_ENCRYPT('4797472753193994', 'F3229A0B371ED2D9441B830D21A390C3')));
```

read data
```shell
> SELECT AES_DECRYPT(FROM_BASE64(EMAIL), 'F3229A0B371ED2D9441B830D21A390C3') as EMAIL from USERS;
> SELECT AES_DECRYPT(FROM_BASE64(EMAIL), 'F3229A0B371ED2D9441B830D21A390C3') as EMAIL_D,AES_DECRYPT(FROM_BASE64(CREDIT_CARD), 'F3229A0B371ED2D9441B830D21A390C3') from USERS WHERE AES_DECRYPT(FROM_BASE64(EMAIL), 'F3229A0B371ED2D9441B830D21A390C3') = 'alex@gmail.com';
> SELECT AES_DECRYPT(FROM_BASE64(EMAIL), 'F3229A0B371ED2D9441B830D21A390C3') as EMAIL_D,AES_DECRYPT(FROM_BASE64(CREDIT_CARD), 'F3229A0B371ED2D9441B830D21A390C3') from USERS WHERE AES_DECRYPT(FROM_BASE64(EMAIL), 'F3229A0B371ED2D9441B830D21A390C3') LIKE 'al%';
> SELECT AES_DECRYPT(FROM_BASE64(EMAIL), 'F3229A0B371ED2D9441B830D21A390C3') as EMAIL_D,AES_DECRYPT(FROM_BASE64(CREDIT_CARD), 'F3229A0B371ED2D9441B830D21A390C3') from USERS ORDER BY AES_DECRYPT(FROM_BASE64(EMAIL), 'F3229A0B371ED2D9441B830D21A390C3') DESC;
```