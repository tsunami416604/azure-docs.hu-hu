---
title: Az Azure Kubernetes Service (AKS) Istio telepítése
description: Ismerje meg, hogyan telepítheti és ise Istio szolgáltatás létrehozása az Azure Kubernetes Service (AKS)-fürt háló
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: ecabb139b79494e8a62687a5634d9865d578c5c6
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104394"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Telepítheti és használhatja Istio Azure Kubernetes Service (AKS)

[Istio] [ istio-github] van egy nyílt forráskódú service háló, amely kínál a legfontosabb funkciók között a mikroszolgáltatások, Kubernetes-fürtben. Ilyen például a forgalomkezelést, felügyeltszolgáltatás-identitás és a biztonsági, a házirend betartatása és observability. Istio kapcsolatos további információkért tekintse meg a hivatalos [Istio mi?] [ istio-docs-concepts] dokumentációját.

Ez a cikk bemutatja, hogyan Istio telepítéséhez. A Istio `istioctl` bináris ügyfél települ az ügyfélszámítógépen, akkor az Istio összetevői telepítve vannak, egy Kubernetes-fürtöt az aks-en. Ezek az utasítások hivatkozhat Istio verzió *1.0.4-es*. További Istio verziók annak [GitHub - Istio kiadásokban][istio-github-releases].

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Töltse le a Istio
> * A bináris Istio ügyfél telepítése
> * A Istio Kubernetes összetevőinek telepítése
> * A telepítés ellenőrzése

## <a name="before-you-begin"></a>Előkészületek

Ebben a cikkben részletes lépései azt feltételezik, hogy létrehozott egy AKS-fürt (Kubernetes 1.10 és újabb, az RBAC engedélyezve), és kiépített egy `kubectl` kapcsolatot a fürttel. Ha ezek az elemek bármelyikét segítségre van szüksége, tekintse meg a [AKS gyors][aks-quickstart].

Istio telepítéséhez szükséges [Helm] [ helm] verzió *2.10.0* vagy később megfelelően telepítette és konfigurálta a fürtben. Ha a telepítése Helm segítségre van szüksége, tekintse meg a [AKS Helm telepítéssel kapcsolatos útmutató][helm-install]. Ha nincs legalább verzió *2.10.0* Helm telepített, frissítési, vagy tekintse meg a [Istio – telepítési útmutató Helm-] [ istio-install-helm] egyéb telepítési lehetőségekért.

Ez a cikk elkülöníti a Istio telepítéssel kapcsolatos útmutató több különálló lépésekre. Hogy Istio telepítésének módjáról, valamint megtudhatja, hogyan Istio a Kubernetes-szel használható egyes lépéseket ismertetjük. A végeredmény megegyezik a hivatalos Istio telepítése struktúra [útmutatást][istio-install-k8s-quickstart].

## <a name="download-istio"></a>Töltse le a Istio

Először töltse le és csomagolja ki a legújabb Istio kiadást. A lépések kissé eltérő, MacOS, Linux vagy a Linux Windows alrendszere rendszerhéjakba, és a egy PowerShell shell. Válassza ki az Ön által preferált környezetben lépések a következő telepítési egyikét:

* [Bash on MacOS, Linux vagy Windows-alrendszer linuxhoz](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

MacOS rendszeren használja `curl` töltse le a legújabb Istio kiadásra, és bontsa ki a `tar` módon:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

Linux vagy a Linux Windows alrendszere használata `curl` töltse le a legújabb Istio kiadásra, és bontsa ki a `tar` módon:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

### <a name="powershell"></a>PowerShell

A PowerShellben használja [Invoke-WebRequest] [ Invoke-WebRequest] töltse le a legújabb Istio kiadásra, és bontsa ki a [Expand-archívum] [ Expand-Archive] , a következő:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.0.4"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

## <a name="install-the-istio-client-binary"></a>A bináris Istio ügyfél telepítése

A `istioctl` ügyfél bináris az ügyfélgépen futtatja, és kezelheti a Istio útválasztási szabályokat és szabályzatokat. A telepítési lépéseket ismét kissé eltérőek ügyfél operációs rendszerek között. Válasszon egyet az alábbi telepítési lépéseket az Ön által preferált környezetben.

> [!IMPORTANT]
> Futtassa a hátralévő lépéseket a legfelső szintű mappát a Istio kiadás, amely a letöltött és kibontott az előző szakaszban.

### <a name="macos"></a>MacOS

A Istio telepítéséhez `istioctl` ügyfél bináris fájlt egy bash-alapú rendszerhéj MacOS rendszeren használja az alábbi parancsokat. Ezek a parancsok másolása a `istioctl` bináris az általános jogú felhasználói program helyre az ügyfél a `PATH`.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Ha szeretné, hogy a Istio parancssori készültségi `istioctl` ügyfél bináris, majd azt az alábbiak szerint állíthatja:

```bash
# Generate the bash completion file and source it in your current shell
istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Most már az szakasz mehet [Istio Kubernetes összetevőinek telepítését](#install-the-istio-kubernetes-components).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux vagy a Linux Windows alrendszere

Használja az alábbi parancsokat a Istio telepítéséhez `istioctl` ügyfél bináris egy bash-alapú rendszerhéj, Linux vagy [a Linux Windows alrendszere][install-wsl]. Ezek a parancsok másolása a `istioctl` bináris az általános jogú felhasználói program helyre az ügyfél a `PATH`.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Ha szeretné, hogy a Istio parancssori készültségi `istioctl` ügyfél bináris, majd azt az alábbiak szerint állíthatja:

```bash
# Generate the bash completion file and source it in your current shell
istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Most már az szakasz mehet [Istio Kubernetes összetevőinek telepítését](#install-the-istio-kubernetes-components).

### <a name="windows"></a>Windows

A Istio telepítéséhez `istioctl` ügyfél bináris egy Powershell-alapú rendszerhéj a Windows, használja a következő parancsokat. Ezek a parancsok másolása a `istioctl` egy új felhasználóhoz ügyfél bináris helyét a program, és keresztül elérhető legyen a `PATH`.

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:/Program Files/Istio"
mv ./bin/istioctl.exe "C:/Program Files/Istio/"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Program Files\Istio\", "User")
```

## <a name="install-the-istio-kubernetes-components"></a>A Istio Kubernetes összetevőinek telepítése

Telepítse a Istio összetevőket az AKS-fürt, használja a Helm. A Istio erőforrásokhoz történő telepítéséhez a `istio-system` névteret, és a biztonsággal és figyeléssel a következő további beállításainak engedélyezése:

```azurecli
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set grafana.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

A Helm-diagram nagy számú objektumok helyez üzembe. Az üzembe helyezés befejezéséhez, a fürt környezettől függően 2-3 percig is eltarthat.

## <a name="validate-the-installation"></a>A telepítés ellenőrzése

Győződjön meg arról, hogy rendelkezik-e Istio sikeres telepítéséhez, hogy biztosítsa a telepítés.

Először győződjön meg arról, hogy létrejöttek-e a várt szolgáltatásokat. Használja a [kubectl get svc] [ kubectl-get] parancsot a futó szolgáltatások megtekintéséhez. Lekérdezés a *istio-rendszer* névteret, ahol a Istio és a kiegészítő szolgáltatás összetevőit a Helm-diagram telepítette:

```console
kubectl get svc --namespace istio-system --output wide
```

Az alábbi példa kimenetében látható, hogy most már futnia kell a szolgáltatásokat:

- *istio -** szolgáltatások
- * jaeger-**, *nyomkövetés*, és *zipkin* kiegészítő nyomkövetés szolgáltatások
- *prometheus* bővítmény metrikaszolgáltatás
- *grafana* bővítmény elemzési és figyelési irányítópult szolgáltatás
- *kiali* irányítópult háló kiegészítő szolgáltatások

Ha a `istio-ingressgateway` egy külső ip-címe látható `<pending>`, várjon néhány percet, amíg által az Azure-hálózatok IP-cím hozzá lett rendelve.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                   AGE       SELECTOR
grafana                  ClusterIP      10.0.26.60     <none>           3000/TCP                                                                                                                  3m        app=grafana
istio-citadel            ClusterIP      10.0.88.87     <none>           8060/TCP,9093/TCP                                                                                                         3m        istio=citadel
istio-egressgateway      ClusterIP      10.0.115.115   <none>           80/TCP,443/TCP                                                                                                            3m        app=istio-egressgateway,istio=egressgateway
istio-galley             ClusterIP      10.0.104.183   <none>           443/TCP,9093/TCP                                                                                                          3m        istio=galley
istio-ingressgateway     LoadBalancer   10.0.12.216    52.187.250.239   80:31380/TCP,443:31390/TCP,31400:31400/TCP,15011:30469/TCP,8060:31999/TCP,853:31235/TCP,15030:32000/TCP,15031:30293/TCP   3m        app=istio-ingressgateway,istio=ingressgateway
istio-pilot              ClusterIP      10.0.38.134    <none>           15010/TCP,15011/TCP,8080/TCP,9093/TCP                                                                                     3m        istio=pilot
istio-policy             ClusterIP      10.0.253.81    <none>           9091/TCP,15004/TCP,9093/TCP                                                                                               3m        istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.181.186   <none>           443/TCP                                                                                                                   3m        istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.177.113   <none>           9091/TCP,15004/TCP,9093/TCP,42422/TCP                                                                                     3m        istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                3m        app=jaeger
jaeger-collector         ClusterIP      10.0.112.81    <none>           14267/TCP,14268/TCP                                                                                                       3m        app=jaeger
jaeger-query             ClusterIP      10.0.179.193   <none>           16686/TCP                                                                                                                 3m        app=jaeger
kiali                    ClusterIP      10.0.211.63    <none>           20001/TCP                                                                                                                 3m        app=kiali
prometheus               ClusterIP      10.0.70.113    <none>           9090/TCP                                                                                                                  3m        app=prometheus
tracing                  ClusterIP      10.0.139.121   <none>           80/TCP                                                                                                                    3m        app=jaeger
zipkin                   ClusterIP      10.0.60.155    <none>           9411/TCP                                                                                                                  3m        app=jaeger
```

Ezután erősítse meg, hogy létrejöttek-e a szükséges podok. Használja a [kubectl get pods] [ kubectl-get] parancsot, majd újra lekérdezése a *istio-rendszer* névtér:

```console
kubectl get pods --namespace istio-system
```

Az alábbi példa kimenetében látható, a futtató podok:

- a *istio -** podok
- a *prometheus -** bővítmény metrikák pod
- a *grafana -** bővítmény elemzési és figyelési irányítópult pod
- a *kiali* kiegészítő szolgáltatás háló irányítópult pod

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-59b787b9b-cr6d7                  1/1       Running     0          6m
istio-citadel-78df8c67d9-9lfpf           1/1       Running     0          6m
istio-egressgateway-6b96cd7f5-k848h      1/1       Running     0          6m
istio-galley-58f566cb66-8mhbv            1/1       Running     0          6m
istio-ingressgateway-6cbbf596f6-6jz8g    1/1       Running     0          6m
istio-pilot-8449d555fc-sl6kp             2/2       Running     0          6m
istio-policy-6b99d88bc5-55s52            2/2       Running     0          6m
istio-sidecar-injector-b88dfb954-8m86s   1/1       Running     0          6m
istio-telemetry-675cb4cb9d-8s7wd         2/2       Running     0          6m
istio-tracing-7596597bd7-sbnt9           1/1       Running     0          6m
kiali-5fbd6ffb-4qcxw                     1/1       Running     0          6m
prometheus-76db5fddd5-2tkxs              1/1       Running     0          6m
```

Az összes a podok állapotát jelenítik meg `Running`. Ha a podok még nem rendelkezik a fenti állapotok megjelenése, várja meg egy-két percet tesznek. Ha bármely podok jelentéséhez, használja a [kubectl ismertetik a pod] [ kubectl-describe] parancs állapotát, valamint a kimeneti.

## <a name="accessing-the-add-ons"></a>A bővítmények elérése

A megvásárolt bővítmények számától megtörtént-e a telepítés a fenti, amely olyan további funkciókat biztosítanak a Istio. A bővítmények a felhasználói felületek nem érhetőek nyilvánosan egy külső ip-cím. A kiegészítő felhasználói felületek elérése a [kubectl port-továbbító] [ kubectl-port-forward] parancsot. Ez a parancs az AKS-fürt az ügyfélszámítógép helyi port és a megfelelő pod közötti biztonságos kapcsolatot hoz létre.

### <a name="grafana"></a>Grafana

Az analitikai és monitorozási irányítópultokat, Istio nyújtanak az [Grafana][grafana]. A helyi portra továbbítja *3000* port az ügyfélgépen *3000* a a pod, amely futtatja a Grafana az AKS-fürt:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

Az alábbi példa kimenetében látható, a Grafana konfigurált port-továbbító:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Most már elérheti a következő URL-címen a Grafana az ügyfélszámítógép - [ http://localhost:3000 ](http://localhost:3000).

### <a name="prometheus"></a>Prometheus

A kifejezés böngészőben a metrikákhoz által biztosított [Prometheus][prometheus]. A helyi portra továbbítja *9090* port az ügyfélgépen *9090* be a pod, amely futtatja a Prometheus az AKS-fürt:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

Az alábbi példa kimenetében Prometheus konfigurált port előre látható:

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

Most már elérheti a Prometheus kifejezés böngészőben a következő URL-címen az ügyfélszámítógép - [ http://localhost:9090 ](http://localhost:9090).

### <a name="jaeger"></a>Jaeger

Nyomkövetés felhasználói felület által biztosított [Jaeger][jaeger]. A helyi portra továbbítja *16686* port az ügyfélgépen *16686* be a pod, amely futtatja a Jaeger az AKS-fürt:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

Az alábbi példa kimenetében Jaeger konfigurált port előre látható:

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

Most már elérheti a Jaeger nyomkövetés felhasználói felülete a következő URL-címen az ügyfélszámítógép - [ http://localhost:16686 ](http://localhost:16686).

### <a name="kiali"></a>Kiali

A háló observability irányítópultján által biztosított [Kiali][kiali]. A helyi portra továbbítja *20001* port az ügyfélgépen *20001* a a pod, amely futtatja a Kiali az AKS-fürt:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

Az alábbi példa kimenetében Kiali konfigurált port előre látható:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Most már elérheti a Kiali háló observability irányítópultján a következő URL-címen az ügyfélszámítógép - [ http://localhost:20001 ](http://localhost:20001).

Az alapértelmezett felhasználónév és a jelszó az Kiali irányítópult *username:admin / jelszó: admin*. Ezeket a hitelesítő adatokat is lehet konfigurálni a *kiali.dashboard.username* és *kiali.dashboard.passphrase* Helm értékeket.

## <a name="next-steps"></a>További lépések

Szeretné látni, hogyan használhatja Istio intelligens útválasztást az alkalmazás több verziói között, és egy canary kiadás bevezetése, a következő dokumentációban tekintheti meg:

> [!div class="nextstepaction"]
> [Az AKS Istio intelligens útválasztási forgatókönyve][istio-scenario-routing]

Ismerje meg az Istio további telepítési és konfigurációs lehetőségeket, tekintse meg a következő hivatalos Istio cikkeket:

- [Istio – Kubernetes rövid telepítési útmutatót][istio-install-k8s-quickstart]
- [Istio - Helm telepítési útmutató][istio-install-helm]
- [Istio - Helm-telepítési beállítások][istio-install-helm-options]

További forgatókönyvek használatával is követheti a [Istio Bookinfo alkalmazás például][istio-bookinfo-example].

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-k8s-quickstart]: https://istio.io/docs/setup/kubernetes/quick-start/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/helm-install/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10
[kubernetes-crd]: https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
[Invoke-WebRequest]: /powershell/module/microsoft.powershell.utility/invoke-webrequest
[Expand-Archive]: /powershell/module/Microsoft.PowerShell.Archive/Expand-Archive
