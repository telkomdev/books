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

### Error

`permissions 0644 for are too open`

The keys need to be read-writable only by you
```shell
$ chmod 600 server_ssh.key
```

`Failed to load key idrsa_data: invalid format`

Add new line to the `server_ssh.key`
```
Tk3AQIDBAUGBw==
-----END OPENSSH PRIVATE KEY----- (press Enter button here, to add new line)

```

### CRLF
Replace CRLF to LF

```
$ sed 's/\r$//' server_ssh.key > server_ssh_backup.key
```
