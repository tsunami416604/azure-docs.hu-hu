---
title: Az Azure Monitor erőforráscsoport Insights |} A Microsoft Docs
description: Az állapotát és teljesítményét az elosztott alkalmazások és szolgáltatások az Azure monitorral, az erőforráscsoport szintjén ismertetése
services: azure-monitor
author: NumberByColors
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 08/29/2018
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: 723006d37ed0570e32790a0bb70a3dce5a87ade8
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43345219"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>A figyelő erőforráscsoportok és az Azure Monitor (előzetes verzió)

A modern alkalmazások gyakran összetett és elosztott rendelkező, sok diszkrét részt együttműködése valamilyen szolgáltatás biztosításához. FELISMERVE, ezt a komplexitást, az Azure Monitor nyújt figyelési erőforráscsoportokhoz. Így egyszerűen, osztályozhatja és diagnosztizálhatja problémáit az egyes erőforrások észlel, miközben környezet beállítására állapotát és teljesítményét az erőforráscsoport&mdash;és az alkalmazás&mdash;egészére.

## <a name="access-insights-for-resource-groups"></a>Erőforrás-csoportok férhetnek hozzá az információkhoz

1. Válassza ki **erőforráscsoportok** a bal oldali navigációs sávban.
2. Válasszon egyet az erőforrás-csoportok, amelyeket fel szeretne. (Ha rendelkezik az előfizetés szerinti szűrés erőforráscsoportok sok néha hasznos lehet.)
3. Egy erőforráscsoport insights eléréséhez kattintson **Insights** bármely erőforráscsoport a bal oldali menüben.

![Erőforrás csoport insights áttekintési oldalának képernyőképe](.\media\resource-group-insights\0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Erőforrások az aktív riasztások és állapotbeli problémák

Az Áttekintés oldal jeleníti meg, hány riasztást formázás és még aktív, a jelenlegi Azure Resource Health az egyes erőforrások együtt. Együtt ezek az információk segítségével gyorsan azonosíthatják a problémákat tapasztal, amikor erőforrásokat. Riasztások segítségével észlelheti a problémákat a kódot, és hogyan konfigurálta az infrastruktúrát. Az Azure Resource Health felületek probléma és az Azure platform magát, amely nem az egyes alkalmazásoknak.

![Képernyőkép az Azure Resource Health panelen](.\media\resource-group-insights\0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Az Azure Resource Health megjelenítéséhez, ellenőrizze a **megjelenítése az Azure Resource Health** a táblázat felett található. A lap betöltése gyorsan segítségével alapértelmezés szerint ez az oszlop rejtett.

![Képernyőkép a resource health graph hozzáadva](.\media\resource-group-insights\0003-overview.png)

Alapértelmezés szerint az erőforrások app réteget és az erőforrástípus szerint vannak csoportosítva. **App-réteget** erőforrástípusok, egy egyszerű kategorizálása, amely csak az erőforráscsoport insights áttekintő oldalán keretén belül van. Nincsenek erőforrástípusok kapcsolódó alkalmazáskód, a számítási infrastruktúra, hálózatkezelési, tárolási + adatbázisok. Felügyeleti eszközök a saját alkalmazás rétegek lekérése, és minden más erőforráshoz tartozó kategorizált a **más** app réteget. Ez a csoportosítás segítséget talál egy pillantással milyen az alkalmazás alrendszere megfelelő állapotú, és nem kifogástalan.

## <a name="diagnose-issues-in-your-resource-group"></a>Diagnosztizálhatja a problémákat az erőforráscsoportban

Az erőforráscsoport lapján insights segítségével diagnosztizálhatja a problémákat hatókörön belüli más eszközöket biztosít

   |         |          |
   | ---------------- |:-----|
   | [**Riasztások**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)      |  Megtekintheti, hozzon létre, és a riasztásokat. |
   | [**Metrikák**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) | Adatok megjelenítése és feltárása a metrika alapján.    |
   | [**Tevékenységnaplók**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) | Előfizetési szintű történt események az Azure-ban.  |
   | [**Alkalmazástérkép**](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) | Keresse meg az elosztott alkalmazás topológia a teljesítmény szűk vagy hiba elérési pontokhoz való azonosítására. |

## <a name="failures-and-performance"></a>Hibák és teljesítmény

Mi történik, ha az alkalmazás észrevette lassan fut, vagy a felhasználók hibákat jelzett? Érdemes időt vesz át az összes erőforrás probléma azonosítása.

A **teljesítmény** és **hibák** lapokat biztosít, köszönhetően a teljesítmény és a sikertelen diagnosztikai nézetek számos gyakori erőforrástípusok a folyamat leegyszerűsítése érdekében.

A legtöbb erőforrástípusok megnyílik egy katalógus az Azure Monitor munkafüzet sablonokat. Minden munkafüzet létrehozása mentett megosztott munkatársaival és a jövőben, hasonló problémák diagnosztizálása újrafelhasznált szabható testre.

### <a name="investigate-failures"></a>Hibák vizsgálata

Hibák lapon válassza a kipróbálásához **hibák** alatt **vizsgálat** a bal oldali menüben.

A bal oldali menüsáv változik, ha a kijelölt létrejött, új lehetőségeket kínál.

![Képernyőfelvétel: a hiba áttekintő panel](.\media\resource-group-insights\00004-failures.png)

Ha ki van választva az App Service, lehetősége lesz a katalógus az Azure Monitor munkafüzet sablonokkal.

![Képernyőkép a munkafüzet alkalmazáskatalógusában](.\media\resource-group-insights\0005-failure-insights-workbook.png)

A sablon kiválasztása hiba insights nyílik meg a munkafüzetet.

![Képernyőkép a hiba jelentése](.\media\resource-group-insights\0006-failure-visual.png)

A sorok közül választhat. A kijelölt majd egy grafikus részletek nézetben jelenik meg.

![Képernyőkép a hiba részletei](.\media\resource-group-insights\0007-failure-details.png)

Munkafüzetek absztrakt azonnal egyéni jelentések és Vizualizációk egy könnyen megérthető formátumba létrehozása nehéz munkáját. Néhány felhasználó előfordulhat, hogy szeretné, hogy csak az előre összeállított paraméterek beállítása, munkafüzetek is teljes mértékben testre szabható.

Válassza ki, hogyan működik ez a munkafüzet belsőleg megtapasztalhatja, hogy **szerkesztése** a lap tetején található.

![Képernyőkép a további szerkesztés lehetőség](.\media\resource-group-insights\0008-failure-edit.png)

Számos **szerkesztése** mező jelenik meg a munkafüzetet a különböző elemek mellett. Válassza ki a **szerkesztése** műveletek a táblázat alatti mezőbe.

![Képernyőkép a Szerkesztés mezők](.\media\resource-group-insights\0009-failure-edit-graph.png)

Ez a tárja fel az alapjául szolgáló Log Analytics-lekérdezés, amelyek a tábla vizualizációra.

 ![Log analytics lekérdezési ablak képernyőképe](.\media\resource-group-insights\0010-failure-edit-query.png)

A lekérdezés közvetlenül módosíthatja. Vagy használja azt referenciaként, és kérjen kölcsön származó, a saját egyéni paraméteres munkafüzet tervezésekor.

### <a name="investigate-performance"></a>Teljesítmény vizsgálata

Teljesítmény-munkafüzetek a saját katalógus kínál. Az App Service-ben az előre összeállított alkalmazás teljesítményének munkafüzet kínál az alábbi nézetben jelenítheti meg:

 ![Képernyőkép a teljesítmény nézet](.\media\resource-group-insights\0011-performance.png)

Ebben az esetben ha szerkesztési látni fogja, hogy az Azure Monitor-metrikák Vizualizációk egy készlete működteti.

 ![Teljesítmény nézet az Azure-mérőszámok – képernyőfelvétel](.\media\resource-group-insights\0012-performance-metrics.png)

## <a name="next-steps"></a>További lépések

- [Az Azure Monitor munkafüzetek](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)
- [Az Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Az Azure Monitor riasztások](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)
