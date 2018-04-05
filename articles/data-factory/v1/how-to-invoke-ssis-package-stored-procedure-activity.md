---
title: SSIS-csomag használata az Azure Data Factory - tárolt eljárási tevékenység meghívása |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan lehet meghívni egy Azure Data Factory-folyamat a tárolt eljárási tevékenység használja az SQL Server Integration Services (SSIS) csomag.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: article
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 309a64c47eb95ca2ca05bb82bc1c41256305823d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Egy SSIS-csomagot, a tárolt eljárási tevékenység az Azure Data Factory meghívása
Ez a cikk ismerteti, hogyan lehet meghívni egy SSIS-csomagot az Azure Data Factory-folyamat a tárolt eljárási tevékenység használatával. 

> [!NOTE]
> Ez a cikk vonatkozik 1 a Data Factory általánosan elérhető. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely a nyilvános előzetes verziójához, használatakor [meghívása SSIS-csomagok használata a tárolt eljárási tevékenység a 2](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-sql-database"></a>Azure SQL Database 
Ez a cikk a forgatókönyv, amelyen az SSIS-katalógus Azure SQL-adatbázis. Egy Azure SQL felügyelt példányát (előzetes verzió) is használhatja.

### <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul létrehozása
Ha még nem rendelkezik a részletes utasításokat a következő, hozzon létre egy Azure-SSIS-integrációs futásidejű a [oktatóanyag: telepítése SSIS-csomagok](../tutorial-create-azure-ssis-runtime-portal.md). Egy adat-előállító létrehozása az Azure-SSIS-integrációs futásidejű 2-es verzióját, létre kell hoznia. 

## <a name="azure-portal"></a>Azure Portal
Ebben a szakaszban az Azure-portálon a tárolt eljárás tevékenység, amely hívja meg az SSIS-csomag létrehozása a Data Factory-folyamat használja.

### <a name="create-a-data-factory"></a>Data factory létrehozása
Első lépés az adat-előállító létrehozása az Azure-portál használatával. 

1. Lépjen az [Azure Portalra](https://portal.azure.com). 
2. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Az **Új adat-előállító** lapon, a **Név** mezőben adja meg a következőt: **ADFTutorialDataFactory**. 
      
     ![Új adat-előállító lap](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a Név mezőnél az alábbi hiba jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialDataFactory). A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](data-factory-naming-rules.md) ismertető cikkben találja.

    `Data factory name ADFTutorialDataFactory is not available`
3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
4. Az **erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
      - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
      - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. Válassza ki **V1** a a **verzió**.
5. Válassza ki a Data Factory **helyét**. A legördülő listában csak a Data Factory által támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más helyeken is lehetnek.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Create** (Létrehozás) gombra.
8. Az irányítópulton megjelenő csempén a következő állapotleírás látható: **Adat-előállító üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
    ![Data factory kezdőlap](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Kattintson a **Szerző és központi telepítése** indítsa el a Data Factory Editor csempére.

    ![A Data Factory szerkesztője](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-editor.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database-beli társított szolgáltatás létrehozása
A társított szolgáltatás, amely futtatja az Azure SQL database összekapcsolására. az SSIS-katalógusban a data factory létrehozása. Data Factory szolgáltatásnak információk használatával kapcsolódik az SSISDB adatbázist, és végrehajtja a tárolt eljárás egy SSIS-csomag futtatásához. 

1. Kattintson a Data Factory Editor **az új adattároló** a menüben, majd kattintson a **Azure SQL Database**. 

    ![Az új adattároló -> Azure SQL adatbázis](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-sql-database-linked-service-menu.png)
2. A jobb oldali ablaktáblában tegye a következőket:

    1. Cserélje le `<servername>` az Azure SQL server nevével. 
    2. Cserélje le `<databasename>` rendelkező **SSISDB** (a katalógus SSIS-adatbázis neve). 
    3. Cserélje le `<username@servername>` a felhasználó, aki hozzáfér az Azure SQL Server nevével. 
    4. Cserélje le `<password>` rendelkező felhasználó jelszavát. 
    5. A társított szolgáltatás telepítése gombra kattintva a **telepítés** gomb az eszköztáron. 

        ![Azure SQL Database társított szolgáltatás](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-definition.png)

### <a name="create-a-dummy-dataset-for-output"></a>Hozzon létre egy üres kimeneti adatkészletet
A kimeneti adatkészlet dummy adatkészletre mutató ütemezését a folyamat a meghajtók. Figyelje meg, hogy a gyakoriságának beállítása óra és időköz értéke 1. Ezért a folyamat fut. után egy órán belül a folyamat kezdési és befejezési időpontja 

1. Kattintson a bal oldali ablaktáblában a Data Factory Editor **... További** -> **új adatkészlet** -> **Azure SQL**.

    ![Több új adatkészlet ->](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-dataset-menu.png)
2. A következő JSON-részlet átmásolja a JSON-szerkesztőben a jobb oldali ablaktáblán. 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Kattintson az eszköztár **Üzembe helyezés** gombjára. Ez a művelet telepíti az Azure Data Factory szolgáltatásnak az adatkészletet. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Hozzon létre egy folyamatot tárolt eljárási tevékenység 
Ebben a lépésben hoz létre egy folyamatot egy tárolt eljárás tevékenység. A tevékenység a SSIS-csomag futtatásához a sp_executesql tárolt eljárást hív meg. 

1. Kattintson a bal oldali ablaktáblában **... Továbbiak**, majd az **Új adatcsatorna** elemre.
2. A következő JSON-részlet átmásolja a JSON-szerkesztőben: 

    > [!IMPORTANT]
    > Cserélje le &lt;mappanév&gt;, &lt;projekt neve&gt;, &lt;csomagnév&gt; nevű mappa, a projekt és a csomag az SSIS-katalógusban a fájl mentése előtt.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2018-01-19T00:00:00Z",
            "end": "2018-01-19T05:00:00Z",
            "isPaused": false
        }
    }    
    ```
3. Kattintson az eszköztár **Üzembe helyezés** gombjára. Ez a művelet telepíti a feldolgozási sor az Azure Data Factory szolgáltatásnak. 

### <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása
A kimeneti adatkészlet vonatkozó ütemezés szerint óránként van definiálva. A folyamat befejezésének öt órát a kezdési időpont. Ezért lásd: öt folyamat futtatása. 

1. Zárja be a szerkesztő windows, úgy, hogy az a data factory a kezdőlap. Kattintson a **figyelő & kezelése** csempére. 

    ![Diagram csempe](./media/how-to-invoke-ssis-package-stored-procedure-activity/monitor-manage-tile.png)
2. Frissítés a **kezdési időpont** és **befejező időpontja** való **01/18/2018 08:30 AM** és **01/20/2018 08:30-kor**, és kattintson a **alkalmaz**. Megjelenik a **tevékenység windows** a Futtatás futószalag társított. 

    ![Tevékenység windows](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-windows.png)

Folyamatok figyelésével kapcsolatos további információkért lásd: [figyelése és kezelése az Azure Data Factory adatcsatornák a figyelés és a felügyeleti alkalmazás](data-factory-monitor-manage-app.md).

## <a name="azure-powershell"></a>Azure PowerShell
Ebben a szakaszban Azure PowerShell egy tárolt eljárás tevékenység, amely hívja meg az SSIS-csomag létrehozása a Data Factory-folyamat használja.

Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-azurerm-ps) ismertető cikkben szereplő utasításokat a legújabb Azure PowerShell-modulok telepítéséhez.

### <a name="create-a-data-factory"></a>Data factory létrehozása
Az alábbi eljárás lépéseit egy adat-előállító létrehozása. A tárolt eljárási tevékenység a data factory hoz létre egy folyamatot. A tárolt eljárási tevékenység végrehajtja a tárolt eljárás az SSISDB adatbázis a SSIS-csomag futtatásához.

1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../../azure-resource-manager/resource-group-overview.md) nevét idézőjelek között, majd futtassa a parancsot. Például: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$ResourceGroupName` változóhoz, majd futtassa újra a parancsot
2. Futtassa az alábbi parancsot az Azure-erőforráscsoport létrehozásához: 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$ResourceGroupName` változóhoz, majd futtassa újra a parancsot. 
3. Adjon meg egy változót az adat-előállító nevéhez. 

    > [!IMPORTANT]
    >  Frissítse az adat-előállító nevét, hogy globálisan egyedi legyen. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Az adat-előállítóban létrehozásához futtassa a következő **New-AzureRmDataFactory** parancsmag, a $ResGrp változóból helyét és a ResourceGroupName tulajdonság használatával: 
    
    ```powershell       
    $df = New-AzureRmDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Vegye figyelembe a következő szempontokat:

* Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory-példányok létrehozásához a felhasználói fióknak, amellyel belép az Azure-ba, a **közreműködő** vagy **tulajdonos** szerepkörök tagjának, vagy az Azure-előfizetés **rendszergazdájának** kell lennie.

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database-beli társított szolgáltatás létrehozása
A társított szolgáltatás, amely futtatja az Azure SQL database összekapcsolására. az SSIS-katalógusban a data factory létrehozása. Data Factory szolgáltatásnak információk használatával kapcsolódik az SSISDB adatbázist, és végrehajtja a tárolt eljárás egy SSIS-csomag futtatásához. 

1. Hozzon létre egy JSON fájlt **AzureSqlDatabaseLinkedService.json** a **C:\ADF\RunSSISPackage** mappa a következő tartalmát: 

    > [!IMPORTANT]
    > Cserélje le &lt;kiszolgálónév&gt;, &lt;felhasználónév&gt;@&lt;kiszolgálónév&gt; és &lt;jelszó&gt; az Azure SQL Database előtt értékekkel a fájl mentése.

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
2. A **Azure PowerShell**, váltson a **C:\ADF\RunSSISPackage** mappa.
3. Futtassa a **New-AzureRmDataFactoryLinkedService** parancsmaggal hozhat létre a társított szolgáltatás: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzureRmDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Kimeneti adatkészlet létrehozása
A kimeneti adatkészlet dummy adatkészletre mutató ütemezését a folyamat a meghajtók. Figyelje meg, hogy a gyakoriságának beállítása óra és időköz értéke 1. Ezért a folyamat fut. után egy órán belül a folyamat kezdési és befejezési időpontja 

1. Hozzon létre egy OuputDataset.json fájlt a következő tartalommal: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Futtassa a **New-AzureRmDataFactoryDataset** parancsmaggal hozzon létre egy DataSet objektum. 

    ```powershell
    New-AzureRmDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Hozzon létre egy folyamatot tárolt eljárási tevékenység 
Ebben a lépésben hoz létre egy folyamatot egy tárolt eljárás tevékenység. A tevékenység a SSIS-csomag futtatásához a sp_executesql tárolt eljárást hív meg. 

1. Hozzon létre egy JSON fájlt **MyPipeline.json** a a **C:\ADF\RunSSISPackage** mappa a következő tartalmát:

    > [!IMPORTANT]
    > Cserélje le &lt;mappanév&gt;, &lt;projekt neve&gt;, &lt;csomagnév&gt; nevű mappa, a projekt és a csomag az SSIS-katalógusban a fájl mentése előtt.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. A folyamat létrehozása: **RunSSISPackagePipeline**- ben futtassa a **New-AzureRmDataFactoryPipeline** parancsmag.

    ```powershell
    $DFPipeLine = New-AzureRmDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

2. Futtatás **Get-AzureRmDataFactorySlice** részletes információkat, a kimeneti adatkészlet **, amely a folyamat a bemeneti tábla összes szeletek.

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Megfigyelheti, hogy a StartDateTime itt megadott értéke megegyezik a folyamat JSON-fájljában megadott kezdési idővel. 
3. A **Get-AzureRmDataFactoryRun** parancs futtatásával lekérheti az adott szeletre vonatkozó tevékenységfuttatások részleteit.

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Futtassa a parancsmagot, amíg a szelet **Ready** (Kész) vagy **Failed** (Sikertelen) állapotú nem lesz. 

    A következő lekérdezés alapján futtathatók az SSISDB adatbázis annak ellenőrzésére, hogy az Azure SQL Server a csomag végrehajtása. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>További lépések
A tárolt eljárási tevékenység kapcsolatos részletekért lásd: a [tárolt eljárási tevékenység](data-factory-stored-proc-activity.md) cikk.

