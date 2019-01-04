---
title: Folyamatok figyelése és felügyelete az Azure portal és a PowerShell használatával |} A Microsoft Docs
description: Megtudhatja, hogyan figyelheti és kezelheti az Azure-beli adat-előállítók és az Ön által létrehozott folyamatok az Azure portal és az Azure PowerShell használatával.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 9b0fdc59-5bbe-44d1-9ebc-8be14d44def9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 77c55657f57af655b5b8154dbcf58472434396a6
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015492"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Folyamatok figyelése és felügyelete az Azure Data Factory az Azure portal és a PowerShell használatával
> [!div class="op_single_selector"]
> * [Az Azure portal vagy az Azure PowerShell használatával](data-factory-monitor-manage-pipelines.md)
> * [Használatával Monitorozási és felügyeleti alkalmazás](data-factory-monitor-manage-app.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [a Data Factory-folyamatok figyelése és felügyelete](../monitor-visually.md).

Ez a cikk ismerteti figyelése, kezelése és a folyamatok hibakeresése az Azure portal, PowerShell használatával.

> [!IMPORTANT]
> A felügyelet és kezelés alkalmazás figyelése és kezelése az adatcsatornákat, és bármely kapcsolatos hibák elhárítása a hatékonyabb támogatást nyújt. Az alkalmazás használatával kapcsolatos részletekért lásd: [figyelése és Data Factory-folyamatok felügyelete a Monitoring and Management app használatával](data-factory-monitor-manage-app.md). 

> [!IMPORTANT]
> Az Azure Data Factory verziója 1 most használja az új [Azure Monitor riasztási infrastruktúra](../../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). A régi riasztási infrastruktúra elavult. Ennek eredményeképpen a meglévő riasztásokat konfigurálni verzió 1 data factoryk nem fognak működni. A meglévő adat-előállítók v1 riasztások nem települnek át automatikusan. Hozza létre újra az Új riasztási infrastruktúra a riasztásokban kell. Jelentkezzen be az Azure Portalon, és válassza **figyelő** az új riasztások a metrikák (például sikertelen futtatások vagy sikeres futtatások)-verzióra vonatkozó 1 adat-előállítók létrehozását.

## <a name="understand-pipelines-and-activity-states"></a>Megismerheti a folyamatokat és tevékenységi állapotok
Az Azure portal használatával a következőket teheti:

* Az adat-előállító megjelenítése a diagram formájában.
* A folyamat tevékenységének megtekintéséhez.
* Tekintse meg a bemeneti és kimeneti adatkészleteket.

Ez a szakasz azt is ismerteti, hogyan adatkészletet szelet átvált egy állapot egy másik állapotba.   

### <a name="navigate-to-your-data-factory"></a>Keresse meg az adat-előállító
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **adat-előállítók** a a bal oldali menüben. Ha nem látja, kattintson a **további szolgáltatások >**, és kattintson a **adat-előállítók** alatt a **INTELLIGENCIA és elemzés** kategória.

   ![Összes tallózása > adat-előállítók](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. Az a **adat-előállítók** panelen válassza ki az Önt érdeklő adat-előállítóban.

    ![Adat-előállító kiválasztása](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Megtekintheti az adat-előállító kezdőlapjának megnyitásához.

   ![Adat-előállító panel](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Az adat-előállító diagramnézete
A **Diagram** adat-előállító nézetét biztosítja tekinthesse felügyelhető és kezelhető a data factory és az eszközök. Megtekintheti a **Diagram** tekintse meg az adat-előállító, kattintson **Diagram** a data Factory kezdőlapján.

![Diagramnézet](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Nagyítás, Kicsinyítés, szélességhez, kicsinyíthet, 100 %, a diagram elrendezését zárolása és automatikusan elhelyezheti a folyamatokat és adatkészletek nagyítás. Megtekintheti az adatok leszármaztatási információkat is (azaz megjelenítése a kijelölt elemek fölérendelt vagy alárendelt elemeinek).

### <a name="activities-inside-a-pipeline"></a>Egy folyamat belüli tevékenységek
1. Kattintson a jobb gombbal a folyamat, és kattintson a **feldolgozási sor megnyitása** megtekintheti az összes tevékenység a folyamat, valamint a tevékenységek bemeneti és kimeneti adatkészleteket. Ez a funkció akkor hasznos, ha a folyamat több tevékenységet tartalmaz, és szeretné megismerni a működési leszármaztatási egyetlen folyamat.

    ![Folyamat megnyitása menü](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. A következő példában láthatja a folyamat bemeneti és a kimenet egy másolási tevékenységgel. 

    ![Egy folyamat belüli tevékenységek](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Vissza az adat-előállító kezdőlapja a gombra kattintva léphet a **adat-előállító** a bal felső sarokban lévő a webhely-navigációs hivatkozást.

    ![Lépjen vissza a data factory](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>A folyamat belüli egyes tevékenységek állapotának megtekintése
Egy tevékenység aktuális állapotának megtekintéséhez a tevékenység által készített adatkészletekhez állapotának megtekintése.

Ehhez kattintson duplán a **OutputBlobTable** a a **Diagram**, láthatja, hogy a különböző tevékenységfuttatások belüli folyamat által készített szeleteket. Láthatja, hogy a másolási tevékenység a legutóbbi nyolc órát a sikeresen lefutott, és a szelet előállítása a **készen** állapota.  

![A folyamat állapota](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Az adatkészlet szeleteit adat-előállító a következő állapotok egyike lehet:

<table>
<tr>
    <th align="left">Állapot</th><th align="left">Alállapot</th><th align="left">Leírás</th>
</tr>
<tr>
    <td rowspan="8">Várakozás</td><td>ScheduleTime</td><td>Az idő a szelet futtatásának még nem érkezett.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>A fölérendelt függőségek nem áll készen.</td>
</tr>
<tr>
<td>ComputeResources</td><td>A számítási erőforrások nem érhetők el.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Az összes tevékenységpéldány más szeletek futtatásával van elfoglalva.</td>
</tr>
<tr>
<td>ActivityResume</td><td>A tevékenység szüneteltetve van, és nem tudja futtatni a szeletet, amíg a tevékenység folytatása.</td>
</tr>
<tr>
<td>Retry</td><td>Tevékenység-végrehajtási lesz hajtva.</td>
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
<td>A szelet feldolgozása folyamatban van.</td>
</tr>
<tr>
<td rowspan="4">Meghiúsult</td><td>Időtúllépés miatt megszakadt</td><td>A tevékenység-végrehajtási a tevékenység által engedélyezett hosszabb időt vett igénybe.</td>
</tr>
<tr>
<td>Megszakítva</td><td>A szelet felhasználói művelet megszakította.</td>
</tr>
<tr>
<td>Ellenőrzés</td><td>Az érvényesítés nem sikerült.</td>
</tr>
<tr>
<td>-</td><td>A szelet generált kell és/vagy érvényesítése nem sikerült.</td>
</tr>
<td>Kész</td><td>-</td><td>A szelet készen áll a felhasználásra.</td>
</tr>
<tr>
<td>Kihagyva</td><td>None</td><td>A szelet feldolgozás alatt nem.</td>
</tr>
<tr>
<td>None</td><td>-</td><td>A szelet használt létezett egy eltérő állapottal, de azt vissza lett állítva.</td>
</tr>
</table>



A szelet részleteinek megtekintéséhez kattintson egy szelet bejegyzést a a **legutóbb frissített szeletek** panelen.

![Adatszelet részletei](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Ha több alkalommal végre lett hajtva a szeletet, láthatja, a több sort a **tevékenységfuttatások** listája. Futtassa a futtatási bejegyzést kattintva tevékenység részleteit is megtekintheti a **tevékenységfuttatások** listája. A lista tartalmazza a naplófájlokat, és a egy hibaüzenet, ha van ilyen. Ez a funkció akkor hasznos, megtekintése és hibakeresési naplók anélkül, hogy az adat-előállítóhoz.

![Tevékenységfuttatás részletei](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Ha a szelet nem szerepel a **készen áll** állapot, láthatja a fölérendelt szeleteket, amely nem áll készen, és végrehajtja az aktuális szelet forgalomszűrők blokkolják a a **fölérendelt szeleteket, amelyek nem állnak készen** lista. Ez a funkció akkor hasznos, ha a szelet **Várakozás** állapota, és azt szeretné tudni, hogy a szelet vár a fölérendelt függőségek.

![Nem kész állapotú felfelé irányuló szeletek](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Adatkészlet állapot diagramja
Miután telepít egy adat-előállítót, és a folyamatok rendelkezik egy érvényes aktív időszak, az adatkészlet egyik állapotból átmenet szeletekre egy másikba. Jelenleg a szelet állapotát követi a következő ábra állapot:

![Állapotdiagram](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Az adatkészlet állapot áttérési folyamat adat-előállítóban a következő: Várakozás a – folyamatban van vagy folyamatban (érvényesítés) -> -> kész/nem sikerült.

A szelet elindul egy **Várakozás** állapot, Várakozás a végrehajtása előtt teljesítendő előfeltételek. Ezután a tevékenység elindítja a végrehajtása, és a szelet hiányzóra egy **folyamatban** állapota. A tevékenység-végrehajtási lesz sikeres vagy sikertelen. A szelet van megjelölve **készen** vagy **sikertelen**a végrehajtás eredménye alapján.

Visszaállíthatja a szelet lépjen vissza a a **készen** vagy **sikertelen** állapotát a **Várakozás** állapota. Is megjelölheti a szelet állapota **kihagyása**, amely megakadályozza, hogy a tevékenység végrehajtása, és nem dolgozza fel a szeletet.

## <a name="pause-and-resume-pipelines"></a>Pause and resume a folyamatok és
Azure PowerShell használatával kezelheti a folyamatokat. Például felfüggesztése és folytatása a folyamatokat az Azure PowerShell-parancsmagok futtatásával. 

> [!NOTE] 
> A diagram nézet nem támogatja a felfüggesztése és folytatása a folyamatokat. Ha szeretne egy felhasználói felületet használja, a figyelési és azzal való kezelésének alkalmazást kell használni. Az alkalmazás használatával kapcsolatos részletekért lásd: [figyelése és Data Factory-folyamatok felügyelete a Monitoring and Management app használatával](data-factory-monitor-manage-app.md) cikk. 

Is szüneteltetése/felfüggeszteni folyamatok használatával a **Suspend-AzureRmDataFactoryPipeline** PowerShell-parancsmagot. Ez a parancsmag akkor hasznos, ha nem kívánja a folyamatok futtatásához, amíg egy probléma nem oldódik. 

```powershell
Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Példa:

```powershell
Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Miután a problémát megoldottuk a folyamat, folytathatja a felfüggesztett folyamat a következő PowerShell-parancs futtatásával:

```powershell
Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Példa:

```powershell
Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Kereshet bennük hibákat
Az Azure Data Factory biztosít széles körű lehetőségekkel hibakeresése és folyamatok hibaelhárítása az Azure portal és az Azure PowerShell használatával.

> [!NOTE] 
> Sokkal egyszerűbb legyen a troubleshot hibákat a Monitoring & Management App használatával. Az alkalmazás használatával kapcsolatos részletekért lásd: [figyelése és Data Factory-folyamatok felügyelete a Monitoring and Management app használatával](data-factory-monitor-manage-app.md) cikk. 

### <a name="find-errors-in-a-pipeline"></a>Keresse meg a hibákat a folyamat
Ha a tevékenység futása sikertelen, a folyamat, az adatkészlet, a folyamat által előállított állapotban van, hiba történt a hiba miatt. Hibakeresés és a hiba elhárítása az Azure Data Factory a következő módszerekkel.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Hiba hibakeresése az Azure portal használatával
1. Az a **tábla** panelen kattintson a probléma szelet, amely rendelkezik a **állapot** beállítása **sikertelen**.

   ![A probléma szelet tábla panelről](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Az a **adatszelet** panelen, kattintson a tevékenység futtatása sikertelen.

   ![Hiba történt az adatszelet](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. Az a **az Activity run details** panelen letöltheti a fájlokat, a HDInsight feldolgozó társított. Kattintson a **letöltése** az állapot/stderr a hibanaplófájlt, amely tartalmazza a hiba részleteinek letöltéséhez.

   ![A hiba tevékenységfuttatás részletei panel](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Hiba hibakeresés a PowerShell használatával
1. Indítsa el a **PowerShellt**.
2. Futtassa a **Get-AzureRmDataFactorySlice** paranccsal megtekintheti az adatszeletek, és azok állapotát. Megjelenik a szelet állapotát tartalmazó **sikertelen**.        

    ```powershell   
    Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Példa:

    ```powershell   
    Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Cserélje le **StartDateTime** a folyamat kezdési időponttal. 
3. Most futtassa a **Get-AzureRmDataFactoryRun** parancsmag részletes információkat a tevékenység futtatása a szelet.

    ```powershell   
    Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Példa:

    ```powershell   
    Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    A StartDateTime értéke, amely az előző lépésben feljegyzett hiba vagy probléma szelet kezdési ideje. A dátum-idő kettős idézőjelek közé kell foglalni.
4. Részleteit a hiba, amely a következőhöz hasonló kimenetnek kell megjelennie:

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. Futtathatja a **Save-AzureRmDataFactoryLog** parancsmagot az azonosító értéke, hogy tekintse meg a kimenetben, és töltse le a naplófájlok segítségével a **- DownloadLogsoption** a parancsmaghoz.

    ```powershell
    Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>A folyamat hibák újrafuttatása

> [!IMPORTANT]
> Egyszerűbb legyen a hibák elhárítása, és futtassa újra a meghibásodott szeletek a Monitoring & Management App használatával. Az alkalmazás használatával kapcsolatos részletekért lásd: [figyelése és Data Factory-folyamatok felügyelete a Monitoring and Management app használatával](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Az Azure Portal használata
Miután hibaelhárítása és hibakeresése a folyamat sikertelen, hiba futtathatja ellenőrizheti, hogy a hiba szeletet, kattintson a **futtatása** gombra a parancssávon.

![Futtassa újra a meghibásodott szeletek](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Az esetben a szelet érvényesítése miatt meghiúsult a házirend-hiba (például, ha adatokat nem érhető el), javítsa ki a hibát, és ellenőrizze, hogy az újra gombra kattintva a **ellenőrzése** gombra a parancssávon.

![Javítsa a hibákat, és ellenőrzés](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Azure PowerShell használatával
Hibák használatával futtathatja a **Set-AzureRmDataFactorySliceStatus** parancsmagot. Tekintse meg a [Set-AzureRmDataFactorySliceStatus](https://docs.microsoft.com/powershell/module/azurerm.datafactories/set-azurermdatafactoryslicestatus) szintaxisát és egyéb részletek a parancsmaggal kapcsolatban a témakör.

**Példa**

Az alábbi példa állítja be a tábla összes szelet állapota "DAWikiAggregatedData" az Azure data Factory "WikiADF" a "Várakozás".

A "frissítés"típusa "Upstreaminpipeline paramétert", ami azt jelenti, hogy a tábla minden egyes szelet és minden függő (upstream) tábla állapotok vannak beállítva "Várakozás" értékre van állítva. Más lehetséges Ez a paraméter értéke "Egyéni".

```powershell
Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Riasztások létrehozása az Azure Portalon

1.  Jelentkezzen be az Azure Portalon, és válassza **figyelő -> riasztások** , nyissa meg a riasztások oldaláról.

    ![Nyissa meg a riasztások oldaláról.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Válassza ki **+ Új riasztási szabály** egy új riasztást létrehozni.

    ![Hozzon létre egy új riasztás](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Adja meg a **riasztási feltétel**. (Ügyeljen arra, hogy válassza ki, hogy **adat-előállítók** a a **szűrés erőforrástípus szerint** mezőben.) Az értékeket is megadhat **dimenziók**.

    ![Adja meg a riasztási feltétel - cél kiválasztása](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![Adja meg a riasztás feltétel – riasztási feltételek hozzáadása](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![Adja meg a riasztás feltétel – riasztási logika hozzáadása](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Adja meg a **riasztás részletei**.

    ![A riasztás részleteinek megadása](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Adja meg a **műveletcsoport**.

    ![A műveleti csoport megadása – új műveletcsoport létrehozása](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![A műveletcsoport - készlet tulajdonságainak meghatározása](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![A műveletcsoport - létrehozott új műveleti csoport megadása](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Adat-előállító áthelyezése másik erőforráscsoportba vagy előfizetésbe
Áthelyezheti egy adat-előállítót egy másik erőforráscsoportban vagy egy másik előfizetés használatával a **áthelyezése** sáv gombra az adat-előállító kezdőlapja a parancsot.

![Helyezze át a data factory](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Kapcsolódó erőforrásokat (például a riasztások az adat-előállítóhoz társított), az adat-előállító együtt is helyezheti.

![Helyezze át az erőforrások párbeszédpanel](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
