---
title: A Istio telepítése az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan telepítheti és használhatja a Istio a Service Mesh Azure Kubernetes szolgáltatásbeli (ak-) fürtben való létrehozásához
author: paulbouwer
ms.topic: article
ms.date: 02/19/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 5d47bb7f1f9c1ce39239d6f6ee5826a60ce1741a
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86244165"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>A Istio telepítése és használata az Azure Kubernetes szolgáltatásban (ak)

A [Istio][istio-github] egy nyílt forráskódú szolgáltatás rácsvonala, amely a Kubernetes-fürtökön futó összes szolgáltatás egyik kulcsfontosságú készletét biztosítja. Ezek a szolgáltatások közé tartoznak a forgalomirányítás, a szolgáltatások identitása és a biztonság, a házirendek betartatása és a Megfigyelhetőség. A Istio kapcsolatos további információkért tekintse meg a hivatalos [Mi az Istio?][istio-docs-concepts] dokumentációt.

Ez a cikk bemutatja, hogyan telepítheti a Istio. A Istio `istioctl` -ügyfél bináris fájlja telepítve van az ügyfélszámítógépre, és a Istio-összetevők a Kubernetes-fürtön vannak telepítve az AK-ban.

> [!NOTE]
> Az alábbi utasítások a Istio-verzióra hivatkoznak `1.4.0` .
>
> A Istio `1.4.x` -kiadásokat a Istio csapat tesztelte a Kubernetes verzióiban, `1.13` `1.14` `1.15` . További Istio-verziók a [GitHub-Istio kiadásokban][istio-github-releases], valamint a [Istio-Hírek][istio-release-notes] és a támogatott Kubernetes-verziók információi a [Istio általános gyakori kérdések][istio-faq]című témakörben találhatók.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Töltse le és telepítse a Istio istioctl-ügyfél bináris fájlját
> * A Istio telepítése az AK-on
> * A Istio telepítésének ellenőrzése
> * A bővítmények elérése
> * Istio eltávolítása az AK-ból

## <a name="before-you-begin"></a>Előkészületek

A cikkben részletezett lépések azt feltételezik, hogy létrehozott egy AK-fürtöt (Kubernetes `1.13` és újabb, RBAC engedélyezve), és létesítettek egy, `kubectl` a fürttel létesített kapcsolatokat. Ha segítségre van szüksége ezen elemek bármelyikével kapcsolatban, tekintse meg az [AK][aks-quickstart]gyors üzembe helyezését ismertető cikket.

Győződjön meg arról, hogy elolvasta a [Istio teljesítményére és méretezhetőségére](https://istio.io/docs/concepts/performance-and-scalability/) vonatkozó dokumentációt, és Ismerje meg a Istio futtatásához szükséges további erőforrás-követelményeket az AK-fürtben. Az alapvető és a memóriára vonatkozó követelmények az adott munkaterhelés alapján változhatnak. Válasszon megfelelő számú csomópontot és virtuálisgép-méretet a telepítéshez.

Ez a cikk a Istio telepítési útmutatóját több különálló lépésben elkülöníti. A végeredmény ugyanaz, mint a hivatalos Istio telepítési [útmutatója][istio-install-istioctl].

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>A Istio-összetevők telepítése az AK-on

A [Grafana][grafana] és a [Kiali][kiali] telepítését a Istio-telepítés részeként fogjuk telepíteni. A Grafana elemzési és figyelési irányítópultokat biztosít, a Kiali pedig egy Service Mesh-megfigyelő irányítópultot biztosít. A beállításban ezeknek az összetevőknek minden olyan hitelesítő adatra van szüksége, amelyet [titkos][kubernetes-secrets]adatként kell megadni.

A Istio-összetevők telepítése előtt létre kell hozni a titkokat mind a Grafana, mind a Kiali. Ezeket a titkokat a `istio-system` Istio által használt névtérbe kell telepíteni, ezért a névteret is létre kell hozni. A `--save-config` névtér létrehozásakor a beállítást kell használnia `kubectl create` , hogy a Istio-telepítő később is futtatható legyen `kubectl apply` ezen az objektumon.

```console
kubectl create namespace istio-system --save-config
```

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

Most, hogy sikeresen létrehozta a Grafana és a Kiali titkot az AK-fürtben, itt az ideje, hogy telepítse a Istio-összetevőket. 

A Istio [Helm][helm] telepítési megközelítése a jövőben elavulttá válik. A Istio új telepítési megközelítése kihasználja az `istioctl` ügyfél bináris fájljait, a [Istio konfigurációs profilokat][istio-configuration-profiles], valamint az új [Istio-vezérlési sík spec és API][istio-control-plane]-t. Ezt az új megközelítést fogjuk használni a Istio telepítéséhez.

> [!NOTE]
> A Istio jelenleg Linux-csomópontokon történő futtatásra kell ütemezni. Ha a fürtben Windows Server-csomópontok vannak, meg kell győződnie arról, hogy a Istio-hüvelyek csak Linux-csomópontokon futnak. A csomópont- [választókkal][kubernetes-node-selectors] gondoskodunk róla, hogy a hüvelyek a megfelelő csomópontokra legyenek ütemezve.

> [!CAUTION]
> Az [SDS (Secret Discovery Service)][istio-feature-sds] és a [Istio CNI][istio-feature-cni] Istio funkciók jelenleg az [Alpha][istio-feature-stages]-ben találhatók, ezért ezeket a lépéseket meg kell adni. 
>
> Vegye figyelembe, hogy a [szolgáltatásfiók-jogkivonat mennyiségi kivetítési][kubernetes-feature-sa-projected-volume] Kubernetes funkciója (az SDS követelménye) mostantól minden Kubernetes 1,13-es és újabb verziójú AK-ra **engedélyezett** .

Hozzon létre egy nevű fájlt `istio.aks.yaml` az alábbi tartalommal. Ez a fájl a Istio konfigurálásának részletes leírását fogja tárolni a [Istio-vezérlési síkon][istio-control-plane] .

```yaml
apiVersion: install.istio.io/v1alpha2
kind: IstioControlPlane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
      # Enable mutual TLS for the control plane
      controlPlaneSecurityEnabled: true
      mtls:
        # Require all service to service communication to have mtls
        enabled: false
    grafana:
      # Enable Grafana deployment for analytics and monitoring dashboards
      enabled: true
      security:
        # Enable authentication for Grafana
        enabled: true
    kiali:
      # Enable the Kiali deployment for a service mesh observability dashboard
      enabled: true
    tracing:
      # Enable the Jaeger deployment for tracing
      enabled: true
```

Telepítse a istio `istioctl apply` -t a parancs és a fenti `istio.aks.yaml` istio-vezérlési sík spec file használatával a következő módon:

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

A telepítő számos [CRDs][kubernetes-crd] telepít, majd a függőségek kezelésével telepíti az adott Istio-konfigurációhoz definiált összes érintett objektumot. A következő kimeneti kódrészlethez hasonlóan kell megjelennie.

```console
Applying manifests for these components:
- Tracing
- EgressGateway
- NodeAgent
- Grafana
- Policy
- Citadel
- CertManager
- IngressGateway
- Injector
- Prometheus
- PrometheusOperator
- Kiali
- Telemetry
- Galley
- Cni
- Pilot
- Base
- CoreDNS
NodeAgent is waiting on a prerequisite...
Telemetry is waiting on a prerequisite...
Galley is waiting on a prerequisite...
Cni is waiting on a prerequisite...
Grafana is waiting on a prerequisite...
Policy is waiting on a prerequisite...
Citadel is waiting on a prerequisite...
EgressGateway is waiting on a prerequisite...
Tracing is waiting on a prerequisite...
Kiali is waiting on a prerequisite...
PrometheusOperator is waiting on a prerequisite...
IngressGateway is waiting on a prerequisite...
Prometheus is waiting on a prerequisite...
CertManager is waiting on a prerequisite...
Injector is waiting on a prerequisite...
Pilot is waiting on a prerequisite...
Applying manifest for component Base
Waiting for CRDs to be applied.
CRDs applied.
Finished applying manifest for component Base
Prerequisite for Tracing has completed, proceeding with install.
Prerequisite for Injector has completed, proceeding with install.
Prerequisite for Telemetry has completed, proceeding with install.
Prerequisite for Policy has completed, proceeding with install.
Prerequisite for PrometheusOperator has completed, proceeding with install.
Prerequisite for NodeAgent has completed, proceeding with install.
Prerequisite for IngressGateway has completed, proceeding with install.
Prerequisite for Kiali has completed, proceeding with install.
Prerequisite for EgressGateway has completed, proceeding with install.
Prerequisite for Galley has completed, proceeding with install.
Prerequisite for Grafana has completed, proceeding with install.
Prerequisite for Cni has completed, proceeding with install.
Prerequisite for Citadel has completed, proceeding with install.
Applying manifest for component Tracing
Prerequisite for Prometheus has completed, proceeding with install.
Prerequisite for Pilot has completed, proceeding with install.
Prerequisite for CertManager has completed, proceeding with install.
Applying manifest for component Kiali
Applying manifest for component Prometheus
Applying manifest for component IngressGateway
Applying manifest for component Policy
Applying manifest for component Telemetry
Applying manifest for component Citadel
Applying manifest for component Galley
Applying manifest for component Pilot
Applying manifest for component Injector
Applying manifest for component Grafana
Finished applying manifest for component Kiali
Finished applying manifest for component Tracing
Finished applying manifest for component Prometheus
Finished applying manifest for component Citadel
Finished applying manifest for component Policy
Finished applying manifest for component IngressGateway
Finished applying manifest for component Injector
Finished applying manifest for component Galley
Finished applying manifest for component Pilot
Finished applying manifest for component Grafana
Finished applying manifest for component Telemetry

Component IngressGateway installed successfully:
================================================

serviceaccount/istio-ingressgateway-service-account created
deployment.apps/istio-ingressgateway created
gateway.networking.istio.io/ingressgateway created
sidecar.networking.istio.io/default created
poddisruptionbudget.policy/ingressgateway created
horizontalpodautoscaler.autoscaling/istio-ingressgateway created
service/istio-ingressgateway created

...
```

Ezen a ponton üzembe helyezte a Istio-t az AK-fürtön. A Istio sikeres üzembe helyezésének biztosítása érdekében térjünk át a következő szakaszra a [Istio telepítésének ellenőrzéséhez](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>A Istio telepítésének ellenőrzése

Először ellenőrizze, hogy létrejöttek-e a várt szolgáltatások. A futó szolgáltatások megtekintéséhez használja az [kubectl Get SVC][kubectl-get] parancsot. Kérdezze le a `istio-system` névteret, ahol a Istio és a kiegészítő összetevőket a `istio` Helm diagram telepítette:

```console
kubectl get svc --namespace istio-system --output wide
```

Az alábbi példában szereplő kimenet a következő szolgáltatásokat mutatja be:

- `istio-*`Services
- `jaeger-*`, `tracing` és `zipkin` kiegészítő nyomkövetési szolgáltatások
- `prometheus`kiegészítő metrikai szolgáltatás
- `grafana`a kiegészítő elemzési és monitorozási irányítópult szolgáltatás
- `kiali`kiegészítő szolgáltatás hálójának irányítópult szolgáltatása

Ha a `istio-ingressgateway` külső IP-címet mutat `<pending>` , várjon néhány percet, amíg az Azure-hálózat nem rendel hozzá IP-címet.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.116.147   <none>           3000/TCP                                                                                                                     92s   app=grafana
istio-citadel            ClusterIP      10.0.248.152   <none>           8060/TCP,15014/TCP                                                                                                           94s   app=citadel
istio-galley             ClusterIP      10.0.50.100    <none>           443/TCP,15014/TCP,9901/TCP,15019/TCP                                                                                         93s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.36.213    20.188.221.111   15020:30369/TCP,80:31368/TCP,443:30045/TCP,15029:32011/TCP,15030:31212/TCP,15031:32411/TCP,15032:30009/TCP,15443:30010/TCP   93s   app=istio-ingressgateway
istio-pilot              ClusterIP      10.0.23.222    <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                       93s   istio=pilot
istio-policy             ClusterIP      10.0.59.250    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                 93s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.123.219   <none>           443/TCP                                                                                                                      93s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.216.9     <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                       89s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                   96s   app=jaeger
jaeger-collector         ClusterIP      10.0.221.24    <none>           14267/TCP,14268/TCP,14250/TCP                                                                                                95s   app=jaeger
jaeger-query             ClusterIP      10.0.46.154    <none>           16686/TCP                                                                                                                    95s   app=jaeger
kiali                    ClusterIP      10.0.174.97    <none>           20001/TCP                                                                                                                    94s   app=kiali
prometheus               ClusterIP      10.0.245.226   <none>           9090/TCP                                                                                                                     94s   app=prometheus
tracing                  ClusterIP      10.0.249.95    <none>           9411/TCP                                                                                                                     95s   app=jaeger
zipkin                   ClusterIP      10.0.154.89    <none>           9411/TCP                                                                                                                     94s   app=jaeger
```

Ezután győződjön meg arról, hogy a szükséges hüvelyek létre lettek hozva. Használja a [kubectl Get hüvely][kubectl-get] parancsot, és ismételje meg a `istio-system` névterek lekérdezését:

```console
kubectl get pods --namespace istio-system
```

A következő példa kimenete az alábbi, a-t futtató hüvelyeket mutatja:

- a `istio-*` hüvelyek
- a `prometheus-*` kiegészítő mérőszámok Pod
- az `grafana-*` Add-on Analytics és a monitoring Dashboard Pod
- a `kiali` kiegészítő szolgáltatás hálójának irányítópult-Pod

```console
NAME                                          READY   STATUS    RESTARTS   AGE
grafana-6bc97ff99-k9sk4                       1/1     Running   0          92s
istio-citadel-6b5c754454-tb8nf                1/1     Running   0          94s
istio-galley-7d6d78d7c5-zshsd                 2/2     Running   0          94s
istio-ingressgateway-85869c5cc7-x5d76         1/1     Running   0          95s
istio-pilot-787d6995b5-n5vrj                  2/2     Running   0          94s
istio-policy-6cf4fbc8dc-sdsg5                 2/2     Running   2          94s
istio-sidecar-injector-5d5b978668-wrz2s       1/1     Running   0          94s
istio-telemetry-5498db684-6kdnw               2/2     Running   1          94s
istio-tracing-78548677bc-74tx6                1/1     Running   0          96s
kiali-59b7fd7f68-92zrh                        1/1     Running   0          95s
prometheus-7c7cf9dbd6-rjxcv                   1/1     Running   0          94s
```

Az összes hüvelynek állapota a következő: `Running` . Ha a hüvelye nem rendelkezik ezekkel az állapotokkal, várjon egy percet vagy kettőt, amíg meg nem történik. Ha bármelyik hüvely hibát jelez, használja a [kubectl leírását a pod][kubectl-describe] paranccsal a kimenet és az állapot áttekintéséhez.

## <a name="accessing-the-add-ons"></a>A bővítmények elérése

A Istio számos bővítményt telepített a telepítőnk, amely további funkciókat biztosít. A bővítmények webalkalmazásai **nem** jelennek meg nyilvánosan külső IP-címen keresztül. 

A bővítmény felhasználói felületének eléréséhez használja az `istioctl dashboard` parancsot. Ez a parancs a [kubectl Port-Forward][kubectl-port-forward] és egy véletlenszerű portot használ, hogy biztonságos kapcsolatot hozzon létre az ügyfélszámítógép és a megfelelő Pod-kapcsolat között az AK-fürtben. Ezután automatikusan megnyitja a bővítmény webalkalmazást az alapértelmezett böngészőben.

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

A Istio az AK-fürtből való eltávolításához használja az `istioctl manifest generate` parancsot a `istio.aks.yaml` Istio Control Plan Plane spec file utasítással. Ez létrehozza a központilag telepített jegyzékfájlt, amelyet a rendszer az `kubectl delete` összes telepített összetevő és a névtér eltávolításához vezet `istio-system` .

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>Istio-CRDs és-titkok eltávolítása

A fenti parancsok törlik a Istio összes összetevőjét és névterét, de továbbra is a generált Istio titkos kódok maradnak. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Következő lépések

Az alábbi dokumentáció azt ismerteti, hogyan használható a Istio a Kanári-kiadások kivezetéséhez szükséges intelligens útválasztás biztosításához:

> [!div class="nextstepaction"]
> [AK Istio intelligens útválasztási forgatókönyv][istio-scenario-routing]

A Istio további telepítési és konfigurációs lehetőségeinek megismeréséhez tekintse meg a következő hivatalos Istio útmutatót:

- [Istio – telepítési útmutatók][istio-installation-guides]

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
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: /windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: ./concepts-clusters-workloads.md#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

[app-insights]: ../azure-monitor/app/kubernetes.md

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./servicemesh-istio-scenario-routing.md
