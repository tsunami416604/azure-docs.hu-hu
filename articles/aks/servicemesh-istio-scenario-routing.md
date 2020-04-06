---
title: Az Istio használata intelligens útválasztáshoz
titleSuffix: Azure Kubernetes Service
description: Ismerje meg, hogyan használhatja az Istio-t intelligens útválasztási és kanári-kiadások üzembe helyezésére egy Azure Kubernetes-szolgáltatás (AKS) fürtben
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 01a7764eb0a353e6842441093f70ad29c9316bbd
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668271"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Intelligens útválasztási és kanári-kiadások használata az Istio szolgáltatással az Azure Kubernetes szolgáltatásban (AKS)

[Az Istio][istio-github] egy nyílt forráskódú szolgáltatásháló, amely egy Kubernetes-fürt mikroszolgáltatásaiközött kulcsfontosságú funkciókat biztosít. Ezek közé tartozik a forgalomkezelés, a szolgáltatás identitása és biztonsága, a házirendek betartatása és a megfigyelhetőség. További információ az Istio, lásd a hivatalos [Mi istio?][istio-docs-concepts] dokumentáció.

Ez a cikk bemutatja, hogyan használhatja az Istio forgalomkezelési funkcióit. A minta AKS szavazási alkalmazás intelligens útválasztási és kanárikiadások feltárására szolgál.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Az alkalmazás központi telepítése
> * Az alkalmazás frissítése
> * Az alkalmazás kanárikiadásának bevezetése
> * A bevezetés véglegesítése

## <a name="before-you-begin"></a>Előkészületek

> [!NOTE]
> Ez a forgatókönyv tesztelték ellen `1.3.2`Istio verzió .

A cikkben ismertetett lépések feltételezik, hogy létrehozott egy AKS-fürtöt (Kubernetes `1.13` `kubectl` és újabb, az RBAC engedélyezve), és kapcsolatot létesített a fürttel. Istio telepítve is lesz a fürtben.

Ha segítségre van szüksége ezen elemek bármelyikével kapcsolatban, olvassa el az [AKS rövid útmutatóját][aks-quickstart] és [az Istio telepítése című témakört az AKS-útmutatóban.][istio-install]

## <a name="about-this-application-scenario"></a>Az alkalmazásforgatókönyv ről

A minta AKS szavazás app szolgáltat kettő szavazás választások (**macskák** vagy **Kutyák)**-hoz használók. Van egy tárolási összetevő, amely megőrzi az egyes beállításokra vonatkozó szavazatok számát. Emellett van egy elemzési összetevő, amely részletezi az egyes beállításokleadott szavazatok körül.

Ebben az alkalmazásban a szavazási `1.0` alkalmazás és az `1.0` elemzési összetevő verziójának üzembe helyezésével kezdi. Az elemzési összetevő egyszerű számokat biztosít a szavazatok számához. A szavazó alkalmazás és az `1.0` elemzési összetevő a Redis által támogatott tárolóösszetevő verziójával lép kölcsönhatásba.

Az elemzési összetevőt `1.1`verzióra frissíti, amely számlálókat, és most összesítéseket és százalékokat biztosít.

A felhasználók egy részhalmaza az alkalmazás kanári-kiadáson keresztüli tesztelését `2.0` teszteli. Ez az új verzió egy Olyan tárolási összetevőt használ, amelyet egy MySQL-adatbázis támogat.

Ha biztos abban, `2.0` hogy a verzió a felhasználók részhalmazán `2.0` az elvárt módon működik, akkor a verziót az összes felhasználó számára is bekell adnia.

## <a name="deploy-the-application"></a>Az alkalmazás központi telepítése

Kezdjük azzal, hogy az alkalmazást az Azure Kubernetes-szolgáltatás (AKS) fürtbe telepíti. Az alábbi ábra bemutatja, hogy mi `1.0` fut a szakasz végéig – az Istio ingress átjárón keresztül kiszolgált bejövő kéréseket tartalmazó összes összetevő verziója:

![Az AKS voting alkalmazás összetevői és útválasztása.](media/servicemesh/istio/scenario-routing-components-01.png)

A cikket követő összetevők az [Azure-Samples/aks-voting-app][github-azure-sample] GitHub-tárházban érhetők el. Letöltheti a műtermékeket, vagy klónozhatja a tárta az alábbiak szerint:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Váltson a következő mappára a letöltött / klónozott tárházban, és futtassa az összes további lépést ebből a mappából:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Először hozzon létre egy névteret az AKS-fürtben `voting` a minta AKS szavazási alkalmazás hoz létre a következőképpen:

```console
kubectl create namespace voting
```

Címkézze fel `istio-injection=enabled`a névteret a segítségével. Ez a címke arra utasítja istio automatikusan adja be az istio-proxyk oldalkocsiként az összes podebben a névtérben.

```console
kubectl label namespace voting istio-injection=enabled
```

Most hozzuk létre az AKS voting alkalmazás összetevőit. Hozza létre ezeket `voting` az összetevőket az előző lépésben létrehozott névtérben.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

A következő példa kimenete a létrehozás alatt álló erőforrásokat mutatja:

```output
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio van néhány speciális követelmények körül podok és szolgáltatások. További információt a [Podok és szolgáltatások istio követelményeinek dokumentációjában][istio-requirements-pods-and-services]talál.

A létrehozott podok megtekintéséhez használja a [kubectl get pods][kubectl-get] parancsot az alábbiak szerint:

```console
kubectl get pods -n voting --show-labels
```

A következő példa kimeneti azt mutatja, három példánya van a `voting-app` pod, és egy példánya mind a és `voting-analytics` `voting-storage` a podok. Mindegyik hüvelynek két tartálya van. Az egyik ilyen tartály az alkatrész, `istio-proxy`a másik pedig a következő:

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

A podkal kapcsolatos információk megtekintéséhez a [kubectl írja le][kubectl-describe] pod parancsot `voting-analytics` címkeválasztókkal a pod kiválasztásához. Szűrjük a kimenetet, hogy megmutassuk a podban lévő két tartály részleteit:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

Nem tud csatlakozni a szavazó alkalmazáshoz, amíg létre nem hozza az Istio [átjárót][istio-reference-gateway] és a [virtuális szolgáltatást.][istio-reference-virtualservice] Ezek az Istio-erőforrások az alapértelmezett Istio ingress átjáróról irányítják a forgalmat az alkalmazásunkhoz.

> [!NOTE]
> Az **átjáró** a szolgáltatásháló szélén található összetevő, amely bejövő vagy kimenő HTTP- és TCP-forgalmat fogad.
> 
> A **virtuális szolgáltatás** egy vagy több célszolgáltatás útválasztási szabályait határozza meg.

Használja `kubectl apply` a parancsot az átjáró és a virtuális szolgáltatás yaml üzembe helyezéséhez. Ne felejtse el megadni azt a névteret, amelybe ezek az erőforrások telepítve vannak.

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

A következő példa kimenete az új átjáró és virtuális szolgáltatás létrehozása látható:

```output
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Szerezze be az Istio Ingress Gateway IP-címét a következő paranccsal:

```output
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

A következő példa kimenete a be- és énemi átjáró IP-címét mutatja:

```output
20.188.211.19
```

Nyisson meg egy böngészőt, és illessze be az IP-címet. Megjelenik a minta AKS szavazási alkalmazás.

![Az Istio-ban futó AKS voting alkalmazás engedélyezte az AKS-fürtöt.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

A képernyő alján található információk azt mutatják, `voting-app` hogy `1.0` `voting-storage` az alkalmazás a (Redis) verzióját `1.0` és verzióját használja.

## <a name="update-the-application"></a>Az alkalmazás frissítése

Telepítsük az elemzési összetevő új verzióját. Ez az `1.1` új verzió az egyes kategóriák száma mellett az összegeket és a százalékokat is megjeleníti.

A következő ábra azt mutatja, hogy mi fog `1.1` futni `voting-analytics` a szakasz végén - `voting-app` csak az összetevő verziójának van forgalom irányítva az összetevőből. Annak ellenére, `voting-analytics` hogy az összetevő verziója `voting-analytics` `1.0` továbbra is fut, és a szolgáltatás hivatkozik rá, az Istio proxyk lehetővé teszik a forgalmat.

![Az AKS voting alkalmazás összetevői és útválasztása.](media/servicemesh/istio/scenario-routing-components-02.png)

Telepítsük az `1.1` `voting-analytics` összetevő verzióját. Hozza létre ezt `voting` az összetevőt a névtérben:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

A következő példa kimenete a létrehozás alatt álló erőforrásokat mutatja:

```output
deployment.apps/voting-analytics-1-1 created
```

Nyissa meg a minta AKS szavazási alkalmazást egy böngészőben, az előző lépésben kapott Istio Ingress Gateway IP-címével.

A böngésző felváltva a két nézet alább látható. Mivel a Kubernetes [szolgáltatást][kubernetes-service] csak `voting-analytics` egyetlen címkeválasztóval (`app: voting-analytics`) rendelkező összetevőhöz használja, a Kubernetes a ciklikus multiplexelés alapértelmezett viselkedését használja a választónak megfelelő podok között. Ebben az esetben ez `1.0` mind `1.1` a `voting-analytics` verzió, és a podok.

![Az AKS voting alkalmazásban futó elemzési összetevő 1.0-s verziója.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![Az AKS voting alkalmazásunkban futó elemzési összetevő 1.1-es verziója.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Az összetevő két verziója közötti váltást `voting-analytics` az alábbiak szerint jelenítheti meg. Ne felejtse el használni a saját Istio Ingress Gateway IP-címét.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

A következő példa kimeneta a visszaadott webhely megfelelő részét mutatja, amikor a webhely átvált a verziók között:

```output
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Forgalom zárolása az alkalmazás 1.1-es verziójára

Most zárjuk le a forgalmat `1.1` `voting-analytics` csak az `1.0` összetevő `voting-storage` és az összetevő verziója. Ezután az összes többi összetevőhöz útválasztási szabályokat kell megadnia.

> * A **virtuális szolgáltatás** egy vagy több célszolgáltatás útválasztási szabályait határozza meg.
> * A **célszabály** határozza meg a forgalmi szabályzatokat és a verzióspecifikus házirendeket.
> * A **szabályzat** határozza meg, hogy milyen hitelesítési módszerek fogadhatók el a számítási feladatok(ak)on.

A `kubectl apply` paranccsal cserélje le a `voting-app` virtuális szolgáltatás definícióját a másik összetevőhöz, és adja hozzá a [célszabályokat][istio-reference-destinationrule] és a [virtuális szolgáltatásokat.][istio-reference-virtualservice] A `voting` névtérhez [házirendet][istio-reference-policy] fog hozzáadni annak érdekében, hogy a szolgáltatások közötti kommunikáció kölcsönös TLS- és ügyféltanúsítványok használatával biztonságos legyen.

* A szabályzat `peers.mtls.mode` célja `STRICT` annak biztosítása, hogy a `voting` közös TLS a névtéren belüli szolgáltatások között érvényesítve legyen.
* Azt is `trafficPolicy.tls.mode` meg `ISTIO_MUTUAL` a, hogy az összes rendeltetési szabály. Az Istio erős identitással rendelkező szolgáltatásokat nyújt, és biztosítja a szolgáltatások közötti kommunikációt a kölcsönös TLS és az Istio által transzparens módon kezelt ügyféltanúsítványok használatával.

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

A következő példa kimenetaz új szabályzatot, a célszabályokat és a virtuális szolgáltatásokat frissíti/hozlétre mutatja be:

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Ha ismét megnyitja az AKS voting alkalmazást egy `1.1` böngészőben, az `voting-analytics` `voting-app` összetevő csak az összetevő új verzióját használja.

![Az AKS voting alkalmazásunkban futó elemzési összetevő 1.1-es verziója.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Láthatja, hogy most már csak az `1.1` `voting-analytics` összetevő verziójához van irányítva az alábbiak szerint. Ne felejtse el használni a saját Istio Ingress Gateway IP-címét:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

A következő példa kimeneta a visszaadott webhely megfelelő részét mutatja be:

```output
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Most erősítsük meg, hogy Istio kölcsönös TLS-t használ az egyes szolgáltatásaink közötti kommunikáció biztosítására. Ehhez az `istioctl` ügyfél bináris fájlján az [authn tls-check][istioctl-authn-tls-check] parancsot fogjuk használni, amely a következő formát ölti.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Ez a parancskészlet információt nyújt a megadott szolgáltatásokhoz való hozzáférésről a névtérben lévő és címkék nek megfelelő összes podról:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

Ez a következő példa kimenet azt mutatja, hogy a kölcsönös TLS a fenti lekérdezéseink mindegyikére érvényes. A kimenet a kölcsönös TLS-t érvényesítő szabályzati és célszabályokat is megjeleníti:

```output
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

## <a name="roll-out-a-canary-release-of-the-application"></a>Az alkalmazás kanárikiadásának bevezetése

Most telepítse `2.0` a `voting-app`, `voting-analytics`és `voting-storage` az összetevők új verzióját. Az `voting-storage` új összetevő a MySQL-t `voting-app` használja `voting-analytics` a Redis helyett, és `voting-storage` a és az összetevők frissülnek, hogy lehetővé tegyék számukra az új összetevő használatát.

Az `voting-app` összetevő mostantól támogatja a funkciójelző funkcióit. Ez a szolgáltatásjelző lehetővé teszi az Istio kanárikiadási képességének tesztelését a felhasználók egy részhalmaza számára.

Az alábbi ábra azt mutatja, hogy mi lesz a szakasz végén.

* Az `1.0` `voting-app` összetevő verziója, `1.1` az `voting-analytics` összetevő `1.0` verziója `voting-storage` és az összetevő verziója képes kommunikálni egymással.
* Az `2.0` `voting-app` összetevő verziója, `2.0` az `voting-analytics` összetevő `2.0` verziója `voting-storage` és az összetevő verziója képes kommunikálni egymással.
* Az `2.0` összetevő `voting-app` verziója csak azok számára érhető el, akik egy adott szolgáltatásjelző készlettel rendelkeznek. Ezt a módosítást egy szolgáltatásjelző segítségével kezelik egy cookie-n keresztül.

![Az AKS voting alkalmazás összetevői és útválasztása.](media/servicemesh/istio/scenario-routing-components-03.png)

Először frissítse az Istio célszabályokat és a virtuális szolgáltatásokat, hogy azok az új összetevők et kielégítse. Ezek a frissítések biztosítják, hogy a forgalmat ne irányítsa helytelenül az új összetevőkhöz, és a felhasználók nem kapnak váratlan hozzáférést:

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

A következő példa kimeneta a frissítés alatt áll a célszabályok és a virtuális szolgáltatások frissítése közben:

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Ezután adjuk hozzá a Kubernetes objektumokat `2.0` az új verzió-összetevőkhöz. Azt is `voting-storage` frissíti a `3306` szolgáltatást, hogy tartalmazza a port a MySQL:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

A következő példakimenet a Kubernetes-objektumok sikeres enfrissítésre vagy létrehozásra kerül:

```output
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Várjon, amíg az összes verziópod `2.0` fut. Használja a [kubectl get pods][kubectl-get] parancsot az `-w` óra kapcsolóval, `voting` hogy figyelje a változásokat az összes poda a névtérben:

```console
kubectl get pods --namespace voting -w
```

Most már képesnek kell lennie `1.0` arra, hogy váltson a szavazási alkalmazás verziója és változata `2.0` (kanári) között. A képernyő alján lévő funkciójelző beállítja a cookie-t. Ezt a cookie-t a `voting-app` virtuális szolgáltatás arra `2.0`használja, hogy a felhasználókat az új verzióra irányítsa.

![Az AKS Voting alkalmazás 1.0-s verziója - a funkciójelző nincs beállítva.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![Az AKS Voting alkalmazás 2.0-s verziója - funkciójelző is beállítva.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

A szavazatok száma eltér az alkalmazás verziói között. Ez a különbség kiemeli, hogy két különböző tároló háttérrendszereket használ.

## <a name="finalize-the-rollout"></a>A bevezetés véglegesítése

Miután sikeresen tesztelte a kanári-kiadást, frissítse a virtuális szolgáltatást, `voting-app` hogy az összes forgalmat az `2.0` `voting-app` összetevő verziójához irányítsa. Ezután minden `2.0` felhasználó látja az alkalmazás verzióját, függetlenül attól, hogy a szolgáltatásjelző be van-e állítva vagy sem:

![Az AKS voting alkalmazás összetevői és útválasztása.](media/servicemesh/istio/scenario-routing-components-04.png)

Frissítse az összes célszabályt, hogy eltávolítsa azoknak az összetevőknek a verzióit, amelyeket már nem kíván aktívnak. Ezután frissítse az összes virtuális szolgáltatást, hogy ne hivatkozzezekre a verziókra.

Mivel már nincs forgalom az összetevők egyik régebbi verzióihoz sem, most már biztonságosan törölheti az összetevők összes központi telepítését.

![Az AKS voting alkalmazás összetevői és útválasztása.](media/servicemesh/istio/scenario-routing-components-05.png)

Most sikeresen kivezették az AKS voting alkalmazás új verzióját.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása 

Az ebben a forgatókönyvben használt AKS szavazási alkalmazást a névtér törlésével az alábbiak szerint távolíthatja el az `voting` AKS-fürtből:

```console
kubectl delete namespace voting
```

A következő példa kimenetazt mutatja, hogy az AKS szavazási alkalmazás összes összetevője el lett távolítva az AKS-fürtből.

```output
namespace "voting" deleted
```

## <a name="next-steps"></a>További lépések

További forgatókönyveket is megvizsgálhat az [Istio Bookinfo alkalmazás példával.][istio-bookinfo-example]

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
[istio-install]: ./servicemesh-istio-install.md
