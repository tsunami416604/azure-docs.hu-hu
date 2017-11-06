---
title: "Adatok másolása a helyszíni rendszerekből a felhőbe az Azure Data Factory használatával | Microsoft Docs"
description: "Megismerheti az adatok a helyszíni adattárolókból az Azure-beli felhőbe, az Azure Data Factory saját üzemeltetésű integrációs moduljával történő másolásának folyamatát."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/06/2017
ms.author: jingwang
ms.openlocfilehash: 95d1dce536f8f8f0fc8d93f201520fd84f0f7629
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2017
---
# <a name="copy-data-between-on-premises-and-cloud"></a>Adatok másolása a helyszíni rendszer és a felhő között

[!INCLUDE [data-factory-what-is-include-md](../../includes/data-factory-what-is-include.md)]

#### <a name="this-tutorial"></a>Ez az oktatóanyag

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd [a Data Factory 1. verziójának dokumentációját](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Ebben az oktatóanyagban az Azure PowerShell használatával egy Data Factory-folyamatot hozunk létre adatok egy helyszíni SQL Server-adatbázisból egy Azure Blob-tárolóba történő másolására. Létrehozzuk és alkalmazzuk az Azure Data Factory egy saját üzemeltetésű integrációs modulját (IR), amely lehetővé teszi a helyszíni és a felhőalapú adattárolók integrálását.  Az adat-előállítók egyéb eszközökkel/SDK-kkal való létrehozásával kapcsolatban lásd [rövid útmutatóinkat](quickstart-create-data-factory-dot-net.md).

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Saját üzemeltetésű integrációs modul létrehozása
> * Helyszíni SQL Server-alapú társított szolgáltatás létrehozása és titkosítása egy saját üzemeltetésű integrációs modulban.
> * Azure Storage-beli társított szolgáltatás létrehozása
> * SQL Server- és Azure Storage-adatkészletek létrehozása
> * Folyamat létrehozása másolási tevékenységgel az adatok áthelyezéséhez
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek

* **Egy SQL Server**. Ebben az oktatóanyagban egy helyszíni SQL Server-adatbázist használunk **forrásadattárként**.
* **Azure Storage-fiók** Ebben az oktatóanyagban egy Azure-blobtárolót használunk **cél-/fogadóadattárként**. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket.
* **Azure PowerShell**. Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-azurerm-ps) ismertető cikkben szereplő utasításokat.

## <a name="create-a-data-factory"></a>Data factory létrehozása

1. Indítsa el a **PowerShellt**. Az Azure PowerShellt hagyja megnyitva az oktatóanyag végéig. Ha bezárja és újra megnyitja, akkor újra futtatnia kell a parancsokat.

    Futtassa a következő parancsot, és adja meg az Azure Portalra való bejelentkezéshez használt felhasználónevet és jelszót.
    ```powershell
    Login-AzureRmAccount
    ```
    Futtassa a következő parancsot a fiókhoz tartozó előfizetések megtekintéséhez.

    ```powershell
    Get-AzureRmSubscription
    ```
    Futtassa a következő parancsot a használni kívánt előfizetés kiválasztásához. Cserélje le a **SubscriptionId** kifejezést az Azure-előfizetés azonosítójára:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Futtassa a **Set-AzureRmDataFactoryV2** parancsmagot egy adat-előállító létrehozásához. A parancs végrehajtása előtt cserélje le a helyőrzőket a saját értékeire.

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    $df = Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Vegye figyelembe a következő szempontokat:

    * Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.

        ```
        Data factory name "<data factory name>" is not available.
        ```

    * Data Factory-példányok létrehozásához az Azure-előfizetés közreműködőjének vagy rendszergazdájának kell lennie.
    * A Data Factory jelenleg csak az Egyesült Államok keleti és 2. keleti régiójában teszi lehetővé adat-előállítók létrehozását. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

## <a name="create-a-self-hosted-ir"></a>Saját üzemeltetésű integrációs modul létrehozása

Ebben a szakaszban egy saját üzemeltetésű integrációs modult hozhat létre, és társíthatja azt egy helyszíni csomóponttal (géppel).

1. Hozzon létre egy saját üzemeltetésű integrációs modult. Használjon egy egyedi nevet, amennyiben már létezne egy integrációs modul ezen a néven.

   ```powershell
   $integrationRuntimeName = "<your integration runtime name>"
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Itt látható a minta kimenete:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. Futtassa az alábbi parancsot a létrehozott integrációs modul állapotának lekéréséhez.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Itt látható a minta kimenete:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. Futtassa az alábbi parancsot a hitelesítési kulcsok lekéréséhez, hogy a saját üzemeltetésű integrációs modult regisztrálhassa a Data Factory-szolgáltatásban a felhőben. Másolja be az egyik kulcsot a saját üzemeltetésű integrációs modul regisztrálásához.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Itt látható a minta kimenete:

   ```json
   {
       "AuthKey1":  "IR@8437c862-d6a9-4fb3-87dd-7d4865a9e845@ab1@eu@VDnzgySwUfaj3pfSUxpvfsXXXXXXx4GHiyF4wboad0Y=",
       "AuthKey2":  "IR@8437c862-d6a9-4fb3-85dd-7d4865a9e845@ab1@eu@sh+k/QNJGBltXL46vXXXXXXXXOf/M1Gne5aVqPtbweI="
   }
   ```

4. [Töltse le](https://www.microsoft.com/download/details.aspx?id=39717) a saját üzemeltetésű integrációs modult egy helyi windowsos gépre, és az előző lépésben lekért hitelesítési kulcs használatával regisztrálja manuálisan.

   ![Integrációs modul regisztrálása](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

   Az integrációs modul sikeres regisztrációja esetén a következő üzenet jelenik meg:

   ![Sikeres regisztráció](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

   A csomópont a felhőszolgáltatáshoz való csatlakozásakor a következő oldal jelenik meg:

   ![Csomópont csatlakoztatva](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Azure Storage-beli társított szolgáltatás (cél/fogadó) létrehozása

1. Hozzon létre egy **AzureStorageLinkedService.json** nevű JSON-fájlt a **C:\ADFv2Tutorial** mappában az alábbi tartalommal. Ha még nem létezik, hozza létre az ADFv2Tutorial mappát.  Az &lt;accountname&gt; és az &lt;accountkey&gt; kifejezést cserélje le az Azure Storage-tárfiókja nevére, illetve kulcsára.

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

2. Az **Azure PowerShellben** váltson az **ADFv2Tutorial** mappára.

   Futtassa a **Set-AzureRmDataFactoryV2LinkedService** parancsmagot az **AzureStorageLinkedService** társított szolgáltatás létrehozásához. Az oktatóanyagban alkalmazott parancsmagok a **ResourceGroupName** és a **DataFactoryName** paraméter értékeit fogja felvenni. Vagy a Set-AzureRmDataFactoryV2 parancsmag használatával lekért **DataFactory** objektumot is átadhatja anélkül, hogy minden egyes alkalommal meg kellene adnia a ResourceGroupName és a DataFactoryName értéket a parancsmag futtatásakor.

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Itt látható egy mintakimenet:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>SQL Server-alapú társított szolgáltatás létrehozása és titkosítása (forrás)

1. Hozzon létre egy **SqlServerLinkedService.json** nevű JSON-fájlt a **C:\ADFv2Tutorial** mappában a következő tartalommal: a fájl mentése előtt a **&lt;servername>**, **&lt;databasename>**, **&lt;username>**, **&lt;servername>** és **&lt;password>** értékeket cserélje le a SQL Server értékeire. Cserélje le az **&lt;integration** **runtime** **name>** értéket az integrációs modul nevére.

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```
2. A helyszíni saját üzemeltetésű integrációs modulban a bizalmas hasznos JSON-adatok titkosításához futtassa a **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential** parancsot, és adja át a fenti hasznos JSON-adatokat. A titkosítás a hitelesítő adatokat az adatvédelmi API (DPAPI) segítségével titkosítja és a saját üzemeltetésű integrációs modul csomópontján helyileg tárolja. A kimenő hasznos adatok átirányíthatóak egy másik JSON-fájlba (ebben az esetben az „encryptedLinkedService.json” fájlba), amely titkosított hitelesítő adatokat tartalmaz.

    A parancs futtatása előtt cserélje le az **&lt;integration runtime name&gt;** értéket az integrációs modul nevére.

   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName <integration runtime name> -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Az **SqlServerLinkedService** létrehozásához az előző lépésben szereplő JSON használatával futtassa a következő parancsot:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Adatkészletek létrehozása

### <a name="prepare-an-on-premises-sql-server-for-the-tutorial"></a>Helyszíni SQL Server előkészítése az oktatóanyaghoz

Ebben a lépésben olyan bemeneti és kimeneti adatkészleteket hoz létre, amelyek a másolási művelet be- és kimeneti adatait képezik majd (Helyszíni SQL Server-adatbázis => Azure Blob Storage). Az adatkészletek létrehozása előtt végezze el a következő lépéseket (a részletes lépések a lista után olvashatóak):

- Hozzon létre egy **emp** nevű táblát az adat-előállítóhoz társított szolgáltatásként hozzáadott SQL Server-adatbázisban, és szúrjon be a táblába néhány mintabejegyzést.
- Hozzon létre egy **adftutorial** nevű Blob-tárolót az adat-előállítóhoz társított szolgáltatásként hozzáadott Azure Blob Storage-fiókban.


1. A helyszíni SQL Server-alapú társított szolgáltatáshoz (**SqlServerLinkedService**) megadott adatbázisban a következő SQL-szkripttel hozza létre az **emp** táblát.

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Szúrjon be a táblába néhány mintabejegyzést:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```



### <a name="create-a-dataset-for-source-sql-database"></a>Adatkészlet létrehozása a forrás SQL-adatbázishoz

1. Hozzon létre egy **SqlServerDataset.json** nevű JSON-fájlt a **C:\ADFv2Tutorial** mappában a következő tartalommal:  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. Az **SqlServerDataset** adatkészlet létrehozásához futtassa a **Set-AzureRmDataFactoryV2Dataset** parancsmagot.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Itt látható a minta kimenete:

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>Adatkészlet létrehozása a fogadó Azure Blob Storage-hoz

1. Hozzon létre egy **AzureBlobDataset.json** nevű JSON-fájlt a **C:\ADFv2Tutorial** mappában a következő tartalommal:

    > [!IMPORTANT]
    > Ez a példakód azt feltételezi, hogy az Azure Blob Storage-ban rendelkezik egy **adftutorial** nevű tárolóval.

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. Az **AzureBlobDataset** adatkészlet létrehozásához futtassa a **Set-AzureRmDataFactoryV2Dataset** parancsmagot.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Itt látható a minta kimenete:

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-pipelines"></a>Folyamatok létrehozása

### <a name="create-the-pipeline-sqlservertoblobpipeline"></a>Az „SqlServerToBlobPipeline” folyamat létrehozása

1. Hozzon létre egy **SqlServerToBlobPipeline.json** nevű JSON-fájlt a **C:\ADFv2Tutorial** mappában a következő tartalommal:

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Az **SQLServerToBlobPipeline** folyamat létrehozásához futtassa a **Set-AzureRmDataFactoryV2Pipeline** parancsmagot.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Itt látható a minta kimenete:

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```



## <a name="start-and-monitor-a-pipeline-run"></a>Folyamat futásának indítása és monitorozása

1. Indítsa el az „SQLServerToBlobPipeline” folyamat futását, és őrizze meg a folyamat futtatási azonosítóját későbbi monitorozás céljából.  

    ```powershell
    $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
    ```

2. Az „**SQLServerToBlobPipeline**” folyamat futási állapotának folyamatos ellenőrzéséhez futtassa a következő szkriptet, majd nyomtassa ki a végső eredményt.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    Itt látható a példa futtatás kimenete:

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName :
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Az „**SQLServerToBlobPipeline**” folyamat futtatási azonosítójának lekéréséhez és a tevékenységfuttatás részletes eredményének áttekintéséhez tegye a következőket.

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Itt látható a példa futtatás kimenete:

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 4,
      "throughput": 0.01,
      "errors": []
    }
    ```
4. Csatlakozzon a fogadó Azure Blob Storage-hoz, és ellenőrizze, hogy az adatok megfelelően át lettek-e másolva az Azure SQL Database-ből.

## <a name="next-steps"></a>Következő lépések
A példában szereplő folyamat adatokat másol az egyik helyről egy másikra egy Azure Blob Storage-ban. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Saját üzemeltetésű integrációs modul létrehozása
> * Helyszíni SQL Server-alapú társított szolgáltatás létrehozása és titkosítása egy saját üzemeltetésű integrációs modulban
> * Azure Storage-beli társított szolgáltatás létrehozása
> * SQL Server- és Azure Storage-adatkészletek létrehozása
> * Folyamat létrehozása másolási tevékenységgel az adatok áthelyezéséhez
> * Folyamat futásának indítása
> * A folyamat és a tevékenységek futásának monitorozása

Az Azure Data Factory által forrásként és fogadóként támogatott adattárak listája a [támogatott adattárakat ismertető](copy-activity-overview.md#supported-data-stores-and-formats) cikkben található.

Folytassa a következő oktatóanyaggal, amelyben az adatok egy forrásból egy célhelyre történő tömeges másolását ismerheti meg:

> [!div class="nextstepaction"]
>[Adatok tömeges másolása](tutorial-bulk-copy.md)
