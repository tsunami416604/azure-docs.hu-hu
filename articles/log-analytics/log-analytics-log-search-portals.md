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
ms.devlang: na
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: magoedte; bwren
ms.component: na
ms.openlocfilehash: 996502ffe5a31fcfa1b73dab9a041c336c4ea98f
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45602631"
---
# <a name="viewing-and-analyzing-data-in-log-analytics"></a>A Log Analytics adatok megtekintésére és elemzésére
Két lehetőség van a Log analytics szolgáltatásban tárolt adatok elemzése és az alkalmi elemzési lekérdezések létrehozásához az Azure Portalon érhető el. A lekérdezések létrehozásakor használja a fenti portálokhoz más funkciók, például a riasztások és az irányítópultok is használható.

## <a name="log-analytics-page"></a>Log Analytics-lap
Nyissa meg a Log Analytics lapot **naplók** a Log Analytics menüben. Ez az új felülettel jelentkezik a naplóadatok használatának és lekérdezések létrehozásáról. Ismerkedjen meg ezen a portálon, és vizsgálja meg a szolgáltatásokat [Ismerkedés a Log Analytics-oldal az Azure Portalon](query-language/get-started-analytics-portal.md).

A Log Analytics lapot a következő fejlesztéseket kínálja keresztül a [naplóbeli keresés](#log-search) tapasztalható.

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


### <a name="firewall-requirements"></a>Tűzfalra vonatkozó követelmények
A böngészőben a Log Analytics lapot, és a bővített analitika portál eléréséhez a következő címek hozzáférésre van szüksége.  Ha a böngésző tűzfalon keresztül fér hozzá az Azure Portalon, engedélyeznie kell ezeket a címeket a hozzáférést.

| URI | IP | Portok |
|:---|:---|:---|
| portal.loganalytics.io | Dinamikus | 80,443 |
| api.loganalytics.io    | Dinamikus | 80,443 |
| docs.loganalytics.io   | Dinamikus | 80,443 |


## <a name="log-search-classic"></a>Naplókeresés (klasszikus)
Nyissa meg a keresési oldalon a **naplók (klasszikus)** a Log Analytics menüben, vagy a **Log Analytics** az Azure figyelő menü. Ez a alapvető lekérdezések segítségével naplóadatokat elemzésére alkalmas. Szerkesztési lekérdezések több funkciókat biztosít a lekérdezési nyelv a teljes ismerete nélkül.  Megtekintheti ezeket a funkciókat az összegzését [naplókeresések létrehozása az Azure Log Analytics naplóbeli keresés](log-analytics-log-search-log-search-portal.md). 


![Log Search lap](media/log-analytics-log-search-portals/log-search-portal.png)


## <a name="next-steps"></a>További lépések

- Végig egy [oktatóanyag naplóbeli keresés használatával](log-analytics-tutorial-viewdata.md) megtudhatja, hogyan hozhat létre lekérdezést a lekérdezési nyelv segítségével
- Végig egy [. lecke a bővített analitika portál használatával](query-language/get-started-analytics-portal.md) amely biztosítja, hogy ugyanazt a felhasználói élményt, mint a Log Analytics lapot.

