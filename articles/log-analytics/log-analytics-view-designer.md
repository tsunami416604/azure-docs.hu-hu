---
title: Adatok elemzése az Azure Log Analytics nézetek létrehozása |} A Microsoft Docs
description: Nézettervező a Log Analytics használatával jelennek meg az Azure Portalon, és a Log Analytics-munkaterületen adatvizualizációk széles tartalmazó egyéni nézeteket hozhat létre. Ez a cikk áttekintést ad a Nézettervező, és létrehozását és szerkesztését, az egyéni nézetek eljárásokat mutat be.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 042271594893350e4883c6dc7dbdd21d84db0520
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50958321"
---
# <a name="create-custom-views-by-using-view-designer-in-log-analytics"></a>Az egyéni nézetek létrehozása az adatforrásnézet-tervezőből a Log Analytics használatával
Az adatforrásnézet-tervezőből használatával [Azure Log Analytics](log-analytics-queries.md), különböző egyéni nézeteket hozhat létre az Azure Portalon, amelyek segítségével adatokat a Log Analytics-munkaterület megjelenítése. Ez a cikk áttekintést nyújt az adatforrásnézet-tervezőből és eljárások létrehozása és módosítása az egyéni nézetek.

Nézettervező kapcsolatos további információkért lásd:

* [Csempe referencia](log-analytics-view-designer-tiles.md): biztosít egy referencia-útmutató az egyéni nézetekben a rendelkezésre álló csempék beállításai.
* [Vizualizációs rész referenciája](log-analytics-view-designer-parts.md): biztosít egy referencia-útmutató a beállítások a vizualizációs rész, amely az egyéni nézetek érhetők el.


## <a name="concepts"></a>Alapelvek
Nézetek jelennek meg a **áttekintése** lapján a Log Analytics-munkaterületet az Azure Portalon. Betűrend szerint jelennek meg a csempék az egyes egyéni nézetek és megoldások a csempék vannak telepítve az ugyanazon a munkaterületen.

![– Áttekintés oldalra](media/log-analytics-view-designer/overview-page.png)

Az adatforrásnézet-tervezőből létrehozott nézeteket az alábbi táblázatban leírt elemeket tartalmazzák:

| Rész | Leírás |
|:--- |:--- |
| Csempék | A Log Analytics-munkaterületre jelennek **áttekintése** lapot. Minden csempe ezt az egyéni nézetének visual összegzését jeleníti meg. Minden csempe írja be a rekordok egy másik vizualizációt biztosít. Egyéni nézet megjelenítéséhez olyan csempét választ. |
| Egyéni nézet | Amikor kiválaszt egy csempe jelenik meg. Egyes nézetek egy vagy több Vizualizáció részt tartalmaz. |
| Vizualizáció részei | Egy vizualizációt, az adatok alapján egy vagy több Log Analytics-munkaterületnek jelentenek [naplókereséseket](log-analytics-log-search.md). A legtöbb részei egy fejlécet, amely biztosít magas szintű képi megjelenítés, és a egy listát, amely megjeleníti a kiemelt találatok tartalmazzák. Minden egyes rész írja be a Log Analytics-munkaterületet a rekordok egy másik vizualizációt biztosít. A rész részletes rekordok biztosító Naplókeresés végrehajtásához elemek választhatja ki. |


## <a name="work-with-an-existing-view"></a>Meglévő nézet használata
Az adatforrásnézet-tervezőből létrehozott nézetek jelenítse meg a következő beállításokat:

![Áttekintése menü](media/log-analytics-view-designer/overview-menu.png)

A beállítások a következő táblázat ismerteti:

| Beállítás | Leírás |
|:--|:--|
| Frissítés   | Frissíti a nézetet a legújabb adatokkal. | 
| Elemzés | Megnyílik a [bővített analitika portál](log-analytics-log-search-portals.md) naplólekérdezések az adatok elemzéséhez. |
| Szerkesztés       | A nézet megnyitása az adatforrásnézet-tervezőből tartalmának és konfigurációs szerkesztéséhez.  |
| Klónozás      | Létrehoz egy új nézetet, és megnyílik a adatforrásnézet-tervezőből. Az új nézet neve ugyanaz, mint az eredeti nevén, de nem *másolási* rá hozzáfűzve. |
| Dátumtartomány | A dátum és idő dátumtartomány-szűrő, amely megtalálható a nézet az adatok megadása A dátumtartomány bármely dátumtartományokat lekérdezést a nézetben beállítása előtt a rendszer alkalmazza.  |
| +          | Egyéni szűrő megadásához, hogy van definiálva a nézet. |


## <a name="create-a-new-view"></a>Új nézet létrehozása
Létrehozhat egy új nézetet az adatforrásnézet-tervezőből kiválasztásával **adatforrásnézet-tervezőből** a Log Analytics-munkaterület menüben.

![Tervező csempe megtekintéséhez](media/log-analytics-view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Nézettervező használata
Nézettervező használatával hozzon létre új nézetek, vagy szerkesztheti a meglévőket. 

Nézettervező három ablaktáblák rendelkezik: 
* **Tervezési**: az egyéni nézetet tartalmaz, amely létrehozásakor és szerkesztését. 
* **Vezérlők**: tartalmazza a csempék és hozzáadott részek a **tervezési** ablaktáblán. 
* **Tulajdonságok**: a csempék vagy a kiválasztott részek tulajdonságait jeleníti meg.

![Nézettervező](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Konfigurálja a nézet csempe
Egyéni nézet csak egy adott csempe rendelkezhet. A jelenlegi csempe megtekintéséhez, vagy válasszon ki egy másik meglévőt, jelölje be a **csempe** lapján a **vezérlő** ablaktáblán. A **tulajdonságok** ablaktábla megjeleníti az aktuális csempe tulajdonságait. 

A csempe tulajdonságok található információk alapján konfigurálhatja a [referencia Csempére](log-analytics-view-designer-tiles.md) majd **alkalmaz** menti a módosításokat.

### <a name="configure-the-visualization-parts"></a>A Vizualizáció részek konfigurálása
Nézet tartalmazhat bármilyen vizualizációt kijelzők száma. Egy olyan nézetre részek hozzáadásához válassza a **nézet** lapra, és kattintson a Vizualizáció tartozik. A **tulajdonságok** ablaktábla a kijelölt részt tulajdonságait jeleníti meg. 

A nézet tulajdonságaiban található információk alapján konfigurálhatja a [vizualizációs rész referenciája](log-analytics-view-designer-parts.md) majd **alkalmaz** menti a módosításokat.

A nézetekben Vizualizáció részei csak egy sor van. Egy új helyre húzásával átrendezheti a meglévő részeket.

Eltávolíthat egy vizualizációs rész a nézet kiválasztásával a **X** felső rész, jobb.


### <a name="menu-options"></a>Menü parancsai
A beállítások használatához a nézetek szerkesztési módban az alábbi táblázatban leírtak szerint.

![Szerkesztés menü](media/log-analytics-view-designer/edit-menu.png)

| Beállítás | Leírás |
|:--|:--|
| Mentés        | Menti a módosításokat, és bezárja a nézetet. |
| Mégse      | Elveti a módosításokat, és bezárja a nézetet. |
| Nézet törlése | Törli a nézetet. |
| Exportálás      | Exportálja a nézetet, hogy egy [Azure Resource Manager-sablon](../azure-resource-manager/resource-group-authoring-templates.md) importálható egy másik munkaterületre. A fájl nevét a nézet nevét, és rendelkezik egy *omsview* bővítmény. |
| Importálás      | Importálja a *omsview* exportált egy másik munkaterületről. Ez a művelet felülírja a meglévő nézetek konfigurációját. |
| Klónozás       | Létrehoz egy új nézetet, és megnyílik a adatforrásnézet-tervezőből. Az új nézet neve ugyanaz, mint az eredeti nevén, de nem *másolási* rá hozzáfűzve. |

## <a name="next-steps"></a>További lépések
* Adjon hozzá [Csempék](log-analytics-view-designer-tiles.md) a egyéni nézethez.
* Adjon hozzá [Vizualizáció részek](log-analytics-view-designer-parts.md) a egyéni nézethez.
