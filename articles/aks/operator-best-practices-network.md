---
title: Az operátorokkal kapcsolatos gyakorlati tanácsok – Hálózati kapcsolat az Azure Kubernetes-szolgáltatásokban (AKS)
description: Ismerje meg a fürtüzemeltető bevált módszereit a virtuális hálózati erőforrásokés a kapcsolat az Azure Kubernetes szolgáltatás (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: 93659a0891b09c83db9f63fe0756fcf4d7e87f6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594685"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Hálózati kapcsolatra és biztonságra vonatkozó ajánlott eljárások az Azure Kubernetes Service-ben (AKS)

Afürtök létrehozása és kezelése az Azure Kubernetes Szolgáltatás (AKS), hálózati kapcsolatot biztosít a csomópontok és alkalmazások. Ezek a hálózati erőforrások közé tartoznak az IP-címtartományok, a terheléselosztók és a be- és betolakodók. Az alkalmazások magas színvonalú szolgáltatásának fenntartásához meg kell terveznie, majd konfigurálnia kell ezeket az erőforrásokat.

Ez az ajánlott eljárások ról szóló cikk a hálózati kapcsolatra és a fürtüzemeltetők biztonságára összpontosít. Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * A kubenet és az Azure CNI hálózati módok összehasonlítása az AKS-ben
> * Tervezze meg a szükséges IP-címzést és -kapcsolatot
> * Forgalom elosztása terheléselosztók, betolakodó vezérlők vagy webalkalmazás-tűzfal (WAF) használatával
> * Biztonságos csatlakozás fürtcsomópontokhoz

## <a name="choose-the-appropriate-network-model"></a>Válassza ki a megfelelő hálózati modellt

**Gyakorlati tanácsok –** A meglévő virtuális hálózatokkal vagy helyszíni hálózatokkal való integrációhoz használja az Azure CNI-hálózatot az AKS-ben. Ez a hálózati modell lehetővé teszi az erőforrások és a vezérlők nagyobb elkülönítését vállalati környezetben.

A virtuális hálózatok biztosítják az alapvető kapcsolatot az AKS-csomópontok és az ügyfelek számára az alkalmazások eléréséhez. Az AKS-fürtökvirtuális hálózatokba történő üzembe helyezésének két különböző módja van:

* **Kubenet hálózatkezelés** – Az Azure kezeli a virtuális hálózati erőforrásokat a fürt üzembe helyezésekor, és a [kubenet][kubenet] Kubernetes bővítményt használja.
* **Azure CNI-hálózat –** egy meglévő virtuális hálózatba telepíthető, és az [Azure Container Networking Interface (CNI)][cni-networking] Kubernetes beépülő modult használja. A podok egyéni IP-címeket kapnak, amelyek más hálózati szolgáltatásokhoz vagy helyszíni erőforrásokhoz továbbíthatók.

A Tárolóhálózati csatoló (CNI) egy szállító-semleges protokoll, amely lehetővé teszi, hogy a tároló futásidejű kéréseket a hálózati szolgáltató. Az Azure CNI ip-címeket rendel a podokhoz és a csomópontokhoz, és IP-címkezelési (IP-címkezelő) funkciókat biztosít, amint csatlakozik a meglévő Azure virtuális hálózatokhoz. Minden csomópont- és pod-erőforrás kap egy IP-címet az Azure virtuális hálózatban, és nincs szükség további útválasztásra más erőforrásokkal vagy szolgáltatásokkal való kommunikációhoz.

![Két csomópontot ábrázoló, egyetlen Azure-alapú virtuális hálózathoz összekötő hidakkal](media/operator-best-practices-network/advanced-networking-diagram.png)

A legtöbb éles környezetben, az Azure CNI-hálózat használatát kell használnia. Ez a hálózati modell lehetővé teszi az erőforrások vezérlésének és kezelésének elkülönítését. Biztonsági szempontból gyakran szeretné, hogy a különböző csapatok kezeljék és biztosítsák ezeket az erőforrásokat. Az Azure CNI-hálózatlehetővé teszi, hogy csatlakozzon a meglévő Azure-erőforrásokhoz, helyszíni erőforrásokhoz vagy más szolgáltatásokhoz közvetlenül az egyes podokhoz rendelt IP-címeken keresztül.

Az Azure CNI-hálózat használatakor a virtuális hálózati erőforrás az AKS-fürt egy külön erőforráscsoportban található. Az aks szolgáltatásnév engedélyeinek delegálása az erőforrások eléréséhez és kezeléséhez. Az AKS-fürt által használt egyszerű szolgáltatásnak legalább [hálózati közreműködői](../role-based-access-control/built-in-roles.md#network-contributor) engedélyekkel kell rendelkeznie a virtuális hálózaton belüli alhálózaton. Ha a beépített hálózati közreműködői szerepkör használata helyett [egyéni szerepkört](../role-based-access-control/custom-roles.md) szeretne definiálni, a következő engedélyek szükségesek:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Az AKS-szolgáltatáselső delegálásról további információt a [Más Azure-erőforrásokhoz való hozzáférés delegálása][sp-delegation]című témakörben talál.

Mivel minden csomópont és pod saját IP-címet kap, tervezze meg az AKS-alhálózatok címtartományait. Az alhálózatnak elég nagynak kell lennie ahhoz, hogy ip-címeket adjon meg minden üzembe helyezett csomóponthoz, podhoz és hálózati erőforráshoz. Minden AKS-fürtnek a saját alhálózatába kell helyezni. A helyszíni vagy társviszony-létesített hálózatokhoz való kapcsolódás engedélyezéséhez az Azure-ban ne használjon olyan IP-címtartományokat, amelyek átfedésben vannak a meglévő hálózati erőforrásokkal. Az egyes csomópontok kubenet és Azure CNI-hálózattal futtatott podok száma alapértelmezett korlátokat állapít meg. A horizontális felskálázási események vagy a fürtfrissítések kezeléséhez további IP-címekre is szüksége van a hozzárendelt alhálózatban való használatra. Ez a további címterület különösen fontos, ha Windows Server-tárolókat használ (jelenleg előzetes verzióban az AKS-ben), mivel ezek a csomópontkészletek frissítést igényelnek a legújabb biztonsági javítások telepítéséhez. A Windows Server-csomópontokról az [AKS csomópontkészletének frissítése][nodepool-upgrade]című témakörben talál további információt.

A szükséges IP-cím kiszámításáról az [Azure CNI-hálózat konfigurálása az AKS-ben][advanced-networking]című témakörben található.

### <a name="kubenet-networking"></a>Kubenet hálózatépítés

Bár a kubenet nem követeli meg a virtuális hálózatok beállítását a fürt telepítése előtt, vannak hátrányai:

* A csomópontok és a podok különböző IP-alhálózatokon vannak elhelyezve. A felhasználó által definiált útválasztás (UDR) és az IP-továbbítás a podok és csomópontok közötti forgalom irányítására szolgál. Ez a további útválasztás csökkentheti a hálózati teljesítményt.
* A meglévő helyszíni hálózatokkal való kapcsolatok vagy más Azure virtuális hálózatokkal való társviszony-létesítés összetett lehet.

A Kubenet kisebb fejlesztési vagy tesztelési számítási feladatokhoz alkalmas, mivel nem kell a virtuális hálózatot és az alhálózatokat az AKS-fürttől elkülönítve létrehoznia. Az alacsony forgalmú egyszerű webhelyek, illetve a számítási feladatok tárolókba való áthelyezésére szintén kihasználhatják a kubenet-hálózatkezeléssel üzembe helyezett AKS-fürtök egyszerűségét. A legtöbb éles környezetben, meg kell terveznie, és használja az Azure CNI-hálózat. Saját [IP-címtartományokat és virtuális hálózatokat is konfigurálhat a kubenet használatával.][aks-configure-kubenet-networking]

## <a name="distribute-ingress-traffic"></a>Be- és forgalom elosztása

**Ajánlott eljárások –** HTTP- vagy HTTPS-forgalom terjesztése az alkalmazások között, használja a bejövő erőforrások at és a vezérlőket. A be- éselődési vezérlők további funkciókat biztosítanak egy normál Azure-terheléselosztón keresztül, és natív Kubernetes-erőforrásokként kezelhetők.

Az Azure-terheléselosztó az Ügyfélforgalmat az AKS-fürtben lévő alkalmazások között oszthatja el, de korlátozott abban, amit az adott forgalomról megért. A terheléselosztó erőforrás a 4. A HTTP-t vagy HTTPS protokollt használó legtöbb webalkalmazásnak Kuberenetes bejövő erőforrásokat és vezérlőket kell használnia, amelyek a 7. A be- és forgalom az alkalmazás URL-címe alapján terjesztheti a forgalmat, és kezelheti a TLS/SSL-végződést. Ez a képesség csökkenti a letett és leképezett IP-címek számát is. A terheléselosztó, minden alkalmazás általában szüksége van egy nyilvános IP-címet rendelve, és az AKS-fürtben a szolgáltatáshoz van rendelve. Egy be- és elő-vissza erőforrás egyetlen IP-cím forgalmat oszthat több alkalmazás.

![AKS-fürt ben a forgalom áramlását bemutató diagram](media/operator-best-practices-network/aks-ingress.png)

 A be- és visszalépésnek két összetevője van:

 * Egy be- és visszafutó *erőforrás,* és
 * Egy be- és vissza- és *visszaadatzuk a vezérlőt*

A bejövő forgalom erőforrás egy `kind: Ingress` YAML-jegyzékfájl, amely meghatározza az állomás, tanúsítványok és szabályok a forgalmat az AKS-fürtben futó szolgáltatásokhoz. A következő példa a YAML-jegyzékfájl forgalmat oszt a *myapp.com* két szolgáltatás, *a blogszolgáltatás* vagy *a storeservice egyikére.* Az ügyfél az egyik vagy a másik szolgáltatáshoz irányul az általuk elért URL-cím alapján.

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

A bejövő adatkezelő egy démon, amely egy AKS-csomóponton fut, és figyeli a bejövő kérelmeket. A forgalom ezután a bejövő forgalomban meghatározott szabályok alapján oszlik el. A leggyakoribb bejövő adatbeviteli vezérlő az [NGINX-en]alapul. Az AKS nem korlátozza önt egy adott vezérlőre, így más vezérlőket is használhat, például [a Kontúrt,][contour] [a HAProxyt][haproxy]vagy [a Traefik-et.][traefik]

A rendszernek linuxos csomóponton kell ütemeznie a be- és énekLes-vezérlőket. A Windows Server-csomópontok (jelenleg előzetes verzióban az AKS) nem futtatható a bejövő adatbeviteli vezérlő. A YAML-jegyzékfájlban vagy a Helm-diagram központi telepítésében használjon csomópontválasztót annak jelzésére, hogy az erőforrásnak Linux-alapú csomóponton kell futnia. További információ: [A csomópontválasztók használata annak szabályozására, hogy a podok hol vannak ütemezve az AKS-ben.][concepts-node-selectors]

A be- és a be- és abefoglaláshoz számos forgatókönyv létezik, többek között a következő útmutatók:

* [Egyszerű be- és átszivárgási vezérlő létrehozása külső hálózati kapcsolattal][aks-ingress-basic]
* [Belső, magánhálózati és IP-címet használó be- és áthálózaton belüli adatélmény-vezérlő létrehozása][aks-ingress-internal]
* [Saját TLS-tanúsítványokat használó be- és hanghálózati vezérlő létrehozása][aks-ingress-own-tls]
* Bejövő forgalom vezérlő létrehozása, amely a Let's Encrypt segítségével automatikusan generáltTLS-tanúsítványokat [dinamikus nyilvános IP-címmel][aks-ingress-tls] vagy [statikus nyilvános IP-címmel][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Biztonságos forgalom webalkalmazás-tűzfallal (WAF)

**Ajánlott eljárások –** A bejövő forgalom potenciális támadások felderítéséhez használjon egy webalkalmazás tűzfal (WAF), például [barracuda WAF az Azure-hoz][barracuda-waf] vagy az Azure Application Gateway. Ezek a fejlettebb hálózati erőforrások a forgalmat csak a HTTP- és HTTPS-kapcsolatokon vagy az alapvető SSL-végződtetésen túl is irányíthatják.

A szolgáltatások és alkalmazások forgalmat elosztott bejövő forgalom általában egy Kubernetes-erőforrás az AKS-fürtben. A vezérlő démonként fut egy AKS-csomóponton, és felhasználja a csomópont erőforrásainak egy részét, például a PROCESSZORt, a memóriát és a hálózati sávszélességet. Nagyobb környezetekben gyakran szeretné kiszervezni a forgalom útválasztásának vagy a TLS-végződésnek egy részét az AKS-fürtön kívüli hálózati erőforrásnak. Azt is szeretné, hogy ellenőrizze a bejövő forgalom a lehetséges támadásokat.

![A webalkalmazás-tűzfal (WAF), például az Azure App Gateway képes megvédeni és terjeszteni a forgalmat az AKS-fürt](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

A webalkalmazás-tűzfal (WAF) a bejövő forgalom szűrésével további biztonsági réteget biztosít. Az Open Web Application Security Project (OWASP) olyan szabályokat biztosít, amelyek figyelik az olyan támadásokat, mint a webhelyek közötti parancsfájlok vagy a cookie-k mérgezése. [Az Azure Application Gateway][app-gateway] (jelenleg előzetes verzióban az AKS- ben) egy WAF, amely integrálható az AKS-fürtökkel, hogy biztosítsa ezeket a biztonsági funkciókat, mielőtt a forgalom elérné az AKS-fürtöt és -alkalmazásokat. Más, harmadik féltől származó megoldások is elláthatják ezeket a funkciókat, így továbbra is használhatja a meglévő befektetéseket vagy szakértelmet egy adott termékben.

A terheléselosztó vagy a bejövő forgalom erőforrásai továbbra is futnak az AKS-fürtben a forgalomeloszlás további finomítása érdekében. Az App Gateway központilag kezelhető egy erőforrás-definícióval rendelkező be- és erőforrás-vezérlőként. Első lépésekhez [hozzon létre egy Application Gateway ingress vezérlőt.][app-gateway-ingress]

## <a name="control-traffic-flow-with-network-policies"></a>Forgalomáramlás szabályozása hálózati házirendekkel

**Ajánlott eljárások –** a hálózati házirendek használatával engedélyezi vagy letagadja a podok forgalmát. Alapértelmezés szerint a fürtön belüli podok között minden forgalom engedélyezett. A nagyobb biztonság érdekében határozza meg azokat a szabályokat, amelyek korlátozzák a podkommunikációt.

A hálózati házirend egy Kubernetes szolgáltatás, amely lehetővé teszi a podok közötti forgalom szabályozását. Dönthet úgy, hogy engedélyezi vagy letagadja a forgalmat olyan beállítások alapján, mint a hozzárendelt címkék, névtér vagy forgalmi port. A hálózati házirendek használata felhőalapú módot biztosít a forgalom áramlásának szabályozására. A podok dinamikusan jönnek létre egy AKS-fürtben, a szükséges hálózati házirendek automatikusan alkalmazható. Ne használja az Azure hálózati biztonsági csoportokat a pod-to-pod forgalom szabályozására, hálózati házirendek használatára.

A hálózati házirend használatához a szolgáltatást engedélyezni kell, amikor AKS-fürtöt hoz létre. Meglévő AKS-fürtön nem engedélyezhető a hálózati házirend. Előre tervezzen, hogy engedélyezze a hálózati házirendet a fürtökön, és szükség szerint használhassa őket. A hálózati házirendet csak Linux-alapú csomópontokhoz és podok az AKS-ben kell használni.

A hálózati házirend Kubernetes-erőforrásként jön létre YAML-jegyzékfájl használatával. A szabályzatok meghatározott podokra vonatkoznak, majd a be- vagy kilépési szabályok határozzák meg, hogy a forgalom hogyan tud folyni. A következő példa egy hálózati házirendet alkalmaz az alkalmazással rendelkező *podokra: háttércímke* rájuk alkalmazva. A be- és a be- és visszaút szabály, majd csak engedélyezi a forgalmat a podok az *alkalmazás: előtér* címke:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

A szabályzatok használatának első lépései: [Biztonságos forgalom a podok között az Azure Kubernetes-szolgáltatás (AKS) hálózati szabályzatok használatával.][use-network-policies]

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Biztonságos csatlakozás csomópontokhoz egy megerősített állomáson keresztül

**Ajánlott eljárások –** Ne tegye elérhetővé a távoli kapcsolatot az AKS-csomópontok. Hozzon létre egy megerősített állomást vagy ugródobozt egy felügyeleti virtuális hálózatban. A megerősített állomás segítségével biztonságosan irányíthatja a forgalmat az AKS-fürtbe a távoli felügyeleti feladatokhoz.

Az AKS legtöbb művelete elvégezhető az Azure felügyeleti eszközeivel vagy a Kubernetes API-kiszolgálón keresztül. Az AKS-csomópontok nem kapcsolódnak a nyilvános internethez, és csak magánhálózaton érhetők el. A csomópontokhoz való csatlakozáshoz, a karbantartási vagy hibaelhárításhoz irányítsa a kapcsolatokat egy megerősített állomáson vagy ugródobozon keresztül. Ennek az állomásnak egy külön felügyeleti virtuális hálózatban kell lennie, amely biztonságosan az AKS-fürt virtuális hálózatához kapcsolódik.

![Csatlakozás AKS-csomópontokhoz megerősített állomás vagy ugródoboz használatával](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

A megerősített gazdagép felügyeleti hálózatát is biztosítani kell. Azure [ExpressRoute-][expressroute] vagy [VPN-átjáró][vpn-gateway] használatával csatlakozhat egy helyszíni hálózathoz, és szabályozhatja a hozzáférést a hálózati biztonsági csoportok használatával.

## <a name="next-steps"></a>További lépések

Ez a cikk a hálózati kapcsolatra és a biztonságra összpontosított. A Kubernetes hálózati alapjairól az [Azure Kubernetes-szolgáltatás (AKS) hálózati fogalmai című témakörben][aks-concepts-network] talál további információt.

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[Nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool