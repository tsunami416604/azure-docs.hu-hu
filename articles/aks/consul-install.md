---
title: A Hashicorp Consul telepítése az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan telepítheti és használhatja a Konzulot egy Service Mesh Azure Kubernetes-szolgáltatásbeli (ak-) fürtön való létrehozásához
services: container-service
author: dstrebel
ms.service: container-service
ms.topic: article
ms.date: 06/19/2019
ms.author: dastrebe
ms.openlocfilehash: 7acd2533079499091427c7e63741b9c587ee29e5
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70189223"
---
# <a name="install-and-use-consul-connect-in-azure-kubernetes-service-aks"></a>A konzuli kapcsolat telepítése és használata az Azure Kubernetes szolgáltatásban (ak)

A [konzul][consul-github] egy nyílt forráskódú szolgáltatás rácsvonala, amely a Kubernetes-fürtben található összes szolgáltatásra kiterjedő funkciókat biztosít. Ezek a szolgáltatások közé tartoznak a szolgáltatások felderítése, az állapot-ellenőrzés, a szolgáltatások szegmentálása és a megfigyelés. További információ a Konzulról: a [konzul?][consul-docs-concepts] dokumentációja.

Ez a cikk bemutatja, hogyan telepítheti a konzult. A konzuli összetevők egy Kubernetes-fürtre vannak telepítve az AK-ban.

> [!NOTE]
> Ezek az utasítások a konzuli verzióra `1.5`hivatkoznak.
>
> A konzuli `1.5.x` kiadásokat a Hashicorp csapat tesztelte a Kubernetes `1.14`verzióin `1.12` `1.14`,. További konzuli verziók a [GitHub-konzuli][consul-github-releases] kiadásokban és az egyes kiadásokra vonatkozó információk a [Consul-kibocsátási megjegyzésekben][consul-release-notes]című témakörben találhatók.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A konzuli összetevők telepítése az AK-on
> * A konzul telepítésének ellenőrzése
> * Konzul eltávolítása az AK-ból

## <a name="before-you-begin"></a>Előkészületek

A cikkben részletezett lépések azt feltételezik, hogy létrehozott egy AK-fürtöt `1.11` (Kubernetes és újabb, RBAC engedélyezve), és létesítettek egy `kubectl` , a fürttel létesített kapcsolatokat. Ha segítségre van szüksége ezen elemek bármelyikével kapcsolatban, tekintse meg az [AK][aks-quickstart]gyors üzembe helyezését ismertető cikket.

Az utasítások követéséhez és a konzul telepítéséhez a [Helm][helm] szükséges. Javasoljuk, hogy a fürtben megfelelően `2.12.2` telepítse és konfigurálja a verziót. Ha segítségre van szüksége a Helm telepítéséhez, tekintse meg az [AK Helm telepítési útmutatóját][helm-install]. Az összes konzuli hüvelyt is be kell ütemezni a Linux-csomópontokon történő futtatásra.

Ez a cikk a konzuli telepítési útmutatót több különálló lépésben választja el. A végeredmény ugyanaz, mint a hivatalos konzul telepítési [útmutatója][consul-install-k8].

### <a name="install-the-consul-components-on-aks"></a>A konzuli összetevők telepítése az AK-on

Először a Kubernetes GitHub-tárházban található hivatalos HashiCorp-Konzulot fogjuk bevezetni.

```bash
git clone https://github.com/hashicorp/consul-helm.git
cd consul-helm
```

Ezután létre kell hoznia egy egyéni Helm Values fájlt a konzul telepítéséhez. A konzul telepítése előtt hozza létre a következő egyéni értékeket tartalmazó fájlt.

```bash
vim consul-values.yaml
```

Ezután másolja és illessze be a következő értékeket a Consul-Values. YAML fájlba.

```yaml
# Sets datacenter name and version Consul to use
global:
  datacenter: dc1
  image: "consul:1.5.2"

# Enables proxies to be injected into pods
connectInject:
  enabled: true

# Enables UI on ClusterIP
ui:
  service:
    type: "ClusterIP"

# Enables GRCP that is required for connectInject
client:
  enabled: true
  grpc: true

# Sets replicase to 3 for HA
server:
  replicas: 3
  bootstrapExpect: 1
  disruptionBudget:
    enabled: true
    maxUnavailable: 1

# Syncs Kubernetes service discovery with Consul
syncCatalog:
  enabled: true
```

Most, hogy rendelkezünk az egyéni értékek fájllal, a Konzulot az AK-fürtbe is telepíthetjük

Bash

```bash
helm install -f consul-values.yaml --name consul --namespace consul .
```
A `Consul` Helm diagram több objektumot helyez üzembe. A listát a fenti `helm install` parancs kimenetében tekintheti meg. A konzuli összetevők üzembe helyezése 4 – 5 percet is igénybe vehet, a fürt környezetéből függően.

> [!NOTE]
> Az összes konzuli hüvelyt Linux-csomópontokon kell futtatni. Ha a fürtön a Linux-csomópontok készletei mellett Windows Server-csomópont-készletek is vannak, ellenőrizze, hogy az összes konzuli hüvely a Linux-csomópontokon való futtatásra van-e ütemezve.

Ekkor központilag telepítette a konzult az AK-fürtbe. A konzul sikeres üzembe helyezésének biztosítása érdekében térjünk át a következő szakaszra a konzul telepítésének ellenőrzéséhez.

## <a name="validate-the-consul-installation"></a>A konzul telepítésének ellenőrzése

Először ellenőrizze, hogy létrejöttek-e a várt szolgáltatások. A futó szolgáltatások megtekintéséhez használja az [kubectl Get SVC][kubectl-get] parancsot. Kérdezze le `consul` a névteret, ahol a konzuli összetevőket a `consul` Helm diagram telepítette:

```console
kubectl get svc --namespace consul
```

Az alábbi példában szereplő kimenet a következő szolgáltatásokat mutatja be:

```console
NAME                                 TYPE           CLUSTER-IP     EXTERNAL-IP             PORT(S)                                                                   AGE
consul                               ExternalName   <none>         consul.service.consul   <none>                                                                    38m
consul-consul-connect-injector-svc   ClusterIP      10.0.89.113    <none>                  443/TCP                                                                   40m
consul-consul-dns                    ClusterIP      10.0.166.82    <none>                  53/TCP,53/UDP                                                             40m
consul-consul-server                 ClusterIP      None           <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   40m
consul-consul-ui                     ClusterIP      10.0.117.164   <none>                  80/TCP                                                                    40m
```

Ezután győződjön meg arról, hogy a szükséges hüvelyek létre lettek hozva. Használja a [kubectl Get hüvely][kubectl-get] parancsot, és ismételje meg `consul` a névterek lekérdezését:

```console
kubectl get pods --namespace consul
```

A következő példa kimenete az alábbi, a-t futtató hüvelyeket mutatja:

```console
NAME                                                              READY   STATUS    RESTARTS   AGE
consul-consul-7cc9v                                               1/1     Running   0          37m
consul-consul-7klg7                                               1/1     Running   0          37m
consul-consul-connect-injector-webhook-deployment-57f88df8hgmfs   1/1     Running   0          37m
consul-consul-lq8qb                                               1/1     Running   0          37m
consul-consul-server-0                                            1/1     Running   0          37m
consul-consul-server-1                                            1/1     Running   0          36m
consul-consul-server-2                                            1/1     Running   0          36m
consul-consul-sync-catalog-7cf7d5bfff-jjbjv                       1/1     Running   2          37m
```

 Az összes hüvelynek állapota a `Running`következő:. Ha a hüvelye nem rendelkezik ezekkel az állapotokkal, várjon egy percet vagy kettőt, amíg meg nem történik. Ha bármelyik hüvely hibát jelez, használja a [kubectl leírását a pod][kubectl-describe] paranccsal a kimenet és az állapot áttekintéséhez.

## <a name="accessing-the-consul-ui"></a>A konzul felhasználói felületének elérése

A konzul felhasználói felületét a fenti, a konzul felhasználói FELÜLETén alapuló konfigurációt biztosító telepítője telepítette. A konzul felhasználói felülete nem nyilvánosan elérhető külső IP-címen keresztül. A kiegészítő felhasználói felületek eléréséhez használja a [kubectl Port-Forward][kubectl-port-forward] parancsot. Ez a parancs biztonságos kapcsolatot hoz létre az ügyfélszámítógép és a megfelelő Pod-kapcsolat között az AK-fürtben.

```bash
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```
Most megnyithat egy böngészőt, és rámutathat `http://localhost:8080/ui` a konzul felhasználói felületének megnyitására. A felhasználói felület megnyitásakor a következőnek kell megjelennie:

![Konzul felhasználói felülete](./media/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Konzul eltávolítása az AK-ból

> [!WARNING]
> Ha egy futó rendszerből törli a konzult, akkor a szolgáltatások közötti forgalomhoz kapcsolódó problémák léphetnek fel. A folytatás előtt győződjön meg arról, hogy a rendszernek megfelelő jogosultságokkal rendelkezik, hogy konzul nélkül is működjön.

### <a name="remove-consul-components-and-namespace"></a>A konzul összetevőinek és névterének eltávolítása

Ha el szeretné távolítani a konzult az AK-fürtből, használja a következő parancsokat. A `helm delete` parancsok el fogják távolítani `consul` a diagramot, `kubectl delete ns` és a parancs eltávolítja `consul` a névteret.

```bash
helm delete --purge consul
kubectl delete ns consul
```

## <a name="next-steps"></a>További lépések

A konzul további telepítési és konfigurációs lehetőségeinek megismeréséhez tekintse meg a következő hivatalos konzuli cikkeket:

- [A Consul-Helm telepítési útmutatója][consul-install-k8]
- [A konzul-Helm telepítési lehetőségei][consul-install-helm-options]

További forgatókönyveket is követhet a konzuli [példa alkalmazás][consul-app-example]használatával.

<!-- LINKS - external -->
[Hashicorp]: https://hashicorp.com
[cosul-github]: https://github.com/hashicorp/consul
[helm]: https://helm.sh

[consul-docs-concepts]: https://www.consul.io/docs/platform/k8s/index.html
[consul-github]: https://github.com/hashicorp/consul
[consul-github-releases]: https://github.com/hashicorp/consul/releases
[consul-release-notes]: https://github.com/hashicorp/consul/blob/master/CHANGELOG.md
[consul-install-download]: https://www.consul.io/downloads.html
[consul-install-k8]: https://www.consul.io/docs/platform/k8s/run.html
[consul-install-helm-options]: https://www.consul.io/docs/platform/k8s/helm.html#configuration-values-
[consul-app-example]: https://github.com/hashicorp/demo-consul-101/tree/master/k8s
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
