# How to work around with Docker pull limit
Source: [How to work around Docker's new download rate limit on Red Hat OpenShift](https://developers.redhat.com/blog/2021/02/18/how-to-work-around-dockers-new-download-rate-limit-on-red-hat-openshift#authenticate_to_your_docker_hub_account)

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
