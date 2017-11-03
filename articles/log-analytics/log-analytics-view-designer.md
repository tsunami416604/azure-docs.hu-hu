---
title: "Adatok elemzése az OMS szolgáltatáshoz nézetek létrehozása |} Microsoft Docs"
description: "A Naplóelemzési adatforrásnézet-tervezőből lehetővé teszi az OMS és az Azure portálon megjelenő egyéni nézetek létrehozása, és az OMS-tárházban található adatok különböző képi megjelenítéseket tartalmaz. Ez a cikk adatforrásnézet-tervezőből és létrehozásának és szerkesztésének egyéni nézetek eljárások áttekintését tartalmazza."
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
ms.date: 07/17/2017
ms.author: bwren
ms.openlocfilehash: e3c463d749dc4179df58286b9bb75584880a6bc6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-view-designer-to-create-custom-views-in-log-analytics"></a>Adatforrásnézet-tervezőből segítségével egyéni nézeteket hozhat létre a Naplóelemzési
Az adatforrásnézet-tervezőből [Naplóelemzési](log-analytics-overview.md) segítségével egyéni nézeteket hozhat létre, amelyek tartalmazzák az OMS-tárházban található adatok különböző képi OMS-konzolon. Ez a cikk adatforrásnézet-tervezőből és létrehozásának és szerkesztésének egyéni nézetek eljárások áttekintését tartalmazza.

Az adatforrásnézet-tervezőből elérhető további cikkeit a következők:

* [Hivatkozás csempe](log-analytics-view-designer-tiles.md) -hivatkozás a beállítások az egyes a csempék, hogy az egyéni nézetek használható.
* [A képi megjelenítés része hivatkozás](log-analytics-view-designer-parts.md) -hivatkozás a beállítások az egyes a csempék, hogy az egyéni nézetek használható.

>[!NOTE]
> Ha a munkaterületet lett frissítve a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), akkor minden nézetben lekérdezések úgy kell megírni, a [új lekérdezési nyelv](https://go.microsoft.com/fwlink/?linkid=856078).  A munkaterület verziófrissítése előtt készült nézetekkel lesz automtically alakítja át.

## <a name="concepts"></a>Alapelvek
A nézet-tervezővel létrehozott nézetek a következő táblázatban elemeket tartalmazzák.

| Rész | Leírás |
|:--- |:--- |
| Mozaik elrendezés |Megjelenik a fő naplózási Analytics áttekintő irányítópulthoz.  Tartalmazza az egyéni nézet szereplő információk visual összefoglalójához.  Különböző csempe adjon meg másik képi megjelenítések bejegyzések az OMS-tárházban.  Kattintson a Csempére kattintva nyissa meg az egyéni nézet a. |
| Egyéni nézet |Jelenik meg, ha a felhasználó a Csempére kattint.  Egy vagy több képi megjelenítés részeket tartalmazza. |
| A képi megjelenítés részei |Egy vagy több alapuló képi megjelenítés adatainak az OMS-tárházban [keresések jelentkezzen](log-analytics-log-searches.md).  A legtöbb részei magas szintű képi megjelenítés biztosító fejléc és a legfelső szintű eredmények listáját tartalmazza.  Másik része típusok különböző képi bejegyzések az OMS-tárházban adja meg.  Kattintson az elemeket biztosít a részletes rekordok napló keresés végrehajtásához részében. |

![Tervező áttekintése](media/log-analytics-view-designer/overview.png)

## <a name="add-view-designer-to-your-workspace"></a>A munkaterület adatforrásnézet-tervezőből hozzáadása
Míg adatforrásnézet-tervezőből Preview, hozzá kell adnia azt a munkaterület kiválasztásával **előzetes verziójú funkciók** a a **beállítások** szakasza az OMS-portálon.

![Minta engedélyezése](media/log-analytics-view-designer/preview.png)

## <a name="creating-and-editing-views"></a>Létrehozásának és szerkesztésének nézetek
### <a name="create-a-new-view"></a>Új nézet létrehozása
Az új nézet megnyitásához a **adatforrásnézet-tervezőből** a fő OMS irányítópultján adatforrásnézet-tervezőből csempére kattintva.

![Tervező csempéje](media/log-analytics-view-designer/view-designer-tile.png)

### <a name="edit-an-existing-view"></a>Egy meglévő nézet szerkesztése
Az adatforrásnézet-tervezőből a meglévő nézet szerkesztéséhez nyissa meg a nézet a fő OMS-irányítópulton a csempére kattintva.  Kattintson a **szerkesztése** gombra kattintva nyissa meg a nézet az adatforrásnézet-tervezőből.

![Nézet szerkesztése](media/log-analytics-view-designer/menu-edit.png)

### <a name="clone-an-existing-view"></a>Klónozza a meglévő nézet
Klónozni egy nézet, ha létrehoz egy új nézetet, és megnyitja azt a Az adatforrásnézet-tervezőből.  Az új nézetében meg fog jelenni a névvel, az eredeti "másolható" hozzáfűzött a végéig.  Klónozni egy nézet, a fő OMS-irányítópulton a csempére kattintva nyissa meg a meglévő nézetek.  Kattintson a **Klónozás** gombra kattintva nyissa meg a nézet az adatforrásnézet-tervezőből.

![Egy nézet klónozása](media/log-analytics-view-designer/edit-menu-clone.png)

### <a name="delete-an-existing-view"></a>Meglévő nézet törlése
Meglévő nézet törléséhez nyissa meg a nézet a fő OMS-irányítópulton a csempére kattintva.  Kattintson a **szerkesztése** gombra kattintva nyissa meg az adatforrásnézet-tervezőből a nézetet, és kattintson a **nézet törlése**.

![Nézet törlése](media/log-analytics-view-designer/edit-menu-delete.png)

### <a name="export-an-existing-view"></a>Egy meglévő nézethez exportálása
Egy nézet egy JSON-fájl, amely egy másik munkaterület importálja, illetve használja exportálhatja egy [Azure Resource Manager sablon](../azure-resource-manager/resource-group-authoring-templates.md).  Egy meglévő nézethez exportálásához nyissa meg a nézet a fő OMS-irányítópulton a csempére kattintva.  Kattintson a **exportálása** gombra a böngésző letöltési mappában található fájl létrehozásához.  A fájl neve lesz a nevét, a nézet a kiterjesztésű *omsview*.

![Nézet exportálása](media/log-analytics-view-designer/edit-menu-export.png)

### <a name="import-an-existing-view"></a>Egy meglévő nézethez importálása
Importálhatja egy *omsview* exportált egy másik felügyeleti csoportból.  Egy meglévő nézethez importálni, először létre kell hoznia egy új nézetet.  Kattintson a **importálási** gombra, majd válassza ki a *omsview* fájlt.  A konfigurációs fájlban a meglévő nézetek másolódnak.

![Nézet exportálása](media/log-analytics-view-designer/edit-menu-import.png)

## <a name="working-with-view-designer"></a>Az adatforrásnézet-tervezőből használata
Az adatforrásnézet-tervezőből három ablaktáblák rendelkezik.  A **tervezési** ablaktáblán az egyéni nézet jelöli.  Csempék és részeit hozzáadásakor a **vezérlő** ablaktábla a **tervezési** nézetbe kerül ablaktáblán.  A **tulajdonságok** ablaktábla a csempe vagy a kijelölt tulajdonságait.

![Nézettervező](media/log-analytics-view-designer/view-designer-screenshot.png)

### <a name="configure-view-tile"></a>Konfigurálja a csempéje
Egyéni nézet csak egyetlen csempe rendelkezhet.  Válassza ki a **csempe** lapra a **vezérlő** ablaktáblában az aktuális csempe megtekintéséhez, vagy válasszon másik kódot.  A **tulajdonságok** ablaktábla az aktuális csempe tulajdonságait.  A részletes információk szerint a csempe tulajdonságainak konfigurálása a [csempe hivatkozás](log-analytics-view-designer-tiles.md) kattintson **alkalmaz** menti a módosításokat.

### <a name="configure-visualization-parts"></a>A képi megjelenítés részek konfigurálása
A nézet a képi megjelenítés részek tetszőleges számú tartalmazhatnak.  Válassza ki a **nézet** fülre, majd a képi megjelenítés része a nézethez történő hozzáadáshoz.  A **tulajdonságok** ablaktábla a kijelölt kijelző tulajdonságait.  A részletes információk szerint a nézet tulajdonságainak konfigurálása a [képi megjelenítés része hivatkozás](log-analytics-view-designer-parts.md) kattintson **alkalmaz** menti a módosításokat.

### <a name="delete-a-visualization-part"></a>Törölje a képi megjelenítés részt
Eltávolíthatja a képi megjelenítés része a nézet kattintva a **X** gombra a kijelző jobb felső sarokban.

### <a name="rearrange-visualization-parts"></a>A képi megjelenítés részek átrendezése
Nézetek csak a képi megjelenítés részek több sorban is rendelkezik.  Átrendezését meglévő alkatrész nézetben kattintással és húzással őket egy új helyre.

## <a name="next-steps"></a>Következő lépések
* Adja hozzá [Csempék](log-analytics-view-designer-tiles.md) a egyéni nézetben.
* Adja hozzá [képi megjelenítés részek](log-analytics-view-designer-parts.md) a egyéni nézetben.
