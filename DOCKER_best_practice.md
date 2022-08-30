# Docker best practice
## Cleanup dangling images
```shell
$ docker image rm $(docker image ls | grep '<none>' | awk '{print $3}')
```
## Cleanup unused container
```shell
$ docker rm -f $(docker ps -a | grep Exited | awk '{print $1}')
```
## Copy file from image
```shell
$ docker run --rm --entrypoint cat <docker-image:latest> <source_file> > <target_file>
```
## Run & bind volume to container
```shell
$ docker run --rm -it -v <my-volume>:<target_bind_directory> <docker-image:latest>
```
## Run & expose port to container
```
$ docker run --rm -it -p <source_port>:<container_port> <docker-image:latest>
```
