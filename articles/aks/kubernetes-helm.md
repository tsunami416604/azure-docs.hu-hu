---
title: Az Azure-on Kubernetes Helm a tároló üzembe helyezése
description: A Helm csomagolás eszköz segítségével Kubernetes gazdagépfürtökön AKS a tároló üzembe helyezése
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/13/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 531e6d9368b2bf91c48fd41b1e9330879b0df49a
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37102285"
---
# <a name="use-helm-with-azure-kubernetes-service-aks"></a>Az Azure Kubernetes szolgáltatáshoz (AKS) Helm használható

[Helm] [ helm] nyílt forráskódú csomagolás eszköz, amely segít telepíteni, és Kubernetes alkalmazások életciklusának kezelését. Például a Linux-csomag kezelői hasonló *APT* és *Yum*, Helm Kubernetes diagramok, amelyek a csomagok előre konfigurált Kubernetes erőforrások kezelésére szolgál.

Ez a dokumentum lépéseit konfigurálásával és használatával Helm AKS Kubernetes fürtben.

## <a name="before-you-begin"></a>Előkészületek

A dokumentumban foglalt lépések feltételezik, hogy korábban már létrehozott egy AKS-fürtöt, és kiépített egy kubectl-kapcsolatot a fürttel. Ha ezek az elemek megjelenítéséhez a [AKS gyors üzembe helyezés][aks-quickstart].

## <a name="install-helm-cli"></a>Helm parancssori felület telepítése

A Helm CLI ügyfél, amely a fejlesztői rendszeren fut, és lehetővé teszi indítása, leállítása és kezelheti az alkalmazásokat az Helm.

Ha Azure CloudShell használata esetén a Helm CLI már telepítve van. A Mac használja a Helm CLI telepítendő `brew`. A telepítési beállítások lásd [telepítése Helm][helm-install-options].

```console
brew install kubernetes-helm
```

Kimenet:

```
==> Downloading https://homebrew.bintray.com/bottles/kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
######################################################################## 100.0%
==> Pouring kubernetes-helm-2.6.2.sierra.bottle.1.tar.gz
==> Caveats
Bash completion has been installed to:
  /usr/local/etc/bash_completion.d
==> Summary
🍺  /usr/local/Cellar/kubernetes-helm/2.6.2: 50 files, 132.4MB
```

## <a name="create-service-account"></a>Szolgáltatás-fiók létrehozása

Fürt Helm konfigurálása az RBAC a engedélyezve van, meg kell a szolgáltatásfiók és a kötés a kormányrúd szolgáltatás szerepkör. Helm védelmével kapcsolatos további információ / kormányrúd az RBAC a fürt engedélyezve van, lásd: [kormányrúd, a névterek és az RBAC][tiller-rbac]. Vegye figyelembe, ha a fürt nem RBAC engedélyezve van, hagyja ki ezt a lépést.

Hozzon létre egy fájlt `helm-rbac.yaml` és a következő YAM másolja.

```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
```

A szolgáltatásfiók és a kötés szerepkör létrehozása a `kubectl create` parancsot.

```
kubectl create -f helm-rbac.yaml
```

Ha az RBAC használata engedélyezve van a fürt, közül a fürthöz tartozik kormányrúd hozzáférési szintet. Lásd: [Helm: szerepköralapú hozzáférés-vezérlést] [ helm-rbac] konfigurációs beállításokról további információt.

## <a name="configure-helm"></a>Helm konfigurálása

Most már a kormányrúd használatával telepítse a [helm init] [ helm-init] parancsot. Ha a fürt nem RBAC engedélyezve van, távolítsa el a `--service-account` argumentum és értékét.

```
helm init --service-account tiller
```

## <a name="find-helm-charts"></a>Helm diagramok keresése

Helm Kubernetes fürtbe alkalmazások központi telepítéséhez jól használható. Keresse meg a korábban létrehozott Helm diagramok, használja a [helm keresési] [ helm-search] parancsot.

```azurecli-interactive
helm search
```

A kimeneti láthatóhoz hasonló, azonban a legtöbb több diagramokat.

```
NAME                            VERSION DESCRIPTION
stable/acs-engine-autoscaler    2.0.0   Scales worker nodes within agent pools
stable/artifactory              6.1.0   Universal Repository Manager supporting all maj...
stable/aws-cluster-autoscaler   0.3.1   Scales worker nodes within autoscaling groups.
stable/buildkite                0.2.0   Agent for Buildkite
stable/centrifugo               2.0.0   Centrifugo is a real-time messaging server.
stable/chaoskube                0.5.0   Chaoskube periodically kills random pods in you...
stable/chronograf               0.3.0   Open-source web application written in Go and R...
stable/cluster-autoscaler       0.2.0   Scales worker nodes within autoscaling groups.
stable/cockroachdb              0.5.0   CockroachDB is a scalable, survivable, strongly...
stable/concourse                0.7.0   Concourse is a simple and scalable CI system.
stable/consul                   0.4.1   Highly available and distributed service discov...
stable/coredns                  0.5.0   CoreDNS is a DNS server that chains middleware ...
stable/coscale                  0.2.0   CoScale Agent
stable/dask-distributed         2.0.0   Distributed computation in Python
stable/datadog                  0.8.0   DataDog Agent
...
```

Frissítse a diagramok listáját, használja a [helm tárház frissítési] [ helm-repo-update] parancsot.

```azurecli-interactive
helm repo update
```

Kimenet:

```
Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Helm diagramok futtatása

Wordpress Helm diagram használatával történő központi telepítéséhez használja a [helm telepítés] [ helm-install] parancsot.

```azurecli-interactive
helm install stable/wordpress
```

A kimenet az alábbihoz hasonló, de tartoznak a további utasításokat a Kubernetes központi telepítés használatával.

```
NAME:   bilging-ibex
LAST DEPLOYED: Tue Jun  5 14:31:49 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                     READY  STATUS   RESTARTS  AGE
bilging-ibex-mariadb-7557b5474-dmdxn     0/1    Pending  0         1s
bilging-ibex-wordpress-7494c545fb-tskhz  0/1    Pending  0         1s

==> v1/Secret
NAME                    TYPE    DATA  AGE
bilging-ibex-mariadb    Opaque  2     1s
bilging-ibex-wordpress  Opaque  2     1s

==> v1/ConfigMap
NAME                        DATA  AGE
bilging-ibex-mariadb        1     1s
bilging-ibex-mariadb-tests  1     1s

==> v1/PersistentVolumeClaim
NAME                    STATUS   VOLUME   CAPACITY  ACCESS MODES  STORAGECLASS  AGE
bilging-ibex-mariadb    Pending  default  1s
bilging-ibex-wordpress  Pending  default  1s

==> v1/Service
NAME                    TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)                     AGE
bilging-ibex-mariadb    ClusterIP     10.0.76.164   <none>       3306/TCP                    1s
bilging-ibex-wordpress  LoadBalancer  10.0.215.250  <pending>    80:30934/TCP,443:31134/TCP  1s

==> v1beta1/Deployment
NAME                    DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
bilging-ibex-mariadb    1        1        1           0          1s
bilging-ibex-wordpress  1        1        1           0          1s
...
```

## <a name="list-helm-releases"></a>Lista Helm kiadások

A fürtön telepítve kiadásokban listájának megtekintéséhez használja a [helm lista] [ helm-list] parancsot.

```azurecli-interactive
helm list
```

Kimenet:

```
NAME            REVISION    UPDATED                     STATUS      CHART           NAMESPACE
bilging-ibex    1           Tue Jun  5 14:31:49 2018    DEPLOYED    wordpress-1.0.9 default
```

## <a name="next-steps"></a>További lépések

Kubernetes diagramok kezelésével kapcsolatos további információkért a Helm dokumentációjában talál.

> [!div class="nextstepaction"]
> [Helm dokumentáció][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/helm/#helm-install
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
