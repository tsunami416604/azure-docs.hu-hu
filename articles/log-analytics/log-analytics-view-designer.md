---
title: "Adatok elemzése az Azure Naplóelemzés nézetek létrehozása |} Microsoft Docs"
description: "A Naplóelemzési Nézettervező jelennek meg az Azure portálon, és az adatok a Naplóelemzési munkaterület különböző képi tartalmazó egyéni nézetek létrehozása teszi lehetővé. Ez a cikk adatforrásnézet-tervezőből és létrehozásának és szerkesztésének egyéni nézetek eljárások áttekintését tartalmazza."
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
ms.openlocfilehash: a84f40503c1b9778c496461ebbf6864f99bd1c4b
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="use-view-designer-to-create-custom-views-in-log-analytics"></a>Adatforrásnézet-tervezőből segítségével egyéni nézeteket hozhat létre a Naplóelemzési
Az adatforrásnézet-tervezőből [Naplóelemzési](log-analytics-overview.md) teszi lehetővé az Azure portálon, az adatok a Naplóelemzési munkaterület különböző képi tartalmazó egyéni nézeteket hozhat létre. Ez a cikk adatforrásnézet-tervezőből és létrehozásának és szerkesztésének egyéni nézetek eljárások áttekintését tartalmazza.

Az adatforrásnézet-tervezőből elérhető további cikkeit a következők:

* [Hivatkozás csempe](log-analytics-view-designer-tiles.md) -hivatkozás a beállítások az egyes a csempék, hogy az egyéni nézetek használható.
* [A képi megjelenítés része hivatkozás](log-analytics-view-designer-parts.md) -hivatkozás a beállítások az egyes a csempék, hogy az egyéni nézetek használható.

>[!NOTE]
> Ha a munkaterületet lett frissítve a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), akkor minden nézetben lekérdezések úgy kell megírni, a [új lekérdezési nyelv](https://go.microsoft.com/fwlink/?linkid=856078).  A munkaterület verziófrissítése előtt készült nézetekkel lesz automtically alakítja át.

## <a name="concepts"></a>Alapelvek
Nézetek jelennek meg a **áttekintése** lap a Naplóelemzési munkaterület az Azure portálon.  Minden egyéni nézet csempe jelenik betűrendben a csempéket a megoldások telepítve ugyanazon a munkaterületen.

![– Áttekintés oldalra](media/log-analytics-view-designer/overview-page.png)

A nézet-tervezővel létrehozott nézetek a következő táblázatban elemeket tartalmazzák.

| Rész | Leírás |
|:--- |:--- |
| Csempe |Megjelenik a Naplóelemzési munkaterület áttekintő lapja.  Tartalmazza az egyéni nézet szereplő információk visual összefoglalójához.  Különböző csempe a rekordok különböző képi adja meg.  Kattintson a Csempére kattintva nyissa meg az egyéni nézet a. |
| Egyéni nézet |Jelenik meg, ha a felhasználó a Csempére kattint.  Egy vagy több képi megjelenítés részeket tartalmazza. |
| A képi megjelenítés részei |Egy vagy több alapuló képi megjelenítés adatainak a Naplóelemzési munkaterület [keresések jelentkezzen](log-analytics-log-searches.md).  A legtöbb részei magas szintű képi megjelenítés biztosító fejléc és a legfelső szintű eredmények listáját tartalmazza.  Másik része típusok különböző képi a Naplóelemzési munkaterület a rekordok adja meg.  Kattintson az elemeket biztosít a részletes rekordok napló keresés végrehajtásához részében. |


## <a name="work-with-an-existing-view"></a>Egy meglévő nézethez használata
Az adatforrásnézet-tervezőből egy nézet megnyitásakor az alábbi táblázatban a beállítások menü lesz.

![Áttekintés menü](media/log-analytics-view-designer/overview-menu.png)


| Beállítás | Leírás |
|:--|:--|
| Frissítés   | Frissítse a nézetet a legújabb adatokkal. | 
| Elemzés | Nyissa meg a [Advanced Analytics portál](log-analytics-log-search-portals.md#advanced-analytics-portal) napló keresések adatok elemzésére. () log-Analytics-log-Search-Portals.md#Advanced-Analytics-Portal). |
| Szűrés    | A nézetben szereplő adatok idő szűrőt beállítani. |
| Szerkesztés      | Nyissa meg a nézet az adatforrásnézet-tervezőből annak tartalmát és a konfiguráció szerkesztése.   |
| Klónozás     | Hozzon létre egy új nézetet, és nyissa meg az adatforrásnézet-tervezőből.  Az új nézet egyező nevű az eredeti "másolható" hozzáfűzött a végéig. |


## <a name="create-a-new-view"></a>Új nézet létrehozása
Az új nézet létrehozása az **adatforrásnézet-tervezőből** Az adatforrásnézet-tervezőből csempe az Azure-portálon a Naplóelemzési munkaterület áttekintése lapon kattintva.

![Tervező csempéje](media/log-analytics-view-designer/view-designer-tile.png)


## <a name="working-with-view-designer"></a>Az adatforrásnézet-tervezőből használata
Fog dolgozni adatforrásnézet-tervezőből, akár új nézet létrehozása, akár egy meglévőt.  

Az adatforrásnézet-tervezőből három ablaktáblák rendelkezik.  A **tervezési** ablaktáblán az egyéni nézetet tartalmaz, amely éppen létrehozását és szerkesztését.  Csempék és részeit adja hozzá a **vezérlő** ablaktábla a **tervezési** ablaktáblán.  A **tulajdonságok** ablaktábla a csempe vagy a kijelölt tulajdonságait.

![Nézettervező](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>Konfigurálja a csempéje
Egyéni nézet csak egyetlen csempe rendelkezhet.  Válassza ki a **csempe** lapra a **vezérlő** ablaktáblában az aktuális csempe megtekintéséhez, vagy válasszon másik kódot.  A **tulajdonságok** ablaktábla az aktuális csempe tulajdonságait.  A részletes információk szerint a csempe tulajdonságainak konfigurálása a [csempe hivatkozás](log-analytics-view-designer-tiles.md) kattintson **alkalmaz** menti a módosításokat.

### <a name="configure-visualization-parts"></a>A képi megjelenítés részek konfigurálása
A nézet a képi megjelenítés részek tetszőleges számú tartalmazhatnak.  Válassza ki a **nézet** fülre, majd a képi megjelenítés része a nézethez történő hozzáadáshoz.  A **tulajdonságok** ablaktábla a kijelölt kijelző tulajdonságait.  A részletes információk szerint a nézet tulajdonságainak konfigurálása a [képi megjelenítés része hivatkozás](log-analytics-view-designer-parts.md) kattintson **alkalmaz** menti a módosításokat.

Nézetek csak a képi megjelenítés részek több sorban is rendelkezik.  Átrendezését meglévő alkatrész nézetben kattintással és húzással őket egy új helyre.

Eltávolíthatja a képi megjelenítés része a nézet kattintva a **X** gombra a kijelző jobb felső sarokban.


### <a name="menu-options"></a>A beállítások menü
Amikor egy nézet szerkesztési módban dolgozik, lehetősége van a menü a következő táblázatban.

![A Szerkesztés menü](media/log-analytics-view-designer/edit-menu.png)

| Beállítás | Leírás |
|:--|:--|
| Mentés        | A módosítások mentéséhez, és zárja be a nézetben. |
| Mégse      | A módosítások elvetéséhez, és zárja be a nézetben. |
| Nézet törlése | Törli a nézetet. |
| Exportálás      | A nézet exportálása egy [Resource Manager-sablon](../azure-resource-manager/resource-group-authoring-templates.md) , amelyet importálhat egy másik munkaterületre.  A fájl neve lesz a nevét, a nézet a kiterjesztésű *omsview*. |
| Importálás      | Importálás egy *omsview* fájlt, amely egy másik munkaterület-ból exportált.  Ezzel a művelettel felülírja a meglévő nézetek konfigurációs. |
| Klónozás       | Hozzon létre egy új nézetet, és nyissa meg az adatforrásnézet-tervezőből.  Az új nézet egyező nevű az eredeti "másolható" hozzáfűzött a végéig. |
| Közzététel     | A nézet szúrhatók be, egy JSON-fájlba exportálása egy [Mangagement megoldás](../operations-management-suite/operations-management-suite-solutions-resources-views.md).  A fájl neve lesz a nevét, a nézet a kiterjesztésű *json*. Egy második fájl jön létre a kiterjesztésű *resjson* , amely a JSON-fájlban meghatározott erőforrások értékeket tartalmaz.

## <a name="next-steps"></a>További lépések
* Adja hozzá [Csempék](log-analytics-view-designer-tiles.md) a egyéni nézetben.
* Adja hozzá [képi megjelenítés részek](log-analytics-view-designer-parts.md) a egyéni nézetben.
