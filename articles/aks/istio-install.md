---
title: A Istio telepítése az Azure Kubernetes szolgáltatásban (ak)
description: Ismerje meg, hogyan telepítheti és használhatja a Istio a Service Mesh Azure Kubernetes szolgáltatásbeli (ak-) fürtben való létrehozásához
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 9d973cb2ac210e912d93941a2f81889557379f43
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "67625976"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>A Istio telepítése és használata az Azure Kubernetes szolgáltatásban (ak)

A [Istio][istio-github] egy nyílt forráskódú szolgáltatás rácsvonala, amely a Kubernetes-fürtökön futó összes szolgáltatás egyik kulcsfontosságú készletét biztosítja. Ezek a szolgáltatások közé tartoznak a forgalomirányítás, a szolgáltatások identitása és a biztonság, a házirendek betartatása és a Megfigyelhetőség. A Istio kapcsolatos további információkért tekintse meg a hivatalos [Mi az Istio?][istio-docs-concepts] dokumentációt.

Ez a cikk bemutatja, hogyan telepítheti a Istio. A Istio `istioctl` -ügyfél bináris fájlja telepítve van az ügyfélszámítógépre, és a Istio-összetevők a Kubernetes-fürtön vannak telepítve az AK-ban.

> [!NOTE]
> Ezek az utasítások a Istio `1.1.3`-verzióra hivatkoznak.
>
> A Istio `1.1.x` `1.11`- `1.12` kiadásokataIstiocsapattesztelteaKubernetes`1.13`verzióiban,. További Istio-verziók a [GitHub-Istio kiadásokban][istio-github-releases] és az egyes kiadásokra vonatkozó információk a [Istio-kibocsátási megjegyzésekben][istio-release-notes]találhatók.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Istio letöltése
> * A Istio istioctl-ügyfél bináris fájljának telepítése
> * A Istio CRDs telepítése az AK-on
> * A Istio-összetevők telepítése az AK-on
> * A Istio telepítésének ellenőrzése
> * A bővítmények elérése
> * Istio eltávolítása az AK-ból

## <a name="before-you-begin"></a>Előkészületek

A cikkben részletezett lépések azt feltételezik, hogy létrehozott egy AK-fürtöt `1.11` (Kubernetes és újabb, RBAC engedélyezve), és létesítettek egy `kubectl` , a fürttel létesített kapcsolatokat. Ha segítségre van szüksége ezen elemek bármelyikével kapcsolatban, tekintse meg az [AK][aks-quickstart]gyors üzembe helyezését ismertető cikket.

Az utasítások követéséhez és a Istio telepítéséhez a [Helm][helm] szükséges. Javasoljuk, hogy a fürtben megfelelően `2.12.2` telepítse és konfigurálja a verziót. Ha segítségre van szüksége a Helm telepítéséhez, tekintse meg az [AK Helm telepítési útmutatóját][helm-install]. Az összes Istio-hüvelyt is be kell ütemezni a Linux-csomópontokon történő futtatásra.

Ez a cikk a Istio telepítési útmutatóját több különálló lépésben elkülöníti. A végeredmény ugyanaz, mint a hivatalos Istio telepítési [útmutatója][istio-install-helm].

## <a name="download-istio"></a>Istio letöltése

Először töltse le és bontsa ki a legújabb Istio-kiadást. A lépések némileg eltérnek a MacOS-, Linux-vagy Windows-alrendszereken futó bash-rendszerhéjon, valamint egy PowerShell-rendszerhéjon. Válasszon egyet a következő telepítési lépések közül, amelyek megfelelnek az Ön által választott környezetnek:

* [Bash MacOS, Linux vagy Windows rendszerű Linux rendszeren](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

MacOS rendszeren töltse le `curl` a legújabb Istio-kiadást, majd `tar` bontsa ki a következőt a paranccsal:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

Linux vagy Windows rendszerű Linux rendszeren a használatával `curl` letöltheti a legújabb Istio-kiadást, majd `tar` kinyerheti a következővel:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Most lépjen a szakaszra a [Istio istioctl-ügyfél bináris](#install-the-istio-istioctl-client-binary)fájljának telepítéséhez.

### <a name="powershell"></a>PowerShell

A PowerShellben a `Invoke-WebRequest` használatával töltse le a legújabb Istio-kiadást, `Expand-Archive` majd a következő módon bontsa ki a kivonatot:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
# Use TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Most lépjen a szakaszra a [Istio istioctl-ügyfél bináris](#install-the-istio-istioctl-client-binary)fájljának telepítéséhez.

## <a name="install-the-istio-istioctl-client-binary"></a>A Istio istioctl-ügyfél bináris fájljának telepítése

> [!IMPORTANT]
> Győződjön meg arról, hogy az ebben a szakaszban leírt lépéseket a letöltött és kibontott Istio-kiadás legfelső szintű mappájából futtatja.

Az `istioctl` ügyfél bináris fájlja az ügyfélszámítógépen fut, és lehetővé teszi a Istio szolgáltatás hálójának kezelését. A telepítési lépések némileg eltérnek az ügyféloldali operációs rendszerek között. Válasszon egyet a következő telepítési lépések közül, amelyek megfelelnek az Ön által választott környezetnek:

* [MacOS](#macos)
* [Linux vagy Windows alrendszer Linux rendszeren](#linux-or-windows-subsystem-for-linux)
* [Windows](#windows)

### <a name="macos"></a>MacOS

A Istio `istioctl` -ügyfél bináris fájljának MacOS rendszerű bash-alapú rendszerhéjból történő telepítéséhez használja a következő parancsokat. Ezek a parancsok az `istioctl` ügyfél bináris fájljait az általános jogú felhasználói program mappájába `PATH`másolják.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Ha a Istio `istioctl` -ügyfél bináris fájljának a parancssori befejezését szeretné beállítani, a következőképpen állítsa be:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Most lépjen a következő szakaszra, és [telepítse a Istio CRDs az AK-ra](#install-the-istio-crds-on-aks).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux vagy Windows alrendszer Linux rendszeren

A következő parancsokkal telepítheti a Istio `istioctl` -ügyfél bináris fájlját egy bash-alapú rendszerhéjban Linuxon vagy Linuxon futó [Windows][install-wsl]alrendszeren. Ezek a parancsok az `istioctl` ügyfél bináris fájljait az általános jogú felhasználói program mappájába `PATH`másolják.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Ha a Istio `istioctl` -ügyfél bináris fájljának a parancssori befejezését szeretné beállítani, a következőképpen állítsa be:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Most lépjen a következő szakaszra, és [telepítse a Istio CRDs az AK-ra](#install-the-istio-crds-on-aks).

### <a name="windows"></a>Windows

Ha a Istio `istioctl` -ügyfél bináris fájlját **PowerShell**-alapú rendszerhéjban szeretné telepíteni Windows rendszeren, használja a következő parancsokat. Ezek a parancsok az `istioctl` ügyfél bináris fájljait egy Istio mappába másolják, majd azonnal elérhetővé teszik (a jelenlegi rendszerhéjban) és véglegesen (a rendszerhéj újraindításakor `PATH`) a használatával. A parancsok futtatásához nincs szükség emelt szintű (rendszergazdai) jogosultságokra, és nem kell újraindítani a rendszerhéjat.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```

Most lépjen a következő szakaszra, és [telepítse a Istio CRDs az AK-ra](#install-the-istio-crds-on-aks).

## <a name="install-the-istio-crds-on-aks"></a>A Istio CRDs telepítése az AK-on

> [!IMPORTANT]
> Győződjön meg arról, hogy az ebben a szakaszban leírt lépéseket a letöltött és kibontott Istio-kiadás legfelső szintű mappájából futtatja.

A Istio [egyéni erőforrás-definíciókat (CRDs)][kubernetes-crd] használ a futásidejű konfigurációjának kezeléséhez. Először telepíteni kell a Istio-CRDs, mert a Istio-összetevők függőséggel rendelkeznek. A Helm és a `istio-init` diagram segítségével telepítse a Istio-CRDs az `istio-system` AK-fürt névterében:

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

A [feladatok][kubernetes-jobs] üzembe helyezése a `istio-init` Helm diagram részeként történik a CRDs telepítéséhez. Ezeknek a feladatoknak a fürt-környezettől függően 1 – 2 percet kell elvégezniük. A feladatok sikeres befejezését a következőképpen ellenőrizheti:

```azurecli
kubectl get jobs -n istio-system
```

A következő példa kimenete a sikeresen befejezett feladatokat jeleníti meg.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

Most, hogy megerősítettük a feladatok sikeres befejezését, ellenőrizze, hogy megfelelő számú Istio-CRDs van-e telepítve. A megfelelő parancs futtatásával ellenőrizheti, hogy az összes 53 Istio-CRDs telepítve van-e a környezetéhez. A parancsnak vissza kell adni `53`a számot.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

Powershell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

Ha erre a pontra van szüksége, akkor ez azt jelenti, hogy sikeresen telepítette a Istio-CRDs. Most lépjen a következő szakaszra, és [telepítse a Istio összetevőket az AK-ra](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>A Istio-összetevők telepítése az AK-on

> [!IMPORTANT]
> Győződjön meg arról, hogy az ebben a szakaszban leírt lépéseket a letöltött és kibontott Istio-kiadás legfelső szintű mappájából futtatja.

A [Grafana][grafana] és a [Kiali][kiali] telepítését a Istio-telepítés részeként fogjuk telepíteni. A Grafana elemzési és figyelési irányítópultokat biztosít, a Kiali pedig egy Service Mesh-megfigyelő irányítópultot biztosít. A beállításban ezeknek az összetevőknek minden olyan hitelesítő adatra van szüksége, amelyet [titkos][kubernetes-secrets]adatként kell megadni.

A Istio-összetevők telepítése előtt létre kell hozni a titkokat mind a Grafana, mind a Kiali. Hozza létre ezeket a titkokat a környezetének megfelelő parancsok futtatásával.

### <a name="add-grafana-secret"></a>Titkos Grafana hozzáadása

Cserélje le `REPLACE_WITH_YOUR_SECURE_PASSWORD` a tokent a jelszavára, és futtassa a következő parancsokat:

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

### <a name="add-kiali-secret"></a>Titkos Kiali hozzáadása

Cserélje le `REPLACE_WITH_YOUR_SECURE_PASSWORD` a tokent a jelszavára, és futtassa a következő parancsokat:

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

### <a name="install-istio-components"></a>Istio-összetevők telepítése

Most, hogy sikeresen létrehozta a Grafana és a Kiali titkot az AK-fürtben, itt az ideje, hogy telepítse a Istio-összetevőket. A Helm és a `istio` diagram segítségével telepítse a Istio összetevőket a `istio-system` névtérbe az AK-fürtben. Használja a megfelelő parancsokat a környezetéhez.

> [!NOTE]
> A telepítés részeként a következő beállításokat használjuk:
> - `global.controlPlaneSecurityEnabled=true`– a vezérlési síkon engedélyezve van a kölcsönös TLS
> - `mixer.adapters.useAdapterCRDs=false`– távolítsa el az órákat a keverő adapter CRDs, mivel azok elavultak lesznek, és ez javítja a teljesítményt
> - `grafana.enabled=true`– Grafana üzembe helyezésének engedélyezése elemzési és figyelési irányítópultokhoz
> - `grafana.security.enabled=true`– hitelesítés engedélyezése a Grafana
> - `tracing.enabled=true`– a Jaeger üzembe helyezésének engedélyezése nyomkövetéshez
> - `kiali.enabled=true`– a Kiali üzembe helyezésének engedélyezése a szolgáltatásbeli rácsvonalak megfigyelésére szolgáló irányítópulton

Bash

```bash
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set mixer.adapters.useAdapterCRDs=false \
  --set grafana.enabled=true --set grafana.security.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

Powershell

```powershell
helm install install/kubernetes/helm/istio --name istio --namespace istio-system `
  --set global.controlPlaneSecurityEnabled=true `
  --set mixer.adapters.useAdapterCRDs=false `
  --set grafana.enabled=true --set grafana.security.enabled=true `
  --set tracing.enabled=true `
  --set kiali.enabled=true
```

A `istio` Helm diagram nagy mennyiségű objektumot helyez üzembe. A listát a fenti `helm install` parancs kimenetében tekintheti meg. A Istio-összetevők üzembe helyezése 4 – 5 percet is igénybe vehet, a fürt környezetéből függően.

> [!NOTE]
> Minden Istio-hüvelyt Linux-csomópontokon történő futtatásra kell ütemezni. Ha Windows Server-csomópont-készletekkel rendelkezik a fürt Linux-csomópontjain is, ellenőrizze, hogy az összes Istio-hüvely a Linux-csomópontokon való futtatásra van-e ütemezve.

Ezen a ponton üzembe helyezte a Istio-t az AK-fürtön. A Istio sikeres üzembe helyezésének biztosítása érdekében térjünk át a következő szakaszra a [Istio telepítésének ellenőrzéséhez](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>A Istio telepítésének ellenőrzése

Először ellenőrizze, hogy létrejöttek-e a várt szolgáltatások. A futó szolgáltatások megtekintéséhez használja az [kubectl Get SVC][kubectl-get] parancsot. Kérdezze le `istio-system` a névteret, ahol a Istio és a kiegészítő összetevőket a `istio` Helm diagram telepítette:

```console
kubectl get svc --namespace istio-system --output wide
```

Az alábbi példában szereplő kimenet a következő szolgáltatásokat mutatja be:

- `istio-*`Services
- `jaeger-*`, `tracing` és`zipkin` kiegészítő nyomkövetési szolgáltatások
- `prometheus`kiegészítő metrikai szolgáltatás
- `grafana`a kiegészítő elemzési és monitorozási irányítópult szolgáltatás
- `kiali`kiegészítő szolgáltatás hálójának irányítópult szolgáltatása

Ha a `istio-ingressgateway` külső `<pending>`IP-címet mutat, várjon néhány percet, amíg az Azure-hálózat nem rendel hozzá IP-címet.

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

Ezután győződjön meg arról, hogy a szükséges hüvelyek létre lettek hozva. Használja a [kubectl Get hüvely][kubectl-get] parancsot, és ismételje meg `istio-system` a névterek lekérdezését:

```console
kubectl get pods --namespace istio-system
```

A következő példa kimenete az alábbi, a-t futtató hüvelyeket mutatja:

- a `istio-*` hüvelyek
- a `prometheus-*` kiegészítő mérőszámok Pod
- az `grafana-*` add-on Analytics és a monitoring Dashboard Pod
- a `kiali` kiegészítő szolgáltatás hálójának irányítópult-Pod

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

Az `Completed` állapotnak két `istio-init-crd-*` hüvelynek kell lennie. Ezek a hüvelyek felelősek voltak azon feladatok futtatásához, amelyek egy korábbi lépésben hoztak létre a CRDs. Az összes többi hüvely állapota `Running`a következő:. Ha a hüvelye nem rendelkezik ezekkel az állapotokkal, várjon egy percet vagy kettőt, amíg meg nem történik. Ha bármelyik hüvely hibát jelez, használja a [kubectl leírását a pod][kubectl-describe] paranccsal a kimenet és az állapot áttekintéséhez.

## <a name="accessing-the-add-ons"></a>A bővítmények elérése

Több bővítmény is telepítve lett a Istio, amely további funkciókat biztosít. A bővítmények felhasználói felületei külső IP-címen keresztül nem jelennek meg nyilvánosan. A kiegészítő felhasználói felületek eléréséhez használja a [kubectl Port-Forward][kubectl-port-forward] parancsot. Ez a parancs biztonságos kapcsolatot hoz létre az ügyfélszámítógép és a megfelelő Pod-kapcsolat között az AK-fürtben.

A Grafana és a Kiali egy további biztonsági réteget is hozzáadott a hitelesítő adatok megadásával a jelen cikk korábbi részében.

### <a name="grafana"></a>Grafana

A Istio tartozó elemzési és figyelési irányítópultokat a [Grafana][grafana]nyújtja. Továbbítsa a helyi `3000` portot az ügyfélszámítógépen a pod- `3000` porton, amely a Grafana-t futtatja az AK-fürtön:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

A következő példa kimenete a Grafana-re konfigurált portot mutatja:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

A Grafana a következő URL-címen érheti el az ügyfélszámítógépen – [http://localhost:3000](http://localhost:3000). Ha a rendszer kéri, ne felejtse el használni a Grafana titkos kulcson keresztül létrehozott hitelesítő adatokat.

### <a name="prometheus"></a>Prometheus

A Istio metrikáit a [Prometheus][prometheus]nyújtja. Továbbítsa a helyi `9090` portot az ügyfélszámítógépen a pod- `9090` portra, amely a Prometheus-t futtatja az AK-fürtön:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

A következő példa kimenetében látható, hogy az előre megadott port a Prometheus:

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

Most már elérheti a Prometheus-kifejezés böngészőjét a következő URL-címen az ügyfélszámítógépen [http://localhost:9090](http://localhost:9090)–.

### <a name="jaeger"></a>Jaeger

A Istio belüli nyomkövetést a [Jaeger][jaeger]biztosítja. Továbbítsa a helyi `16686` portot az ügyfélszámítógépen a a pod `16686` -porton, amelyen a Jaeger fut az AK-fürtön:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

A következő példa kimenete a "Jaeger"-ra konfigurált portot mutatja be:

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

Most már elérheti a Jaeger nyomkövetés felhasználói felületét a következő URL-címen az ügyfélszámítógépen – [http://localhost:16686](http://localhost:16686).

### <a name="kiali"></a>Kiali

A [Kiali][kiali]által biztosított Service Mesh-megfigyelő irányítópultot. Továbbítsa a helyi `20001` portot az ügyfélszámítógépen a pod- `20001` porton, amely a Kiali-t futtatja az AK-fürtön:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

A következő példa kimenete a Kiali-re konfigurált portot mutatja:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Most már elérheti a Kiali Service Mesh-megfigyelő irányítópultját a következő URL-címen az ügyfélszámítógépen – [http://localhost:20001/kiali/console/](http://localhost:20001/kiali/console/). Ha a rendszer kéri, ne felejtse el használni a Kiali titkos kulcson keresztül létrehozott hitelesítő adatokat.

## <a name="uninstall-istio-from-aks"></a>Istio eltávolítása az AK-ból

> [!WARNING]
> A Istio egy futó rendszerből való törlése a szolgáltatások közötti forgalomhoz vezethet. A folytatás előtt győződjön meg arról, hogy rendelkezik a rendszer megfelelő működéséhez szükséges Istio nélkül.

### <a name="remove-istio-components-and-namespace"></a>Istio összetevők és névtér eltávolítása

Az alábbi parancsokkal távolíthatja el a Istio az AK-fürtből. A `helm delete` parancsok el fogják távolítani `istio` a `istio-init` és a diagramokat `kubectl delete ns` , és a parancs `istio-system` eltávolítja a névteret.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Istio-CRDs eltávolítása

A fenti parancsok törlik a Istio összes összetevőjét és névterét, de továbbra is a Istio CRDs maradnak. A CRDs törléséhez a következő megközelítéseket használhatja.

Megközelítés #1 – Ez a parancs feltételezi, hogy ezt a lépést annak a letöltött és kibontott Istio a felső szintű mappájából futtatja, amelyet a Istio a használatával történő telepítéséhez használt.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Megközelítés #2 – használja a következő parancsok egyikét, ha már nincs hozzáférése a Istio letöltött és kibontott verziójához, amelyet a Istio a-vel való telepítéséhez használt. Ez a parancs eltarthat egy kicsit, hogy eltarthat néhány percig.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

Powershell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>További lépések

Az alábbi dokumentáció azt ismerteti, hogyan használható a Istio a Kanári-kiadások kivezetéséhez szükséges intelligens útválasztás biztosításához:

> [!div class="nextstepaction"]
> [AK Istio intelligens útválasztási forgatókönyv][istio-scenario-routing]

A Istio további telepítési és konfigurációs lehetőségeinek megismeréséhez tekintse meg a következő hivatalos Istio-cikkeket:

- [Istio – Helm telepítési útmutató][istio-install-helm]
- [Istio – Helm telepítési lehetőségek][istio-install-helm-options]

A [Istio Bookinfo alkalmazás példájának][istio-bookinfo-example]használatával további forgatókönyveket is követhet.

A következő Azure Monitor dokumentációból megtudhatja, hogyan figyelheti az AK-alkalmazást Application Insights és Istio használatával:
- [Zero Instrumentation-alkalmazás figyelése Kubernetes által üzemeltetett alkalmazásokhoz][app-insights]

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
