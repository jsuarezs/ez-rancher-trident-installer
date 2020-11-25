# What is this?

This is an ansible operator that will install trident-20.10.0 into a K8S cluster.

# Quick start

1. Create a `trident` namespace
```
$ kubectl create ns trident
namespace/trident created
```
2. Deploy the Operator using `make deploy`:
```
$ make deploy
kubectl apply -f deploy/crds/tridentinstall.czan.io_tridentinstallations_crd.yaml
customresourcedefinition.apiextensions.k8s.io/tridentinstallations.tridentinstall.czan.io created
kubectl apply -f deploy/operator.yaml
deployment.apps/trident-installer created
kubectl apply -f deploy/role.yaml
clusterrole.rbac.authorization.k8s.io/trident-installer created
kubectl apply -f deploy/role_binding.yaml
clusterrolebinding.rbac.authorization.k8s.io/trident-installer created
kubectl apply -f deploy/service_account.yaml
serviceaccount/trident-installer created
```
3. Create a `TridentInstallation`, using the corresponding values for your NetApp HCI environment:
```
$ cat << EOF | kubectl apply -f -
apiVersion: tridentinstall.czan.io/v1alpha1
kind: TridentInstallation
metadata:
  name: trident-installation
  namespace: trident
spec:
  trident_username: solidfire
  trident_password: solidfire
  tenant_name: k8s_trident
  SVIP: 10.0.0.1
  MVIP: 10.0.0.2
  backend_name: solidfire
  storage_driver_name: solidfire-san
  use_chap: true
EOF

tridentinstallation.tridentinstall.czan.io/trident-installation created
```
4. After a few minutes, trident will be running in the `trident` namespace:

Pods:
```
$ kubectl get pods -n trident
NAME                                 READY   STATUS    RESTARTS   AGE
trident-csi-788b4d865c-4fs2t         5/5     Running   0          20s
trident-csi-tpxh2                    2/2     Running   0          20s
trident-installer-66dcdf485b-c4rlq   1/1     Running   0          42s
trident-operator-668bf8cdff-n56pd    1/1     Running   0          32s
```

5. Verify the `TridentBackend` exists:
```
$ kubectl get tridentbackends -n trident
NAME        BACKEND     BACKEND UUID
tbe-xt67v   solidfire   9a1ab01e-007a-44b2-bdb6-ef4a2dbc0c48
```

6. Verify the StorageClasses:
```
$ k get storageclasses
NAME               PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
solidfire-bronze   csi.trident.netapp.io   Delete          Immediate           false                  2m40s
solidfire-gold     csi.trident.netapp.io   Delete          Immediate           false                  2m39s
solidfire-silver   csi.trident.netapp.io   Delete          Immediate           false                  2m39s
```

7. Optionally, set one of the trident storageclasses as the default:
```
$ kubectl patch storageclass solidfire-silver -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
storageclass.storage.k8s.io/solidfire-silver patched
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

## Testing Your Installation
1. Create a PVC:
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: task-pv-claim
spec:
  storageClassName: solidfire-silver
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```

2. Create a pod that mounts the PVC as a volume
```
apiVersion: v1
kind: Pod                  
metadata:
  name: task-pv-pod  
spec:
  volumes:                   
    - name: task-pv-storage
      persistentVolumeClaim:
        claimName: task-pv-claim
  containers:
    - name: task-pv-container      
      image: nginx
      ports:
        - containerPort: 80
          name: "http-server"
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: task-pv-storage
```

3. Verify your pod can run:
```
$ kubectl get pods
NAME          READY   STATUS    RESTARTS   AGE
task-pv-pod   1/1     Running   0          18m
```