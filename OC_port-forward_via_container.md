## Openshift port-forward via Docker container
forward service / container / pods port from Openshift to local using docker container [devtools]

### Requirements
- Docker Engine [https://docs.docker.com/engine/install/]

### Example commands to forward mongodb service
1. Run container [devtools]
```bash
# specify service / pods/ container
# example pods: mongodb-abctoken:27017
# container name: devtools-ubuntu
# local port: 27018
# mongodb port: 27017
sudo docker run --rm -it -p 27018:27017 --name devtools-ubuntu ewzengineering/devtools:ubuntu-22.04
```
2. Login to Openshift cluster using `oc` inside container [devtools]
```bash
# copy your login command below
# you can get login command on Openshift web console
oc login https://<openshift-cluster-url-here> --token=<your-login-token-here>
```
3. Forward port to local
```bash
oc port-forward mongodb-abctoken 27017
```
Now, you can connect to forwarded port on local host machine on port `27018` or `localhost:27018`<br>
4. Verify forwarded port
- Using `nc`
```bash
nc -vzw 3 localhost 27018
# output: Connection to localhost (127.0.0.1) 27018 port [mongodb] succeeded!
```
- Using `mongo` or `mongosh` [mongodb shell client: https://www.mongodb.com/try/download/shell]
```
mongo mongodb://<username>:<password>@localhost:27018/<database_name>
```
