---
title: SSIS-csomag futtatása tárolt eljárási tevékenységgel – Azure
description: Ez a cikk ismerteti, hogyan futtathat egy SQL Server Integration Services (SSIS) csomagot egy Azure Data Factory-folyamat a tárolt eljárás tevékenység használatával.
services: data-factory
documentationcenter: ''
author: swinarko
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: sawinark
ms.openlocfilehash: 063728c03c689c2eafec889bdee8276772ae685a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444035"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>SSIS-csomag futtatása a Tárolt eljárás tevékenységgel az Azure Data Factoryben
Ez a cikk ismerteti, hogyan futtathatja az SSIS-csomagot egy Azure Data Factory-folyamat egy tárolt eljárás tevékenység használatával. 

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-sql-database"></a>Azure SQL Database 
Ebben a cikkben a forgatókönyv egy Azure SQL-adatbázist használ, amely az SSIS-katalógust üzemelteti. Azure SQL Database felügyelt példányis használhatja.

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul létrehozása
Hozzon létre egy Azure-SSIS-integrációs futásidejűt, ha nem rendelkezik ilyenségsel az [oktatóanyag: SSIS-csomagok telepítése](tutorial-create-azure-ssis-runtime-portal.md)című útmutató lépésről lépésre történő követésével.

## <a name="data-factory-ui-azure-portal"></a>Adatfeldolgozó felhasználói felület (Azure portal)
Ebben a szakaszban a Data Factory felhasználói felülethasználatával hozzon létre egy Data Factory-folyamatot egy tárolt eljárástevékenységgel, amely meghívja az SSIS-csomagot.

### <a name="create-a-data-factory"></a>Data factory létrehozása
Az első lépés egy adat-előállító létrehozása az Azure Portal használatával. 

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
2. Nyissa meg az [Azure Portalt.](https://portal.azure.com) 
3. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Az **Új adat-előállító** lapon, a **Név** mezőben adja meg a következőt: **ADFTutorialDataFactory**. 
      
     ![Új adat-előállító lap](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Az Azure-adat-előállító nevének **globálisan egyedinek**kell lennie. Ha a Név mezőnél az alábbi hiba jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialDataFactory). A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
     ![A név nem érhető el – hiba](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
4. Az **erőforráscsoport esetében**tegye az alábbi lépések egyikét:
     
   - Válassza **a Meglévő használata**lehetőséget, és válasszon ki egy meglévő erőforráscsoportot a legördülő listából. 
   - Válassza **az Új létrehozása**lehetőséget, és írja be egy erőforráscsoport nevét.   
         
     Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/management/overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. A **Verzió** résznél válassza a **V2** értéket.
5. Válassza ki a Data Factory **helyét**. A legördülő listában csak a Data Factory által támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más helyeken is lehetnek.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson **a Létrehozás gombra.**
8. Az irányítópulton a következő csempe jelenik meg állapottal: **Adatgyár telepítése**. 

     ![adat-előállító üzembe helyezése csempe](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
     ![Data factory kezdőlap](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Az Azure Data Factory felhasználói felület (UI) alkalmazás külön lapon történő elindításához kattintson az **Létrehozás és monitorozás** csempére. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Tárolt eljárási tevékenységgel rendelkező folyamat létrehozása
Ebben a lépésben a Data Factory felhasználói felület használatával hozzon létre egy folyamatot. Hozzáad egy tárolt eljárási tevékenységet a folyamathoz, és úgy konfigurálja, hogy az SSIS-csomagot a sp_executesql tárolt eljárással futtassa. 

1. Az első lépések lapon kattintson a **Folyamat létrehozása gombra:** 

    ![Első lépések lap](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. A **Tevékenységek** eszközkészletben bontsa ki az **Általános**és a húzással tárolt eljárás idotartamát a folyamattervező felületére. **Stored Procedure** 

    ![A tárolt eljárás idovel végzett tevékenysége](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. A tárolt eljárási tevékenység tulajdonságablakában váltson az **SQL-fiók** lapra, és kattintson a **+ Új**gombra. Hozzon létre egy kapcsolatot az Azure SQL-adatbázis, amely az SSIS katalógus (SSIDB adatbázis) található. 
   
    ![Új társított szolgáltatás gomb](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. A **New Linked Service** (Új társított szolgáltatás) ablakban végezze el az alábbi lépéseket: 

    1. Válassza az **Azure SQL Database** for Type **lehetőséget.**
    2. Válassza ki az **alapértelmezett** Azure-integrációs futásidejű csatlakozni `SSISDB` az Azure SQL-adatbázis, amely az adatbázist.
    3. Válassza ki az Azure SQL-adatbázist, amely az SSISDB-adatbázist **üzemelteti** a Kiszolgáló név mezőjéhez.
    4. Válassza az **SSISDB** lehetőséget az **adatbázis nevéhez.**
    5. A **Felhasználónév**mezőbe írja be az adatbázishoz hozzáféréssel rendelkező felhasználó nevét.
    6. A **Jelszó**mezőbe írja be a felhasználó jelszavát. 
    7. Tesztelje az adatbázishoz való csatlakozást a **Kapcsolat tesztelése** gombra kattintva.
    8. Mentse a csatolt szolgáltatást a **Mentés** gombra kattintva. 

        ![Azure SQL Database társított szolgáltatás](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. A Tulajdonságok ablakban váltson az **SQL-fiók** lap **Tárolt eljárás** lapjára, és hajtsa végre az alábbi lépéseket: 

    1. Válassza a **Szerkesztés** elemet. 
    2. A **Tárolt eljárás neve** mezőben adja meg a beírt értéket. `sp_executesql` 
    3. Kattintson a **+ Új** gombra a **Tárolt eljárás paraméterei** szakaszban. 
    4. A paraméter **nevéhez** írja be az **stmt**. 
    5. A paraméter **típusának** **megadása**String . 
    6. A paraméter **értékéhez** írja be a következő SQL-lekérdezést:

        Az SQL lekérdezésben adja meg a **folder_name**, **project_name**és **package_name** paraméterek megfelelő értékeit. 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Azure SQL Database társított szolgáltatás](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. A folyamatkonfiguráció érvényesítéséhez kattintson az eszköztár **Érvényesítés** gombjára. A **Folyamatérvényesítési jelentés** bezárásához kattintson a **>>** jelre.

    ![Folyamat érvényesítése](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Tegye közzé a folyamatot a Data Factory ban az **Összes közzététele** gombra kattintva. 

    ![Közzététel](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>A folyamat futtatása és figyelése
Ebben a szakaszban elindítja a folyamat futását, majd figyelje azt. 

1. A folyamatfuttatás elindításához kattintson az eszköztár **Eseményindító** gombjára, majd az **Eseményindító gombra.** 

    ![Aktiválás most](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. A **Folyamatfuttatás** ablakban kattintson a **Befejezés** gombra. 
3. Váltson a bal oldali **Monitorozás** lapra. Megjelenik a folyamat futása és állapota más információkkal (például a futtatás kezdési időpontjával) együtt. A nézet frissítéséhez kattintson a **Frissítés** parancsra.

    ![Folyamatfuttatások](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Kattintson az **Actions** (Műveletek) oszlopban található **View Activity Runs** (Tevékenységfuttatások megtekintése) hivatkozásra. Csak egy tevékenységfuttatást lát, mivel a folyamat csak egy tevékenységgel rendelkezik (tárolt eljárási tevékenység).

    ![Tevékenységfuttatások](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. A következő **lekérdezést** futtathatja az SSISDB-adatbázisban az Azure SQL-kiszolgálón, és ellenőrizheti, hogy a csomag végrehajtása. 

    ```sql
    select * from catalog.executions
    ```

    ![Csomagvégrehajtás-végrehajtások ellenőrzése](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Ütemezett eseményindítót is létrehozhat a folyamathoz, hogy a folyamat ütemezés szerint (óránként, naponta stb.) futhasson. Például lásd: [Adatgyár létrehozása - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ebben a szakaszban az Azure PowerShell használatával hozzon létre egy Data Factory-folyamat egy tárolt eljárástevékenység, amely meghívja az SSIS-csomagot. 

Telepítse a legújabb Azure PowerShell-modulokat az [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-az-ps)című témakörutasításait követve. 

### <a name="create-a-data-factory"></a>Data factory létrehozása
Használhatja ugyanazt az adat-előállító, amely rendelkezik az Azure-SSIS IR, vagy hozzon létre egy külön adat-előállító. Az alábbi eljárás az adat-előállító létrehozásának lépéseit ismerteti. Ebben az adat-előállítóban egy tárolt eljárási tevékenységgel rendelkező folyamatot hoz létre. A tárolt eljárás tevékenység az SSISDB-adatbázisban tárolt eljárást hajt végre az SSIS-csomag futtatásához. 

1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) nevét idézőjelek között, majd futtassa a parancsot. Például: `"adfrg"`. 
   
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

5. Az adat-előállító létrehozásához futtassa a következő **Set-AzDataFactoryV2** parancsmagát a $ResGrp változó Hely és ResourceGroupName tulajdonságával: 
    
    ```powershell       
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Vegye figyelembe a következő szempontokat:

* Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory-példányok létrehozásához a felhasználói fióknak, amellyel belép az Azure-ba, a **közreműködő** vagy **tulajdonos** szerepkörök tagjának, vagy az Azure-előfizetés **rendszergazdájának** kell lennie.
* Azon Azure-régiók megtekintéséhez, amelyekben jelenleg elérhető a Data Factory, a következő lapon válassza ki az Önt érdeklő régiókat, majd bontsa ki az **Elemzés** részt, és keresse meg a **Data Factory**: [Elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) szakaszt. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database-beli társított szolgáltatás létrehozása
Hozzon létre egy összekapcsolt szolgáltatást az SSIS-katalógust tároló Azure SQL-adatbázis és az adat-előállító összekapcsolására. A Data Factory a csatolt szolgáltatás adatait használja az SSISDB adatbázishoz való csatlakozáshoz, és egy tárolt eljárást hajt végre egy SSIS-csomag futtatásához. 

1. Hozzon létre egy **AzureSqlDatabaseLinkedService.json** nevű JSON-fájlt a **C:\ADF\RunSSISPackage** mappában a következő tartalommal: 

    > [!IMPORTANT]
    > A &lt;fájl&gt; &lt;mentése&gt;előtt &lt;cserélje&gt; le a kiszolgálónevet , a felhasználónevet és a jelszót az Azure SQL Database értékeire.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. Az **Azure PowerShellben**váltson a **C:\ADF\RunSSISPackage** mappára.

3. Futtassa a **Set-AzDataFactoryV2LinkedService** parancsmagot a csatolt szolgáltatás létrehozásához: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Tárolt eljárási tevékenységgel rendelkező folyamat létrehozása 
Ebben a lépésben hozzon létre egy folyamatot tárolt eljárási tevékenységgel. A tevékenység meghívja a sp_executesql tárolt eljárást az SSIS-csomag futtatásához. 

1. Hozzon létre egy **RunSSISPackagePipeline.json** nevű JSON-fájlt a **C:\ADF\RunSSISPackage** mappában a következő tartalommal:

    > [!IMPORTANT]
    > A &lt;&gt;mappanév &lt;,&gt; &lt;a&gt; PROJEKT NEVE , A CSOMAGNEVE lecserélése az SSIS-katalógusban lévő mappa, projekt és csomag nevére a fájl mentése előtt. 

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

2. A folyamat létrehozásához: **RunSSISPackagePipeline**, Futtassa a **Set-AzDataFactoryV2Pipeline** parancsmag.

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
Használja az **Invoke-AzDataFactoryV2Pipeline** parancsmag a folyamat futtatásához. A parancsmag visszaadja a folyamat futásának azonosítóját a későbbi monitorozás céljából.

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
Az előző lépésben meghívta a folyamat igény szerint. Ütemezési eseményindítót is létrehozhat a folyamat ütemezés szerinti futtatásához (óránként, naponta stb.).

1. Hozzon létre egy **MyTrigger.json** nevű JSON-fájlt a **C:\ADF\RunSSISPackage** mappában a következő tartalommal: 

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
2. Az **Azure PowerShellben**váltson a **C:\ADF\RunSSISPackage** mappára.
3. Futtassa a **Set-AzDataFactoryV2Trigger** parancsmast, amely létrehozza az eseményindítót. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Alapértelmezés szerint az eseményindító leállított állapotban van. Indítsa el az eseményindítót a **Start-AzDataFactoryV2Trigger** parancsmag futtatásával. 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Ellenőrizze, hogy az eseményindító **elindul-e a Get-AzDataFactoryV2Trigger** parancsmag futtatásával. 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Futtassa a következő parancsot a következő óra után. Ha például az aktuális idő 15:25-kor (UTC) van, futtassa a parancsot 16:00-kor UTC-kor. 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    A következő lekérdezést futtathatja az SSISDB-adatbázisban az Azure SQL-kiszolgálón, és ellenőrizheti, hogy a csomag végrehajtása. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>További lépések
A folyamat az Azure Portalhasználatával is figyelheti. A részletes útmutatásról a [Folyamat figyelése](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)című témakörben talál.
