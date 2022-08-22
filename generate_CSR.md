## Pengertian CSR
Certificate Signing Request atau disingkat CSR adalah kunci publik yang belum diformat khusus yang digunakan untuk pendaftaran Sertifikat SSL. Informasi tentang CSR ini penting untuk Otoritas Sertifikat (CA). Diperlukan untuk memvalidasi informasi yang diperlukan untuk mengeluarkan Sertifikat SSL. Kunci publik (CSR) secara bebas diberikan oleh sistem server atau administrator sehingga masing-masing pihak dapat melakukan enkripsi.

### Generate Private Key

```shell
$ openssl genrsa -out private.key 2048
```

### Generate CSR with Private Key and fill the required fields

```shell
$ openssl req -new -key private.key -out csr.txt

You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:ID
State or Province Name (full name) [Some-State]:DKI Jakarta
Locality Name (eg, city) []:Jakarta Pusat
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Your Company
Organizational Unit Name (eg, section) []:Your Company Unit
Common Name (e.g. server FQDN or YOUR name) []:*.yourdomain.com
Email Address []:youremail@gmail.com

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:
```