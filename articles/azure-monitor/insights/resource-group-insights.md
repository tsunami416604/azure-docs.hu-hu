---
title: Azure Monitor erőforráscsoport-elemzések | Microsoft dokumentumok
description: Az Azure Monitor segítségével megismerheti az elosztott alkalmazások és szolgáltatások állapotát és teljesítményét az Erőforráscsoport szintjén
ms.subservice: ''
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 6d97e40bf2bf2298fb53609621db8ff2c6f1038f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663538"
---
# <a name="monitor-resource-groups-with-azure-monitor-preview"></a>Erőforráscsoportok figyelése az Azure Monitorsegítségével (előzetes verzió)

A modern alkalmazások gyakran összetettek és erősen elosztottak, sok különálló alkatrész dolgozik együtt a szolgáltatás nyújtásán. Ennek az összetettségnek a felismerése érdekében az Azure Monitor figyelési elemzéseket biztosít az erőforráscsoportok számára. Ez megkönnyíti az egyes erőforrások által tapasztalt problémák osztályozását és diagnosztizálását, miközben az&mdash;erőforráscsoport&mdash;és az alkalmazás egészének állapotát és teljesítményét egybevetve környezetet kínál.

## <a name="access-insights-for-resource-groups"></a>Erőforráscsoportokhoz való hozzáférés elemzési adatai

1. Válassza az **Erőforráscsoportok lehetőséget** a bal oldali navigációs sávon.
2. Válassza ki a vizsgálni kívánt erőforráscsoportok egyikét. (Ha nagy számú erőforráscsoport előfizetés szerinti szűréssel rendelkezik, néha hasznos lehet.)
3. Erőforráscsoport elemzési adatainak eléréséhez kattintson az **Elemzési** adatok elemre bármely erőforráscsoport bal oldali menüjében.

![Képernyőkép az erőforráscsoportok elemzési adatainak áttekintéséről lap](./media/resource-group-insights/0001-overview.png)

## <a name="resources-with-active-alerts-and-health-issues"></a>Aktív riasztásokkal és egészségügyi problémákkal rendelkező erőforrások

Az áttekintő lap azt mutatja, hogy hány riasztás lett elhelyezve, és még mindig aktív, valamint az egyes erőforrások aktuális Azure-erőforrás-állapota. Ez az információ együttesen segít a problémákat tapasztaló erőforrások gyors felismeréséhez. A riasztások segítenek a kódban felmerülő problémák és az infrastruktúra konfigurálásának módjának észlelésében. Az Azure Resource Health felületekkel kapcsolatos problémák az Azure platform, amely nem az egyes alkalmazások.

![Képernyőkép az Azure Resource Health ablaktábláról](./media/resource-group-insights/0002-overview.png)

### <a name="azure-resource-health"></a>Azure Resource Health

Az Azure Resource Health megjelenítéséhez jelölje be az **Azure-erőforrás-állapot megjelenítése** jelölőnégyzetet a táblázat felett. Ez az oszlop alapértelmezés szerint rejtett, hogy segítse az oldal gyors betöltését.

![Képernyőkép az erőforrás-állapotgrafikon hozzáadásával](./media/resource-group-insights/0003-overview.png)

Alapértelmezés szerint az erőforrások alkalmazásréteg és erőforrástípus szerint vannak csoportosítva. **Az alkalmazásréteg** az erőforrástípusok egyszerű kategorizálása, amely csak az erőforráscsoport-elemzések áttekintési lapjának környezetében található. Vannak olyan erőforrástípusok, amelyek az alkalmazáskódhoz, a számítási infrastruktúrához, a hálózatkezeléshez, a tároláshoz + adatbázisokhoz kapcsolódnak. A felügyeleti eszközök saját alkalmazásréteget kapnak, és minden más erőforrás a **Másik** alkalmazásréteghez tartozóként van kategorizálva. Ez a csoportosítás segítségével egy pillantással láthatja, hogy az alkalmazás alrendszerei kifogástalanok és nem kifogástalanok.

## <a name="diagnose-issues-in-your-resource-group"></a>Az erőforráscsoportproblémáinak diagnosztizálása

Az erőforráscsoport-elemzési lap számos más, a problémák diagnosztizálására szolgáló eszközt is tartalmaz.

   |         |          |
   | ---------------- |:-----|
   | [**Figyelmeztetések**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)      |  Megtekintheti, létrehozhatja és kezelheti az értesítéseket. |
   | [**Mutatókat**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) | Vizualizálja és tárja fel a metrikaalapú adatokat.    |
   | [**Tevékenységnaplók**](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) | Előfizetési szintű események, amelyek az Azure-ban történtek.  |
   | [**Alkalmazástérkép**](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) | Navigáljon az elosztott alkalmazás topológiájában a teljesítménybeli szűk keresztmetszetek vagy a sikertelen elérési pontok azonosításához. |

## <a name="failures-and-performance"></a>Hibák és teljesítmény

Mi a teendő, ha észrevette, hogy az alkalmazás lassan fut, vagy a felhasználók hibákat jelentettek? Időigényes az összes erőforrását átkeresni a problémák elkülönítéséhez.

A **Teljesítmény** és a hibák lap **leegyszerűsíti** ezt a folyamatot azáltal, hogy számos gyakori erőforrástípus teljesítmény- és hibadiagnosztikai nézeteit egyesíti.

A legtöbb erőforrástípus megnyit egy katalógust az Azure Monitor munkafüzet-sablonjaiból. Minden létrehozott munkafüzet testreszabható, menthető, megosztható a csapattal, és a jövőben újra felhasználható hasonló problémák diagnosztizálására.

### <a name="investigate-failures"></a>Hibák vizsgálata

A Hibák lap teszteléséhez válassza a Bal oldali menü **Hibák csoportjában** a **Hibák** lehetőséget.

A bal oldali menüsor a kijelölés után megváltozik, és új lehetőségeket kínál.

![Képernyőkép a Hiba áttekintése ablaktábláról](./media/resource-group-insights/00004-failures.png)

Ha az App Service van kiválasztva, megjelenik egy katalógus az Azure Monitor munkafüzet sablonok.

![Képernyőkép az alkalmazásmunkafüzet-gyűjteményről](./media/resource-group-insights/0005-failure-insights-workbook.png)

Ha kiválasztja a Hibaelemzési sablont, megnyílik a munkafüzet.

![Képernyőkép a hibajelentésről](./media/resource-group-insights/0006-failure-visual.png)

Bármelyik sort kijelölheti. A kijelölés ezután grafikus részletek nézetben jelenik meg.

![A hiba részleteinek képernyőképe](./media/resource-group-insights/0007-failure-details.png)

A munkafüzetek elvontatják az egyéni jelentések és vizualizációk könnyen fogyasztható formátumba való létrehozásának nehéz munkáját. Míg egyes felhasználók csak az előre összeállított paramétereket szeretnék módosítani, a munkafüzetek teljesen testreszabhatók.

Ha meg szeretné tudni, hogyan működik ez a munkafüzet belsőleg, válassza a **Szerkesztés** lehetőséget a felső sávon.

![Képernyőkép a további szerkesztési lehetőségről](./media/resource-group-insights/0008-failure-edit.png)

Számos **Szerkesztési** mező jelenik meg a munkafüzet különböző elemei közelében. Jelölje be a **Szerkesztés** jelölőnégyzetet a műveleti lista alatt.

![Képernyőkép a szerkesztési mezőkről](./media/resource-group-insights/0009-failure-edit-graph.png)

Ez felfedi az alapul szolgáló naplólekérdezést, amely a tábla vizualizációját hajtja.

 ![Képernyőkép a naplólekérdezésablakról](./media/resource-group-insights/0010-failure-edit-query.png)

A lekérdezést közvetlenül módosíthatja. Vagy használhatja referenciaként, és kölcsönözhet belőle saját egyéni paraméterezett munkafüzettervezésekor.

### <a name="investigate-performance"></a>Teljesítmény vizsgálata

A Performance saját munkafüzetgalériát kínál. Az App Service esetében az előre összeállított alkalmazásteljesítmény-munkafüzet a következő nézetet kínálja:

 ![Képernyőkép a teljesítménynézetről](./media/resource-group-insights/0011-performance.png)

Ebben az esetben, ha úgy dönt, szerkesztés látni fogja, hogy ez a képi megjelenítési azure monitor metrikák által.

 ![Képernyőkép az Azure Metrics teljesítménynézetéről](./media/resource-group-insights/0012-performance-metrics.png)

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="enabling-access-to-alerts"></a>A riasztásokhoz való hozzáférés engedélyezése

Az Azure Monitor erőforráscsoportokhoz való riasztásai megtekintéséhez az előfizetéshez tulajdonosi vagy közreműködői szerepkörrel rendelkező személynek meg kell nyitnia az Azure Monitor for Resource Groups szolgáltatást az előfizetés bármely erőforráscsoportjához. Ez lehetővé teszi, hogy az olvasási hozzáféréssel rendelkezők az Azure Figyelő erőforráscsoportokhoz riasztásokat láthassanak az előfizetés összes erőforráscsoportjához. Tulajdonos i vagy közreműködői szerepkör esetén néhány perc múlva frissítse ezt a lapot.

Az Azure Monitor erőforráscsoportok hoz támaszkodik az Azure Monitor riasztások felügyeleti rendszer a riasztási állapot lekéréséhez. Riasztások kezelése alapértelmezés szerint nincs konfigurálva minden erőforráscsoporthoz és előfizetéshez, és csak tulajdonosi vagy közreműködői szerepkörrel rendelkező személy engedélyezheti. Ez lehet engedélyezni akár:
* Az Azure Monitor megnyitása erőforráscsoportok hoz létre az előfizetés bármely erőforráscsoport.
* Vagy ha az előfizetéshez, az **Erőforrás-szolgáltatók**, majd a **Regisztráció a riasztásokhoz**parancsra kattint.

## <a name="next-steps"></a>További lépések

- [Azure Monitor-munkafüzetek](https://docs.microsoft.com/azure/application-insights/app-insights-usage-workbooks)
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Azure Monitor-riasztások](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)
