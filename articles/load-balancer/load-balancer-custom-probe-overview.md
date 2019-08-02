---
title: A szolgáltatás magas rendelkezésre állásának biztosítása Azure Load Balancer állapot-mintavételek használatával
titlesuffix: Azure Load Balancer
description: Állapot-mintavételei használata a terheléselosztó mögött példányok figyelése céljából
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: allensu
ms.openlocfilehash: 75009530940a0cce7adb8469ead5f55f509a1faa
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68275337"
---
# <a name="load-balancer-health-probes"></a>Load Balancer állapot-mintavételei

Azure Load Balancer a terheléselosztási szabályokkal használható állapot-mintavételt biztosít.  Az állapot-mintavételi konfiguráció és a mintavételi válaszok határozzák meg, hogy mely háttérbeli készlet-példányok kapják meg az új folyamatokat. Állapot-mintavételei használatával észleli a hibát egy alkalmazás egy háttér-példányon. Egyéni választ is létrehozhat az állapot-mintavételre, és a Flow Control állapot-ellenőrzésével kezelheti a terhelést vagy a tervezett állásidőt. Ha az állapotfigyelő mintavételező nem sikerül, a terheléselosztó nem irányít, több új folyamatok a megfelelő nem megfelelő állapotú példányhoz.

Az állapotadatok több protokollt is támogatnak. Egy adott típusú állapot-mintavételi típus rendelkezésre állása egy adott protokoll támogatásához Load Balancer SKU-ra változik.  Emellett a szolgáltatás működése Load Balancer SKU-ra változik.

| | Standard termékváltozat | Alapszintű termékváltozat |
| --- | --- | --- |
| [Mintavétel típusok](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Mintavételi viselkedésének lefelé](#probedown) | Az összes mintavételek le, az összes TCP-adatfolyamok továbbra is. | Az összes TCP-folyamat lejár. | 

> [!IMPORTANT]
> Load Balancer állapot-mintavételek az IP-168.63.129.16 származnak, és nem szabad blokkolni a mintavételek számára a példányok jelölését.  Felülvizsgálat [IP-forráscím mintavételi](#probesource) részleteiről.

## <a name="types"></a>Mintavétel típusok

Az állapot-mintavétel a következő három protokoll használatával állítható be a figyelőkhöz:

- [TCP-figyelő](#tcpprobe)
- [HTTP-végpontokat](#httpprobe)
- [Koncové body protokolu HTTPS](#httpsprobe)

Az elérhető típusokat állapot-mintavételei eltérőek lehetnek attól függően, a Load Balancer Termékváltozat kiválasztott:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standard termékváltozat |    &#9989; |   &#9989; |   &#9989; |
| Alapszintű termékváltozat |   &#9989; |   &#9989; | &#10060; |

Függetlenül attól, hogy melyik mintavételi típust választja, az állapot-mintavételek megfigyelheti a háttér-példányok bármely portját, beleértve azt a portot is, amelyen a tényleges szolgáltatást megadták.

### <a name="tcpprobe"></a> TCP-mintavétel

TCP-mintavétel kapcsolatot kezdeményezzen egy háromutas nyitott TCP kézfogás-a meghatározott portot elvégzésével.  A TCP-mintavételek egy négyirányú záró TCP-kézfogással szüntetik meg a kapcsolatokat.

A minimális mintavételi időköz 5 másodperc, a nem megfelelő állapotú válaszok minimális számát pedig 2.  Az összes intervallum teljes időtartama nem haladhatja meg a 120 másodpercet.

A TCP-mintavétel meghiúsul, ha:
* A TCP-figyelőt a példányon egyáltalán nem válaszol az időkorláton során.  A mintavétel a sikertelen mintavételi kérelmek száma alapján van megjelölve, amelyek úgy vannak konfigurálva, hogy a mintavétel előtt megválaszolják a választ.
* A mintavétel egy TCP alaphelyzetbe állítani a példányt kap.

#### <a name="resource-manager-template"></a>Resource Manager-sablon

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

> [!NOTE]
> Csak akkor érhető el, a HTTPS-mintavétel [Standard Load Balancer](load-balancer-standard-overview.md).

A HTTP-és HTTPS-mintavételek a TCP-mintavételre épülnek, és a megadott elérési úttal rendelkező HTTP GET-t adnak ki. Relatív elérési utakat is az ilyen mintavételezők támogatja a HTTP GET. HTTPS-vizsgálatok ugyanazok, mint a HTTP-mintavételek a Transport Layer Security (TLS, SSL-ként ismert) igény szerinti hozzáadásával burkolót. Az állapotminta van megjelölve, ha a példány válaszol a HTTP-állapotkódot 200 az időkorláton belül.  Az állapot mintavétele alapértelmezés szerint 15 másodpercenként megkísérli a beállított állapot mintavételi portjának ellenőrzését. A minimális mintavételi időköz 5 másodperc. Az összes intervallum teljes időtartama nem haladhatja meg a 120 másodpercet.

A HTTP/HTTPS-mintavételek akkor is hasznosak lehetnek, ha ki szeretné fejezni az állapot-mintavételt.  implementálja saját logikáját, hogy eltávolítsa a példányokat a terheléselosztó forgása alól, ha a mintavételi port a szolgáltatáshoz is a figyelő. Például előfordulhat, hogy úgy dönt, eltávolít egy példányt, ha ez meghaladja a 90 %-ot, és nem 200-as HTTP - állapot adja vissza. 

Ha Felhőszolgáltatásokat használ, és webes szerepkörök, amelyek a w3wp.exe rendelkezik, akkor is megvalósítható automatikus figyeléssel a webhely. Hibák a webhely kódban nem 200 állapot térjen vissza a terheléselosztói mintavételezők.

Egy olyan HTTP / HTTPS-mintavétel meghiúsul, ha:
* Állapotminta-végpontot egy HTTP-válaszkód, 200-as (például a 403-as, 404-es vagy 500-as) eltérő adja vissza. Ekkor a rendszer azonnal megjelöli az állapot-mintavételt. 
* A mintavételi végpont nem válaszol a 31 másodperces időkorlát alatt. Több mintavételi kérelem is megválaszolható, mielőtt a mintavétel nem fut állapotba kerül, és az összes időtúllépési intervallum összegét el nem éri.
* Állapotminta-végpontot lezárja a kapcsolatot a TCP alaphelyzetbe állítása keresztül.

#### <a name="resource-manager-templates"></a>Resource Manager-sablonok

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

További információkért lásd: [a szolgáltatásdefiníciós fájlt (csdef) konfigurálja az állapotmintákat](https://msdn.microsoft.com/library/azure/ee758710.aspx) vagy [első lépésként hozza létre a cloud services nyilvános load balancer](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Ha a vendégügynök nem válaszol a HTTP 200 OK, a terheléselosztó nem válaszol, a példány jelöli meg. Majd leállítja az küld a flow-példányhoz. A load balancer továbbra is fennáll, ellenőrizze a példány. 

Ha a vendégügynök válaszol egy HTTP 200-as, a terheléselosztó küld új folyamatok példányhoz újra.

Webes szerepkör használata esetén a webhely kód általában lefut az w3wp.exe, amely az Azure által nem felügyelt hálóhoz vagy Vendég ügynök. A vendégügynök nem jelentett hibák a w3wp.exe (például a HTTP 500-as választ). Ennek következtében a terheléselosztó nem használ annak a példánynak a rotációból.

<a name="health"></a>
## <a name="probehealth"></a>Állapot-mintavételi

TCP, HTTP és HTTPS állapot-mintavételei tekinti a kifogástalan állapotú, és jelölje meg a szerepkörpéldány kifogástalan amennyiben:

* Az állapot mintavétele a virtuális gép indítása után sikeres volt.
* A szerepkör-példány kifogástalanként való megjelöléséhez szükséges mintavételek száma teljesült.

> [!NOTE]
> Ha az állapot-mintavétel ingadozik, a terheléselosztó továbbra is vár, mielőtt a szerepkör-példányt visszaállítja a Kifogástalan állapotba. Az extra várakozási idő a felhasználó és az infrastruktúra védi, és olyan szándékos szabályzat.

## <a name="probe-count-and-timeout"></a>Mintavétel száma és időtúllépés

Állapotminta viselkedésére függ:

* Sikeres, amelyek lehetővé teszik egy példányt való megjelölésének mintavételek száma szerint, akár.
* Egy példány való megjelölésének kiváltó sikertelen mintavételek száma leállítottnak.

A megadott időkorlát-és intervallum-értékek határozzák meg, hogy egy példány fel vagy le van-e jelölve.

## <a name="probedown"></a>Mintavételi viselkedésének lefelé

### <a name="tcp-connections"></a>TCP-kapcsolatok

Az új TCP-kapcsolatok sikeresek maradnak az egészséges háttérbeli példányok számára.

Ha egy háttér-példány állapotadat-mintavétel meghiúsul, a háttér-példányhoz létrehozott TCP-kapcsolatok továbbra is.

Minden mintavételek háttérkészlet szereplő összes példány sikertelen lesz, ha nincsenek új folyamatok küld a háttérkészlethez. A standard Load Balancer lehetővé teszik a létrehozott TCP-adatfolyamok folytatásához.  Alapszintű Load Balancer le fog állni az összes meglévő TCP forgalom a háttérkészlethez.
 
Load Balancer egy átmenő szolgáltatás (nem szakítja meg a TCP-kapcsolatokat), és a folyamat mindig az ügyfél és a virtuális gép vendég operációs rendszere és alkalmazása között van. Az összes mintavétel nélküli készlet esetén a frontend nem válaszol a TCP-kapcsolatok nyílt kísérleteinek (SYN) állapotára, mivel nincs kifogástalan állapotú háttér-példány a folyamat fogadására és a SYN-ACK-k megválaszolására.

### <a name="udp-datagrams"></a>UDP-datagramok

UDP-datagramok megfelelően működő háttér-példányok lesz elküldve.

UDP kapcsolat nélküli, és nincs teljesítményfolyamati állapot nyomon követett UDP-hez. Ha bármely háttérrendszer példányra állapotvizsgálat nem sikerül, előfordulhat, hogy meglévő UDP-forgalom helyezheti át egy másik kifogástalan példányra a háttérkészletben.

Minden mintavételek háttérkészlet szereplő összes példány sikertelen lesz, ha a meglévő UDP-folyamatok az alapszintű és standard szintű terheléselosztóhoz le fog állni.

<a name="source"></a>
## <a name="probesource"></a>Mintavételi forrás IP-címe

Terheléselosztó egy elosztott-ellenőrzési szolgáltatás a belső állapotmodell használja. A szondázás szolgáltatás minden olyan gazdagépen megtalálható, ahol a virtuális gépek, és igény szerint programozhatók, hogy az ügyfél konfigurációján alapuló rendszerállapot-mintavételek készüljenek. Az állapot mintavételi forgalma közvetlenül az állapot-mintavételt és az ügyfél virtuális gépet létrehozó szondázás szolgáltatás között van. Az összes Load Balancer állapot-mintavételei származnak 168.63.129.16 IP-címről használja forrásként.  Az IP-címtartomány egy olyan VNet belül használható, amely nem RFC1918 terület.  Globálisan fenntartott Microsoft tulajdonú IP-cím használata csökkenti az IP-címek ütközését a VNet belül használt IP-címtartomány miatt.  Ez az IP-cím minden régióban azonos, és nem változik, és nem biztonsági kockázat, mert csak a belső Azure platform összetevő képes a csomagok forrására ebből az IP-címről. 

A AzureLoadBalancer szolgáltatás címkéje azonosítja ezt a forrás IP-címet a [hálózati biztonsági csoportokban](../virtual-network/security-overview.md) , és alapértelmezés szerint engedélyezi az állapot-mintavételi forgalmat.

Load Balancer Health-mintavételen kívül a [következő műveletek ezt az IP-címet használják](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Lehetővé teszi, hogy a Virtuálisgép-ügynök való kommunikációhoz. a platformot, hogy jelezze a "Kész" állapotban van
- Lehetővé teszi a kommunikációt a szűrt névfeloldást biztosítanak az ügyfelek számára az egyéni DNS-kiszolgálókat nem határoznak meg a DNS-kiszolgáló.  Ez a szűrés biztosítja, hogy ügyfeleink csak oldható meg a gazdagép az üzemelő példány neve.
- Lehetővé teszi, hogy a virtuális gép dinamikus IP-címet szerezzen be a DHCP szolgáltatásból az Azure-ban.

## <a name="design"></a>Tervezési útmutató

A rendszer az állapot-mintavételt használja a szolgáltatás rugalmas és méretezhetővé tételéhez. A helytelen konfiguráció vagy a rossz kialakítási minta befolyásolhatja a szolgáltatás rendelkezésre állását és méretezhetőségét. Tekintse át ezt a teljes dokumentumot, és gondolja át, milyen hatással van a forgatókönyvre, ha a mintavételi válasz megjelölve vagy megjelölve van, és hogyan befolyásolja az alkalmazási forgatókönyv rendelkezésre állását.

Amikor megtervezi az alkalmazáshoz tartozó állapot modelljét, a háttér-példányon található portot olyan portra kell figyelnie, amely az adott példány állapotát és a megadott alkalmazás __-__ szolgáltatást tükrözi.  Az alkalmazás portjának és a mintavételi portnak nem kell megegyeznie.  Bizonyos helyzetekben érdemes lehet a mintavételi port más, mint az alkalmazás által a szolgáltatás által biztosított port.  

Esetenként hasznos lehet az alkalmazás számára, hogy az állapot-mintavételi választ ne csak az alkalmazás állapotának észlelésére, hanem közvetlenül a Load Balancer is jelezze, ha a példánynak fogadnia kell vagy nem kap új folyamatokat.  A mintavételi választ úgy is kezelheti, hogy az alkalmazás ellennyomás hozzon létre, és szabályozza az új folyamatok egy példányra való továbbítását az állapot-ellenőrzés elvégzésével, illetve az alkalmazás karbantartásának előkészítésével és a forgatókönyv kiürítésének elindításával.  Standard Load Balancer használatakor a mintavétel [](#probedown) nélküli jelek mindig lehetővé teszik a TCP-forgalom folytatását az Üresjárati időkorlát vagy a kapcsolat lezárása után. 

Az UDP-terheléselosztáshoz egyéni állapot-mintavételi jelet kell kiállítani a háttér-példányból, és a megfelelő figyelőhöz tartozó TCP-, HTTP-vagy HTTPS-alapú állapot-mintavételt kell használnia, hogy tükrözze az UDP-alkalmazás állapotát.

Ha a [hektáros portok terheléselosztási](load-balancer-ha-ports-overview.md) szabályait [standard Load Balancerekkel](load-balancer-standard-overview.md)használja, az összes port terheléselosztásra kerül, és egyetlen állapot-mintavételi válasznak kell tükröznie a teljes példány állapotát.

Ne fordítsa le vagy ne vezessen olyan állapotú mintavételt az VNet egy másik példányára, amely megkapja az állapot-mintavételt, mivel ez a konfiguráció lépcsőzetes hibákat eredményezhet a forgatókönyvben.  Vegye figyelembe a következő helyzetet: a külső gyártótól származó készülékek készletét egy Load Balancer erőforrás háttér-készletében helyezi üzembe, amely biztosítja a berendezések méretezését és redundanciát, valamint az állapot mintavételét úgy, hogy a külső gyártótól származó vagy a berendezés mögött más virtuális gépekre fordít.  Ha ugyanazt a portot használja, amellyel lefordítja vagy proxyul kéri a többi virtuális gépre irányuló kéréseket a készülék mögött, akkor a készülék mögötti egyetlen virtuális gépről érkező összes mintavételi válasz megjelöli a berendezést. Ez a konfiguráció a teljes alkalmazási forgatókönyv lépcsőzetes meghibásodását eredményezheti a berendezés mögötti egyetlen backend-példány eredményeként.  Az trigger lehet egy időszakos mintavételi hiba, amely Load Balancer az eredeti célhely (a berendezés példánya) megjelölését, és a teljes alkalmazás-forgatókönyv letiltását eredményezi. A készülék állapotának mintavétele helyette. A mintavétel kiválasztásával megállapítható, hogy az állapot jelzése a hálózati virtuális berendezések (NVA-) forgatókönyvek esetében fontos szempont-e

Ha nem engedélyezi a mintavétel [forrás IP-címét](#probesource) a tűzfal házirendjeiben, az állapot-mintavétel sikertelen lesz, mert nem tudja elérni a példányát.  Load Balancer megjelöli, a példány a állapot-mintavételi hiba miatt le.  Ez a helytelen konfiguráció a terheléselosztási alkalmazás meghibásodását okozhatja.

Ahhoz, hogy a Load Balancer állapotának mintavétele megtörténjen a példányon, engedélyeznie **kell** ezt az IP-címet bármely Azure-beli [hálózati biztonsági csoportban](../virtual-network/security-overview.md) és helyi tűzfal-házirendben.  Alapértelmezés szerint minden hálózati biztonsági csoport tartalmazza az AzureLoadBalancer az állapot-mintavételi forgalom engedélyezésére szolgáló [szolgáltatási címkét](../virtual-network/security-overview.md#service-tags) .

Ha szeretné tesztelni az állapot-mintavételi hibát, vagy egy különálló példányt szeretne megjelölni, akkor a [hálózati biztonsági csoportokkal](../virtual-network/security-overview.md) explicit módon blokkolhatja az állapot-mintavételt (a célport vagy a [forrás IP-címét](#probesource)), és szimulálhatja a mintavétel hibáját.

Ne konfigurálja a VNet a 168.63.129.16-t tartalmazó Microsoft tulajdonú IP-címtartományt.  Az ilyen konfigurációk ütköznek az állapot-mintavétel IP-címével, és a forgatókönyv meghibásodását okozhatja.

Ha a virtuális Gépen több adapterrel rendelkezik, annak érdekében, hogy a mintavétel a kapcsolaton, a kapott válaszol szüksége.  Előfordulhat, hogy a hálózati címfordítást a virtuális gépen, illesztőfelület alapján kell lefordítani.

Ne engedélyezze a [TCP](https://tools.ietf.org/html/rfc1323)-időbélyeget.  A TCP-időbélyegek engedélyezése azt eredményezi, hogy az állapot-ellenőrzéseket a virtuális gép vendég operációs rendszerének TCP-vereme által eldobott TCP-csomagok okozzák, ami Load Balancer megjelöli a megfelelő végpontot.  A TCP-időbélyegek alapértelmezés szerint engedélyezve vannak a biztonsági megerősített virtuális gépek rendszerképein, és le kell tiltani őket.

## <a name="monitoring"></a>Figyelés

A nyilvános és a belső [Standard Load Balancer](load-balancer-standard-overview.md) végpont és a háttérkiszolgáló példány állapota Hálózatfigyelő állapot szerint, többdimenziós metrikák az Azure monitoron keresztül teszi közzé. Ezeket a metrikákat más Azure-szolgáltatások vagy-partneri alkalmazások is felhasználhatják. 

Az alapszintű nyilvános Load Balancer a háttér-készletek alapján összesíti az állapot mintavételi állapotát Azure Monitor naplókon keresztül.  A belső alapszintű terheléselosztó számára nem érhetők el Azure Monitor naplók.  [Azure monitor naplók](load-balancer-monitor-log.md) használatával megtekintheti a nyilvános terheléselosztó mintavételi állapotának állapotát és a mintavételek darabszámát. Adja meg a load balancer állapot statisztikája naplózás használható a Power bi-ban vagy az Azure Operational Insights.

## <a name="limitations"></a>Korlátozások

- HTTPS-vizsgálatok nem támogatják a kölcsönös hitelesítés ügyféltanúsítvánnyal.
- Az állapot-tesztek sikertelenek lesznek, ha a TCP-időbélyegek engedélyezve vannak.

## <a name="next-steps"></a>További lépések

- További tudnivalók a [Standard Load Balancerről](load-balancer-standard-overview.md)
- [Első lépések a Resource Managerben nyilvános load balancer létrehozása PowerShell használatával](load-balancer-get-started-internet-arm-ps.md)
- [Állapot-mintavételei REST API-val](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Az új állapotfigyelő mintavételi képességekhez kérelem [Load Balancer uservoice-on](https://aka.ms/lbuservoice)
