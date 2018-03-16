---
title: "Adatok elemzése az Azure Naplóelemzés nézetek létrehozása |} Microsoft Docs"
description: "Naplóelemzési adatforrásnézet-tervezőből használatával és az Azure-portálon jelenik meg az adatmegjelenítéseket a Naplóelemzési munkaterület különböző egyéni nézeteket is létrehozhat. Ez a cikk adatforrásnézet-tervezőből áttekintését tartalmazza, és eljárások létrehozásának és szerkesztésének egyéni nézetek mutatja be."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: ce41dc30-e568-43c1-97fa-81e5997c946a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: bwren
ms.openlocfilehash: d63d47c39054230307416e24ed1c8295fbf68d93
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="create-custom-views-by-using-view-designer-in-log-analytics"></a>Naplóelemzési adatforrásnézet-tervezőből segítségével egyéni nézeteket hozhat létre
Az adatforrásnézet-tervezőből használatával [Azure Naplóelemzés](log-analytics-overview.md), az Azure portálon, amelyik segíthet a Naplóelemzési munkaterület az adatok megjelenítése egyéni nézetek számos hozhat létre. Ez a cikk áttekintést nyújt adatforrásnézet-tervezőből és eljárások létrehozásának és szerkesztésének egyéni nézetek.

Az adatforrásnézet-tervezőből kapcsolatos további információkért lásd:

* [Hivatkozás csempe](log-analytics-view-designer-tiles.md): a beállítások az egyes az egyéni nézetekben elérhető csempék hivatkozás ismerteti.
* [A képi megjelenítés része hivatkozás](log-analytics-view-designer-parts.md): egy referencia-útmutató az egyéni nézetek elérhető képi megjelenítés összetevőkre vonatkozó beállításokat tartalmaz.


## <a name="concepts"></a>Alapelvek
Nézetek jelennek meg a **áttekintése** lap a Naplóelemzési munkaterület az Azure portálon. Betűrendben jelennek meg az egyes egyéni nézetek csempéket, és a csempék a megoldások telepített ugyanazon a munkaterületen.

![– Áttekintés oldalra](media/log-analytics-view-designer/overview-page.png)

Az adatforrásnézet-tervezőből létrehozott nézetek a következő táblázatban leírt elemeket tartalmazzák:

| Rész | Leírás |
|:--- |:--- |
| Csempék | A Naplóelemzési munkaterület megjelenő **áttekintése** lap. Mindegyik mozaiknál ezt az egyéni nézetének visual összegzését jeleníti meg. Minden egyes csempetípus biztosít a rekordok eltérő képi megjelenítést alkalmazott. Választhat egy csempe olyan egyéni nézetet. |
| Egyéni nézet | Amikor kiválaszt egy csempe jelenik meg. Mindegyik nézetről tartalmaz egy vagy több képi megjelenítés részeit. |
| A képi megjelenítés részei | Az adatok a Naplóelemzési munkaterület egy vagy több alapuló képi megjelenítés jelenleg [keresések jelentkezzen](log-analytics-log-searches.md). A legtöbb részeit közé tartozik a fejlécet, amely magas szintű képi megjelenítés biztosít, és a listáját, ami a felső eredményeit jeleníti meg. Minden egyes rész típusának biztosít a Naplóelemzési munkaterület rekordját eltérő képi megjelenítést alkalmazott. Elem kiválasztása a rész a részletes rekordok biztosító napló keresést végezni. |


## <a name="work-with-an-existing-view"></a>Egy meglévő nézethez használata
Adatforrásnézet-tervezőből létrehozott nézetek megjelenítése a következő beállításokat:

![Áttekintés menü](media/log-analytics-view-designer/overview-menu.png)

A beállítások a következő táblázat ismerteti:

| Beállítás | Leírás |
|:--|:--|
| Frissítés   | Frissíti a nézetet a legújabb adatokkal. | 
| Elemzés | Megnyitja a [Advanced Analytics portál](log-analytics-log-search-portals.md#advanced-analytics-portal) napló keresések adatok elemzésére. |
| Szűrés    | Beállítja az adatokat, a nézetben szereplő idő szűrőt. |
| Szerkesztés      | A nézet megnyitása az adatforrásnézet-tervezőből annak tartalmát és a konfiguráció szerkesztése.  |
| Klónozás     | Létrehoz egy új nézetet, és megnyitja azt a adatforrásnézet-tervezőből. Az új nézet neve megegyezik az eredeti neve megegyezik, azonban *másolási* utótaggal. |


## <a name="create-a-new-view"></a>Új nézet létrehozása
Létrehozhat egy új nézetet az adatforrásnézet-tervezőből kiválasztásával a **adatforrásnézet-tervezőből** csempét a **áttekintése** a Naplóelemzési munkaterület oldalán.

![Tervező csempéje](media/log-analytics-view-designer/view-designer-tile.png)


## <a name="work-with-view-designer"></a>Az adatforrásnézet-tervezőből működik
Adatforrásnézet-tervezőből segítségével hozzon létre új nézetek, vagy módosítsa a létezőket. 

Adatforrásnézet-tervezőből három ablaktáblából áll: 
* **Tervezési**: az egyéni nézetet tartalmaz, amely éppen létrehozását és szerkesztését. 
* **Vezérlők**: tartalmazza a csempék és a hozzáadott részek a **tervezési** ablaktáblán. 
* **Tulajdonságok**: a csempék vagy kijelölt tulajdonságokat jeleníti meg.

![Nézettervező](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-the-view-tile"></a>Konfigurálja a csempéje
Egyéni nézet csak egyetlen csempe rendelkezhet. Az aktuális csempe megtekintéséhez, vagy válasszon másik kódot, válassza ki a **csempe** lapra a **vezérlő** ablaktáblán. A **tulajdonságok** ablaktábla megjeleníti az aktuális csempe tulajdonságait. 

A csempe tulajdonságait található információk alapján konfigurálhatja a [hivatkozás csempe](log-analytics-view-designer-tiles.md) , majd **alkalmaz** menti a módosításokat.

### <a name="configure-the-visualization-parts"></a>A képi megjelenítés kijelzők konfigurálása
A nézet a képi megjelenítés részek tetszőleges számú tartalmazhatnak. Kijelzők hozzáadása egy nézetet, válassza ki a **nézet** lapot, és válassza ki a képi megjelenítés része. A **tulajdonságok** ablaktáblán megjelennek azok a kijelölt kijelző tulajdonságait. 

A Nézet tulajdonságai található információk alapján konfigurálhatja a [képi megjelenítés része hivatkozás](log-analytics-view-designer-parts.md) , majd **alkalmaz** menti a módosításokat.

A nézetekben a képi megjelenítés részei csak egy sor van. A meglévő kijelzők átrendezheti húzással egy új helyre.

Eltávolíthatja a képi megjelenítés része a nézet kiválasztásával a **X** felső sarkában a része.


### <a name="menu-options"></a>A beállítások menü
A beállítások használatához a nézetek szerkesztési módban részelemcímkék ismertetését a következő táblázatban.

![A Szerkesztés menü](media/log-analytics-view-designer/edit-menu.png)

| Beállítás | Leírás |
|:--|:--|
| Mentés        | Menti a változtatásokat, és bezárja a nézetet. |
| Mégse      | Elveti a módosításait, és bezárja a nézetet. |
| Nézet törlése | Törli a nézetet. |
| Exportálás      | Exportálja a nézetet, hogy egy [Azure Resource Manager sablon](../azure-resource-manager/resource-group-authoring-templates.md) , amelyet importálhat egy másik munkaterületre. A fájl nevét a nézet nevét, és egy *omsview* bővítmény. |
| Importálás      | Importálja a *omsview* fájlt, amely egy másik munkaterület-ból exportált. Ez a művelet felülírja a meglévő nézetek konfigurációs. |
| Klónozás       | Létrehoz egy új nézetet, és megnyitja azt a adatforrásnézet-tervezőből. Az új nézet neve megegyezik az eredeti neve megegyezik, azonban *másolási* utótaggal. |
| Közzététel     | A nézet beilleszthet JSON-fájlba exportálja a [felügyeleti megoldás](../operations-management-suite/operations-management-suite-solutions-resources-views.md). A fájl neve megegyezik a nézet neve megegyezik, azonban a *json* bővítmény. Egy második fájlt, és hozza létre a *resjson* bővítmény, az erőforrásokat, amelyek a JSON-fájlban definiált értékeket tartalmaz.

## <a name="next-steps"></a>További lépések
* Adja hozzá [Csempék](log-analytics-view-designer-tiles.md) a egyéni nézetben.
* Adja hozzá [képi megjelenítés részek](log-analytics-view-designer-parts.md) a egyéni nézetben.
