---
title: Load Balancer állapot-mintavételei használatával a szolgáltatás védelme
titlesuffix: Azure Load Balancer
description: Állapot-mintavételei használata a terheléselosztó mögött példányok figyelése céljából
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/11/2018
ms.author: kumud
ms.openlocfilehash: d3e0fd510968abed55e0e3fea2a8689027713a2b
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310402"
---
# <a name="load-balancer-health-probes"></a>Load Balancer állapot-mintavételei

Az Azure Load Balancer állapot-mintavételei alapján határozza meg, mely háttérbeli címkészlet példányok új folyamatok fog kapni. Állapot-mintavételei használatával észleli a hibát egy alkalmazás egy háttér-példányon. Is hozzon létre egy állapotmintát egyéni választ, és használja az állapotmintát esetében forgalomszabályozás, és jelezze a Load Balancer-e a folytatáshoz küldjön új folyamatokat, vagy új folyamatok küld egy háttér-példány leállítása. Ez a terhelés és a tervezett leállás kezelésére használható. Ha az állapotfigyelő mintavételező nem sikerül, a terheléselosztó nem irányít, több új folyamatok a megfelelő nem megfelelő állapotú példányhoz.

Az állapot-mintavételei elérhető típusú és állapot-mintavételek viselkednek attól függ, milyen Termékváltozat Load Balancer, módja. Például az új és meglévő folyamatok működése függ egy flow-e a TCP vagy UDP, valamint melyik Load Balancer Termékváltozatot használja.

| | Standard termékváltozat | Alapszintű termékváltozat |
| --- | --- | --- |
| [Mintavétel típusok](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Mintavételi viselkedésének lefelé](#probedown) | Az összes mintavételek le, az összes TCP-adatfolyamok továbbra is. | Minden mintavételek le, az összes TCP-adatfolyamok megszűnik. | 

> [!IMPORTANT]
> Load Balancer állapot-mintavételei származnak 168.63.129.16 IP-címről, és nem le kell tiltani a mintavételek a példány megjelöléséhez.  Felülvizsgálat [IP-forráscím mintavételi](#probesource) részleteiről.

## <a name="types"></a>Mintavétel típusok

Állapot-mintavételei figyelheti, bármely portra háttér-példány, beleértve a portot, amelyen elérhető a tényleges service. Az egészségügyi mintavételi protokoll állapotadat-mintavételek három különböző típusú konfigurálhatók:

- [TCP-figyelő](#tcpprobe)
- [HTTP-végpontokat](#httpprobe)
- [Koncové body protokolu HTTPS](#httpsprobe)

Az elérhető típusokat állapot-mintavételei eltérőek lehetnek attól függően, a Load Balancer Termékváltozat kiválasztott:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standard termékváltozat |    &#9989; |   &#9989; |   &#9989; |
| Alapszintű termékváltozat |   &#9989; |   &#9989; | &#10060; |

UDP terheléselosztásra kell létrehoznia egy egyéni állapot mintavételi jel a háttér-példány használatával a TCP, HTTP-n vagy HTTPS-állapotminta.

Használata esetén [magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabályok](load-balancer-ha-ports-overview.md) a [Standard Load Balancer](load-balancer-standard-overview.md), minden port elosztott terhelésű, és egyetlen egészségügyi mintavételi választ az egész példány állapotának szeretné bemutatni.  

Meg kell, nem a NAT- és egy állapotminta a példány, amely egy másik példányhoz az állapotminta kap a virtuális hálózatban található, mivel ez a hiba egész hibasorozatot indíthat ebben az esetben proxy.

Ha szeretné egészségügyi mintavételi hiba tesztelése, vagy egy egyéni példány le megjelölése, explicit letiltása az állapotminta egy biztonsági csoportot használhatja (cél vagy [forrás](#probesource)).

### <a name="tcpprobe"></a> TCP-mintavétel

TCP-mintavétel kapcsolatot kezdeményezzen egy háromutas nyitott TCP kézfogás-a meghatározott portot elvégzésével.  Ezt követi a négy Bezárás TCP kézfogás.

A minimális mintavételi időköz 5 másodperc, a nem megfelelő állapotú válaszok minimális számát pedig 2.  A teljes időtartam legfeljebb 120 másodperc.

A TCP-mintavétel meghiúsul, ha:
* A TCP-figyelőt a példányon egyáltalán nem válaszol az időkorláton során.  Mintavétel van megjelölve lettek konfigurálva nyissa meg a megválaszolatlan a mintavétel megjelölése előtti meghiúsult mintavételi kérések száma alapján.
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

A HTTP és HTTPS-vizsgálatok TCP-kapcsolatot létesítsen, és a egy HTTP GET probléma a megadott elérési út. Relatív elérési utakat is az ilyen mintavételezők támogatja a HTTP GET. HTTPS-vizsgálatok ugyanazok, mint a HTTP-mintavételek a Transport Layer Security (TLS, SSL-ként ismert) igény szerinti hozzáadásával burkolót. Az állapotminta van megjelölve, ha a példány válaszol a HTTP-állapotkódot 200 az időkorláton belül.  Ezek állapotának mintavételei próbál meg alapértelmezés szerint 15 másodpercenként ellenőrizze a beállított állapot-mintavételi portot. A minimális mintavételi időköz 5 másodperc. A teljes időtartam legfeljebb 120 másodperc. 

HTTP / HTTPS-vizsgálatok akkor is hasznos lehet, ha azt szeretné megvalósítani a saját logikai példányt eltávolítja a load balancer rotációból. Például előfordulhat, hogy úgy dönt, eltávolít egy példányt, ha ez meghaladja a 90 %-ot, és nem 200-as HTTP - állapot adja vissza. 

Ha Felhőszolgáltatásokat használ, és webes szerepkörök, amelyek a w3wp.exe rendelkezik, akkor is megvalósítható automatikus figyeléssel a webhely. Hibák a webhely kódban nem 200 állapot térjen vissza a terheléselosztói mintavételezők.  A HTTP-mintavétel felülbírálja az alapértelmezett Vendég ügynök mintavétel. 

Egy olyan HTTP / HTTPS-mintavétel meghiúsul, ha:
* Állapotminta-végpontot egy HTTP-válaszkód, 200-as (például a 403-as, 404-es vagy 500-as) eltérő adja vissza. Ezzel a művelettel kijelöli az állapotminta azonnal. 
* Állapotminta-végpontot a 31-ig második időkorlát során egyáltalán nem válaszol. Az időtúllépési érték, amely be van állítva, attól függően több mintavételi kérések mehet megválaszolatlan előtt nem fut, a mintavétel megjelölve (azaz előtt SuccessFailCount mintavételek érkeznek).
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

A felhőszolgáltatásokhoz tartozó szerepkörök (a feldolgozói szerepkörök és a webes szerepkörök) mintavételi figyelés alapértelmezés szerint a vendégügynököt használja.   Vegye figyelembe a végső lehetőség.  Egy explicit módon egy TCP-állapotminta vagy a HTTP-mintavétel mindig érdemes definiálni. Vendég ügynök mintavétel nem hatásos, explicit módon megadott mintavételek a legtöbb alkalmazás forgatókönyvhöz.  

Vendég ügynök mintavétel a vendégügynököt a virtuális gép ellenőrzése. Ezután figyeli, és válaszol egy HTTP 200 OK válasz csak akkor, ha a példány a kész állapotban van. (A többi állapotot foglalt, újrahasznosítás vagy leállítása is.)

További információkért lásd: [a szolgáltatásdefiníciós fájlt (csdef) konfigurálja az állapotmintákat](https://msdn.microsoft.com/library/azure/ee758710.aspx) vagy [első lépésként hozza létre a cloud services nyilvános load balancer](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Ha a vendégügynök nem válaszol a HTTP 200 OK, a terheléselosztó nem válaszol, a példány jelöli meg. Majd leállítja az küld a flow-példányhoz. A load balancer továbbra is fennáll, ellenőrizze a példány. 

Ha a vendégügynök válaszol egy HTTP 200-as, a terheléselosztó küld új folyamatok példányhoz újra.

Webes szerepkör használata esetén a webhely kód általában lefut az w3wp.exe, amely az Azure által nem felügyelt hálóhoz vagy Vendég ügynök. A vendégügynök nem jelentett hibák a w3wp.exe (például a HTTP 500-as választ). Ennek következtében a terheléselosztó nem használ annak a példánynak a rotációból.

## <a name="probehealth"></a>Állapot-mintavételi

TCP, HTTP és HTTPS állapot-mintavételei tekinti a kifogástalan állapotú, és jelölje meg a szerepkörpéldány kifogástalan amennyiben:

* Az állapotminta létrejött a virtuális gép elindul, amikor első alkalommal.
* (Lásd a korábbiakban) SuccessFailCount számát határozza meg a szerepkörpéldány kifogástalan állapotúként kell megjelölnie sikeres mintavételezők értékét. Ha egy szerepkörpéldány el lett távolítva, sikeres, egymást követő mintavételek száma egyenlő vagy kell megjelölni a szerepkörpéldány futtatásával SuccessFailCount értéke meghaladja.

> [!NOTE]
> Ha egy szerepkörpéldány állapotát ingadozik, a terheléselosztó vár már előtt a szerepkörpéldány helyezi el a kifogástalan állapotban. Az extra várakozási idő a felhasználó és az infrastruktúra védi, és olyan szándékos szabályzat.

## <a name="probe-count-and-timeout"></a>Mintavétel száma és időtúllépés

Állapotminta viselkedésére függ:

* Sikeres, amelyek lehetővé teszik egy példányt való megjelölésének mintavételek száma szerint, akár.
* Egy példány való megjelölésének kiváltó sikertelen mintavételek száma leállítottnak.

SuccessFailCount időkorlátja és a gyakoriság értéke határozza meg, e-példány fut vagy nem fut ellenőrzése. Az Azure Portalon az időtúllépés értéke a gyakoriság értékének kétszer.

Terheléselosztási szabály egyetlen állapotmintát definiálva van a megfelelő háttérkészlet.

## <a name="probedown"></a>Mintavételi viselkedésének lefelé

### <a name="tcp-connections"></a>TCP-kapcsolatok

Új TCP-kapcsolatok sikeresek lesznek, kifogástalan állapotú, és a vendég operációs rendszer és az alkalmazás képes fogadni az új folyamat háttér-példányhoz.

Ha egy háttér-példány állapotadat-mintavétel meghiúsul, a háttér-példányhoz létrehozott TCP-kapcsolatok továbbra is.

Minden mintavételek háttérkészlet szereplő összes példány sikertelen lesz, ha nincsenek új folyamatok küld a háttérkészlethez. A standard Load Balancer lehetővé teszik a létrehozott TCP-adatfolyamok folytatásához.  Alapszintű Load Balancer le fog állni az összes meglévő TCP forgalom a háttérkészlethez.
 
Mivel a folyamat minden esetben az ügyfél és a virtuális gép vendég operációs rendszerek között, a készlet összes mintákkal le egy előtérbeli ne válaszolna a TCP-kapcsolódási nyílt kísérletek nem megfelelően működő háttér-példány megkapja a folyamat nem miatt.

### <a name="udp-datagrams"></a>UDP-datagramok

UDP-datagramok megfelelően működő háttér-példányok lesz elküldve.

UDP kapcsolat nélküli, és nincs teljesítményfolyamati állapot nyomon követett UDP-hez. Ha bármely háttérrendszer példányra állapotvizsgálat nem sikerül, előfordulhat, hogy meglévő UDP-forgalom helyezheti át egy másik kifogástalan példányra a háttérkészletben.

Minden mintavételek háttérkészlet szereplő összes példány sikertelen lesz, ha a meglévő UDP-folyamatok az alapszintű és standard szintű terheléselosztóhoz le fog állni.

## <a name="probesource"></a>Mintavételi forrás IP-címe

Terheléselosztó egy elosztott-ellenőrzési szolgáltatás a belső állapotmodell használja. Minden gazdagépen, amelyen a virtuális gépek találhatók programozása állapotadat-mintavételek száma az ügyfél-konfiguráció generálásához. A health mintavételi forgalom közvetlenül az infrastruktúra összetevője, amely az állapotminta generál és az ügyfél virtuális gép között van. Az összes Load Balancer állapot-mintavételei származnak 168.63.129.16 IP-címről használja forrásként.  Tenné a saját IP-címek az Azure virtuális hálózathoz, ha ezen állapotfigyelő mintavételi forrás IP-címet garantáltan egyedinek kell lennie, globálisan a Microsoft számára van fenntartva.  Ez a cím minden régióban azonos, és nem változik. Azt nem tekinthető biztonsági kockázatot jelent, mivel csak a belső Azure platformon is forrás egy csomag az IP-címről. 

Load Balancer állapot-mintavételei mellett a következő műveletek használata az IP-címet:

- Lehetővé teszi, hogy a Virtuálisgép-ügynök való kommunikációhoz. a platformot, hogy jelezze a "Kész" állapotban van
- Lehetővé teszi a kommunikációt a szűrt névfeloldást biztosítanak az ügyfelek számára az egyéni DNS-kiszolgálókat nem határoznak meg a DNS-kiszolgáló.  Ez a szűrés biztosítja, hogy ügyfeleink csak oldható meg a gazdagép az üzemelő példány neve.

A terheléselosztó állapotmintát való megjelöléséhez a példány, meg **kell** bármely Azure-ban az IP-címének engedélyezéséhez [biztonsági csoportok](../virtual-network/security-overview.md) és a helyi tűzfal-házirendek.  Alapértelmezés szerint minden hálózati biztonsági csoport tartalmazza a [szolgáltatáscímke](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer állapot-mintavételi forgalom engedélyezéséhez.

Ha a tűzfal-házirendek IP-cím nem engedélyezett, az állapotminta sikertelen lesz, mivel nem tudja elérni a példány.  Load Balancer megjelöli, a példány a állapot-mintavételi hiba miatt le.  Emiatt az elosztott terhelésű szolgáltatás leáll. 

Emellett ne konfigurálja a virtuális hálózat a Microsoft tulajdonában lévő IP-címtartományt, amely tartalmazza a 168.63.129.16.  Ez az állapotminta IP-címmel lesz ütközik.

Ha a virtuális Gépen több adapterrel rendelkezik, annak érdekében, hogy a mintavétel a kapcsolaton, a kapott válaszol szüksége.  Ez lehet szükség egyedi forrás NAT'ing ezt a címet a virtuális gép / felületen történik.

## <a name="monitoring"></a>Figyelés

A nyilvános és a belső [Standard Load Balancer](load-balancer-standard-overview.md) végpont és a háttérkiszolgáló példány állapota Hálózatfigyelő állapot szerint, többdimenziós metrikák az Azure monitoron keresztül teszi közzé. Ez majd által felhasználható más Azure-szolgáltatások vagy a 3. fél alkalmazásokat. 

Alapszintű nyilvános Load Balancer összegzése a Log Analytics funkciójával háttérkészlet mintavételi állapotát mutatja.  Ez nem használható belső alapszintű Load Balancer Terheléselosztók.  Használhat [log analytics](load-balancer-monitor-log.md) a nyilvános load balancer mintavételi állapot ellenőrzéséhez és mintavételi száma. Adja meg a load balancer állapot statisztikája naplózás használható a Power bi-ban vagy az Azure Operational Insights.

## <a name="limitations"></a>Korlátozások

-  HTTPS-vizsgálatok nem támogatják a kölcsönös hitelesítés ügyféltanúsítvánnyal.

## <a name="next-steps"></a>További lépések

- További tudnivalók a [Standard Load Balancerről](load-balancer-standard-overview.md)
- [Első lépések a Resource Managerben nyilvános load balancer létrehozása PowerShell használatával](load-balancer-get-started-internet-arm-ps.md)
- [Állapot-mintavételei REST API-val](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Az új állapotfigyelő mintavételi képességekhez kérelem [Load Balancer uservoice-on](https://aka.ms/lbuservoice)
