---
title: Az Azure Log Analyticsben adatok megtekintésére és elemzésére |} A Microsoft Docs
description: Ez a cikk ismerteti, amelyek segítségével használhatja a portálok létrehozásához és szerkesztéséhez az Azure Log Analytics naplóbeli kereséseivel.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 8fdf1bed0b75111abef4579565698f0c48b5d843
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724145"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>A Log Analytics adatok megtekintésére és elemzésére
Két lehetőség van a Log analytics szolgáltatásban tárolt adatok elemzése és az alkalmi elemzési lekérdezések létrehozásához az Azure Portalon érhető el. A lekérdezések létrehozásakor használja a fenti portálokhoz más funkciók, például a riasztások és az irányítópultok is használható.

## <a name="log-analytics-page"></a>Log Analytics-lap
Nyissa meg a Log Analytics lapot **naplók** a Log Analytics menüben. Ez az új felülettel jelentkezik a naplóadatok használatának és lekérdezések létrehozásáról. Ismerkedjen meg ezen a portálon, és vizsgálja meg a szolgáltatásokat [Ismerkedés a Log Analytics-oldal az Azure Portalon](query-language/get-started-analytics-portal.md).

A Log Analytics lapot a következő fejlesztéseket kínálja keresztül a [naplóbeli keresés (klasszikus)](#log-search-classic) tapasztalható.

* Több lap – hozzon létre külön lapokra több lekérdezés használata.
* Látványos vizualizációkkal – különböző értékeléséről vagy beállítások.
* Továbbfejlesztett Intellisense és nyelvi automatikus kiegészítését.
* Szintaxiselemek kiemelése – az az olvashatóságot a lekérdezések. 
* Lekérdezéskezelő – mentett hozzáférés lekérdezése, és működik.
* Séma nézet – tekintse át a lekérdezések írásának módját segítség az adatok struktúráját.
* Megosztás – lekérdezések vagy PIN-kód-lekérdezést minden olyan megosztott Azure-irányítópultra mutató hivatkozások létrehozása.
* Az intelligens elemzés – azonosítja a diagramok és a egy gyors elemzés a OK adatforgalmi csúcsokhoz.
* Oszlop kiválasztása – rendezheti, és a lekérdezési eredményekben oszlopok csoportban.

> [!NOTE]
> A Log Analytics lapot a bővített analitika portál, amely egy külső eszköz, az Azure Portalon kívül ugyanazokkal a funkciókkal rendelkezik. A bővített analitika portál továbbra is elérhető, de hivatkozások és a rá mutató hivatkozásokat az Azure Portalon váltotta fel az új lap.

![A speciális analitika portálon](media/log-analytics-log-search-portals/advanced-analytics-portal.png)

### <a name="resource-logs"></a>Erőforrás-naplók
Az új Log Analytics felhasználói felület különböző Azure-erőforrások például a virtuális gépek integrálható. Ez azt jelenti, hogy meg tudja nyitni a Log Analytics lapot, közvetlenül az erőforrás figyelési menü Váltás az Azure Monitor vagy a Log Analytics és az erőforrás-környezet elvesztése nélkül. **Naplók** még nincs engedélyezve az összes Azure-erőforrások, de elindul, a különböző erőforrások menü a portálon megjelenő típusok esetében.

A Log Analytics egy adott erőforráshoz megnyitásakor automatikusan hatóköre csak erőforrás rekordok naplózására.   Ha szeretné, hogy más rekordokat tartalmaz, majd kell azt a Log Analytics vagy az Azure figyelő menü megnyitásához.

A következő beállítások még nem érhető el a Log Analytics-erőforrás nézeten keresztül:

- Mentés
- riasztás beállítása
- Lekérdezéskezelő
- Váltás másik munkaterületet és az erőforrások (jelenleg nem tervezett)


### <a name="firewall-requirements"></a>Tűzfalra vonatkozó követelmények
A böngészőben a Log Analytics lapot, és a bővített analitika portál eléréséhez a következő címek hozzáférésre van szüksége.  Ha a böngésző tűzfalon keresztül fér hozzá az Azure Portalon, engedélyeznie kell ezeket a címeket a hozzáférést.

| URI | IP | Portok |
|:---|:---|:---|
| portal.loganalytics.io | Dinamikus | 80,443 |
| api.loganalytics.io    | Dinamikus | 80,443 |
| docs.loganalytics.io   | Dinamikus | 80,443 |


## <a name="log-search-classic"></a>Naplókeresés (klasszikus)
Nyissa meg a keresési oldalon a **naplók (klasszikus)** a Log Analytics menüben, vagy a **Log Analytics** az Azure figyelő menü. Ez az a klasszikus oldal használt funkcióját, amely nem rendelkezik a további Log Analytics-lekérdezések használata a [Log Analytics lapot](#log-analytics-page) fent.



![Log Search lap](media/log-analytics-log-search-portals/log-search-portal.png)


## <a name="next-steps"></a>További lépések

- Végig egy [oktatóanyag naplóbeli keresés használatával](log-analytics-tutorial-viewdata.md) megtudhatja, hogyan hozhat létre lekérdezést a lekérdezési nyelv segítségével
- Végig egy [. lecke a bővített analitika portál használatával](query-language/get-started-analytics-portal.md) amely biztosítja, hogy ugyanazt a felhasználói élményt, mint a Log Analytics lapot.

