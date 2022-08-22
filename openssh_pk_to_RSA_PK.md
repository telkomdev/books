### Convert OpenSSH Private Key to RSA Private Key

Your OpenSSH Private Key looks like this
```
-----BEGIN OPENSSH PRIVATE KEY-----
b34ahC
-----END OPENSSH PRIVATE KEY-----
```

First, backup your OpenSSH Private Key
```shell
$ cp server_ssh.key server_ssh.key.backup
```

Convert to RSA Private Key
```shell
$ ssh-keygen -p -N "" -m pem -f server_ssh.key
```

Now your server_ssh.key looks like this
```
-----BEGIN RSA PRIVATE KEY-----
b34ahC
-----END RSA PRIVATE KEY-----
```