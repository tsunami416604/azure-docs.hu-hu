---
title: Az Azure Monitor munkafüzet-vizualizációi
description: Ismerje meg az Azure Monitor munkafüzet-vizualizációk összetevőit, beleértve a szöveget, a diagramokat, a rácsokat, a fákat és a grafikonokat.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d05902f47dff3dd2f8a63ae240c0b8825a5c441f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658030"
---
# <a name="azure-monitor-workbook-visualizations"></a>Az Azure Monitor munkafüzet-vizualizációi

Az Azure Monitor-munkafüzetek számos különböző megjelenítési stílust támogatnak a jelentéskészítési igényeknek megfelelően. Ez a cikk példákat mutat be az egyes vizualizációs típusokra.

## <a name="text"></a>Szöveg

A munkafüzetek lehetővé teszik a szerzők számára, hogy szövegblokkokat foglaljanak bele a munkafüzeteikbe. A szöveg lehet a telemetria emberi elemzése, az adatok értelmezését segítő információk, szakaszfejlécek stb.

![Képernyőkép: Apdex szövegtábla](./media/workbooks-visualizations/apdex.png)

A szöveg a Markdown vezérlőn keresztül kerül hozzáadásra, amely teljes formázási vezérlést biztosít.

![Képernyőkép a renderelt táblát építő nyers jelölésről](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>Szövegvezérlő hozzáadása

1. Az eszköztárelemre kattintva átváltson **Edit** a munkafüzetet szerkesztési módra.
2. A **Szöveg hozzáadása** hivatkozással szövegvezérlőt adhat a munkafüzethez.
3. Markdown hozzáadása a vezérlőhöz.
4. A **formázott** szöveg megtekintéséhez kattintson a Kész szerkesztés gombra.

> [!TIP]
> Használja ezt [a Markdown cheat sheet,](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) hogy megtudjuk, a különböző formázási lehetőségeket.

## <a name="charts"></a>Diagramok

A munkafüzetek lehetővé teszik a figyelési adatok diagramként való bemutatását. A támogatott diagramtípusok közé tartoznak a vonal, a sáv, a sáv kategorikus, a terület, a pontdiagramok, a kör és az idő. A szerzők testre szabhatják a diagram magasságát, szélességét, színpalettáját, jelmagyarázatát, címeit, adatmentes üzenetét stb.

A munkafüzetek mind a naplók, mind a metrikaadatforrások diagramjait támogatják. 

### <a name="adding-a-log-chart"></a>Naplódiagram hozzáadása

1. Az eszköztárelemre kattintva átváltson **Edit** a munkafüzetet szerkesztési módra.
2. A **Lekérdezés hozzáadása** hivatkozással naplólekérdezés-vezérlőt adhat a munkafüzethez.
3. Válassza ki a lekérdezés típusát **naplóként,** erőforrástípusként (például Application Insights) és a megcélozandó erőforrásokként.
4. A Lekérdezés szerkesztővel megadhatja a [KQL-t](https://docs.microsoft.com/azure/kusto/query/) elemzésre (például a kérések trendje).
5. Állítsa a képi megjelenítést a következők egyikére: **Terület**, **Sáv**, **Sáv (kategorikus)**, **vonal,** **kör,** **scatter**vagy **idő**.
6. Szükség esetén állítson be más paramétereket is – például időtartományt, megjelenítést, méretet, színpalettát és jelmagyarázatot.

![A naplódiagram képernyőképe szerkesztési módban](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>Naplódiagram paraméterei

| Paraméter | Magyarázat | Példa |
| ------------- |:-------------|:-------------|
| `Query Type` | A használandó lekérdezés típusa | Napló, Azure Resource Graph, stb. |
| `Resource Type` | A megcélozandó erőforrástípus | Application Insights, Log Analytics vagy Azure-first |
| `Resources` | A metrikák értékének leértékeléséhez szükséges erőforrások | Samiapp1 |
| `Time Range` | A naplódiagram megtekintéséhez vezető időablak | Az elmúlt óra, az utolsó 24 óra, stb. |
| `Visualization` | A használandó vizualizáció | Terület, Bár, Vonal, Pite, Scatter, Idő, bár kategorikus |
| `Size` | A vezérlő függőleges mérete | Kicsi, közepes, nagy vagy teljes |
| `Color palette` | A diagramon használandó színpaletta. Figyelmen kívül hagyva több metrikus vagy szegmentált módban. | Kék, zöld, piros stb. |
| `Legend` | A jelmagyarázathoz használandó összesítési függvény | Értékek összege vagy átlaga vagy Max, Min, First, Utolsó érték |
| `Query` | Bármely KQL-lekérdezés, amely a diagram vizualizációja által várt formátumban ad vissza adatokat | _kérelmek \| make-series kérések = count() default = 0 az időbélyegen a ezelőtt(1d) és a now() 1h._ |

### <a name="adding-a-metric-chart"></a>Mérőszámdiagram hozzáadása

1. Az eszköztárelemre kattintva átváltson **Edit** a munkafüzetet szerkesztési módra.
2. A **Metrika hozzáadása** hivatkozással metrikavezérlőt adhat a munkafüzethez.
3. Válasszon ki egy erőforrástípust (például tárfiók), a megcélozandó erőforrásokat, a metrika névtaterét és nevét, valamint a használandó összesítést.
4. Szükség esetén állítson be más paramétereket is – például időtartományt, felosztást, megjelenítést, méretet és színpalettát.

![Képmutató a metrikus diagramról szerkesztési módban](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>Metrikadiagram paraméterei

| Paraméter | Magyarázat | Példa |
| ------------- |:-------------|:-------------|
| `Resource Type` | A megcélozandó erőforrástípus | Tároló vagy virtuális gép. |
| `Resources` | A metrikák értékének leértékeléséhez szükséges erőforrások | MyStorage1 |
| `Namespace` | A metrikával rendelkező névtér | Tárolási > Blob |
| `Metric` | A vizualizáláshoz | Blob >-tranzakciók tárolási > |
| `Aggregation` | A mérőszámra alkalmazandó összesítési függvény | Összeg, darabszám, átlag stb. |
| `Time Range` | A metrika megtekintéséhez a | Az elmúlt óra, az utolsó 24 óra, stb. |
| `Visualization` | A használandó vizualizáció | Terület, Bár, Vonal, Szóró, Rács |
| `Split By` | A metrika felosztása dimenzióra | Tranzakciók földrajzi típus szerint |
| `Size` | A vezérlő függőleges mérete | Kicsi, közepes vagy nagy |
| `Color palette` | A diagramon használandó színpaletta. A paraméter `Split by` használata esetén figyelmen kívül hagyva | Kék, zöld, piros stb. |

## <a name="grids"></a>Rácsok

A rácsok vagy táblák gyakori módjai az adatok felhasználóknak való bemutatásának. A munkafüzetek lehetővé teszik a felhasználók számára, hogy egyénileg stilírezzék a rács oszlopait, hogy gazdag felhasználói felületet biztosítsanak a jelentésekhez.

Az alábbi példa egy rácsot mutat be, amely ikonokat, hőtérképeket és szikrasávokat egyesít az összetett információk bemutatásához. A munkafüzet rendezést, keresőmezőt és egy "go-to-analytics" gombot is biztosít.

![Naplóalapú rács képernyőképe](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>Naplóalapú rács hozzáadása

1. Az eszköztárelemre kattintva átváltson **Edit** a munkafüzetet szerkesztési módra.
2. A **Lekérdezés hozzáadása** hivatkozással naplólekérdezés-vezérlőt adhat a munkafüzethez.
3. Válassza ki a lekérdezés típusát **naplóként,** erőforrástípusként (például Application Insights) és a megcélozandó erőforrásokként.
4. A Lekérdezés szerkesztővel adja meg a KQL-t elemzésre (például küszöbérték alatti memóriával rendelkező virtuális gépek)
5. A képi megjelenítés beállítása **Rácsra**
6. Szükség esetén állítson be más paramétereket is – például az időtartományt, a méretet, a színpalettát és a jelmagyarázatot.

![Képernyőkép a naplóalapú rácsos lekérdezésről](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>Csempék

A csempék nagyon hasznos lehetőséget jelentenek a munkafüzetek összesítő adatainak megjelenítésére. Az alábbi képen a csempék gyakori használati esete látható - alkalmazásszintű összegzés egy részletes rács tetején.

![Képernyőkép a csempe összegzési nézetéről](./media/workbooks-visualizations/tiles-summary.png)

A munkafüzetcsempék támogatják a cím, a felirat, a nagy szöveg, az ikonok, a metrikus alapú színátmenetek, a szikravonal/sávok, az élőláb stb.

### <a name="adding-a-tile"></a>Csempe hozzáadása

1. Az eszköztárelemre kattintva átváltson _Edit_ a munkafüzetet szerkesztési módra.
2. A **Lekérdezés hozzáadása** hivatkozással naplólekérdezés-vezérlőt adhat a munkafüzethez. 
3. Válassza ki a lekérdezés típusát **naplóként,** erőforrástípusként (például Application Insights) és a megcélozandó erőforrásokként.
4. A Lekérdezés szerkesztő vel adja meg a KQL-t elemzésre
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. A méret beállítása **teljesre**
6. A képi megjelenítés beállítása **Mozaikok beállításra**
7. A Beállítások ablaktábla megnyitásához kattintson a **Mozaikbeállítások** gombra
8. A **Mozaikmezők mezőben**állítsa be a következőt:
    * Cím:`name`
    * Balra: `Requests`, Renderer: `Big Number`, `Green to Red`Színpaletta: , Min érték:`0`
    * Alsó:`appName`
9. Kattintson a **Mentés és bezárás** gombra az ablaktábla alján.

![Képernyőkép a csempe összegzési nézetéről](./media/workbooks-visualizations/tile-settings.png)

Így fognak kinézni a csempék olvasási módban:

![Képernyőkép a csempe összegzési nézetéről](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Fák

A munkafüzetek farácsokon keresztül támogatják a hierarchikus nézeteket. A fák lehetővé teszik, hogy egyes sorok a következő szintre bővíthetők legyenek a részletezési élmény érdekében.

Az alábbi példa a tároló állapotának metrikákat (munkakészlet méretét) mutatja, amelyeket farácsként jelenítmeg. A legfelső szintű csomópontok itt az Azure Kubernetes-szolgáltatás (AKS) csomópontok, a következő szint a podok és a végső szint tárolók. Figyelje meg, hogy továbbra is formázhatja az oszlopokat, mint egy rácsban (hőtérkép, ikonok, hivatkozás). Az alapul szolgáló adatforrás ebben az esetben egy Log Analytics munkaterület AKS-naplók.

![Képernyőkép a csempe összegzési nézetéről](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>Farács hozzáadása
1. Az eszköztárelemre kattintva átváltson _Edit_ a munkafüzetet szerkesztési módra.
2. A **Lekérdezés hozzáadása** hivatkozással naplólekérdezés-vezérlőt adhat a munkafüzethez. 
3. Válassza ki a lekérdezés típusát **naplóként,** erőforrástípusként (például Application Insights) és a megcélozandó erőforrásokként.
4. A Lekérdezés szerkesztő vel adja meg a KQL-t elemzésre
    ```kusto
    requests
    | summarize Requests = count() by ParentId = appName, Id = name
    | extend Kind = 'Request', Name = strcat('🌐 ', Id)
    | union (requests
    | summarize Requests = count() by Id = appName
    | extend Kind = 'Request', ParentId = '', Name = strcat('📱 ', Id))
    | project Name, Kind, Requests, Id, ParentId
    | order by Requests desc
    ```
5. A képi megjelenítés beállítása **Rácsra**
6. Kattintson az **Oszlopbeállítások** gombra a Beállítások ablaktábla megnyitásához
7. Az alsó **fa/csoportosítás beállításai** szakaszban állítsa be a következőt:
    * Fa típusa:`Parent/Child`
    * Azonosító mező:`Id`
    * Szülőazonosító mező:`ParentId`
    * A kibontó megjelenítése:`Name`
    * Bontsa ki a fa legfelső szintjét:`checked`
8. A felső _Oszlopok_ szakaszban állítsa be a következőt:
    * _Id_ - Oszlopmegjelenítő:`Hidden`
    * _Szülőazonosító_ – Oszlopmegjelenítő:`Hidden`
    * _Kérések_ - Oszlopmegjelenítő: `Bar` `Blue`, Szín: , Minimális érték:`0`
9. Kattintson a _Mentés és bezárás_ gombra az ablaktábla alján.    

![Képernyőkép a csempe összegzési nézetéről](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>Fa beállításai

| Beállítás | Magyarázat |
|:------------- |:-------------|
| `Id Field` | A rács minden sorának egyedi azonosítója |
| `Parent Id Field` | Az aktuális sor szülőjének azonosítója |
| `Show the expander on` | Az az oszlop, amelyen a fabővítő megjelenik. Gyakori, hogy a farácsok elrejtik az azonosítóés a szülőazonosító mezőt, mert nem olvashatók. Ehelyett a bővítő egy olvashatóbb értékkel rendelkező mezőn jelenik meg - például az entitás neve |
| `Expand the top level of the tree` | Ha be van jelölve, a farács a legfelső szinten lesz kibontva. Akkor hasznos, ha alapértelmezés szerint további információkat szeretne megjeleníteni |

## <a name="graphs"></a>Grafikonok

A munkafüzetek támogatják tetszőleges grafikonok megjelenítését a naplókból származó adatok alapján, hogy megmutassák a figyelési entitások közötti kapcsolatokat.

Az alábbi grafikon a számítógép be- és kiáramlását mutatja különböző porton keresztül a külső számítógépekre/számítógépekről. Típus szerint színeződik (számítógép és port vs. külső IP), és a peremméretek a köztes adatok mennyiségének felelnek meg. Az alapul szolgáló adatok a Virtuálisgép-kapcsolatokat célzó KQL-lekérdezésből származnak.

![Képernyőkép a csempe összegzési nézetéről](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>Diagram hozzáadása
1. Az eszköztárelemre kattintva átváltson _Edit_ a munkafüzetet szerkesztési módra.
2. A **Lekérdezés hozzáadása** hivatkozással naplólekérdezés-vezérlőt adhat a munkafüzethez. 
3. Válassza ki a lekérdezés típusát **naplóként,** erőforrástípusként (például Application Insights) és a megcélozandó erőforrásokként.
4. A Lekérdezés szerkesztő vel adja meg a KQL-t elemzésre
    ```kusto
    let data = dependencies
    | summarize Calls = count() by App = appName, Request = operation_Name, Dependency = name
    | extend RequestId = strcat(App, '::', Request);
    let links = data
    | summarize Calls = sum(Calls) by App, RequestId
    | project SourceId = App, TargetId = RequestId, Calls, Kind = 'App -> Request'
    | union (data
        | project SourceId = RequestId, TargetId = Dependency, Calls, Kind = 'Request -> Dependency');
    let nodes = data
    | summarize Calls = sum(Calls) by App
    | project Id = App, Name = App, Calls, Kind = 'App'
    | union (data
        | summarize Calls = sum(Calls) by RequestId, Request
        | project Id = RequestId, Name = Request, Calls, Kind = 'Request')
    | union (data
        | summarize Calls = sum(Calls) by Dependency
        | project Id = Dependency, Name = Dependency, Calls, Kind = 'Dependency');
    nodes
    | union (links)
    ```
7. A képi megjelenítés beállítása **grafikonra**
8. Kattintson a **Diagrambeállítások** gombra a Beállítások ablaktábla megnyitásához
9. Az _Elrendezési mezők_ alján állítsa be a következőt:
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. A felső _csomópontformátum-beállítások_ mezőben állítsa be a következőt:
    * _Felső tartalom_- `Name`Oszlop használata: , Oszlopmegjelenítő:`Text`
    * _Tartalom középre igazítása_– Oszlop használata: `Calls`, Oszlopmegjelenítő: `Big Number`, Színpaletta:`None`
    * _Alsó tartalom_- `Kind`Oszlop használata: , Oszlopmegjelenítő:`Text`
10. Kattintson a _Mentés és bezárás_ gombra az ablaktábla alján.

![Képernyőkép a csempe összegzési nézetéről](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>További lépések

* Az Azure Resource Manager rel [telepítheti](workbooks-automate.md) a munkafüzeteket.
* [Szabályozhatja](workbooks-access-control.md) és megoszthatja a munkafüzet erőforrásaihoz való hozzáférést.
