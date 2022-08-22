## how to fix

### curl: (60) SSL certificate problem: unable to get local issuer certificate

--------------------------------------

just append your certificate and CA certificate

```shell
$ cat server.crt ca.crt > server.chained.crt
```

### server.crt = usually the long one (The PEM format certificate)
### ca.crt = usually the short one (The PEM format CA certificate chain)

-------------------------------------

```
nginx.conf

// --------
ssl_certificate      /home/yourusername/ssl/server.chained.crt;
ssl_certificate_key  /home/yourusername/ssl/server_private.key;

// ---------
```