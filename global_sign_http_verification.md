## GlobalSign SSL Verification with HTTP Verification
- create folder `/home/user/teco/.well-known/pki-validation`
- inside `/home/user/teco/.well-known/pki-validation` folder, create `gsdv.txt` file and fill with `<meta name="_globalsign-domain-verification" content="your magic string" />`

### Bonus `nginx.conf`

```
server {
    listen 80;
    root /home/user/teco;
    index index.html index.htm index.nginx-debian.html;

    server_name teco.yourdomain.co;

    location /.well-known/pki-validation/gsdv.txt {
      alias /home/user/teco/.well-known/pki-validation/gsdv.txt;
      add_header Content-type text/plain;
    }

}
```