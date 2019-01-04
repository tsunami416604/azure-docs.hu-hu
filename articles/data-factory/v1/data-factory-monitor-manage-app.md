---
title: Figyelheti és kezelheti az adatfolyamatok – Azure |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja a megfigyelési és felügyeleti alkalmazás figyelése és felügyelete az Azure-beli adat-előállítók és folyamatok.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 5b70edd4f65538b52c70881258bc500a34b04d80
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025419"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Folyamatok figyelése és felügyelete az Azure Data Factory a Monitoring and Management app használatával
> [!div class="op_single_selector"]
> * [Az Azure portal vagy az Azure PowerShell használatával](data-factory-monitor-manage-pipelines.md)
> * [Használatával Monitorozási és felügyeleti alkalmazás](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [a Data Factory-folyamatok figyelése és felügyelete](../monitor-visually.md).

Ez a cikk ismerteti, hogyan használható a megfigyelési és felügyeleti alkalmazás figyelése, kezelése és a Data Factory-folyamatok javítása. Akkor is használatának első lépései az alkalmazás által a következő videót:

> [!NOTE]
> Előfordulhat, hogy a felhasználói felület a videóban látható nem pontosan egyezik a kapcsolatban a portálon. Némileg régebbi, de fogalmak változatlan marad. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Indítsa el a figyelési és felügyeleti alkalmazás
A figyelő és felügyeleti alkalmazás indításához kattintson a **Monitor & Manage** csempét a **adat-előállító** a data Factory panel.

![Figyelési csempére a Data Factory kezdőlapján](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

A megfigyelési és felügyeleti alkalmazás, egy külön ablakban nyílik meg kell megjelennie.  

![Figyelési és felügyeleti alkalmazás](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Ha látja, hogy a böngésző "Authorizing..." megakadt, törölje a jelet a **külső cookie-k blokkolását és a helyadatok** jelölőnégyzet – vagy ha nyitva hagyja kiválasztva, hozzon létre kivételt a **login.microsoftonline.com**, és Próbálja meg újra megnyitni az alkalmazást.


A tevékenység Windows listában a középső ablaktáblán látni egy tevékenységablakot a tevékenység minden egyes futtatásához. Például ha a tevékenység esetében öt órára óradíjat futtatott, ütemezett, akkor öt tevékenységablak jelenik meg társított öt adatszeletek. Ha nem látja a lista alján tevékenységablakok, tegye a következőket:
 
- Frissítés a **kezdési idő** és **befejezési idő** tetején, az a folyamat kezdési és befejezési idejének megfelelően, majd kattintson a szűrők a **alkalmaz** gombra.  
- A tevékenység Windows lista nem frissül automatikusan. Kattintson a **frissítése** gombra az eszköztáron a **tevékenység Windows** listája.  

Ha nem rendelkezik egy adat-előállító alkalmazás teszteléséhez ezeket a lépéseket, végezze el az oktatóanyag: [adatok másolása Blob Storage-ból az SQL Database használatával a Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>A megfigyelés és felügyeleti alkalmazás
A bal oldali van a három lappal: **Az erőforrás-kezelő**, **figyelési nézetei**, és **riasztások**. Az első lapra (**erőforrás-kezelő**) alapértelmezés szerint ki van választva.

### <a name="resource-explorer"></a>Erőforrás-kezelő
Tekintse meg a következőket:

* Az erőforrás-kezelő **fanézetben** a bal oldali panelen.
* A **diagramnézet** tetején, a középső ablaktáblán.
* A **tevékenység Windows** lista alján, a középső ablaktáblán.
* A **tulajdonságok**, **Activity Window Explorer**, és **parancsfájl** lapokat a jobb oldali ablaktáblán.

Az erőforrás-kezelőben tekintse meg az adat-előállító fanézetben összes erőforrásának (a folyamatok, adatkészletek, a társított szolgáltatások). Amikor kiválaszt egy objektumot az erőforrás-kezelőben:

* A társított Data Factory-entitások a Diagram nézet van kiemelve.
* [Kapcsolódó tevékenység windows](data-factory-scheduling-and-execution.md) ki vannak emelve a tevékenység Windows lista alján.  
* A kiválasztott objektum tulajdonságait a Tulajdonságok ablakban, a jobb oldali ablaktáblán jelennek meg.
* A JSON-definíciót a kijelölt objektum jelenik meg, ha van ilyen. Példa: egy társított szolgáltatást, adatkészlet vagy folyamat.

![Erőforrás-kezelő](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Tekintse meg a [ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) tevékenységablakok kapcsolatos további részletes információt a cikk.

### <a name="diagram-view"></a>Diagramnézet
Adat-előállító diagramnézete egytáblás tekinthesse figyelésére és a egy adat-előállító és az eszközök kezeléséhez. Amikor kiválaszt egy Data Factory-entitások (folyamat/adatkészlet) a Diagram nézetben:

* A data factory-entitások a faszerkezetes nézetben kiválasztott.
* A társított tevékenységablakok ki vannak emelve a tevékenység Windows listában.
* A kiválasztott objektum tulajdonságait a Tulajdonságok ablakban láthatók.

Ha a folyamat (nem szüneteltetett állapotban) engedélyezve van, a zöld vonal jelenik meg:

![A folyamat futtatása](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Szüneteltetése, folytatása vagy folyamat leáll, a diagram nézetben válassza ki, és a gombok használata a parancssávon.

![Szüneteltethet és folytathat a parancssávon](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Nincsenek három gombok a folyamat a Diagram nézetben. Használhatja a második gombra a folyamat szüneteltetéséhez. Szüneteltetés nem leállítani az aktuálisan futó tevékenységeket, és lehetővé teszi, hogy azok befejezési folytassa. A harmadik gomb szünetelteti a folyamatot, és megszakítja a meglévő tevékenységek végrehajtása. Az első gombra a folyamat folytatódik. Ha szünetelteti a folyamatot, a folyamat színe megváltozik. Felfüggesztett folyamatok például hasonlóan jelenik meg a következő képen: 

![Folyamat felfüggesztve](./media/data-factory-monitor-manage-app/PipelinePaused.png)

Többszörös kijelöléses két vagy több folyamattal a Ctrl billentyű használatával is. A gombok segítségével több folyamatot szüneteltethet és folytathat egyszerre.

Kattintson a jobb gombbal egy folyamatot is, és felfüggeszteni, folytatása vagy leállítja a folyamatot is választhatók. 

![Helyi menü folyamat](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Kattintson a **feldolgozási sor megnyitása** megtekintheti az összes tevékenységet a folyamat lehetőséget. 

![Folyamat megnyitása menü](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

A megnyitott folyamatnézet láthatja az összes tevékenység a folyamat. Ebben a példában csak egyetlen tevékenység van: Másolási tevékenység. 

![Megnyitott folyamat](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Az előző nézethez való visszatéréshez kattintson a felső navigációs menüben található az adat-előállító nevét.

A folyamat megtekintheti egy kimeneti adatkészletet, vagy ha, vigye az egérmutatót a kimeneti adatkészlet kiválasztásakor megjelenik a tevékenység Windows előugró ablak az adott adatkészlethez.

![Tevékenység Windows előugró ablak](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Kattintson ide a részletek a hozzá tartozó adatszeleteket a **tulajdonságok** a jobb oldali ablakban.

![Tevékenységek ablak tulajdonságai](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

A jobb oldali ablaktáblán, váltson a **Activity Window Explorer** lapon további részletek megtekintéséhez.

![Activity Window Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Is megjelenik **változók megoldott** adott tevékenységéhez tartozó minden futtatási kísérlet a **kísérletek** szakaszban.

![Megoldott változók](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Váltson a **parancsfájl** lapján megtekintheti a kijelölt objektum a JSON-parancsfájl definíciót.   

![Script lapon](./media/data-factory-monitor-manage-app/ScriptTab.png)

Tevékenységablakok három helyen tekintheti meg:

* A diagramnézet, (középső ablaktábla) tevékenység Windows előugró.
* A jobb oldali Activity Window Explorer.
* A tevékenység Windows lista az alsó ablaktáblán.

A tevékenység Windows előugró és Activity Window Explorer görgethet az előző héten, és a következő hét a jobb és bal nyíl használatával.

![Tevékenység Window Explorer balra vagy jobbra nyíl](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

A Diagram nézet alján ezekre a gombokra jelenik meg: Nagyítás az, Kicsinyítés, nagyítás beállítása, Nagyítás 100 %-elrendezés zárolása. A **zárolási elrendezés** gomb megakadályozza a táblák és folyamatok véletlen áthelyezését a Diagram nézetben. Ez alapértelmezés szerint be van. Kapcsolja ki, és entitások Navigálás a diagramban. Kapcsolja ki, amikor az utolsó gomb segítségével automatikusan elhelyezheti a táblák és folyamatok. Vagy az egér kereke használatával is nagyítás.

![Diagram nézet Nagyítás parancsok](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>Tevékenységablakok lista
A tevékenység Windows lista alján, a középső ablaktáblán az erőforrás-kezelő vagy a Diagram nézetben kiválasztott adatkészlet minden tevékenység windows jeleníti meg. Alapértelmezés szerint a lista van, csökkenő sorrendben, ami azt jelenti, hogy megjelenik-e a legújabb tevékenység ablak tetején.

![Tevékenységablakok lista](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Ebben a listában nem frissül automatikusan, ezért a frissítés gomb az eszköztáron manuálisan frissítheti.  

Tevékenységablakok lehet a következő állapotok valamelyikében:

<table>
<tr>
    <th align="left">status</th><th align="left">A részállapot</th><th align="left">Leírás</th>
</tr>
<tr>
    <td rowspan="8">Várakozás</td><td>ScheduleTime</td><td>Az idő a tevékenység időszak futtatásához nem jár.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>A fölérendelt függőségek nem áll készen.</td>
</tr>
<tr>
<td>ComputeResources</td><td>A számítási erőforrások nem érhetők el.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Az összes Tevékenységpéldány futtatásával elfoglalva más tevékenységablakok.</td>
</tr>
<tr>
<td>ActivityResume</td><td>A tevékenység szüneteltetve van, és nem futtatható a tevékenységablakok folytatásáig.</td>
</tr>
<tr>
<td>Retry</td><td>A tevékenység-végrehajtási lesz hajtva.</td>
</tr>
<tr>
<td>Ellenőrzés</td><td>Érvényesítés még nem indul el.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Érvényesítés újrapróbálására vár.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Folyamatban</td><td>Érvényesítés</td><td>Érvényesítés folyamatban van.</td>
</tr>
<td>-</td>
<td>A tevékenységek ablak feldolgozása folyamatban van.</td>
</tr>
<tr>
<td rowspan="4">Meghiúsult</td><td>Időtúllépés miatt megszakadt</td><td>A tevékenység-végrehajtási a tevékenység által engedélyezett hosszabb időt vett igénybe.</td>
</tr>
<tr>
<td>Megszakítva</td><td>A tevékenységek ablak felhasználói művelet megszakította.</td>
</tr>
<tr>
<td>Ellenőrzés</td><td>Az érvényesítés nem sikerült.</td>
</tr>
<tr>
<td>-</td><td>A tevékenységek ablak létrehozott vagy érvényesített nem sikerült.</td>
</tr>
<td>Kész</td><td>-</td><td>A tevékenységek ablak készen áll a felhasználásra.</td>
</tr>
<tr>
<td>Kihagyva</td><td>-</td><td>A tevékenységek ablak nem lett feldolgozva.</td>
</tr>
<tr>
<td>None</td><td>-</td><td>Egy tevékenységablakot létezett egy eltérő állapottal, de alaphelyzetbe lett állítva.</td>
</tr>
</table>


Ha rákattint egy tevékenységablakot a listában, láthatja a részletes adatokat a **Activity Windows Explorer** vagy a **tulajdonságok** a jobb oldali ablakban.

![Activity Window Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Tevékenységablakok frissítése
A részletek nem automatikusan frissülnek, ezért a frissítés (második) gombra a parancssávon manuálisan frissítheti a tevékenységablakok lista.  

### <a name="properties-window"></a>Tulajdonságok ablak
A Tulajdonságok ablakban a jobb szélső ablaktáblán a megfigyelési és felügyeleti alkalmazás szerepel.

![Tulajdonságok ablak](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Az erőforrás-kezelő (fa nézet), a Diagram nézet vagy a tevékenység Windows listán kijelölt elem tulajdonságainak jeleníti meg.

### <a name="activity-window-explorer"></a>Activity Window Explorer
A **Activity Window Explorer** időszak van a megfigyelési és felügyeleti alkalmazás a jobb szélső ablaktáblán. Az a tevékenység Windows felugró ablakban vagy a tevékenység Windows listában kiválasztott tevékenységablakot részleteit jeleníti meg.

![Activity Window Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Átválthat egy másik tevékenység ablak felső naptár nézetben kattintással. Tekintse meg az előző hét vagy a jövő héten tevékenységablakok tetején a bal oldali/jobbra mutató nyílra gombok is használja.

Használhatja a eszköztárgombok alsó ablaktábláján a tevékenység ablakban futtassa újra vagy frissítse a részletek ablaktáblájában.

### <a name="script"></a>Szkript
Használhatja a **parancsfájl** fülre kattintva megtekintheti a kiválasztott Data Factory-entitások (társított szolgáltatás, adatkészlet vagy folyamatok) a JSON-definícióját.

![Script lapon](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Rendszer-nézetek használata
A megfigyelési és felügyeleti alkalmazás tartalmaz előre elkészített rendszernézetek (**legutóbbi tevékenység windows**, **nem sikerült a tevékenységablakok**, **folyamatban tevékenységablakok**), amelyek engedélyezik a Önnek, hogy a data Factory legutóbbi/sikertelen/folyamatban tevékenységablakok.

Váltson a **figyelési nézetei** lapon kattintson a bal oldalon.

![Figyelési nézetek lap](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Jelenleg három rendszernézetek támogatott. Válasszon egy beállítást a legutóbbi tevékenység a windows, a sikertelen tevékenységek windows vagy a tevékenység Windows listában (a középső ablaktábla alján) tevékenységet a folyamatban lévő windows megtekintéséhez.

Amikor kiválasztja a **legutóbbi tevékenység windows** beállítást, megjelenik az összes legutóbbi tevékenység windows csökkenő sorrendben a **utolsó kísérlet időpontja**.

Használhatja a **nem sikerült a tevékenységablakok** nézetre, és tekintse meg a listában az összes sikertelen tevékenység windows. Válasszon egy sikertelen tevékenységablakot a listában a részleteinek megtekintéséhez a **tulajdonságok** ablakban vagy a **Activity Window Explorer**. Sikertelen tevékenységablakot bármely naplókat is letöltheti.

## <a name="sort-and-filter-activity-windows"></a>Szűrés és rendezés tevékenységablakok
Módosítsa a **kezdési idő** és **befejezési idő** beállításai a tevékenységablakok szűrő parancsra a parancssávon. A kezdő és záró idő módosítása után kattintson a gomb mellett a tevékenység Windows lista frissítése a Befejezés időpontja.

![Kezdési és befejezési időpontja](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Minden alkalommal jelenleg a megfigyelési és felügyeleti alkalmazás UTC formátumban.
>
>

Az a **tevékenység Windows lista**, kattintson az oszlop neve (például: Állapot).

![Tevékenység Windows lista oszlop menü](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

A következőket teheti:

* Rendezés növekvő sorrendben.
* Rendezés csökkenő sorrendben.
* Szűrés egy vagy több értéket (kész, várakozik, és így tovább).

Amikor egy oszlop szűrőt ad meg, a szűrő gombra engedélyezve van az adott oszlop, amely azt jelzi, hogy az oszlopban szereplő értékek szűrt értékek láthatja.

![Egy oszlop, a tevékenység Windows listájának szűrése](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Szűrő törlése ugyanabban az előugró ablakban is használhatja. A tevékenység Windows listát az összes szűrő kikapcsolása, kattintson a szűrő törlése gombra a parancssávon.

![A tevékenység Windows listát az összes szűrő kikapcsolása](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Kötegelt műveletek végrehajtása
### <a name="rerun-selected-activity-windows"></a>Futtassa újra a kijelölt tevékenység windows
Válasszon egy tevékenységablakot, kattintson a lefelé mutató nyílra az első parancs sáv gomb, és válassza ki **ismétlés** / **futtassa újra a felső folyamat**. Amikor kiválasztja a **futtassa újra a felső folyamat** lehetőséget, azt is, valamint minden felsőbb szintű tevékenység windows Újrafuttatja.
    ![Futtassa újra a adatszeleteket](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Is több tevékenységablakok válassza ki a listából, és futtassa újra a azokat egy időben. Előfordulhat, hogy szeretné szűrni a tevékenységablakok állapota alapján (például: **Nem sikerült**) –, majd futtassa újból a sikertelen tevékenységet a windows a tevékenységablakok sikertelenségét okozó probléma megoldása után. A következő részben részletes információk a szűrésről tevékenységablakok a listában.  

### <a name="pauseresume-multiple-pipelines"></a>Szüneteltethet és folytathat több folyamatot
Többszörös kijelöléses két vagy több folyamattal a Ctrl billentyű használatával is. Szüneteltethet és folytathat azokat a (amely ki vannak emelve az alábbi képen piros téglalap) gombok segítségével.

![Szüneteltethet és folytathat a parancssávon](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
