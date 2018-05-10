---
title: Az Azure-on Kubernetes Helm a tároló üzembe helyezése
description: A Helm csomagolás eszköz segítségével Kubernetes gazdagépfürtökön AKS a tároló üzembe helyezése
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/24/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: c46bd64b3fec06e4ba4050542f27ba3e70862e45
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="use-helm-with-azure-kubernetes-service-aks"></a>Az Azure Kubernetes szolgáltatáshoz (AKS) Helm használható

[Helm] [ helm] nyílt forráskódú csomagolás eszköz, amely segít telepíteni, és Kubernetes alkalmazások életciklusának kezelését. Például a Linux-csomag kezelői hasonló *APT* és *Yum*, Helm Kubernetes diagramok, amelyek a csomagok előre konfigurált Kubernetes erőforrások kezelésére szolgál.

Ez a dokumentum lépéseit konfigurálásával és használatával Helm AKS Kubernetes fürtben.

## <a name="before-you-begin"></a>Előkészületek

A dokumentumban foglalt lépések feltételezik, hogy korábban már létrehozott egy AKS-fürtöt, és kiépített egy kubectl-kapcsolatot a fürttel. Ha ezek az elemek megjelenítéséhez a [AKS gyors üzembe helyezés][aks-quickstart].

## <a name="install-helm-cli"></a>Helm parancssori felület telepítése

A Helm CLI ügyfél, amely a fejlesztői rendszeren fut, és lehetővé teszi indítása, leállítása és kezelheti az alkalmazásokat az Helm diagramok.

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

## <a name="configure-helm"></a>Helm konfigurálása

A [helm init] [ helm-init] parancs segítségével Helm összetevőinek telepítése Kubernetes fürtben, és az ügyféloldali konfigurációkat. A következő parancsot a AKS fürtön telepíteni a Helm, és a Helm ügyfél konfigurálásához.

```azurecli-interactive
helm init
```

Kimenet:

```
$HELM_HOME has been configured at /Users/neilpeterson/.helm.

Tiller (the Helm server-side component) has been installed into your Kubernetes Cluster.

Please note: by default, Tiller is deployed with an insecure 'allow unauthenticated users' policy.
For more information on securing your installation see: https://docs.helm.sh/using_helm/#securing-your-helm-installation
Happy Helming!
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

Egy NGINX érkező tartományvezérlő telepítéséhez használja a [helm telepítés] [ helm-install] parancsot.

```azurecli-interactive
helm install stable/nginx-ingress
```

A kimenet az alábbihoz hasonló, de tartoznak a további utasításokat a Kubernetes központi telepítés használatával.

```
NAME:   tufted-ocelot
LAST DEPLOYED: Thu Oct  5 00:48:04 2017
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                    DATA  AGE
tufted-ocelot-nginx-ingress-controller  1     5s

==> v1/Service
NAME                                         CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
tufted-ocelot-nginx-ingress-controller       10.0.140.10  <pending>    80:30486/TCP,443:31358/TCP  5s
tufted-ocelot-nginx-ingress-default-backend  10.0.34.132  <none>       80/TCP                      5s

==> v1beta1/Deployment
NAME                                         DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
tufted-ocelot-nginx-ingress-controller       1        1        1           0          5s
tufted-ocelot-nginx-ingress-default-backend  1        1        1           1          5s
...
```

További tájékoztatást az NGINX érkező vezérlőhöz Kubernetes, lásd: [NGINX érkező vezérlő][nginx-ingress].

## <a name="list-helm-charts"></a>Lista Helm diagramok

A fürtön telepítve diagramok listájának megtekintéséhez használja a [helm lista] [ helm-list] parancsot.

```azurecli-interactive
helm list
```

Kimenet:

```
NAME            REVISION    UPDATED                     STATUS      CHART               NAMESPACE
bilging-ant     1           Thu Oct  5 00:11:11 2017    DEPLOYED    nginx-ingress-0.8.7 default
```

## <a name="next-steps"></a>További lépések

Kubernetes diagramok kezelésével kapcsolatos további információkért a Helm dokumentációjában talál.

> [!div class="nextstepaction"]
> [Helm dokumentáció][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://github.com/kubernetes/helm/blob/master/docs/index.md
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/helm/#helm-install
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md