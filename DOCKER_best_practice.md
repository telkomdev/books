# Docker best practice
## Cleanup dangling images
```
$ docker image rm $(docker image ls | grep '<none>' | awk '{print $3}')
```
## Cleanup unused container
```shell
$ docker rm -f $(docker ps -a | grep Exited | awk '{print $1}')
```
