---
title: Folyamatok figyelése és kezelése az Azure Portal és a PowerShell használatával
description: Megtudhatja, hogyan használhatja az Azure Portalon és az Azure PowerShellt a létrehozott Azure-adatgyárak és folyamatok figyelésére és kezelésére.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 44aadecfa80524345932c03abb51e8ebd040a902
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73666969"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Az Azure Data Factory-folyamatok figyelése és kezelése az Azure Portal és a PowerShell használatával
> [!div class="op_single_selector"]
> * [Az Azure Portal/Az Azure PowerShell használata](data-factory-monitor-manage-pipelines.md)
> * [A Figyelés és kezelés alkalmazás használata](data-factory-monitor-manage-app.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el a [Data Factory folyamatok figyelését és kezelését a alkalmazásban.](../monitor-visually.md)

Ez a cikk ismerteti, hogyan figyelheti, kezelheti és hibakeresés a folyamatok az Azure Portal és a PowerShell használatával.

> [!IMPORTANT]
> A figyelési & felügyeleti alkalmazás jobb támogatást nyújt az adatfolyamatok figyeléséhez és kezeléséhez, valamint az esetleges problémák elhárításához. Az alkalmazás használatával kapcsolatos részletekért tekintse meg a [Data Factory folyamatok figyelését és kezelését a Figyelés i és felügyeleti alkalmazás használatával.](data-factory-monitor-manage-app.md) 

> [!IMPORTANT]
> Az Azure Data Factory 1-es verziója mostantól az új [Azure Monitor riasztási infrastruktúrát](../../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)használja. A régi riasztási infrastruktúra elavult. Ennek eredményeképpen az 1-es verziójú adatgyárakhoz konfigurált meglévő riasztások már nem működnek. A v1-es adatgyárak meglévő riasztásai nem kerülnek automatikus áttelepítésre. Ezeket a riasztásokat újra létre kell hoznia az új riasztási infrastruktúrán. Jelentkezzen be az Azure Portalon, és válassza **a Figyelő** lehetőséget, ha új riasztásokat hozhat létre metrikák (például sikertelen futtatások vagy sikeres futtatások) az 1-es verziójú adatgyárakhoz.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>A folyamatok és a tevékenységállapotok ismertetése
Az Azure Portal használatával a következőket teheti:

* Az adatgyár megtekintése diagramként.
* Folyamatok tevékenységeinek megtekintése.
* Bemeneti és kimeneti adatkészletek megtekintése.

Ez a szakasz azt is ismerteti, hogy az adatkészlet szeletek hogyan váltát egyik állapotból a másikba.   

### <a name="navigate-to-your-data-factory"></a>Navigálás az adat-előállítóra
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Kattintson az **Adatgyárak** parancsra a bal oldali menüben. Ha nem látja, kattintson a **További szolgáltatások >** elemre, majd kattintson az **Adatgyárak** elemre a **INTELLIGENCE + ANALYTICS** kategória alatt.

   ![Böngésszen az összes > adatgyárban](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. Az **adatgyárak** panelen válassza ki az önt érdeklő adatgyárat.

    ![Adat-előállító kiválasztása](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   Meg kell jelennie az adat-előállító kezdőlapjának.

   ![Adat-előállító panel](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Az adat-előállító diagramnézete
Az adat-előállító **diagram** nézete egyetlen üvegtáblát biztosít az adat-előállító és az eszközök figyelésére és kezelésére. Az adat-előállító **Diagram** nézetének megtekintéséhez kattintson az adat-előállító kezdőlapján a **Diagram** elemre.

![Diagramnézet](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

Nagyíthat, kicsinyíthet, nagyíthat, nagyíthat, 100%-ra nagyíthat, zárolhatja a diagram elrendezését, és automatikusan elhelyezheti a folyamatokat és adatkészleteket. Megtekintheti az adatvonal-információkat is (azaz a kijelölt cikkek felső és alsóbb rétegbeli tételeit).

### <a name="activities-inside-a-pipeline"></a>Folyamatokon belüli tevékenységek
1. Kattintson a jobb gombbal a folyamatra, majd kattintson a **Folyamat megnyitása** parancsra a folyamatban lévő összes tevékenység, valamint a tevékenységek bemeneti és kimeneti adatkészletei megtekintéséhez. Ez a funkció akkor hasznos, ha a folyamat egynél több tevékenységet tartalmaz, és meg szeretné érteni egy folyamat működési vonalát.

    ![Folyamat megnyitása menü](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. A következő példában egy másolási tevékenység jelenik meg a folyamatban egy bemeneti és egy kimeneti. 

    ![Folyamatokon belüli tevékenységek](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. Az adat-előállító kezdőlapjára úgy navigálhat vissza, hogy a bal felső sarokban lévő morzsa morzsa **adatgyárhivatkozására** kattint.

    ![Vissza az adat-előállítóba](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>A folyamaton belüli egyes tevékenységek állapotának megtekintése
Egy tevékenység aktuális állapotát a tevékenység által létrehozott adatkészletek állapotának megtekintésével tekintheti meg.

Ha duplán kattint a **Diagram** **OutputBlobTable** táblázatára, láthatja a különböző tevékenységek által a folyamaton belül futó összes szeletet. Láthatja, hogy a másolási tevékenység sikeresen futott az elmúlt nyolc órában, és kész **állapotban** hozta létre a szeleteket.  

![A csővezeték állapota](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Az adat-előállító adatkészletszeletek az alábbi állapotok egyikével rendelkezhetnek:

<table>
<tr>
    <th align="left">Állapot</th><th align="left">Alállam</th><th align="left">Leírás</th>
</tr>
<tr>
    <td rowspan="8">Várakozás</td><td>ScheduleTime (Ütemezési idő)</td><td>Még nem jött el az idő, hogy a szelet elfusson.</td>
</tr>
<tr>
<td>Adatkészletfüggőségek</td><td>A felső szintfüggő függőségek még nem állnak készen.</td>
</tr>
<tr>
<td>Számítási erőforrások</td><td>A számítási erőforrások nem érhetők el.</td>
</tr>
<tr>
<td>Egyidejűségi határ</td> <td>Az összes tevékenységpéldány más szeletek futtatásával van elfoglalva.</td>
</tr>
<tr>
<td>ActivityResume</td><td>A tevékenység szünetel, és nem futtatható a szeletek, amíg a tevékenység folytatódik.</td>
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
<td>A szelet feldolgozása folyamatban van.</td>
</tr>
<tr>
<td rowspan="4">Sikertelen</td><td>Időd-kiállás</td><td>A tevékenység végrehajtása tovább tartott, mint amit a tevékenység engedélyezett.</td>
</tr>
<tr>
<td>Megszakítva</td><td>A szelet felhasználói művelettel megszakította.</td>
</tr>
<tr>
<td>Ellenőrzés</td><td>Az érvényesítés nem sikerült.</td>
</tr>
<tr>
<td>-</td><td>A szelet nem jött létre és/vagy érvényesítve nem sikerült.</td>
</tr>
<td>Kész</td><td>-</td><td>A szelet fogyasztásra kész.</td>
</tr>
<tr>
<td>Kimarad</td><td>None</td><td>A szelet feldolgozása nem kerül feldolgozásra.</td>
</tr>
<tr>
<td>None</td><td>-</td><td>Egy másik állapotú szelet, de alaphelyzetbe lett állítva.</td>
</tr>
</table>



A szeletek részleteit úgy tekintheti meg, hogy a **Legutóbb frissített szeletek** panelen egy szeletbejegyzésre kattint.

![Szelet részletei](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Ha a szeletet többször is végrehajtották, a **Tevékenységfuttatások** listában több sor jelenik meg. A futtatott tevékenységek részleteit a **Tevékenységfuttatások** lista futtatási bejegyzésére kattintva tekintheti meg. A listában az összes naplófájl látható, valamint egy hibaüzenet, ha van ilyen. Ez a funkció akkor hasznos, ha az adat-előállító elhagyása nélkül is megtekintheti és debugolni szeretné a naplókat.

![Tevékenységfuttatás részletei](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Ha a szelet nem **Kész** állapotban van, láthatja azokat a felfelé lévő szeleteket, amelyek nem állnak készen, és blokkolják az aktuális szelet et az **upstream szeletekben, amelyek nem állnak készen.** Ez a funkció akkor hasznos, ha a szelet **várakozó** állapotban van, és meg szeretné érteni azokat a felsőáramfüggő függőségeket, amelyekre a szelet várakozik.

![Nem kész felfelé lévő szeletek](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Adatkészlet állapotábrája
Miután üzembe helyezett egy adat-előállító és a folyamatok egy érvényes aktív időszak, az adatkészlet szeletek átmenet egyik állapotból a másikba. Jelenleg a szelet állapota a következő állapotdiagramot követi:

![Állapotdiagram](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

Az adat-előállítóban az adatkészlet állapotátmeneti folyamata a következő: Waiting -> In-Progress/In-Progress (Érvényesítés) -> Kész/sikertelen.

A szelet **várakozási** állapotban kezdődik, és várja az előfeltételek teljesítését a végrehajtás előtt. Ezután a tevékenység elindul a végrehajtás, és a szelet folyamatban **állapotba** kerül. A tevékenység végrehajtása sikeres vagy sikertelen lehet. A szelet a végrehajtás eredménye alapján **Kész** vagy sikertelen ként van **megjelölve.**

A szelet alaphelyzetbe állítása a **Kész** vagy **sikertelen** állapotból **a Várakozás** állapotba való visszatéréshez. A szelet állapotát is kijelölheti **Kihagyás**néven, ami megakadályozza, hogy a tevékenység végrehajtsa, és ne dolgozza fel a szeletet.

## <a name="pause-and-resume-pipelines"></a>Folyamatok szüneteltetése és folytatása
A folyamatok az Azure PowerShell használatával kezelheti. Például szüneteltetheti és folytathatja a folyamatokat az Azure PowerShell-parancsmagok futtatásával. 

> [!NOTE] 
> A diagramnézet nem támogatja a folyamatok szüneteltetését és folytatását. Ha felhasználói felületet szeretne használni, használja a figyelési és kezelőalkalmazást. Az alkalmazás használatával kapcsolatos részletekért tekintse meg a [Data Factory-folyamatok figyelését és kezelését a Figyelési és felügyeleti alkalmazás cikkhasználatával.](data-factory-monitor-manage-app.md) 

Szüneteltetheti/felfüggesztheti a folyamatokat a **Suspend-AzDataFactoryPipeline** PowerShell parancsmag használatával. Ez a parancsmag akkor hasznos, ha nem szeretné futtatni a folyamatokat, amíg egy probléma nem oldható meg. 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Példa:

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Miután a problémát kijavították a folyamattal, a következő PowerShell-parancs futtatásával folytathatja a felfüggesztett folyamatot:

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Példa:

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Hibakeresési folyamatok
Az Azure Data Factory gazdag lehetőségeket biztosít a folyamatok hibakereséséhez és hibaelhárításához az Azure Portal és az Azure PowerShell használatával.

> [!NOTE] 
> Sokkal könnyebb a hibaesetén a Monitoring & Management alkalmazás használatával. Az alkalmazás használatával kapcsolatos részletekért tekintse meg a [Data Factory-folyamatok figyelését és kezelését a Figyelési és felügyeleti alkalmazás cikkhasználatával.](data-factory-monitor-manage-app.md) 

### <a name="find-errors-in-a-pipeline"></a>Hibák keresése egy folyamatban
Ha a tevékenység futtatása sikertelen egy folyamatban, a folyamat által létrehozott adatkészlet hibaállapotban van a hiba miatt. Az Azure Data Factory hibáit az alábbi módszerekkel hibakereséssel és hibaelhárítással teheti meg.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>Hiba hibakeresése az Azure Portalon
1. A **Táblázat** lapon kattintson arra a problémaszeletre, amelynek **állapota** **sikertelen.**

   ![Táblázatpenge problémás szelettel](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Az **Adatszelet** panelen kattintson a sikertelen tevékenységfuttatásra.

   ![Adatszelet hibával](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. A **Tevékenység futtatási részletek** panelen letöltheti a HDInsight-feldolgozáshoz társított fájlokat. Kattintson a **Letöltés** az állapothoz/stderr gombra a hibanapló-fájlt tartalmazó hibanapló fájl letöltéséhez.

   ![A tevékenység futtatási részleteinek panelje hibával](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>Hiba hibakeresése a PowerShell használatával
1. Indítsa el a **PowerShellt**.
2. A Slice szeletek és állapotuk megtekintéséhez futtassa a **Get-AzDataFactorySlice** parancsot. A sikertelen állapotú szeletnek meg kell **jelennie.**        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Példa:

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Cserélje le **a StartDateTime-ot** a folyamat kezdési időpontjára. 
3. Most futtassa a **Get-AzDataFactoryRun** parancsmag a szelet futó tevékenységrészleteinek megismeréséhez.

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Példa:

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    A StartDateTime értéke az előző lépésből észlelt hiba/probléma szelet kezdési időpontja. A dátum-idő dátum-idő kell mellékelni idézőjelek.
4. A következőhöz hasonló hiba részleteivel együtt a kimenetnek kell láthatónak lennie:

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
5. Futtathatja a **Save-AzDataFactoryLog** parancsmagát a kimenetből látható azonosító értékkel, és letöltheti a naplófájlokat a **parancsmag -DownloadLogsoption** használatával.

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Hibák újrafuttatása egy folyamatban

> [!IMPORTANT]
> A figyelési & felügyeleti alkalmazással egyszerűbben háríthatja el a hibákat, és futtathatja újra a sikertelen szeleteket. Az alkalmazás használatával kapcsolatos részletekért tekintse meg a [Data Factory folyamatok figyelését és kezelését a Figyelés i és felügyeleti alkalmazás használatával.](data-factory-monitor-manage-app.md) 

### <a name="use-the-azure-portal"></a>Az Azure Portal használata
Miután hibaelhárítási és hibakeresési hibák egy folyamatban, akkor újra futtathatja hibák at navigálás a hiba szeletet, és kattintson a **Futtatás** gombra a parancssávon.

![Sikertelen szelet újbóli futtatása](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Abban az esetben, ha a szelet érvényesítése házirend-hiba miatt sikertelen (például ha nem állnak rendelkezésre adatok), a hibát kijavíthatja, és újra érvényesítheti a parancssáv **Érvényesítés** gombjára kattintva.

![Hibák kijavítása és érvényesítése](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Azure PowerShell használatával
A hibák at a **Set-AzDataFactorySliceStatus** parancsmag használatával futtathatja újra. A [Set-AzDataFactorySliceStatus](https://docs.microsoft.com/powershell/module/az.datafactory/set-azdatafactoryslicestatus) témakörben a szintaxist és a parancsmag egyéb részleteit találja.

**Példa:**

A következő példa a "DAWikiAggregatedData" tábla összes szeletének állapotát "Várakozás" állapotra állítja az Azure -os "WikiADF" adatgyárban.

Az "UpdateType" beállítása "UpstreamInPipeline", ami azt jelenti, hogy a tábla és az összes függő (upstream) tábla állapota "Várakozás" értékre van állítva. A paraméter másik lehetséges értéke az "Egyén".

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Riasztások létrehozása az Azure Portalon

1.  Jelentkezzen be az Azure Portalon, és válassza **a figyelő -> riasztások** a riasztások lap megnyitásához.

    ![Nyissa meg a Riasztások lapot.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Új riasztás létrehozásához válassza a **+ Új riasztási szabályt.**

    ![Új riasztás létrehozása](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Adja meg a **riasztási feltételt**. (Ügyeljen arra, hogy a **Szűrő erőforrástípus szerint** mezőben válassza ki az **Adatgyárakat.)** A Dimenziók értéket is **megadhatja.**

    ![A riasztási feltétel meghatározása - Cél kiválasztása](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![A riasztási feltétel megadása - Riasztási feltételek hozzáadása](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![A riasztási feltétel megadása - Riasztási logika hozzáadása](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Adja meg a **riasztás részleteit**.

    ![A riasztás részleteinek meghatározása](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Adja meg a **Művelet csoportot**.

    ![A műveletcsoport definiálása – új műveletcsoport létrehozása](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![A műveletcsoport definiálása – tulajdonságok beállítása](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![A műveletcsoport definiálása – új műveletcsoport létrehozása](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Adatgyár áthelyezése másik erőforráscsoportba vagy előfizetésbe
Az adat-előállítók másik erőforráscsoportba vagy másik előfizetésbe helyezhető át az adat-előállító kezdőlapjának **Áthelyezés** parancssávgombjával.

![Adatgyár áthelyezése](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

A kapcsolódó erőforrásokat (például az adat-előállítóhoz társított riasztásokat) az adatsaránkkal együtt is áthelyezheti.

![Erőforrások áthelyezése párbeszédpanel](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
