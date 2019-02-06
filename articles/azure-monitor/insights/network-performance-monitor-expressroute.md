---
title: Network Performance Monitor megoldás az Azure Log Analyticsben |} A Microsoft Docs
description: A Network Performance Monitor az ExpressRoute-figyelő képesség segítségével figyelheti a végpontok közötti kapcsolatait és teljesítményét a fiókirodák és az Azure között az Azure expressroute-on keresztül.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: abshamsft
ms.openlocfilehash: 2dcbe170a69c0c285cb6425427f94b5efced8712
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747458"
---
# <a name="expressroute-monitor"></a>ExpressRoute-figyelő

Az Azure ExpressRoute-figyelő a funkcióval a [Network Performance Monitor](network-performance-monitor.md) végpontok közötti kapcsolatait és teljesítményét a fiókirodák és az Azure közötti figyelése az Azure expressroute-on keresztül. Fő előnyei a következők: 

- Autodetection az ExpressRoute-kapcsolatcsoportokat az Ön előfizetéséhez rendelve.
- Nyomon követését, a sávszélesség-használat, a veszteség és a késleltetés a kapcsolatcsoportot, a társviszony-létesítés és a Azure Virtual Network szintjén az expressroute-hoz.
- Az ExpressRoute-Kapcsolatcsoportok hálózati topológiájának felderítése.

![ExpressRoute-figyelő](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Konfiguráció 
Nyissa meg a Network Performance Monitor konfigurációját, nyissa meg a [Network Performance Monitor megoldás](network-performance-monitor.md) válassza **konfigurálása**.

### <a name="configure-network-security-group-rules"></a>Hálózatbiztonságicsoport-szabályok konfigurálása 
Az Azure-ban használt kiszolgálók figyeléshez a Network Performance Monitor használatával konfigurálja a hálózati biztonsági csoport (NSG) szabályai a Network Performance Monitor használata szintetikus tranzakciókhoz használt porton a TCP-forgalom engedélyezéséhez. Az alapértelmezett port: 8084. Ez a konfiguráció lehetővé teszi, hogy a Log Analytics-ügynök kommunikáljon a helyszíni Azure virtuális gépeken telepített ügynök figyelése. 

NSG-kkel kapcsolatos további információkért lásd: [hálózati biztonsági csoportok](../../virtual-network/manage-network-security-group.md). 

>[!NOTE]
> Mielőtt folytatja ezt a lépést, a helyszíni kiszolgáló az ügynök és az Azure-kiszolgáló ügynök telepítése, és futtassa az EnableRules.ps1 PowerShell-szkriptet. 

 
### <a name="discover-expressroute-peering-connections"></a>Fedezze fel a társviszony-létesítési ExpressRoute-kapcsolatok 
 
1. Válassza ki a **ExpressRoute-Társviszonyok** megtekintése.
2. Válassza ki **felderítése most** felderítésére minden az ExpressRoute privát társviszony-létesítéseket, amely csatlakozik a virtuális hálózatok az Azure-előfizetésében csatolva az Azure Log Analytics-munkaterületet.

    >[!NOTE]
    > A megoldás jelenleg csak az ExpressRoute privát társviszony-létesítéseket deríti fel. 

    >[!NOTE]
    > Csak privát társviszony-létesítéseket a Log Analytics-munkaterülettel társított az előfizetéshez tartozó virtuális hálózatokhoz felderítése történik meg. Ha ExpressRoute kívül az előfizetés ehhez a munkaterülethez társított virtuális hálózatok csatlakoztatva van, hozzon létre egy Log Analytics-munkaterület ezen előfizetések. A Network Performance Monitor segítségével ezeket társviszony figyelése. 

    ![ExpressRoute-figyelő konfigurálása](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
 A felderítés befejeződése után egy táblázatban láthatók a felderített privát társviszony-kapcsolatokat. A társviszony-létesítéshez figyelési kezdetben letiltott állapotban van egy. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>A társviszony-létesítési ExpressRoute-kapcsolatok a figyelés engedélyezése 

1. Válassza ki a figyelni kívánt privát társviszony-létesítési kapcsolat.
2. A jobb oldali panelén válassza a **figyelheti a társviszony-létesítés** jelölőnégyzetet. 
3. Ha azt tervezi, ehhez a kapcsolathoz hálózatállapot-események létrehozása, kiválasztása **egészségügyi figyelés engedélyezése a társviszony**. 
4. Válassza ki a feltételek figyelése. Beállíthat egyedi küszöbértékeket egészségügyi eseménygenerálás küszöbértékek megadásával. Minden alkalommal, amikor a feltétel értéke a megadott küszöbértéknél, a társviszony-létesítési kapcsolat túllépik, állapottal kapcsolatos esemény jön létre. 
5. Válassza ki **ügynökök hozzáadása** kiválasztása a monitorozási ügynökök, a társviszony-létesítési kapcsolat használni kíván. Győződjön meg arról, hogy a kapcsolat mindkét végén adjon hozzá ügynökök. Szüksége lesz legalább egy ügynök kapcsolódik a társviszony a virtuális hálózatban. Legalább egy helyi ügynök kapcsolódik a társviszony is szükséges. 
6. Válassza ki **mentése** a konfiguráció mentéséhez. 

   ![Az ExpressRoute-figyelési konfiguráció](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


Miután engedélyezte a szabályok és a select értékek és az ügynökök, várjon 30 – 60 percet feltölti az értékek és a **az ExpressRoute monitorozása** csempék jelennek meg. Amikor megjelenik a figyelési csempék, az ExpressRoute-Kapcsolatcsoportok és kapcsolati erőforrás most által figyelt Network Performance Monitor. 

>[!NOTE]
> Ez a funkció megbízhatóan működik, munkaterületek, akik frissítettek az új lekérdezési nyelvre.

## <a name="walkthrough"></a>Útmutatás 

A Network Performance Monitor-irányítópult az ExpressRoute-Kapcsolatcsoportok és társviszony-kapcsolatok áttekintése látható. 

![Network Performance Monitor-irányítópult](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Kapcsolatcsoportok listája 

Az összes figyelt ExpressRoute-Kapcsolatcsoportok listájának megtekintéséhez válassza ki az ExpressRoute-Kapcsolatcsoportok csempe. Válassza ki egy kapcsolatcsoportot, és megtekintheti annak állapotát, trenddiagramok csomagvesztés, a sávszélesség kihasználtságát és a késés. A diagramok használata interaktív. Kiválaszthat egy küldik az ábrázolást a diagramok egyéni időtartományából. Húzza az egeret keresztül egy adott területre a diagram a nagyításra, és tekintse meg a részletes adatokat. 

![Az ExpressRoute-Kapcsolatcsoportok listája](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>Az adatveszteség, késés és átviteli sebesség trendek 

A sávszélesség-kihasználtságáról, a késés és a veszteség diagramok használata interaktív. Szabadon nagyíthatja bármely szakaszt az ezekbe a diagramokba egér-vezérlők használatával. Is láthatja a sávszélesség, a késés és az adatveszteség-adatok a más időközönként. A bal felső sarokban a **műveletek** gombra, válassza **dátum/idő**. 

![Az ExpressRoute késés](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Társviszony-Létesítéseket listája 

Magánhálózati társviszony-létesítésen keresztül csatlakozva a virtuális hálózatok felé irányuló összes kapcsolatot listáját, válassza ki a **privát társviszony létesítése** csempét az irányítópulton. Itt kiválaszthatja a virtuális hálózati kapcsolat, és megtekintheti annak állapotát, trenddiagramok csomagvesztés, a sávszélesség kihasználtságát és a késés. 

![ExpressRoute-társviszony](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Kapcsolatcsoport topológia 

Kapcsolatcsoport topológiájának megtekintéséhez jelölje ki a **topológia** csempére. Ezzel továbblép a topológia e nézetében a kiválasztott kapcsolatcsoporthoz vagy a társviszony-létesítés. A topológiadiagramot a minden egyes szegmens a késést biztosít a hálózaton található, és a egy csomópont a diagram egyes 3. rétegbeli ugrások képviseli. További információt a Ugrás hop kiválasztásával tárja fel. Látható-e a helyszíni útválasztók ugrásainak felvenni szintjének növeléséhez a csúszkát a **szűrők**. A csúszka sávjának áthelyezése a bal vagy jobb növekedése esetén, vagy a topológia Graph ugrások száma csökken. Minden egyes szegmens a késés akkor látható, amely lehetővé teszi a hálózat nagy késésű szegmensek gyorsabban elkülönítését.

![ExpressRoute-topológia](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>A kapcsolatcsoport részletes topológia megtekintése 

Ebben a nézetben látható a virtuális hálózati kapcsolatok. 

![Az ExpressRoute virtuális hálózati kapcsolatok](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnosztika 

Több kapcsolatcsoportot kapcsolódási problémák diagnosztizálása a Network Performance Monitor segítségével. A problémák is látható, alább láthatók.

Tekintse meg az értesítés kódokat, és riasztásokat állíthat be a rajtuk keresztül **LogAnalytics**. Az a **NPM diagnosztikai** lapon láthatja minden aktivált diagnosztikai üzenet leírása.

| Értesítés kódot (naplók) | Leírás |
| --- | --- |
| 5501 | Nem sikerült bejárni az ExpressRoute-kapcsolatcsoport másodlagos kapcsolatát |
| 5502 | Nem sikerült bejárni az ExpressRoute-kapcsolatcsoport elsődleges kapcsolatát |
| 5503 | Nincs kapcsolat a munkaterülethez társított előfizetés nem található | 
| 5508 | Nem sikerült meghatározni e forgalmat a kapcsolatcsoport sem továbbítja az elérési út |
| 5510 | A megfelelő kapcsolatcsoport nem továbbítja a forgalmat | 
| 5511 | A megfelelő virtuális hálózat nem továbbítja a forgalmat | 

**Kapcsolatcsoport nem működik.** A Network Performance Monitor értesíti, amint az a helyszíni erőforrások és az Azure virtuális hálózatok közötti kapcsolat megszakad. Ez az értesítés segít proaktív művelet végrehajtása előtt a felhasználó eszkalálást kap, és csökkentheti az állásidőt.

![ExpressRoute-kapcsolatcsoport nem működik](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**Nem a megfelelő kapcsolatcsoport áthaladó forgalmat.** A Network Performance Monitor értesíti, amikor adatforgalommal nem a megfelelő ExpressRoute-kapcsolatcsoport keresztül. A probléma akkor fordulhat elő, ha a kapcsolatcsoport nem működik, és a forgalom áthaladnak a biztonsági mentési útvonal. Azt is történhet, ha egy útválasztási probléma. Ez az információ segít proaktív módon kezelheti az útválasztási házirend található a konfigurációs problémákat, és győződjön meg arról, hogy a legtöbb optimális és biztonságos útvonalat használja. 

 

**Nem az elsődleges kapcsolatcsoportot áthaladó forgalmat.** A Network Performance Monitor értesíti, ha adatforgalommal másodlagos ExpressRoute-kapcsolatcsoporton keresztül. Annak ellenére, hogy nem minden csatlakozási problémákba ebben az esetben, amelyek segítségével proaktív módon a hibák elhárításának az elsődleges kapcsolatcsoportot teszi azt jobban előkészített. 

 
![Az ExpressRoute-adatforgalmat](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Kiugró mértékű kihasználtsága miatt teljesítménycsökkenést.** A sávszélesség kihasználtsági trendek azonosításához, hogy az Azure-beli számítási teljesítménycsökkenés miatt a sávszélesség-használatot egy csúcs-e a késés trendje a kapcsolhatja össze. Majd ennek megfelelően műveleteket végezheti el.

![Az ExpressRoute sávszélesség-használat](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>További lépések
[Naplók keresése](../../azure-monitor/log-query/log-query-overview.md) részletes hálózati teljesítmény adatfelderítési rekordok megtekintéséhez.
