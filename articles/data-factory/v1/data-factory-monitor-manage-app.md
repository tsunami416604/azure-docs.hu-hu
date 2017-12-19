---
title: "Megfigyelés és kezelés adatok folyamatok - Azure |} Microsoft Docs"
description: "Útmutató: a figyelés és a felügyeleti alkalmazás segítségével Azure adat-előállítók és a folyamatok felügyeletét és kezelését."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: f3f07bc4-6dc3-4d4d-ac22-0be62189d578
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 65b5389837dc1d1693b1c4326b98264c8d75fd06
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-monitoring-and-management-app"></a>Figyelheti és kezelheti az Azure Data Factory adatcsatornák a figyelés és felügyelet alkalmazással
> [!div class="op_single_selector"]
> * [Az Azure portálon vagy az Azure PowerShell használatával](data-factory-monitor-manage-pipelines.md)
> * [Használatával figyelése és a felügyeleti alkalmazás](data-factory-monitor-manage-app.md)
>
>

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [felügyeletéhez és kezeléséhez az adat-előállító adatcsatornák 2-es](../monitor-visually.md).

A cikkből megtudhatja, hogyan használható a figyelés és a felügyeleti alkalmazás figyeléséhez, kezeléséhez és az adat-előállító adatcsatornák debug. Tájékoztatást kaphat az hibáiról riasztásokat létrehozásával kapcsolatos információkat is biztosít. Ismerkedés az alkalmazás által a következő videolejátszás használatával:

> [!NOTE]
> A felhasználói felületen látható módon a videó előfordulhat, hogy nem egyeznek pontosan kapcsolatban a portálon. Némileg régebbi, de fogalmak változatlan marad. 

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Data-Factory-Monitoring-and-Managing-Big-Data-Piplines/player]
>

## <a name="launch-the-monitoring-and-management-app"></a>Indítsa el a figyelés és a felügyeleti alkalmazás
A figyelő és a felügyeleti alkalmazás indításához kattintson a **figyelő & kezelése** csempét a **adat-előállító** a data factory paneljén.

![A Data Factory kezdőlapon figyelési csempe](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Meg kell jelennie egy külön ablakban nyissa meg a figyelés és a felügyeleti alkalmazás.  

![Figyelési és felügyeleti alkalmazás](./media/data-factory-monitor-manage-app/AppLaunched.png)

> [!NOTE]
> Ha azt látja, hogy a webböngésző akadt-e a "Engedélyező...", törölje a jelet a **külső cookie-k blokkolását, és a helyadatok** jelölőnégyzet – vagy a tárolás során is garantálják az kiválasztva, hozzon létre egy kivételt **login.microsoftonline.com**, és próbálja meg újra megnyitni az alkalmazás.


A középső ablaktáblán tevékenység Windows listájában látni egy tevékenység ablakban tevékenység minden egyes futtatásához. Például ha a tevékenység öt órán keresztül óránkénti futásra ütemezett, látni öt tevékenység windows társított öt adatszeletek. Ha nem látja a lista alján tevékenységablakok, tegye a következőket:
 
- Frissítés a **kezdési időpont** és **befejező időpontja** szűrők felel meg a kezdési és befejezési időpontjai között a folyamat, és kattintson a lap tetején a **alkalmaz** gombra.  
- A tevékenység Windows lista nem frissül automatikusan. Kattintson a **frissítése** gombra az eszköztáron a **tevékenység Windows** listája.  

Ha még nem rendelkezik a Data Factory alkalmazás teszteléséhez ezeket a lépéseket, tegye az oktatóanyag: [adatok másolása az Blob-tároló az SQL-adatbázis használata a Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="understand-the-monitoring-and-management-app"></a>A megfigyelés és a felügyeleti alkalmazás
A bal oldali vannak a három lappal: **erőforrás-kezelő**, **figyelési nézetei**, és **riasztások**. Az első lap (**erőforrás-kezelő**) alapértelmezés szerint engedélyezett.

### <a name="resource-explorer"></a>Erőforrás-kezelő
Tekintse át a következőket:

* Az erőforrás-kezelő **fanézetben** a bal oldali ablaktáblán.
* A **diagramnézet** a középső ablaktáblán a lap tetején.
* A **tevékenység Windows** lista alján a középső ablaktáblán.
* A **tulajdonságok**, **tevékenység ablak Explorer**, és **parancsfájl** lapokon a jobb oldali ablaktáblán.

Az erőforrás-kezelőben lásd: összes erőforrást (adatcsatornák, adatkészleteket, összekapcsolt szolgáltatások) fanézetben adat-előállító. Amikor kijelöl egy objektumot az erőforrás-kezelőben:

* A társított adat-előállító entitás ki van jelölve, a Diagram nézetben.
* [Hozzárendelt tevékenység windows](data-factory-scheduling-and-execution.md) emel ki a tevékenységet Windows lista alján.  
* A kiválasztott objektum tulajdonságait a Tulajdonságok ablak jobb oldali ablaktáblán jelennek meg.
* A kijelölt objektum JSON-definícióból jelenik meg, ha van ilyen. Például: a társított szolgáltatás, a DataSet adatkészlet vagy folyamat.

![Erőforrás-kezelő](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Tekintse meg a [ütemezés és a végrehajtás](data-factory-scheduling-and-execution.md) cikk tevékenység windows kapcsolatos további részletes információt.

### <a name="diagram-view"></a>Diagramnézet
A Diagram nézetben az adat-előállító egytáblás üveg felügyeletéhez és a data factory és az eszközök kezeléséhez biztosít. Ha a Diagram nézetben kiválaszt egy adat-előállító entitás (dataset/pipeline):

* A data factory entitás a faszerkezetes nézetben kiválasztott van.
* A társított tevékenység windows a tevékenység Windows listán vannak kiemelve.
* A Tulajdonságok ablakban láthatók a kiválasztott objektum tulajdonságait.

Ha a folyamat (nem a szünetel) engedélyezve van, a zöld vonallal is látható:

![A folyamat fut](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Szüneteltetése, folytatása vagy jelölje ki azt a diagram nézetben és a gombok segítségével a parancssávon folyamat leáll.

![A parancssávon felfüggesztése vagy folytatása](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)
 
Nincsenek három gombok a tölcsér a Diagram nézetben. A második gomb segítségével felfüggeszti a folyamatot. Felfüggesztése nem leállítja a futó tevékenységeket, és lehetővé teszi, hogy folytatható befejezésig. A harmadik gomb megszakítja a folyamatot, és a meglévő tevékenységek végrehajtása leáll. Az első gombra a folyamat folytatódik. Ha a feldolgozási sor fel van függesztve, a folyamat színe megváltozik. Például egy felfüggesztett folyamat néz ki az alábbi képen: 

![Feldolgozási sor felfüggesztve](./media/data-factory-monitor-manage-app/PipelinePaused.png)

A Ctrl billentyűt használja a többszörös kiválasztási két vagy több folyamatok segítségével. A gombok segítségével felfüggesztése vagy folytatása több folyamatok egyszerre.

Kattintson a jobb gombbal egy folyamatot is, és beállítások is választhatók felfüggesztése, folytatása vagy egy folyamat leáll. 

![Az adatcsatorna helyi menü](./media/data-factory-monitor-manage-app/right-click-menu-for-pipeline.png)

Kattintson a **nyitott folyamat** megtekintéséhez az összes tevékenység a feldolgozási beállítás. 

![Folyamat megnyitása menü](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

A megnyitott csővezeték nézetben tekintse meg a sorban az összes tevékenység. Ebben a példában csak egy tevékenység nincs: másolási tevékenység. 

![Megnyitott folyamat](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Lépjen vissza az előző nézetével, kattintson a navigációs menü felső részén adat-előállító.

Az adatcsatorna nézetben egy kimeneti adatkészletet, vagy amikor az egér átvitele a kimeneti adatkészlet kiválasztása után megjelenik a tevékenység Windows előugró ablak, hogy az adatkészlethez.

![Tevékenység Windows előugró ablak](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Egy tevékenység ablakban ki azt a részletek megtekintéséhez kattintson a **tulajdonságok** a jobb oldali ablak.

![Tevékenység ablak tulajdonságai](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

A jobb oldali ablaktáblában váltani a **tevékenység ablak Explorer** lap további részletek megtekintéséhez.

![Tevékenység ablak Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Azt is láthatja, **változók feloldva** az egyes futtatására tett kísérlet egy tevékenység a **kísérletek** szakasz.

![Megoldott változók](./media/data-factory-monitor-manage-app/ResolvedVariables.PNG)

Váltás a **parancsfájl** fülre a JSON-parancsfájl definícióból a kijelölt objektum megtekintéséhez.   

![Parancsfájl lap](./media/data-factory-monitor-manage-app/ScriptTab.png)

Tevékenység windows három helyen tekintheti meg:

* A tevékenység Windows előugró ablak az a Diagram nézet (középső ablaktábla).
* A tevékenység ablak Explorer, a jobb oldali ablaktáblán.
* A tevékenység Windows listája az alsó ablaktáblán.

A tevékenység Windows előugró ablak és tevékenység ablak Explorer görgetve az előző héten és a következő hét a jobb és bal nyíl használatával.

![Tevékenység ablak Explorer balra vagy jobbra nyíl](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

A Diagram nézet alján megjelenik az ilyen gombokat: Nagyítás a, Kicsinyítés, nagyítás beállítása, Nagyítás 100 %-os, Elrendezés zárolása. A **zárolási elrendezés** gomb megakadályozza a táblák és folyamatok véletlen áthelyezését a Diagram nézetben. Alapértelmezés szerint le van. Kapcsolja ki, és entitások Navigálás a diagramban. Kapcsolja ki, ha az utolsó gomb segítségével táblák és folyamatok automatikus formázása. Az egér kerekének használatával is bejövő vagy kimenő ráközelíthet.

![Diagram nézet Nagyítás parancsok](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)

### <a name="activity-windows-list"></a>A tevékenységek Windows listája
A tevékenység Windows a középső ablaktáblán alján megjelenik az erőforrás-kezelővel vagy a Diagram nézetben kiválasztott adatkészlet összes tevékenység windows. Alapértelmezés szerint a lista van, csökkenő sorrendben, ami azt jelenti, hogy megjelenik-e a legújabb tevékenység ablak tetején.

![A tevékenységek Windows listája](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Ebben a listában nem frissülnek automatikusan, ezért a frissítés gombra az eszköztáron kézi frissítéséhez.  

Tevékenység windows a következő állapotok valamelyikében lehet:

<table>
<tr>
    <th align="left">status</th><th align="left">A részállapot</th><th align="left">Leírás</th>
</tr>
<tr>
    <td rowspan="8">Várakozás</td><td>ScheduleTime</td><td>Az idő a tevékenység időszakot még nem érkezett.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>A fölérendelt függőségek nem állnak készen.</td>
</tr>
<tr>
<td>ComputeResources</td><td>A számítási erőforrások nem érhetők el.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Az összes Tevékenységpéldány futtatásával elfoglalva más tevékenység windows.</td>
</tr>
<tr>
<td>ActivityResume</td><td>A tevékenység szüneteltetve van, és nem futtatható a tevékenység windows folytatásáig.</td>
</tr>
<tr>
<td>Retry</td><td>A tevékenység végrehajtási lesz hajtva.</td>
</tr>
<tr>
<td>Ellenőrzés</td><td>Érvényesítés még a még nem indult el.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Érvényesítés újrapróbálására vár.</td>
</tr>
<tr>
<tr>
<td rowspan="2">Folyamatban</td><td>Érvényesítés</td><td>Ellenőrzése folyamatban van.</td>
</tr>
<td>-</td>
<td>A tevékenység ablakban feldolgozása folyamatban van.</td>
</tr>
<tr>
<td rowspan="4">Sikertelen</td><td>Időtúllépésbe került</td><td>A tevékenység végrehajtási tevékenység által megengedett érték időt vett igénybe.</td>
</tr>
<tr>
<td>Törölve</td><td>A tevékenység ablakban felhasználói művelet megszakította.</td>
</tr>
<tr>
<td>Ellenőrzés</td><td>Az érvényesítés nem sikerült.</td>
</tr>
<tr>
<td>-</td><td>Tevékenységéhez generált vagy érvényesítése nem sikerült.</td>
</tr>
<td>Kész</td><td>-</td><td>A tevékenység ablakban készen áll a felhasználásra.</td>
</tr>
<tr>
<td>Kihagyva</td><td>-</td><td>A tevékenység ablak nincs feldolgozva.</td>
</tr>
<tr>
<td>None</td><td>-</td><td>Egy tevékenység ablakban létezett egy eltérő állapottal, de alaphelyzetbe lett állítva.</td>
</tr>
</table>


Ha a listában egy tevékenység ablakban gombra kattint, megjelenik az részleteit a a **tevékenység Windows Explorer** vagy a **tulajdonságok** a jobb oldali ablak.

![Tevékenység ablak Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### <a name="refresh-activity-windows"></a>Tevékenység windows frissítése
A részletek nem automatikusan frissülnek, ezért a frissítés (második) gombra a parancssávon manuális frissítéséhez a windows a tevékenységek listája.  

### <a name="properties-window"></a>Tulajdonságok ablak
A Tulajdonságok ablak van, a figyelés és a felügyeleti alkalmazás a jobb szélső panelén.

![Tulajdonságok ablak](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Megjeleníti az erőforrás-kezelő (fanézetben), a Diagram nézet vagy a tevékenység Windows listán kijelölt elem tulajdonságai.

### <a name="activity-window-explorer"></a>Tevékenység ablak Explorer
A **tevékenység ablak Explorer** időszak van a figyelés és a felügyeleti alkalmazás a jobb szélső panelén. A tevékenység ablakban, a tevékenység Windows előugró ablakban vagy a tevékenység Windows listában kiválasztott részleteit jeleníti meg.

![Tevékenység ablak Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Egy másik tevékenység ablak tetején naptár nézetben kattintással lehet váltani. Lásd az előző hét vagy a következő hét tevékenység windows tetején a bal oldali/jobbra gomb is használja.

Segítségével gombokat az alsó ablaktáblában futtassa újra a tevékenység ablakban, vagy frissítse a részletek ablaktáblájában.

### <a name="script"></a>Szkript
Használhatja a **parancsfájl** fülre kattintva megtekintheti a kijelölt adat-előállító entitás (társított szolgáltatás, adatkészlet vagy csővezeték) JSON-definícióból.

![Parancsfájl lap](./media/data-factory-monitor-manage-app/ScriptTab.png)

## <a name="use-system-views"></a>Rendszer-nézetek
A figyelés és felügyelet alkalmazást tartalmaz, előre összeállított rendszernézetek (**legutóbbi tevékenységek windows**, **sikertelen volt a tevékenység windows**, **folyamatban lévő tevékenységek windows**), amelyek lehetővé teszik, hogy a data factory legutóbbi/nem sikerült/folyamatban tevékenységablakok megtekintése.

Váltás a **figyelési nézetei** lapon kattintson a bal oldalon.

![Figyelési nézetek lap](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Jelenleg nincsenek három rendszernézetek támogatott. Válassza ki a legutóbbi tevékenységek windows, a sikertelen tevékenységet windows vagy a folyamatban lévő tevékenységek windows a tevékenység Windows listában (középső ablaktábla alján) lehetőséget.

Ha bejelöli a **legutóbbi tevékenységek windows** beállítást, megjelenik minden legutóbbi tevékenységek windows csökkenő sorrendben a **utolsó kísérlet ideje**.

Használhatja a **sikertelen volt a tevékenység windows** nézetre, és tekintse meg a listában az összes sikertelen tevékenység windows. Egy meghiúsult tevékenységet ablak az információk a részletes listáján válassza ki a **tulajdonságok** ablakban vagy a **tevékenység ablak Explorer**. A naplók a sikertelen tevékenységet időszak is letöltheti.

## <a name="sort-and-filter-activity-windows"></a>Rendezésére és szűrésére tevékenység windows
Módosítsa a **kezdési időpont** és **befejező időpontja** beállításai a szűrő tevékenység windows parancsra a parancssávon. A kezdési és befejezési időpontjának módosítása után a gombra a tevékenység Windows listájának frissítése a befejező időpont mellett.

![Kezdő és befejező időpontja](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [!NOTE]
> Minden alkalommal jelenleg a figyelés és a felügyeleti alkalmazás UTC formátumban.
>
>

Az a **tevékenység Windows lista**, kattintson az oszlop nevét (például: állapot).

![Tevékenység Windows lista oszlop menü](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Tegye a következőket:

* Rendezés növekvő sorrendben.
* Rendezés csökkenő sorrendben.
* Szűrés egy vagy több (kész, Várakozás, és így tovább).

Ha szűrőt ad meg egy olyan oszlop, tekintse meg a szűrő gombra az adott oszlop, amely azt jelzi, hogy az oszlopban szereplő értékek szűrt értékek engedélyezett.

![A tevékenység Windows lista oszlopon szűrése](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Szűrő törlése használhatja ugyanabban az előugró ablakban. A tevékenység Windows lista az összes szűrő törlése, kattintson a szűrő törlése gombra a parancssávon.

![A tevékenység Windows lista az összes szűrő törlése](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)

## <a name="perform-batch-actions"></a>Kötegelt műveleteket
### <a name="rerun-selected-activity-windows"></a>Futtassa újra a kijelölt tevékenység windows
Egy tevékenység ablak, kattintson a lefelé mutató nyílra az első parancs sáv gombhoz válassza ki és **újrafuttatása** / **futtassa újra a előtt folyamat**. Ha bejelöli a **futtassa újra a előtt folyamat** beállítás, az összes felsőbb szintű tevékenység windows is Újrafuttatja.
    ![Futtassa újra a műveletet egy tevékenység ablak](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Is több tevékenység windows listáján válassza ki, és futtassa újra a azokat egy időben. Tevékenység windows állapota alapján szűrni kívánt (például: **sikertelen**) –, majd futtassa újra a sikertelen tevékenységet windows a problémát, amelynek hatására a tevékenység windows sikertelen kijavítása után. Lásd a következő tevékenység windows listájában szűrés vonatkozó további információért.  

### <a name="pauseresume-multiple-pipelines"></a>Több folyamatok szüneteltet
A Ctrl billentyűt használja a multiselect két vagy több folyamatok segítségével. A (amely emel ki az alábbi képen piros téglalap) gombok segítségével/szüneteltet őket.

![A parancssávon felfüggesztése vagy folytatása](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

## <a name="create-alerts"></a>Riasztások létrehozása
A **riasztások** lap lehetővé teszi, hogy hozzon létre riasztást és nézet/Szerkesztés/törlés meglévő riasztást. Akkor is is tiltása/engedélyezése egy riasztást. A riasztások lapon megtekintéséhez kattintson a **riasztások** fülre.

![Riasztások lap](./media/data-factory-monitor-manage-app/AlertsTab.png)

### <a name="to-create-an-alert"></a>Riasztás létrehozása
1. Kattintson a **hozzáadása riasztás** hozzáadása egy riasztást. Megjelenik a **részletek** lap.

    ![Riasztások – Részletek lap létrehozása](./media/data-factory-monitor-manage-app/CreateAlertDetailsPage.png)
2. Adja meg a **neve** és **leírás** a riasztást, majd kattintson a **következő**. Megjelenik a **szűrők** lap.

    ![Riasztások – szűrők lap létrehozása](./media/data-factory-monitor-manage-app/CreateAlertFiltersPage.png)
3. Válassza ki a **esemény**, **állapot**, és **részállapot** (nem kötelező), hogy szeretné-e a Data Factory szolgáltatás riasztás létrehozása, és kattintson a **következő**. Megjelenik a **címzettek** lap.

    ![Riasztások – címzettek lap létrehozása](./media/data-factory-monitor-manage-app/CreateAlertRecipientsPage.png)
4. Válassza ki a **E-mail-előfizetés rendszergazdái** lehetőséget és/vagy adjon meg egy **további rendszergazdai e-mail**, és kattintson a **Befejezés**. Meg kell jelennie a listában a riasztást.

    ![Riasztások listája](./media/data-factory-monitor-manage-app/AlertsList.png)

A riasztáslistában Szerkesztés/Törlés/tiltása/engedélyezése egy riasztást a riasztás társított gombok használatával.

### <a name="eventstatussubstatus"></a>A részállapot/esemény/állapota
A következő táblázat a rendelkezésre álló eseményeket és állapotokat (és részállapotok) listáját tartalmazza.

| esemény neve | status | A részállapot |
| --- | --- | --- |
| A tevékenység futtatása megkezdődött |Elindítva |Indulás alatt |
| A tevékenység futtatása befejeződött |Sikeres |Sikeres |
| A tevékenység futtatása befejeződött |Sikertelen |Nem sikerült erőforrás-elosztás<br/><br/>Sikertelen végrehajtása<br/><br/>Időtúllépést okozott<br/><br/>Érvényesítés<br/><br/>Elhagyott |
| Igény szerinti HDI-fürtöt létrehozni elindítva |Elindítva |-|
| Igény szerinti HDI-fürtnek sikeresen létrehozva |Sikeres |-|
| Igény szerinti HDI-fürtnek törlése |Sikeres |-|

### <a name="to-edit-delete-or-disable-an-alert"></a>Szerkesztése, törlése, vagy tiltsa le a riasztás

A (pirossal kiemelt) következő gombok segítségével szerkesztése, törlése, vagy tiltsa le a riasztást.

![Riasztások gombok](./media/data-factory-monitor-manage-app/AlertButtons.png)
