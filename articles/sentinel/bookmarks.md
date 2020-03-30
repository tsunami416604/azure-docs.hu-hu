---
title: Vadászati könyvjelzők használata az Azure Sentinel adatvizsgálatához
description: Ez a cikk ismerteti, hogyan használhatja az Azure Sentinel vadászati könyvjelzők az adatok nyomon követésére.
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/24/2019
ms.openlocfilehash: eacc104bf7157732f91507f631bcda2cd166bfb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588688"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>Az Azure Sentinel segítségével nyomon követheti az adatokat vadászat közben

A fenyegetésvadászat hoz általában megköveteli a naplóadatok hegyeinek áttekintését, amelyek a rosszindulatú viselkedés bizonyítékait keresik. E folyamat során a nyomozók olyan eseményeket találnak, amelyeket emlékezni akarnak, újra meg akarnak látogatni és elemezni a lehetséges hipotézisek érvényesítése és a kompromisszum teljes történetének megértése részeként.

Az Azure Sentinelben lévő könyvjelzők levadászása segít ebben, megőrizve az Azure Sentinel – Naplók ban futtatott **lekérdezéseket,** valamint az Ön által relevánsnak ítélt lekérdezési eredményeket. A környezetfüggő megfigyeléseket jegyzetekkel és címkékkel is rögzítheti, és hivatkozhat az eredményekre. A könyvjelzővel ellátott adatok at ön és csapattársai számára is láthatóak az egyszerű együttműködés érdekében.

A könyvjelzővel ellátott adatokat bármikor újra meglátogathatja a **Vadászat** ablaktábla **Könyvjelzők** lapján. A szűrési és keresési lehetőségek segítségével gyorsan megtalálhatja az aktuális vizsgálat konkrét adatait. Másik lehetőségként megtekintheti a könyvjelzővel ellátott adatokat közvetlenül a **HuntingBookmark** táblában a Log Analytics-munkaterületen. Példa:

> [!div class="mx-imgBorder"]
> ![A HuntingBookmark tábla megtekintése](./media/bookmarks/bookmark-table.png)

A könyvjelzők táblaból való megtekintése lehetővé teszi a könyvjelzővel ellátott adatok más adatforrásokkal való szűrését, összegzését és illesztését, így könnyen kereshet megerősítő bizonyítékokat.

Jelenleg előzetes verzióban, ha talál valamit, amit sürgősen meg kell oldani, miközben a vadászat a naplók, egy pár kattintással, akkor létrehozhat egy könyvjelzőt, és elősegíti azt egy esemény, vagy adja hozzá a könyvjelzőt egy meglévő esemény. Az incidensekről további információt az [Oktatóanyag: Az Azure Sentinelincidensekkel kapcsolatos incidensek kivizsgálása](tutorial-investigate-cases.md)című témakörben talál. 

Szintén előnézetben, akkor láthatóvá könyvjelzővel kapcsolatos adatokat, kattintson a **Vizsgálat** a könyvjelző adatait. Ez elindítja a vizsgálati élményt, amelyben megtekintheti, kivizsgálhatja és vizuálisan kommunikálhatja az eredményeket egy interaktív entitás-diagram és idővonal segítségével.

## <a name="add-a-bookmark"></a>Könyvjelző hozzáadása

1. Az Azure Portalon keresse meg a **Sentinel** > **fenyegetéskezelés-kezelés** > **vadászat** lekérdezések futtatásához a gyanús és rendellenes viselkedést.

2. Válassza ki a vadászati lekérdezések egyikét, és a jobb oldalon, a vadászati lekérdezés részletei ben válassza a **Lekérdezés futtatása**lehetőséget. 

3. Válassza **a Lekérdezés eredményeinek megtekintése**lehetőséget. Példa:
    
    > [!div class="mx-imgBorder"]
    > ![az Azure Sentinel-vadászat lekérdezési eredményeinek megtekintése](./media/bookmarks/new-processes-observed-example.png)
    
    Ez a művelet megnyitja a lekérdezés eredményeit a **Naplók** ablaktáblán.

4. A naplólekérdezés eredménylistájában a jelölőnégyzetekkel jelöljön ki egy vagy több olyan sort, amely az érdekesnek talált információkat tartalmazza.

5. Válassza **a Könyvjelző hozzáadása**lehetőséget:
    
    > [!div class="mx-imgBorder"]
    > ![Vadászati könyvjelző hozzáadása a lekérdezéshez](./media/bookmarks/add-hunting-bookmark.png)

6. A jobb oldalon, a **Könyvjelző hozzáadása** ablaktáblán szükség esetén frissítsd a könyvjelző nevét, adj hozzá címkéket és jegyzeteket, hogy könnyebben azonosíthasd, mi volt érdekes az elemben.

7. A **Lekérdezésadatai csoportban** a legördülő mezők segítségével nyerjen ki információkat a **Partner**, **Állomás**és **IP-cím** entitástípusok lekérdezési eredményeiből. Ez a művelet a kiválasztott entitástípust egy adott oszlophoz rendeli a lekérdezés eredményéből. Példa:
    
    > [!div class="mx-imgBorder"]
    > ![Entitástípusok leképezése vadászkönyvjelzőhöz](./media/bookmarks/map-entity-types-bookmark.png)
    
    A könyvjelző nek a vizsgálati grafikonon való megtekintéséhez (jelenleg előzetes verzióban) legalább egy entitástípust le kell képeznie, amely **fiók**, **állomás**vagy **IP-cím.** 

5. A módosítások véglegesítéséhez és a könyvjelző hozzáadásához kattintson a **Mentés** gombra. Minden könyvjelzővel ellátott adatot megosztunk más nyomozókkal, és ez az első lépés az együttműködésen alapuló vizsgálati élmény felé.

 
> [!NOTE]
> A naplólekérdezés eredményei támogatják a könyvjelzőket, amikor ez az ablaktábla meg nyílik az Azure Sentinelből. Például válassza az **Általános** > **naplók** lehetőséget a navigációs sávon, válassza ki az eseményhivatkozásokat a vizsgálati grafikonon, vagy válasszon egy riasztási azonosítót egy incidens teljes részleteiből (jelenleg előzetes verzióban). Nem hozhat létre könyvjelzőket, ha a **Naplók** ablaktábla más helyekről, például közvetlenül az Azure Monitorból nyílik meg.

## <a name="view-and-update-bookmarks"></a>Könyvjelzők megtekintése és frissítése 

1. Az Azure Portalon keresse meg a **Sentinel** > **fenyegetéskezelési** > **vadászat .** 

2. A **könyvjelzők** listájának megtekintéséhez kattintson a Könyvjelzők fülre.

3. Egy adott könyvjelző megkereséséhez használja a keresőmezőt vagy a szűrőbeállításokat.

4. Jelölje ki az egyes könyvjelzőket, és tekintse meg a könyvjelző részleteit a jobb oldali részletek ablaktáblában.

5. Szükség szerint hajtsa végre a módosításokat, amelyeket a rendszer automatikusan ment.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Könyvjelzők feltárása a vizsgálati grafikonon

> [!IMPORTANT]
> A vizsgálati grafikonon és a vizsgálati grafikonon lévő könyvjelzők feltárása jelenleg nyilvános előzetes verzióban van.
> Ezek a szolgáltatások szolgáltatásszint-szerződés nélkül vannak biztosítva, és nem ajánlottak éles számítási feladatokhoz.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

1. Az Azure Portalon keresse meg a **Sentinel** > **fenyegetéskezelés** > **vadászati** > **könyvjelzői** lapot, és válassza ki a vizsgálni kívánt könyvjelzőt vagy könyvjelzőket.

2. A könyvjelző részleteiben győződjön meg arról, hogy legalább egy entitás le van képezve. Az **ENTITÁSOK**esetében például az **IP,** **a Gép**vagy a **Partner**bejegyzések jelennek meg.

3. Kattintson a **Vizsgálat** gombra a könyvjelző megtekintéséhez a vizsgálati grafikonon.

A vizsgálati grafikon használatára vonatkozó utasításokat [lásd: A vizsgálati grafikon használata mélymerüléshez.](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive)

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Könyvjelzők hozzáadása új vagy meglévő incidenshez

> [!IMPORTANT]
> Könyvjelzők hozzáadása egy új vagy meglévő incidenshez jelenleg nyilvános előzetes verzióban van.
> Ez a szolgáltatás szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

1. Az Azure Portalon keresse meg a **Sentinel** > **fenyegetéskezelés** > **vadászati** > **könyvjelzői** lapot, és válassza ki az incidenshez hozzáadni kívánt könyvjelzőt vagy könyvjelzőket.

2. Válassza **az Incidensműveletek (Előnézet)** lehetőséget a parancssávon:
    
    > [!div class="mx-imgBorder"]
    > ![Könyvjelzők hozzáadása az incidenshez](./media/bookmarks/incident-actions.png)

3. Válassza az **Új incidens létrehozása** vagy a **Hozzáadás a meglévő incidenshez**lehetőséget, ha szükséges. Ezután:
    
    - Új incidens esetén: Tetszés szerint frissítse az incidens részleteit, majd válassza a **Létrehozás gombot.**
    - Könyvjelző hozzáadása egy meglévő incidenshez: Jelöljön ki egy incidenst, majd kattintson **a Hozzáadás gombra.** 

Az incidensen belüli könyvjelző megtekintéséhez: Keresse meg a **Sentinel** > **fenyegetéskezelési** > **incidenseket,** és válassza ki a könyvjelzővel kapcsolatos incidenst. Válassza **a Részletek megtekintése**lehetőséget, majd a **Könyvjelzők** lapot.

> [!TIP]
> A parancssáv **Incidensműveletek (Előnézet)** beállításának alternatívájaként a helyi menüben (**...**) egy vagy több könyvjelzőt használhat az **Új incidens létrehozása**, A **hozzáadás a meglévő incidenshez**és az **Eltávolítás incidensből**lehetőség kiválasztásához. 

## <a name="view-bookmarked-data-in-logs"></a>Könyvjelzővel ellátott adatok megtekintése a naplókban

A könyvjelzővel ellátott lekérdezések, eredmények vagy azok előzményeinek megtekintéséhez jelölje ki a könyvjelzőt a **Vadászati** > **könyvjelzők** lapon, és használja a részleteket tartalmazó ablaktáblán található hivatkozásokat: 

- **A forráslekérdezés megtekintése** a forráslekérdezés megtekintéséhez a **Naplók** ablaktáblán.

- **A könyvjelzőnaplók megtekintésével** megtekintheti az összes könyvjelzőmetaadatot, beleértve a frissítést, a frissített értékeket és a frissítés bekövetkezésének idejét.

Az összes könyvjelző nyers könyvjelzőadatait úgy is megtekintheti, hogy a **Vadászati** > **könyvjelzők** lap parancssávján kiválasztja a **Könyvjelzőnaplók** lehetőséget:

> [!div class="mx-imgBorder"]
> ![Könyvjelzőnaplók](./media/bookmarks/bookmark-logs.png)

Ez a nézet az összes könyvjelzőt megjeleníti a társított metaadatokkal. [A Kulcsszólekérdezési nyelv](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) (KQL) lekérdezések segítségével a keresett könyvjelző legújabb verziójára szűrhet.

> [!NOTE]
> A könyvjelző létrehozása és a **Könyvjelzők** lapon való megjelenítése között jelentős késleltetés (percben mérve) lehet.

## <a name="delete-a-bookmark"></a>Könyvjelző törlése
 
1.  Az Azure Portalon keresse meg a **Sentinel** > **fenyegetéskezelés** > **vadászati** > **könyvjelzői** lapot, és válassza ki a törölni kívánt könyvjelzőt vagy könyvjelzőket. 

2. Kattintson a jobb gombbal a beállításokra, és válassza a könyvjelző vagy könyvjelzők törlésének lehetőségét. Ha például csak egy könyvjelzőt jelölt ki, akkor a **Könyvjelző törlése,** 2 **könyvjelző törlése** pedig ha két könyvjelzőt jelölt ki.
    
A könyvjelző törlésével eltávolítja a könyvjelzőt a **Könyvjelző** lap listájáról. A Log Analytics-munkaterület **HuntingBookmark** táblája továbbra is tartalmazza a korábbi könyvjelzőbejegyzéseket, de a legutóbbi bejegyzés a SoftDelete értéket igazértékre **módosítja,** így könnyen kiszűrheti a régi könyvjelzőket. A könyvjelzők törlése nem távolítja el a más könyvjelzőkkel vagy figyelmeztetésekkel társított entitásokat a vizsgálati élményből. 


## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta, hogyan futtathatja a vadászati vizsgálatot az Azure Sentinel ben könyvjelzők használatával. Ha többet szeretne megtudni az Azure Sentinelről, olvassa el az alábbi cikkeket:


- [Proaktív vadászat a fenyegetésekre](hunting.md)
- [Automatikus vadászati kampányok jegyzetfüzetei használata](notebooks.md)
