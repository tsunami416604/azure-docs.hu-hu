---
title: A vadászati könyvjelzők használata adatvizsgálatokhoz az Azure Sentinel szolgáltatásban
description: Ez a cikk azt ismerteti, hogyan használható az Azure Sentinel vadászati könyvjelzői az adatnyomon követéshez.
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
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588688"
---
# <a name="keep-track-of-data-during-hunting-with-azure-sentinel"></a>A vadászat során az Azure Sentinel használatával nyomon követheti az adatgyűjtést

A fenyegetések vadászata jellemzően a kártékony viselkedés bizonyítékait keresi a naplózott adatok hegyeinek áttekintéséhez. Ezen eljárás során a nyomozók a lehetséges hipotézisek érvényesítése és a kompromisszumok teljes történetének megismerése során megkeresik azokat az eseményeket, amelyeket érdemes megjegyezni, újra felkeresni és elemezni.

Az Azure Sentinelben elérhető vadászati könyvjelzők segítségével elvégezheti az **Azure Sentinel-logs-naplókban**futtatott lekérdezések megőrzését, valamint a szükségesnek ítélt lekérdezési eredményeket. Feljegyzések és címkék hozzáadásával rögzítheti a kontextusbeli megjegyzéseket, és hivatkozhat az eredményekre. A könyvjelzővel ellátott adatelemek az egyszerű együttműködés érdekében láthatók Önnek és a csapattársaik számára.

A beérkező könyvjelzők adatait bármikor újra felkeresheti a **vadászat** ablaktábla **könyvjelzők** lapján. A szűrési és a keresési beállítások használatával gyorsan megtalálhatja az aktuális vizsgálathoz tartozó konkrét adatait. Azt is megteheti, hogy megtekinti a könyvjelzővel ellátott adatait közvetlenül a Log Analytics munkaterület **HuntingBookmark** táblájában. Például:

> [!div class="mx-imgBorder"]
> ![HuntingBookmark-tábla megtekintése](./media/bookmarks/bookmark-table.png)

A könyvjelzők táblázatból való megtekintése lehetővé teszi a könyvjelzővel ellátott adatok szűrését, összegzését és más adatforrásokkal való csatlakoztatását, így könnyen megkeresheti a egybehangzó-bizonyítékokat.

Jelenleg előzetes verzióban érhető el, ha olyan dolgot talál, amelyet sürgősen meg kell oldani a naplók vadászata közben, néhány kattintással létrehozhat egy könyvjelzőt, és előléptetheti azt egy incidensbe, vagy hozzáadhatja a könyvjelzőt egy meglévő incidenshez. További információ az incidensekről [: oktatóanyag: incidensek vizsgálata az Azure Sentinel szolgáltatással](tutorial-investigate-cases.md). 

Az előzetes verzióban is megjelenítheti a könyvjelzővel ellátott adatokat a könyvjelző részletei közül a **vizsgálat** gombra kattintva. Ez elindítja a vizsgálati folyamatot, amelyben megtekintheti, megvizsgálhatja és vizuálisan továbbíthatja az eredményeket egy interaktív entitás – gráf diagram és idősor használatával.

## <a name="add-a-bookmark"></a>Könyvjelző hozzáadása

1. A Azure Portalban navigáljon a **Sentinel** > **veszélyforrások kezelése** > **vadászat** a gyanús és rendellenes viselkedésre vonatkozó lekérdezések futtatásához.

2. Válassza ki az egyik vadászati lekérdezést, és a jobb oldalon, a vadászati lekérdezés részleteiben válassza a **lekérdezés futtatása**lehetőséget. 

3. Válassza a **lekérdezés eredményeinek megtekintése**lehetőséget. Például:
    
    > [!div class="mx-imgBorder"]
    > ![az Azure Sentinel Hunting lekérdezési eredményeinek megtekintése](./media/bookmarks/new-processes-observed-example.png)
    
    Ez a művelet megnyitja a lekérdezési eredményeket a **naplók** ablaktáblán.

4. A naplók lekérdezésének eredményei listán a jelölőnégyzetek használatával jelöljön ki egy vagy több olyan sort, amely az érdekesnek talált információkat tartalmazza.

5. Válassza a **Könyvjelző hozzáadása**lehetőséget:
    
    > [!div class="mx-imgBorder"]
    > ![a vadászati könyvjelző hozzáadása a lekérdezéshez](./media/bookmarks/add-hunting-bookmark.png)

6. A jobb oldalon a **Könyvjelző hozzáadása** panelen igény szerint frissítheti a könyvjelző nevét, címkékkel és megjegyzésekkel láthatja el, hogy milyen érdekes az elem.

7. A **lekérdezési adatok** szakaszban a legördülő lista segítségével kinyerheti az adatokat a **fiók**, a **gazdagép**és az **IP-cím** típusú entitások lekérdezési eredményeiből. Ez a művelet leképezi a kiválasztott entitás típusát egy adott oszlopra a lekérdezési eredményből. Például:
    
    > [!div class="mx-imgBorder"]
    > ![leképezheti az entitások típusait a vadászati könyvjelzők](./media/bookmarks/map-entity-types-bookmark.png)
    
    Ha meg szeretné tekinteni a könyvjelzőt a vizsgálati gráfban (jelenleg előzetes verzióban), legalább egy olyan entitást le kell képeznie, amelyik a **fiók**, a **gazdagép**vagy az **IP-cím**. 

5. A módosítások elvégzéséhez és a könyvjelző hozzáadásához kattintson a **Mentés** gombra. Minden könyvjelzővel ellátott adattal megosztanak más nyomozókat, és ez az első lépés az együttműködési vizsgálat során.

 
> [!NOTE]
> A napló lekérdezési eredményei támogatják a könyvjelzőket, valahányszor megnyitják ezt a panelt az Azure Sentinelből. Kiválaszthatja például az **általános** > **naplókat** a navigációs sávon, kiválaszthatja az események hivatkozásait a vizsgálatok gráfban, vagy kiválaszthat egy riasztási azonosítót az incidens részletes adatai közül (jelenleg előzetes verzióban érhető el). Nem hozhat létre könyvjelzőket, ha a **naplók** panel más helyekről nyílik meg, például közvetlenül a Azure monitorból.

## <a name="view-and-update-bookmarks"></a>Könyvjelzők megtekintése és frissítése 

1. A Azure Portal navigáljon a **Sentinel** > **veszélyforrások kezelése** > **vadászathoz**. 

2. A könyvjelzők listájának megtekintéséhez kattintson a **könyvjelzők** fülre.

3. Ha segítségre van szüksége egy adott könyvjelző megkereséséhez, használja a keresőmező vagy a szűrő beállításait.

4. Válassza az egyéni könyvjelzők lehetőséget, és a jobb oldali részletek panelen tekintse meg a könyvjelző részleteit.

5. Szükség szerint végezze el a módosításokat, amelyek automatikusan mentve lesznek.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>A könyvjelzők feltárása a vizsgálati gráfban

> [!IMPORTANT]
> A vizsgálati gráfban található könyvjelzők feltárása és maga a vizsgálati gráf jelenleg nyilvános előzetes verzióban érhető el.
> Ezeket a szolgáltatásokat szolgáltatói szerződés nélkül biztosítjuk, és éles számítási feladatokhoz nem ajánlott.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. A Azure Portal navigáljon a **Sentinel** > **fenyegetések kezelése** > a **vadászat** > **könyvjelzők** lapra, és válassza ki a vizsgálni kívánt könyvjelzőt vagy könyvjelzőket.

2. A könyvjelző részletei területen győződjön meg arról, hogy legalább egy entitás le van képezve. Az **entitások**esetében például az **IP-cím**, a **gép**vagy a **fiók**bejegyzései jelennek meg.

3. Kattintson **a vizsgálat gombra a** könyvjelző a vizsgálati gráfban való megtekintéséhez.

A vizsgálati gráf használatára vonatkozó utasításokért tekintse meg a részletes elemzés [a vizsgálati gráf használatával](tutorial-investigate-cases.md#use-the-investigation-graph-to-deep-dive)című témakört.

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Könyvjelzők hozzáadása egy új vagy meglévő incidenshez

> [!IMPORTANT]
> A könyvjelzők új vagy meglévő incidenshez való hozzáadása jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

1. A Azure Portal navigáljon a **Sentinel** > **veszélyforrások kezelése** > a **vadászat** > **könyvjelzők** lapra, és válassza ki az incidenshez hozzáadni kívánt könyvjelzőt vagy könyvjelzőket.

2. Válassza az **incidens műveletek (előzetes verzió)** lehetőséget a parancssáv:
    
    > [!div class="mx-imgBorder"]
    > ![könyvjelzők hozzáadása az incidenshez](./media/bookmarks/incident-actions.png)

3. Szükség szerint válassza az **új incidens létrehozása** vagy **a meglévő incidenshez való hozzáadás**lehetőséget. Ezután:
    
    - Új incidens esetén: igény szerint frissítheti az incidens részleteit, majd válassza a **Létrehozás**lehetőséget.
    - Könyvjelzők meglévő incidenshez való hozzáadásához: válasszon ki egy incidenst, majd válassza a **Hozzáadás**lehetőséget. 

Az incidensen belüli könyvjelző megtekintéséhez navigáljon a **Sentinel** > **veszélyforrások kezelése** > **incidensek** elemre, és válassza ki az incidenst a könyvjelzővel. Válassza a **teljes részletek megtekintése**lehetőséget, majd kattintson a **könyvjelzők** fülre.

> [!TIP]
> A parancssáv **incidensek (előzetes verzió)** lehetőségének alternatívájaként a menüsávon a helyi menü ( **..** .) segítségével választhatja ki az **új incidens létrehozására**, **a meglévő incidenshez való hozzáadására**és **az incidensből való eltávolításra**vonatkozó beállításokat. 

## <a name="view-bookmarked-data-in-logs"></a>Könyvjelzővel ellátott adatnaplók megtekintése

A könyvjelzővel ellátott lekérdezések, eredmények vagy előzmények megtekintéséhez jelölje ki a könyvjelzőt a **vadászat** > **könyvjelzők** lapról, és használja a részleteket tartalmazó ablaktáblán található hivatkozásokat: 

- A forrás **lekérdezés megtekintése** a forrás lekérdezés megtekintéséhez a **naplók** ablaktáblán.

- A **könyvjelzők naplóiban** megtekintheti az összes könyvjelző-metaadatot, beleértve a frissítést, a frissített értékeket és a frissítés időpontját.

Megtekintheti az összes könyvjelzőhöz tartozó nyers könyvjelzők adatait is, ha a > **könyvjelzők** **lapon a parancssáv** könyvjelzői közül kiválasztja a **kurzort** :

> [!div class="mx-imgBorder"]
> ![könyvjelző-naplók](./media/bookmarks/bookmark-logs.png)

Ez a nézet megjeleníti a társított metaadatokkal rendelkező összes könyvjelzőt. A [Kulcsszóválasztó lekérdezési nyelv](https://docs.microsoft.com/sharepoint/dev/general-development/keyword-query-language-kql-syntax-reference) (KQL) lekérdezésekkel leszűrheti a keresett könyvjelző legújabb verzióját.

> [!NOTE]
> A könyvjelzők létrehozása és a **könyvjelzők** lapon megjelenő idő között jelentős késleltetés (percben mérve) lehet.

## <a name="delete-a-bookmark"></a>Könyvjelző törlése
 
1.  A Azure Portal navigáljon a **Sentinel** > **veszélyforrások kezelése** > a **vadászat** > **könyvjelzők** lapra, és válassza ki a törölni kívánt könyvjelzőt vagy könyvjelzőket. 

2. Kattintson a jobb gombbal a kiválasztott elemekre, és válassza a könyvjelző vagy könyvjelzők törlésének lehetőségét. Ha például csak egy könyvjelzőt jelölt ki, törölje a **könyvjelzőt** , és ha két könyvjelzőt jelölt ki, **törölheti a 2** könyvjelzőt.
    
A könyvjelző törlése eltávolítja a könyvjelzőt a **Könyvjelző** lapon lévő listából. A Log Analytics munkaterület **HuntingBookmark** táblázata továbbra is az előző könyvjelző-bejegyzéseket fogja tartalmazni, a legújabb bejegyzés azonban igaz értékre módosítja a **SoftDelete** értéket, így a régi könyvjelzők kiszűrhetők. A könyvjelzők törlése nem távolítja el a más könyvjelzőhöz vagy riasztásokhoz társított vizsgálati élményből származó entitásokat. 


## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan futtathat egy vadászati vizsgálatot a könyvjelzők használatával az Azure Sentinelben. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:


- [Proaktív vadászat a fenyegetések ellen](hunting.md)
- [Automatikus vadászati kampányok futtatása jegyzetfüzetek használatával](notebooks.md)
