## Create new service account
```shell
$ oc create sa <service_account_name> -n <namespace>
```
## Add user's role (role binding)
```shell
$ oc adm policy add-role-to-user <role> -z <service_account_name> -n <namespace>
```
## Get service account token
```shell
$ oc sa get-token <service_account_name> -n <namespace>
```
