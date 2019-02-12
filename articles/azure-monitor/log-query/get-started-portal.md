---
title: Az Azure Monitor log analytics használatának első lépései |} A Microsoft Docs
description: Ez a cikk egy oktatóanyag, a Log Analytics szolgáltatást az Azure Portalon kell lekérdezéseket írni.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: bwren
ms.openlocfilehash: b9a5c78ff9d6c1e2c7194f5b92511e94dfafb058
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55990496"
---
# <a name="get-started-with-azure-monitor-log-analytics"></a>Az Azure Monitor log analytics használatának első lépései

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Ebben az oktatóanyagban megismerheti, hogyan Azure Monitor log analytics használatához az Azure Portalon az Azure Monitor log-lekérdezések írása lesz. Mely arról szól, hogyan való:

- Egyszerű lekérdezéseket írni
- A séma az adatok megismerése
- Szűrése, rendezése és csoportosíthatja az eredményeket
- Időtartomány alkalmazása
- Diagramok létrehozása
- Mentés és a lekérdezések betöltése
- Exportálás és lekérdezések megosztása


## <a name="meet-log-analytics"></a>A log analytics felel meg
A log analytics egy webes eszköz írása, és hajtsa végre az Azure Monitor log-lekérdezéseket a. Nyissa meg a kiválasztásával **naplók** az Azure figyelő menü. Egy új üres lekérdezés kezdődik.

![Kezdőlap](media/get-started-portal/homepage.png)



## <a name="basic-queries"></a>Alapszintű lekérdezéseket
Lekérdezések keresési feltételek, azonosíthatja a trendeket, mintáinak elemzése és sok más összefüggéseket az adatok alapján az használható. Kezdje egy egyszerű lekérdezést:

```Kusto
Event | search "error"
```

Ez a lekérdezés átvizsgálja a _esemény_ rekordok bármely vlastnost "error" kifejezést tartalmazó tábla.

Lekérdezések megkezdheti a következők egyikével táblanév vagy egy **keresési** parancsot. A fenti példában a táblanév előtaggal kezdődik _esemény_, amely megadja, hogy a lekérdezés hatókörét. A függőleges vonal (|) karakterrel elválasztó parancsok, így az elsőt a bemenetben a következő parancs kimenetét. Tetszőleges számú parancsokat adhat hozzá egyetlen lekérdezést.

Egy másik módja, hogy ugyanabból a lekérdezés írása a következő lesz:

```Kusto
search in (Event) "error"
```

Ebben a példában **keresési** hatókörét a _esemény_ table és az adott tábla összes rekordját keresett kifejezés "error".

## <a name="running-a-query"></a>A lekérdezés futtatása
A lekérdezés futtatásához kattintson a **futtatása** gombra vagy nyomja le **Shift + Enter**. Vegye figyelembe a következő adatokat, amelyek meghatározzák a kódot, amely akkor fut, és a visszaadott adatok:

- Sortörések: Egyetlen szünet lehetővé teszi, hogy világosabb a lekérdezést. Több sortörést külön azt fel.
- A kurzor: Vigye a kurzort a lekérdezés végrehajtásához, belül valahol. Az aktuális lekérdezési kódot kell, amíg nem talál egy üres sort minősül.
- Időtartomány - egy időtartománya _elmúlt 24 órában_ alapértelmezés szerint van beállítva. Egy másik tartományt használ, használja a időválasztó, vagy adjon hozzá egy kifejezett idő dátumtartomány-szűrőt ad a lekérdezéshez.


## <a name="understand-the-schema"></a>A séma bemutatása
A séma az gyűjteményét, vizuálisan logikai kategória szerint csoportosítva. A kategóriák számos olyan figyelési megoldások. A _LogManagement_ kategória tartalmazza a közös adatok, például a Windows és a Syslog események, teljesítményadatok és ügyfél szívveréseket.

![Séma](media/get-started-portal/schema.png)

Minden tábla adatok felépítéséről oszlopok különböző adattípusokkal, az oszlop neve melletti ikon jelzi. Például a _esemény_ a képernyőképen látható tábla oszlopai például _számítógép_ szöveg, azaz _EventCategory_ egy számot, azaz és  _TimeGenerated_ dátum/idő azaz.

## <a name="filter-the-results"></a>Az eredmények szűréséhez
Első lépésként előkészítés véglegesítése a _esemény_ tábla.

```Kusto
Event
```

Automatikusan log analytics által eredmények hatókörök:

- Időtartomány:  Alapértelmezés szerint lekérdezések pedig csak az utolsó 24 óra.
- Az eredmények száma: Eredmények korlátozva, legfeljebb 10 000 rekordot.

Ez a lekérdezés nagyon általános, és hasznos lehet a túl sok eredményt adja vissza. A táblázat elemei, vagy explicit módon hozzá egy szűrőt a lekérdezés az eredményeket szűrheti. Szűrés eredményei a táblázat elemei keresztül vonatkozik a meglévő eredményhalmaz, amíg magát a lekérdezést egy szűrőt egy új szűrt eredmények állítsa be, és ezért sikerült előállítani a pontosabb eredmények adja vissza.

### <a name="add-a-filter-to-the-query"></a>A lekérdezési szűrő hozzáadása
Az egyes rekordok balra nyíl van. Kattintson a nyílra kattintva nyissa meg a egy adott rekord részleteit.

Vigye a fenti oszlop nevét a "+" és "-" ikonok megjelenítése. Adjon hozzá egy szűrőt, amely ugyanazt az értéket csak rekordokat ad vissza, kattintson a "+" jelre. Kattintson a "-", zárja ki ezt az értéket a rekordokat, majd **futtassa** újra futtatni a lekérdezést.

![Lekérdezési szűrő hozzáadása](media/get-started-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>Szűrheti is őket a táblázat elemei
Most már koncentráljunk a egy súlyosságát események _hiba_. A megadott nevű oszlop _Error_. Görgessen jobbra, tekintse meg ezt az oszlopot kell.

Kattintson a szűrő ikon mellett az oszlop címét, és az előugró ablakban válassza ki, amely értékek _kezdődik_ szöveg _hiba_:

![Szűrés](media/get-started-portal/filter.png)


## <a name="sort-and-group-results"></a>Rendezés és csoportosítás eredmények
Az eredmények most vannak leszűkül, hogy csak az SQL Server, az elmúlt 24 órában létrehozott hibaesemények tartalmazza. Az eredmények azonban nem vannak rendezve bármilyen módon. Az eredmények például egy adott oszlop szerinti rendezéshez _időbélyeg_ például kattintson az oszlop címére. Egy kattintás növekvő sorrendben, míg a második kattintás csökkenő rendezhető.

![Rendezési oszlop](media/get-started-portal/sort-column.png)

Az eredmények rendezése egy másik úgy, hogy csoportokat. Az adott oszlop, egyszerűen húzzon át az oszlop fejlécére, az egyéb oszlopok fölött szerint csoportosíthatja az eredményeket. Alcsoportok, húzza többi oszlopot a felső sávon is.

![Csoportok](media/get-started-portal/groups.png)

## <a name="select-columns-to-display"></a>Válassza ki a megjelenítendő oszlopok
Eredmények táblázatában gyakran sok oszlopot tartalmaz. Előfordulhat, hogy a visszaadott oszlopok némelyike nem jelennek meg alapértelmezés szerint, vagy előfordulhat, hogy el kívánja távolítani egyes megjelenített oszlopokat. Válassza ki a megjelenítendő oszlopokat, a gombra kattintva oszlopok:

![Oszlopok kiválasztása](media/get-started-portal/select-columns.png)


## <a name="select-a-time-range"></a>Időtartomány megadása
Alapértelmezés szerint a log analytics alkalmazza a _elmúlt 24 órában_ időtartomány. Egy másik tartományt használja, válasszon ki egy másik értéket a időválasztó keresztül, és kattintson a **futtatása**. Az előre definiált értékek mellett használhatja a _egyéni időtartomány_ választhatja a lekérdezés egy abszolút címtartományt.

![Időpontválasztó](media/get-started-portal/time-picker.png)

Egyéni időtartomány kiválasztásakor a kiválasztott értékek (UTC), amely eltérhet a helyi időzóna.

Ha a lekérdezés kifejezetten egy szűrőt tartalmaz _TimeGenerated_választó cím jelennek meg az idő _beállítás a lekérdezésben_. Manuálisan válassza le lesz tiltva ütközés elkerülése érdekében.


## <a name="charts"></a>Diagramok
Mellett eredményt adnak vissza egy táblázat, lekérdezési eredmények vizuális formátumban is megjeleníthetők. Például használja a következő lekérdezést:

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

Alapértelmezés szerint egy táblázatban eredmények jelennek meg. Kattintson a _diagram_ kattintva megjelenítjük az eredményeket egy grafikus nézetben:

![Oszlopdiagram](media/get-started-portal/bar-chart.png)

Az eredményeket egy halmozott sávdiagram látható. Kattintson a _-ig halmozott oszlop_ válassza _torta_ az eredményeket egy másik nézetének megjelenítése:

![Tortadiagram](media/get-started-portal/pie-chart.png)

Különböző tulajdonságait a nézetben, például az x és y tengely, vagy a csoportosítás, és a beállítások felosztás manuálisan nem módosíthatók a vezérlősávon.

Is beállíthat az előnyben részesített view a lekérdezésben, a renderelési operátorral.

### <a name="smart-diagnostics"></a>Intelligens diagnosztika
A idődiagramját van egy hirtelen megugrás vagy a lépés az adatok jelenhet meg, egy kijelölt pont a sor. Ez azt jelzi, hogy _intelligens diagnosztika_ azonosította szűrje ki a hirtelen módosítás tulajdonságok kombinációja. Kattintson a pont, a szűrő a részletekért és szűrt verzió megtekintéséhez. Ez segíthet azonosítani, hogy mi okozta, hogy a módosítás:

![Intelligens diagnosztika](media/get-started-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>Rögzítés az irányítópulton
A diagram vagy a táblát az egyik a megosztott Azure-irányítópultok rögzítése, kattintson a rajzszög ikonra.

![Rögzítés az irányítópulton](media/get-started-portal/pin-dashboard.png)

Bizonyos egyszerűbb lépnek érvénybe a diagram rögzítése az irányítópultra:

- Tábla oszlopainak és sorainak: Annak érdekében, hogy az Irányítópulton egy táblázat rögzítéséhez, négy vagy kevesebb oszlopot kell rendelkeznie. Csak a felső hét sorok jelennek meg.
- Korlátozása: Lekérdezések pedig automatikusan csak az elmúlt 14 napban.
- Doboz száma korlátozás: Egy diagram, amely különálló bins sok jeleníti meg, ha kevesebb feltöltött bins automatikusan csoportosítva egyetlen _mások_ bin.

## <a name="save-queries"></a>Lekérdezések mentése
Miután létrehozott egy hasznos lekérdezés, érdemes menti vagy megosztja másokkal. A **mentése** ikonra a felső sávon található van.

Mentheti a teljes lapja, vagy egyetlen lekérdezés továbbítása. Functions olyan lekérdezések, amelyeket más lekérdezések által is lehet hivatkozni. Annak érdekében, hogy egy lekérdezés mentéséhez függvényében, meg kell adnia egy függvény aliasa, amely ezt a lekérdezést, amikor más lekérdezések által hivatkozott meghívásához használt név.

![Függvény mentése](media/get-started-portal/save-function.png)

Log analytics-lekérdezések mindig egy kijelölt munkaterületen menti, és a munkaterület más felhasználókkal megosztott.

## <a name="load-queries"></a>Lekérdezések betöltése
A lekérdezéskezelő ikonra a jobb felső területen van. Ez felsorolja az összes mentett lekérdezések kategória szerint. Lehetővé teszi kedvencként adott lekérdezések gyorsan megtalálhatja azokat a jövőben. Kattintson duplán egy korábban mentett lekérdezés az aktuális ablakban történő hozzáadáshoz.

![Lekérdezéskezelő](media/get-started-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>Exportálási és megosztási hivatkozásként
A log analytics több módszert is exportáló támogatja:

- Excel: Az eredmények mentése CSV-fájlként.
- Power BI: Exportálhatja az eredményeket a power bi-ban. Lásd: [importálása az Azure Monitor log-adatok Power BI-bA](../../azure-monitor/platform/powerbi.md) részleteiről.
- Hivatkozás megosztása: Magát a lekérdezést, amely majd küldhető és más, ugyanahhoz a munkaterülethez hozzáféréssel rendelkező felhasználók által végrehajtott hivatkozásként is megoszthatók.

## <a name="next-steps"></a>További lépések

- Tudjon meg többet [Azure Monitor log-lekérdezések írása](get-started-queries.md).
