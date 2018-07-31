---
title: Load Balancer az egyéni minták használatával állapot figyelése |} A Microsoft Docs
description: Az egyéni minták az Azure Load Balancer használata a terheléselosztó mögött példányok figyelése céljából
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2018
ms.author: kumud
ms.openlocfilehash: afe46cf9fc710decba4524bd5a0fe1e73804f636
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39344164"
---
# <a name="load-balancer-health-probes"></a>Load Balancer állapot-mintavételei

Az Azure Load Balancer állapot-mintavételei alapján határozza meg, mely háttérbeli címkészlet példányok új folyamatok fog kapni. Állapot-mintavételei használatával észleli a hibát egy alkalmazás egy háttér-példányon. Is hozzon létre egy állapotmintát egyéni választ, és használja az állapotmintát esetében forgalomszabályozás, és jelezze a Load Balancer-e a folytatáshoz küldjön új folyamatokat, vagy új folyamatok küld egy háttér-példány leállítása. Ez a terhelés és a tervezett leállás kezelésére használható.

Ha az állapotfigyelő mintavételező nem sikerül, a terheléselosztó nem irányít, több új folyamatok a megfelelő nem megfelelő állapotú példányhoz. A meglévő és új folyamatok viselkedése attól függ, egy flow-e a TCP vagy UDP, valamint melyik Load Balancer Termékváltozatot használja.  Felülvizsgálat [mintavételi viselkedésének le részletes](#probedown).

## <a name="health-probe-types"></a>Állapot-mintavételi típusok

Állapot-mintavételei figyelheti, bármely portra háttér-példány, beleértve a portot, amelyen elérhető a tényleges service. Az állapotminta figyelői a TCP- vagy HTTP-végpontokat támogatja. 

UDP terheléselosztásra, hozza létre a háttér-példány HTTP vagy TCP-állapotminta használatával egyéni állapot mintavételi jel.

Használata esetén [magas rendelkezésre ÁLLÁSÚ portok terheléselosztási szabályok](load-balancer-ha-ports-overview.md) a [Standard Load Balancer](load-balancer-standard-overview.md), minden port elosztott terhelésű, és egyetlen egészségügyi mintavételi választ tükröznie kell azt, hogy az egész példány állapotának.  

Meg kell, nem a NAT- és egy állapotminta a példány, amely egy másik példányhoz az állapotminta kap a virtuális hálózatban található, mivel ez a hiba egész hibasorozatot indíthat ebben az esetben proxy.

### <a name="tcp-probe"></a>TCP-mintavétel

TCP-mintavétel kapcsolatot kezdeményezzen egy háromutas nyitott TCP kézfogás-a meghatározott portot elvégzésével.  Ezt követi a négy Bezárás TCP kézfogás.

A minimális mintavételi időköz 5 másodperc, a nem megfelelő állapotú válaszok minimális számát pedig 2.  A teljes időtartam legfeljebb 120 másodperc.

A TCP-mintavétel meghiúsul, ha:
* A TCP-figyelőt a példányon egyáltalán nem válaszol az időkorláton során.  Mintavétel van megjelölve lettek konfigurálva nyissa meg a megválaszolatlan a mintavétel megjelölése előtti meghiúsult mintavételi kérések száma alapján.
* A mintavétel egy TCP alaphelyzetbe állítani a példányt kap.

### <a name="http-probe"></a>HTTP-mintavétel

HTTP-mintavételek TCP-kapcsolatot létesítsen, és a egy HTTP GET probléma a megadott elérési út. HTTP-mintavételek a HTTP GET relatív útvonalakat támogatja. Az állapotminta van megjelölve, ha a példány válaszol a HTTP-állapotkódot 200 az időkorláton belül.  HTTP állapotának mintavételei kísérlet a alapértelmezés szerint 15 másodpercenként ellenőrizze a beállított állapot-mintavételi portot. A minimális mintavételi időköz 5 másodperc. A teljes időtartam legfeljebb 120 másodperc. 


HTTP-mintavételt is lehet hasznos, ha a saját logikai példányt eltávolítja a load balancer rotációból megvalósításához. Például előfordulhat, hogy úgy dönt, eltávolít egy példányt, ha ez meghaladja a 90 %-ot, és nem 200-as HTTP - állapot adja vissza. 

Ha Felhőszolgáltatásokat használ, és webes szerepkörök, amelyek a w3wp.exe rendelkezik, akkor is megvalósítható automatikus figyeléssel a webhely. Hibák a webhely kódban nem 200 állapot térjen vissza a terheléselosztói mintavételezők.  A HTTP-mintavétel felülbírálja az alapértelmezett Vendég ügynök mintavétel. 

A HTTP-mintavétel meghiúsul, ha:
* HTTP-állapotminta-végpontot egy HTTP-válaszkód, 200-as (például a 403-as, 404-es vagy 500-as) eltérő adja vissza. Ezzel a művelettel kijelöli az állapotminta azonnal. 
* HTTP-állapotminta-végpontot a során egyáltalán nem válaszol a 31-ig második időtúllépési időszakot. Az időtúllépési érték, amely be van állítva, attól függően több mintavételi kérések mehet megválaszolatlan előtt nem fut, a mintavétel megjelölve (azaz előtt SuccessFailCount mintavételek érkeznek).
* HTTP-mintavétel végpont lezárja a kapcsolatot a TCP alaphelyzetbe állítása keresztül.

### <a name="guest-agent-probe-classic-only"></a>Vendég ügynök szonda (csak Klasszikus modell)

A felhőszolgáltatásokhoz tartozó szerepkörök (a feldolgozói szerepkörök és a webes szerepkörök) mintavételi figyelés alapértelmezés szerint a vendégügynököt használja.   Vegye figyelembe a végső lehetőség.  Az explicit módon egy TCP-állapotminta vagy a HTTP-mintavétel mindig érdemes definiálni. Vendég ügynök mintavétel nem hatásos, explicit módon megadott mintavételek a legtöbb alkalmazás forgatókönyvhöz.  

Vendég ügynök mintavétel a vendégügynököt a virtuális gép ellenőrzése. Ezután figyeli, és válaszol egy HTTP 200 OK válasz csak akkor, ha a példány a kész állapotban van. (A többi állapotot foglalt, újrahasznosítás vagy leállítása is.)

További információkért lásd: [a szolgáltatásdefiníciós fájlt (csdef) konfigurálja az állapotmintákat](https://msdn.microsoft.com/library/azure/ee758710.aspx) vagy [első lépésként hozza létre a cloud services nyilvános load balancer](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Ha a vendégügynök nem válaszol a HTTP 200 OK, a terheléselosztó nem válaszol, a példány jelöli meg. Majd leállítja az küld a flow-példányhoz. A load balancer továbbra is fennáll, ellenőrizze a példány. 

Ha a vendégügynök válaszol egy HTTP 200-as, a terheléselosztó küld új folyamatok példányhoz újra.

Webes szerepkör használata esetén a webhely kód általában lefut az w3wp.exe, amely az Azure által nem felügyelt hálóhoz vagy Vendég ügynök. A vendégügynök nem jelentett hibák a w3wp.exe (például a HTTP 500-as választ). Ennek következtében a terheléselosztó nem használ annak a példánynak a rotációból.

## <a name="probe-health"></a>Állapot-mintavételi

TCP- és HTTP állapotadat-mintavételek tekinti a kifogástalan állapotú, és jelölje meg a szerepkörpéldány kifogástalan amennyiben:

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

> [!IMPORTANT]
> A terheléselosztó állapotmintája a 168.63.129.16 IP-címet használja. A nyilvános IP-cím belső platform erőforrások bring-your-saját-IP-címhez Azure-beli virtuális hálózat forgatókönyv kommunikációt létesít. A virtuális nyilvános IP-címet 168.63.129.16 használja minden olyan régióban, és nem módosul. Azt javasoljuk, hogy lehetővé tegye az IP-címet minden olyan Azure-ban [biztonsági csoportok](../virtual-network/security-overview.md) és helyi tűzfalházirendeket. Azt nem tekinthető biztonsági kockázatot jelent, mivel csak a belső Azure platform egy adott címről csomag is forrás. Ha nem engedélyezi az IP-címet a tűzfal-házirendjeinek a váratlan viselkedés akkor jelentkezik, számos forgatókönyv esetében, beleértve a sikertelen a betöltés elosztott terhelésű szolgáltatás. Is ne konfigurálja a virtuális hálózat és a egy IP-címtartományt, amely tartalmazza a 168.63.129.16.  Ha a virtuális Gépen több adapterrel rendelkezik, annak érdekében, hogy a mintavétel a kapcsolaton, a kapott válaszol szüksége.  Ez lehet szükség egyedi forrás NAT'ing ezt a címet a virtuális gép / felületen történik.

## <a name="probedown"></a>Mintavételi viselkedésének lefelé

### <a name="tcp-connections"></a>TCP-kapcsolatok

Új TCP-kapcsolatok a háttér-példányra, amely állapota kifogástalan, és a vendég operációs rendszer és az alkalmazás képes fogadni az új folyamat sikeres lesz.

Ha egy háttér-példány állapotadat-mintavétel meghiúsul, a háttér-példányhoz létrehozott TCP-kapcsolatok továbbra is.

Minden mintavételek háttérkészlet szereplő összes példány sikertelen lesz, ha nincsenek új folyamatok küld a háttérkészlethez. A standard Load Balancer lehetővé teszik a létrehozott TCP-adatfolyamok folytatásához.  Alapszintű Load Balancer le fog állni az összes meglévő TCP-adatfolyamok a háttérkészlethez.
 
Mivel a folyamat minden esetben az ügyfél és a virtuális gép vendég operációs rendszerek között, a készlet összes mintákkal le egy előtérbeli ne válaszolna a TCP-kapcsolódási nyílt kísérletek nem megfelelően működő háttér-példány megkapja a folyamat nem miatt.

### <a name="udp-datagrams"></a>UDP-datagramok

UDP-datagramok megfelelően működő háttér-példányok lesz elküldve.

UDP kapcsolat nélküli, és nincs teljesítményfolyamati állapot nyomon követett UDP-hez. Ha bármely háttérrendszer példányra állapotvizsgálat nem sikerül, előfordulhat, hogy meglévő UDP-forgalom helyezheti át egy másik kifogástalan példányra a háttérkészletben.

Minden mintavételek háttérkészlet szereplő összes példány sikertelen lesz, ha a meglévő UDP-folyamatok az alapszintű és standard szintű terheléselosztóhoz le fog állni.

## <a name="monitoring"></a>Figyelés

Az összes [Standard Load Balancer](load-balancer-standard-overview.md) állapot-mintavételi többdimenziós metrikák példányonként az Azure monitoron keresztül mutatja.

Alapszintű Load Balancer mintavételi állapot per Log Analytics funkciójával háttérkészlet tesz elérhetővé.  Ezt csak akkor érhető el nyilvános alapszintű Load Balancer Terheléselosztók és belső alapszintű terheléselosztó esetén nem érhető el.  Használhat [log analytics](load-balancer-monitor-log.md) a nyilvános load balancer mintavételi állapot ellenőrzéséhez és mintavételi száma. Adja meg a load balancer állapot statisztikája naplózás használható a Power bi-ban vagy az Azure Operational Insights.

## <a name="limitations"></a>Korlátozások

-  HTTP-állapotminta nem támogatja a TLS (HTTPS).  Inkább egy TCP-Hálózatfigyelővel a 443-as porton.

## <a name="next-steps"></a>További lépések

- [Első lépések a Resource Managerben nyilvános load balancer létrehozása PowerShell használatával](load-balancer-get-started-internet-arm-ps.md)
- [Állapot-mintavételei REST API-val](https://docs.microsoft.com/en-us/rest/api/load-balancer/loadbalancerprobes/get)

