Install `putty-tools`
```shell
$ sudo apt-get install putty-tools
```

generate the `private key` from `.ppk`
```shell
$ puttygen idrsa.ppk -O private-openssh -o idrsa_b
```

generate the `public key` from `.ppk`
```shell
$ puttygen idrsa_a.ppk -O public-openssh -o idrsa_b.pub
```
