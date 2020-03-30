---
title: Konzul telepítése az Azure Kubernetes szolgáltatásban (AKS)
description: Megtudhatja, hogyan telepítheti és használhatja a konzult egy Azure Kubernetes-szolgáltatás (AKS) fürtben lévő szolgáltatásháló létrehozásához
author: dstrebel
ms.topic: article
ms.date: 10/09/2019
ms.author: dastrebe
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 1601ab6d81b888fd2247e95f22c58e1fc91df698
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273741"
---
# <a name="install-and-use-consul-in-azure-kubernetes-service-aks"></a>Konzul telepítése és használata az Azure Kubernetes szolgáltatásban (AKS)

[Consul][consul-github] egy nyílt forráskódú szolgáltatás háló, amely egy kulcskészlet a funkciók között a mikroszolgáltatások egy Kubernetes-fürtben. Ezek közé tartozik a szolgáltatás felderítése, állapot-ellenőrzés, szolgáltatás szegmentálása és megfigyelhetőségét. A konzulról további információt a ["Mi az a konzul?"][consul-docs-concepts] dokumentációban talál.

Ez a cikk bemutatja, hogyan kell telepíteni konzul. A consul-összetevők az AKS Kubernetes-fürtébe vannak telepítve.

> [!NOTE]
> Ezek az utasítások `1.6.0`referencia Konzul változat , `2.14.2`és használja legalább Helm változat .
>
> A Consul `1.6.x` kiadások futtathatók a Kubernetes verzióival. `1.13+` További konzuli verziókat a [GitHub - Consul Releases][consul-github-releases] oldalon talál, és információkat az egyes kiadásokról a [Consul- Release Notes][consul-release-notes]oldalon.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A Consul-összetevők telepítése az AKS-re
> * A konzul telepítésének ellenőrzése
> * Konzul eltávolítása az AKS-ből

## <a name="before-you-begin"></a>Előkészületek

A cikkben ismertetett lépések feltételezik, hogy létrehozott egy AKS-fürtöt (Kubernetes `1.13` és `kubectl` újabb, az RBAC engedélyezve), és kapcsolatot létesített a fürttel. Ha ezekhez az elemekhez segítségre van szüksége, olvassa el az [AKS rövid útmutatóját.][aks-quickstart] Győződjön meg arról, hogy a fürt legalább 3 csomópont a Linux-csomópont készletben.

Szükséged lesz [Helmre,][helm] hogy kövesse ezeket az utasításokat, és telepítse a konzult. Javasoljuk, hogy a legújabb stabil verziót megfelelően telepítse és konfigurálja a fürtben. Ha segítségre van szüksége a Helm telepítéséhez, olvassa el az [AKS Helm telepítési útmutatóját.][helm-install] Minden konzuli podot is úgy kell ütemezni, hogy Linux csomópontokon fusson.

Ez a cikk a konzul telepítési útmutatóját több különálló lépésre választja el. A végeredmény ugyanaz a szerkezet, mint a hivatalos konzul telepítési [útmutató][consul-install-k8].

### <a name="install-the-consul-components-on-aks"></a>A Consul-összetevők telepítése az AKS-re

Kezdjük azzal, hogy `v0.10.0` letöltjük a Konzuli Helm diagram verzióját. Ez a verzió a diagram `1.6.0`tartalmazza Konzul változat .

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download](includes/servicemesh/consul/download-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download](includes/servicemesh/consul/download-powershell.md)]

::: zone-end

A Helm és `consul-helm` a letöltött diagram segítségével telepítse `consul` a Consul-összetevőket az AKS-fürt névterébe. 

> [!NOTE]
> **Telepítési lehetőségek**
> 
> A telepítés részeként a következő lehetőségeket használjuk:
> - `connectInject.enabled=true`- proxyk befecskendezésének lehetővé tétele a hüvelyekbe
> - `client.enabled=true`- lehetővé teszi konzul ügyfelek futtatásához minden csomópont
> - `client.grpc=true`- lehetővé teszi a gRPC-hallgató t
> - `syncCatalog.enabled=true`- sync Kubernetes és konzul szolgáltatások
>
> **Csomópontválasztók**
>
> A consult jelenleg linuxos csomópontokon való futtatásra kell ütemezni. Ha a fürtben Windows Server-csomópontok találhatók, gondoskodnia kell arról, hogy a konzuli podok csak Linux-csomópontokon fussanak. A [csomópontválasztók][kubernetes-node-selectors] segítségével biztosítjuk, hogy a podok a megfelelő csomópontokra legyenek ütemezve.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/consul/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/consul/install-components-powershell.md)]

::: zone-end

A `Consul` Helm diagram számos objektumot telepít. Láthatjuk a listát a kimenet `helm install` a parancs felett. A consul-összetevők üzembe helyezése a fürtkörnyezettől függően körülbelül 3 percet is igénybe vehet.

Ezen a ponton már telepített konzul az AKS-fürt. Annak érdekében, hogy a konzul sikeres telepítése sikeres legyen, térjünk át a következő részre, hogy érvényesítsük a konzul telepítését.

## <a name="validate-the-consul-installation"></a>A konzul telepítésének ellenőrzése

Ellenőrizze, hogy az erőforrások sikeresen létrejöttek-e. Használja a [kubectl get svc][kubectl-get] és [kubectl get pod][kubectl-get] parancsokat a `consul` névtér `helm install` lekérdezéséhez, ahol a Consul összetevők telepítve voltak a parancs:

```console
kubectl get svc --namespace consul --output wide
kubectl get pod --namespace consul --output wide
```

A következő példa kimeneti jeleníti meg a szolgáltatások és a podok (linuxos csomópontokon ütemezve), amelyek most már fut:

```output
NAME                                 TYPE           CLUSTER-IP    EXTERNAL-IP             PORT(S)                                                                   AGE     SELECTOR
consul                               ExternalName   <none>        consul.service.consul   <none>                                                                    38s     <none>
consul-consul-connect-injector-svc   ClusterIP      10.0.98.102   <none>                  443/TCP                                                                   3m26s   app=consul,component=connect-injector,release=consul
consul-consul-dns                    ClusterIP      10.0.46.194   <none>                  53/TCP,53/UDP                                                             3m26s   app=consul,hasDNS=true,release=consul
consul-consul-server                 ClusterIP      None          <none>                  8500/TCP,8301/TCP,8301/UDP,8302/TCP,8302/UDP,8300/TCP,8600/TCP,8600/UDP   3m26s   app=consul,component=server,release=consul
consul-consul-ui                     ClusterIP      10.0.50.188   <none>                  80/TCP                                                                    3m26s   app=consul,component=server,release=consul

NAME                                                              READY   STATUS    RESTARTS   AGE    IP            NODE                            NOMINATED NODE   READINESS GATES
consul-consul-connect-injector-webhook-deployment-99f74fdbcr5zj   1/1     Running   0          3m9s   10.240.0.68   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-jbksc                                               1/1     Running   0          3m9s   10.240.0.44   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-jkwtq                                               1/1     Running   0          3m9s   10.240.0.70   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-0                                            1/1     Running   0          3m9s   10.240.0.91   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-server-1                                            1/1     Running   0          3m9s   10.240.0.38   aks-linux-92468653-vmss000001   <none>           <none>
consul-consul-server-2                                            1/1     Running   0          3m9s   10.240.0.10   aks-linux-92468653-vmss000000   <none>           <none>
consul-consul-sync-catalog-d846b79c-8ssr8                         1/1     Running   2          3m9s   10.240.0.94   aks-linux-92468653-vmss000002   <none>           <none>
consul-consul-tz2t5                                               1/1     Running   0          3m9s   10.240.0.12   aks-linux-92468653-vmss000000   <none>           <none>
```

Az összes hüvelynek meg kell `Running`jelennie. Ha a hüvelyek nem rendelkeznek ezekkel az állapotokkal, várjon egy-két percet, amíg meg nem teszik. Ha bármelyik podok jelentést egy probléma, használja a [kubectl írja pod][kubectl-describe] parancsot, hogy tekintse át a kimeneti és állapoti.

## <a name="accessing-the-consul-ui"></a>A konzuli felhasználói felület elérése

A Consul UI a fenti beállításunkba lett telepítve, és felhasználói felületen alapuló konfigurációt biztosít a konzulszámára. A konzul felhasználói felülete nem nyilvánosan érhető el külső IP-címen keresztül. A Consul felhasználói felületének eléréséhez használja a [kubectl port-forward parancsot.][kubectl-port-forward] Ez a parancs biztonságos kapcsolatot hoz létre az ügyfélgép és az AKS-fürt megfelelő podja között.

```console
kubectl port-forward -n consul svc/consul-consul-ui 8080:80
```

Most megnyithat egy böngészőt, és rámutathat a konzul felhasználói felületének `http://localhost:8080/ui` megnyitására. A felhasználói felület megnyitásakor a következőknek kell megjedkednie:

![Konzul ui](./media/servicemesh/consul/consul-ui.png)

## <a name="uninstall-consul-from-aks"></a>Konzul eltávolítása az AKS-ből

> [!WARNING]
> A konzul futtatási rendszerből való törlése a szolgáltatások közötti, forgalommal kapcsolatos problémákat okozhat. A folytatás előtt győződjön meg arról, hogy rendelkezik arról, hogy a rendszer továbbra is megfelelően működjön konzul nélkül.

### <a name="remove-consul-components-and-namespace"></a>Konzuli összetevők és névtér eltávolítása

A Consul a KKS-fürtből való eltávolításához használja a következő parancsokat. A `helm delete` parancsok eltávolítják a `consul` `kubectl delete namespace` diagramot, `consul` a parancs pedig a névteret.

```console
helm delete --purge consul
kubectl delete namespace consul
```

## <a name="next-steps"></a>További lépések

A konzul további telepítési és konfigurációs lehetőségeinek megismeréséhez olvassa el a következő hivatalos konzuli cikkeket:

- [Consul - Helm telepítési útmutató][consul-install-k8]
- [Consul - Helm telepítési lehetőségek][consul-install-helm-options]

További forgatókönyveket is követhet a következő használatával:

- [Konzul iapplication][consul-app-example]

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
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[consul-scenario-mtls]: ./consul-mtls.md
[helm-install]: ./kubernetes-helm.md
