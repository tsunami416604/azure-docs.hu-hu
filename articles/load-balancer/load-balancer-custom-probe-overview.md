---
title: Használja az Azure Load Balancer állapot-mintavételei méretezni, és adja meg a szolgáltatás magas rendelkezésre állás
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
ms.date: 12/14/2018
ms.author: kumud
ms.openlocfilehash: 51d781f331bcbc08642dc32c21baa150e9e5eee6
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53538685"
---
# <a name="load-balancer-health-probes"></a>Load Balancer állapot-mintavételei

Az Azure Load Balancer biztosít állapotadat-mintavételek terheléselosztási szabályokkal való használatra.  Egészségügyi mintavételi konfiguráció és a mintavételi válaszok határozza meg, mely háttérbeli címkészlet példányok új folyamatok fog kapni. Állapot-mintavételei használatával észleli a hibát egy alkalmazás egy háttér-példányon. Hozzon létre egy állapotmintát egyéni válasz is, és az állapotadat-mintavétel esetében forgalomszabályozás használata kezelheti a terhelés és a tervezett leállás. Ha az állapotfigyelő mintavételező nem sikerül, a terheléselosztó nem irányít, több új folyamatok a megfelelő nem megfelelő állapotú példányhoz.

Állapot-mintavételei több protokoll támogatja. Load Balancer Termékváltozat eltérő állapotadat-mintavétel egy adott protokoll támogatása egy adott típusú rendelkezésre állását.  Ezenkívül a szolgáltatás működését a Load Balancer Termékváltozat által változik.

| | Standard termékváltozat | Alapszintű termékváltozat |
| --- | --- | --- |
| [Mintavétel típusok](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Mintavételi viselkedésének lefelé](#probedown) | Az összes mintavételek le, az összes TCP-adatfolyamok továbbra is. | Minden mintavételek le, az összes TCP-adatfolyamok megszűnik. | 

> [!IMPORTANT]
> Load Balancer állapot-mintavételei származnak 168.63.129.16 IP-címről, és nem le kell tiltani a mintavételek a példány megjelöléséhez.  Felülvizsgálat [IP-forráscím mintavételi](#probesource) részleteiről.

## <a name="types"></a>Mintavétel típusok

Az állapotminta használatával a következő három protokoll figyelők konfigurálhatók:

- [TCP-figyelő](#tcpprobe)
- [HTTP-végpontokat](#httpprobe)
- [Koncové body protokolu HTTPS](#httpsprobe)

Az elérhető típusokat állapot-mintavételei eltérőek lehetnek attól függően, a Load Balancer Termékváltozat kiválasztott:

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Standard termékváltozat |    &#9989; |   &#9989; |   &#9989; |
| Alapszintű termékváltozat |   &#9989; |   &#9989; | &#10060; |

Attól függetlenül, úgy dönt, mintavételi típusának állapotadat-mintavételek figyelheti, bármely portra háttér-példány, beleértve a portot, amelyen elérhető a tényleges service.

### <a name="tcpprobe"></a> TCP-mintavétel

TCP-mintavétel kapcsolatot kezdeményezzen egy háromutas nyitott TCP kézfogás-a meghatározott portot elvégzésével.  TCP-mintavétel-kapcsolattal egy négy Bezárás TCP kézfogás megszűnik.

A minimális mintavételi időköz 5 másodperc, a nem megfelelő állapotú válaszok minimális számát pedig 2.  Minden időközök teljes időtartama nem haladhatja meg 120 másodperc.

A TCP-mintavétel meghiúsul, ha:
* A TCP-figyelőt a példányon egyáltalán nem válaszol az időkorláton során.  Mintavétel van megjelölve lettek konfigurálva a mintavétel le megjelölése előtti megválaszolatlan nyissa meg a meghiúsult mintavételi kérések száma alapján.
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

A HTTP és HTTPS-vizsgálatok, a TCP-mintavétel buildet, és a egy HTTP GET probléma a megadott elérési út. Relatív elérési utakat is az ilyen mintavételezők támogatja a HTTP GET. HTTPS-vizsgálatok ugyanazok, mint a HTTP-mintavételek a Transport Layer Security (TLS, SSL-ként ismert) igény szerinti hozzáadásával burkolót. Az állapotminta van megjelölve, ha a példány válaszol a HTTP-állapotkódot 200 az időkorláton belül.  Az állapotminta próbál meg alapértelmezés szerint 15 másodpercenként ellenőrizze a beállított állapot-mintavételi portot. A minimális mintavételi időköz 5 másodperc. Minden időközök teljes időtartama nem haladhatja meg 120 másodperc.

HTTP / HTTPS-vizsgálatok akkor is hasznos lehet, ha az állapotfigyelő mintavételező express.  a load balancer rotációból példányt eltávolítja, ha a mintavételi portot szintén a figyelő a szolgáltatás magát a saját logikát alkalmazzák. Például előfordulhat, hogy úgy dönt, eltávolít egy példányt, ha ez meghaladja a 90 %-ot, és nem 200-as HTTP - állapot adja vissza. 

Ha Felhőszolgáltatásokat használ, és webes szerepkörök, amelyek a w3wp.exe rendelkezik, akkor is megvalósítható automatikus figyeléssel a webhely. Hibák a webhely kódban nem 200 állapot térjen vissza a terheléselosztói mintavételezők.

Egy olyan HTTP / HTTPS-mintavétel meghiúsul, ha:
* Állapotminta-végpontot egy HTTP-válaszkód, 200-as (például a 403-as, 404-es vagy 500-as) eltérő adja vissza. Ezzel a művelettel kijelöli az állapotminta le azonnal. 
* Állapotminta-végpontot a 31 másodperces időkorlát időszakban egyáltalán nem válaszol. Több mintavételi kérések megválaszolatlan mehet, mielőtt a mintavétel nem fut, és amíg nem egyezik meg az összes időkorlátok elérte a megjelölve.
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

A felhőszolgáltatásokhoz tartozó szerepkörök (a feldolgozói szerepkörök és a webes szerepkörök) mintavételi figyelés alapértelmezés szerint a vendégügynököt használja.  Vendég ügynök mintavétel egy utolsó mentsvára konfigurációs.  Mindig használjon explicit módon egy TCP-állapotminta vagy HTTP-mintavétel. Vendég ügynök mintavétel nem hatásos, explicit módon megadott mintavételek a legtöbb alkalmazás forgatókönyvhöz.

Vendég ügynök mintavétel a vendégügynököt a virtuális gép ellenőrzése. Ezután figyeli, és válaszol egy HTTP 200 OK válasz csak akkor, ha a példány a kész állapotban van. (A többi állapotot foglalt, újrahasznosítás vagy leállítása is.)

További információkért lásd: [a szolgáltatásdefiníciós fájlt (csdef) konfigurálja az állapotmintákat](https://msdn.microsoft.com/library/azure/ee758710.aspx) vagy [első lépésként hozza létre a cloud services nyilvános load balancer](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Ha a vendégügynök nem válaszol a HTTP 200 OK, a terheléselosztó nem válaszol, a példány jelöli meg. Majd leállítja az küld a flow-példányhoz. A load balancer továbbra is fennáll, ellenőrizze a példány. 

Ha a vendégügynök válaszol egy HTTP 200-as, a terheléselosztó küld új folyamatok példányhoz újra.

Webes szerepkör használata esetén a webhely kód általában lefut az w3wp.exe, amely az Azure által nem felügyelt hálóhoz vagy Vendég ügynök. A vendégügynök nem jelentett hibák a w3wp.exe (például a HTTP 500-as választ). Ennek következtében a terheléselosztó nem használ annak a példánynak a rotációból.

<a name="health"></a>
## <a name="probehealth"></a>Állapot-mintavételi

TCP, HTTP és HTTPS állapot-mintavételei tekinti a kifogástalan állapotú, és jelölje meg a szerepkörpéldány kifogástalan amennyiben:

* Az állapotminta sikeres egyszer után a virtuális gép elindul.
* Elérte a megadott számú mintavételek a szerepkörpéldány kifogástalan állapotúként kell megjelölnie.

> [!NOTE]
> Ha az állapotminta ingadozik, a terheléselosztó vár már előtt a szerepkörpéldány helyezi el a kifogástalan állapotban. Az extra várakozási idő a felhasználó és az infrastruktúra védi, és olyan szándékos szabályzat.

## <a name="probe-count-and-timeout"></a>Mintavétel száma és időtúllépés

Állapotminta viselkedésére függ:

* Sikeres, amelyek lehetővé teszik egy példányt való megjelölésének mintavételek száma szerint, akár.
* Egy példány való megjelölésének kiváltó sikertelen mintavételek száma leállítottnak.

Az időkorlát és időköz megadott határozza meg, hogy e-példány van megjelölve, felfelé vagy lefelé.

## <a name="probedown"></a>Mintavételi viselkedésének lefelé

### <a name="tcp-connections"></a>TCP-kapcsolatok

Új TCP-kapcsolatok megfelelően működő háttér-példányok fennmaradó sikeres lesz.

Ha egy háttér-példány állapotadat-mintavétel meghiúsul, a háttér-példányhoz létrehozott TCP-kapcsolatok továbbra is.

Minden mintavételek háttérkészlet szereplő összes példány sikertelen lesz, ha nincsenek új folyamatok küld a háttérkészlethez. A standard Load Balancer lehetővé teszik a létrehozott TCP-adatfolyamok folytatásához.  Alapszintű Load Balancer le fog állni az összes meglévő TCP forgalom a háttérkészlethez.
 
Load Balancer egy közvetlenül csatlakoztatott szolgáltatás (állítsa le a TCP-kapcsolatok) és a flow mindig az ügyfél és a virtuális gép vendég operációs rendszer és alkalmazás között. A készlet összes mintákkal le egy előtérbeli ne válaszolna a TCP-kapcsolatot nyitott kísérletek (külön) nem megfelelően működő háttér-példány a folyamatot, és a egy szinkronizálás a mi nyugtázás válaszolhatnak nem okoz

### <a name="udp-datagrams"></a>UDP-datagramok

UDP-datagramok megfelelően működő háttér-példányok lesz elküldve.

UDP kapcsolat nélküli, és nincs teljesítményfolyamati állapot nyomon követett UDP-hez. Ha bármely háttérrendszer példányra állapotvizsgálat nem sikerül, előfordulhat, hogy meglévő UDP-forgalom helyezheti át egy másik kifogástalan példányra a háttérkészletben.

Minden mintavételek háttérkészlet szereplő összes példány sikertelen lesz, ha a meglévő UDP-folyamatok az alapszintű és standard szintű terheléselosztóhoz le fog állni.

<a name="source"></a>
## <a name="probesource"></a>Mintavételi forrás IP-címe

Terheléselosztó egy elosztott-ellenőrzési szolgáltatás a belső állapotmodell használja. Ellenőrzési található minden gazdagépen, virtuális gépek és programozott igény szerinti állapotadat-mintavételek száma az ügyfél-konfiguráció generálásához. A health mintavételi forgalom közvetlenül az ellenőrzési szolgáltatás, amely az állapotminta generál és az ügyfél virtuális gép között van. Az összes Load Balancer állapot-mintavételei származnak 168.63.129.16 IP-címről használja forrásként.  IP-címtér egy virtuális hálózathoz, amely nem az RFC1918 terület belül is használhatja.  Segítségével egy globálisan fenntartott, a Microsoft tulajdonában lévő, IP-cím csökkenti az esélyét, hogy az IP-címterület használata a virtuális hálózaton belül egy IP-címütközés.  Az IP-címet minden régióban azonos, és nem változik és nem biztonsági kockázatot mert csak a belső Azure-platform összetevő is forrás egy csomag az IP-címről. 

A AzureLoadBalancer szolgáltatáscímke azonosítja a forrás IP-címet a [hálózati biztonsági csoportok](../virtual-network/security-overview.md) , és lehetővé teszi az egészségügyi mintavételi forgalom alapértelmezés szerint.

Load Balancer állapot-mintavételei mellett a következő műveletek használata az IP-címet:

- Lehetővé teszi, hogy a Virtuálisgép-ügynök való kommunikációhoz. a platformot, hogy jelezze a "Kész" állapotban van
- Lehetővé teszi a kommunikációt a szűrt névfeloldást biztosítanak az ügyfelek számára az egyéni DNS-kiszolgálókat nem határoznak meg a DNS-kiszolgáló.  Ez a szűrés biztosítja, hogy ügyfeleink csak oldható meg a gazdagép az üzemelő példány neve.
- Lehetővé teszi a virtuális gép dinamikus IP-cím beszerzése a DHCP szolgáltatás az Azure-ban.

## <a name="design"></a> Tervezési útmutató

Győződjön meg arról, a szolgáltatás rugalmas, és lehetővé teszi, hogy a csoport állapot-mintavételei szolgálnak. A Virtual Network szolgáltatás hibás vagy hibás a kialakításban befolyásolhatja a rendelkezésre állás és méretezhetőség, a szolgáltatás. Tekintse át a teljes dokumentum, és fontolja meg, mi a forgatókönyv gyakorolt hatása akkor, ha ez a mintavételi válasz meg van jelölve,, vagy megjelölte, és milyen hatással van az alkalmazás forgatókönyv rendelkezésre állását.

Az alkalmazás kialakításakor az állapotközpontú modellről, kell mintavételi port egy háttér-példányon, amely annak a példánynak az állapotát tükrözi __és__ az alkalmazásszolgáltatást meg van adva.  Az alkalmazásport a mintavételi portot nem szükséges és azonosnak kell lennie.  Bizonyos esetekben kívánatos lehet a mintavételi portot kell lennie, mint a port, az alkalmazás szolgáltatást biztosít a számára.  

Egyes esetekben hasznos lehet, hogy az alkalmazás adott egészségügyi mintavételi válasz nem csupán észleli az alkalmazás állapotával, de is közvetlenül a Load Balancer jelezze, hogy a példány fogadni vagy nem kapják az új folyamatok létrehozása.  Ahhoz, hogy az alkalmazás, hozzon létre új folyamatokat példányhoz ellennyomás és szabályozási kézbesítését az állapotminta sikertelen vagy az alkalmazás karbantartási készítheti elő, és a forgatókönyv Kiürítés kezdeményezése a mintavétel választ is módosíthatja.  A Standard Load Balancer használatakor egy [le mintavételi](#probedown) jel mindig engedélyezi a hozzáférést, amíg üresjárati időtúllépés vagy csatlakozási megszüntetésre TCP-adatfolyamok. 

UDP terheléselosztásra, hozzon létre egy egyéni állapot-mintavételi jel a háttér-példány és a megfelelő figyelőre célzó állapotmintát TCP, HTTP vagy HTTPS használatával az UDP-alkalmazás állapotát tükrözi.

Használata esetén [magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabályok](load-balancer-ha-ports-overview.md) a [Standard Load Balancer](load-balancer-standard-overview.md), minden port elosztott terhelésű, és egyetlen egészségügyi mintavételi választ az egész példány állapotának szeretné bemutatni.

Fordítja le, vagy a proxy egy állapotminta keresztül a példányon, amely egy másik példányhoz az állapotminta kap a virtuális hálózatban található, ez a konfiguráció vezethet ebben az esetben a lépcsőzetesen terjedő hibáktól.  A következő esetet: külső berendezések készletét a háttérkészlet, a terheléselosztó erőforrás méretet és redundanciát biztosít a berendezések van üzembe helyezve, és az állapotadat-mintavétel mintavételi port van beállítva, hogy a külső készülék proxyk vagy a rendszer lefordítja arra a készülék mögötti virtuális gépeivel.  Ön mintavételi a lefordítandó használja ugyanazt a portot vagy a proxykiszolgáló kérelmeket a más virtuális gépek a készülék mögött, ha a készülék mögött egyetlen virtuális gép mintavételi válaszának lezárásával befejezettként jelöli meg magát a készülék kézbesíthetetlen. Ez a konfiguráció eredményeként egy egyetlen olyan háttérszolgáltatással példány mögött a készülék teljes forgatókönyv egymásra épülő hibához vezethet.  Az eseményindító időszakos mintavételi hibát okoz, hogy jelölje meg az eredeti cél (a készülék példány) le a terheléselosztó és pedig letilthatja az alkalmazás teljes forgatókönyv lehet. Ehelyett megvizsgálja a készülék magát állapotát. A mintavétel a állapotáról legutoljára jelzés meghatározni a kijelölt hálózati virtuális készülékek (NVA) forgatókönyvek esetén fontos szempont, és az alkalmazás szállítójához kérjen a megfelelő állapotáról legutoljára jelzés Mi az ilyen esetekben.

Ha nem engedélyezi a [forrás IP-cím](#probesource) a mintavétel a tűzfal-házirendek, az állapotminta sikertelen lesz, mivel nem tudja elérni a példány.  Load Balancer megjelöli, a példány a állapot-mintavételi hiba miatt le.  A Virtual Network szolgáltatás hibás okozhat a terhelés elosztott terhelésű alkalmazási forgatókönyv sikertelen lesz.

Load Balancer állapotmintához jelöljön ki a példány akkor **kell** bármely Azure-ban az IP-címének engedélyezéséhez [hálózati biztonsági csoportok](../virtual-network/security-overview.md) és a helyi tűzfal-házirendek.  Alapértelmezés szerint minden hálózati biztonsági csoport tartalmazza a [szolgáltatáscímke](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer állapot-mintavételi forgalom engedélyezéséhez.

Egészségügyi mintavételi hiba tesztelése, vagy jelölje meg egy egyéni példány le szeretné, ha egy [hálózati biztonsági csoportok](../virtual-network/security-overview.md) explicit módon letiltja az állapotminta (célport vagy [forrás IP-cím](#probesource)), és szimulálhatja a Hiba történt a mintavétel.

A Microsoft tulajdonában lévő, amely tartalmazza a 168.63.129.16 IP-címtartomány nem konfigurálja a virtuális hálózathoz.  Az ilyen konfigurációk fog ütköznek az állapotminta IP-címét, és okozhat a forgatókönyvben sikertelen lesz.

Ha a virtuális Gépen több adapterrel rendelkezik, annak érdekében, hogy a mintavétel a kapcsolaton, a kapott válaszol szüksége.  Forráshálózat előfordulhat, hogy kell fordítania ezt a címet a virtuális gép / felületen történik.

Ne engedélyezze a [TCP időbélyegeket](https://tools.ietf.org/html/rfc1323).  Engedélyezése TCP időbélyegek miatt állapotadat-mintavételek TCP-csomagokat a virtuális gép vendég operációs rendszer TCP protokollkészlet, ami jelölést le a megfelelő endpoint Load Balancer eredményez által eldobott miatt meghiúsul.  TCP időbélyegeket rendszeresen biztonsági alapértelmezés szerint engedélyezve van megerősített Virtuálisgép-rendszerképek, és le kell tiltani.

## <a name="monitoring"></a>Figyelés

A nyilvános és a belső [Standard Load Balancer](load-balancer-standard-overview.md) végpont és a háttérkiszolgáló példány állapota Hálózatfigyelő állapot szerint, többdimenziós metrikák az Azure monitoron keresztül teszi közzé. Ezek a metrikák más Azure-szolgáltatások vagy harmadik arty alkalmazások felhasználhassák. 

Alapszintű nyilvános Load Balancer összegzése a Log Analytics funkciójával háttérkészlet mintavételi állapotát mutatja.  A log Analytics alapvető belső terheléselosztók nem érhetők el.  Használhat [log analytics](load-balancer-monitor-log.md) a nyilvános load balancer mintavételi állapot ellenőrzéséhez és mintavételi száma. Adja meg a load balancer állapot statisztikája naplózás használható a Power bi-ban vagy az Azure Operational Insights.

## <a name="limitations"></a>Korlátozások

- HTTPS-vizsgálatok nem támogatják a kölcsönös hitelesítés ügyféltanúsítvánnyal.
- Állapot-mintavételei sikertelen lesz, ha engedélyezve vannak a TCP időbélyegzőnél.

## <a name="next-steps"></a>További lépések

- További tudnivalók a [Standard Load Balancerről](load-balancer-standard-overview.md)
- [Első lépések a Resource Managerben nyilvános load balancer létrehozása PowerShell használatával](load-balancer-get-started-internet-arm-ps.md)
- [Állapot-mintavételei REST API-val](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Az új állapotfigyelő mintavételi képességekhez kérelem [Load Balancer uservoice-on](https://aka.ms/lbuservoice)
