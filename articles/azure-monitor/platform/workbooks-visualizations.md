---
title: Azure Monitor munkafüzetek vizualizációi
description: Tudjon meg többet a Azure Monitor munkafüzet vizualizációs összetevőiről, beleértve a szöveget, a diagramokat, a rácsokat, a fákat és a diagramokat.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: d05902f47dff3dd2f8a63ae240c0b8825a5c441f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658030"
---
# <a name="azure-monitor-workbook-visualizations"></a>Azure Monitor munkafüzetek vizualizációi

Azure Monitor munkafüzetek számos különböző vizualizációs stílust támogatnak a jelentéskészítési igények kielégítéséhez. Ez a cikk példákat tartalmaz az egyes vizualizációk típusára.

## <a name="text"></a>Szöveg

A munkafüzetek lehetővé teszik a szerzők számára, hogy szöveges blokkokat foglaljanak bele a munkafüzetekbe. A szöveg lehet a telemetria emberi elemzése, amely segítséget nyújt a felhasználóknak az adatok értelmezésében, a szakaszok fejlécében stb.

![Képernyőkép a Apdex](./media/workbooks-visualizations/apdex.png)

A szöveg egy Markdown-vezérlőn keresztül lett hozzáadva, amely teljes formázási vezérlést biztosít.

![Képernyőkép a megjelenített táblázatot felépítő nyers Markdown](./media/workbooks-visualizations/markdown.png)

### <a name="add-a-text-control"></a>Szövegbeviteli vezérlőelem hozzáadása

1. Az eszköztár **szerkesztése** elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. A **text (szöveg hozzáadása** ) hivatkozás használatával adjon hozzá egy szövegbeviteli vezérlőt a munkafüzethez.
3. Adja hozzá a Markdown a vezérlőhöz.
4. A formázott szöveg megjelenítéséhez kattintson a **kész szerkesztés** gombra.

> [!TIP]
> Ez a [Markdown-Cheat-adatlap](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet) a különböző formázási beállítások megismerésére használható.

## <a name="charts"></a>Diagramok

A munkafüzetek lehetővé teszik, hogy a figyelési adatgyűjtés diagramként legyen megjelenítve. A támogatott diagramtípusok közé tartozik a vonal, a sáv, a kategorikus, a terület, a pontdiagram, a torta és az idő. A szerzők dönthetnek úgy, hogy testre szabják a diagram magasságát, szélességét, színpalettáját, jelmagyarázatát, címeit, nem adatüzeneteit stb.

A munkafüzetek a naplók és a metrikus adatforrások diagramjait támogatják. 

### <a name="adding-a-log-chart"></a>Naplózási diagram hozzáadása

1. Az eszköztár **szerkesztése** elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. A **lekérdezés hozzáadása** hivatkozásra kattintva hozzáadhat egy log lekérdezési vezérlőt a munkafüzethez.
3. Válassza ki a lekérdezés típusát **naplóként**, erőforrástípusként (például Application Insights) és a célként megadott erőforrásokat.
4. A lekérdezéstervező segítségével adja meg az elemzés [KQL](https://docs.microsoft.com/azure/kusto/query/) (például a kérelmek trendje).
5. Állítsa a vizualizációt a következőre **: terület**, **sáv**, **sáv (kategorikus)**, **vonal**, torta **,** pont vagy **idő**. **Pie**
6. Szükség esetén más paramétereket is beállíthat, például a vizualizációt, a méretet, a színpalettát és a jelmagyarázatot.

![Képernyőfelvétel a diagram szerkesztési módjáról](./media/workbooks-visualizations/log-chart.png)

#### <a name="log-chart-parameters"></a>A naplózási diagram paraméterei

| Paraméter | Magyarázat | Példa |
| ------------- |:-------------|:-------------|
| `Query Type` | A használandó lekérdezés típusa | Napló, Azure Resource Graph stb. |
| `Resource Type` | A célként használandó erőforrás típusa | Application Insights, Log Analytics vagy Azure – első |
| `Resources` | Erőforrások készlete, amelyből beolvashatók a mérőszámok értéke | MyApp1 |
| `Time Range` | A naplózási diagram megtekintésének időablaka | Az elmúlt óra, az elmúlt 24 óra stb. |
| `Visualization` | A használni kívánt vizualizáció | Körzet, sáv, vonal, torta, Scatter, idő, Bar kategorikus |
| `Size` | A vezérlő függőleges mérete | Kicsi, közepes, nagy vagy teljes |
| `Color palette` | A diagramban használandó színpaletta Több metrikus vagy szegmentált módban figyelmen kívül hagyva. | Kék, zöld, piros stb. |
| `Legend` | A jelmagyarázathoz használandó aggregációs függvény | Értékek vagy maximum, minimum, első, utolsó érték összege vagy átlaga |
| `Query` | Bármely olyan KQL-lekérdezés, amely a diagram vizualizációja által várt formátumban adja vissza az adatok értékét | _a kérelmek \| sorozatos kérelmeket igényelnek = count () default = 0 az időbélyegzőn (1d) – most (), 1 – 5. lépés_ |

### <a name="adding-a-metric-chart"></a>Metrikus diagram hozzáadása

1. Az eszköztár **szerkesztése** elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. A **metrika hozzáadása** hivatkozásra kattintva hozzáadhat egy mérőszám vezérlőelemet a munkafüzethez.
3. Válasszon ki egy erőforrástípust (például Storage-fiók), a célként megadott erőforrásokat, a metrika névterét és nevét, valamint a használandó összesítést.
4. Szükség esetén más paramétereket is beállíthat, például időtartományt, felosztást, vizualizációt, méretet és színpalettát.

![A metrikus diagram képernyőképe szerkesztési módban](./media/workbooks-visualizations/metric-chart.png)

#### <a name="metric-chart-parameters"></a>Metrikai diagram paraméterei

| Paraméter | Magyarázat | Példa |
| ------------- |:-------------|:-------------|
| `Resource Type` | A célként használandó erőforrás típusa | Tároló vagy virtuális gép. |
| `Resources` | Erőforrások készlete, amelyből beolvashatók a mérőszámok értéke | MyStorage1 |
| `Namespace` | A metrikával rendelkező névtér | Storage > blob |
| `Metric` | A megjeleníteni kívánt metrika | Storage > blob > tranzakció |
| `Aggregation` | A metrikára alkalmazandó aggregációs függvény | Összeg, Darabszám, átlag stb. |
| `Time Range` | Az időablak, amely a metrikát jeleníti meg | Az elmúlt óra, az elmúlt 24 óra stb. |
| `Visualization` | A használni kívánt vizualizáció | Terület, sáv, vonal, Scatter, rács |
| `Split By` | A mérőszám kiosztása egy dimenzión | Tranzakciók földrajzi típus szerint |
| `Size` | A vezérlő függőleges mérete | Kis, közepes vagy nagy |
| `Color palette` | A diagramban használandó színpaletta Figyelmen kívül hagyva `Split by` , ha a paraméter használatban van | Kék, zöld, piros stb. |

## <a name="grids"></a>Rácsok

A rácsok vagy táblázatok a felhasználók számára történő adatmegjelenítés közös módja. A munkafüzetek lehetővé teszik a felhasználók számára a rács oszlopainak egyéni stílusát, hogy a jelentésekben gazdag felhasználói felületet nyújtsanak.

Az alábbi példa egy olyan rácsot mutat be, amely kombinálja az ikonokat, a intenzitástérképei és a Spark-sávokat a komplex információk megjelenítéséhez. A munkafüzet a rendezést, a keresőmezőt és a "to-Analytics" gombot is tartalmazza.

![A log-alapú rács képernyőképe](./media/workbooks-visualizations/grid.png)

### <a name="adding-a-log-based-grid"></a>Log-alapú rács hozzáadása

1. Az eszköztár **szerkesztése** elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. A **lekérdezés hozzáadása** hivatkozásra kattintva hozzáadhat egy log lekérdezési vezérlőt a munkafüzethez.
3. Válassza ki a lekérdezés típusát **naplóként**, erőforrástípusként (például Application Insights) és a célként megadott erőforrásokat.
4. A lekérdezéstervező segítségével megadhatja az elemzés KQL (például egy küszöbérték alatti virtuális gépeket)
5. Vizualizáció beállítása **rácsra**
6. Szükség esetén más paramétereket is beállíthat – például időtartomány, méret, színpaletta és jelmagyarázat.

![A log-alapú rács lekérdezésének képernyőképe](./media/workbooks-visualizations/grid-query.png)

## <a name="tiles"></a>Csempék

A csempék nagyon hasznosak a munkafüzetek összegző adatainak bemutatására. Az alábbi képen látható a csempék általános használati esete – az alkalmazás szintű összefoglalás a részletes rácson felül.

![Képernyőfelvétel a csempe összefoglaló nézetéről](./media/workbooks-visualizations/tiles-summary.png)

A munkafüzet csempék támogatják a címet, a feliratot, a nagyméretű szöveget, az ikonokat, a metrikai átmeneteket, a Spark-vonalakat, a sávokat, a láblécet stb.

### <a name="adding-a-tile"></a>Csempe hozzáadása

1. Az eszköztár _szerkesztése_ elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. A **lekérdezés hozzáadása** hivatkozásra kattintva hozzáadhat egy log lekérdezési vezérlőt a munkafüzethez. 
3. Válassza ki a lekérdezés típusát **naplóként**, erőforrástípusként (például Application Insights) és a célként megadott erőforrásokat.
4. Az elemzéshez használandó KQL megadásához használja a lekérdezés-szerkesztőt
    ```kusto
    requests
    | summarize Requests = count() by appName, name
    | top 7 by Requests desc
    ```
5. **Teljes** méret beállítása
6. Vizualizáció beállítása **csempék** számára
7. Kattintson a **csempe beállításai** gombra a beállítások ablaktábla megnyitásához.
8. A **csempe mezőiben**állítsa be a következőket:
    * Cím`name`
    * Bal: `Requests` , megjelenítő: `Big Number` , színpaletta: `Green to Red` , minimális érték:`0`
    * Alján`appName`
9. Kattintson a **Mentés és bezárás** gombra a panel alján.

![Képernyőfelvétel a csempe összefoglaló nézetéről](./media/workbooks-visualizations/tile-settings.png)

A csempék olvasási módban fognak megjelenni:

![Képernyőfelvétel a csempe összefoglaló nézetéről](./media/workbooks-visualizations/tiles-read-mode.png)

## <a name="trees"></a>Fák

A munkafüzetek farácson keresztül támogatják a hierarchikus nézeteket. A fák lehetővé teszik, hogy egyes sorok kiterjeszthetők legyenek a következő szintre a részletezési élmény érdekében.

Az alábbi példában a tároló állapot metrikái (Munkakészlet mérete) láthatók farácsként. A legfelső szintű csomópontok itt az Azure Kubernetes Service (ak) csomópontjai, a következő szint a hüvelyek, a végső szint pedig tároló. Figyelje meg, hogy továbbra is formázhatja az oszlopokat, például egy rácsban (hő, ikonok, hivatkozás). Ebben az esetben az alapul szolgáló adatforrás egy Log Analytics munkaterület, AK-naplókkal.

![Képernyőfelvétel a csempe összefoglaló nézetéről](./media/workbooks-visualizations/trees.png)

### <a name="adding-a-tree-grid"></a>Fa rács hozzáadása
1. Az eszköztár _szerkesztése_ elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. A **lekérdezés hozzáadása** hivatkozásra kattintva hozzáadhat egy log lekérdezési vezérlőt a munkafüzethez. 
3. Válassza ki a lekérdezés típusát **naplóként**, erőforrástípusként (például Application Insights) és a célként megadott erőforrásokat.
4. Az elemzéshez használandó KQL megadásához használja a lekérdezés-szerkesztőt
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
5. Vizualizáció beállítása **rácsra**
6. Kattintson az **oszlop beállításai** gombra a beállítások ablaktábla megnyitásához.
7. Az alul található **Tree/Group By Settings** szakaszban állítsa be a következőt:
    * Fa típusa:`Parent/Child`
    * Azonosító mező:`Id`
    * Szülő-azonosító mező:`ParentId`
    * A Expander megjelenítése:`Name`
    * A fa legfelső szintjének kibontása:`checked`
8. Az _oszlopok_ szakaszban, a felső részen állítsa be a következőt:
    * _Azonosító_ – oszlop megjelenítő:`Hidden`
    * _Szülő-azonosító_ – oszlop megjelenítő:`Hidden`
    * _Kérelmek_ – oszlop megjelenítő: `Bar` , szín: `Blue` , minimális érték:`0`
9. Kattintson a _Mentés és bezárás_ gombra a panel alján.    

![Képernyőfelvétel a csempe összefoglaló nézetéről](./media/workbooks-visualizations/tree-settings.png)

### <a name="tree-settings"></a>Fa beállításai

| Beállítás | Magyarázat |
|:------------- |:-------------|
| `Id Field` | A rács minden sorának egyedi azonosítója |
| `Parent Id Field` | Az aktuális sor szülőjének azonosítója |
| `Show the expander on` | Az az oszlop, amelyen be kell mutatni a fa kiexpanderét. A farácsok esetében gyakori, hogy elrejtsék az azonosítót és a szülő azonosító mezőt, mert nem nagyon olvashatók. Ehelyett a Expander egy olvashatóbb értéket tartalmazó mezőben jelenik meg, mint az entitás neve. |
| `Expand the top level of the tree` | Ha be van jelölve, a farács a legfelső szinten lesz kibontva. Hasznos, ha további információkat szeretne megjeleníteni alapértelmezetten |

## <a name="graphs"></a>Grafikonok

A munkafüzetek lehetővé támogatják a naplókból származó adatok alapján tetszőleges gráfok megjelenítését a figyelési entitások közötti kapcsolatok megjelenítéséhez.

Az alábbi ábrán egy számítógépnek a külső számítógépekre irányuló és kívüli különböző portokon keresztül áramló adatok láthatók. Színes típus szerint (számítógép vagy port vagy külső IP-cím), a peremhálózat mérete pedig a közti adatforgalom mennyiségének felel meg. A mögöttes adatok a virtuálisgép-kapcsolatokra irányuló KQL-lekérdezésekből származnak.

![Képernyőfelvétel a csempe összefoglaló nézetéről](./media/workbooks-visualizations/graph.png)

### <a name="adding-a-graph"></a>Gráf hozzáadása
1. Az eszköztár _szerkesztése_ elemre kattintva váltson át a munkafüzet szerkesztési módjára.
2. A **lekérdezés hozzáadása** hivatkozásra kattintva hozzáadhat egy log lekérdezési vezérlőt a munkafüzethez. 
3. Válassza ki a lekérdezés típusát **naplóként**, erőforrástípusként (például Application Insights) és a célként megadott erőforrásokat.
4. Az elemzéshez használandó KQL megadásához használja a lekérdezés-szerkesztőt
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
7. Vizualizáció beállítása **gráfra**
8. A beállítások ablaktábla megnyitásához kattintson a **Graph-beállítások** gombra.
9. A lenti _elrendezés mezőkben_ állítsa be a következőt:
    * `Node Id`: `Id`
    * `Source Id`: `SourceId`
    * `Target Id`: `TargetId`
    * `Edge Label`: `None`
    * `Edge Size`: `Calls`
    * `Node Size`: `None`
    * `Coloring Type`: `Categorical`
    * `Node Color Field`: `Kind`
    * `Color palette`: `Pastel`
10. A felső _csomópont-formátum beállításainál_ állítsa be a következőt:
    * _Top Content_-use oszlop: `Name` , oszlop megjelenítő:`Text`
    * _Center Content_-use oszlop: `Calls` , oszlop megjelenítő: `Big Number` , színpaletta:`None`
    * _Alsó Content_-use oszlop: `Kind` , oszlop megjelenítő:`Text`
10. Kattintson a _Mentés és bezárás_ gombra a panel alján.

![Képernyőfelvétel a csempe összefoglaló nézetéről](./media/workbooks-visualizations/graph-settings.png)

## <a name="next-steps"></a>További lépések

* Munkafüzetek [üzembe helyezése](workbooks-automate.md) Azure Resource Managersal.
* A munkafüzet erőforrásaihoz való hozzáférés [szabályozása](workbooks-access-control.md) és megosztása.
