---
title: "Adatok növekményes másolása az Azure Data Factory használatával | Microsoft Docs"
description: "Az oktatóanyag során egy Azure Data Factory-folyamatot hoz majd létre, amely adatok növekményes másolását végzi majd egy Azure SQL Database-adatbázisból egy Azure Blob Storage-tárolóba."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/10/2017
ms.author: shlo
ms.openlocfilehash: a9d59e8ad2669b7a00ec83e019148bbac96f679f
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2017
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage"></a>Adatok növekményes betöltése az Azure SQL Database-ből az Azure Blob Storage-ba
Az Azure Data Factory egy felhőalapú adatintegrációs szolgáltatás. Lehetővé teszi olyan, a felhőben futó, adatvezérelt munkafolyamatok létrehozását, amelyek alkalmasak az adatok átvitelének és átalakításának irányítására és automatizálására. Az Azure Data Factory segítségével létrehozhatók és ütemezhetők a különböző adattárolókból adatokat beolvasó adatvezérelt munkafolyamatok, feldolgozhatók és átalakíthatók az adatok különböző számítási szolgáltatások használatával (pl. Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics és Azure Machine Learning), és a kimeneti adatok közzétehetők olyan adattárakban, mint például az Azure SQL Data Warehouse, ahonnan az üzleti intelligenciára épülő (BI-) alkalmazások felhasználhatják őket. 

Az adatintegrációs folyamatok során az egyik széles körben alkalmazott módszer az adatok növekményes betöltése, amelynek keretében a kezdeti adatbetöltést és -elemzéseket követően az elemzési eredmények rendszeresen frissítve lesznek. Ebben az oktatóanyagban arra összpontosítunk, hogyan tölthetők be csak az új és a frissített rekordok az adatforrásokból az adatfogadókba. Ez a folyamat a teljes betöltéseknél sokkal hatékonyabban üzemel – különösen a nagyobb adatkészletek esetében.    

A Data Factory használatával kialakíthat felső küszöbbel rendelkező megoldásokat a növekményes adatbetöltés megvalósítása érdekében a Keresés, a Másolás és a Tárolt eljárás tevékenység a folyamat során történő használatával.  

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Az adatraktár előkészítése a küszöbértékek tárolására.   
> * Adat-előállító létrehozása
> * Társított szolgáltatások létrehozása. 
> * Forrás-, fogadó- és küszöbadatkészletek létrehozása.
> * Folyamat létrehozása.
> * A folyamat futtatása.
> * A folyamat futásának monitorozása. 

## <a name="overview"></a>Áttekintés
A megoldás felsőszintű áttekintő ábrája: 

![Adatok növekményes betöltése](media\tutorial-Incrementally-load-data-from-azure-sql-to-blob\incrementally-load.png)

Az alábbiak a megoldás kialakításának leglényegesebb lépései: 

1. **A küszöb oszlopának kiválasztása**.
    Jelölje ki az adatforrás egyik oszlopát, amelynek alapján az új és a frissített rekordok minden egyes futtatáskor leválogathatók. Normális esetben az ebben a kiválasztott oszlopban (például: last_modify_time vagy ID) lévő adatok a sorok létrehozásával vagy frissítésével folyamatosan növekednek. Az ebben az oszlopban lévő legnagyobb érték szolgál a küszöbként.
2. **Egy adatraktár előkészítése a küszöbértékek tárolására**.   
    Ebben az oktatóanyagban a küszöbértékeket egy Azure SQL-adatbázisban tároljuk.
3. **Egy folyamat létrehozása a következő munkafolyamattal:** 
    
    Ebben a megoldásban a folyamat a következő tevékenységeket tartalmazza:
  
    1. Két **keresési** tevékenység létrehozása. Az első keresési tevékenység az utolsó küszöbértéket kéri le. A második keresési tevékenység az új küszöbértéket kéri le. Ezeket a küszöbértékeket a rendszer átadja a másolási tevékenységnek. 
    2. Egy **másolási tevékenység** létrehozása, amely a küszöbértéket tartalmazó oszlopban a régi küszöbértéknél magasabb, az új küszöbértéknél alacsonyabb értékkel rendelkező sorokat másolja át a forrásadattárból. Ezután a módosított adatokat a forrásadattárból új fájlként egy blobtárolóba másolja. 
    3. Egy **tárolt eljárási tevékenység** létrehozása, amely frissíti a küszöbértékeket a folyamat következő futtatásához. 


Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek
* **Azure SQL Database** Ezt az adatbázist használjuk **forrásadattárként**. Ha még nem rendelkezik Azure SQL Database-adatbázissal, a létrehozás folyamatáért lásd az [Azure SQL-adatbázis létrehozását](../sql-database/sql-database-get-started-portal.md) ismertető cikket.
* **Azure Storage-fiók** A blobtárolót használjuk majd **fogadóadattárként**. Ha még nem rendelkezik Azure Storage-fiókkal, a létrehozás folyamatáért lásd a [tárfiók létrehozását](../storage/common/storage-create-storage-account.md#create-a-storage-account) ismertető cikket. Hozzon létre egy tárolót **adftutorial** néven. 
* **Azure PowerShell**. Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-azurerm-ps) ismertető cikkben szereplő utasításokat.

### <a name="create-a-data-source-table-in-your-azure-sql-database"></a>Adatforrástábla létrehozása az Azure SQL-adatbázisban
1. Nyissa meg az **SQL Server Management Studiót**, majd a **Kiszolgálókezelőben** kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés** elemet.
2. Futtassa a következő SQL-parancsot az Azure SQL-adatbázison egy tábla `data_source_table` néven, adatforrástárként történő létrehozásához.  
    
    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    Az oktatóanyagban a **LastModifytime** oszlopot használjuk **küszöboszlopként**.  Az adatforrástár adatai az alábbi táblázatban láthatók:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-sql-database-to-store-the-high-watermark-value"></a>Egy másik tábla létrehozása az SQL-adatbázisban a felső küszöbértékek tárolására
1. Futtassa a következő SQL-parancsot az Azure SQL-adatbázison egy tábla `watermarktable` néven, a küszöbértékek tárolására történő létrehozásához.  
    
    ```sql
    create table watermarktable
    (
    
    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
3. Állítsa be a felső küszöb alapértelmezett **értékét** a forrásadattár táblanevével.  (Ebben az oktatóanyagban a tábla neve a következő: **data_source_table**.)

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
4. Tekintse át a következő tábla adatait: `watermarktable`.
    
    ```sql
    Select * from watermarktable
    ```
    Kimenet: 

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-azure-sql-database"></a>Tárolt eljárás létrehozása az Azure SQL-adatbázisban 

Az alábbi parancs futtatásával hozzon létre egy tárolt eljárást az Azure SQL-adatbázisban.

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN
    
    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName
    
END
```

## <a name="create-a-data-factory"></a>Adat-előállító létrehozása

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
    Set-AzureRmDataFactoryV2 -ResourceGroupName "<your resource group to create the factory>" -Location "East US" -Name "<specify the name of data factory to create. It must be globally unique.>" 
    ```

    Vegye figyelembe a következő szempontokat:

    * Az Azure adat-előállító nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.

        ```
        The specified Data Factory name '<data factory name>' is already in use. Data Factory names must be globally unique.
        ```

    * Data Factory-példányok létrehozásához az Azure-előfizetés közreműködőjének vagy rendszergazdájának kell lennie.
    * A Data Factory 2-es verziója jelenleg csak az USA keleti régiójában teszi lehetővé adat-előállítók létrehozását. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.


## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Társított szolgáltatásokat hoz létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóval történő társításához. Ebben a szakaszban az Azure Storage-fiókkal és az Azure SQL-adatbázissal társított szolgáltatásokat hoz létre. 

### <a name="create-azure-storage-linked-service"></a>Azure Storage-beli társított szolgáltatás létrehozása
1. Hozzon létre egy **AzureStorageLinkedService.json** nevű JSON-fájlt a **C:\ADF** mappában az alábbi tartalommal (ha még nem létezne, hozza létre az ADF nevű mappát). Mielőtt mentené a fájlt, az `<accountName>` és az `<accountKey>` helyőrzőt cserélje le az Azure Storage-fiók nevére és kulcsára.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. Az **Azure PowerShellben** váltson az **ADF** mappára.
3. Futtassa a **Set-AzureRmDataFactoryV2LinkedService** parancsmagot az **AzureStorageLinkedService** társított szolgáltatás létrehozásához. A következő példában a **ResourceGroupName** és a **DataFactoryName** paraméter értékeit fogja megadni. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Itt látható a minta kimenete:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-azure-sql-database-linked-service"></a>Azure SQL Database-beli társított szolgáltatás létrehozása.
1. Hozzon létre egy **AzureSQLDatabaseLinkedService.json** nevű JSON-fájlt a **C:\ADF** mappában az alábbi tartalommal (ha még nem létezne, hozza létre az ADF nevű mappát). Mielőtt mentené a fájlt, a **&lt;server&gt;, a &lt;user id&gt; és a &lt;password&gt;** helyőrzőt cserélje az Azure SQL-kiszolgáló nevére, a felhasználói azonosítóra és a jelszóra. 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name> ; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. Az **Azure PowerShellben** váltson az **ADF** mappára.
3. Futtassa a **Set-AzureRmDataFactoryV2LinkedService** parancsmagot az **AzureSqlDatabaseLinkedService** társított szolgáltatás létrehozásához. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Itt látható a minta kimenete:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ProvisioningState :
    ```

## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben adatkészleteket hoz létre, amelyek a forrás és a fogadó adatait jelölik. 

### <a name="create-a-source-dataset"></a>Forrásadatkészlet létrehozása

1. Hozzon létre egy SourceDataset.json nevű JSON-fájlt ugyanebben a mappában az alábbi tartalommal: 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
   
    ```
    Ebben az oktatóanyagban a tábla neve a következő: **data_source_table**. Ha más nevű táblát használ, cserélje le a nevet. 
2.  Futtassa a Set-AzureRmDataFactoryV2Dataset parancsmagot a SourceDataset adatkészlet létrehozásához.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Itt látható a parancsmag mintakimenete:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Fogadó adatkészlet létrehozása

1. Hozzon létre egy SinkDataset.json nevű JSON-fájlt ugyanebben a mappában az alábbi tartalommal: 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incrementalcopy",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')", 
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

    > [!IMPORTANT]
    > Ez a kódtöredék azt feltételezi, hogy az Azure Blob Storage-ban rendelkezik egy **adftutorial** nevű blobtárolóval. Ha még nem létezik, hozza létre a tárolót, vagy állítsa be egy meglévő tároló nevét. Az `incrementalcopy` kimeneti mappa automatikusan létrejön, ha még nem létezik a tárolóban. Ebben az oktatóanyagban a fájl nevét dinamikusan hozzuk létre a következő kifejezés használatával: `@CONCAT('Incremental-', pipeline().RunId, '.txt')`.
2.  Futtassa a Set-AzureRmDataFactoryV2Dataset parancsmagot a SinkDataset adatkészlet létrehozásához.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Itt látható a parancsmag mintakimenete:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset    
    ```

## <a name="create-a-dataset-for-watermark"></a>Adatkészlet létrehozása a küszöbhöz
Ebben a lépésben egy adatkészletet hozunk létre a felső küszöbértékek tárolására. 

1. Hozzon létre egy WatermarkDataset.json nevű JSON-fájlt ugyanebben a mappában az alábbi tartalommal: 

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2.  Futtassa a Set-AzureRmDataFactoryV2Dataset parancsmagot a WatermarkDataset adatkészlet létrehozásához.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Itt látható a parancsmag mintakimenete:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Folyamat létrehozása
Az oktatóanyag során létrehoz egy folyamatot két keresési, egy másolási és egy tárolt eljárási tevékenységgel, egyetlen folyamattá összefűzve. 


1. Hozzon létre egy IncrementalCopyPipeline.json nevű JSON-fájlt ugyanebben a mappában az alábbi tartalommal: 

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupOldWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from watermarktable"
                        },
    
                        "dataset": {
                        "referenceName": "WatermarkDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupNewWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select MAX(LastModifytime) as NewWatermarkvalue from data_source_table"
                        },
    
                        "dataset": {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                
                {
                    "name": "IncrementalCopyActivity",
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupNewWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupOldWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ],
    
                    "inputs": [
                        {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "SinkDataset",
                            "type": "DatasetReference"
                        }
                    ]
                },
    
                {
                    "name": "StoredProceduretoWriteWatermarkActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
    
                        "storedProcedureName": "sp_write_watermark",
                        "storedProcedureParameters": {
                            "LastModifiedtime": {"value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}", "type": "datetime" },
                            "TableName":  { "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}", "type":"String"}
                        }
                    },
    
                    "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
    
                    "dependsOn": [
                        {
                            "activity": "IncrementalCopyActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]
            
        }
    }
    ```
    

2. Az IncrementalCopyPipeline folyamat létrehozásához futtassa a Set-AzureRmDataFactoryV2Pipeline parancsmagot.
    
   ```powershell
   Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Itt látható a minta kimenete: 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Activities        : {LookupOldWaterMarkActivity, LookupNewWaterMarkActivity, IncrementalCopyActivity, StoredProceduretoWriteWatermarkActivity}
    Parameters        :
   ```
 
## <a name="run-the-pipeline"></a>A folyamat futtatása

1. Futtassa az **IncrementalCopyPipeline** folyamatot az **Invoke-AzureRmDataFactoryV2Pipeline** parancsmag használatával. Cserélje le a helyőrzőket a saját erőforráscsoportja és adat-előállítója nevére.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup "<your resource group>" -dataFactoryName "<your data factory name>"
    ``` 
2. A Get-AzureRmDataFactoryV2ActivityRun parancsmag futtatásával ellenőrizze a folyamat állapotát, amíg azt nem látja, hogy minden tevékenység sikeresen fut. Cserélje le a helyőrzőket a saját megfelelő időértékeire a RunStartedAfter és a RunStartedBefore paraméter esetében.  Ebben az oktatóanyagban a -RunStartedAfter "2017/09/14" és -RunStartedBefore "2017/09/15" paramétert használjuk.

    ```powershell
    Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Itt látható a minta kimenete:
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:42:50 AM
    DurationInMs      : 7777
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:43:07 AM
    DurationInMs      : 25437
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:10 AM
    ActivityRunEnd    : 9/14/2017 7:43:29 AM
    DurationInMs      : 19769
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:32 AM
    ActivityRunEnd    : 9/14/2017 7:43:47 AM
    DurationInMs      : 14467
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```

## <a name="review-the-results"></a>Az eredmények áttekintése

1. Az Azure-blobtárolóban (fogadótároló) azt kell látnia, hogy az adatok át lettek másolva a SinkDataset által meghatározott fájlba.  Ebben az oktatóanyagban a fájl neve: `Incremental- d4bf3ce2-5d60-43f3-9318-923155f61037.txt`.  Nyissa meg a fájlt, és láthatja, hogy a benne lévő rekordok egyeznek az Azure SQL-adatbázisban lévő adatokkal.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ``` 
2. Ellenőrizze a `watermarktable` legutolsó értékét, és láthatja, hogy a küszöbérték módosult.

    ```sql
    Select * from watermarktable
    ```
    
    Itt látható a minta kimenete:
 
    TableName | WatermarkValue
    --------- | --------------
    data_source_table   2017-09-05  8:06:00.000

### <a name="insert-data-into-data-source-store-to-verify-delta-data-loading"></a>Adatok beszúrása a forrásadattárba a növekményes adatbetöltés ellenőrzéséhez

1. Szúrjon be új adatokat az Azure SQL-adatbázisba (forrásadattár):

    ```sql
    INSERT INTO data_source_table
    VALUES (6, 'newdata','9/6/2017 2:23:00 AM')
    
    INSERT INTO data_source_table
    VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
    ``` 

    Az Azure SQL-adatbázis a következő frissített adatokat tartalmazza:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    6 | newdata | 2017-09-06 02:23:00.000
    7 | newdata | 2017-09-07 09:01:00.000
    ```
2. Futtassa ismét az **IncrementalCopyPipeline** folyamatot az **Invoke-AzureRmDataFactoryV2Pipeline** parancsmag használatával. Cserélje le a helyőrzőket a saját erőforráscsoportja és adat-előállítója nevére.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup "<your resource group>" -dataFactoryName "<your data factory name>"
    ```
3. A **Get-AzureRmDataFactoryV2ActivityRun** parancsmag futtatásával ellenőrizze a folyamat állapotát, amíg azt nem látja, hogy minden tevékenység sikeresen fut. Cserélje le a helyőrzőket a saját megfelelő időértékeire a RunStartedAfter és a RunStartedBefore paraméter esetében.  Ebben az oktatóanyagban a -RunStartedAfter "2017/09/14" és -RunStartedBefore "2017/09/15" paramétert használjuk.

    ```powershell
    Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Itt látható a minta kimenete:
 
    ```json
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:58 AM
    DurationInMs      : 31758
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:52 AM
    DurationInMs      : 25497
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:00 AM
    ActivityRunEnd    : 9/14/2017 8:53:20 AM
    DurationInMs      : 20194
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:23 AM
    ActivityRunEnd    : 9/14/2017 8:53:41 AM
    DurationInMs      : 18502
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```
4.  Az Azure-blobtárolóban láthatja, hogy egy új fájl lett létrehozva. Ebben az oktatóanyagban az új fájl neve a következő: `Incremental-2fc90ab8-d42c-4583-aa64-755dba9925d7.txt`.  Nyissa meg ezt a fájlt, és láthatja, hogy 2 sornyi rekordot tartalmaz.
5.  Ellenőrizze a `watermarktable` legutolsó értékét, és láthatja, hogy a küszöbérték ismét módosult.

    ```sql
    Select * from watermarktable
    ```
    Példa a kimenetre: 
    
    TableName | WatermarkValue
    --------- | ---------------
    data_source_table | 2017-09-07 09:01:00.000

     
## <a name="next-steps"></a>Következő lépések
Az oktatóanyagban az alábbi lépéseket hajtotta végre: 

> [!div class="checklist"]
> * **Küszöb** oszlop meghatározása és tárolása egy Azure SQL Database-adatbázisban.  
> * Adat-előállító létrehozása
> * Társított szolgáltatások létrehozása az SQL Database-hez és a Blob Storage-hoz. 
> * Forrás- és fogadó-adatkészletek létrehozása.
> * Folyamat létrehozása.
> * A folyamat futtatása.
> * A folyamat futásának monitorozása. 

Folytassa a következő oktatóanyaggal, amelyben az adatok Azure Spark-fürtök használatával való átalakítását ismerheti meg:

> [!div class="nextstepaction"]
>[Adatok átalakítása a felhőben a Spark használatával](tutorial-transform-data-spark-powershell.md)



