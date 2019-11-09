---
title: A Istio telepítése az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan telepítheti és használhatja a Istio a Service Mesh Azure Kubernetes szolgáltatásbeli (ak-) fürtben való létrehozásához
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 245ac3b1fd88b8d2430e9ddefef3562efd16e6d1
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73885381"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>A Istio telepítése és használata az Azure Kubernetes szolgáltatásban (ak)

A [Istio][istio-github] egy nyílt forráskódú szolgáltatás rácsvonala, amely a Kubernetes-fürtökön futó összes szolgáltatás egyik kulcsfontosságú készletét biztosítja. Ezek a szolgáltatások közé tartoznak a forgalomirányítás, a szolgáltatások identitása és a biztonság, a házirendek betartatása és a Megfigyelhetőség. A Istio kapcsolatos további információkért tekintse meg a hivatalos [Mi az Istio?][istio-docs-concepts] dokumentációt.

Ez a cikk bemutatja, hogyan telepítheti a Istio. A Istio `istioctl` ügyfél bináris fájlja telepítve van az ügyfélszámítógépre, és a Istio-összetevők Kubernetes-fürtbe vannak telepítve az AK-ban.

> [!NOTE]
> Ezek az utasítások a Istio verzióját `1.3.2`, és legalább a Helm-verziót `2.14.2`használják.
>
> A Istio `1.3.x` kiadásait a Istio csapat tesztelte a Kubernetes-verziók `1.13`, `1.14``1.15`. További Istio-verziók a [GitHub-Istio kiadásokban][istio-github-releases], valamint a [Istio-Hírek][istio-release-notes] és a támogatott Kubernetes-verziók információi a [Istio általános gyakori kérdések][istio-faq]című témakörben találhatók.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Töltse le és telepítse a Istio istioctl-ügyfél bináris fájlját
> * A Istio telepítése az AK-on
> * A Istio telepítésének ellenőrzése
> * A bővítmények elérése
> * Istio eltávolítása az AK-ból

## <a name="before-you-begin"></a>Előkészületek

A cikkben részletesen ismertetett lépések azt feltételezik, hogy létrehozott egy AK-fürtöt (Kubernetes `1.13` és újabb rendszerű, RBAC engedélyezve), és létrehozta a fürttel létesített `kubectl`-kapcsolatokat. Ha segítségre van szüksége ezen elemek bármelyikével kapcsolatban, tekintse meg az [AK][aks-quickstart]gyors üzembe helyezését ismertető cikket.

Az utasítások követéséhez és a Istio telepítéséhez a [Helm][helm] szükséges. Javasoljuk, hogy a legújabb stabil verziót megfelelően telepítse és konfigurálja a fürtben. Ha segítségre van szüksége a Helm telepítéséhez, tekintse meg az [AK Helm telepítési útmutatóját][helm-install]. Az összes Istio-hüvelyt is be kell ütemezni a Linux-csomópontokon történő futtatásra.

Győződjön meg arról, hogy elolvasta a [Istio teljesítményének és méretezhetőségének](https://istio.io/docs/concepts/performance-and-scalability/) dokumentációját, és Ismerje meg a Istio futtatásához szükséges további erőforrás-követelményeket az AK-fürtben. Az alapvető és a memóriára vonatkozó követelmények az adott munkaterhelés alapján változhatnak. Válasszon megfelelő számú csomópontot és virtuálisgép-méretet a telepítéshez.

Ez a cikk a Istio telepítési útmutatóját több különálló lépésben elkülöníti. A végeredmény ugyanaz, mint a hivatalos Istio telepítési [útmutatója][istio-install-helm].

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="add-the-istio-helm-chart-repository"></a>A Istio Helm chart adattár hozzáadása

Adja hozzá a Istio Helm chart-tárházat a Istio kiadáshoz. Győződjön meg arról, hogy futtatja a `helm repo update` a diagram adattárához tartozó helyi információk frissítéséhez.

```azurecli
helm repo add istio.io https://storage.googleapis.com/istio-release/releases/$ISTIO_VERSION/charts/
helm repo update
```

## <a name="install-the-istio-crds-on-aks"></a>A Istio CRDs telepítése az AK-on

A Istio [egyéni erőforrás-definíciókat (CRDs)][kubernetes-crd] használ a futásidejű konfigurációjának kezeléséhez. Először telepíteni kell a Istio-CRDs, mert a Istio-összetevők függőséggel rendelkeznek. Az Istio-CRDs a Helm és a `istio-init` diagram használatával telepítheti az AK-fürt `istio-system` névterében:

```azurecli
helm install istio.io/istio-init --name istio-init --namespace istio-system
```

A [feladatok][kubernetes-jobs] üzembe helyezése az `istio-init` Helm diagram részeként történik a CRDs telepítéséhez. Ezeknek a feladatoknak a kitöltéséhez a fürt környezete alapján kevesebb, mint 20-as is kell lennie. A feladatok sikeres befejezését a következőképpen ellenőrizheti:

```azurecli
kubectl get jobs -n istio-system
```

A következő példa kimenete a sikeresen befejezett feladatokat jeleníti meg.

```console
NAME                      COMPLETIONS   DURATION   AGE
istio-init-crd-10-1.3.2   1/1           14s        14s
istio-init-crd-11-1.3.2   1/1           12s        14s
istio-init-crd-12-1.3.2   1/1           14s        14s
```

Most, hogy megerősítettük a feladatok sikeres befejezését, ellenőrizze, hogy megfelelő számú Istio-CRDs van-e telepítve. A következő parancs futtatásával ellenőrizheti, hogy az összes 23 Istio-CRDs telepítve van-e. A parancsnak a `23`számot kell visszaadnia.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - check CRD count](includes/servicemesh/istio/install-check-crd-count-powershell.md)]

::: zone-end

Ha erre a pontra van szüksége, akkor ez azt jelenti, hogy sikeresen telepítette a Istio-CRDs. Most lépjen a következő szakaszra, és [telepítse a Istio összetevőket az AK-ra](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>A Istio-összetevők telepítése az AK-on

A [Grafana][grafana] és a [Kiali][kiali] telepítését a Istio-telepítés részeként fogjuk telepíteni. A Grafana elemzési és figyelési irányítópultokat biztosít, a Kiali pedig egy Service Mesh-megfigyelő irányítópultot biztosít. A beállításban ezeknek az összetevőknek minden olyan hitelesítő adatra van szüksége, amelyet [titkos][kubernetes-secrets]adatként kell megadni.

A Istio-összetevők telepítése előtt létre kell hozni a titkokat mind a Grafana, mind a Kiali. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>Istio-összetevők telepítése

Most, hogy sikeresen létrehozta a Grafana és a Kiali titkot az AK-fürtben, itt az ideje, hogy telepítse a Istio-összetevőket. Az Istio-összetevőket a Helm és a `istio` diagram használatával telepítheti az AK-fürt `istio-system` névterére. 

> [!NOTE]
> **Telepítési beállítások**
> 
> A telepítés részeként a következő beállításokat használjuk:
> - `global.controlPlaneSecurityEnabled=true` – a vezérlési síkon engedélyezve van a kölcsönös TLS
> - `global.mtls.enabled=true` – az összes szolgáltatás és szolgáltatás közötti kommunikáció megkövetelése a MTLS
> - `grafana.enabled=true` – Grafana üzembe helyezésének engedélyezése elemzési és figyelési irányítópultokhoz
> - `grafana.security.enabled=true` – hitelesítés engedélyezése a Grafana
> - `tracing.enabled=true` – a Jaeger üzembe helyezésének engedélyezése nyomkövetéshez
> - `kiali.enabled=true` – a Kiali üzembe helyezésének engedélyezése a szolgáltatásbeli hálót megfigyelő irányítópulton
>
> **Csomópont-választók**
>
> A Istio jelenleg Linux-csomópontokon történő futtatásra kell ütemezni. Ha a fürtben Windows Server-csomópontok vannak, meg kell győződnie arról, hogy a Istio-hüvelyek csak Linux-csomópontokon futnak. A csomópont- [választókkal][kubernetes-node-selectors] gondoskodunk róla, hogy a hüvelyek a megfelelő csomópontokra legyenek ütemezve.

> [!CAUTION]
> Az [SDS (Secret Discovery Service)][istio-feature-sds] és a [Istio CNI][istio-feature-cni] Istio funkciók jelenleg az [Alpha][istio-feature-stages]-ben találhatók, ezért ezeket a lépéseket meg kell adni. Emellett a szolgáltatásfiók- [jogkivonat mennyiségi kivetítési][kubernetes-feature-sa-projected-volume] Kubernetes funkciója (az SDS követelménye) nincs engedélyezve a jelenlegi AK-verziókban.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/istio/install-components-powershell.md)]

::: zone-end

A `istio` Helm diagram nagy mennyiségű objektumot helyez üzembe. A listát a fenti `helm install` parancs kimenetében tekintheti meg. A Istio-összetevők üzembe helyezéséhez a fürt környezetéből függően 2 percen belül kell elvégeznie a telepítést.

Ezen a ponton üzembe helyezte a Istio-t az AK-fürtön. A Istio sikeres üzembe helyezésének biztosítása érdekében térjünk át a következő szakaszra a [Istio telepítésének ellenőrzéséhez](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>A Istio telepítésének ellenőrzése

Először ellenőrizze, hogy létrejöttek-e a várt szolgáltatások. A futó szolgáltatások megtekintéséhez használja az [kubectl Get SVC][kubectl-get] parancsot. Kérdezze le a `istio-system` névteret, ahol a Istio és a kiegészítő összetevőket a `istio` Helm diagram telepítette:

```console
kubectl get svc --namespace istio-system --output wide
```

Az alábbi példában szereplő kimenet a következő szolgáltatásokat mutatja be:

- `istio-*` szolgáltatások
- `jaeger-*`, `tracing`és `zipkin` kiegészítő nyomkövetési szolgáltatások
- `prometheus` kiegészítő metrikák szolgáltatás
- `grafana` kiegészítő elemzési és monitorozási irányítópult szolgáltatás
- `kiali` kiegészítő szolgáltatás rácsvonal-irányítópult szolgáltatása

Ha a `istio-ingressgateway` `<pending>`külső IP-címét jeleníti meg, várjon néhány percet, amíg az Azure-hálózat nem rendel hozzá IP-címet.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.164.244   <none>           3000/TCP                                                                                                                                     53s   app=grafana
istio-citadel            ClusterIP      10.0.49.16     <none>           8060/TCP,15014/TCP                                                                                                                           53s   istio=citadel
istio-galley             ClusterIP      10.0.175.173   <none>           443/TCP,15014/TCP,9901/TCP                                                                                                                   53s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.226.151   20.188.221.111   15020:31128/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:30817/TCP,15030:30436/TCP,15031:32485/TCP,15032:30980/TCP,15443:30124/TCP   53s   app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.102.158   <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       53s   istio=pilot
istio-policy             ClusterIP      10.0.234.53    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                                 53s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.216.8     <none>           443/TCP,15014/TCP                                                                                                                            53s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.154.215   <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       53s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                                   52s   app=jaeger
jaeger-collector         ClusterIP      10.0.26.109    <none>           14267/TCP,14268/TCP                                                                                                                          52s   app=jaeger
jaeger-query             ClusterIP      10.0.70.55     <none>           16686/TCP                                                                                                                                    52s   app=jaeger
kiali                    ClusterIP      10.0.36.206    <none>           20001/TCP                                                                                                                                    53s   app=kiali
prometheus               ClusterIP      10.0.236.99    <none>           9090/TCP                                                                                                                                     53s   app=prometheus
tracing                  ClusterIP      10.0.83.152    <none>           80/TCP                                                                                                                                       52s   app=jaeger
zipkin                   ClusterIP      10.0.25.86     <none>           9411/TCP                                                                                                                                     52s   app=jaeger
```

Ezután győződjön meg arról, hogy a szükséges hüvelyek létre lettek hozva. Használja a [kubectl Get hüvely][kubectl-get] parancsot, és ismételje meg a `istio-system` névtér lekérdezését:

```console
kubectl get pods --namespace istio-system
```

A következő példa kimenete az alábbi, a-t futtató hüvelyeket mutatja:

- a `istio-*` hüvely
- a `prometheus-*` kiegészítő mérőszámok Pod
- a `grafana-*` kiegészítő elemzési és monitorozási irányítópultja
- a `kiali` kiegészítő szolgáltatás rácsvonalának irányítópultja

```console
NAME                                     READY   STATUS      RESTARTS   AGE
grafana-7c48555456-msl7b                 1/1     Running     0          88s
istio-citadel-566fc66db7-m8wgl           1/1     Running     0          87s
istio-galley-5746db8d56-pl5gg            1/1     Running     0          88s
istio-ingressgateway-6c94f7c9bf-f5lt5    1/1     Running     0          88s
istio-init-crd-10-1.3.2-xw9g2            0/1     Completed   0          92m
istio-init-crd-11-1.3.2-54rz8            0/1     Completed   0          92m
istio-init-crd-12-1.3.2-789qj            0/1     Completed   0          92m
istio-pilot-6748968b6d-rvdfx             2/2     Running     0          87s
istio-policy-7576bbbcf7-2stft            2/2     Running     0          87s
istio-sidecar-injector-76d79d494-7jk9n   1/1     Running     0          87s
istio-telemetry-74b7bf676d-tfrcl         2/2     Running     0          88s
istio-tracing-655d9588bc-d2htg           1/1     Running     0          86s
kiali-65d55bcfb8-tqrfk                   1/1     Running     0          88s
prometheus-846f9849bd-br8kp              1/1     Running     0          87s
```

Három `istio-init-crd-*` hüvelynek kell lennie `Completed` állapottal. Ezek a hüvelyek felelősek voltak azon feladatok futtatásához, amelyek egy korábbi lépésben hoztak létre a CRDs. Az összes többi hüvelynek `Running`állapotot kell mutatnia. Ha a hüvelye nem rendelkezik ezekkel az állapotokkal, várjon egy percet vagy kettőt, amíg meg nem történik. Ha bármelyik hüvely hibát jelez, használja a [kubectl leírását a pod][kubectl-describe] paranccsal a kimenet és az állapot áttekintéséhez.

## <a name="accessing-the-add-ons"></a>A bővítmények elérése

A Istio számos bővítményt telepített a telepítőnk, amely további funkciókat biztosít. A bővítmények webalkalmazásai **nem** jelennek meg nyilvánosan külső IP-címen keresztül. 

A bővítmény felhasználói felületének eléréséhez használja a `istioctl dashboard` parancsot. Ez a parancs a [kubectl Port-Forward][kubectl-port-forward] és egy véletlenszerű portot használ, hogy biztonságos kapcsolatot hozzon létre az ügyfélszámítógép és a megfelelő Pod-kapcsolat között az AK-fürtben. Ezután automatikusan megnyitja a bővítmény webalkalmazást az alapértelmezett böngészőben.

A Grafana és a Kiali egy további biztonsági réteget is hozzáadott a hitelesítő adatok megadásával a jelen cikk korábbi részében.

### <a name="grafana"></a>Grafana

A Istio tartozó elemzési és figyelési irányítópultokat a [Grafana][grafana]nyújtja. Ha a rendszer kéri, ne felejtse el használni a Grafana titkos kulcson keresztül létrehozott hitelesítő adatokat. A következőképpen nyissa meg biztonságosan a Grafana irányítópultját:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

A Istio metrikáit a [Prometheus][prometheus]nyújtja. A következő módon nyissa meg biztonságosan a Prometheus-irányítópultot:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

A Istio belüli nyomkövetést a [Jaeger][jaeger]biztosítja. A következő módon nyissa meg biztonságosan a Jaeger-irányítópultot:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

A [Kiali][kiali]által biztosított Service Mesh-megfigyelő irányítópultot. Ha a rendszer kéri, ne felejtse el használni a Kiali titkos kulcson keresztül létrehozott hitelesítő adatokat. A következőképpen nyissa meg biztonságosan a Kiali irányítópultját:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Elérhető a [megbízottat][envoy] proxyk egyszerű felülete. Konfigurációs információkat és mérőszámokat biztosít egy megadott Pod-on futó megbízottat proxyhoz. A következő módon nyissa meg biztonságosan a megbízott felületet:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Istio eltávolítása az AK-ból

> [!WARNING]
> A Istio egy futó rendszerből való törlése a szolgáltatások közötti forgalomhoz vezethet. A folytatás előtt győződjön meg arról, hogy rendelkezik a rendszer megfelelő működéséhez szükséges Istio nélkül.

### <a name="remove-istio-components-and-namespace"></a>Istio összetevők és névtér eltávolítása

Az alábbi parancsokkal távolíthatja el a Istio az AK-fürtből. A `helm delete` parancsok el fogják távolítani a `istio` és `istio-init` diagramokat, és az `kubectl delete namespace` parancs eltávolítja a `istio-system` névteret.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete namespace istio-system
```

### <a name="remove-istio-crds-and-secrets"></a>Istio-CRDs és-titkok eltávolítása

A fenti parancsok törlik a Istio összes összetevőjét és névterét, de továbbra is maradnak a Istio CRDs és a titkokkal. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>További lépések

Az alábbi dokumentáció azt ismerteti, hogyan használható a Istio a Kanári-kiadások kivezetéséhez szükséges intelligens útválasztás biztosításához:

> [!div class="nextstepaction"]
> [AK Istio intelligens útválasztási forgatókönyv][istio-scenario-routing]

A Istio további telepítési és konfigurációs lehetőségeinek megismeréséhez tekintse meg a következő hivatalos Istio-cikkeket:

- [Istio – Helm telepítési útmutató][istio-install-helm]
- [Istio – Helm telepítési lehetőségek][istio-install-helm-options]

A következő esetekben is elvégezheti a további forgatókönyvek használatát:

- [Istio Bookinfo alkalmazás – példa][istio-bookinfo-example]

A következő Azure Monitor dokumentációból megtudhatja, hogyan figyelheti az AK-alkalmazást Application Insights és Istio használatával:

- [Zero Instrumentation-alkalmazás figyelése Kubernetes által üzemeltetett alkalmazásokhoz][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./servicemesh-istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
