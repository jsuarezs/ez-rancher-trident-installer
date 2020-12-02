# The Trident Helm Chart

## Add the Repository

```bash
$ helm repo add trident https://raw.githubusercontent.com/NetApp/ez-rancher-trident-installer/master/chart
"trident" has been added to your repositories
```

## Install the chart

```bash
$ helm install trident netapp/ez-rancher-trident-installer -n trident
NAME: trident
LAST DEPLOYED: Mon Sep  7 12:39:21 2020
NAMESPACE: trident
STATUS: deployed
REVISION: 1
TEST SUITE: None
```
