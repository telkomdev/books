# Docker best practice
## Cleanup dangling images
```
$ docker image rm $(docker image ls | grep '<none>' | awk '{print $3}')
```