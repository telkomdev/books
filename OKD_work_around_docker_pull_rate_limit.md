## Authenticate to Docker Hub account
```shell
$ oc create secret docker-registry docker \
  --docker-server=docker.io \
  --docker-username=<username> \
  --docker-password=<password> \
  --docker-email=<email>
```
## Link secret `docker` to default Service Account
```shell
$ oc secrets link default docker --for=pull
```
