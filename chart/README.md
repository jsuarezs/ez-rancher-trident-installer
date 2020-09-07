## Add the Repository:
```
$ helm repo add trident https://raw.githubusercontent.com/sgryczan/TridentInstaller/dev/chart
"trident" has been added to your repositories
```

## Install the chart:
```
$ helm install trident trident/trident-installer -n trident
NAME: trident
LAST DEPLOYED: Mon Sep  7 12:39:21 2020
NAMESPACE: trident
STATUS: deployed
REVISION: 1
TEST SUITE: None
```