---
title: Megismerheti, hogyan riasztást küld a metrika a munkát az Azure monitorban.
description: Mire képes a metrikákhoz kapcsolódó riasztások, és hogyan működnek az Azure monitorban áttekintést kaphat.
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: alerts
ms.openlocfilehash: 59973d9530bf1c3ab3e77290b25e50860f9de0ca
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342983"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Megismerheti, hogyan riasztást küld a metrika a munkát az Azure monitorban

Az Azure monitorban metrikákhoz kapcsolódó riasztások felett többdimenziós metrikák működik. Ezek a metrikák lehet [platform metrikák](alerts-metric-near-real-time.md#metrics-and-dimensions-supported), [egyéni metrikákat](../../azure-monitor/platform/metrics-custom-overview.md), [népszerű naplók az Azure Monitor alakítani a metrikák](../../azure-monitor/platform/alerts-metric-logs.md) és Application Insights-metrikák. Metrikákhoz kapcsolódó riasztások kiértékelése annak ellenőrzéséhez, hogy rendszeres időközönként egy vagy több metrikát idősorozat-igaz, és értesíti, amint az értékelések teljesülnek a feltételek. Metrikákhoz kapcsolódó riasztások állapotalapúak, azaz csak küldenek értesítéseket állapot.

## <a name="how-do-metric-alerts-work"></a>Hogyan működik a metrikákhoz kapcsolódó riasztások?

A célként megadott erőforrás figyelni, metrika neve, feltétel típusa (statikus vagy dinamikus), és a feltétel (egy operátort és egy küszöbértéket/érzékenység) és a riasztási szabály aktiválódásakor aktiválását műveletcsoport megadásával meghatározhatja a metrikaalapú riasztási szabály. Szabályfeltétel-típusokra hatással küszöbértékek határozzák meg. [Dinamikus küszöbérték feltételt és érzékenységi lehetőségekkel kapcsolatos további](alerts-dynamic-thresholds.md).

### <a name="alert-rule-with-static-condition-type"></a>Statikus feltétellel típusú riasztási szabály

Tegyük fel, létrehozott egy egyszerű statikus küszöbérték a metrikaalapú riasztási szabály a következő:

- Célerőforrásnál (az Azure-erőforrás figyelni kívánt): myVM
- Metrika: Százalékos processzorhasználat
- Feltétel típusa: Statikus
- Idő összesítése (nyers metrikaértékek keresztül futtató statisztika. Támogatott idő összesítések Min, Max, Avg, összesen): Átlag
- Időszak (a hely vissza ablak keresztül melyik mérőszám a rendszer ellenőrzi a értékek): Az utolsó 5 perc alatt
- Gyakoriság (a gyakoriság, amellyel a metrikariasztás ellenőrzi, ha a feltételek teljesülnek-e): 1 perc
- Operátor: Nagyobb, mint
- Küszöbérték: 70

Kezdve a riasztási szabály jön létre a figyelő 1 percenként fut, és az utolsó 5 percben metrikaértékek megvizsgál, és ellenőrzi, ha ezek az értékek átlaga meghaladja a 70. Ha a feltétel teljesül, amely, az átlagos százalékos Processzorhasználat az utolsó 5 percig meghaladja a 70, a riasztási szabály egy aktivált értesítés aktiválódik. Ha konfigurálta a műveletcsoport, a riasztási szabályhoz tartozó e-mailben vagy a web hook művelet, kapni fog egy aktivált értesítést is.

### <a name="alert-rule-with-dynamic-condition-type"></a>Riasztási szabály a dinamikus feltétel típusa

Tegyük fel, létrehozott egy egyszerű dinamikus küszöbértékeket a metrikaalapú riasztási szabály a következő:

- Célerőforrásnál (az Azure-erőforrás figyelni kívánt): myVM
- Metrika: Százalékos processzorhasználat
- Feltétel típusa: Dinamikus
- Idő összesítése (nyers metrikaértékek keresztül futtató statisztika. Támogatott idő összesítések Min, Max, Avg, összesen): Átlag
- Időszak (a hely vissza ablak keresztül melyik mérőszám a rendszer ellenőrzi a értékek): Az utolsó 5 perc alatt
- Gyakoriság (a gyakoriság, amellyel a metrikariasztás ellenőrzi, ha a feltételek teljesülnek-e): 1 perc
- Operátor: Nagyobb, mint
- Érzékenység: Közepes
- Tekintse meg a vissza időszakok: 4
- Szabálysértések száma: 4

A riasztási szabály létrehozása után a dinamikus küszöbértékeket, gépi tanulási algoritmus fogja beszerezni előzményadatok elérhető, kiszámíthatja a küszöbérték, amely legjobban megfelel a metrika sorozat működését és folyamatosan fog ismerje meg, győződjön meg arról, hogy új adatok alapján a pontosabb küszöbértéket.

Az az idő a riasztási szabály jön létre a figyelő 1 percenként fut, és a metrikaértékek megvizsgálja az elmúlt 20 perc, 5 perces időszakokra vannak csoportosítva, és ellenőrzi, hogy ha az egyes 4 időszakokra az időszak értékek átlaga meghaladja-e a várt küszöbértéket. Ha, hogy a feltétel teljesül, az átlagos százalékos Processzorhasználat az elmúlt 20 perc (négy 5 perces időszakra) eltérnek a várt viselkedés négyszer, a riasztási szabály egy aktivált értesítés aktiválódik. Ha konfigurálta a műveletcsoport, a riasztási szabályhoz tartozó e-mailben vagy a web hook művelet, kapni fog egy aktivált értesítést is.

### <a name="view-and-resolution-of-fired-alerts"></a>Nézet és aktivált riasztások feloldása

A fenti példákban ez riasztási szabályok az Azure Portalon is megtekinthetők a **minden riasztás** panelen.

Tegyük fel a "myVM" használata a további ellenőrzések meghaladja a küszöbértéket a rendszer továbbra is fennáll, a riasztási szabály nem indulnak el újra, a feltételek megoldásáig.

Egy kicsit, ha a használati "myVM" ismét le a normál, a küszöbérték alá csökken. A riasztási szabály a feltétel a megoldott értesítést küldjön a még két alkalommal figyeli. A riasztási szabály feloldva/inaktiválása üzenetet küld a három egymást követő pontot váltakozó feltételek esetén zaj csökkentésére a riasztási feltétel nem teljesülése esetén.

A feloldott értesítés küldése webhook vagy e-mailben, mivel az Azure Portalon (figyelő állapota is nevezik) riasztási példány állapotát is értéke megoldott.

### <a name="using-dimensions"></a>Méretek használatával

Az Azure monitorban metrikariasztásokat is egyetlen szabállyal több dimenziók értékkombinációkra figyelésére is alkalmas. Tekintsük át, miért használhat több dimenzió kombináció egy példa segítségével.

Tegyük fel, a webhely App Service-csomag rendelkezik. CPU-használat a webhelyet vagy alkalmazást futtató alkalmazáspéldányok a figyelni kívánt. Ehhez a metrikaalapú riasztási szabály használatával az alábbiak szerint:

- Célerőforrásnál: myAppServicePlan
- Metrika: Százalékos processzorhasználat
- Feltétel típusa: Statikus
- Dimenziók
  - Példány InstanceName1, InstanceName2 =
- Idő összesítése: Átlag
- Időszak: Az utolsó 5 perc alatt
- Gyakorisága: 1 perc
- Operátor: GreaterThan
- Küszöbérték: 70

Mielőtt Ez a szabály figyeli, ha az átlagos CPU-használat az elmúlt 5 percig meghaladja a 70 %-os hasonlóan. Azonban ugyanaz a szabály figyelheti a webhely két példányai. Minden példány első figyeli a rendszer külön-külön, és külön-külön értesítéseket fog kapni.

Tegyük fel, amelyek kapja a masszív igény szerinti webes alkalmazás rendelkezik, és további példányok hozzáadása kell. A fenti szabály továbbra is elegendő: két példány figyeli. Azonban a szabály módon hozhat létre:

- Célerőforrásnál: myAppServicePlan
- Metrika: Százalékos processzorhasználat
- Feltétel típusa: Statikus
- Dimenziók
  - Instance = *
- Idő összesítése: Átlag
- Időszak: Az utolsó 5 perc alatt
- Gyakorisága: 1 perc
- Operátor: GreaterThan
- Küszöbérték: 70

Ez a szabály automatikusan figyelni fogja a példány azaz minden értékét a példányok figyelheti, anélkül, hogy újra módosítani a metrikaalapú riasztási szabály származnak.

Több dimenzióban figyelésekor riasztások szabályt hozhat létre dinamikus küszöbértékek egyszerre személyre szabott metrika sorozat több száz küszöbértékeit. Dinamikus küszöbérték kevesebb riasztási szabályok kezelése és a felügyeleti és a riasztásokat szabályok létrehozását mentése jelentős mennyiségű időt eredményez.

Tegyük fel, számos webalkalmazás rendelkezik, és nem ismeri a leginkább megfelelő küszöbértéket van. A fenti szabályok mindig használja a 70 %-os küszöbértéket. Azonban a szabály módon hozhat létre:

- Célerőforrásnál: myAppServicePlan
- Metrika: Százalékos processzorhasználat
- Feltétel típusa: Dinamikus
- Dimenziók
  - Instance = *
- Idő összesítése: Átlag
- Időszak: Az utolsó 5 perc alatt
- Gyakorisága: 1 perc
- Operátor: GreaterThan
- Érzékenység: Közepes
- Tekintse meg a vissza időszakok: 1
- Szabálysértések száma: 1

Ez a szabály figyeli, ha az átlagos CPU-használat az elmúlt 5 percig meghaladja az egyes példányok várt működése. Ugyanaz a szabály példányok figyelheti, anélkül, hogy újra módosítani a metrikaalapú riasztási szabály származnak. Minden példány egy küszöbértéket, amely megfelel a metrika sorozat működését lekérése, és a folyamatos módosítása, hogy a küszöbérték pontosabb új adatok alapján lesz. Például a előtt mindegyik példány külön-külön figyeli, és külön-külön értesítéseket fog kapni.

Szabálysértések száma és a hely visszaírt növelése lehetővé teszi a jelentős eltérés-definícióban csak riasztásra a riasztások szűrése. [További információ a Speciális beállítások dinamikus küszöbértékek](alerts-dynamic-thresholds.md#what-do-the-advanced-settings-in-dynamic-thresholds-mean).

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Metrikákhoz kapcsolódó riasztások az Azure Monitor használatával nagy mennyiségű figyelése

Az eddigi látott hogyan egyetlen metrikariasztás használható egy vagy több metrika figyelése egyetlen Azure-erőforráshoz kapcsolódó idősoros. Sok esetben érdemes alkalmazandó az erőforrások száma azonos riasztási szabályt. Az Azure Monitor is támogatja az egy a metrikaalapú riasztási szabály több erőforrások monitorozását. Ez a funkció jelenleg csak virtuális gépeken támogatott. Ezenkívül egyetlen metrikariasztás figyelheti az erőforrások egy Azure-régióban.

Megadhat egyetlen metrikariasztás három módszerrel a figyelés hatóköre:

- egy előfizetésen belül egy Azure-régióban lévő virtuális gépek listájának
- egy vagy több erőforráscsoport az előfizetéshez (egy Azure-régióban) található összes virtuális gép
- egy adott előfizetéshez (egy Azure-régióban) található összes virtuális gép

Például a metrikaalapú riasztási szabályok több erőforrást figyelő létrehozása van [bármely más metrikariasztás létrehozása](alerts-metric.md) , amely figyeli egy erőforrást. Csak a különbség az, hogy kell választani a figyelni kívánt összes erőforrást. Ezek a szabályok használatával is létrehozhat [Azure Resource Manager-sablonok](../../azure-monitor/platform/alerts-metric-create-templates.md#template-for-metric-alert-that-monitors-multiple-resources). Az egyes virtuális gépekhez egyedi értesítéseket fog kapni.

## <a name="typical-latency"></a>Átlagos késés

A metrikákhoz kapcsolódó riasztások általában értesítést kap 5 perc alatt Ha a riasztási szabály gyakoriságának 1 perc lehet. A nagy terhelés értesítési rendszerek esetben hosszabb késéssel láthatja.

## <a name="supported-resource-types-for-metric-alerts"></a>Metrikákhoz kapcsolódó riasztások a támogatott erőforrástípusok

Annak a támogatott típusok teljes listáját a jelen [cikk](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported).

Ha klasszikus metrikariasztásokat használatát még ma, és keres, ha metrikákhoz kapcsolódó riasztások támogatásához az összes erőforrás megtekintéséhez használja, az alábbi táblázat az erőforrás típusok klasszikus metrikariasztásokat támogatja, és ha támogatja őket metrikákhoz kapcsolódó riasztások még ma, vagy sem.

|Klasszikus metrikariasztásokat támogatja erőforrás típusa | Metrikákhoz kapcsolódó riasztások által támogatott |
|-------------------------------------------------|----------------------------|
| Microsoft.ApiManagement/service | Igen |
| Microsoft.Batch/batchAccounts| Igen|
|Microsoft.Cache/redis| Igen |
|Microsoft.ClassicCompute/virtualMachines | Nem |
|Microsoft.ClassicCompute/domainNames/slots/roles | Nem|
|Microsoft.CognitiveServices/accounts | Nem |
|Microsoft.Compute/virtualMachines | Igen|
|Microsoft.Compute/virtualMachineScaleSets| Igen|
|Microsoft.ClassicStorage/storageAccounts| Nem |
|Microsoft.DataFactory/datafactories | Igen|
|Microsoft.DBforMySQL/servers| Igen|
|Microsoft.DBforPostgreSQL/servers| Igen|
|Microsoft.Devices/IotHubs | Nem|
|Microsoft.DocumentDB/databaseAccounts| Igen|
|Microsoft.EventHub/namespaces | Igen|
|Microsoft.Logic/workflows | Igen|
|Microsoft.Network/loadBalancers |Igen|
|Microsoft.Network/publicIPAddresses| Igen|
|Microsoft.Network/applicationGateways| Igen|
|Microsoft.Network/expressRouteCircuits| Igen|
|Microsoft.Network/trafficManagerProfiles | Igen|
|Microsoft.Search/searchServices | Igen|
|Microsoft.ServiceBus/namespaces| Igen |
|Microsoft.Storage/storageAccounts | Igen|
|Microsoft.StreamAnalytics/streamingjobs| Igen|
|Microsoft.TimeSeriesInsights/environments | Igen|
|Microsoft. Web/serverfarms | Igen |
|Microsoft. Webhelyek / (kivéve a functions) | Igen|
|Microsoft. Web/hostingEnvironments/multiRolePools | Nem|
|Microsoft. Web/hostingEnvironments/workerPools| Nem |
|Microsoft.SQL/Servers | Nem |

## <a name="next-steps"></a>További lépések

- [Ismerje meg, hogyan létrehozása, megtekintése és kezelése az Azure-ban metrikákhoz kapcsolódó riasztások](alerts-metric.md)
- [Ismerje meg, hogyan helyezhet üzembe Azure Resource Manager-sablonok használatával metrikákhoz kapcsolódó riasztások](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [További információ a műveletcsoportokról](action-groups.md)
- [További információ a dinamikus küszöbértékek feltétel típusa](alerts-dynamic-thresholds.md)
