# k3s Common Command Usage

## Install `kubectl`
Installation instruction based on official docs
- Windows: https://kubernetes.io/docs/tasks/tools/install-kubectl-windows
- Linux: https://kubernetes.io/docs/tasks/tools/install-kubectl-linux
- MacOS: https://kubernetes.io/docs/tasks/tools/install-kubectl-macos
Verify `kubectl` installation
```shell
kubectl version
```
If installation proccess correct, you will get output like this
```
Client Version: version.Info{Major:"1", Minor:"27", GitVersion:"v1.27.4", GitCommit:"fa3d7990104d7c1f16943a67f11b154b71f6a132", GitTreeState:"clean", BuildDate:"2023-07-19T12:20:54Z", GoVersion:"go1.20.6", Compiler:"gc", Platform:"linux/amd64"}
Kustomize Version: v5.0.1
```

## Setup kubeconfig
The default location of `kubectl` config is located on `~/.kube/config`, you can copy `kubeconfig` to default config file or change the default config file location by updating OS environment `KUBECONFIG`<br>
If you are using k3s UI like rancher, there is download menu on navigation top bar<br>
<img src="https://i.ibb.co/B4wDHHW/Screenshot-from-2023-07-27-14-48-32-cropped.png" /><br>
Example usage by usuing `KUBECONFIG` OS environment
```shell
# downloaded kubeconfig location ~/Downloads/k3s-cluster.yaml

# update default kubconfig
export KUBECONFIG=~/Downloads/k3s-cluster.yaml

# try to get server version information
kubectl version

#  will get ouput server information like this
# Client Version: version.Info{Major:"1", Minor:"27", GitVersion:"v1.27.4", GitCommit:"fa3d7990104d7c1f16943a67f11b154b71f6a132", GitTreeState:"clean", BuildDate:"2023-07-19T12:20:54Z", GoVersion:"go1.20.6", Compiler:"gc", Platform:"linux/amd64"}
# Kustomize Version: v5.0.1
# Server Version: version.Info{Major:"1", Minor:"24", GitVersion:"v1.24.6+k3s1", GitCommit:"a8e0c66d1a90a2bbc4ffa975129ca35756cc7c14", GitTreeState:"clean", BuildDate:"2022-09-28T16:52:07Z", GoVersion:"go1.18.6", Compiler:"gc", Platform:"linux/amd64"}
```

## Forwarding service port to local
On k3s, you can easily container/pods port by using service
```shell
# namespace or project: my-project
# service name: my-microservice
kubectl port-forward svc/my-microservice -n my-project 8080:8080

# example output
# Forwarding from 127.0.0.1:8080 -> 8080
# Forwarding from [::1]:8080 -> 8080

```
