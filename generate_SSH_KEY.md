# Linux generate SSH Key

 The `SSH` supports different kinds of authentication techniques. One of the most common mechanisms is password authentication, and the other one is public-key-based authentication. In both, the public key-based authentication is more secure and convenient than the password authentication method and based on digital signatures.

 ### Verify SSH Key if exist

 ```shell
 $ ls -l ~/.ssh/id_*.pub
 ```

### Generate new SSH key pair
If you will receive an error saying `No such file or directory` then, it means you don’t have an SSH key on your machine.

 ```shell
 $ ssh-keygen -t rsa -b 4096 -C "sudiman@yahoo.com"
 ```

 then answer several questions. Notes: `just leave passphrase empty `.

 ### Verify SSH key pair generated

 ```shell
 $ ls ~/.ssh/id_*
 /home/sudiman/.ssh/id_rsa  /home/sudiman/.ssh/id_rsa.pub
 ```

 ### Copy the public key to Ubuntu remote server

 #### Copy with `ssh-copy-id`
 ```shell
 $ ssh-copy-id your_username@your_host
 ```

Replace `your_username` and `your_host` with yours.

#### Manual Copy to the Server
```shell
$ cat ~/.ssh/id_rsa.pub | ssh your_username@your_host "mkdir -p ~/.ssh && chmod 700 ~/.ssh && cat >> ~/.ssh/authorized_keys && chmod 600 ~/.ssh/authorized_keys"
```

Replace `your_username` and `your_host` with yours.

### Try to Log in with the SSH key on a remote server
```shell
$ ssh your_username@your_host
```

### Disable SSH password authentication
In this step, you will disable the SSH password authentication to add an extra layer of security. Before disable, make sure that the user, which is login, has sudo privileges or a root account on this server. First, login into your remote server with a root account or sudo privileges. Now, open the ‘/etc/ssh/sshd_config’ SSH configuration file by executing the below-given command:

```shell
$ sudo vi /etc/ssh/sshd_config
```

Find the `PasswordAuthentication` configuration line and set it with a ‘no’ argument.
```
PasswordAuthentication no
```

Exit and restart `SSH Service`
```shell
$ sudo systemctl restart ssh
```