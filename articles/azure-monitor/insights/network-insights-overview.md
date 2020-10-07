---
title: Azure Monitor hálózatok számára (előzetes verzió)
description: A hálózat Azure Monitor gyors áttekintése, amely az összes telepített hálózati erőforrás állapotának és metrikáinak átfogó áttekintését teszi lehetővé konfiguráció nélkül.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/24/2020
ms.openlocfilehash: 5f076f477c36f96d1807ce7071720225a6df8e03
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803806"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure Monitor hálózatok számára (előzetes verzió)
A hálózati Azure Monitor a konfiguráció nélkül átfogó képet nyújt az összes telepített hálózati erőforrás [állapotáról](https://docs.microsoft.com/azure/service-health/resource-health-checks-resource-types) és [mérőszámáról](../platform/metrics-supported.md) .  Emellett hozzáférést biztosít az összes olyan hálózati figyelési lehetőséghez, mint a [kapcsolat figyelője](../../network-watcher/connection-monitor-preview.md), [a hálózati biztonsági csoportok (NSG) adatforgalmának naplózása, a](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) [Traffic Analytics](../../network-watcher/traffic-analytics.md)és egyéb hálózati [diagnosztikai](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) funkciók.

A hálózatok Azure Monitor a figyelés következő fő összetevőinek köré szerveződik:
- [Hálózati állapot és mérőszámok](#networkhealth)
- [Kapcsolatok](#connectivity)
- [Adatforgalom](#traffic)
- [Diagnosztikai eszközkészlet](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>Hálózati állapot és mérőszámok

A hálózatok Azure Monitor **áttekintése** lap egy könnyed módszert biztosít a hálózati erőforrások leltárának megjelenítéséhez a Resource Health és a riasztások mellett. Négy kulcsfontosságú funkcionális területre oszlik: keresés és szűrés, Resource Health és mérőszámok, riasztások. és függőség nézet

![Áttekintő lap](media/network-insights-overview/overview.png)

### <a name="search-and-filtering"></a>Keresés és szűrés
Az erőforrás állapota és riasztások nézet a szűrők, például az **előfizetés**, az **erőforráscsoport** és az **erőforrástípus**alapján testreszabható. A keresőmező lehetővé teszi az erőforrás-tulajdonságok közötti keresés lehetőségét.

A keresőmező az erőforrások és a kapcsolódó erőforrások keresésére használható. Egy nyilvános IP-cím például egy Application Gatewayhoz van társítva. A nyilvános IP-címek DNS-nevének keresésekor a rendszer a nyilvános és a hozzá tartozó Application Gateway is azonosítja.

![Azure Monitor hálózatok számára – keresés](media/network-insights-overview/search.png)


### <a name="resource-health-and-metric"></a>Resource Health és metrika
Az egyes csempék egy erőforrástípust jelölnek, és az összes, a Resource Health állapottal együtt kiválasztott előfizetésben üzembe helyezett példányok számát adja meg. Az alábbi példában a 45 ER és a VPN-kapcsolatok üzembe helyezése, 44 kifogástalan, és 1 nem érhető el.

![Azure Monitor hálózatok számára – erőforrás állapota](media/network-insights-overview/resource-health.png)

A nem elérhető ER-és VPN-kapcsolatokra kattintva metrikai nézetet indít el. 

![Hálózatok Azure Monitor – metrikus nézet](media/network-insights-overview/metric-view.png)

A rács nézet minden elemére rákattinthat. Kattintson a Health (állapot) ikonra az adott kapcsolatok erőforrás-állapotának átirányításához. Kattintson a riasztások elemre, hogy átirányítsa a riasztások és metrikák lapra az adott kapcsolatban. 

### <a name="alerts"></a>Riasztások
A jobb oldali **riasztások** rács a kiválasztott erőforrásokhoz generált összes riasztást megjeleníti az összes előfizetésben. Kattintson a riasztások száma gombra a részletes riasztások oldal megtekintéséhez.

### <a name="dependency-view"></a>Függőség nézet
A **függőség** nézet segít megjeleníteni az erőforrás konfigurálásának módját. A függőségi nézet mostantól Application Gateway, a virtuális WAN és a Load Balancer esetében is támogatott. Application Gateway esetén például a függőség nézet a metrikák rács nézetében a Application Gateway erőforrás nevére kattintva érhető el. Ez a virtuális WAN-ra és a Load Balancer is vonatkozik.

![Azure Monitor hálózatok számára – Application Gateway nézet](media/network-insights-overview/application-gateway.png)

A Application Gateway **függőségi** nézete egyszerűsített képet nyújt arról, hogy az előtér-IP-címek hogyan kapcsolódnak a figyelőkhöz, a szabályokhoz és a backend-készlethez. A csatlakozó élek színkódoltak, és további részleteket biztosítanak a háttér-készlet állapota alapján. A nézet az összes kapcsolódó háttér-készlet, például a virtuálisgép-méretezési csoport és a VM-példányok Application Gateway metrikáinak és metrikáinak részletes áttekintését is tartalmazza.

![Azure Monitor hálózatok számára – függőség nézet](media/network-insights-overview/dependency-view.png)

A függőségi gráf lehetővé teszi az egyszerű navigációt a konfigurációs beállításokhoz. Kattintson a jobb gombbal a háttér-készletre más funkciók eléréséhez. Ha például a háttérrendszer-készlet egy virtuális gép, akkor a kapcsolódási problémák azonosításához közvetlenül hozzáférhet a virtuális gépekkel kapcsolatos megállapításokhoz és Network Watcher a kapcsolódási hibákhoz.

![Azure Monitor hálózatok számára – függőség Nézet menü](media/network-insights-overview/dependency-view-menu.png)

A függőség nézet keresési és szűrési sávján keresztül könnyedén kereshet a gráfon. Például a *AppGWTestRule* keresése az alábbi példában leszűkíti a grafikus nézetet a *AppGWTestRule*-n keresztül csatlakozó összes csomópontra.

![Hálózatok Azure Monitor – keresési példa](media/network-insights-overview/search-example.png)

A különböző szűrők segítségével leszűkítheti az adott elérési utat és állapotot. Válassza például *a csak a nem megfelelő állapotú* elemet **az állapot legördülő menüjéből** az összes olyan szegély megjelenítéséhez, ahol az állapot állapota *sérült*.

Kattintson a **részletes mérőszám nézetre** egy előre konfigurált munkafüzet elindításához, amely részletes mérőszámokkal rendelkezik a Application Gatewayhoz, az összes háttér-készlet erőforrásaihoz és az előtér-IP-címekhez. 

## <a name="connectivity"></a><a name="connectivity"></a>Kapcsolat

A **kapcsolat lap a** kiválasztott előfizetések esetében a kapcsolat figyelője és a [Csatlakozáskezelő (előzetes verzió)](../../network-watcher/connection-monitor-preview.md) használatával konfigurált összes tesztet könnyedén megjelenítheti.

![Hálózatok Azure Monitor kapcsolat lapja](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

A tesztek a források és a célhelyek csempéi szerint vannak csoportosítva, és megjelenítik az egyes tesztek elérhetőségi állapotát. A elérhető beállítások egyszerű hozzáférést biztosítanak a elérhetőségi feltételek az ellenőrzéseken alapuló konfigurálásához (%) és RTT (MS). Az értékek beállítása után az egyes tesztek állapota a kiválasztási feltételek alapján frissül.

![Azure Monitor hálózati kapcsolati tesztek](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)

Ha bármilyen forrásra vagy célhelyre kattint, a rendszer metrikai nézetet indít el.

![Hálózati Azure Monitor kapcsolati metrikái](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)


A rács nézet minden elemére rákattinthat. Kattintson a **elérhetőség** ikonra a **kapcsolat figyelője** portálra való átirányításhoz, ahol megtekintheti a hop by hop topológiát és a kapcsolat befolyásoló problémáit. Kattintson a **riasztások** elemre a riasztásokra való átirányításhoz, és ellenőrizze, hogy a kiválasztott figyelő a metrikák lapra való átirányításához nem sikerült a (z) **%/oda-vissza** .

A jobb oldali **riasztások**   rács az összes előfizetésben konfigurált kapcsolati tesztekhez generált riasztások nézetét jeleníti meg. Kattintson a riasztások száma gombra a részletes riasztások oldal megtekintéséhez.

## <a name="traffic"></a><a name="traffic"></a>Adatforgalom
A Traffic (forgalom) lapon elérhetők a [Traffic Analytics](../../network-watcher/traffic-analytics.md) [NSG](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) -NSG konfigurált összes, a kiválasztott előfizetések és a csoportok szerint csoportosított összes. Az ezen a lapon megadott keresési funkció lehetővé teszi a keresett IP-címhez konfigurált NSG azonosítását. Bármilyen IP-címet megkereshet a környezetében, és a csempézett regionális nézet megjeleníti az összes NSG, valamint a NSG flow-naplókat és a Traffic Analytics konfigurációs állapotát is.

![Hálózati Azure Monitor forgalmi nézete](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)

A bármely régió csempére kattintva olyan rácsos nézetet indít el, amely könnyen megtekinthető és konfigurálható NSG-naplókat és Traffic Analyticseket biztosít.  

![Forgalmi régió nézet a Azure Monitor hálózatok számára](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)

A rács nézet minden elemére rákattinthat. Kattintson a konfiguráció állapota elemre a NSG folyamat naplójának és a Traffic Analytics konfigurációjának szerkesztéséhez. Kattintson a riasztások elemre a kiválasztott NSG konfigurált forgalmi riasztások átirányításához. Ehhez hasonlóan a munkaterületre kattintva is megnyithatja a Traffic Analytics nézetet.  

A jobb oldali **riasztások**   rács a Traffic Analytics munkaterület-alapú riasztásokat jeleníti meg az összes előfizetésen belül. Kattintson a riasztások száma gombra a részletes riasztások oldal megtekintéséhez.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> Diagnosztikai eszközkészlet
A diagnosztikai eszközkészlet hozzáférést biztosít a hálózat hibaelhárításához elérhető összes diagnosztikai szolgáltatáshoz. Ebből a legördülő listából elérheti az olyan szolgáltatásokat, mint a [csomagok rögzítése](../../network-watcher/network-watcher-packet-capture-overview.md), a [VPN-hibakeresés](../../network-watcher/network-watcher-troubleshoot-overview.md), a [kapcsolódási hibák](../../network-watcher/network-watcher-connectivity-overview.md), a [következő ugrás](../../network-watcher/network-watcher-next-hop-overview.md) és az [IP-folyamat ellenőrzése](../../network-watcher/network-watcher-ip-flow-verify-overview.md).

![Diagnosztikai eszközkészlet lap](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="troubleshooting"></a>Hibaelhárítás 

Az általános hibaelhárítási útmutatóért tekintse meg a dedikált munkafüzet-alapú információkkal [kapcsolatos hibaelhárítási cikket](troubleshoot-workbooks.md).

Ez a szakasz a Azure Monitor hálózatokhoz való használata során felmerülő gyakori problémák diagnosztizálását és hibaelhárítását ismerteti. Az alábbi lista segítségével megkeresheti az adott hibához kapcsolódó információkat.

### <a name="resolving-performance-issues-or-failures"></a>Teljesítménnyel kapcsolatos problémák és hibák elhárítása

A hálózatokkal Azure Monitor azonosított hálózatkezeléssel kapcsolatos problémák elhárításához tekintse meg a hibás erőforrás hibaelhárítási dokumentációját. A magas használatú szolgáltatások hibaelhárítási hivatkozásai alább láthatók.
* Virtual Network (VNET)
* Application Gateway
* VPN Gateway
* ExpressRoute 
* Load Balancer 

### <a name="why-dont-i-see-the-resources-from-all-the-subscriptions-i-have-selected"></a>Miért nem látom az összes kiválasztott előfizetés erőforrásait

A hálózati információk csak 5 előfizetésből származó erőforrásokat jelenítenek meg egyszerre. 

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-network-insights-how-do-i-do-so"></a>Szeretném módosítani vagy további vizualizációkat hozzáadni a hálózati információkhoz

Ha módosítani szeretné a módosításokat, válassza a "szerkesztési mód" lehetőséget a munkafüzet módosításához, majd mentse a munkáját új munkafüzetként, amely egy kijelölt előfizetéshez és erőforráscsoporthoz van kötve.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Mi az az idő, amikor a munkafüzetek bármely részét rögzítjük

Kihasználjuk az "automatikus" időkeretet, ezért attól függ, hogy milyen időtartomány van kiválasztva.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Mi az az időtartomány, amikor a munkafüzet bármely része rögzítve van

Az időtartomány az irányítópult beállításaitól függ.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-network-insights"></a>Mi a teendő, ha más típusú vagy saját vizualizációkat szeretnék látni? Hogyan módosíthatom a hálózati adatfelismeréseket

Szerkesztheti a megjelenő munkafüzetet bármelyik oldalsó panelen és részletes mérőszám nézetben, a szerkesztési mód használatával, majd mentheti a munkáját új munkafüzetként, amely az összes új módosítást tartalmazni fogja.


## <a name="next-steps"></a>További lépések

- További információ a hálózati monitorozásról: [Mi az az Azure Network Watcher?](../../network-watcher/network-watcher-monitoring-overview.md).
- Ismerkedjen meg a forgatókönyvekkel, amelyek támogatják az új és a meglévő jelentések testreszabását, valamint az [interaktív jelentések Azure monitor-munkafüzetekkel való létrehozását](../platform/workbooks-overview.md)ismertető áttekintést.
