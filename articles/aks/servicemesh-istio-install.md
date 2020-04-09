---
title: Istio telepítése az Azure Kubernetes szolgáltatásban (AKS)
description: Ismerje meg, hogyan telepítheti és használhatja az Istio-t egy Szolgáltatásháló létrehozásához egy Azure Kubernetes-szolgáltatás (AKS) fürtben
author: paulbouwer
ms.topic: article
ms.date: 02/19/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d1d02cb42a86023e5c341daab678c39f22f75dda
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877694"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Az Istio telepítése és használata az Azure Kubernetes szolgáltatásban (AKS)

[Az Istio][istio-github] egy nyílt forráskódú szolgáltatásháló, amely egy Kubernetes-fürt mikroszolgáltatásaiközött kulcsfontosságú funkciókat biztosít. Ezek közé tartozik a forgalomkezelés, a szolgáltatás identitása és biztonsága, a házirendek betartatása és a megfigyelhetőség. További információ az Istio, lásd a hivatalos [Mi istio?][istio-docs-concepts] dokumentáció.

Ez a cikk bemutatja, hogyan kell telepíteni istio. Az Istio-ügyfél `istioctl` bináris telepítve van az ügyfélgépre, és az Istio-összetevők egy Kubernetes-fürtbe vannak telepítve az AKS-en.

> [!NOTE]
> A következő utasítások az `1.4.0`Istio verziójára vonatkoznak.
>
> Az `1.4.x` Istio-kiadásokat az Istio csapata tesztelte a `1.13`Kubernetes verziókkal szemben `1.14`. `1.15` További Istio verziókat talál a [GitHub - Istio Releases][istio-github-releases]címen, az [Istio News][istio-release-notes] minden egyes kiadásáról és a támogatott Kubernetes verziókról az [Istio General GYIK-ben.][istio-faq]

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Az Istio istioctl kliens bináris letöltése és telepítése
> * Az Istio telepítése AKS-re
> * Az Istio-telepítés ellenőrzése
> * A bővítmények elérése
> * Istio eltávolítása az AKS-ből

## <a name="before-you-begin"></a>Előkészületek

A cikkben ismertetett lépések feltételezik, hogy létrehozott egy AKS-fürtöt (Kubernetes `1.13` és `kubectl` újabb, az RBAC engedélyezve), és kapcsolatot létesített a fürttel. Ha ezekhez az elemekhez segítségre van szüksége, olvassa el az [AKS rövid útmutatóját.][aks-quickstart]

Győződjön meg arról, hogy elolvasta az [Istio teljesítmény és skálázhatóság](https://istio.io/docs/concepts/performance-and-scalability/) dokumentációt az Istio az AKS-fürtben való futtatásához szükséges további erőforrás-követelmények megértéséhez. Az alapvető és a memória követelmények az adott számítási feladatoktól függően változik. Válassza ki a megfelelő számú csomópontok és a virtuális gép méretét, hogy kielégedjen a beállítás.

Ez a cikk az Istio telepítési útmutatót több különálló lépésre bontja. A végeredmény ugyanaz a szerkezet, mint a hivatalos Istio telepítési [útmutató][istio-install-istioctl].

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>Az Istio-összetevők telepítése az AKS-re

[A Grafana][grafana] és [Kiali][kiali] telepítését az Istio telepítés részeként telepítjük. A Grafana elemzési és figyelési irányítópultokat biztosít, a Kiali pedig egy szolgáltatásháló-megfigyelhetőségi irányítópultot. A mi beállítás, minden ilyen összetevők hitelesítő adatokat, amelyeket meg kell adni, mint egy [titkos][kubernetes-secrets].

Mielőtt telepítenénk az Istio komponenseket, meg kell teremtenünk a titkokat Grafana és Kiali számára is. Ezeket a titkos kulcsokat telepíteni kell az `istio-system` Istio által használt névtérbe, ezért a névteret is létre kell hoznunk. Meg kell használni `--save-config` a lehetőséget, amikor `kubectl create` létrehozza a névteret keresztül, `kubectl apply` hogy az Istio telepítő futtatható ezen az objektumon a jövőben.

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

Most, hogy sikeresen létrehoztuk a Grafana és a Kiali titkait az AKS-fürtben, itt az ideje telepíteni az Istio összetevőket. 

Az Istio [Helm][helm] telepítési megközelítése a jövőben elavult lesz. Az Istio új telepítési megközelítése `istioctl` kihasználja az ügyfél bináris, az [Istio konfigurációs profilokat][istio-configuration-profiles]és az új [Istio vezérlősík specifikációját és api-ját.][istio-control-plane] Ez az új megközelítés az, amit mi fogunk használni telepíteni Istio.

> [!NOTE]
> Az Istio-t jelenleg linuxos csomópontokon való futtatásra kell ütemezni. Ha a fürtben Windows Server-csomópontok találhatók, biztosítania kell, hogy az Istio-podok csak Linux-csomópontokon fussanak. A [csomópontválasztók][kubernetes-node-selectors] segítségével biztosítjuk, hogy a podok a megfelelő csomópontokra legyenek ütemezve.

> [!CAUTION]
> Az [SDS (secret discovery service)][istio-feature-sds] és [az Istio CNI][istio-feature-cni] Istio funkciók jelenleg [alfa,][istio-feature-stages]így gondoltam kell adni, mielőtt ezeket. 
>
> Vegye figyelembe, hogy a [szolgáltatásfiók tokenkötet-kivetítése][kubernetes-feature-sa-projected-volume] Kubernetes szolgáltatás (az SDS követelménye) **mostantól engedélyezve** van az Összes Kubernetes 1.13 és újabb verziók az AKS-en.

Hozzon létre `istio.aks.yaml` egy megnevezett fájlt a következő tartalommal. Ez a fájl fogja tartani az [Istio vezérlő sík spec][istio-control-plane] részleteket konfigurálása Istio.

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

Telepítse istio `istioctl apply` a parancsot, és a fenti `istio.aks.yaml` Istio vezérlő sík spec fájlt az alábbiak szerint:

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

A telepítő számos [CRD-t][kubernetes-crd] telepít, majd kezeli a függőségeket az Istio ezen konfigurációjához definiált összes releváns objektum telepítéséhez. A következő kimeneti kódrészlethez hasonló tetszőséget kell látnia.

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

Ezen a ponton telepítette az Istio-t az AKS-fürtre. Annak érdekében, hogy van egy sikeres telepítése Istio, térjünk át a következő [szakaszban érvényesítése Istio telepítés](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Az Istio-telepítés ellenőrzése

Először ellenőrizze, hogy a várt szolgáltatások létrejöttek-e. Használja a [kubectl get svc][kubectl-get] parancsot a futó szolgáltatások megtekintéséhez. A `istio-system` névtér lekérdezése, ahol az Istio és a `istio` bővítmény összetevőit telepítette a Helm diagram:

```console
kubectl get svc --namespace istio-system --output wide
```

A következő példa kimeneti mutatja a szolgáltatások, amelyek most futnak:

- `istio-*`Szolgáltatások
- `jaeger-*`, `tracing`, `zipkin` és kiegészítő nyomkövetési szolgáltatások
- `prometheus`bővítménymérőszámok szolgáltatás
- `grafana`bővítményelemzési és figyelési irányítópult-szolgáltatás
- `kiali`bővítményszolgáltatás hálójának irányítópult-szolgáltatása

Ha `istio-ingressgateway` a külső ip-címét `<pending>`jeleníti meg, várjon néhány percet, amíg az Azure-hálózat egy IP-címet hozzárendel.

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

Ezután ellenőrizze, hogy a szükséges podok jöttek létre. Használja a [kubectl get pods][kubectl-get] parancsot, és ismét lekérdezi a `istio-system` névteret:

```console
kubectl get pods --namespace istio-system
```

A következő példa kimeneti mutatja a podok, amelyek futnak:

- a `istio-*` hüvelyek
- a `prometheus-*` bővítmény mérőszámok pod
- a `grafana-*` kiegészítő elemzési és monitoring műszerfal pod
- a `kiali` kiegészítő szolgáltatás hálójának irányítópult-podja

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

Az összes hüvelynek meg kell `Running`jelennie. Ha a hüvelyek nem rendelkeznek ezekkel az állapotokkal, várjon egy-két percet, amíg meg nem teszik. Ha bármelyik podok jelentést egy probléma, használja a [kubectl írja pod][kubectl-describe] parancsot, hogy tekintse át a kimeneti és állapoti.

## <a name="accessing-the-add-ons"></a>A bővítmények elérése

Az Istio számos olyan bővítményt telepített a fenti beállításban, amelyek további funkciókat biztosítanak. A bővítmények webes alkalmazásai **nem** nyilvánosan, külső IP-címen keresztül vannak elérhetővé. 

A bővítmény felhasználói felületének eléréséhez `istioctl dashboard` használja a parancsot. Ez a parancs [kubectl port-forward][kubectl-port-forward] és egy véletlenszerű portot használ, hogy biztonságos kapcsolatot hozzon létre az ügyfélgép és az AKS-fürt megfelelő podja között. Ezután automatikusan megnyitja a bővítmény webalkalmazást az alapértelmezett böngészőben.

A grafanai és a kiali további biztonsági réteget adtunk hozzá a cikk korábbi részében a hitelesítő adatok megadásával.

### <a name="grafana"></a>Grafana

Az Istio elemzési és figyelési irányítópultjait a [Grafana][grafana]biztosítja. Ne felejtse el használni a Grafana titkos ítin keresztül létrehozott hitelesítő adatokat korábban, amikor a rendszer kéri. Nyissa meg a Grafana műszerfalat biztonságosan az alábbiak szerint:

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Az Istio mutatóit a [Prometheus][prometheus]biztosítja. Nyissa meg biztonságosan a Prometheus műszerfalat az alábbiak szerint:

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Az Istio-n belüli nyomon követést [jaeger][jaeger]biztosítja. Nyissa meg a Jaeger műszerfalat biztonságosan az alábbiak szerint:

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali között

A [Kiali][kiali]szolgáltatásháló-megfigyelhetőségi irányítópultot biztosít. Ne feledje, hogy használja a hitelesítő adatokat létrehozott keresztül Kiali titkos korábban, amikor kéri. Nyissa meg a Kiali műszerfalat biztonságosan az alábbiak szerint:

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Egy egyszerű felület a [követ][envoy] proxyk áll rendelkezésre. Konfigurációs információkat és metrikákat biztosít egy adott podban futó envoy proxyhoz. Nyissa meg biztonságosan az Envoy felületet az alábbiak szerint:

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Istio eltávolítása az AKS-ből

> [!WARNING]
> Istio törlése egy futó rendszer ből a szolgáltatások közötti forgalommal kapcsolatos problémákat okozhat. A folytatás előtt győződjön meg arról, hogy rendelkezik arról, hogy a rendszer továbbra is megfelelően működjön Istio nélkül.

### <a name="remove-istio-components-and-namespace"></a>Istio-összetevők és névtér eltávolítása

Az Istio eltávolítása az AKS-fürtből, használja a `istioctl manifest generate` parancsot az `istio.aks.yaml` Istio vezérlősík specifikációs fájl. Ez létrehozza az üzembe helyezett jegyzéket, `kubectl delete` amelyet az összes telepített összetevő `istio-system` és a névtér eltávolítása érdekében hozunk létre.

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>Istio CRD-k és titkok eltávolítása

A fenti parancsok törlik az összes Istio-összetevőt és névteret, de még mindig maradnak a generált Istio titkok. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>További lépések

A következő dokumentáció bemutatja, hogyan használhatja az Istio-t intelligens útválasztásra egy kanári-kiadás bevezetéséhez:

> [!div class="nextstepaction"]
> [AKS Istio intelligens útválasztási forgatókönyv][istio-scenario-routing]

Az Istio további telepítési és konfigurációs lehetőségeinek megismeréséhez olvassa el a következő hivatalos Istio-útmutatót:

- [Istio - telepítési útmutatók][istio-installation-guides]

További forgatókönyveket is követhet a következő használatával:

- [Például Istio Bookinfo alkalmazás][istio-bookinfo-example]

Az AKS-alkalmazás Figyelése az Application Insights és az Istio használatával című témakörben a következő Azure Monitor-dokumentációban olvashat:

- [Nulla instrumentation alkalmazás figyelése Kubernetes üzemeltetett alkalmazások][app-insights]

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

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
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
