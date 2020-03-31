---
title: Tárolók üzembe helyezése helmtel a Kubernetes-ben az Azure-ban
description: Ismerje meg, hogyan helyezheti üzembe a Helm csomagolási eszközt az Azure Kubernetes-szolgáltatás (AKS) fürtjében lévő tárolók üzembe helyezéséhez
services: container-service
author: zr-msft
ms.topic: article
ms.date: 11/22/2019
ms.author: zarhoads
ms.openlocfilehash: 4a9ccaff0e3425c365a64ecb4fbadf3c7aa8dcfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595178"
---
# <a name="install-applications-with-helm-in-azure-kubernetes-service-aks"></a>Alkalmazások telepítése helmtel az Azure Kubernetes szolgáltatásban (AKS)

[A Helm][helm] egy nyílt forráskódú csomagolási eszköz, amely segít a Kubernetes-alkalmazások életciklusának telepítésében és kezelésében. Hasonló a Linux csomagkezelők, mint az *APT* és *a Yum,* Helm kezelésére használják Kubernetes diagramok, amelyek csomagok előre konfigurált Kubernetes erőforrások.

Ez a cikk bemutatja, hogyan konfigurálhatja és használhatja a Helm egy Kubernetes-fürtben az AKS-en.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AKS-fürttel. Ha AKS-fürtre van szüksége, tekintse meg az AKS [gyorsútmutatót az Azure CLI használatával][aks-quickstart-cli] vagy az Azure Portal [használatával.][aks-quickstart-portal]

Szüksége van a Helm CLI-re is, amely a fejlesztői rendszeren futó ügyfél. Ez lehetővé teszi, hogy indítsa el, állítsa le, és kezelheti alkalmazások Helm. Ha az Azure Cloud Shell, a Helm CLI már telepítve van. A helyi platformon található telepítési utasításokról a [Helm telepítése című][helm-install]témakörben talál.

> [!IMPORTANT]
> A Helm linuxos csomópontokon való futtatásra szolgál. Ha a fürtben Windows Server-csomópontok találhatók, biztosítania kell, hogy a Helm-podok csak Linux-csomópontokon fussanak. Azt is meg kell győződnie arról, hogy a telepített Helm-diagramok is a megfelelő csomópontokon való futtatásra vannak ütemezve. Ebben a cikkben a parancsok [csomópont-választók][k8s-node-selector] segítségével győződjön meg arról, podok vannak ütemezve a megfelelő csomópontok, de nem minden Helm-diagramok kiteheti a csomópont-választó. A fürtön más beállításokat is használhat, például [a taints-t.][taints]

## <a name="verify-your-version-of-helm"></a>A Helm verziójának ellenőrzése

A `helm version` parancs segítségével ellenőrizze a telepített Helm verzióját:

```console
helm version
```

A következő példa a Helm 3.0.0-s verziójával látható:

```console
$ helm version

version.BuildInfo{Version:"v3.0.0", GitCommit:"e29ce2a54e96cd02ccfce88bee4f58bb6e2a28b6", GitTreeState:"clean", GoVersion:"go1.13.4"}
```

A Helm v3 esetében kövesse a [Helm v3 szakasz lépéseit.](#install-an-application-with-helm-v3) A Helm v2 esetében kövesse a [Helm v2 szakasz lépéseit.](#install-an-application-with-helm-v2)

## <a name="install-an-application-with-helm-v3"></a>Alkalmazás telepítése a Helm v3-mal

### <a name="add-the-official-helm-stable-charts-repository"></a>Adja hozzá a hivatalos Helm stabil diagramok tárház

Használja a [helm repo][helm-repo-add] parancsot a hivatalos Helm stabil diagramtár hozzáadásához.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

### <a name="find-helm-charts"></a>Helm-diagramok keresése

A helm diagramok segítségével alkalmazásokat telepíthet egy Kubernetes-fürtbe. Az előre létrehozott Helm-diagramok kereséséhez használja a [helm search][helm-search] parancsot:

```console
helm search repo stable
```

A következő tömörített példa kimenet néhány használható Helm-diagramot mutat be:


```console
$ helm search repo stable

NAME                                    CHART VERSION   APP VERSION                     DESCRIPTION                                       
stable/acs-engine-autoscaler            2.2.2           2.1.1                           DEPRECATED Scales worker nodes within agent pools 
stable/aerospike                        0.3.1           v4.5.0.5                        A Helm chart for Aerospike in Kubernetes          
stable/airflow                          4.10.0          1.10.4                          Airflow is a platform to programmatically autho...
stable/ambassador                       4.4.7           0.85.0                          A Helm chart for Datawire Ambassador              
stable/anchore-engine                   1.3.7           0.5.2                           Anchore container analysis and policy evaluatio...
stable/apm-server                       2.1.5           7.0.0                           The server receives data from the Elastic APM a...
stable/ark                              4.2.2           0.10.2                          DEPRECATED A Helm chart for ark                   
stable/artifactory                      7.3.1           6.1.0                           DEPRECATED Universal Repository Manager support...
stable/artifactory-ha                   0.4.1           6.2.0                           DEPRECATED Universal Repository Manager support...
stable/atlantis                         3.8.4           v0.8.2                          A Helm chart for Atlantis https://www.runatlant...
stable/auditbeat                        1.1.0           6.7.0                           A lightweight shipper to audit the activities o...
stable/aws-cluster-autoscaler           0.3.3                                           Scales worker nodes within autoscaling groups.    
stable/aws-iam-authenticator            0.1.1           1.0                             A Helm chart for aws-iam-authenticator            
stable/bitcoind                         0.2.2           0.17.1                          Bitcoin is an innovative payment network and a ...
stable/bookstack                        1.1.2           0.27.4-1                        BookStack is a simple, self-hosted, easy-to-use...
stable/buildkite                        0.2.4           3                               DEPRECATED Agent for Buildkite                    
stable/burrow                           1.5.2           0.29.0                          Burrow is a permissionable smart contract machine 
stable/centrifugo                       3.1.0           2.1.0                           Centrifugo is a real-time messaging server.       
stable/cerebro                          1.3.1           0.8.5                           A Helm chart for Cerebro - a web admin tool tha...
stable/cert-manager                     v0.6.7          v0.6.2                          A Helm chart for cert-manager                     
stable/chaoskube                        3.1.2           0.14.0                          Chaoskube periodically kills random pods in you...
stable/chartmuseum                      2.4.0           0.8.2                           Host your own Helm Chart Repository               
stable/chronograf                       1.1.0           1.7.12                          Open-source web application written in Go and R...
stable/clamav                           1.0.4           1.6                             An Open-Source antivirus engine for detecting t...
stable/cloudserver                      1.0.3           8.1.5                           An open-source Node.js implementation of the Am...
stable/cluster-autoscaler               6.2.0           1.14.6                          Scales worker nodes within autoscaling groups.    
stable/cluster-overprovisioner          0.2.6           1.0                             Installs the a deployment that overprovisions t...
stable/cockroachdb                      2.1.16          19.1.5                          CockroachDB is a scalable, survivable, strongly...
stable/collabora-code                   1.0.5           4.0.3.1                         A Helm chart for Collabora Office - CODE-Edition  
stable/concourse                        8.2.7           5.6.0                           Concourse is a simple and scalable CI system.     
stable/consul                           3.9.2           1.5.3                           Highly available and distributed service discov...
stable/contour                          0.1.0           v0.15.0                         Contour Ingress controller for Kubernetes         
stable/coredns                          1.7.4           1.6.4                           CoreDNS is a DNS server that chains plugins and...
stable/cosbench                         1.0.1           0.0.6                           A benchmark tool for cloud object storage services
stable/coscale                          1.0.0           3.16.0                          CoScale Agent                                     
stable/couchbase-operator               1.0.1           1.2.1                           A Helm chart to deploy the Couchbase Autonomous...
stable/couchdb                          2.3.0           2.3.1                           DEPRECATED A database featuring seamless multi-...
stable/dask                             3.1.0           1.1.5                           Distributed computation in Python with task sch...
stable/dask-distributed                 2.0.2                                           DEPRECATED: Distributed computation in Python     
stable/datadog                          1.38.3          6.14.0                          DataDog Agent 
...
```

A diagramok listájának frissítéséhez használja a [helm repo update][helm-repo-update] parancsot. A következő példa egy sikeres tárújbóli frissítést mutat be:

```console
$ helm repo update

Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈
```

### <a name="run-helm-charts"></a>Helm-diagramok futtatása

A helm-diagramok telepítéséhez használja a [helm install][helm-install-command] parancsot, és adja meg a telepítendő diagram kiadási nevét és nevét. Helm-diagram telepítése működés közben, telepítsen egy alapvető nginx-telepítést helm diagram használatával.

```console
helm install my-nginx-ingress stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

A következő tömörített példa kimenet a Helm-diagram által létrehozott Kubernetes-erőforrások telepítési állapotát mutatja:

```console
$ helm install my-nginx-ingress stable/nginx-ingress \
>     --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
>     --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME: my-nginx-ingress
LAST DEPLOYED: Fri Nov 22 10:08:06 2019
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The nginx-ingress controller has been installed.
It may take a few minutes for the LoadBalancer IP to be available.
You can watch the status by running 'kubectl --namespace default get services -o wide -w my-nginx-ingress-controller'
...
```

A `kubectl get services` parancs segítségével lejuthat a szolgáltatás *KÜLSŐ-IP-címéhez.* Az alábbi parancs például a *my-nginx-inress-controller* szolgáltatás *KÜLSŐ-IP-címét* jeleníti meg:

```console
$ kubectl --namespace default get services -o wide -w my-nginx-ingress-controller

NAME                          TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
my-nginx-ingress-controller   LoadBalancer   10.0.123.1     <EXTERNAL-IP>   80:31301/TCP,443:31623/TCP   96s   app=nginx-ingress,component=controller,release=my-nginx-ingress
```

### <a name="list-releases"></a>Kiadási kiadások listázása

A fürtre telepített kiadások listájának megtekintéséhez `helm list` használja a parancsot.

```console
helm list
```

A következő példa az előző lépésben üzembe helyezett *my-nginx-inress kiadást* mutatja be:

```console
$ helm list

NAME                NAMESPACE   REVISION    UPDATED                                 STATUS      CHART                   APP VERSION
my-nginx-ingress    default     1           2019-11-22 10:08:06.048477 -0600 CST    deployed    nginx-ingress-1.25.0    0.26.1 
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Helm-diagram telepítésekor számos Kubernetes-erőforrás jön létre. Ezek az erőforrások podok, üzembe helyezések és szolgáltatások. Az erőforrások karbantartásához használja a [helm uninstall][helm-cleanup] parancsot, és adja `helm list` meg a kiadás nevét, ahogy az az előző parancsban található.

```console
helm uninstall my-nginx-ingress
```

A következő példa a *my-nginx-inress* nevű kiadás titulusa:

```console
$ helm uninstall my-nginx-ingress

release "my-nginx-ingress" uninstalled
```

## <a name="install-an-application-with-helm-v2"></a>Alkalmazás telepítése a Helm v2-vel

### <a name="create-a-service-account"></a>Szolgáltatásfiók létrehozása

Mielőtt üzembe helyezheti a Helm-et egy RBAC-kompatibilis AKS-fürtben, szüksége van egy szolgáltatásfiókra és szerepkör-kötésre a Tiller szolgáltatáshoz. A Helm / Tiller RBAC-kompatibilis fürtben való védelméről további információt a [Tiller, a Namespaces és az RBAC című][tiller-rbac]témakörben talál. Ha az AKS-fürt nincs engedélyezve az RBAC-on, hagyja ki ezt a lépést.

Hozzon létre `helm-rbac.yaml` egy elnevezett fájlt, és másolja a következő YAML-fájlba:

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

Hozza létre a szolgáltatásfiókot `kubectl apply` és a szerepkörkötést a következő paranccsal:

```console
kubectl apply -f helm-rbac.yaml
```

### <a name="secure-tiller-and-helm"></a>Biztonságos talajművelő és kormányrúd

A Helm-ügyfél és a Tiller szolgáltatás a TLS/SSL használatával hitelesíti és kommunikálegymással. Ez a hitelesítési módszer segít a Kubernetes-fürt védelmében, és milyen szolgáltatások telepíthetők. A biztonság növelése érdekében létrehozhatja saját aláírt tanúsítványait. Minden Helm-felhasználó saját ügyféltanúsítványt kapna, és a Tiller inicializálva lenne a Kubernetes-fürtben a tanúsítványok alkalmazásával. További információ: [TLS/SSL használata Helm és Tiller között.][helm2-ssl]

Egy RBAC-kompatibilis Kubernetes-fürt, szabályozhatja a hozzáférési szint Tiller a fürthöz. Megadhatja a Kubernetes névteret, amelyben a Tiller telepítve van, és korlátozhatja, hogy a Tiller milyen névterekben helyezhet üzembe erőforrásokat. Ez a megközelítés lehetővé teszi, hogy tiller példányokat hozzon létre a különböző névterekben, és korlátozza a telepítési határokat, és a Helm-ügyfél felhasználóinak hatókörét bizonyos névterekre. További információ: [Helm szerepköralapú hozzáférés-vezérlés.][helm2-rbac]

### <a name="configure-helm"></a>Helm konfigurálása

Egy alap-tiller üzembe helyezéséhez egy AKS-fürt, használja a [helm init][helm2-init] parancsot. Ha a fürt nincs engedélyezve `--service-account` az RBAC- kód, távolítsa el az argumentumot és az értéket. A következő példák a [történelem-max][helm2-history-max] értéket is 200-ra állították.

Ha a TLS/SSL-t tiller hez és helmhez konfigurálta, hagyja ki ezt az alapvető inicializálási lépést, és adja meg a szükséges `--tiller-tls-` et a következő példában látható módon.

```console
helm init --history-max 200 --service-account tiller --node-selectors "beta.kubernetes.io/os=linux"
```

Ha a TLS/SSL-t Helm és `--tiller-tls-*` Tiller között konfigurálta, adja meg a saját tanúsítványainak paramétereit és nevét, ahogy az a következő példában látható:

```console
helm init \
    --tiller-tls \
    --tiller-tls-cert tiller.cert.pem \
    --tiller-tls-key tiller.key.pem \
    --tiller-tls-verify \
    --tls-ca-cert ca.cert.pem \
    --history-max 200 \
    --service-account tiller \
    --node-selectors "beta.kubernetes.io/os=linux"
```

### <a name="find-helm-charts"></a>Helm-diagramok keresése

A helm diagramok segítségével alkalmazásokat telepíthet egy Kubernetes-fürtbe. Az előre létrehozott Helm-diagramok kereséséhez használja a [helm search][helm2-search] parancsot:

```console
helm search
```

A következő tömörített példa kimenet néhány használható Helm-diagramot mutat be:

```
$ helm search

NAME                           CHART VERSION    APP VERSION  DESCRIPTION
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

A diagramok listájának frissítéséhez használja a [helm repo update][helm2-repo-update] parancsot. A következő példa egy sikeres tárújbóli frissítést mutat be:

```console
$ helm repo update

Hold tight while we grab the latest from your chart repositories...
...Skip local chart repository
...Successfully got an update from the "stable" chart repository
Update Complete.
```

### <a name="run-helm-charts"></a>Helm-diagramok futtatása

A helm-diagramok telepítéséhez használja a [helm install][helm2-install-command] parancsot, és adja meg a telepítendő diagram nevét. Helm-diagram telepítése működés közben, telepítsen egy alapvető nginx-telepítést helm diagram használatával. Ha a TLS/SSL konfigurálta, adja hozzá a paramétert a `--tls` Helm ügyféltanúsítvány használatához.

```console
helm install stable/nginx-ingress \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

A következő tömörített példa kimenet a Helm-diagram által létrehozott Kubernetes-erőforrások telepítési állapotát mutatja:

```
$ helm install stable/nginx-ingress --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux

NAME:   flailing-alpaca
LAST DEPLOYED: Thu May 23 12:55:21 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ConfigMap
NAME                                      DATA  AGE
flailing-alpaca-nginx-ingress-controller  1     0s

==> v1/Pod(related)
NAME                                                            READY  STATUS             RESTARTS  AGE
flailing-alpaca-nginx-ingress-controller-56666dfd9f-bq4cl       0/1    ContainerCreating  0         0s
flailing-alpaca-nginx-ingress-default-backend-66bc89dc44-m87bp  0/1    ContainerCreating  0         0s

==> v1/Service
NAME                                           TYPE          CLUSTER-IP  EXTERNAL-IP  PORT(S)                     AGE
flailing-alpaca-nginx-ingress-controller       LoadBalancer  10.0.109.7  <pending>    80:31219/TCP,443:32421/TCP  0s
flailing-alpaca-nginx-ingress-default-backend  ClusterIP     10.0.44.97  <none>       80/TCP                      0s
...
```

A nginx-inress-controller szolgáltatás *KÜLSŐ-IP-címének* feltöltése egy-két percet vesz igénybe, és lehetővé teszi, hogy webböngészővel férjen hozzá.

### <a name="list-helm-releases"></a>List Helm kiadások

A fürtre telepített kiadások listájának megtekintéséhez használja a [helm list][helm2-list] parancsot. A következő példa az előző lépésben üzembe helyezett nginx-intheskiadást mutatja be. Ha a TLS/SSL konfigurálta, adja hozzá a paramétert a `--tls` Helm ügyféltanúsítvány használatához.

```console
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                 APP VERSION   NAMESPACE
flailing-alpaca   1         Thu May 23 12:55:21 2019    DEPLOYED    nginx-ingress-1.6.13    0.24.1      default
```

### <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Helm-diagram telepítésekor számos Kubernetes-erőforrás jön létre. Ezek az erőforrások podok, üzembe helyezések és szolgáltatások. Az erőforrások karbantartásához használja `helm delete` a parancsot, és adja meg `helm list` a kiadás nevét az előző parancsban található módon. A következő példa törli a *flailing-alpaca*nevű kiadást:

```console
$ helm delete flailing-alpaca

release "flailing-alpaca" deleted
```

## <a name="next-steps"></a>További lépések

A Kubernetes-alkalmazások központi telepítéseinek helmtel való kezeléséről a Helm dokumentációjában olvashat bővebben.

> [!div class="nextstepaction"]
> [Helm dokumentáció][helm-documentation]

<!-- LINKS - external -->
[helm]: https://github.com/kubernetes/helm/
[helm-cleanup]: https://helm.sh/docs/intro/using_helm/#helm-uninstall-uninstalling-a-release
[helm-documentation]: https://helm.sh/docs/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-command]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[helm-repo-add]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helm-search]: https://helm.sh/docs/intro/using_helm/#helm-search-finding-charts
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm2-init]: https://v2.helm.sh/docs/helm/#helm-init
[helm2-install-command]: https://v2.helm.sh/docs/helm/#helm-install
[helm2-install-options]: https://github.com/kubernetes/helm/blob/master/docs/install.md
[helm2-list]: https://v2.helm.sh/docs/helm/#helm-list
[helm2-history-max]: https://v2.helm.sh/docs/using_helm/#initialize-helm-and-install-tiller
[helm2-rbac]: https://v2.helm.sh/docs/using_helm/#role-based-access-control
[helm2-repo-update]: https://v2.helm.sh/docs/helm/#helm-repo-update
[helm2-search]: https://v2.helm.sh/docs/helm/#helm-search
[tiller-rbac]: https://v2.helm.sh/docs/using_helm/#tiller-namespaces-and-rbac
[helm2-ssl]: https://v2.helm.sh/docs/using_helm/#using-ssl-between-helm-and-tiller
            
<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
[taints]: operator-best-practices-advanced-scheduler.md
