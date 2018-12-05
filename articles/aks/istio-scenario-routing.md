---
title: Intelligens az Útválasztás és az Azure Kubernetes Service (AKS) Istio tesztcsoportos kiadások
description: Ismerje meg, hogyan használható Istio intelligens útválasztást és a tesztcsoportos kiadások, az Azure Kubernetes Service (AKS)-fürt üzembe helyezése
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: 0a4e5e7e310a9949ee59291c2032eafda46955a9
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892313"
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

Ebben a cikkben részletes lépései azt feltételezik, hogy létrehozott egy AKS-fürt (Kubernetes 1.10 és újabb, az RBAC engedélyezve), és kiépített egy `kubectl` kapcsolatot a fürttel. A fürtben telepített Istio is szükséges.

Ha ezek az elemek bármelyikét segítségre van szüksége, tekintse meg a [AKS gyors] [ aks-quickstart] és [Istio telepítse az aks-ben][istio-install].

## <a name="about-this-application-scenario"></a>Információ arról forgatókönyvről, alkalmazás

A minta az AKS szavazóalkalmazás (macskák vagy kutyák) két szavazati lehetőséget biztosít a felhasználók számára. Nincs olyan tárolási összetevő, amely továbbra is fennáll az egyes lehetőségek szavazatok száma. Emellett van egy elemzési összetevője, amely körül a leadott egyes beállítások részleteit.

Ebben a cikkben start verzió telepítésével *1.0* a szavazóalkalmazás és verzió *1.0* az analytics-összetevő. Az analytics összetevőt egyszerű counts biztosít a szavazatok száma. A szavazóalkalmazás és az analytics-összetevő verziója interakcióba *1.0* a tárolási összetevő, amely a Redis használatával.

Az analytics összetevőt verzióra frissít *1.1*, amely counts biztosít, és most összesíti és a százalékos.

Felhasználók tesztelése verziója egy részhalmazát *2.0* az alkalmazás egy canary kiadás keresztül. Az új verzió használ a tárolási összetevő, amely egy MySQL-adatbázis használatával.

Ha biztos abban, hogy verziót *2.0* verzió fokozatosan felhasználók egy része a várt módon működik, *2.0* a felhasználók számára.

## <a name="deploy-the-application"></a>Az alkalmazás központi telepítése

Először az alkalmazást az Azure Kubernetes Service (AKS)-fürt üzembe helyezése. Az alábbi ábrán látható, hogy futtatható rendszerek – Ez a szakasz végén verzió *1.0* az összes összetevő a bejövő kérelmek szervizelt az Istio bejövő átjárón keresztül:

![Az AKS lehetőségre szavazott alkalmazás-összetevők és az útválasztást.](media/istio/components-and-routing-01.png)

Az összetevők, akkor kövesse az ebben a cikkben együtt érhetők el a [Azure-minták és az aks--szavazóalkalmazást] [ github-azure-sample] GitHub-adattárban. Töltse le az összetevők, vagy a tárház klónozása a következő:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Módosítsa a következő mappához a letöltött / klónozott tárház, és az utána következő összes lépés futtatása ebből a mappából:

```console
cd scenarios/intelligent-routing-with-istio
```

Először hozzon létre egy névteret a az AKS-fürt esetében a minta az AKS szavazóalkalmazás nevű *szavazási* módon:

```console
kubectl create namespace voting
```

Címke tartalmazó névtér `istio-injection=enabled`. Ezt a címkét az automatikus elhelyezése a istio proxyk oldalkocsikkal, a podokat megszünteti a névtérben lévő összes Istio utasítja.

```console
kubectl label namespace voting istio-injection=enabled
```

Most hozzuk létre az AKS-szavazóalkalmazást összetevői. Ezeket az összetevőket hozhat létre a *szavazási* az előző lépésben létrehozott névtér.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

Az alábbi példa kimenetében látható, hogy sikeresen létrejöttek az erőforrásokat:

```
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

```console
kubectl get pods -n voting
```

Az alábbi példa kimenetében látható, három példánya a *-szavazóalkalmazást* pod és a egy egyetlen példánya is a *szavazási analytics* és *szavazási tárolási* podok. A podok mindegyike rendelkezik két tárolót. Ezek a tárolók egyik összetevő, a másik pedig a *istio-proxy*:

```
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-669f99dcc8-lzh7k   2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-bdmld         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-gcrng         2/2       Running   0          1m
voting-app-1-0-6c65c4bdd4-strzc         2/2       Running   0          1m
voting-storage-1-0-7954799d96-5fv9r     2/2       Running   0          1m
```

A pod kapcsolatos információk megtekintéséhez használja a [kubectl ismertetik a pod][kubectl-describe]. A podnév cserélje le a saját AKS-fürtöt az előző kimenetében podot neve:

```console
kubectl describe pod voting-app-1-0-6c65c4bdd4-bdmld --namespace voting
```

A *istio-proxy* tároló rendelkezik automatikusan lett alkalmazza, és az összetevők, a hálózati forgalom kezelésére Istio által az alábbi példa kimenetében látható módon:

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.0.4
[...]
```

Nem lehet csatlakozni a szavazóalkalmazás a Istio létrehozásáig [átjáró] [ istio-reference-gateway] és [virtuális][istio-reference-virtualservice]. Ezeket az erőforrásokat Istio irányíthatja a forgalmat az alapértelmezett Istio bejövő átjárón az alkalmazáshoz.

> [!NOTE]
> A *átjáró* egy összetevő, amely megkapja a bejövő vagy kimenő HTTP- és TCP-forgalom szolgáltatás háló szélén.
>
> A *virtuális* határozza meg az útválasztási szabályok egy vagy több cél szolgáltatásokhoz.

Használja a `istioctl` ügyfél bináris üzembe helyezéséhez az átjáró és a szolgáltatás virtuális yaml. Ahogy a `kubectl apply` parancshoz, ne felejtse el megadni a névtér, amely ezeket az erőforrásokat a rendszer üzembe helyezi.

```console
istioctl create -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Szerezze be az IP-cím az Istio bejövő átjáró a következő paranccsal:

```console
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Az alábbi példa kimenetében látható, a bejövő forgalom átjáró IP-címe:

```
52.187.250.239
```

Nyissa meg egy böngészőt, és illessze be az IP-cím. A minta az AKS szavazóalkalmazás jelenik meg.

![Az AKS-szavazóalkalmazást az Istio futó engedélyezve van az AKS-fürtöt.](media/istio/deploy-app-01.png)

A képernyő alján tartalmazza, hogy az alkalmazás által használt verziója *1.0-s* , a *-szavazóalkalmazást* és verzió *1.0-s* (Redis), a beállítást.

## <a name="update-the-application"></a>Az alkalmazás frissítése

A Microsoft üzembe helyezni az analytics összetevő új verzióját. Az új verzió *1.1* összegek és az egyes kategóriák száma mellett százalékos jeleníti meg.

Az alábbi ábrán látható az ebben a szakaszban – csak verzió végén futtatható *1.1* , a *szavazási analytics* összetevő rendelkezik a forgalom a *-szavazóalkalmazást* összetevő. Annak ellenére, hogy verzió *1.0-s* , a *szavazási analytics* összetevő továbbra is fut, és hivatkozik a *szavazási analytics* szolgáltatástól, az Istio proxyk forgalom letiltása és azt onnan.

![Az AKS lehetőségre szavazott alkalmazás-összetevők és az útválasztást.](media/istio/components-and-routing-02.png)

Üzembe helyezni a verzió *1.1* , a *szavazási analytics* összetevő. Hozzon létre ehhez az összetevőhöz a *szavazási* névtér:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Nyissa meg az AKS szavazóalkalmazás újra, egy böngészőben a Istio bejövő átjáró IP-cím használatával beszerzett minta az előző lépésben.

A böngésző alternatívák között az alább látható a két nézetet. Óta használja egy Kubernetes [szolgáltatás] [ kubernetes-service] számára a *szavazási analytics* csak egy egycímkés választó az összetevőt (`app: voting-analytics`), Kubernetes használja az alapértelmezett Ciklikus időszeleteléses között, amelyek megfelelnek a választó a podok viselkedését. Ebben az esetben két verziója *1.0* és *1.1* , a *szavazási analytics* podok.

![Az analytics-összetevő az AKS-szavazóalkalmazást futó 1.0-s verzióját.](media/istio/deploy-app-01.png)

![Az analytics-összetevő az AKS-szavazóalkalmazást futó 1.1-es verzióját.](media/istio/update-app-01.png)

Jelenítheti meg a két verziója közötti váltáskor a *szavazási analytics* összetevő az alábbiak szerint. Fontos, hogy a saját Istio bejövő átjáró IP-címet használja.

```console
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
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

Most nézzük zárolását, így csak a verzió-forgalom *1.1-es* , a *szavazás-analytics* összetevőt és verzióra *1.0* , a *szavazási tárolási* összetevő. Majd határozza meg az útválasztási szabályokat az összes többi összetevőt.

> * A *virtuális* határozza meg az útválasztási szabályok egy vagy több cél szolgáltatásokhoz.
> * A *Célszabály* határozza meg a forgalom és a verzió adott házirendeket.
> * A *házirend* határozza meg, milyen hitelesítési módszerek a workload(s) elfogadható.

Használja a `istioctl` cserélje le a virtuális szolgáltatásdefiníció a bináris ügyfél a *-szavazóalkalmazást* , és adja hozzá [cél szabályok] [ istio-reference-destinationrule] és [ Virtuális szolgáltatások] [ istio-reference-virtualservice] a többi összetevő.

Is hozzáadhat egy [házirend] [ istio-reference-policy] , a *szavazási* annak érdekében, hogy az összes szolgáltatás közötti kommunikációhoz névtér használatával lett biztonságossá téve a TLS kölcsönös és az ügyféltanúsítványok.

Van egy meglévő virtuális definíciót *-szavazóalkalmazást* , hogy cserélje le, használja a `istioctl replace` paranccsal a következőképpen:

```console
istioctl replace -f istio/step-2a-update-voting-app-virtualservice.yaml --namespace voting
```

Az alábbi példa kimenetében látható, a Istio virtuális szolgáltatás frissítése sikerült:

```
Updated config virtual-service/voting/voting-app to revision 141902
```

Ezután a `istioctl create` parancs használatával adja hozzá az új házirend és is az új cél szabályok és a többi összetevő virtuális szolgáltatások.

* A házirend `peers.mtls.mode` beállítása `STRICT` annak érdekében, hogy a TLS kölcsönös kényszerítve van-e a szolgáltatásai között a *szavazási* névtér.
* Is beállíthat a `trafficPolicy.tls.mode` való `ISTIO_MUTUAL` a cél-szabályokban. Istio erős identitásokkal szolgáltatásokat biztosít, és a TLS kölcsönös és Istio átláthatóan kezeli az ügyfél-tanúsítványok használata a szolgáltatások közötti kommunikáció biztonságossá teszi.

```console
istioctl create -f istio/step-2b-add-routing-for-all-components.yaml --namespace voting
```

Az alábbi példa kimenetében látható az új házirend, a cél szabályokat, és virtuális szolgáltatások létrehozása sikeresen megtörtént:

```
Created config policy/voting/default to revision 142118
Created config destination-rule/voting/voting-app at revision 142119
Created config destination-rule/voting/voting-analytics at revision 142120
Created config virtual-service/voting/voting-analytics at revision 142121
Created config destination-rule/voting/voting-storage at revision 142122
Created config virtual-service/voting/voting-storage at revision 142123
```

Ha egy böngészőben, csak az új verziót nyissa meg az AKS szavazóalkalmazás *1.1-es* , a *szavazási analytics* összetevő által használt a *-szavazóalkalmazást* összetevő.

![Az analytics-összetevő az AKS-szavazóalkalmazást futó 1.1-es verzióját.](media/istio/update-app-01.png)

Könnyebben jelenítheti meg, hogy mi most csak kapcsolódóak pedig az verzió *1.1* , a *szavazási analytics* összetevő az alábbiak szerint. Ne felejtse el a Istio bejövő átjáró IP-címet használja.

Jelenítheti meg, hogy Ön már csak kapcsolódóak pedig az verzió *1.1* , a *szavazási analytics* összetevő az alábbiak szerint. Ne felejtse a saját Istio bejövő átjáró IP-címét:

```azurecli-interactive
INGRESS_IP=52.187.250.239
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

Az alábbi példa kimenetében látható, amelyet a visszaadott webhelyen:

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Győződjön meg arról, hogy Istio használja a TLS kölcsönös a szolgáltatások közötti biztonságos kommunikáció kiépítéséhez. A következő parancsokat minden egyes TLS beállításainak ellenőrzése a *-szavazóalkalmazást* szolgáltatások:

```console
istioctl authn tls-check voting-app.voting.svc.cluster.local
istioctl authn tls-check voting-analytics.voting.svc.cluster.local
istioctl authn tls-check voting-storage.voting.svc.cluster.local
```

Az alábbi Példakimenet azt mutatja, hogy a TLS kölcsönös kényszerítve van-e a házirend- és cél-szabályok használatával a szolgáltatások mindegyike esetében:

```
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Az alkalmazás egy canary kiadás bevezetése

Most már üzembe helyezni az új verzió *2.0* , a *-szavazóalkalmazást*, *szavazási analytics*, és *szavazási tárolási* összetevők. Az új *szavazási tárolási* összetevő MySQL használata helyett a Redis, és a *-szavazóalkalmazást* és *szavazási analytics* összetevők frissülnek, hogy azokat az új használatára*szavazási tárolási* összetevő.

A *-szavazóalkalmazást* összetevő mostantól támogatja a funkcióhoz jelző. Ez a funkció jelző lehetővé teszi, hogy tesztelje a felhasználók egy alhalmazára Istio canary kiadás képességét.

Az alábbi ábrán látható, ez a szakasz végén található futtatható.

* Verzió *1.0* , a *-szavazóalkalmazást* összetevő, a verzió *1.1-es* , a *szavazási analytics* összetevő és verzió  *1.0-s* , a *szavazási tárolási* összetevő tudnak egymással kommunikálni.
* Verzió *2.0* , a *-szavazóalkalmazást* összetevő, a verzió *2.0* , a *szavazási analytics* összetevő és verzió  *2.0-s* , a *szavazási tárolási* összetevő tudnak egymással kommunikálni.
* Verzió *2.0* , a *-szavazóalkalmazást* összetevő a felhasználók számára, amely egy adott funkció jelzőjének értéke csak érhetők el. Ez a változás felügyelt egy funkció jelölője keresztül a cookie-k használatával.

![Az AKS lehetőségre szavazott alkalmazás-összetevők és az útválasztást.](media/istio/components-and-routing-03.png)

Gondoskodjon arról, hogy ezen új összetevők Istio cél szabályok és virtuális szolgáltatások először frissítése Ezeket a frissítéseket, nem irányíthatja a forgalmat nem megfelelően az új összetevők és a felhasználó nem kap váratlan hozzáférést biztosítja:

```console
istioctl replace -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

Az alábbi példa kimenetében látható, hogy a cél szabályok és a virtuális szolgáltatások frissítése sikeresen megtörtént:

```
Updated config destination-rule/voting/voting-app to revision 150930
Updated config virtual-service/voting/voting-app to revision 150931
Updated config destination-rule/voting/voting-analytics to revision 150937
Updated config virtual-service/voting/voting-analytics to revision 150939
Updated config destination-rule/voting/voting-storage to revision 150940
Updated config virtual-service/voting/voting-storage to revision 150941
```

Ezután vegye fel a Kubernetes-objektumokat az új verzióhoz tartozó *2.0* összetevőket. Is frissítheti a *szavazási tárolási* szolgáltatás tartalmazza a *3306-os* portot a MySQL-hez:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

Az alábbi példa kimenetében látható, a Kubernetes-objektumokat sikeresen frissítve vagy létrehozott:

```
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Várja meg, amíg az összes verzió *2.0* podok is futnak. Használja a [kubectl get pods] [ kubectl-get] parancsot minden podok megtekintéséhez a *szavazási* névtér:

```azurecli-interactive
kubectl get pods --namespace voting
```

Most meg kell tudni váltani a verzió között *1.0* és verzió *2.0* (tesztcsoportos) a szavazóalkalmazás. A képernyő alján funkció jelző be egy cookie-t állítja be. A cookie segítségével a *-szavazóalkalmazást* útvonal felhasználóknak, hogy az új verzió virtuális szolgáltatás *2.0*.

![1.0-s verzióját az AKS-szavazóalkalmazást - funkció jelölője nincs van beállítva.](media/istio/canary-release-01.png)

![Az AKS-szavazóalkalmazást - jelzőt IS szolgáltatáskészlet 2.0-s verzióját.](media/istio/canary-release-02.png)

A szavazat különbözőek az alkalmazás verziói között. Ez a különbség emeli ki, hogy két különböző tárolási háttérrendszerek használja.

## <a name="finalize-the-rollout"></a>A bevezetés véglegesítése

Frissítése után a canary kiadás sikeres tesztelését, a *-szavazóalkalmazást* virtuális szolgáltatást, hogy minden forgalmat irányítson verzióra *2.0* , a *-szavazóalkalmazást* összetevő. Minden felhasználó ekkor megjelenik a verzió *2.0* az alkalmazás, függetlenül attól, hogy a szolgáltatás jelző be van állítva, vagy nem:

![Az AKS lehetőségre szavazott alkalmazás-összetevők és az útválasztást.](media/istio/components-and-routing-04.png)

Az összes a cél szabályainak frissítése az összetevők verziójának eltávolítása ettől kezdve nem aktív. Ezután frissítse a virtuális szolgáltatások leállítása a hivatkozó verziókat.

Már nem tartozik az összetevők régebbi verzióját valamelyik forgalmat, mivel most már nyugodtan törölheti a központi telepítések ezen összetevők számára.

![Az AKS lehetőségre szavazott alkalmazás-összetevők és az útválasztást.](media/istio/components-and-routing-05.png)

Sikeresen most jelennek meg az AKS-Szavazóalkalmazást új verziója.

## <a name="next-steps"></a>További lépések

További forgatókönyvek segítségével megismerheti a [Istio Bookinfo alkalmazás például][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/spec-requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#Gateway
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#VirtualService
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/istio.networking.v1alpha3/#DestinationRule
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md
