---
title: Azure Data Factory - tárolt eljárási tevékenység használatával SSIS-csomagok meghívásához |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan lehet egy Azure Data Factory-folyamatot a tárolt eljárási tevékenység használatával az SQL Server Integration Services (SSIS) csomagok meghívásához.
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
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: fe2b509b62884c1cea554bc8dc5df25489205264
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966956"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Egy tárolt eljárási tevékenység használatával az Azure Data Factory SSIS-csomagok meghívásához
Ez a cikk bemutatja, hogyan kell elindítani az SSIS-csomag az Azure Data Factory-folyamatot egy tárolt eljárási tevékenység használatával. 

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [meghívása SSIS-csomagokat a tárolt eljárási tevékenység használatával](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-sql-database"></a>Azure SQL Database 
Ebben a cikkben található útmutatások követéséhez használja az Azure SQL database, amelyen az SSIS-katalógus. Egy Azure SQL Database felügyelt példánya is használhatja.

### <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul létrehozása
Hozzon létre egy Azure-SSIS integrációs modult, ha még nincs fiókja, a részletes utasításokat a következő a [oktatóanyag: üzembe SSIS-csomagok](../tutorial-create-azure-ssis-runtime-portal.md). Az Azure-SSIS integrációs modul létrehozása a Data Factory 1. verzió nem használható. 

## <a name="azure-portal"></a>Azure Portal
Ebben a szakaszban, az Azure portal használatával hozzon létre egy Data Factory-folyamatot egy tárolt eljárási tevékenység, amely SSIS-csomag hív meg.

### <a name="create-a-data-factory"></a>Data factory létrehozása
Első lépés az adat-előállító létrehozása az Azure portal használatával. 

1. Lépjen az [Azure Portalra](https://portal.azure.com). 
2. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Az **Új adat-előállító** lapon, a **Név** mezőben adja meg a következőt: **ADFTutorialDataFactory**. 
      
     ![Új adat-előállító lap](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a Név mezőnél az alábbi hiba jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialDataFactory). A Data Factory-összetevők elnevezésére vonatkozó részleteket a [Data Factory elnevezési szabályait](data-factory-naming-rules.md) ismertető cikkben találja.

    `Data factory name ADFTutorialDataFactory is not available`
3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
4. Az **erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
      - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
      - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. Válassza ki **V1** számára a **verzió**.
5. Válassza ki a Data Factory **helyét**. A legördülő listában csak a Data Factory által támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más helyeken is lehetnek.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Create** (Létrehozás) gombra.
8. Az irányítópulton megjelenő csempén a következő állapotleírás látható: **Adat-előállító üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
    ![Data factory kezdőlap](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Kattintson a **készítése és üzembe helyezése** csempére kattintva indítsa el a Data Factory Editor.

    ![A Data Factory szerkesztője](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-editor.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database-beli társított szolgáltatás létrehozása
Hozzon létre egy társított szolgáltatást, az Azure SQL-adatbázis üzemeltető az SSIS-katalógus a data factoryjához. A Data Factory információkat használja ezt a társított szolgáltatást az SSISDB-adatbázishoz való csatlakozáshoz, és végrehajt egy tárolt eljárást az SSIS-csomag futtatásához. 

1. A Data Factory Editorban kattintson **új adattároló** a menüben, majd kattintson a **Azure SQL Database**. 

    ![Új adattároló -> Azure SQL Database](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-sql-database-linked-service-menu.png)
2. A jobb oldali ablaktáblán tegye a következőket:

    1. Cserélje le `<servername>` az Azure SQL server nevével. 
    2. Cserélje le `<databasename>` a **SSISDB** (az SSIS katalógus-adatbázis neve). 
    3. Cserélje le `<username@servername>` a felhasználó, aki hozzáfér az Azure SQL server nevével. 
    4. Cserélje le `<password>` az a felhasználó jelszava. 
    5. A társított szolgáltatás üzembe helyezéséhez kattintson a **telepítés** gombra az eszköztáron. 

        ![Azure SQL Database társított szolgáltatás](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-definition.png)

### <a name="create-a-dummy-dataset-for-output"></a>Helyőrző kimeneti adatkészlet létrehozása
A kimeneti adatkészlet egy helyőrző adatkészletet, amely a folyamat ütemezését. Figyelje meg, hogy a frequency értéke Hour és interval értéke 1. Ezért a folyamat fut, miután egy órán belül a folyamat kezdési és befejezési időpontja. 

1. Kattintson a bal oldali panelen, a Data Factory Editor **... További** -> **új adatkészlet** -> **Azure SQL**.

    ![Több -> Új adatkészlet](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-dataset-menu.png)
2. Másolja a következő JSON-kódrészletre a JSON-szerkesztőt, a jobb oldali ablaktáblán. 
    
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
3. Kattintson az eszköztár **Üzembe helyezés** gombjára. Ez a művelet telepíti az adatkészlet az Azure Data Factory szolgáltatásban. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Tárolt eljárási tevékenység rendelkező folyamat létrehozása 
Ebben a lépésben létrehoz egy folyamatot egy tárolt eljárási tevékenység a. A tevékenység futtatása az SSIS-csomag sp_executesql tárolt eljárást hív meg. 

1. A bal oldali ablaktáblán kattintson a **... Továbbiak**, majd az **Új adatcsatorna** elemre.
2. Másolja a következő JSON-kódrészletre a JSON-szerkesztőbe: 

    > [!IMPORTANT]
    > Cserélje le &lt;mappanevet&gt;, &lt;projektnév&gt;, &lt;csomagnév&gt; nevű mappa, projektek és az SSIS-katalógus a fájl mentése előtt a csomagot.

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
3. Kattintson az eszköztár **Üzembe helyezés** gombjára. Ez a művelet telepíti a folyamat az Azure Data Factory szolgáltatásban. 

### <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása
A kimeneti adatkészlet az ütemezés szerint óránként van meghatározva. A folyamat befejezési idő a kezdési időpontból öt órát jelent. Ezért öt folyamatfuttatást láthatja. 

1. Zárja be a szerkesztő windows, úgy, hogy az adat-előállító kezdőlapjának megnyitásához. Kattintson a **Monitor & Manage** csempére. 

    ![Diagram csempe](./media/how-to-invoke-ssis-package-stored-procedure-activity/monitor-manage-tile.png)
2. Frissítés a **kezdési idő** és **befejezési idő** , **2018-01-18-ra 08:30-kor** és **2018-01-20 08:30-kor**, és kattintson a **Alkalmaz**. Megtekintheti a **tevékenységablakok** a folyamat futásához társított. 

    ![Tevékenységablakok](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-windows.png)

Folyamatok figyelésével kapcsolatos további információkért lásd: [figyelése és kezelése az Azure Data Factory-folyamatok a megfigyelési és felügyeleti alkalmazás használatával](data-factory-monitor-manage-app.md).

## <a name="azure-powershell"></a>Azure PowerShell
Ebben a szakaszban az Azure PowerShell használatával hozzon létre egy Data Factory-folyamatot egy tárolt eljárási tevékenység, amely SSIS-csomag hív meg.

Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-azurerm-ps) ismertető cikkben szereplő utasításokat a legújabb Azure PowerShell-modulok telepítéséhez.

### <a name="create-a-data-factory"></a>Data factory létrehozása
Az alábbi eljárás lépéseit egy adat-előállító létrehozásához. Egy tárolt eljárási tevékenység, a data factory-folyamatot hoz létre. A tárolt eljárási tevékenység végrehajt egy tárolt eljárást az SSISDB adatbázis futtatásához az SSIS-csomag.

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

5. Az adat-előállító létrehozásához futtassa a következő **New-AzureRmDataFactory** parancsmagot a $ResGrp változó Location és ResourceGroupName tulajdonság használatával: 
    
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
Hozzon létre egy társított szolgáltatást, az Azure SQL-adatbázis üzemeltető az SSIS-katalógus a data factoryjához. A Data Factory információkat használja ezt a társított szolgáltatást az SSISDB-adatbázishoz való csatlakozáshoz, és végrehajt egy tárolt eljárást az SSIS-csomag futtatásához. 

1. Hozzon létre egy JSON-fájlt **AzureSqlDatabaseLinkedService.json** a **C:\ADF\RunSSISPackage** mappában az alábbi tartalommal: 

    > [!IMPORTANT]
    > Cserélje le &lt;servername&gt;, &lt;felhasználónév&gt;@&lt;servername&gt; és &lt;jelszó&gt; értékeket az Azure SQL Database előtt a fájl mentése.

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
2. A **Azure PowerShell-lel**, váltson át a **C:\ADF\RunSSISPackage** mappát.
3. Futtassa a **New-AzureRmDataFactoryLinkedService** parancsmagot a társított szolgáltatás létrehozásához: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzureRmDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Kimeneti adatkészlet létrehozása
A kimeneti adatkészlet egy helyőrző adatkészletet, amely a folyamat ütemezését. Figyelje meg, hogy a frequency értéke Hour és interval értéke 1. Ezért a folyamat fut, miután egy órán belül a folyamat kezdési és befejezési időpontja. 

1. Hozzon létre egy OuputDataset.json fájlt az alábbi tartalommal: 
    
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
2. Futtassa a **New-AzureRmDataFactoryDataset** parancsmaggal hozzon létre egy adatkészletet. 

    ```powershell
    New-AzureRmDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Tárolt eljárási tevékenység rendelkező folyamat létrehozása 
Ebben a lépésben létrehoz egy folyamatot egy tárolt eljárási tevékenység a. A tevékenység futtatása az SSIS-csomag sp_executesql tárolt eljárást hív meg. 

1. Hozzon létre egy JSON-fájlt **MyPipeline.json** a a **C:\ADF\RunSSISPackage** mappában az alábbi tartalommal:

    > [!IMPORTANT]
    > Cserélje le &lt;mappanevet&gt;, &lt;projektnév&gt;, &lt;csomagnév&gt; nevű mappa, projektek és az SSIS-katalógus a fájl mentése előtt a csomagot.

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

2. A folyamat létrehozásához: **RunSSISPackagePipeline**futtassa a **New-AzureRmDataFactoryPipeline** parancsmagot.

    ```powershell
    $DFPipeLine = New-AzureRmDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

2. Futtatás **Get-AzureRmDataFactorySlice** részletes információkat az összes szelet részleteit a kimeneti adatkészlet **, amely a folyamat kimeneti táblája.

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Megfigyelheti, hogy a StartDateTime itt megadott értéke megegyezik a folyamat JSON-fájljában megadott kezdési idővel. 
3. A **Get-AzureRmDataFactoryRun** parancs futtatásával lekérheti az adott szeletre vonatkozó tevékenységfuttatások részleteit.

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Futtassa a parancsmagot, amíg a szelet **Ready** (Kész) vagy **Failed** (Sikertelen) állapotú nem lesz. 

    Futtathatja a következő lekérdezés az SSISDB-adatbázison annak ellenőrzéséhez, hogy az Azure SQL Serveren a csomag végrehajtása. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>További lépések
A tárolt eljárás tevékenységgel kapcsolatos részletekért lásd: a [Stored Procedure-tevékenység](data-factory-stored-proc-activity.md) cikk.

