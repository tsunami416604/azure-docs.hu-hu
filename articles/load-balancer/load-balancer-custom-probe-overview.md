---
title: A szolgáltatás méretezésére és a rendelkezésre állásának biztosítására szolgáló állapot-próbák
titleSuffix: Azure Load Balancer
description: Ebből a cikkből megtudhatja, hogyan használhatók az állapot-mintavételek a Azure Load Balancer mögötti példányok figyeléséhez
services: load-balancer
documentationcenter: na
author: asudbring
manager: kumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: fdc7254b4c6e798c0f32f5fac3575474ed6ec1d0
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077076"
---
# <a name="load-balancer-health-probes"></a>A Load Balancer állapotmintái

Ha a terheléselosztási szabályokat Azure Load Balancer használatával használja, meg kell adnia egy állapot-mintavételt, hogy az Load Balancer a háttérbeli végpont állapotának észlelését.  Az állapot-mintavétel és a mintavételi válaszok konfigurációja határozza meg, hogy mely háttérbeli készlet-példányok kapják meg az új folyamatokat. Az állapot-mintavételek segítségével észlelhető egy alkalmazás meghibásodása egy háttér-végponton. Egyéni választ is létrehozhat az állapot-mintavételre, és a Flow Control állapot-ellenőrzésével kezelheti a terhelést vagy a tervezett állásidőt. Ha az állapot-mintavétel sikertelen, Load Balancer leállítja az új folyamatok küldését a megfelelő sérült példányra.

Az állapotadatok több protokollt is támogatnak. Egy adott állapot mintavételi protokolljának rendelkezésre állása Load Balancer SKU-tól függ.  Emellett a szolgáltatás működése Load Balancer SKU-ban is változik a táblázatban látható módon:

| | Standard termékváltozat | Alapszintű termékváltozat |
| --- | --- | --- |
| [Mintavétel típusok](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Mintavételi viselkedésének lefelé](#probedown) | Az összes mintavételek le, az összes TCP-adatfolyamok továbbra is. | Az összes TCP-folyamat lejár. | 


>[!IMPORTANT]
>Tekintse át a dokumentumot teljes egészében, beleértve az alábbi fontos [tervezési útmutatót](#design) egy megbízható szolgáltatás létrehozásához.

>[!IMPORTANT]
>Load Balancer állapot-mintavételek az IP-168.63.129.16 származnak, és nem szabad blokkolni a mintavételek számára a példányok jelölését.  Felülvizsgálat [IP-forráscím mintavételi](#probesource) részleteiről.

## <a name="probes"></a>Mintavételi konfiguráció

Az állapot-mintavételi konfiguráció a következő elemekből áll:

- Az egyéni mintavételek közötti intervallum időtartama
- Azoknak a mintavételi válaszoknak a száma, amelyeket meg kell figyelni a mintavétel más állapotba való átállása előtt
- A mintavétel protokollja
- A mintavétel portja
- Http-alapú http-(S-) mintavételek használatakor használandó HTTP-elérési út

> [!NOTE]
> Azure PowerShell, Azure CLI, sablonok vagy API használata esetén a mintavételi definíció nem kötelező, vagy nincs bejelölve. A mintavétel-ellenőrzési tesztek csak az Azure Portal használata esetén hajthatók végre.

## <a name="understanding-application-signal-detection-of-the-signal-and-reaction-of-the-platform"></a>Az alkalmazási jel ismertetése, a jel észlelése és a platform reakciója

A mintavételi válaszok száma mindkettőre vonatkozik

- azon sikeres mintavételek száma, amelyek lehetővé teszik, hogy a példány megjelölve legyen megjelölve, és
- azon sikertelen mintavételek száma, amelyek miatt egy példány le lesz jelölve.

A megadott időkorlát-és intervallum-értékek határozzák meg, hogy egy példány fel vagy le lesz-e jelölve.  Az Intervallum időtartamának szorzata a mintavételi válaszok száma határozza meg azt az időtartamot, ameddig a mintavételi válaszokat észlelni kell.  A szolgáltatás pedig a szükséges mintavételek elvégzése után fog reagálni.

A viselkedést egy példával is illusztráljuk. Ha a mintavételi válaszok számát a 2 értékre állítja, és az intervallum 5 másodpercre van állítva, akkor ez azt jelenti, hogy a mintavételi hibákat 10 másodpercen belül meg kell figyelni.  Mivel a mintavétel elküldésének időpontja nincs szinkronizálva az alkalmazás állapotának megváltozásakor, a következő két forgatókönyv szerint lehet megkötni az időt:

1. Ha az alkalmazás megkezdi az első mintavétel megkezdése előtt egy sikertelen mintavételi választ, az események észlelése 10 másodpercet vesz igénybe (2 x 5 másodperc intervallum), valamint az alkalmazás időtartamát, amely az első a mintavétel megérkezik.  Feltételezzük, hogy az észlelés valamivel több mint 10 másodpercet vesz igénybe.
2. Ha az alkalmazás közvetlenül az első mintavétel után kezdi meg a sikertelen mintavételt, akkor az események észlelése nem kezdődik el, amíg a következő mintavétel megérkezik (és sikertelen lesz), plusz egy 10 másodpercet (2 x 5 másodperces intervallum).  Feltételezheti, hogy ez az észlelés 15 másodpercen belül eltarthat.

Ebben a példában az észlelést követően a platform némi időt vesz igénybe, hogy reagáljon erre a változásra.  Ez a következőtől függ: 

1. Amikor az alkalmazás megkezdi az állapot módosítását, és
2. Ha ez a változás észlelhető, és megfelel a szükséges feltételeknek (a mintavételek száma a megadott időközönként történik), és
3. Ha az észlelést a platformon keresztül közölték 

Tegyük fel, hogy a hibás mintavételre adott reakció legalább 10 másodpercnél kevesebb, míg 15 másodpercnél több időt vesz igénybe, hogy reagáljon az alkalmazáson belüli jel változására.  Ez a példa arra szolgál, hogy bemutassa, mi történik, de nem lehet előre jelezni a fenti durva útmutatón túli pontos időtartamot.
 
## <a name="types"></a>Mintavétel típusok

Az állapot-mintavétel által használt protokoll a következők egyikére konfigurálható:

- [TCP-figyelő](#tcpprobe)
- [HTTP-végpontokat](#httpprobe)
- [Koncové body protokolu HTTPS](#httpsprobe)

Az elérhető protokollok a használt Load Balancer SKU-tól függnek:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standard termékváltozat |    &#9989; |   &#9989; |   &#9989; |
| Alapszintű termékváltozat |   &#9989; |   &#9989; | &#10060; |

### <a name="tcpprobe"></a> TCP-mintavétel

TCP-mintavétel kapcsolatot kezdeményezzen egy háromutas nyitott TCP kézfogás-a meghatározott portot elvégzésével.  A TCP-mintavételek egy négyirányú záró TCP-kézfogással szüntetik meg a kapcsolatokat.

A minimális mintavételi időköz 5 másodperc, a nem megfelelő állapotú válaszok minimális számát pedig 2.  Az összes intervallum teljes időtartama nem haladhatja meg a 120 másodpercet.

A TCP-mintavétel meghiúsul, ha:
* A TCP-figyelőt a példányon egyáltalán nem válaszol az időkorláton során.  A mintavétel a sikertelen mintavételi kérelmek száma alapján van megjelölve, amelyek úgy vannak konfigurálva, hogy a mintavétel előtt megválaszolják a választ.
* A mintavétel egy TCP alaphelyzetbe állítani a példányt kap.

Az alábbi ábrán látható, hogyan fejezheti ki az ilyen típusú mintavételi konfigurációkat egy Resource Manager-sablonban:

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="httpprobe"></a> <a name="httpsprobe"></a> HTTP / HTTPS-mintavétel

>[!NOTE]
>Csak akkor érhető el, a HTTPS-mintavétel [Standard Load Balancer](load-balancer-standard-overview.md).

A HTTP-és HTTPS-mintavételek a TCP-mintavételre épülnek, és a megadott elérési úttal rendelkező HTTP GET-t adnak ki. Relatív elérési utakat is az ilyen mintavételezők támogatja a HTTP GET. HTTPS-vizsgálatok ugyanazok, mint a HTTP-mintavételek a Transport Layer Security (TLS, SSL-ként ismert) igény szerinti hozzáadásával burkolót. Az állapotminta van megjelölve, ha a példány válaszol a HTTP-állapotkódot 200 az időkorláton belül.  Az állapot mintavétele alapértelmezés szerint 15 másodpercenként megkísérli a beállított állapot mintavételi portjának ellenőrzését. A minimális mintavételi időköz 5 másodperc. Az összes intervallum teljes időtartama nem haladhatja meg a 120 másodpercet.

A HTTP-/HTTPS-mintavételek is hasznosak lehetnek a saját logikájának megvalósításához, hogy eltávolítsa a példányokat a terheléselosztó forgása alól, ha a mintavételi port egyben a szolgáltatás figyelője is. Például előfordulhat, hogy úgy dönt, eltávolít egy példányt, ha ez meghaladja a 90 %-ot, és nem 200-as HTTP - állapot adja vissza. 

Ha Felhőszolgáltatásokat használ, és webes szerepkörök, amelyek a w3wp.exe rendelkezik, akkor is megvalósítható automatikus figyeléssel a webhely. Hibák a webhely kódban nem 200 állapot térjen vissza a terheléselosztói mintavételezők.

Egy olyan HTTP / HTTPS-mintavétel meghiúsul, ha:
* Állapotminta-végpontot egy HTTP-válaszkód, 200-as (például a 403-as, 404-es vagy 500-as) eltérő adja vissza. Ekkor a rendszer azonnal megjelöli az állapot-mintavételt. 
* A mintavételi végpont nem válaszol a 31 másodperces időkorlát alatt. Több mintavételi kérelem is megválaszolható, mielőtt a mintavétel nem fut állapotba kerül, és az összes időtúllépési intervallum összegét el nem éri.
* Állapotminta-végpontot lezárja a kapcsolatot a TCP alaphelyzetbe állítása keresztül.

Az alábbi ábrán látható, hogyan fejezheti ki az ilyen típusú mintavételi konfigurációkat egy Resource Manager-sablonban:

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guestagent"></a>Vendég ügynök szonda (csak Klasszikus modell)

A felhőszolgáltatásokhoz tartozó szerepkörök (a feldolgozói szerepkörök és a webes szerepkörök) mintavételi figyelés alapértelmezés szerint a vendégügynököt használja.  A vendég ügynök mintavétele a legutóbb konfigurált konfiguráció.  Az állapot-mintavételt mindig explicit módon, TCP-vagy HTTP-mintavételsel kell használni. Vendég ügynök mintavétel nem hatásos, explicit módon megadott mintavételek a legtöbb alkalmazás forgatókönyvhöz.

Vendég ügynök mintavétel a vendégügynököt a virtuális gép ellenőrzése. Ezután figyeli, és válaszol egy HTTP 200 OK válasz csak akkor, ha a példány a kész állapotban van. (A többi állapotot foglalt, újrahasznosítás vagy leállítása is.)

További információkért lásd: [a szolgáltatásdefiníciós fájlt (csdef) konfigurálja az állapotmintákat](https://msdn.microsoft.com/library/azure/ee758710.aspx) vagy [első lépésként hozza létre a cloud services nyilvános load balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-internet-classic-cloud#check-load-balancer-health-status-for-cloud-services).

Ha a vendégügynök nem válaszol a HTTP 200 OK, a terheléselosztó nem válaszol, a példány jelöli meg. Majd leállítja az küld a flow-példányhoz. A load balancer továbbra is fennáll, ellenőrizze a példány. 

Ha a vendégügynök válaszol egy HTTP 200-as, a terheléselosztó küld új folyamatok példányhoz újra.

Webes szerepkör használata esetén a webhely kód általában lefut az w3wp.exe, amely az Azure által nem felügyelt hálóhoz vagy Vendég ügynök. A vendégügynök nem jelentett hibák a w3wp.exe (például a HTTP 500-as választ). Ennek következtében a terheléselosztó nem használ annak a példánynak a rotációból.

<a name="health"></a>
## <a name="probehealth"></a>Mintavételezési viselkedés

A TCP, a HTTP és a HTTPS állapotú tesztek kifogástalannak minősülnek, és a háttér-végpontot a következőképpen kell megjelölni:

* Az állapot mintavétele a virtuális gép indítása után sikeres volt.
* A háttérbeli végpont megfelelő állapotának megjelöléséhez szükséges mintavételek száma teljesült.

Minden olyan háttér-végpont, amely kifogástalan állapotot ért el, jogosult az új folyamatok fogadására.  

> [!NOTE]
> Ha az állapot-mintavétel ingadozik, a terheléselosztó a háttér-végpont Kifogástalan állapotba való visszaállítását követően is megvárja a problémát. Az extra várakozási idő a felhasználó és az infrastruktúra védi, és olyan szándékos szabályzat.

## <a name="probedown"></a>Mintavételi viselkedésének lefelé

### <a name="tcp-connections"></a>TCP-kapcsolatok

Az új TCP-kapcsolatok sikeresen megmaradnak az egészséges háttérbeli végponton.

Ha a háttérbeli végpont állapota nem sikerül, a rendszer a háttérbeli végponthoz tartozó TCP-kapcsolatokat folytatja.

Minden mintavételek háttérkészlet szereplő összes példány sikertelen lesz, ha nincsenek új folyamatok küld a háttérkészlethez. A standard Load Balancer lehetővé teszik a létrehozott TCP-adatfolyamok folytatásához.  Alapszintű Load Balancer le fog állni az összes meglévő TCP forgalom a háttérkészlethez.
 
Load Balancer egy átmenő szolgáltatás (nem szakítja meg a TCP-kapcsolatokat), és a folyamat mindig az ügyfél és a virtuális gép vendég operációs rendszere és alkalmazása között van. Az összes mintavétel nélküli készlet esetén a frontend nem válaszol a TCP-kapcsolatok nyílt kísérleteinek (SYN) állapotára, mivel nincs kifogástalan háttér-végpont a folyamat fogadásához és a SYN-ACK-vel való reagáláshoz.

### <a name="udp-datagrams"></a>UDP-datagramok

Az UDP-datagramok kifogástalan háttérbeli végpontokra lesznek továbbítva.

UDP kapcsolat nélküli, és nincs teljesítményfolyamati állapot nyomon követett UDP-hez. Ha a háttérbeli végpont állapotának mintavétele sikertelen, a meglévő UDP-folyamatok a háttér-készlet egy másik kifogástalan példányára léphetnek.

Minden mintavételek háttérkészlet szereplő összes példány sikertelen lesz, ha a meglévő UDP-folyamatok az alapszintű és standard szintű terheléselosztóhoz le fog állni.

<a name="source"></a>
## <a name="probesource"></a>Mintavételi forrás IP-címe

Terheléselosztó egy elosztott-ellenőrzési szolgáltatás a belső állapotmodell használja. A szondázás szolgáltatás minden olyan gazdagépen megtalálható, ahol a virtuális gépek, és igény szerint programozhatók, hogy az ügyfél konfigurációján alapuló rendszerállapot-mintavételek készüljenek. Az állapot mintavételi forgalma közvetlenül az állapot-mintavételt és az ügyfél virtuális gépet létrehozó szondázás szolgáltatás között van. Az összes Load Balancer állapot-mintavételei származnak 168.63.129.16 IP-címről használja forrásként.  Az IP-címtartomány egy olyan VNet belül használható, amely nem RFC1918 terület.  Globálisan fenntartott Microsoft tulajdonú IP-cím használata csökkenti az IP-címek ütközését a VNet belül használt IP-címtartomány miatt.  Ez az IP-cím minden régióban azonos, és nem változik, és nem biztonsági kockázat, mert csak a belső Azure platform összetevő képes a csomagok forrására ebből az IP-címről. 

A AzureLoadBalancer szolgáltatás címkéje azonosítja ezt a forrás IP-címet a [hálózati biztonsági csoportokban](../virtual-network/security-overview.md) , és alapértelmezés szerint engedélyezi az állapot-mintavételi forgalmat.

Load Balancer Health [-mintavételen kívül a következő műveletek ezt az IP-címet használják](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Lehetővé teszi, hogy a Virtuálisgép-ügynök való kommunikációhoz. a platformot, hogy jelezze a "Kész" állapotban van
- Lehetővé teszi a kommunikációt a szűrt névfeloldást biztosítanak az ügyfelek számára az egyéni DNS-kiszolgálókat nem határoznak meg a DNS-kiszolgáló.  Ez a szűrés biztosítja, hogy ügyfeleink csak oldható meg a gazdagép az üzemelő példány neve.
- Lehetővé teszi, hogy a virtuális gép dinamikus IP-címet szerezzen be a DHCP szolgáltatásból az Azure-ban.

## <a name="design"></a>Tervezési útmutató

A rendszer az állapot-mintavételt használja a szolgáltatás rugalmas és méretezhetővé tételéhez. A helytelen konfiguráció vagy a rossz kialakítási minta befolyásolhatja a szolgáltatás rendelkezésre állását és méretezhetőségét. Tekintse át ezt a teljes dokumentumot, és gondolja át, milyen hatással van a forgatókönyvre, ha a mintavételi válasz megjelölve vagy megjelölve van, és hogyan befolyásolja az alkalmazási forgatókönyv rendelkezésre állását.

Amikor megtervezi az alkalmazáshoz tartozó állapotfigyelő modellt, a háttér-végponton található portot kell mintavételre felhasználnia, amely tükrözi az adott példány állapotát __és__ az Ön által biztosított alkalmazást.  Az alkalmazás portjának és a mintavételi portnak nem kell megegyeznie.  Bizonyos helyzetekben érdemes lehet a mintavételi port más, mint az alkalmazás által a szolgáltatás által biztosított port.  

Esetenként hasznos lehet az alkalmazás számára, hogy az állapot-mintavételi választ ne csak az alkalmazás állapotának észlelésére, hanem közvetlenül a Load Balancer is jelezze, ha a példánynak fogadnia kell vagy nem kap új folyamatokat.  A mintavételi választ úgy is kezelheti, hogy az alkalmazás ellennyomás hozzon létre, és szabályozza az új folyamatok egy példányra való továbbítását az állapot-ellenőrzés elvégzésével, illetve az alkalmazás karbantartásának előkészítésével és a forgatókönyv kiürítésének elindításával.  Standard Load Balancer használatakor a mintavétel nélküli jelek mindig lehetővé [teszik a TCP](#probedown) -forgalom folytatását az Üresjárati időkorlát vagy a kapcsolat lezárása után. 

Az UDP-terheléselosztáshoz egyéni állapot-mintavételi jelet kell kiállítani a háttérbeli végpontból, és a megfelelő figyelőhöz tartozó TCP-, HTTP-vagy HTTPS-alapú állapot-mintavételt kell használnia, hogy tükrözze az UDP-alkalmazás állapotát.

Ha a [hektáros portok terheléselosztási szabályait](load-balancer-ha-ports-overview.md) [standard Load Balancerekkel](load-balancer-standard-overview.md)használja, az összes port terheléselosztásra kerül, és egyetlen állapot-mintavételi válasznak kell tükröznie a teljes példány állapotát.

Ne fordítsa le vagy ne vezessen olyan állapotú mintavételt az VNet egy másik példányára, amely megkapja az állapot-mintavételt, mivel ez a konfiguráció lépcsőzetes hibákat eredményezhet a forgatókönyvben.  Vegye figyelembe a következő helyzetet: a külső gyártótól származó készülékek készletét egy Load Balancer erőforrás háttér-készletében helyezi üzembe, amely biztosítja a berendezések méretezését és redundanciát, valamint az állapot mintavételét úgy, hogy a külső gyártótól származó vagy a berendezés mögött más virtuális gépekre fordít.  Ha ugyanazt a portot használja, amellyel lefordítja vagy proxyul kéri a többi virtuális gépre irányuló kéréseket a készülék mögött, akkor a készülék mögötti egyetlen virtuális gépről érkező összes mintavételi válasz megjelöli a berendezést. Ez a konfiguráció a teljes alkalmazási forgatókönyv lépcsőzetes meghibásodását eredményezheti a berendezés mögötti egyetlen háttérbeli végpont eredményeként.  Az trigger lehet egy időszakos mintavételi hiba, amely Load Balancer az eredeti célhely (a berendezés példánya) megjelölését, és a teljes alkalmazás-forgatókönyv letiltását eredményezi. A készülék állapotának mintavétele helyette. A mintavétel kiválasztásával megállapítható, hogy az állapot jelzése a hálózati virtuális berendezések (NVA-) forgatókönyvek esetében fontos szempont-e

Ha nem engedélyezi a mintavétel [forrás IP-címét](#probesource) a tűzfal házirendjeiben, az állapot-mintavétel sikertelen lesz, mert nem tudja elérni a példányát.  Load Balancer megjelöli, a példány a állapot-mintavételi hiba miatt le.  Ez a helytelen konfiguráció a terheléselosztási alkalmazás meghibásodását okozhatja.

Ahhoz, hogy a Load Balancer állapotának mintavétele megtörténjen a példányon, engedélyeznie **kell** ezt az IP-címet bármely Azure-beli [hálózati biztonsági csoportban](../virtual-network/security-overview.md) és helyi tűzfal-házirendben.  Alapértelmezés szerint minden hálózati biztonsági csoport tartalmazza az AzureLoadBalancer az állapot-mintavételi forgalom engedélyezésére szolgáló [szolgáltatási címkét](../virtual-network/security-overview.md#service-tags) .

Ha szeretné tesztelni az állapot-mintavételi hibát, vagy egy különálló példányt szeretne megjelölni, akkor a [hálózati biztonsági csoportokkal](../virtual-network/security-overview.md) explicit módon blokkolhatja az állapot-mintavételt (a célport vagy a [forrás IP-címét](#probesource)), és szimulálhatja a mintavétel hibáját.

Ne konfigurálja a VNet a 168.63.129.16-t tartalmazó Microsoft tulajdonú IP-címtartományt.  Az ilyen konfigurációk ütköznek az állapot-mintavétel IP-címével, és a forgatókönyv meghibásodását okozhatja.

Ha a virtuális Gépen több adapterrel rendelkezik, annak érdekében, hogy a mintavétel a kapcsolaton, a kapott válaszol szüksége.  Előfordulhat, hogy a hálózati címfordítást a virtuális gépen, illesztőfelület alapján kell lefordítani.

Ne engedélyezze a [TCP-időbélyeget](https://tools.ietf.org/html/rfc1323).  A TCP-időbélyegek engedélyezése a virtuális gép vendég operációs rendszerének TCP-verem által eldobott TCP-csomagok meghibásodása esetén az állapot-ellenőrzéseket okozhatja, ami Load Balancer megjelöli a megfelelő végpontot.  A TCP-időbélyegek alapértelmezés szerint engedélyezve vannak a biztonsági megerősített virtuális gépek rendszerképein, és le kell tiltani őket.

## <a name="monitoring"></a>Figyelés

Mind a nyilvános, mind a belső [standard Load Balancer](load-balancer-standard-overview.md) a végpontok és a háttérbeli végpontok állapotának tesztelési állapota többdimenziós metrikák Azure monitoron keresztül. Ezeket a metrikákat más Azure-szolgáltatások vagy-partneri alkalmazások is felhasználhatják. 

Az alapszintű nyilvános Load Balancer a háttér-készletek alapján összesíti az állapot mintavételi állapotát Azure Monitor naplókon keresztül.  A belső alapszintű terheléselosztó számára nem érhetők el Azure Monitor naplók.  [Azure monitor naplók](load-balancer-monitor-log.md) használatával megtekintheti a nyilvános terheléselosztó mintavételi állapotának állapotát és a mintavételek darabszámát. Adja meg a load balancer állapot statisztikája naplózás használható a Power bi-ban vagy az Azure Operational Insights.

## <a name="limitations"></a>Korlátozások

- HTTPS-vizsgálatok nem támogatják a kölcsönös hitelesítés ügyféltanúsítvánnyal.
- Ha a TCP-időbélyegek engedélyezve vannak, a assumehHealth-mintavétel sikertelen lesz.

## <a name="next-steps"></a>További lépések

- További tudnivalók a [Standard Load Balancerről](load-balancer-standard-overview.md)
- [Első lépések a Resource Managerben nyilvános load balancer létrehozása PowerShell használatával](load-balancer-get-started-internet-arm-ps.md)
- [Állapot-mintavételei REST API-val](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Az új állapotfigyelő mintavételi képességekhez kérelem [Load Balancer uservoice-on](https://aka.ms/lbuservoice)
