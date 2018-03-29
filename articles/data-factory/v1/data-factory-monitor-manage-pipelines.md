---
title: Figyelheti és folyamatok kezelése az Azure portál és a PowerShell használatával |} Microsoft Docs
description: Útmutató az Azure portál és az Azure PowerShell használatával az Azure adat-előállítók és a folyamatok létrehozott felügyeletét és kezelését.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 9b0fdc59-5bbe-44d1-9ebc-8be14d44def9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 94b3c1e812bdf3345d5fb1f7308fb7a55be8f922
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Figyelheti és kezelheti az Azure Data Factory-folyamatok az Azure portál és a PowerShell használatával
> [!div class="op_single_selector"]
> * [Az Azure portálon vagy az Azure PowerShell használatával](data-factory-monitor-manage-pipelines.md)
> * [Használatával figyelése és a felügyeleti alkalmazás](data-factory-monitor-manage-app.md)

> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [felügyeletéhez és kezeléséhez az adat-előállító adatcsatornák 2-es](../monitor-visually.md).

> [!IMPORTANT]
> A figyelés a & felügyeleti alkalmazás figyelése és az adatok folyamatok kezelését és hibaelhárítását probléma merül fel egy jobb támogatást biztosít. Az alkalmazás használatával kapcsolatos részletekért lásd: [felügyeletéhez és kezeléséhez az adat-előállító adatcsatornák a figyelés és felügyelet alkalmazással](data-factory-monitor-manage-app.md). 


Ez a cikk ismerteti, hogyan figyeléséhez, kezeléséhez és a folyamatok hibakeresése az Azure-portál és a PowerShell használatával.

## <a name="understand-pipelines-and-activity-states"></a>Adatcsatornák és a tevékenység állapota
Az Azure portál használatával a következő műveletek végezhetők el:

* Megtekintheti a data factory mint ábrázoló diagram.
* A feldolgozási soros tevékenységek megtekintése.
* Bemeneti és kimeneti adatkészletek megtekintése.

Ez a szakasz azt is ismerteti, hogyan dataset szelet átkerül egy állapotból egy másik állapotba.   

### <a name="navigate-to-your-data-factory"></a>Keresse meg a data factory
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **adat-előállítók** a bal oldali menüben. Ha nem látja, kattintson a **további szolgáltatások >**, és kattintson a **adat-előállítók** alatt a **ESZKÖZINTELLIGENCIA + ANALITIKA** kategóriát.

   ![Keresse meg az összes > adat-előállítók](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. Az a **adat-előállítók** panelen válassza ki az adat-előállító kíváncsiak vagyunk.

    ![Adat-előállító kiválasztása](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   A data factory kezdőlapjának kell megjelennie.

   ![Adat-előállító panel](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>A data factory a diagram nézet
A **Diagram** egy adat-előállító nézete egytáblás üveg felügyeletéhez és a data factory és az eszközök kezeléséhez. Megtekintéséhez a **Diagram** , a data factory megtekintéséhez kattintson az **Diagram** a kezdőlapon az adat-előállítóban.

![Diagramnézet](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Nagyítás, Kicsinyítés, nagyítás elférjen, 100 %-os nagyítás, a diagram elrendezését zárolása és adatcsatornákat és adathalmazokat automatikus formázása. Megtekintheti az Leszármaztatás adatait is (Ez azt jelenti, hogy a kijelölt elemek felsőbb és alsóbb rétegbeli elemek megjelenítése).

### <a name="activities-inside-a-pipeline"></a>Egy folyamat belüli tevékenységek
1. Kattintson a jobb gombbal a folyamatot, és kattintson **nyitott folyamat** a sorban, a tevékenységekhez tartozó bemeneti és kimeneti adatkészletek együtt az összes tevékenység megtekintéséhez. Ez a szolgáltatás akkor hasznos, ha a feldolgozási sor egynél több tevékenységet tartalmaz, és szeretné tudni, hogy a működési Leszármaztatás egyetlen folyamat.

    ![Folyamat megnyitása menü](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. Az alábbi példában látható a másolási tevékenység során a folyamat a bemeneti és egy kimeneti. 

    ![Egy folyamat belüli tevékenységek](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Lépjen vissza az adat-előállítóban kezdőlapján kattintson a **adat-előállító** a webhely-navigációs a bal felső sarokban lévő hivatkozásra.

    ![Lépjen vissza az adat-előállító](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>Egy folyamat belül minden tevékenység állapotának megtekintése
A tevékenység jelenlegi állapotában is megtekintheti az adatkészleteket, amelyek a tevékenység által létrehozott bármelyikét állapotának megtekintése.

Ehhez kattintson duplán a **OutputBlobTable** a a **Diagram**, megtekintheti a szeleteket, amelyek egy folyamat belül különböző tevékenység fut hozzák létre. Láthatja, hogy a másolási tevékenység futott sikeresen a az utolsó nyolc óra, és a szeleteket előállított a **készen** állapotát.  

![A folyamat állapota](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

A dataset szeletek adat-előállító a következő állapotok egyike lehet:

<table>
<tr>
    <th align="left">Állapot</th><th align="left">Alállapot</th><th align="left">Leírás</th>
</tr>
<tr>
    <td rowspan="8">Várakozás</td><td>ScheduleTime</td><td>A szelet futtatásának időpontjában még nem érkezett.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>A fölérendelt függőségek nem állnak készen.</td>
</tr>
<tr>
<td>ComputeResources</td><td>A számítási erőforrások nem érhetők el.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Az összes tevékenységpéldány más szeletek futtatásával van elfoglalva.</td>
</tr>
<tr>
<td>ActivityResume</td><td>A tevékenység szüneteltetve van, és nem tudja futtatni a szeleteket, amíg a tevékenység folytatja a működését.</td>
</tr>
<tr>
<td>Retry</td><td>Tevékenység végrehajtási lesz hajtva.</td>
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
<td>A szelet feldolgozása folyamatban van.</td>
</tr>
<tr>
<td rowspan="4">Meghiúsult</td><td>Időtúllépésbe került</td><td>A tevékenység végrehajtási tevékenység által megengedett érték időt vett igénybe.</td>
</tr>
<tr>
<td>Törölve</td><td>A szelet felhasználói művelet megszakította.</td>
</tr>
<tr>
<td>Ellenőrzés</td><td>Az érvényesítés nem sikerült.</td>
</tr>
<tr>
<td>-</td><td>A szelet jön létre, illetve érvényesítése sikertelen.</td>
</tr>
<td>Kész</td><td>-</td><td>A szelet készen áll a felhasználásra.</td>
</tr>
<tr>
<td>Kihagyva</td><td>None</td><td>A szelet feldolgozása folyamatban nem.</td>
</tr>
<tr>
<td>None</td><td>-</td><td>A szelet használt létezett egy eltérő állapottal, de a rendszer visszaállította.</td>
</tr>
</table>



A szelet bejegyzés kattintson a szelet részleteit is megtekintheti a **szeletek nemrég frissített** panelen.

![Szelet részletei](./media/data-factory-monitor-manage-pipelines/slice-details.png)

A szelet több alkalommal végre lett hajtva, ha látja-e a több sort a **tevékenység fut** listája. Futtassa a futtatási bejegyzést kattintva tevékenységgel kapcsolatos részleteket a **tevékenység fut** listája. A lista tartalmazza az összes naplófájlt, valamint egy hibaüzenet, ha van ilyen. Ez a szolgáltatás akkor hasznos, megtekintése és hibakeresési naplókat, anélkül, hogy a data factory.

![Tevékenységfuttatás részletei](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Ha a szelet nem szerepel a **készen** állapot, megtekintheti a felfelé irányuló szeletek nem állnak készen, és az aktuális szelet végrehajtás alatt blokkolják a **felfelé irányuló szeletek nem áll készen** listája. A szolgáltatás akkor hasznos, ha a szelet **Várakozás** állapotát, és szeretné tudni, hogy a szelet vár a fölérendelt függőségek.

![Nem kész állapotú felfelé irányuló szeletek](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>A DataSet állapot diagramja
Miután telepít egy adat-előállítót, és a folyamatok érvényes aktív idővel rendelkeznek, az adatkészlet átmenet egy állapot másik szeletek. A szelet állapota jelenleg a következő ábra állapotát követi:

![Állapotdiagram](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

A dataset állapot átmenet folyamata adat-előállítót a rendszeren a következő: várakozás közben a rendszer -> folyamatban lévő vagy a-folyamatban (érvényesítés) -> kész/nem sikerült.

A szelet elindul egy **Várakozás** állapot, Várakozás a végrehajtása előtt teljesítendő előfeltételek. Ezután a tevékenység végrehajtásának elkezdése, és a szelet kerülnek egy **folyamatban** állapotát. A tevékenység végrehajtási előfordulhat, hogy sikeres vagy sikertelen. A szelet jelölésű **készen** vagy **sikertelen**a végrehajtás eredménye alapján.

Visszaállíthatja a szelet lépjen vissza a a **készen** vagy **sikertelen** állapotát a **Várakozás** állapotát. A szelet állapot is jelölheti **kihagyása**, amely megakadályozza, hogy a tevékenység végrehajtása, és nem az a szelet feldolgozása.

## <a name="pause-and-resume-pipelines"></a>Felfüggesztése és folytatása folyamatok
A folyamatok Azure PowerShell használatával kezelheti. Például szüneteltetése és folytatása folyamatok Azure PowerShell-parancsmag futtatásával. 

> [!NOTE] 
> A diagram nézet nem támogatja a felfüggesztése és folytatása folyamatok. Ha szeretne egy felhasználói felületet használnak, használja a figyelési és kezelését alkalmazást. Az alkalmazás használatával kapcsolatos részletekért lásd: [felügyeletéhez és kezeléséhez az adat-előállító adatcsatornák a figyelés és felügyelet alkalmazással](data-factory-monitor-manage-app.md) cikk. 

Is szüneteltetése vagy felfüggesztjük folyamatok használatával a **Suspend-AzureRmDataFactoryPipeline** PowerShell-parancsmagot. Ez a parancsmag akkor hasznos, ha nem szeretné futtatni a folyamatok, a probléma a megoldásáig. 

```powershell
Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Példa:

```powershell
Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

A probléma kijavítása a a folyamat, után folytathatja a felfüggesztett folyamat a következő PowerShell-parancs futtatásával:

```powershell
Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Példa:

```powershell
Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Folyamatok hibakeresése
Az Azure Data Factory debug vagy folyamatok hibaelhárítása az Azure portál és az Azure PowerShell használatával gazdag lehetőségeket kínál.

> [! Megjegyzés:} sokkal könnyebb a figyelés és a felügyeleti alkalmazás troubleshot hibák. Az alkalmazás használatával kapcsolatos részletekért lásd: [felügyeletéhez és kezeléséhez az adat-előállító adatcsatornák a figyelés és felügyelet alkalmazással](data-factory-monitor-manage-app.md) cikk. 

### <a name="find-errors-in-a-pipeline"></a>Hiba található egy folyamatot
Ha a tevékenység futtatása sikertelen, a folyamat, a folyamat által létrehozott adatkészlet állapotban van hiba hibája miatt. Hibakeresés, és az Azure Data Factory hibáinak elhárítása az alábbi módszerek segítségével.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Hiba történt az Azure portál használata
1. Az a **tábla** panelen kattintson a probléma szelet, amely rendelkezik a **állapot** beállítása **sikertelen**.

   ![Probléma szelet tábla panelről](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Az a **adatszelet** panelen, kattintson a tevékenység futtatása sikertelen.

   ![Hiba történt az adatszelet](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. Az a **tevékenység fut részletek** panelen letöltheti a fájlokat, társított HDInsight feldolgozása. Kattintson a **letöltése** az állapot/stderr letölteni a hibanaplófájlt, amely tartalmazza a hiba részleteit.

   ![A következő hiba tevékenységfuttatási részleteit megjelenítő panelen](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>A PowerShell szolgáltatás használatával debug hiba
1. Indítsa el a **PowerShellt**.
2. Futtassa a **Get-AzureRmDataFactorySlice** parancsot a szeletek és azok állapotának megtekintéséhez. A szelet és az állapotuk kell megjelennie **sikertelen**.        

    ```powershell   
    Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Példa:

    ```powershell   
    Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Cserélje le **StartDateTime** az a folyamat kezdési idejét. 
3. Futtassa a **Get-AzureRmDataFactoryRun** parancsmag részletes információkat a tevékenység Futtatás újrapróbálása.

    ```powershell   
    Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Példa:

    ```powershell   
    Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    StartDateTime értéke a hiba vagy probléma szelet, amely az előző lépésben feljegyzett kezdési idejét. A dátum-idő dupla idézőjelek között kell megadni.
4. Adatokkal kapcsolatban, amelyek a következőhöz hasonló kimenetnek kell megjelennie:

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
5. Futtathatja a **mentés-AzureRmDataFactoryLog** parancsmagot az azonosító értéke, tekintse meg a kimenetben, és a naplófájlok használva töltik le a **- DownloadLogsoption** a parancsmaghoz.

    ```powershell
    Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Futtassa újra a feldolgozási hibák

> [!IMPORTANT]
> Célszerűbb hibáinak elhárítása, és futtassa újra a sikertelen szeletek a figyelés & a felügyeleti alkalmazás használatával. Az alkalmazás használatával kapcsolatos részletekért lásd: [felügyeletéhez és kezeléséhez az adat-előállító adatcsatornák a figyelés és felügyelet alkalmazással](data-factory-monitor-manage-app.md). 

### <a name="use-the-azure-portal"></a>Az Azure Portal használata
Hibaelhárítás és a feldolgozási hibák hibakeresése, után ismét lefuttathatja hibák hiba újrapróbálása megnyitásával, majd kattintson a **futtatása** gombra a parancssávon.

![Futtassa újra a hibás szeletet](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Esetében a szelet meghiúsult érvényesítési házirend hiba miatt (Ha például adatok nem érhető el), javítsa ki a hibát, és ellenőrizze, hogy az újra gombra kattintva a **ellenőrzése** gombra a parancssávon.

![Javítsa a hibákat, és érvényesítése](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Azure PowerShell használatával
Hibák használatával ismét lefuttathatja a **Set-AzureRmDataFactorySliceStatus** parancsmag. Tekintse meg a [Set-AzureRmDataFactorySliceStatus](https://msdn.microsoft.com/library/mt603522.aspx) szintaxisát és egyéb adatait a parancsmag a témakörben.

**Példa**

Az alábbi példa állítja be a következő táblázatban minden szeletek állapotának "DAWikiAggregatedData" "Várakozási" az Azure data factory "WikiADF".

A "frissítés"típusa "Upstreaminpipeline paramétert", ami azt jelenti, hogy a tábla minden egyes szeletet és minden függő (fölérendelt) tábla állapotok van beállítva "Vár" értékre van állítva. Más lehetséges Ez a paraméter értéke "Egyéni".

```powershell
Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Egy adat-előállító áthelyezése egy másik erőforráscsoportban vagy előfizetés
Áthelyezheti egy adat-előállító egy másik erőforráscsoportban vagy egy másik előfizetésben található használatával a **áthelyezése** sáv gombjára a data factory kezdőlapján parancsot.

![Adat-előállító áthelyezése](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

Át is helyezheti a kapcsolódó erőforrások (például a riasztások az adat-előállítóban társított), és az adat-előállítóban.

![Források párbeszédpanelt](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
