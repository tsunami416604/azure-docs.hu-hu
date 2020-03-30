---
title: Hálózati teljesítményfigyelő megoldás az Azure Log Analytics szolgáltatásban | Microsoft dokumentumok
description: A Hálózati teljesítményfigyelő ExpressRoute-figyelő funkciójával az Azure ExpressRoute-on keresztül figyelheti a fiókirodák és az Azure közötti végpontok közötti kapcsolatot és teljesítményt.
ms.subservice: logs
ms.topic: conceptual
author: abshamsft
ms.author: absha
ms.date: 11/27/2018
ms.openlocfilehash: 6ac610d7dcf9849b6b439741957684867b9d01aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660750"
---
# <a name="expressroute-monitor"></a>ExpressRoute-figyelő

Az Azure ExpressRoute-figyelő funkcióval a [Network Performance Monitor](network-performance-monitor.md) segítségével figyelheti a fiókirodák és az Azure közötti végpontok közötti kapcsolatot és teljesítményt az Azure ExpressRoute-on keresztül. A legfontosabb előnyök a következők: 

- Az előfizetéshez társított ExpressRoute-áramkörök automatikus észlelése.
- A sávszélesség-kihasználtság, a veszteség és a késés nyomon követése a kapcsolatcsoporton, a társviszony-létesítésen és az Azure virtuális hálózat szintjén az ExpressRoute-hoz.
- Az ExpressRoute-áramkörök hálózati topológiájának felderítése.

![ExpressRoute-figyelő](media/network-performance-monitor-expressroute/expressroute-intro.png)

## <a name="configuration"></a>Konfiguráció 
A Hálózati teljesítményfigyelő konfigurációjának megnyitásához nyissa meg a [Hálózati teljesítményfigyelő megoldást,](network-performance-monitor.md) és válassza a **Konfigurálás**lehetőséget.

### <a name="configure-network-security-group-rules"></a>Hálózati biztonsági csoport szabályainak konfigurálása 
Az Azure-ban a Hálózati teljesítményfigyelőn keresztül figyelésre használt kiszolgálók esetében konfigurálja a hálózati biztonsági csoport (NSG) szabályait úgy, hogy a TCP-forgalmat engedélyezze a Hálózati teljesítményfigyelő által használt porton a szintetikus tranzakciókhoz. Az alapértelmezett port a 8084. Ez a konfiguráció lehetővé teszi, hogy az Azure virtuális gépeken telepített Log Analytics-ügynök kommunikáljon egy helyszíni figyelőügynökkel. 

Az NSG-kről további információt a [Hálózati biztonsági csoportok című témakörben talál.](../../virtual-network/manage-network-security-group.md) 

>[!NOTE]
> Mielőtt folytatná ezt a lépést, telepítse a helyszíni kiszolgálóügynököt és az Azure-kiszolgálóügynököt, és futtassa az EnableRules.ps1 PowerShell-parancsfájlt. 

 
### <a name="discover-expressroute-peering-connections"></a>ExpressRoute-társviszony-létesítési kapcsolatok felderítése 
 
1. Válassza az **ExpressRoute társviszony-létesítési nézetet.**
2. Válassza **a Discover Now** lehetőséget az összes ExpressRoute-alapú privát társviszony-létesítés, amely az Azure Log Analytics-munkaterülethez kapcsolódó Azure-előfizetés virtuális hálózataihoz kapcsolódik.

    >[!NOTE]
    > A megoldás jelenleg csak expressroute-i privát társtársi kapcsolatokat fedez fel. 

    >[!NOTE]
    > Csak a log analytics-munkaterülethez kapcsolódó előfizetéshez társított virtuális hálózatokhoz kapcsolódó privát társviszony-létesítéseket fedezik fel. Ha az ExpressRoute a munkaterülethez kapcsolódó előfizetésen kívüli virtuális hálózatokhoz csatlakozik, hozzon létre egy Log Analytics-munkaterületet ezekben az előfizetésekben. Ezután a Hálózati teljesítményfigyelő segítségével figyelheti ezeket a társviszony-létesítéseket. 

    ![ExpressRoute-figyelő konfigurációja](media/network-performance-monitor-expressroute/expressroute-configure.png)
 
   A felderítés befejezése után a felderített privát társviszony-létesítési kapcsolatok egy táblázatban jelennek meg. Ezek a társviszony-létesítések figyelése kezdetben letiltott állapotban van. 

### <a name="enable-monitoring-of-the-expressroute-peering-connections"></a>Az ExpressRoute-társviszony-létesítési kapcsolatok figyelésének engedélyezése 

1. Jelölje ki a figyelni kívánt privát társviszony-létesítést.
2. A jobb oldali ablaktáblában jelölje be a **Társviszony-létesítés figyelése jelölőnégyzetet.** 
3. Ha állapoteseményeket kíván létrehozni ehhez a kapcsolathoz, válassza **az Állapotfigyelés engedélyezése lehetőséget ehhez a társviszony-létesítéshez.** 
4. Válassza ki a figyelési feltételeket. Egyéni küszöbértékeket állíthat be az állapotesemény létrehozásához a küszöbértékek megadásával. Amikor a feltétel értéke meghaladja a társviszony-létesítési kapcsolat kijelölt küszöbértékét, egy állapotesemény jön létre. 
5. Válassza **az Ügynökök hozzáadása lehetőséget** a társviszony-létesítésfigyelési kapcsolat figyeléséhez használni kívánt figyelési ügynökök kiválasztásához. Győződjön meg arról, hogy ügynököket ad hozzá a kapcsolat mindkét végén. A társviszony-létesítéshez csatlakoztatott virtuális hálózatban legalább egy ügynökre van szükség. Emellett legalább egy helyszíni ügynökcsatlakozik ehhez a társviszony-létesítéshez. 
6. A konfiguráció mentéséhez válassza a **Mentés** gombot. 

   ![ExpressRoute figyelési konfigurációja](media/network-performance-monitor-expressroute/expressroute-configure-discovery.png)


Miután engedélyezte a szabályokat, és kiválaszthatértékeket és ügynököket, várjon 30–60 percet, amíg az értékek feltöltése és az **ExpressRoute figyelési** csempék megjelennek. Amikor megjelenik a figyelési csempék, az ExpressRoute-áramkörök és a kapcsolati erőforrások most már figyeli a Hálózati teljesítmény figyelő. 

>[!NOTE]
> Ez a funkció megbízhatóan működik az új lekérdezési nyelvre frissített munkaterületeken.

## <a name="walkthrough"></a>Útmutatás 

A Hálózati teljesítményfigyelő irányítópultja áttekintést nyújt az ExpressRoute-áramkörök és a társviszony-létesítési kapcsolatok állapotáról. 

![A Hálózati teljesítményfigyelő irányítópultja](media/network-performance-monitor-expressroute/npm-dashboard-expressroute.png) 

### <a name="circuits-list"></a>Áramkörök listája 

Az összes figyelt ExpressRoute-kapcsolat listájának megtekintéséhez válassza az ExpressRoute-áramkörök csempét. Kiválaszthatja az áramkört, és megtekintheti annak állapotát, a csomagvesztés trenddiagramjait, a sávszélesség-kihasználtságot és a késést. A diagramok interaktívak. A diagramok ábrázolásához egyéni időablakot is kiválaszthat. Az egérmutatót a diagram egy területe fölé húzva nagyíthatja és láthatja a részletes adatpontokat. 

![ExpressRoute-áramkörök listája](media/network-performance-monitor-expressroute/expressroute-circuits.png) 

### <a name="trends-of-loss-latency-and-throughput"></a>A veszteség, a késés és az átviteli képesség tendenciái 

A sávszélesség-kihasználtság, a késés és a veszteségdiagramok interaktívak. A diagramok bármelyik szakaszát az egér vezérlőivel nagyíthatja. Megtekintheti a sávszélesség, a késés és a veszteség adatait más időközönként is. A **Műveletek** gomb bal felső részén válassza a **Dátum/idő**lehetőséget. 

![ExpressRoute-késés](media/network-performance-monitor-expressroute/expressroute-latency.png) 

### <a name="peerings-list"></a>Társviszony-létesítések listája 

Ha privát társviszony-létesítésen keresztül szeretné a virtuális hálózatokhoz való összes kapcsolatot, válassza a **Privát társviszony-létesítés csempét** az irányítópulton. Itt kiválaszthatja a virtuális hálózati kapcsolatot, és megtekintheti annak állapotát, a csomagvesztés trenddiagramjait, a sávszélesség-kihasználtságot és a késést. 

![ExpressRoute-társviszony-létesítések](media/network-performance-monitor-expressroute/expressroute-peerings.png) 

### <a name="circuit-topology"></a>Áramköri topológia 

Az áramköri topológia megtekintéséhez válassza a **Topológia csempét.** Ez a művelet a kijelölt kapcsolatcsoport vagy társviszony-létesítés topológia nézetére viszi. A topológiadiagram a hálózat minden szegmensének késését biztosítja, és minden 3. Egy ugrás kiválasztása további részleteket tár fel a hopról. Ha növelni szeretné a láthatóság szintjét a helyszíni ugrások felvételéhez, mozgassa a csúszkát a **SZŰRŐK csoportban.** Ha a csúszkát balra vagy jobbra mozgatja, az növeli vagy csökkenti a topológiadiagramon lévő ugrások számát. Az egyes szegmensek késése látható, ami lehetővé teszi a nagy késleltetésű szegmensek gyorsabb elkülönítését a hálózaton.

![ExpressRoute-topológia](media/network-performance-monitor-expressroute/expressroute-topology.png)

### <a name="detailed-topology-view-of-a-circuit"></a>Áramkör részletes topológiája 

Ez a nézet a virtuális hálózati kapcsolatokat mutatja. 

![ExpressRoute virtuális hálózati kapcsolatok](media/network-performance-monitor-expressroute/expressroute-vnet.png)
 
## <a name="diagnostics"></a>Diagnosztika 

A Hálózati teljesítményfigyelő segítségével számos áramköri kapcsolódási problémát diagnosztizál. A megjelenő problémák közül néhány az alábbiakban található.

Az értesítési kódokat a LogAnalytics segítségével **láthatja,** és riasztásokat állíthat be rájuk. Az **NPM diagnosztika** lapon láthatja az összes aktivált diagnosztikai üzenet leírását.

| Értesítési kód (naplók) | Leírás |
| --- | --- |
| 5501 | Nem lehet áthaladni az ExpressRoute-kapcsolaton keresztül |
| 5502 | Nem lehet áthaladni az ExpressRoute-kapcsolatelsődleges kapcsolatán keresztül |
| 5503 | Nem található a munkaterülethez kapcsolódó előfizetéshez kapcsolódó kapcsolat. | 
| 5508 | Nem határozható meg, hogy a forgalom áthalad-e bármely kör(ek)en a menetvonalhoz |
| 5510 | A forgalom nem halad át a tervezett áramkörön | 
| 5511 | A forgalom nem halad át a tervezett virtuális hálózaton | 

**Az áramkör leállt.** A Hálózati teljesítményfigyelő értesíti Önt, amint a helyszíni erőforrások és az Azure virtuális hálózatok közötti kapcsolat megszakad. Ez az értesítés segít proaktív lépéseket tenni, mielőtt megkapja a felhasználói eszkalációkat, és csökkenti az állásidőt.

![Az ExpressRoute-kapcsolat nem érhető el](media/network-performance-monitor-expressroute/expressroute-circuit-down.png)
 

**A forgalom nem a tervezett áramkörön keresztül áramlik.** A Hálózati teljesítményfigyelő értesíti, ha a forgalom nem a kívánt ExpressRoute-áramkörön keresztül áramlik. Ez a probléma akkor fordulhat elő, ha az áramkör nem érhető el, és a forgalom a biztonsági mentési útvonalon halad át. Ez akkor is előfordulhat, ha útválasztási probléma merül fel. Ez az információ segít proaktív módon kezelni az útválasztási szabályzatokban szereplő konfigurációs problémákat, és biztosítani a legoptimálisabb és legbiztonságosabb útvonalat. 

 

**A forgalom nem az elsődleges áramkörön keresztül áramlik.** A Hálózati teljesítményfigyelő értesíti, ha a másodlagos ExpressRoute-kapcsolaton keresztül forgalom halad. Annak ellenére, hogy ebben az esetben nem tapasztal semmilyen kapcsolódási problémát, az elsődleges áramkörrel kapcsolatos problémák proaktív elhárítása jobban felkészíti. 

 
![ExpressRoute-forgalom](media/network-performance-monitor-expressroute/expressroute-traffic-flow.png)


**Lebomlás a csúcskihasználtság miatt.** A sávszélesség-kihasználtsági trend et a késési trenddel összefüggésben meghatározhatja, hogy az Azure-beli számítási feladatok lebontása a sávszélesség-kihasználtság csúcsértéke miatt következik-e be. Ezután ennek megfelelően cselekedhet.

![ExpressRoute sávszélesség-kihasználtsága](media/network-performance-monitor-expressroute/expressroute-peak-utilization.png)

 

## <a name="next-steps"></a>További lépések
A részletes hálózati teljesítményadatrekordok megtekintéséhez [keressen naplókat.](../../azure-monitor/log-query/log-query-overview.md)
