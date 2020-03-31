---
title: Adatfolyamatok figyelése és kezelése – Azure
description: Ismerje meg, hogyan használhatja a Figyelés és kezelés alkalmazást az Azure-adatgyárak és -folyamatok figyelésére és kezelésére.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: de56ba1281d0f20c8be838fa1bc9ebc24905b26c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260370"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Az Azure Data Factory-folyamatok figyelése és kezelése a Figyelési és Felügyeleti alkalmazás használatával
> [!div class="op_single_selector"]
> * [Az Azure Portal/Az Azure PowerShell használata](data-factory-monitor-manage-pipelines.md)
> * [A Figyelés és kezelés alkalmazás használata](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [Data Factory folyamatok figyelését és kezelését a alkalmazásban.](../monitor-visually.md)

Ez a cikk azt ismerteti, hogyan használhatja a Figyelés és kezelés alkalmazást a Data Factory-folyamatok figyelésére, kezelésére és hibakeresésére. Az alkalmazás használatának megkezdéséhez nézze meg a következő videót:

> [!NOTE]
> Előfordulhat, hogy a videóban látható felhasználói felület nem pontosan egyezik azzal, amit a portálon lát. Ez valamivel régebbi, de a fogalmak változatlanok maradnak. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>A Figyelési és felügyeleti alkalmazás elindítása
A Monitor és kezelés alkalmazás elindításához kattintson a Data Factory **panelen** az **adatgyár Adatgyárának** & kezelése csempére.

![Figyelés csempe a Data Factory kezdőlapján](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

A Figyelési és felügyeleti alkalmazás nak külön ablakban kell megnyitva lennie.  

![Figyelési és felügyeleti alkalmazás](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Ha azt látja, hogy a webböngésző beragadt az "Engedélyezés..." jelölőnégyzetből, törölje a jelet a **Harmadik féltől származó cookie-k és webhelyadatok blokkolása** jelölőnégyzetből – vagy jelölje be, hozzon létre kivételt **login.microsoftonline.com**számára, majd próbálja meg újra megnyitni az alkalmazást.


A középső ablaktábla Tevékenység Windows listájában egy tevékenységablak jelenik meg egy tevékenység minden egyes futtatásához. Ha például a tevékenység öt órán keresztül óránkénti futtatására van ütemezve, öt tevékenységablak jelenik meg öt adatszelethez társítva. Ha az alsó listában nem láthatók tevékenységablakok, tegye a következőket:
 
- Frissítse a **kezdő és** a **záró időszűrőket** a tetején, hogy megfeleljenek a folyamat kezdési és befejezési időpontjának, majd kattintson az **Alkalmaz** gombra.  
- A Tevékenység Windows listája nem frissül automatikusan. Kattintson a **Windows tevékenységlistájának** eszköztárának **Frissítés** gombjára.  

Ha nem rendelkezik data factory alkalmazással a lépések teszteléséhez, végezze el az oktatóanyagot: másolja az adatokat a [Blob Storage-ból az SQL Database-be a Data Factory használatával.](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

## <a name="understand-the-monitoring-and-management-app"></a>A Figyelési és felügyeleti alkalmazás megismerése
A bal oldalon három lap található: **Erőforrás-kezelő**, **Figyelő nézetek**és **Riasztások**. Alapértelmezés szerint az első lap (**Erőforrás-kezelő**) van kiválasztva.

### <a name="resource-explorer"></a>Erőforrás-kezelő
A következőket látja:

* Az Erőforrás-kezelő **fanézete** a bal oldali ablaktáblában.
* A középső ablaktábla tetején lévő **Diagram nézet.**
* A **Tevékenység Windows** lista a középső ablaktábla alján.
* A jobb oldali ablaktáblában található **Tulajdonságok**, **Tevékenységablak-kezelő**és **Parancsfájl** lapok.

Az Erőforrás-kezelőben az összes erőforrás (csővezeték, adatkészlet, csatolt szolgáltatás) megjelenik az adat-előállítóban egy fanézetben. Amikor kijelöl egy objektumot az Erőforrás-kezelőben:

* A társított Data Factory entitás ki van emelve a Diagram nézetben.
* [A társított tevékenységablakok](data-factory-scheduling-and-execution.md) ki vannak emelve a Tevékenység Windows listájában alul.  
* A kijelölt objektum tulajdonságai a jobb oldali ablak Tulajdonságok ablakában jelennek meg.
* A kijelölt objektum JSON-definíciója adott esetben megjelenik. Például: csatolt szolgáltatás, adatkészlet vagy folyamat.

![Erőforrás-kezelő](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

A [tevékenységablakokról](data-factory-scheduling-and-execution.md) az Ütemezés és végrehajtás című cikkben olvashat részletesen.

### <a name="diagram-view"></a>Diagramnézet
Az adat-előállító diagramnézete egyetlen üvegtáblát biztosít az adat-előállító és az eszközök figyelésére és kezelésére. Amikor kijelöl egy Adatgyári entitást (adatkészletet/folyamatot) a Diagram nézetben:

* Az adat-előállító entitás ki van jelölve a fa nézetben.
* A kapcsolódó tevékenységablakok ki vannak emelve a Tevékenység Windows listában.
* A kijelölt objektum tulajdonságai a Tulajdonságok ablakban jelennek meg.

Ha a folyamat engedélyezve van (nem szüneteltetett állapotban), zöld vonallal jelenik meg:

![Futó folyamat](./media/data-factory-monitor-manage-app/PipelineRunning.png)

A folyamatot szüneteltetheti, folytathatja vagy leállíthat, ha kijelöli azt a diagramnézetben, és használja a parancssáv gombjait.

![Szünet/folytatás a parancssávon](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
A diagramnézetben a folyamathoz három parancssávgomb található. A második gombbal szüneteltetheti a folyamatot. A szüneteltetés nem szünteti meg az éppen futó tevékenységeket, és lehetővé teszi számukra, hogy folytassa a befejezést. A harmadik gomb szünetelteti a folyamatot, és leállítja a meglévő végrehajtási tevékenységeket. Az első gomb folytatja a folyamatot. A folyamat szüneteltetésekor a folyamat színe megváltozik. Egy szüneteltetett folyamat például az alábbi képen látható: 

![A folyamat szünetel](./media/data-factory-monitor-manage-app/PipelinePaused.png)

A Ctrl billentyűvel több vagy több folyamatot is kijelölhet. A parancssáv gombjaival egyszerre több folyamatot is szüneteltethet/folytathat.

A jobb gombbal egy folyamatra is kattinthat, és megadhatja a folyamat felfüggesztéséhez, folytatásához vagy leállításához rendelkezésre álló beállításokat. 

![A csővezeték helyi menüje](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Kattintson a **Folyamat megnyitása** beállításra a folyamatban lévő összes tevékenység megtekintéséhez. 

![Folyamat megnyitása menü](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

A megnyitott folyamatnézetben a folyamatban lévő összes tevékenység látható. Ebben a példában csak egy tevékenység van: A tevékenység másolása. 

![Megnyitott folyamat](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Az előző nézethez való visszalépéshez kattintson az adatgyár nevére a képernyő tetején lévő zsemlemorzsa menüben.

A folyamatnézetben, amikor kijelöl egy kimeneti adatkészletet, vagy amikor az egeret a kimeneti adatkészlet fölé mozgatja, megjelenik az adott adatkészlet Tevékenység Windows előugró ablaka.

![Tevékenység Windows előugró ablak](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

A tevékenységablakra kattintva megtekintheti a részleteket a jobb oldali ablak **Tulajdonságok** ablakában.

![Tevékenység ablak tulajdonságai](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

A jobb oldali ablaktáblában váltson a **Tevékenységablak-kezelő** lapra, és további részleteket szeretne látni.

![Tevékenységablak-kezelő](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

A Kísérletek szakaszban is **megjelennek a megoldott változók** egy tevékenység minden egyes **futtatási** kísérletéhez.

![Feloldott változók](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Váltson a **Parancsfájl** lapra a kijelölt objektum JSON-parancsfájl-definíciójának megtekintéséhez.   

![Parancsfájl lap](./media/data-factory-monitor-manage-app/ScriptTab.png)

A tevékenységablakokat három helyen láthatja:

* A Tevékenység Windows előugró ablak a Diagram nézetben (középső ablaktábla).
* A Tevékenységablak-kezelő a jobb oldali ablaktáblában.
* A Tevékenység Windows listája az alsó ablaktáblán.

A Tevékenység windowsos előugró ablakban és a Tevékenységablak-kezelőben a bal és a jobb nyíl segítségével görgethet az előző hétre és a következő hétre.

![Tevékenységablak-kezelő balra/jobbra nyíl](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

A Diagramnézet alján a következő gombok láthatók: Nagyítás, Kicsinyítés, Nagyítás igazítás, 100%-os nagyítás, Zárolás elrendezés. Az **Elrendezés zárolása** gomb megakadályozza, hogy véletlenül táblázatokat és folyamatokat mozgasson a Diagram nézetben. Alapból be van kapcsolva. Kikapcsolhatja, és áthelyezheti az entitásokat a diagramon. Ha kikapcsolja, az utolsó gombbal automatikusan pozícionálhatja a táblázatokat és a folyamatokat. Az egérgörgővel nagyíthat vagy kicsinyíthet is.

![Diagram nézet nagyítási parancsai](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Tevékenységablakok lista
A középső ablaktábla alján található Tevékenység Windows lista az Erőforrás-kezelőben vagy a Diagram nézetben kijelölt adatkészlet összes tevékenységablakát megjeleníti. Alapértelmezés szerint a lista csökkenő sorrendben van, ami azt jelenti, hogy a legújabb tevékenységablak látható a tetején.

![Tevékenységablakok lista](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Ez a lista nem frissül automatikusan, ezért az eszköztár frissítésgombjával manuálisan frissítheti azt.  

A tevékenységablakok az alábbi állapotok valamelyikében lehetnek:

<table>
<tr>
    <th align="left">status</th><th align="left">Részállapot</th><th align="left">Leírás</th>
</tr>
<tr>
    <td rowspan="8">Várakozás</td><td>ScheduleTime (Ütemezési idő)</td><td>Még nem jött el az idő, hogy a tevékenységablak fusson.</td>
</tr>
<tr>
<td>Adatkészletfüggőségek</td><td>A felső szintfüggő függőségek még nem állnak készen.</td>
</tr>
<tr>
<td>Számítási erőforrások</td><td>A számítási erőforrások nem érhetők el.</td>
</tr>
<tr>
<td>Egyidejűségi határ</td> <td>Az összes tevékenységpéldány más tevékenységablakok futtatásával van elfoglalva.</td>
</tr>
<tr>
<td>ActivityResume</td><td>A tevékenység szünetel, és nem tudja futtatni a tevékenység ablakokat, amíg nem folytatódik.</td>
</tr>
<tr>
<td>Retry</td><td>A tevékenység végrehajtása újra megkísérlése folyamatban van.</td>
</tr>
<tr>
<td>Ellenőrzés</td><td>Az érvényesítés még nem kezdődött el.</td>
</tr>
<tr>
<td>Érvényesítésújrapróbálkozás</td><td>Az érvényesítés a megkísérlésre vár.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Folyamatban</td><td>Érvényesítése</td><td>Az érvényesítés folyamatban van.</td>
</tr>
<td>-</td>
<td>A tevékenységablak feldolgozása folyamatban van.</td>
</tr>
<tr>
<td rowspan="4">Sikertelen</td><td>Időd-kiállás</td><td>A tevékenység végrehajtása tovább tartott, mint amit a tevékenység engedélyezett.</td>
</tr>
<tr>
<td>Megszakítva</td><td>A tevékenységablakot felhasználói művelet szakította meg.</td>
</tr>
<tr>
<td>Ellenőrzés</td><td>Az érvényesítés nem sikerült.</td>
</tr>
<tr>
<td>-</td><td>A tevékenységablak létrehozása vagy érvényesítése nem sikerült.</td>
</tr>
<td>Kész</td><td>-</td><td>A tevékenységablak készen áll a fogyasztásra.</td>
</tr>
<tr>
<td>Kimarad</td><td>-</td><td>A tevékenységablak nem lett feldolgozva.</td>
</tr>
<tr>
<td>None</td><td>-</td><td>Egy tevékenységablak, amely korábban más állapotú volt, de alaphelyzetbe lett állítva.</td>
</tr>
</table>


Amikor a listában egy tevékenységablakra kattint, a **windows-kezelőben** vagy a jobb oldalon található **Tulajdonságok** ablakban láthatja a tevékenység részleteit.

![Tevékenységablak-kezelő](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Tevékenységablakok frissítése
A részletek nem frissülnek automatikusan, ezért a parancssáv frissítésgombjával (a második gombbal) manuálisan frissítheti a tevékenységablakok listáját.  

### <a name="properties-window"></a>Tulajdonságok ablak
A Tulajdonságok ablak a Figyelés és kezelés alkalmazás jobb szélső ablaktáblájában található.

![Tulajdonságok ablak](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Az Erőforrás-kezelőben (fanézet), a Diagramnézetben vagy a Windows tevékenység listában kijelölt elem tulajdonságait jeleníti meg.

### <a name="activity-window-explorer"></a>Tevékenységablak-kezelő
A **Tevékenységablak-kezelő** ablak a Figyelés i és felügyeleti alkalmazás jobb szélső ablaktáblájában található. A Tevékenység Windows előugró ablakban vagy a Tevékenység Windows listában kijelölt tevékenységablak részleteit jeleníti meg.

![Tevékenységablak-kezelő](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Egy másik tevékenységablakra kattintva válthat a tetején lévő naptárnézetben. A felül látható bal/jobb nyílnyílgombokkal az előző hét vagy a következő hét tevékenységablakait is megtekintheti.

Az alsó ablaktábla eszköztárgombjaival újrafuttathatja a tevékenységablakot, vagy frissítheti az ablaktáblán lévő részleteket.

### <a name="script"></a>Parancsfájl
A **Parancsfájl** lapon megtekintheti a kijelölt Data Factory entitás (csatolt szolgáltatás, adatkészlet vagy folyamat) JSON-definícióját.

![Parancsfájl lap](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Rendszernézetek használata
A Figyelés és kezelés alkalmazás előre elkészített rendszernézeteket tartalmaz (**Legutóbbi tevékenységablakok**, **Sikertelen tevékenységablakok**, **Folyamatban lévő tevékenység ablakok),** amelyek lehetővé teszik az adat-előállító legutóbbi/sikertelen/folyamatban lévő tevékenységablakainak megtekintését.

Kattintással váltson a bal oldali **Figyelési nézetek** lapra.

![Nézetek figyelése lap](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Jelenleg három támogatott rendszernézet van. Válasszon egy lehetőséget a legutóbbi tevékenységablakok, sikertelen tevékenységablakok vagy folyamatban lévő tevékenységablakok megtekintéséhez a Tevékenység Windows listájában (a középső ablaktábla alján).

Ha a **Legutóbbi tevékenységek ablaka lehetőséget választja,** az összes legutóbbi tevékenységablak csökkenő sorrendben jelenik meg az **utolsó próbálkozás időpontja szerint.**

A Sikertelen **tevékenység ablaknézetben** megtekintheti a lista összes sikertelen tevékenységablakát. Válasszon ki egy sikertelen tevékenységablakot a listában, ha a **Tulajdonságok** ablakban vagy a **Tevékenységablak-kezelőben**szeretné látni a részleteket. A sikertelen tevékenységablak naplóit is letöltheti.

## <a name="sort-and-filter-activity-windows"></a>Tevékenységablakok rendezése és szűrése
A tevékenységablakok szűréséhez módosítsa a parancssáv **kezdési** és **befejezési** időbeállításait. A kezdési és befejezési időpont módosítása után kattintson a befejezési időpont melletti gombra a Tevékenység Windows-lista frissítéséhez.

![Kezdési és befejezési idők](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Jelenleg minden alkalommal UTC formátumban a Figyelés és kezelés alkalmazásban.
>
>

A **Tevékenység Windows listájában**kattintson egy oszlop nevére (például: Állapot).

![A Tevékenység Windows-lista oszlopának menüje](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Az lehetőségek érhetők el:

* Rendezés növekvő sorrendben.
* Rendezés csökkenő sorrendben.
* Szűrés egy vagy több érték szerint (Kész, Várakozás stb.).

Amikor szűrőt ad meg egy oszlopon, az oszlophoz engedélyezett szűrőgomb jelenik meg, ami azt jelzi, hogy az oszlop értékei szűrt értékek.

![Szűrés a Windows tevékenységlistájának egyik oszlopára](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

A szűrők törléséhez használhatja ugyanazt az előugró ablakot. A Tevékenység Windows-lista összes szűrőjének törléséhez kattintson a parancssáv szűrőtörlése gombjára.

![A Tevékenység Windows-listájának összes szűrőjének törlése](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Kötegelt műveletek végrehajtása
### <a name="rerun-selected-activity-windows"></a>A kijelölt tevékenységablakok újrafuttatása
Jelöljön ki egy tevékenységablakot, kattintson az első parancssáv gomb lefelé mutató nyilára, és válassza az **Ismétlés futtatása** / a**folyamat felső folyásával**lehetőséget. Ha a **folyamat felső folyásával** való újrafuttatás lehetőséget választja, az összes upstream tevékenységablakot is újrafuttatja.
    ![Tevékenységablak ismétlése](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Több tevékenységablakot is kijelölhet a listában, és egyszerre futtathatja őket újra. Előfordulhat, hogy a tevékenységablakokat az állapot (például: **Sikertelen)** alapján szeretné szűrni, majd újra futtatni a sikertelen tevékenységablakokat, miután kijavította a tevékenységablakokat okozó problémát. A tevékenységablakok szűrésével kapcsolatos részleteket a következő szakaszban találja.  

### <a name="pauseresume-multiple-pipelines"></a>Több folyamat szüneteltetése/folytatása
A Ctrl billentyűvel több vagy több folyamatot is kijelölhet. A parancssáv gombjaival (amelyek az alábbi képen a piros téglalapban vannak kiemelve) szüneteltetheti/folytathatja azokat.

![Szünet/folytatás a parancssávon](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
