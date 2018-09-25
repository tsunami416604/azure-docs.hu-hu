---
title: Megismerheti, hogyan riasztást küld a metrika a munkát az Azure monitorban.
description: Mire képes a metrikákhoz kapcsolódó riasztások, és hogyan működnek az Azure monitorban áttekintést kaphat.
author: snehithm
ms.author: snmuvva
ms.date: 9/18/2018
ms.topic: conceptual
ms.service: azure-monitor
ms.component: alerts
ms.openlocfilehash: 1ec47ddf5769dd8ed624277a86db57f449581b90
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948689"
---
# <a name="understand-how-metric-alerts-work-in-azure-monitor"></a>Megismerheti, hogyan riasztást küld a metrika a munkát az Azure monitorban

Az Azure monitorban metrikákhoz kapcsolódó riasztások felett többdimenziós metrikák működik. Ezek a metrikák platform mérőszámokat, egyéni metrikák (előzetes verzió), népszerű naplók, metrikák, a standard mérőszámok az Application Insights alakítani a Log Analytics lehet. Metrikákhoz kapcsolódó riasztások kiértékelése annak ellenőrzéséhez, hogy rendszeres időközönként feltételek egy vagy metrika idősorozat-igaz, és értesíti, amint az értékelések teljesülnek-e. Metrikákhoz kapcsolódó riasztások állapotalapúak, csak küldenek értesítéseket állapot.

## <a name="how-do-metric-alerts-work"></a>Hogyan működik a metrikákhoz kapcsolódó riasztások

Megadhatja a metrikaalapú riasztási szabály a célként megadott erőforrás figyelt metrika neve és a feltétel (egy operátort és egy küszöbértéket.) és a riasztási szabály aktiválódásakor aktiválását műveletcsoport megadásával.
Tegyük fel, létrehozott egy egyszerű a metrikaalapú riasztási szabály a következő:

- Célerőforrásnál (az Azure-erőforrás figyelni kívánt): myVM
- Metrika: Százalékos Processzorhasználat
- Idő összesítése (nyers metrikaértékek keresztül futtató statisztika. Támogatott idő összesítések Min, Max, Avg, összesen): átlagos
- Időszak (a hely vissza ablak keresztül melyik mérőszám a rendszer ellenőrzi a értékek): az utolsó 5 perc alatt
- Gyakoriság (a gyakoriság, amellyel a metrikariasztás ellenőrzi, ha a feltételek teljesülnek-e): 1 perc
- Üzemeltető: Nagyobb, mint
- Küszöbérték: 70

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
- Metrika: Százalékos Processzorhasználat
- Dimenziók
  - Példány InstanceName1, InstanceName2 =
- Idő összesítése: átlagos
- Időszak: az utolsó 5 perc alatt
- Gyakoriság: 1 perc
- Üzemeltető: GreaterThan
- Küszöbérték: 70

Mielőtt Ez a szabály figyeli, ha az átlagos CPU-használat az elmúlt 5 percig meghaladja a 70 %-os hasonlóan. Azonban ugyanaz a szabály figyelheti a webhely két példányai. Minden példány első figyeli a rendszer külön-külön, és külön-külön értesítéseket fog kapni.

Tegyük fel, a webes alkalmazás kapja a masszív igény szerint, és további példányok hozzáadása kell. A fenti szabály továbbra is elegendő: két példány figyeli. Azonban a következő létrehozhat egy szabályt.

- Célerőforrásnál: myAppServicePlan
- Metrika: Százalékos Processzorhasználat
- Dimenziók
  - Példány = *
- Idő összesítése: átlagos
- Időszak: az utolsó 5 perc alatt
- Gyakoriság: 1 perc
- Üzemeltető: GreaterThan
- Küszöbérték: 70

Ez a szabály automatikusan figyelni fogja a példány azaz minden értékét a példányok figyelheti, anélkül, hogy újra módosítani a metrikaalapú riasztási szabály származnak.

### <a name="monitoring-multiple-resource-using-metric-alerts"></a>Több erőforrást metrikákhoz kapcsolódó riasztások figyelése

Ahogy az előző szakaszban láthatta, az lehetséges, hogy egyetlen a metrikaalapú riasztási szabály, amely minden egyes dimenzió kombináció (vagyis) metrika az idősor lépésközi). Azonban Ön egyszerre csak egy erőforrás ezt korlátozva. Metrikákhoz kapcsolódó riasztások mostantól is támogatják az előzetes verzióban érhető el egyetlen szabállyal több erőforrások figyeléséhez. Ha virtuális gépek 100s az előfizetésében, ez az új funkció segítségével, gyorsan beállította a figyelést a számukra. 

Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Metrikaalapú riasztási szabályok több erőforrást figyelő létrehozása jelenleg nem támogatott az Azure Portalon keresztül. Ezek a szabályok az Azure Resource Manager-sablonok segítségével hozhat létre.

## <a name="typical-latency"></a>Átlagos késés

A metrikákhoz kapcsolódó riasztások általában értesítést kap 5 perc alatt Ha a riasztási szabály gyakoriságának 1 perc lehet. A nagy terhelés értesítési rendszerek esetben hosszabb késéssel láthatja.

## <a name="supported-resource-types-for-metric-alerts"></a>Metrikákhoz kapcsolódó riasztások a támogatott erőforrástípusok

Annak a támogatott típusok teljes listáját a jelen [cikk](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported)

## <a name="next-steps"></a>További lépések

- [Ismerje meg, hogyan hozhat létre, megtekintése és kezelése az Azure-ban metrikákhoz kapcsolódó riasztások](alert-metric.md)
- [Ismerje meg, hogyan helyezhet üzembe Azure Resource Manager-sablonok használatával metrikákhoz kapcsolódó riasztások](monitoring-create-metric-alerts-with-templates.md)
- [További információ a műveletcsoportokról](monitoring-action-groups.md)
