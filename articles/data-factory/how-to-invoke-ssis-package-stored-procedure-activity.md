---
title: SSIS-csomag futtatása a tárolt eljárási tevékenység – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan futtathat egy SQL Server Integration Services (SSIS) csomag az Azure Data Factory-folyamatot a tárolt eljárási tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: jingwang
ms.openlocfilehash: 1cff60e6134e08e4b9e59a9f69ec09700cca1814
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58098746"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>A tárolt eljárási tevékenységgel az Azure Data Factory SSIS-csomag futtatása
Ez a cikk ismerteti, hogyan futtathat egy SSIS-csomag az Azure Data Factory-folyamatot egy tárolt eljárási tevékenység használatával. 

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-sql-database"></a>Azure SQL Database 
Ebben a cikkben található útmutatások követéséhez használja az Azure SQL database, amelyen az SSIS-katalógus. Egy Azure SQL Database felügyelt példánya is használhatja.

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul létrehozása
Hozzon létre egy Azure-SSIS integrációs modult, ha még nincs fiókja, a részletes utasításokat a következő a [oktatóanyag: SSIS csomagok üzembe helyezése](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Data Factory felhasználói felülete (Azure portal)
Ebben a szakaszban a Data Factory felhasználói felülete egy tárolt eljárási tevékenység, amely SSIS-csomag elindítja a Data Factory-folyamatok létrehozásához használhatja.

### <a name="create-a-data-factory"></a>Data factory létrehozása
Első lépés az adat-előállító létrehozása az Azure portal használatával. 

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
2. Lépjen az [Azure Portalra](https://portal.azure.com). 
3. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Az **Új adat-előállító** lapon, a **Név** mezőben adja meg a következőt: **ADFTutorialDataFactory**. 
      
     ![Új adat-előállító lap](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a Név mezőnél az alábbi hiba jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialDataFactory). A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
     ![A név nem érhető el – hiba](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
4. Az **erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
   - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
   - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
     Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. A **Verzió** résznél válassza a **V2** értéket.
5. Válassza ki a Data Factory **helyét**. A legördülő listában csak a Data Factory által támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más helyeken is lehetnek.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Create** (Létrehozás) gombra.
8. Az irányítópulton a következő állapotleírás látható: **Data factory üzembe helyezése**. 

     ![adat-előállító üzembe helyezése csempe](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
     ![Data factory kezdőlap](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Az Azure Data Factory felhasználói felület (UI) alkalmazás külön lapon történő elindításához kattintson az **Létrehozás és monitorozás** csempére. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Tárolt eljárási tevékenység rendelkező folyamat létrehozása
Ebben a lépésben a Data Factory felhasználói felülete létrehoz egy folyamatot használja. Tárolt eljárás tevékenység hozzáadása a folyamatban, és konfigurálja azt az SSIS-csomag futtatása a sp_executesql tárolt eljárás használatával. 

1. Az első lépések oldalán kattintson **folyamat létrehozása**: 

    ![Első lépések lap](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. Az a **tevékenységek** eszközkészletben bontsa ki a **általános**, és húzza **tárolt eljárás** tevékenységet a folyamat tervezőfelületére. 

    ![Fogd és vidd tárolt eljárási tevékenység](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. A tárolt eljárási tevékenység tulajdonságok ablakában váltson a **SQL-fiók** fülre, majd **+ új**. Kapcsolatot hoz létre az Azure SQL Database üzemeltető az SSIS-katalógus (SSIDB-adatbázis). 
   
    ![Új társított szolgáltatás gomb](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. Az **Új társított szolgáltatás** ablakban végezze el az alábbi lépéseket: 

    1. Válassza ki **az Azure SQL Database** a **típus**.
    2. Válassza ki a **alapértelmezett** Azure integrációs modul csatlakozni az Azure SQL Database, amelyen a `SSISDB` adatbázis.
    3. Válassza ki az Azure SQL Database, az SSISDB adatbázist üzemeltető a **kiszolgálónév** mező.
    4. Válassza ki **SSISDB** a **adatbázisnév**.
    5. A **felhasználónév**, adja meg az adatbázishoz hozzáféréssel rendelkező felhasználó nevét.
    6. A **jelszó**, adja meg a felhasználó jelszavát. 
    7. Az adatbázis kapcsolatának teszteléséhez kattintson **kapcsolat tesztelése** gombra.
    8. A társított szolgáltatás mentéséhez kattintson a **mentése** gombra. 

        ![Azure SQL Database társított szolgáltatás](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. A Tulajdonságok ablakában váltson a **tárolt eljárás** lapról a **SQL-fiók** lapra, és kövesse az alábbi lépéseket: 

    1. Válassza a **Szerkesztés** elemet. 
    2. Az a **tárolt eljárás neveként** mezőben adjon meg `sp_executesql`. 
    3. Kattintson a **+ új** a a **tárolt eljárás paraméterei** szakaszban. 
    4. A **neve** adja meg a paraméter **utasításban**. 
    5. A **típus** adja meg a paraméter **karakterlánc**. 
    6. A **érték** paraméter, adja meg a következő SQL-lekérdezést:

        Az SQL-lekérdezésben, adja meg a megfelelő értékeit a **mappa_neve**, **projektnév**, és **csomag_neve** paramétereket. 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Azure SQL Database társított szolgáltatás](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. A folyamat konfiguráció érvényesítéséhez kattintson **ellenőrzése** az eszköztáron. A **Folyamatérvényesítési jelentés** bezárásához kattintson a **>>** jelre.

    ![Folyamat érvényesítése](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. A folyamat közzétételére az adat-előállító kattintva **összes közzététele** gombra. 

    ![Közzététel](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>Futtassa a és a folyamat figyelése
Ebben a szakaszban a folyamat futásának aktiválásához, és megfigyeli azt. 

1. Folyamat futásának aktiválásához kattintson **eseményindító** az eszköztáron, majd kattintson a **Aktiválás most**. 

    ![Aktiválás most](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. A **Folyamatfuttatás** ablakban kattintson a **Befejezés** gombra. 
3. Váltson a bal oldali **Monitorozás** lapra. Megjelenik a folyamat futtatása és egyéb információk (például a Futtatás kezdő időpont) és annak állapotát. A nézet frissítéséhez kattintson a **Frissítés** parancsra.

    ![Folyamatfuttatások](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Kattintson az **Actions** (Műveletek) oszlopban található **View Activity Runs** (Tevékenységfuttatások megtekintése) hivatkozásra. Láthatja, hogy csak egy tevékenységfuttatás, a folyamat egyetlen tevékenységgel (tárolt eljárás tevékenység) rendelkezik.

    ![Tevékenységfuttatások](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. Futtathatja a következő **lekérdezés** szemben az SSISDB adatbázis-az Azure SQL Serveren, ellenőrizze, hogy a csomag végrehajtása. 

    ```sql
    select * from catalog.executions
    ```

    ![Csomag végrehajtásának ellenőrzése](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Is létrehozhat ütemezett eseményindítóként a folyamathoz, hogy a folyamat fut egy ütemezés szerint (óránként, naponta stb.). Egy vonatkozó példáért lásd: [- adat-előállító létrehozása a Data Factory felhasználói felülete](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ebben a szakaszban az Azure PowerShell használatával hozzon létre egy Data Factory-folyamatot egy tárolt eljárási tevékenység, amely SSIS-csomag hív meg. 

Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-az-ps) ismertető cikkben szereplő utasításokat a legújabb Azure PowerShell-modulok telepítéséhez. 

### <a name="create-a-data-factory"></a>Data factory létrehozása
Az azonos adat-előállítót, amely rendelkezik az Azure-SSIS integrációs modul használata, vagy egy különálló adat-előállító létrehozásához. Az alábbi eljárás lépéseit egy adat-előállító létrehozásához. Egy tárolt eljárási tevékenység, a data factory-folyamatot hoz létre. A tárolt eljárási tevékenység végrehajt egy tárolt eljárást az SSISDB adatbázis futtatásához az SSIS-csomag. 

1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) nevét idézőjelek között, majd futtassa a parancsot. Például: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$ResourceGroupName` változóhoz, majd futtassa újra a parancsot
2. Futtassa az alábbi parancsot az Azure-erőforráscsoport létrehozásához: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$ResourceGroupName` változóhoz, majd futtassa újra a parancsot. 
3. Adjon meg egy változót az adat-előállító nevéhez. 

    > [!IMPORTANT]
    >  Frissítse az adat-előállító nevét, hogy globálisan egyedi legyen. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Az adat-előállító létrehozásához futtassa a következő **Set-AzDataFactoryV2** parancsmagot a $ResGrp változó Location és ResourceGroupName tulajdonság használatával: 
    
    ```powershell       
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Vegye figyelembe a következő szempontokat:

* Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory-példányok létrehozásához a felhasználói fióknak, amellyel belép az Azure-ba, a **közreműködő** vagy **tulajdonos** szerepkörök tagjának, vagy az Azure-előfizetés **rendszergazdájának** kell lennie.
* Azure-régióban, amelyben a Data Factory jelenleg listája, válassza ki a régiók, amelyek a következő oldalon érdeklődésére számot tartó, és bontsa ki **Analytics** található **adat-előállító**: [Régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/). Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database-beli társított szolgáltatás létrehozása
Hozzon létre egy társított szolgáltatást, az Azure SQL-adatbázis üzemeltető az SSIS-katalógus a data factoryjához. A Data Factory információkat használja ezt a társított szolgáltatást az SSISDB-adatbázishoz való csatlakozáshoz, és végrehajt egy tárolt eljárást az SSIS-csomag futtatásához. 

1. Hozzon létre egy JSON-fájlt **AzureSqlDatabaseLinkedService.json** a **C:\ADF\RunSSISPackage** mappában az alábbi tartalommal: 

    > [!IMPORTANT]
    > Cserélje le &lt;servername&gt;, &lt;felhasználónév&gt;, és &lt;jelszó&gt; értékeket az Azure SQL Database, a fájl mentése előtt.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
                }
            }
        }
    }
    ```

2. A **Azure PowerShell-lel**, váltson át a **C:\ADF\RunSSISPackage** mappát.

3. Futtassa a **Set-AzDataFactoryV2LinkedService** parancsmagot a társított szolgáltatás létrehozásához: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Tárolt eljárási tevékenység rendelkező folyamat létrehozása 
Ebben a lépésben létrehoz egy folyamatot egy tárolt eljárási tevékenység a. A tevékenység futtatása az SSIS-csomag sp_executesql tárolt eljárást hív meg. 

1. Hozzon létre egy JSON-fájlt **RunSSISPackagePipeline.json** a a **C:\ADF\RunSSISPackage** mappában az alábbi tartalommal:

    > [!IMPORTANT]
    > Cserélje le &lt;MAPPANEVET&gt;, &lt;PROJEKTNÉV&gt;, &lt;CSOMAGNÉV&gt; nevű mappa, projektek és az SSIS-katalógus a fájl mentése előtt a csomagot. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. A folyamat létrehozásához: **RunSSISPackagePipeline**futtassa a **Set-AzDataFactoryV2Pipeline** parancsmagot.

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Itt látható a minta kimenete:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Folyamat futásának létrehozása
Használja a **Invoke-AzDataFactoryV2Pipeline** parancsmagot futtathatja a folyamatot. A parancsmag visszaadja a folyamat futásának azonosítóját a későbbi monitorozás céljából.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

A folyamat futási állapotának folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez futtassa az alábbi PowerShell-szkriptet. Másolja/illessze be az alábbi szkriptet a PowerShell-ablakba, majd nyomja le az Enter billentyűt. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

### <a name="create-a-trigger"></a>Eseményindító létrehozása
Az előző lépésben a folyamat igény szerinti meghívása. A folyamatok futtatása ütemezés szerint (óránként, naponta stb.) az ütemezési eseményindító is létrehozhat.

1. Hozzon létre egy JSON-fájlt **MyTrigger.json** a **C:\ADF\RunSSISPackage** mappában az alábbi tartalommal: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. A **Azure PowerShell-lel**, váltson át a **C:\ADF\RunSSISPackage** mappát.
3. Futtassa a **Set-AzDataFactoryV2Trigger** parancsmagot, amely az eseményindítót hoz létre. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Alapértelmezés szerint az eseményindító leállított állapotban van. Az eseményindító elindításához futtassa a **Start-AzDataFactoryV2Trigger** parancsmagot. 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Győződjön meg arról, hogy az eseményindító futtatásával el van-e a **Get-AzDataFactoryV2Trigger** parancsmagot. 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Futtassa a következő parancsot a következő óra elteltével. Például ha az aktuális idő 3:25-kor (UTC), futtassa a parancsot, 4 Órakor (UTC). 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    Futtathatja a következő lekérdezés az SSISDB-adatbázison annak ellenőrzéséhez, hogy az Azure SQL Serveren a csomag végrehajtása. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>További lépések
Is figyelemmel kísérheti a folyamat az Azure portal használatával. Lépésenkénti útmutatásért lásd: [a folyamat figyelése](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).
