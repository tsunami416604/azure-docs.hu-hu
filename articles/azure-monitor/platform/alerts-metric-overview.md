---
title: Megismerheti, hogyan riasztást küld a metrika a munkát az Azure monitorban.
description: Mire képes a metrikákhoz kapcsolódó riasztások, és hogyan működnek az Azure monitorban áttekintést kaphat.
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.component: alerts
ms.openlocfilehash: cf315718377210e13a6634c0eaf8caab158bcc4d
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53342221"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Megismerheti, hogyan riasztást küld a metrika a munkát az Azure monitorban

Az Azure monitorban metrikákhoz kapcsolódó riasztások felett többdimenziós metrikák működik. Ezek a metrikák lehet platform mérőszámokat, [egyéni metrikákat](../../azure-monitor/platform/metrics-custom-overview.md), [metrikák alakítani a Log Analytics népszerű naplók](../../azure-monitor/platform/alerts-metric-logs.md), standard mérőszámok az Application Insights. Metrikákhoz kapcsolódó riasztások kiértékelése annak ellenőrzéséhez, hogy rendszeres időközönként egy vagy több metrikát idősorozat-igaz, és értesíti, amint az értékelések teljesülnek a feltételek. Metrikákhoz kapcsolódó riasztások állapotalapúak, azaz csak küldenek értesítéseket állapot.

## <a name="how-do-metric-alerts-work"></a>Hogyan működik a metrikákhoz kapcsolódó riasztások

Megadhatja a metrikaalapú riasztási szabály a célként megadott erőforrás figyelt metrika neve és a feltétel (egy operátort és egy küszöbértéket.) és a riasztási szabály aktiválódásakor aktiválását műveletcsoport megadásával.
Tegyük fel, létrehozott egy egyszerű a metrikaalapú riasztási szabály a következő:

- Célerőforrásnál (az Azure-erőforrás figyelni kívánt): myVM
- Metrika: Százalékos processzorhasználat
- Idő összesítése (nyers metrikaértékek keresztül futtató statisztika. Támogatott idő összesítések Min, Max, Avg, összesen): Átlag
- Időszak (a hely vissza ablak keresztül melyik mérőszám a rendszer ellenőrzi a értékek):      Az utolsó 5 perc alatt
- Gyakoriság (a gyakoriság, amellyel a metrikariasztás ellenőrzi, ha a feltételek teljesülnek-e): 1 perc
- Operátor:     Nagyobb, mint
- Küszöbérték:      70

Kezdve a riasztási szabály jön létre a figyelő 1 percenként fut, és az utolsó 5 percben metrikaértékek megvizsgál, és ellenőrzi, ha ezek az értékek átlaga meghaladja a 70. Ha a feltétel teljesül, amely, az átlagos százalékos Processzorhasználat az utolsó 5 percig meghaladja a 70, a riasztási szabály egy aktivált értesítés aktiválódik. Ha konfigurálta a műveletcsoport, a riasztási szabályhoz tartozó e-mailben vagy a web hook művelet, kapni fog egy aktivált értesítést is.

A riasztási szabály elsőre adott példányának is megtekinthetők az összes riasztás panel az Azure Portalon.

Tegyük fel a "myVM" használata továbbra is meghaladja a küszöbértéket a rendszer az ellenőrzések, a riasztási szabály nem indulnak el újra, amíg a probléma nincs megoldva.

Miután múlva, ha a használati "myVM" megugrik a normál, kerül a megadott küszöbértéknél. A riasztási szabály a feltétel a megoldott értesítést küldjön a még két alkalommal figyeli. A riasztási szabály feloldva/inaktiválása üzenetet küld a három egymást követő pontot váltakozó feltételek esetén zaj csökkentésére a riasztási feltétel nem teljesülése esetén.

A feloldott értesítés küldése webhook vagy e-mailben, mivel az Azure Portalon (figyelő állapota is nevezik) riasztási példány állapotát is értéke megoldott.

## <a name="monitoring-at-scale-using-metric-alerts-in-azure-monitor"></a>Metrikákhoz kapcsolódó riasztások az Azure Monitor használatával nagy mennyiségű figyelése

### <a name="using-dimensions"></a>Méretek használatával

Az Azure monitorban metrikariasztásokat is egyetlen szabállyal több dimenzió értékkombinációkra figyelésére is alkalmas. Tekintsük át, miért használhat több dimenzió kombináció egy példa segítségével.

Tegyük fel, a webhely App Service-csomag rendelkezik. CPU-használat a webhelyet vagy alkalmazást futtató alkalmazáspéldányok a figyelni kívánt. Ehhez a metrikaalapú riasztási szabály használatával az alábbiak szerint

- Célerőforrásnál: myAppServicePlan
- Metrika: Százalékos processzorhasználat
- Dimenziók
  - Példány InstanceName1, InstanceName2 =
- Idő összesítése: Átlag
- Időszak: Az utolsó 5 perc alatt
- Gyakorisága: 1 perc
- Operátor: GreaterThan
- Küszöbérték: 70

Mielőtt Ez a szabály figyeli, ha az átlagos CPU-használat az elmúlt 5 percig meghaladja a 70 %-os hasonlóan. Azonban ugyanaz a szabály figyelheti a webhely két példányai. Minden példány első figyeli a rendszer külön-külön, és külön-külön értesítéseket fog kapni.

Tegyük fel, a webes alkalmazás kapja a masszív igény szerint, és további példányok hozzáadása kell. A fenti szabály továbbra is elegendő: két példány figyeli. Azonban a következő létrehozhat egy szabályt.

- Célerőforrásnál: myAppServicePlan
- Metrika: Százalékos processzorhasználat
- Dimenziók
  - Példány = *
- Idő összesítése: Átlag
- Időszak: Az utolsó 5 perc alatt
- Gyakorisága: 1 perc
- Operátor: GreaterThan
- Küszöbérték: 70

Ez a szabály automatikusan figyelni fogja a példány azaz minden értékét a példányok figyelheti, anélkül, hogy újra módosítani a metrikaalapú riasztási szabály származnak.

### <a name="monitoring-multiple-resources-using-metric-alerts"></a>Metrikákhoz kapcsolódó riasztások használatával több erőforrások figyelése

Ahogy az előző szakaszban láthatta, az lehetséges, hogy egyetlen a metrikaalapú riasztási szabály, amely minden egyes dimenzió kombináció (vagyis) metrika az idősor lépésközi). Azonban korábban, továbbra is korlátozva lett egyszerre csak egy erőforrás ezt. Az Azure Monitor is támogatja az egy a metrikaalapú riasztási szabály több erőforrások monitorozását. Ez a funkció jelenleg előzetes verzió, és csak a virtuális gépeket. Ezenkívül egyetlen metrikariasztás figyelheti az erőforrások egy Azure-régióban.

Megadhat egyetlen metrikariasztás három módszerrel a figyelés hatóköre:

- egy előfizetésen belül egy Azure-régióban lévő virtuális gépek listájának
- egy vagy több erőforráscsoport az előfizetéshez (egy Azure-régióban) található összes virtuális gép
- egy adott előfizetéshez (egy Azure-régióban) található összes virtuális gép

Metrikaalapú riasztási szabályok több erőforrást figyelő létrehozása jelenleg nem támogatott az Azure Portalon keresztül. Ezek a szabályok segítségével hozhat létre [Azure Resource Manager-sablonok](../../azure-monitor/platform/alerts-metric-create-templates.md#resource-manager-template-for-metric-alert-that-monitors-multiple-resources). Az egyes virtuális gépekhez egyedi értesítéseket fog kapni. 

## <a name="typical-latency"></a>Átlagos késés

A metrikákhoz kapcsolódó riasztások általában értesítést kap 5 perc alatt Ha a riasztási szabály gyakoriságának 1 perc lehet. A nagy terhelés értesítési rendszerek esetben hosszabb késéssel láthatja.

## <a name="supported-resource-types-for-metric-alerts"></a>Metrikákhoz kapcsolódó riasztások a támogatott erőforrástípusok

Annak a támogatott típusok teljes listáját a jelen [cikk](../../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported).

Ha klasszikus metrikariasztásokat használatát még ma, és keres, ha metrikákhoz kapcsolódó riasztások támogatásához az összes erőforrás megtekintéséhez használja, az alábbi táblázat az erőforrás típusok klasszikus metrikariasztásokat támogatja, és ha támogatja őket metrikákhoz kapcsolódó riasztások még ma, vagy sem.

|Klasszikus metrikariasztásokat támogatja erőforrás típusa | Metrikákhoz kapcsolódó riasztások által támogatott |
|-------------------------------------------------|----------------------------|
| Microsoft.ApiManagement/service | Igen |
| Microsoft.Batch/batchAccounts| Igen|
|Microsoft.Cache/redis| Igen
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
|Microsoft.DocumentDB/databaseAccounts| Nem|
|Microsoft.EventHub/namespaces | Igen|
|Microsoft.Logic/workflows | Igen|
|Microsoft.Network/loadBalancers |Igen|
|Microsoft.Network/publicIPAddresses| Igen|
|Microsoft.Network/applicationGateways| Igen|
|Microsoft.Network/expressRouteCircuits| Igen|
|Microsoft.Network/trafficManagerProfiles | Igen|
|Microsoft.Search/searchServices | Nem|
|Microsoft.ServiceBus/namespaces| Nem|
|Microsoft.Storage/storageAccounts | Igen|
|Microsoft.StreamAnalytics/streamingjobs| Igen|
|Microsoft.TimeSeriesInsights/environments | Igen|
|A Microsoft. Webalkalmazás/kiszolgálófarmok | Igen |
|A Microsoft. Webhelyek / (kivéve a functions) | Igen|
|A Microsoft. Webalkalmazás/hostingEnvironments/multiRolePools | Nem|
|A Microsoft. Webalkalmazás/hostingEnvironments/workerPools| Nem
|Microsoft.SQL/Servers | Nem|

## <a name="next-steps"></a>További lépések

- [Ismerje meg, hogyan hozhat létre, megtekintése és kezelése az Azure-ban metrikákhoz kapcsolódó riasztások](alerts-metric.md)
- [Ismerje meg, hogyan helyezhet üzembe Azure Resource Manager-sablonok használatával metrikákhoz kapcsolódó riasztások](../../azure-monitor/platform/alerts-metric-create-templates.md)
- [További információ a műveletcsoportokról](action-groups.md)
