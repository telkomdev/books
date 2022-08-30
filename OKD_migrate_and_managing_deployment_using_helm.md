# Migrate & managing deployment using Helm
## Update current YAML file on K8s/Openshift/OKD
### Current YAML config file
```yaml
apiVersion: apps.openshift.io/v1
...
metadata:
  ...
  labels:
    app: REST-Services
  name: my-rest-api
  namespace: my-project
  ...
...
```
### New YAML config file
```yaml
apiVersion: apps.openshift.io/v1
...
metadata:
  ...
  labels:
    app: REST-Services
    app.kubernetes.io/managed-by: Helm
  annotations:
    meta.helm.sh/release-name: my-rest-api
    meta.helm.sh/release-namespace: my-project
  name: my-rest-api
  namespace: my-project
  ...
```
## Run deployment using Helm chart
```shell
$ helm upgrade --install --debug --namespace my-project my-rest-api .
```
## Known issues
### YAML config on K8s/Openshift/OKD wont update
Try to run deployment using `--force` option, use at your own risk
```shell
$ helm upgrade --install --force --debug --namespace my-project my-rest-api .
```
