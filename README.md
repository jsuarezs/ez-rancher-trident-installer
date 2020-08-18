# What is this?

This is an ansible operator that will install trident-20.04.0 into a K8S cluster.

# Quick start

1. `make deploy`
2. After a few minutes, trident will be running in the `trident` namespace:

```
-> trident-installer git:(master) x kubectl get pods -n trident
NAME                                 READY   STATUS    RESTARTS   AGE
trident-csi-788b4d865c-4fs2t         5/5     Running   0          20s
trident-csi-tpxh2                    2/2     Running   0          20s
trident-installer-66dcdf485b-c4rlq   1/1     Running   0          42s
trident-operator-668bf8cdff-n56pd    1/1     Running   0          32s
```


## Example TridentInstallation

```
apiVersion: tridentinstall.czan.io/v1alpha1
kind: TridentInstallation
metadata:
  name: example-tridentinstallation
spec:
  trident_username: solidfire
  trident_password: solidfire
  tenant_name: k8s_trident
  SVIP: 10.0.0.1
  MVIP: 10.0.0.2
  backend_name: solidfire
  storage_driver_name: solidfire-san
  use_chap: true

```