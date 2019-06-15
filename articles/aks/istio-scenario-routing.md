---
title: Intelligens az Útválasztás és az Azure Kubernetes Service (AKS) Istio tesztcsoportos kiadások
description: Ismerje meg, hogyan használható Istio intelligens útválasztást és a tesztcsoportos kiadások, az Azure Kubernetes Service (AKS)-fürt üzembe helyezése
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: bd660a2b6ffb96478c3170cc7013ff22518b758f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64702208"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Intelligens útválasztást és a tesztcsoportos kiadások használata Istio Azure Kubernetes Service (AKS)

[Istio] [ istio-github] van egy nyílt forráskódú service háló, amely kínál a legfontosabb funkciók között a mikroszolgáltatások, Kubernetes-fürtben. Ilyen például a forgalomkezelést, felügyeltszolgáltatás-identitás és a biztonsági, a házirend betartatása és observability. Istio kapcsolatos további információkért tekintse meg a hivatalos [Istio mi?] [ istio-docs-concepts] dokumentációját.

Ez a cikk bemutatja, hogyan Istio forgalom felügyeleti funkciójának használatához. Egy mintául szolgáló AKS szavazóalkalmazás intelligens útválasztást és a tesztcsoportos kiadások szolgál.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Az alkalmazás központi telepítése
> * Az alkalmazás frissítése
> * Az alkalmazás egy canary kiadás bevezetése
> * A bevezetés véglegesítése

## <a name="before-you-begin"></a>Előkészületek

> [!NOTE]
> Ebben a forgatókönyvben elleni Istio verziója tesztelve lett `1.1.3`.

Ebben a cikkben részletes lépései azt feltételezik, hogy létrehozott egy AKS-fürt (Kubernetes `1.11` és újabb, az RBAC engedélyezve), és kiépített egy `kubectl` kapcsolatot a fürttel. A fürtben telepített Istio is kell.

Ha ezek az elemek bármelyikét segítségre van szüksége, tekintse meg a [AKS gyors] [ aks-quickstart] és [Istio telepítse az aks-ben] [ istio-install] útmutatást.

## <a name="about-this-application-scenario"></a>Információ arról forgatókönyvről, alkalmazás

A minta az AKS szavazóalkalmazás két szavazati lehetőséget biztosít (**macskák** vagy **kutyák**) a felhasználók számára. Nincs olyan tárolási összetevő, amely továbbra is fennáll az egyes lehetőségek szavazatok száma. Emellett van egy elemzési összetevője, amely körül a leadott egyes beállítások részleteit.

Az alkalmazás ebben a forgatókönyvben start verzió telepítésével `1.0` a szavazóalkalmazás és verzió `1.0` az analytics-összetevő. Az analytics összetevőt egyszerű counts biztosít a szavazatok száma. A szavazóalkalmazás és az analytics-összetevő verziója interakcióba `1.0` a tárolási összetevő, amely a Redis használatával.

Az analytics összetevőt verzióra frissít `1.1`, amely counts biztosít, és most összesíti és a százalékos.

Felhasználók tesztelése verziója egy részhalmazát `2.0` az alkalmazás egy canary kiadás keresztül. Az új verzió használ a tárolási összetevő, amely egy MySQL-adatbázis használatával.

Ha biztos abban, hogy verziót `2.0` verzió fokozatosan felhasználók egy része a várt módon működik, `2.0` a felhasználók számára.

## <a name="deploy-the-application"></a>Az alkalmazás központi telepítése

Először az alkalmazást az Azure Kubernetes Service (AKS)-fürt üzembe helyezése. Az alábbi ábrán látható, hogy futtatható rendszerek – Ez a szakasz végén verzió `1.0` az összes összetevő a bejövő kérelmek szervizelt az Istio bejövő átjárón keresztül:

![Az AKS lehetőségre szavazott alkalmazás-összetevők és az útválasztást.](media/istio/components-and-routing-01.png)

Az összetevők, akkor kövesse az ebben a cikkben együtt érhetők el a [Azure-minták és az aks--szavazóalkalmazást] [ github-azure-sample] GitHub-adattárban. Töltse le az összetevők, vagy a tárház klónozása a következő:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Módosítsa a következő mappához a letöltött / klónozott tárház, és az utána következő összes lépés futtatása ebből a mappából:

```console
cd scenarios/intelligent-routing-with-istio
```

Először hozzon létre egy névteret a az AKS-fürt esetében a minta az AKS szavazóalkalmazás nevű `voting` módon:

```azurecli
kubectl create namespace voting
```

Címke tartalmazó névtér `istio-injection=enabled`. Ezt a címkét az automatikus elhelyezése a istio proxyk oldalkocsikkal, a podokat megszünteti a névtérben lévő összes Istio utasítja.

```azurecli
kubectl label namespace voting istio-injection=enabled
```

Most hozzuk létre az AKS-szavazóalkalmazást összetevői. Ezeket az összetevőket hozhat létre a `voting` az előző lépésben létrehozott névtér.

```azurecli
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

Az alábbi példa kimenetében látható az éppen létrehozott erőforrásokat:

```console
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio van néhány konkrét követelményeket podok és -szolgáltatások. További információkért lásd: a [Podok és -szolgáltatások dokumentációja Istio követelményei][istio-requirements-pods-and-services].

A létrehozott podok megtekintéséhez használja a [kubectl get pods] [ kubectl-get] paranccsal a következőképpen:

```azurecli
kubectl get pods -n voting
```

Az alábbi példa kimenetében látható, három példánya a `voting-app` pod és a egy egyetlen példánya is a `voting-analytics` és `voting-storage` podok. A podok mindegyike rendelkezik két tárolót. Ezek a tárolók egyik összetevő, a másik pedig a `istio-proxy`:

```console
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s
```

A pod kapcsolatos információk megtekintéséhez használja a [kubectl ismertetik a pod][kubectl-describe]. A podnév cserélje le a saját AKS-fürtöt az előző kimenetében podot neve:

```azurecli
kubectl describe pod voting-app-1-0-956756fd-d5w7z --namespace voting
```

A `istio-proxy` tároló rendelkezik automatikusan lett alkalmazza, és az összetevők, a hálózati forgalom kezelésére Istio által az alábbi példa kimenetében látható módon:

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.1.3
[...]
```

Nem lehet csatlakozni a szavazóalkalmazás a Istio létrehozásáig [átjáró] [ istio-reference-gateway] és [virtuális][istio-reference-virtualservice]. Ezeket az erőforrásokat Istio irányíthatja a forgalmat az alapértelmezett Istio bejövő átjárón az alkalmazáshoz.

> [!NOTE]
> A **átjáró** egy összetevő, amely megkapja a bejövő vagy kimenő HTTP- és TCP-forgalom szolgáltatás háló szélén.
> 
> A **virtuális** határozza meg az útválasztási szabályok egy vagy több cél szolgáltatásokhoz.

Használja a `kubectl apply` parancsot az átjáró és a szolgáltatás virtuális yaml üzembe helyezéséhez. Ne feledje, hogy adja meg a névtér, amely ezeket az erőforrásokat a rendszer üzembe helyezi.

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Az alábbi példa kimenetében látható, az új átjáró és a létrehozandó virtuális szolgáltatás:

```console
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Szerezze be az IP-cím az Istio bejövő átjáró a következő paranccsal:

```azurecli
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Az alábbi példa kimenetében látható, a bejövő forgalom átjáró IP-címe:

```
20.188.211.19
```

Nyissa meg egy böngészőt, és illessze be az IP-cím. A minta az AKS szavazóalkalmazás jelenik meg.

![Az AKS-szavazóalkalmazást az Istio futó engedélyezve van az AKS-fürtöt.](media/istio/deploy-app-01.png)

A képernyő alján tartalmazza, hogy az alkalmazás által használt verziója `1.0` , `voting-app` és verzió `1.0` , `voting-storage` (Redis).

## <a name="update-the-application"></a>Az alkalmazás frissítése

A Microsoft üzembe helyezni az analytics összetevő új verzióját. Az új verzió `1.1` összegek és az egyes kategóriák száma mellett százalékos jeleníti meg.

Az alábbi ábrán látható, hogy mit fog futni az ebben a szakaszban – csak verzió végén `1.1` , a `voting-analytics` összetevő rendelkezik a forgalom a `voting-app` összetevő. Annak ellenére, hogy verzió `1.0` , a `voting-analytics` összetevő továbbra is fut, és hivatkozik a `voting-analytics` szolgáltatástól, az Istio proxyk letiltása azt a forgalmat.

![Az AKS lehetőségre szavazott alkalmazás-összetevők és az útválasztást.](media/istio/components-and-routing-02.png)

Üzembe helyezni a verzió `1.1` , a `voting-analytics` összetevő. Hozzon létre ehhez az összetevőhöz a `voting` névteret:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Az alábbi példa kimenetében látható az éppen létrehozott erőforrásokat:

```console
deployment.apps/voting-analytics-1-1 created
```

Nyissa meg az AKS szavazóalkalmazás újra, egy böngészőben a Istio bejövő átjáró IP-cím használatával beszerzett minta az előző lépésben.

A böngésző alternatívák között az alább látható a két nézetet. Óta használja egy Kubernetes [szolgáltatás] [ kubernetes-service] számára a `voting-analytics` csak egy egycímkés választó az összetevőt (`app: voting-analytics`), Kubernetes használ, Ciklikus időszeleteléses közötti alapértelmezett viselkedését a a választó megfelelő podok. Ebben az esetben két verziója `1.0` és `1.1` , a `voting-analytics` podok.

![Az analytics-összetevő az AKS-szavazóalkalmazást futó 1.0-s verzióját.](media/istio/deploy-app-01.png)

![Az analytics-összetevő az AKS-szavazóalkalmazást futó 1.1-es verzióját.](media/istio/update-app-01.png)

A két verziója közötti váltás segítségével megjelenítheti a `voting-analytics` összetevő az alábbiak szerint. Fontos, hogy a saját Istio bejövő átjáró IP-címet használja.

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

PowerShell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

Az alábbi példa kimenetében látható a visszaadott webhely megfelelő része, a hely kapcsolók verziói között:

```
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Forgalom 1.1-es verziójára az alkalmazás zárolását

Most tekintsük zárolását, így csak a verzió-forgalom `1.1` , a `voting-analytics` összetevőt és verzióra `1.0` , a `voting-storage` összetevő. Majd határozza meg az útválasztási szabályokat az összes többi összetevőt.

> * A **virtuális** határozza meg az útválasztási szabályok egy vagy több cél szolgáltatásokhoz.
> * A **Célszabály** határozza meg a forgalom és a verzió adott házirendeket.
> * A **házirend** határozza meg, milyen hitelesítési módszerek a workload(s) elfogadható.

Használja a `kubectl apply` cserélje le a virtuális szolgáltatásdefiníció a parancsot a `voting-app` , és adja hozzá [cél szabályok] [ istio-reference-destinationrule] és [virtuális szolgáltatások] [ istio-reference-virtualservice] a többi összetevő. Hozzáadhat egy [házirend] [ istio-reference-policy] , a `voting` annak érdekében, hogy az összes szolgáltatás közötti kommunikációhoz névtér használatával lett biztonságossá téve a TLS kölcsönös és az ügyféltanúsítványok.

* A házirend `peers.mtls.mode` beállítása `STRICT` annak érdekében, hogy a TLS kölcsönös kényszerítve van-e a szolgáltatásai között a `voting` névtér.
* Azt is beállíthatja a `trafficPolicy.tls.mode` való `ISTIO_MUTUAL` a cél-szabályokban. Istio erős identitásokkal szolgáltatásokat biztosít, és a TLS kölcsönös és Istio átláthatóan kezeli az ügyfél-tanúsítványok használata a szolgáltatások közötti kommunikáció biztonságossá teszi.

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

Az alábbi példa kimenetében látható, az új házirend, a cél szabályok és a folyamatban, frissíteni vagy hozható létre virtuális szolgáltatások:

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Ha egy böngészőben, csak az új verziót nyissa meg az AKS-szavazóalkalmazást `1.1` , a `voting-analytics` összetevő által használt a `voting-app` összetevő.

![Az analytics-összetevő az AKS-szavazóalkalmazást futó 1.1-es verzióját.](media/istio/update-app-01.png)

Jelenítheti meg, hogy Ön már csak kapcsolódóak pedig az verzió `1.1` , a `voting-analytics` összetevő az alábbiak szerint. Ne felejtse a saját Istio bejövő átjáró IP-címét:

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

PowerShell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

Az alábbi példa kimenetében látható, amelyet a visszaadott webhelyen:

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Nézzük most győződjön meg arról, hogy Istio TLS kölcsönös használ a szolgáltatások közötti kommunikáció biztonságossá tételéhez. Ezt használjuk a [authn tls-ellenőrzésének] [ istioctl-authn-tls-check] parancs a `istioctl` ügyfél bináris, amely a következő formátumot követi.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Ezen parancsok készlete a megadott szolgáltatások, a hozzáférés kapcsolatos adatok megadása a névtérben és megfelelnek a címkék az összes podok:

Bash

```bash
# mTLS configuration between each of the istio ingress pods and the voting-app service
kubectl get pod -n istio-system -l app=istio-ingressgateway | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.istio-system voting-app.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-analytics service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-analytics.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-storage service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
kubectl get pod -n voting -l app=voting-analytics,version=1.1 | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local
```

PowerShell

```powershell
# mTLS configuration between each of the istio ingress pods and the voting-app service
(kubectl get pod -n istio-system -l app=istio-ingressgateway | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".istio-system") voting-app.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-analytics service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-analytics.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-analytics,version=1.1 | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }
```

Az alábbi példa kimenetében látható, hogy a TLS kölcsönös kényszerítése minden, a fenti lekérdezések. A is megjelenítheti a szabályzat, és kikényszeríti a kölcsönös TLS cél szabályok:

```console
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Az alkalmazás egy canary kiadás bevezetése

Most már üzembe helyezni az új verzió `2.0` , a `voting-app`, `voting-analytics`, és `voting-storage` összetevőket. Az új `voting-storage` összetevő MySQL használata helyett a Redis, és a `voting-app` és `voting-analytics` összetevők frissülnek, hogy ezzel új `voting-storage` összetevő.

A `voting-app` összetevő mostantól támogatja a funkcióhoz jelző. Ez a funkció jelző lehetővé teszi, hogy tesztelje a felhasználók egy alhalmazára Istio canary kiadás képességét.

Az alábbi ábrán látható, hogy mit kell fut ez a szakasz végén.

* Verzió `1.0` , a `voting-app` összetevő, a verzió `1.1` , a `voting-analytics` összetevő és verzió `1.0` , a `voting-storage` összetevő tudnak egymással kommunikálni.
* Verzió `2.0` , a `voting-app` összetevő, a verzió `2.0` , a `voting-analytics` összetevő és verzió `2.0` , a `voting-storage` összetevő tudnak egymással kommunikálni.
* Verzió `2.0` , a `voting-app` összetevő a felhasználók számára, amely egy adott funkció jelzőjének értéke csak érhetők el. Ez a változás felügyelt egy funkció jelölője keresztül a cookie-k használatával.

![Az AKS lehetőségre szavazott alkalmazás-összetevők és az útválasztást.](media/istio/components-and-routing-03.png)

Gondoskodjon arról, hogy ezen új összetevők Istio cél szabályok és virtuális szolgáltatások először frissítése Ezeket a frissítéseket, nem irányíthatja a forgalmat nem megfelelően az új összetevők és a felhasználó nem kap váratlan hozzáférést biztosítja:

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

Az alábbi példa kimenetében látható, a cél szabályok és a virtuális szolgáltatások frissítése:

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Ezután vegye fel a Kubernetes-objektumokat az új verzióhoz tartozó `2.0` összetevőket. Is frissítheti a `voting-storage` szolgáltatás tartalmazza a `3306` portot a MySQL-hez:

```azurecli
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

Az alábbi példa kimenetében látható, a Kubernetes-objektumokat sikeresen frissítve vagy létrehozott:

```console
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Várja meg, amíg az összes verzió `2.0` podok is futnak. Használja a [kubectl get pods] [ kubectl-get] parancsot minden podok megtekintéséhez a `voting` névteret:

```azurecli
kubectl get pods --namespace voting
```

Most meg kell tudni váltani a verzió között `1.0` és verzió `2.0` (tesztcsoportos) a szavazóalkalmazás. A képernyő alján funkció jelző be egy cookie-t állítja be. Ez a cookie segítségével a `voting-app` útvonal felhasználóknak, hogy az új verzió virtuális szolgáltatás `2.0`.

![1\.0-s verzióját az AKS-szavazóalkalmazást - funkció jelölője nincs van beállítva.](media/istio/canary-release-01.png)

![Az AKS-szavazóalkalmazást - jelzőt IS szolgáltatáskészlet 2.0-s verzióját.](media/istio/canary-release-02.png)

A szavazat különbözőek az alkalmazás verziói között. Ez a különbség emeli ki, hogy két különböző tárolási háttérrendszerek használja.

## <a name="finalize-the-rollout"></a>A bevezetés véglegesítése

Miután sikeresen tesztelését a canary kiadás, frissítse a `voting-app` virtuális szolgáltatást, hogy minden forgalmat irányítson verzióra `2.0` , a `voting-app` összetevő. Minden felhasználó ekkor megjelenik a verzió `2.0` az alkalmazás, függetlenül attól, hogy a szolgáltatás jelző be van állítva, vagy nem:

![Az AKS lehetőségre szavazott alkalmazás-összetevők és az útválasztást.](media/istio/components-and-routing-04.png)

Az összes a cél szabályainak frissítése az összetevők verziójának eltávolítása ettől kezdve nem aktív. Ezután frissítse a virtuális szolgáltatások leállítása a hivatkozó verziókat.

Már nem tartozik az összetevők régebbi verzióját valamelyik forgalmat, mivel most már nyugodtan törölheti a központi telepítések ezen összetevők számára.

![Az AKS lehetőségre szavazott alkalmazás-összetevők és az útválasztást.](media/istio/components-and-routing-05.png)

Sikeresen most jelennek meg az AKS-Szavazóalkalmazást új verziója.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása 

Eltávolíthatja az AKS szavazóalkalmazás használtuk ebben a forgatókönyvben az AKS-fürt törlésével a `voting` névtér az alábbiak szerint:

```azurecli
kubectl delete namespace voting
```

Az alábbi példa kimenetében látható, hogy az AKS szavazóalkalmazás minden összetevőjének el lettek távolítva az AKS-fürt.

```console
namespace "voting" deleted
```

## <a name="next-steps"></a>További lépések

További forgatókönyvek segítségével megismerheti a [Istio Bookinfo alkalmazás például][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md
