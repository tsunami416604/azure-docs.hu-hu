---
title: Az Azure Kubernetes Service (AKS) Istio telepítése
description: Ismerje meg, hogyan telepítheti és használhatja a Istio egy szolgáltatás háló létrehozása Azure Kubernetes Service (AKS)-fürtben
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: c7c234e181e10499e532436bfde05ed89bdc7d28
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67465688"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Telepítheti és használhatja Istio Azure Kubernetes Service (AKS)

[Istio][istio-github] is an open-source service mesh that provides a key set of functionality across the microservices in a Kubernetes cluster. These features include traffic management, service identity and security, policy enforcement, and observability. For more information about Istio, see the official [What is Istio?][istio-docs-concepts] dokumentációját.

Ez a cikk bemutatja, hogyan Istio telepítéséhez. A Istio `istioctl` bináris ügyfél telepítve van az ügyfélszámítógép és az Istio összetevői telepítve vannak, egy Kubernetes-fürtöt az aks-en.

> [!NOTE]
> Ezek az utasítások hivatkozhat Istio verzió `1.1.3`.
>
> A Istio `1.1.x` kiadásokban a Kubernetes-verzió alapján Istio csapat által tesztelt `1.11`, `1.12`, `1.13`. További Istio verziók annak [GitHub - Istio kiadásokban][istio-github-releases] and information about each of the releases at [Istio - Release Notes][istio-release-notes].

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Töltse le a Istio
> * A bináris Istio istioctl ügyfél telepítése
> * Telepítse a Istio renderelési szótárak az aks-en
> * Az aks-en a Istio összetevőinek telepítése
> * A Istio a telepítés ellenőrzése
> * A bővítmények elérése
> * Az AKS Istio eltávolítása

## <a name="before-you-begin"></a>Előkészületek

Ebben a cikkben részletes lépései azt feltételezik, hogy létrehozott egy AKS-fürt (Kubernetes `1.11` és újabb, az RBAC engedélyezve), és kiépített egy `kubectl` kapcsolatot a fürttel. Ha ezek az elemek bármelyikét segítségre van szüksége, tekintse meg a [AKS gyors][aks-quickstart].

Szüksége lesz [Helm][helm] kövesse ezeket az utasításokat, és Istio telepítéséhez. Javasoljuk, hogy a verzió `2.12.2` vagy később megfelelően telepítette és konfigurálta a fürtben. Ha a telepítése Helm segítségre van szüksége, tekintse meg a [AKS Helm telepítéssel kapcsolatos útmutató][helm-install]. Az összes Istio podok is kell ütemezni Linux-csomópontokon való futtatáshoz.

Ez a cikk elkülöníti a Istio telepítéssel kapcsolatos útmutató több különálló lépésekre. A végeredmény megegyezik a hivatalos Istio telepítése struktúra [útmutatást][istio-install-helm].

## <a name="download-istio"></a>Töltse le a Istio

Először töltse le és csomagolja ki a legújabb Istio kiadást. A lépések kissé eltérőek a bash felületet MacOS, Linux vagy Windows alrendszer Linux rendszeren, és a egy PowerShell parancshéj. Válasszon egyet az alábbi telepítési lépéseket, amely megfelel az Ön által preferált környezetben:

* [Bash on MacOS, Linux vagy Windows-alrendszer linuxhoz](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

MacOS rendszeren használja `curl` töltse le a legújabb Istio kiadásra, és bontsa ki a `tar` módon:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

Linux vagy a Linux Windows alrendszere használata `curl` töltse le a legújabb Istio kiadásra, és bontsa ki a `tar` módon:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Most már az szakasz mehet [bináris Istio istioctl ügyfél telepítése](#install-the-istio-istioctl-client-binary).

### <a name="powershell"></a>PowerShell

A PowerShellben használja `Invoke-WebRequest` töltse le a legújabb Istio kiadásra, és bontsa ki a `Expand-Archive` módon:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
# Use TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Most már az szakasz mehet [bináris Istio istioctl ügyfél telepítése](#install-the-istio-istioctl-client-binary).

## <a name="install-the-istio-istioctl-client-binary"></a>A bináris Istio istioctl ügyfél telepítése

> [!IMPORTANT]
> Győződjön meg arról, hogy futtatja a lépéseket ebben a szakaszban a letöltött és kibontott Istio kiadás a legfelső szintű mappából.

A `istioctl` ügyfél bináris az ügyfélgépen futtatja, és lehetővé teszi, hogy a Istio szolgáltatás háló kezelése. A telepítési lépések kissé eltérőek ügyfél operációs rendszerek között. Válasszon egyet az alábbi telepítési lépéseket, amely megfelel az Ön által preferált környezetben:

* [MacOS](#macos)
* [Linux vagy a Linux Windows alrendszere](#linux-or-windows-subsystem-for-linux)
* [Windows](#windows)

### <a name="macos"></a>MacOS

A Istio telepítéséhez `istioctl` ügyfél bináris fájlt egy bash-alapú rendszerhéj MacOS rendszeren használja az alábbi parancsokat. Ezek a parancsok másolása a `istioctl` bináris az általános jogú felhasználói program helyre az ügyfél a `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Ha szeretné, hogy a Istio parancssori készültségi `istioctl` ügyfél bináris, majd azt az alábbiak szerint állíthatja:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Most már továbbléphet a következő szakaszban [telepítse a Istio renderelési szótárak AKS](#install-the-istio-crds-on-aks).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux vagy a Linux Windows alrendszere

Használja az alábbi parancsokat a Istio telepítéséhez `istioctl` ügyfél bináris egy bash-alapú rendszerhéj, Linux vagy [a Linux Windows alrendszere][install-wsl]. Ezek a parancsok másolása a `istioctl` bináris az általános jogú felhasználói program helyre az ügyfél a `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Ha szeretné, hogy a Istio parancssori készültségi `istioctl` ügyfél bináris, majd azt az alábbiak szerint állíthatja:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Most már továbbléphet a következő szakaszban [telepítse a Istio renderelési szótárak AKS](#install-the-istio-crds-on-aks).

### <a name="windows"></a>Windows

A Istio telepítéséhez `istioctl` bináris az ügyfél egy **Powershell**-alapú rendszerhéj a Windows, használja a következő parancsokat. Ezek a parancsok másolása a `istioctl` ügyfél bináris Istio mappába, és lehetővé teszi tartósan keresztül a `PATH`. Nincs szükség emelt szintű (rendszergazdai) engedélyekkel, futtassa a következő parancsokat.

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Istio\", "User")
```

Most már továbbléphet a következő szakaszban [telepítse a Istio renderelési szótárak AKS](#install-the-istio-crds-on-aks).

## <a name="install-the-istio-crds-on-aks"></a>Telepítse a Istio renderelési szótárak az aks-en

> [!IMPORTANT]
> Győződjön meg arról, hogy futtatja a lépéseket ebben a szakaszban a letöltött és kibontott Istio kiadás a legfelső szintű mappából.

Istio használ [egyéni erőforrás-definíciókban (renderelési szótárak)][kubernetes-crd] kezelheti a futtatókörnyezet konfigurációját. Először telepíteni a Istio renderelési szótárak, mivel a Istio-összetevők függőségi rajtuk kell. Használja a Helm és a `istio-init` diagram a Istio renderelési szótárak történő telepítéséhez a `istio-system` névtér az AKS-fürt:

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[Feladatok][kubernetes-jobs] részeként üzembe helyezett a `istio-init` Helm-diagramot a renderelési szótárak telepítéséhez. Ezek a feladatok között fürt a környezettől függően 1 – 2 percet vesz igénybe. Ellenőrizheti, hogy a feladat sikeresen befejeződött-e meg a következők szerint:

```azurecli
kubectl get jobs -n istio-system
```

Az alábbi példa kimenetében látható a sikeresen befejezett feladatok.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

Most, hogy a feladat sikeres befejezése jóváhagyta, hogy ellenőrizzük, hogy van-e a megfelelő számú Istio renderelési szótárak telepítve. Ellenőrizheti, hogy minden 53 Istio renderelési szótárak telepítve van-e a környezetének megfelelő parancs futtatásával. A parancs a számot adja vissza `53`.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

PowerShell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

Ha van erre a pontra, majd, amely azt jelenti, hogy sikeresen telepítette a Istio renderelési szótárak. Most már továbbléphet a következő szakaszban [Istio összetevőinek telepítését az aks-en](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Az aks-en a Istio összetevőinek telepítése

> [!IMPORTANT]
> Győződjön meg arról, hogy futtatja a lépéseket ebben a szakaszban a letöltött és kibontott Istio kiadás a legfelső szintű mappából.

Azt fogjuk telepíteni [Grafana][grafana] and [Kiali][kiali] a Istio telepítésének részeként. Grafana biztosít analitikai és monitorozási irányítópultokat, és Kiali tartalmaz egy szolgáltatás háló observability irányítópultot. A telepítő az egyes összetevők szükséges hitelesítő adatokat, amelyeket meg kell adni egy [titkos][kubernetes-titkok].

Azt is telepíthető a Istio összetevők, mielőtt azt a titkos kulcsok Grafana és Kiali kell létrehoznia. A környezetének megfelelő parancsok futtatásával hozzon létre a titkos adatokat.

### <a name="add-grafana-secret"></a>Add Grafana Secret

Cserélje le a `REPLACE_WITH_YOUR_SECURE_PASSWORD` jelszavát a jogkivonatot, és futtassa a következő parancsokat:

#### <a name="macos-linux"></a>MacOS, Linux

```bash
GRAFANA_USERNAME=$(echo -n "grafana" | base64)
GRAFANA_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$GRAFANA_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("grafana"))
$GRAFANA_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE" | kubectl apply -f -
```

### <a name="add-kiali-secret"></a>Add Kiali Secret

Cserélje le a `REPLACE_WITH_YOUR_SECURE_PASSWORD` jelszavát a jogkivonatot, és futtassa a következő parancsokat:

#### <a name="macos-linux"></a>MacOS, Linux

```bash
KIALI_USERNAME=$(echo -n "kiali" | base64)
KIALI_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$KIALI_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("kiali"))
$KIALI_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE" | kubectl apply -f -
```

### <a name="install-istio-components"></a>Istio összetevőinek telepítése

Most, hogy az AKS-fürt sikeresen létrehoztuk a Grafana és Kiali titkos kulcsait, azt az idő a Istio összetevők telepítéséhez. Használja a Helm és a `istio` diagram be Istio összetevőinek telepítését a `istio-system` névtér az AKS-fürt. Használja a megfelelő parancsokat környezete számára.

> [!NOTE]
> A telepítés részeként használjuk a következő beállításokat:
> - `global.controlPlaneSecurityEnabled=true` -kölcsönös TLS a vezérlősík engedélyezve
> - `mixer.adapters.useAdapterCRDs=false` -a mixer adapteren renderelési szótárak órák eltávolítani, mert azok elavulttá válik, és ez növeli a teljesítményt
> - `grafana.enabled=true` -elemzéshez a Grafana üzembe helyezés engedélyezése és figyelést
> - `grafana.security.enabled=true` -a Grafana hitelesítés engedélyezése
> - `tracing.enabled=true` -nyomkövetés Jaeger központi telepítés engedélyezése
> - `kiali.enabled=true` – a háló observability szolgáltatási irányítópult Kiali központi telepítés engedélyezése

Bash

```bash
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set mixer.adapters.useAdapterCRDs=false \
  --set grafana.enabled=true --set grafana.security.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

PowerShell

```powershell
helm install install/kubernetes/helm/istio --name istio --namespace istio-system `
  --set global.controlPlaneSecurityEnabled=true `
  --set mixer.adapters.useAdapterCRDs=false `
  --set grafana.enabled=true --set grafana.security.enabled=true `
  --set tracing.enabled=true `
  --set kiali.enabled=true
```

A `istio` Helm-diagramot helyez üzembe egy nagy számú objektumok. Láthatja, hogy a lista és a kimenetét a `helm install` a fenti parancsot. Az összetevők telepítési állapotát a Istio fürt a környezettől függően 4 – 5 percet is igénybe vehet.

> [!NOTE]
> Az összes Istio podok Linux-csomópontokon való futtatáshoz kell ütemezni. Ha a Windows Server csomópontkészletek mellett Linux csomópontkészleteit a fürtön, győződjön meg arról, hogy az összes Istio podok Linux-csomópontokon való futáshoz van ütemezve.

Ezen a ponton Istio telepítette az AKS-fürt. Győződjön meg arról, hogy van-e Istio sikeres telepítéséhez, a továbbvezet a következő szakaszban [Istio telepítésének ellenőrzése](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>A Istio a telepítés ellenőrzése

Először győződjön meg arról, hogy létrejöttek-e a várt szolgáltatásokat. Használja a [kubectl get svc][kubectl-get] parancsot a futó szolgáltatások megtekintéséhez. Lekérdezés a `istio-system` névteret, ahol az Istio és a bővítmény összetevők telepítette a `istio` Helm-diagramot:

```console
kubectl get svc --namespace istio-system --output wide
```

Az alábbi példa kimenetében látható, hogy most már futnia kell a szolgáltatásokat:

- `istio-*` Szolgáltatások
- `jaeger-*`, `tracing`, és `zipkin` kiegészítő nyomkövetés szolgáltatások
- `prometheus` a metrikaszolgáltatás bővítmény
- `grafana` bővítmény elemzési és figyelési irányítópult szolgáltatás
- `kiali` kiegészítő szolgáltatások háló irányítópult

Ha a `istio-ingressgateway` egy külső ip-címe látható `<pending>`, várjon néhány percet, amíg által az Azure-hálózatok IP-cím hozzá lett rendelve.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                                                                                                                      AGE       SELECTOR
grafana                  ClusterIP      10.0.81.182    <none>          3000/TCP                                                                                                                                     119s      app=grafana
istio-citadel            ClusterIP      10.0.96.33     <none>          8060/TCP,15014/TCP                                                                                                                           119s      istio=citadel
istio-galley             ClusterIP      10.0.237.158   <none>          443/TCP,15014/TCP,9901/TCP                                                                                                                   119s      istio=galley
istio-ingressgateway     LoadBalancer   10.0.154.12    20.188.211.19   15020:30603/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:31198/TCP,15030:30610/TCP,15031:30937/TCP,15032:31344/TCP,15443:31499/TCP   119s      app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.178.56    <none>          15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       119s      istio=pilot
istio-policy             ClusterIP      10.0.116.118   <none>          9091/TCP,15004/TCP,15014/TCP                                                                                                                 119s      istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.31.160    <none>          443/TCP                                                                                                                                      119s      istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.187.246   <none>          9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       119s      istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>          5775/UDP,6831/UDP,6832/UDP                                                                                                                   119s      app=jaeger
jaeger-collector         ClusterIP      10.0.116.63    <none>          14267/TCP,14268/TCP                                                                                                                          119s      app=jaeger
jaeger-query             ClusterIP      10.0.22.108    <none>          16686/TCP                                                                                                                                    119s      app=jaeger
kiali                    ClusterIP      10.0.142.50    <none>          20001/TCP                                                                                                                                    119s      app=kiali
prometheus               ClusterIP      10.0.138.134   <none>          9090/TCP                                                                                                                                     119s      app=prometheus
tracing                  ClusterIP      10.0.165.210   <none>          80/TCP                                                                                                                                       118s      app=jaeger
zipkin                   ClusterIP      10.0.126.211   <none>          9411/TCP                                                                                                                                     118s      app=jaeger
```

Ezután erősítse meg, hogy létrejöttek-e a szükséges podok. Használja a [kubectl get pods][kubectl-get] parancsot, majd újra lekérdezése a `istio-system` névteret:

```console
kubectl get pods --namespace istio-system
```

Az alábbi példa kimenetében látható, a futtató podok:

- a `istio-*` podok
- a `prometheus-*` bővítmény metrikák pod
- a `grafana-*` bővítmény elemzési és figyelési irányítópult pod
- a `kiali` kiegészítő szolgáltatás háló irányítópult pod

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-88779954d-nzpm7                  1/1       Running     0          6m26s
istio-citadel-7f699dc8c8-n7q8g           1/1       Running     0          6m26s
istio-galley-649bc8cd97-wfjzm            1/1       Running     0          6m26s
istio-ingressgateway-65dfbd566-42wkn     1/1       Running     0          6m26s
istio-init-crd-10-tmtw5                  0/1       Completed   0          20m38s
istio-init-crd-11-ql25l                  0/1       Completed   0          20m38s
istio-pilot-958dd8cc4-4ckf9              2/2       Running     0          6m26s
istio-policy-86b4b7cf9-zf7v7             2/2       Running     4          6m26s
istio-sidecar-injector-d48786c5c-pmrj9   1/1       Running     0          6m26s
istio-telemetry-7f6996fdcc-84w94         2/2       Running     3          6m26s
istio-tracing-79db5954f-h7hmz            1/1       Running     0          6m26s
kiali-5c4cdbb869-s28dv                   1/1       Running     0          6m26s
prometheus-67599bf55b-pgxd8              1/1       Running     0          6m26s
```

Meg kell adni két `istio-init-crd-*` a podok egy `Completed` állapotát. Ezek a podok is elvégzi a feladatokat a renderelési szótárak korábbi lépésben létrehozott felelős. Az összes többi a podok állapotúnak kell lennie egy a `Running`. Ha a podok még nem rendelkezik a fenti állapotok megjelenése, várja meg egy-két percet tesznek. Ha bármely podok jelentéséhez, használja a [kubectl ismertetik a pod][kubectl-describe] parancs állapotát, valamint a kimeneti.

## <a name="accessing-the-add-ons"></a>A bővítmények elérése

A megvásárolt bővítmények számától megtörtént-e a telepítés a fenti, amely olyan további funkciókat biztosítanak a Istio. A bővítmények a felhasználói felületek nem érhetőek nyilvánosan egy külső ip-cím. A kiegészítő felhasználói felületek elérése a [kubectl port-továbbító][kubectl-port-forward] parancsot. Ez a parancs az AKS-fürt az ügyfélszámítógép és a megfelelő pod közötti biztonságos kapcsolatot hoz létre.

Adja meg a hitelesítő adatok számukra a cikkben korábban a Grafana és Kiali hozzáadott egy további biztonsági réteget.

### <a name="grafana"></a>Grafana

Az elemzéseket és irányítópultokat monitoringjának Istio által biztosított [Grafana][grafana]. A helyi portra továbbítja `3000` port az ügyfélgépen `3000` a a pod, amely futtatja a Grafana az AKS-fürt:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

Az alábbi példa kimenetében látható, a Grafana konfigurált port-továbbító:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Most már elérheti a következő URL-címen a Grafana az ügyfélszámítógép - [ http://localhost:3000 ](http://localhost:3000). Ne felejtse el a Grafana használatával titkos korábban létrehozott amikor a rendszer kéri a hitelesítő adatokat.

### <a name="prometheus"></a>Prometheus

Metrikák Istio által biztosított [Prometheus][prometheus]. A helyi portra továbbítja `9090` port az ügyfélgépen `9090` a a pod, amely futtatja a Prometheus az AKS-fürt:

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

Nyomkövetés belül Istio által biztosított [Jaeger][jaeger]. A helyi portra továbbítja `16686` port az ügyfélgépen `16686` a a pod, amely futtatja a Jaeger az AKS-fürt:

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

A háló observability irányítópultján által biztosított [Kiali][kiali]. A helyi portra továbbítja `20001` port az ügyfélgépen `20001` a a pod, amely futtatja a Kiali az AKS-fürt:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

Az alábbi példa kimenetében Kiali konfigurált port előre látható:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Most már elérheti a Kiali háló observability irányítópultján a következő URL-címen az ügyfélszámítógép - [ http://localhost:20001/kiali/console/ ](http://localhost:20001/kiali/console/). Ne felejtse el a Kiali keresztül titkos korábban létrehozott amikor a rendszer kéri a hitelesítő adatokat.

## <a name="uninstall-istio-from-aks"></a>Az AKS Istio eltávolítása

> [!WARNING]
> Forgalom Istio töröl egy működő rendszerben eredményezhet kapcsolatos problémákat a szolgáltatások között. Győződjön meg arról, hogy a rendszer továbbra is megfelelően működjön Istio nélkül a folytatás előtt rendelkezések végzett.

### <a name="remove-istio-components-and-namespace"></a>Istio összetevők és a névtér eltávolítása

Az AKS-fürt Istio eltávolításához használja a következő parancsokat. A `helm delete` -parancsok eltávolítanak a `istio` és `istio-init` diagramok, és a `kubectl delete ns` parancs eltávolítja a `istio-system` névtér.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Távolítsa el a renderelési Istio szótárak

A fenti parancsokat az Istio összetevők és a névtér törlése, de azt is marad, a Istio renderelési szótárak. A renderelési szótárak törléséhez használhatja, a következő módszerek.

Megközelítés #1 – Ez a parancs feltételezi, hogy ezt a lépést, amely a Istio telepítéséhez is használt Istio letöltött és kibontott kiadásában a legfelső szintű mappából futtatja.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Megközelítés #2 – használja az alábbi parancsok, ha már nincs hozzáférése a Istio telepítéséhez használt Istio letöltött és kibontott kiadása. Ez a parancs egy kicsit tovább - tart, ez eltarthat néhány percig.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

PowerShell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>További lépések

Az alábbi dokumentáció azt ismerteti, hogyan használható Istio vezethet be olyan canary kiadás intelligens útválasztást biztosít:

> [!div class="nextstepaction"]
> [Az AKS Istio intelligens útválasztási forgatókönyve][istio-scenario-routing]

Ismerje meg az Istio további telepítési és konfigurációs lehetőségeket, tekintse meg a következő hivatalos Istio cikkeket:

- [Istio - Helm telepítési útmutató][istio-install-helm]
- [Istio - Helm-telepítési beállítások][istio-install-helm-options]

További forgatókönyvek használatával is követheti a [Istio Bookinfo alkalmazás például][istio-bookinfo-example].

Megtudhatja, hogyan figyelheti az AKS-alkalmazást az Application Insights és Istio, tekintse meg a következő Azure Monitor dokumentációja ismerteti:
- [Nulla instrumentation alkalmazásfigyelés kubernetes üzemeltetett alkalmazások][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/about/notes/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
