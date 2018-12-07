---
title: Az Azure-beli Kubernetes Helm-tárolók üzembe helyezése
description: Tárolók az Azure Kubernetes Service (AKS)-fürt üzembe helyezése a Helm csomagolás eszközzel
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/01/2018
ms.author: iainfou
ms.openlocfilehash: f9f4c1cccac7e40c7d2fd8f76221bc1870ade45f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993240"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Alkalmazások telepítése a Helm használatával az Azure Kubernetes Service (AKS)

[Helm] [ helm] egy nyílt forráskódú csomagolás eszköz, amely segít telepítése és a Kubernetes-alkalmazások életciklusának kezelését. Például a Linux csomagkezelők hasonló *APT* és *Yum*, Helm Kubernetes diagramok, amelyek-csomagok előre konfigurált Kubernetes-erőforrások kezelésére használható.

Ez a cikk bemutatja, hogyan konfigurálhatja és használhatja a Helm a Kubernetes-fürtben az aks-en.

## <a name="before-you-begin"></a>Előkészületek

Ebben a dokumentumban ismertetett lépések feltételezik, hogy már létrehozott egy AKS-fürtöt, és létrehozott egy `kubectl` kapcsolatot a fürttel. Ha ezeket az elemeket kell jelenik meg, a [AKS gyors][aks-quickstart].

A Helm CLI telepítve van, az ügyfél, amely a fejlesztői rendszeren fut, és lehetővé teszi, hogy indítása, leállítása és felügyelje alkalmazásait, a Helm használatával is szükséges. Ha az Azure Cloud Shellt használja, a Helm CLI már telepítve van. A témakör a telepítési utasításokat a helyi platformon, [telepítése Helm][helm-install].

## <a name="create-a-service-account"></a>Szolgáltatásfiók létrehozása

Az RBAC-kompatibilis AKS-fürt telepítése Helm, előtt szükség van a szolgáltatásfiók és a szerepkör kötést a tiller valóban szolgáltatás. További információ biztonságossá tenni a Helm / az RBAC a tiller valóban engedélyezve van a fürtöt, tekintse meg [a tiller valóban, a névterek és az RBAC][tiller-rbac]. Ha az AKS-fürt nem RBAC engedélyezve van, hagyja ki ezt a lépést.

Hozzon létre egy fájlt `helm-rbac.yaml` másolja be a következő yaml-kódot:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
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

A szolgáltatásfiók és a szerepkör-kötés létrehozása a `kubectl apply` parancsot:

```console
kubectl apply -f helm-rbac.yaml
```

## <a name="secure-tiller-and-helm"></a>A tiller valóban és a Helm védelmének biztosítása

A Helm-ügyfél és a tiller valóban szolgáltatás hitelesítéséhez és egymással a TLS/SSL használatával kommunikálni. Ez a hitelesítési módszer segít biztosítani a Kubernetes-fürt, és pontosan milyen szolgáltatásokat is üzembe helyezhetők. A biztonság növelése érdekében a saját önaláírt tanúsítványokat is létrehozhat. Minden egyes Helm felhasználó lenne megkapja a saját ügyféltanúsítvány, és a tiller valóban szeretné inicializálni a Kubernetes-fürt alkalmazott tanúsítványokkal. További információkért lásd: [TLS/SSL használatával Helm és a tiller valóban között][helm-ssl].

Az RBAC-kompatibilis Kubernetes-fürttel szabályozhatja a tiller valóban rendelkezik a fürthöz hozzáférési szintjét. Adja meg a tiller valóban üzembe helyezett Kubernetes-névtér, és milyen a tiller valóban Ezután telepítheti az erőforrások névterek korlátozása. Ez a megközelítés lehetővé teszi a Tiller-példányok létrehozásához a különböző névterekben és korlát telepítési határok, és az egyes névterek Helm-ügyfél felhasználói hatókör. További információkért lásd: [szerepköralapú hozzáférés-vezérlés Helm][helm-rbac].

## <a name="configure-helm"></a>Helm konfigurálása

Egy alapszintű tiller valóban az AKS-fürt üzembe helyezéséhez használja a [helm init] [ helm-init] parancsot. Ha a fürt nem RBAC engedélyezve, távolítsa el a `--service-account` argumentum és értékét. Ha konfigurálta a TLS/SSL a tiller valóban és a Helm, ez a alapvető alkalmazásinicializálási lépéshez ugorjon, és Ehelyett adja meg a szükséges `--tiller-tls-` a következő példában látható módon.

```console
helm init --service-account tiller
```

Ha konfigurálta a TLS/SSL Helm és a tiller valóban között adja meg a `--tiller-tls-*` paramétereket és a saját tanúsítványok, az alábbi példában látható módon nevei:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --service-account tiller
```

## <a name="find-helm-charts"></a>Keresse meg a Helm-diagramok

Helm-diagramok segítségével Kubernetes-fürthöz az alkalmazások központi telepítése. Előre létrehozott Helm-diagramokat keres, használja a [helm keresési] [ helm-search] parancsot:

```console
helm search
```

A következő sűrített példához kimenetet mutat be néhányat, a Helm-diagramok használható:

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
stable/aks-engine-autoscaler   2.2.0            2.1.1        Scales worker nodes within agent pools
stable/aerospike               0.1.7            v3.14.1.2    A Helm chart for Aerospike in Kubernetes
stable/anchore-engine          0.1.7            0.1.10       Anchore container analysis and policy evaluatio...
stable/apm-server              0.1.0            6.2.4        The server receives data from the Elastic APM a...
stable/ark                     1.0.1            0.8.2        A Helm chart for ark
stable/artifactory             7.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/artifactory-ha          0.2.1            6.0.0        Universal Repository Manager supporting all maj...
stable/auditbeat               0.1.0            6.2.4        A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler  0.3.3                         Scales worker nodes within autoscaling groups.
stable/bitcoind                0.1.3            0.15.1       Bitcoin is an innovative payment network and a ...
stable/buildkite               0.2.3            3            Agent for Buildkite
stable/burrow                  0.4.4            0.17.1       Burrow is a permissionable smart contract machine
stable/centrifugo              2.0.1            1.7.3        Centrifugo is a real-time messaging server.
stable/cerebro                 0.1.0            0.7.3        A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager            v0.3.3           v0.3.1       A Helm chart for cert-manager
stable/chaoskube               0.7.0            0.8.0        Chaoskube periodically kills random pods in you...
stable/chartmuseum             1.5.0            0.7.0        Helm Chart Repository with support for Amazon S...
stable/chronograf              0.4.5            1.3          Open-source web application written in Go and R...
stable/cluster-autoscaler      0.6.4            1.2.2        Scales worker nodes within autoscaling groups.
stable/cockroachdb             1.1.1            2.0.0        CockroachDB is a scalable, survivable, strongly...
stable/concourse               1.10.1           3.14.1       Concourse is a simple and scalable CI system.
stable/consul                  3.2.0            1.0.0        Highly available and distributed service discov...
stable/coredns                 0.9.0            1.0.6        CoreDNS is a DNS server that chains plugins and...
stable/coscale                 0.2.1            3.9.1        CoScale Agent
stable/dask                    1.0.4            0.17.4       Distributed computation in Python with task sch...
stable/dask-distributed        2.0.2                         DEPRECATED: Distributed computation in Python
stable/datadog                 0.18.0           6.3.0        DataDog Agent
...
```

A diagramok listájának frissítéséhez használja a [helm-tárház frissítési] [ helm-repo-update] parancsot. Az alábbi példa bemutatja egy tárház sikeres frissítés:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

## <a name="run-helm-charts"></a>Futtassa a Helm-diagramok

Helm-diagramok telepítéséhez használja a [helm install] [ helm-install] parancsot, majd adja meg a nevét, a diagram telepítéséhez. Ez a művelet látható, hozzunk telepítse egy Helm-diagram használatával alapszintű Wordpress üzembe helyezéséhez. Ha a TLS/SSL konfigurálta, vegye fel a `--tls` paraméter a Helm ügyféltanúsítvány használatára.

```console
helm install stable/wordpress
```

A következő sűrített példához kimenet a Kubernetes-erőforrást a Helm-diagram által létrehozott központi telepítési állapotát jeleníti meg:

```
$ helm install stable/wordpress

NAME:   wishful-mastiff
LAST DEPLOYED: Thu Jul 12 15:53:56 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1beta1/Deployment
NAME                       DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
wishful-mastiff-wordpress  1        1        1           0          1s

==> v1beta1/StatefulSet
NAME                     DESIRED  CURRENT  AGE
wishful-mastiff-mariadb  1        1        1s

==> v1/Pod(related)
NAME                                        READY  STATUS   RESTARTS  AGE
wishful-mastiff-wordpress-6f96f8fdf9-q84sz  0/1    Pending  0         1s
wishful-mastiff-mariadb-0                   0/1    Pending  0         1s

==> v1/Secret
NAME                       TYPE    DATA  AGE
wishful-mastiff-mariadb    Opaque  2     2s
wishful-mastiff-wordpress  Opaque  2     2s

==> v1/ConfigMap
NAME                           DATA  AGE
wishful-mastiff-mariadb        1     2s
wishful-mastiff-mariadb-tests  1     2s

==> v1/PersistentVolumeClaim
NAME                       STATUS   VOLUME   CAPACITY  ACCESS MODES  STORAGECLASS  AGE
wishful-mastiff-wordpress  Pending  default  2s

==> v1/Service
NAME                       TYPE          CLUSTER-IP   EXTERNAL-IP  PORT(S)                     AGE
wishful-mastiff-mariadb    ClusterIP     10.1.116.54  <none>       3306/TCP                    2s
wishful-mastiff-wordpress  LoadBalancer  10.1.217.64  <pending>    80:31751/TCP,443:31264/TCP  2s
...
```

Egy-a két percet vesz igénybe a *EXTERNAL-IP* kitöltődnek, és lehetővé teszi, hogy egy webböngészővel rendelkező férni a Wordpress szolgáltatás-címét.

## <a name="list-helm-releases"></a>A Helm List-kiadások

Kiadásokban a fürtön telepíteni listájának megtekintéséhez használja a [helm list] [ helm-list] parancsot. Az alábbi példa bemutatja az előző lépésben üzembe helyezett Wordpress kiadását. Ha a TLS/SSL konfigurálta, vegye fel a `--tls` paraméter a Helm ügyféltanúsítvány használatára.

```console
$ helm list

NAME             REVISION    UPDATED                     STATUS      CHART              NAMESPACE
wishful-mastiff  1           Thu Jul 12 15:53:56 2018    DEPLOYED    wordpress-2.1.3  default
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor telepít egy Helm-diagram, egy Kubernetes-erőforrások száma jönnek létre. Ezeket az erőforrásokat podok, központi telepítések és szolgáltatásokat tartalmazza. Ezek az erőforrások törléséhez használja a `helm delete` parancsot, majd írja be a kiadás nevét, az előző következőbeli `helm list` parancsot. Az alábbi példával törölhet a kiadás nevű *wishful mastiff*:

```console
$ helm delete wishful-mastiff

release "wishful-mastiff" deleted
```

## <a name="next-steps"></a>További lépések

Alkalmazástelepítések Kubernetes Helm-kezelésével kapcsolatos további információkért a Helm-dokumentációjában talál.

> [!div class="nextstepaction"]
> [Helm-dokumentáció][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-documentation]: https://docs.helm.sh/
[helm-init]: https://docs.helm.sh/helm/#helm-init
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[helm-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm-list]: https://docs.helm.sh/helm/#helm-list
[helm-rbac]: https://docs.helm.sh/using_helm/#role-based-access-control
[helm-repo-update]: https://docs.helm.sh/helm/#helm-repo-update
[helm-search]: https://docs.helm.sh/helm/#helm-search
[tiller-rbac]: https://docs.helm.sh/using_helm/#tiller-namespaces-and-rbac
[helm-ssl]: https://docs.helm.sh/using_helm/#using-ssl-between-helm-and-tiller

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
