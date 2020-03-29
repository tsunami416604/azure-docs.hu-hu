---
title: SSIS-csomag meghívása az Azure Data Factory használatával – Tárolt eljárási tevékenység
description: Ez a cikk ismerteti, hogyan lehet meghívni egy SQL Server Integration Services (SSIS) csomagot egy Azure Data Factory-folyamat a tárolt eljárás tevékenység használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: ea86c4670a8eb6dc5e2133ed01045e8aada0f707
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75438789"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>SSIS-csomag meghívása tárolt eljárási tevékenység használatával az Azure Data Factoryban
Ez a cikk ismerteti, hogyan lehet meghívni egy SSIS-csomagot egy Azure Data Factory-folyamat egy tárolt eljárástevékenység használatával. 

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa [el az SSIS-csomagok meghívása tárolt eljárástevékenység használatával című témakört.](../how-to-invoke-ssis-package-stored-procedure-activity.md)

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-sql-database"></a>Azure SQL Database 
Ebben a cikkben a forgatókönyv egy Azure SQL-adatbázist használ, amely az SSIS-katalógust üzemelteti. Azure SQL Database felügyelt példányis használhatja.

### <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul létrehozása
Hozzon létre egy Azure-SSIS-integrációs futásidejűt, ha nem rendelkezik ilyenségsel az [oktatóanyag: SSIS-csomagok telepítése](../tutorial-create-azure-ssis-runtime-portal.md)című útmutató lépésről lépésre történő követésével. A Data Factory 1-es verziója nem használható Azure-SSIS-integrációs futásidejű létrehozásához. 

## <a name="azure-powershell"></a>Azure PowerShell
Ebben a szakaszban az Azure PowerShell használatával hozzon létre egy Data Factory-folyamat egy tárolt eljárástevékenység, amely meghívja az SSIS-csomagot.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Telepítse a legújabb Azure PowerShell-modulokat az [Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-az-ps)című témakörutasításait követve.

### <a name="create-a-data-factory"></a>Data factory létrehozása
Az alábbi eljárás az adat-előállító létrehozásának lépéseit ismerteti. Ebben az adat-előállítóban egy tárolt eljárási tevékenységgel rendelkező folyamatot hoz létre. A tárolt eljárás tevékenység az SSISDB-adatbázisban tárolt eljárást hajt végre az SSIS-csomag futtatásához.

1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../../azure-resource-manager/management/overview.md) nevét idézőjelek között, majd futtassa a parancsot. Például: `"adfrg"`. 
   
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

5. Az adat-előállító létrehozásához futtassa a következő **New-AzDataFactory** parancsmagát a $ResGrp változó Hely és ResourceGroupName tulajdonságával: 
    
    ```powershell       
    $df = New-AzDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Vegye figyelembe a következő szempontokat:

* Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory-példányok létrehozásához a felhasználói fióknak, amellyel belép az Azure-ba, a **közreműködő** vagy **tulajdonos** szerepkörök tagjának, vagy az Azure-előfizetés **rendszergazdájának** kell lennie.

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database-beli társított szolgáltatás létrehozása
Hozzon létre egy összekapcsolt szolgáltatást az SSIS-katalógust tároló Azure SQL-adatbázis és az adat-előállító összekapcsolására. A Data Factory a csatolt szolgáltatás adatait használja az SSISDB adatbázishoz való csatlakozáshoz, és egy tárolt eljárást hajt végre egy SSIS-csomag futtatásához. 

1. Hozzon létre egy **AzureSqlDatabaseLinkedService.json** nevű JSON-fájlt a **C:\ADF\RunSSISPackage** mappában a következő tartalommal: 

    > [!IMPORTANT]
    > Cserélje &lt;le&gt; &lt;a&gt;@&lt;kiszolgálónevet&gt; &lt;,&gt; a felhasználónév kiszolgálónevét és jelszavát az Azure SQL Database értékeire a fájl mentése előtt.

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
3. Futtassa a **New-AzDataFactoryLinkedService** parancsmagot a csatolt szolgáltatás létrehozásához: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Kimeneti adatkészlet létrehozása
Ez a kimeneti adatkészlet egy dumi a dataset, amely a folyamat ütemezését vezéreli. Figyelje meg, hogy a gyakoriság óra, az intervallum pedig 1. Ezért a folyamat óránként egyszer fut a folyamat kezdési és befejezési időszakán belül. 

1. OutputDataset.json fájl létrehozása a következő tartalommal: 
    
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
2. Az adatkészlet létrehozásához futtassa a **New-AzDataFactoryDataset** parancsmagát. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Tárolt eljárási tevékenységgel rendelkező folyamat létrehozása 
Ebben a lépésben hozzon létre egy folyamatot tárolt eljárási tevékenységgel. A tevékenység meghívja a sp_executesql tárolt eljárást az SSIS-csomag futtatásához. 

1. Hozzon létre egy **MyPipeline.json** nevű JSON-fájlt a **C:\ADF\RunSSISPackage** mappában a következő tartalommal:

    > [!IMPORTANT]
    > A &lt;fájl&gt; &lt;mentése&gt;előtt &lt;cserélje&gt; le a mappanevet , a projekt nevét a mappa, a projekt és a csomag nevére az SSIS-katalógusban.

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

2. A folyamat létrehozásához: **RunSSISPackagePipeline**, futtassa a **New-AzDataFactoryPipeline** parancsmag.

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

1. **Futtassa a Get-AzDataFactorySlice parancsot** a kimeneti adatkészlet**, a folyamat kimeneti táblájának összes szeletének részleteinek megismeréséhez.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Megfigyelheti, hogy a StartDateTime itt megadott értéke megegyezik a folyamat JSON-fájljában megadott kezdési idővel. 
1. **Futtassa a Get-AzDataFactoryRun futtatások** részleteit egy adott szelethez.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Futtassa a parancsmagot, amíg a szelet **Ready** (Kész) vagy **Failed** (Sikertelen) állapotú nem lesz. 

    A következő lekérdezést futtathatja az SSISDB-adatbázisban az Azure SQL-kiszolgálón, és ellenőrizheti, hogy a csomag végrehajtása. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>További lépések
A tárolt eljárástevékenységről a [Tárolt eljárás tevékenységről](data-factory-stored-proc-activity.md) szóló cikkben olvashat részletesen.

