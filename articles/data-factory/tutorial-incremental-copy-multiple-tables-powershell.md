---
title: 'Több tábla növekményes másolása Azure Data Factory használatával '
description: Az oktatóanyag során egy Azure Data Factory-folyamatot hoz létre, amely egy helyszíni SQL Server több táblájának módosított adatait másolja növekményesen egy Azure SQL-adatbázisba.
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/22/2018
ms.author: yexu
ms.openlocfilehash: b841acf45c20320fada895f20dfc4065837d5add
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683404"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Adatok növekményes betöltése az SQL Server több táblájából egy Azure SQL-adatbázisba
Az oktatóanyag során egy Azure-beli adat-előállítót hoz létre egy olyan folyamattal, amely változásadatokat tölt be egy helyszíni SQL Server több táblájából egy Azure SQL-adatbázisba.    

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Forrás- és céladattárak előkészítése.
> * Adat-előállító létrehozása
> * Hozzon létre egy saját üzemeltetésű integrációs modult.
> * Az Integration Runtime telepítése. 
> * Társított szolgáltatások létrehozása. 
> * Forrás-, fogadó- és küszöbadatkészletek létrehozása.
> * Folyamat létrehozása, futtatása és figyelése.
> * Itt tekintheti meg a művelet eredményét.
> * Adatok hozzáadása vagy frissítése a forrástáblákban.
> * A folyamat újrafuttatása és monitorozása.
> * A végső eredmények áttekintése.

## <a name="overview"></a>Áttekintés
Az alábbiak a megoldás kialakításának leglényegesebb lépései: 

1. **A küszöb oszlopának kiválasztása**.
    Jelölje ki az adatforrás egyes táblázatainak egy-egy oszlopát, amely alapján az új és a frissített rekordok minden egyes futtatáskor azonosíthatóak. Normális esetben az ebben a kiválasztott oszlopban (például: last_modify_time vagy ID) lévő adatok a sorok létrehozásával vagy frissítésével folyamatosan növekednek. Az ebben az oszlopban lévő legnagyobb érték szolgál a küszöbként.

1. **Egy adatraktár előkészítése a küszöbértékek tárolására**.   
    Ebben az oktatóanyagban a küszöbértékeket egy SQL-adatbázisban tároljuk.

1. **Egy folyamat létrehozása a következő tevékenységekkel**: 
    
    a. Egy ForEach tevékenység létrehozása, amely végighalad a forrástáblanevek listáján, amelyet a rendszer paraméterként ad át a folyamatnak. Minden forrástáblához elindítja a következő tevékenységeket a változásadatok betöltéséhez az adott tábla esetében.

    b. Két keresési tevékenység létrehozása. Az első keresési tevékenység az utolsó küszöbértéket kéri le. A második keresési tevékenység az új küszöbértéket kéri le. Ezeket a küszöbértékeket a rendszer átadja a másolási tevékenységnek.

    c. Egy másolási tevékenység létrehozása, amely a küszöbértéket tartalmazó oszlopban a régi küszöbértéknél magasabb, az új küszöbértéknél alacsonyabb értékkel rendelkező sorokat másolja át a forrásadattárból. Ezután a módosított adatokat a forrásadattárból új fájlként az Azure Blob Storage-ba másolja.

    d. Egy StoredProcedure tevékenység létrehozása, amely frissíti a küszöbértékeket a folyamat következő futtatásához. 

    Itt látható a megoldás összefoglaló jellegű ábrája: 

    ![Adatok növekményes betöltése](media/tutorial-incremental-copy-multiple-tables-powershell/high-level-solution-diagram.png)


Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek
* **Egy SQL Server**. Ebben az oktatóanyagban egy helyszíni SQL Server-adatbázist használ forrásadattárként. 
* **Azure SQL Database** Egy SQL-adatbázist használ fogadóadattárként. Ha még nem rendelkezik SQL-adatbázissal, a létrehozás folyamatáért lásd az [Azure SQL-adatbázis létrehozását](../sql-database/sql-database-get-started-portal.md) ismertető cikket. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Forrástáblák létrehozása az SQL Server-adatbázisban

1. Nyissa meg az SQL Server Management Studiót, és csatlakozzon a helyszíni SQL Server-adatbázishoz.

1. A **Kiszolgálókezelőben** kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés** elemet.

1. Futtassa a következő SQL-parancsot az adatbázison a `customer_table` és a `project_table` nevű tábla létrehozásához:

    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );
        
    INSERT INTO customer_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'John','9/1/2017 12:56:00 AM'),
    (2, 'Mike','9/2/2017 5:23:00 AM'),
    (3, 'Alice','9/3/2017 2:36:00 AM'),
    (4, 'Andy','9/4/2017 3:21:00 AM'),
    (5, 'Anny','9/5/2017 8:06:00 AM');
    
    INSERT INTO project_table
    (Project, Creationtime)
    VALUES
    ('project1','1/1/2015 0:00:00 AM'),
    ('project2','2/2/2016 1:23:00 AM'),
    ('project3','3/4/2017 5:16:00 AM');
    
    ```

### <a name="create-destination-tables-in-your-azure-sql-database"></a>Céltáblák létrehozása az Azure SQL-adatbázisban
1. Nyissa meg az SQL Server Management Studiót, és csatlakozzon az SQL Server-adatbázishoz.

1. A **Kiszolgálókezelőben** kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés** elemet.

1. Futtassa a következő SQL-parancsot az SQL-adatbázison a `customer_table` és a `project_table` nevű tábla létrehozásához:  
    
    ```sql
    create table customer_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );
    
    create table project_table
    (
        Project varchar(255),
        Creationtime datetime
    );

    ```

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Egy másik tábla létrehozása az Azure SQL-adatbázisban a felső küszöbértékek tárolására
1. Futtassa a következő SQL-parancsot az SQL-adatbázison egy `watermarktable` nevű, a küszöbértékek tárolására szolgáló tábla létrehozásához: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
1. Szúrja be a két forrástábla kezdeti küszöbértékeit a küszöbértékek táblájába.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-the-azure-sql-database"></a>Tárolt eljárás létrehozása az Azure SQL-adatbázisban 

Az alábbi parancs futtatásával hozzon létre egy tárolt eljárást az SQL-adatbázisban. Ez a tárolt eljárás minden folyamatfuttatás után frissíti a küszöbértéket. 

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures-in-the-azure-sql-database"></a>Adattípusok és további tárolt eljárások létrehozása az Azure SQL-adatbázisban
Az alábbi lekérdezés futtatásával hozzon létre két tárolt eljárást és két adattípust az SQL-adatbázisban. Ezek összevonják a forrástáblák adatait a céltáblákba. 

Ahhoz, hogy az utazást könnyen el lehessen kezdeni, közvetlenül használjuk ezeket a tárolt eljárásokat egy tábla változón keresztül, majd összevonjuk őket a célhelyek tárolójába. Legyen óvatos, ha nem vár "nagy" számú különbözeti sort (több mint 100) a Table változóban.  

Ha nagy számú különbözeti sort kell egyesíteni a célhelyen, javasoljuk, hogy a másolási tevékenység használatával másolja át az összes különbözeti értéket a cél tárolóban lévő ideiglenes "átmeneti" táblába, majd a Table Vari használata nélkül készítse el saját tárolt eljárását. képes egyesíteni őket az "átmeneti" táblából a "Final" táblába. 


```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE usp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
AS

BEGIN
  MERGE customer_table AS target
  USING @customer_table AS source
  ON (target.PersonID = source.PersonID)
  WHEN MATCHED THEN
      UPDATE SET Name = source.Name,LastModifytime = source.LastModifytime
  WHEN NOT MATCHED THEN
      INSERT (PersonID, Name, LastModifytime)
      VALUES (source.PersonID, source.Name, source.LastModifytime);
END

GO

CREATE TYPE DataTypeforProjectTable AS TABLE(
    Project varchar(255),
    Creationtime datetime
);

GO

CREATE PROCEDURE usp_upsert_project_table @project_table DataTypeforProjectTable READONLY
AS

BEGIN
  MERGE project_table AS target
  USING @project_table AS source
  ON (target.Project = source.Project)
  WHEN MATCHED THEN
      UPDATE SET Creationtime = source.Creationtime
  WHEN NOT MATCHED THEN
      INSERT (Project, Creationtime)
      VALUES (source.Project, source.Creationtime);
END

```

### <a name="azure-powershell"></a>Azure PowerShell
Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/azurerm/install-azurerm-ps) ismertető cikkben szereplő utasításokat a legújabb Azure PowerShell-modulok telepítéséhez.

## <a name="create-a-data-factory"></a>Data factory létrehozása
1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) nevét dupla idézőjelek között, majd futtassa a parancsot. Például: `"adfrg"`. 
   
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$resourceGroupName` változóhoz, majd futtassa újra a parancsot.

1. Adjon meg egy változót az adat-előállító helyéhez. 

    ```powershell
    $location = "East US"
    ```
1. Futtassa az alábbi parancsot az Azure-erőforráscsoport létrehozásához: 

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ``` 
    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$resourceGroupName` változóhoz, majd futtassa újra a parancsot.

1. Adjon meg egy változót az adat-előállító nevéhez. 

    > [!IMPORTANT]
    >  Frissítse az adat-előállító nevét, hogy globálisan egyedi legyen. Például: ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
1. Az adatelőállító létrehozásához futtassa az alábbi **set-AzDataFactoryV2** parancsmagot: 
    
    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Vegye figyelembe a következő szempontokat:

* Az adat-előállító nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra:

    ```
    The specified Data Factory name 'ADFIncMultiCopyTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Adatelőállító-példányok létrehozásához a felhasználói fióknak, amellyel bejelentkezik az Azure-ba, a közreműködő vagy tulajdonos szerepkörök tagjának, vagy az Azure-előfizetés rendszergazdájának kell lennie.
* Azon Azure-régiók megtekintéséhez, amelyekben jelenleg elérhető a Data Factory, a következő lapon válassza ki az Önt érdeklő régiókat, majd bontsa ki az **Elemzés** részt, és keresse meg a **Data Factory**: [Elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) szakaszt. Az adat-előállítók által használt adattárak (Azure Storage, SQL Database stb.) és számítási erőforrások (Azure HDInsight stb.) más régiókban is lehetnek.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]


## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Társított szolgáltatásokat hoz létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóval történő társításához. Ebben a szakaszban a helyszíni SQL Server-adatbázissal és az Azure SQL-adatbázissal társított szolgáltatásokat hoz létre. 

### <a name="create-the-sql-server-linked-service"></a>Az SQL Server társított szolgáltatásának létrehozása
Ebben a lépésben a helyszíni SQL Server-adatbázist társítja az adat-előállítóval.

1. Hozzon létre egy **SqlServerLinkedService. JSON** nevű JSON-fájlt a C:\ADFTutorials\IncCopyMultiTableTutorial mappában az alábbi tartalommal. Válassza ki az SQL Serverhez való kapcsolódáshoz használt hitelesítési módszernek megfelelő szakaszt. Hozza létre a helyi mappákat, ha még nem léteznek. 

    > [!IMPORTANT]
    > Válassza ki az SQL Serverhez való kapcsolódáshoz használt hitelesítési módszernek megfelelő szakaszt.

    SQL-hitelesítés használata esetén másolja a következő JSON-definíciót:

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=False;data source=<servername>;initial catalog=<database name>;user id=<username>;Password=<password>"
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    } 
   ```    
    Windows-hitelesítés használata esetén a következő JSON-definíciót másolja ki:

    ```json
    {  
        "name":"SqlServerLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=True;data source=<servername>;initial catalog=<database name>",
                "userName":"<username> or <domain>\\<username>",
                "password":{  
                    "type":"SecureString",
                    "value":"<password>"
                }
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
    ```
    > [!IMPORTANT]
    > - Válassza ki az SQL Serverhez való kapcsolódáshoz használt hitelesítési módszernek megfelelő szakaszt.
    > - Cserélje le az &lt;integration runtime name> értéket az Integration Runtime nevére.
    > - A fájl mentése előtt a &lt;servername>, &lt;databasename>, &lt;username> és &lt;password> értékeket cserélje le az SQL Server-adatbázis értékeire.
    > - Ha perjel karaktert (`\`) kell használnia a felhasználói fiók vagy a kiszolgáló nevében, használja az escape-karaktert (`\`). Például: `mydomain\\myuser`.

1. A PowerShellben futtassa a következő parancsmagot a C:\ADFTutorials\IncCopyMultiTableTutorial mappára való váltáshoz.

    ```powershell
    Set-Location 'C:\ADFTutorials\IncCopyMultiTableTutorial'
    ```

1. Futtassa a **set-AzDataFactoryV2LinkedService** parancsmagot a társított szolgáltatás AzureStorageLinkedService létrehozásához. A következő példában a *ResourceGroupName* és a *DataFactoryName* paraméter értékeit fogja megadni: 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerLinkedService" -File ".\SqlServerLinkedService.json"
    ```

    Itt látható a minta kimenete:

    ```json
    LinkedServiceName : SqlServerLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerLinkedService
    ```

### <a name="create-the-sql-database-linked-service"></a>Az SQL-adatbázis társított szolgáltatásának létrehozása
1. Hozzon létre egy **AzureSQLDatabaseLinkedService. JSON** nevű JSON-fájlt a C:\ADFTutorials\IncCopyMultiTableTutorial mappában az alábbi tartalommal. (Ha még nem létezik, hozza létre az ADF mappát.) Cserélje le a &lt;servername&gt;, &lt;adatbázisnév&gt;, &lt;Felhasználónév&gt;, és &lt;jelszó&gt; a SQL Server-adatbázis neve, az adatbázis neve, a Felhasználónév és a jelszót a fájl mentése előtt. 

    ```json
    {  
        "name":"AzureSQLDatabaseLinkedService",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"AzureSqlDatabase",
            "typeProperties":{  
                "connectionString":"integrated security=False;encrypt=True;connection timeout=30;data source=<servername>.database.windows.net;initial catalog=<database name>;user id=<user name>;Password=<password>;"
            }
        }
    }
    ```
1. A PowerShellben futtassa a **set-AzDataFactoryV2LinkedService** parancsmagot a társított szolgáltatás AzureSQLDatabaseLinkedService létrehozásához. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Itt látható a minta kimenete:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben olyan adatkészleteket hoz létre, amelyek az adatforrást, az adat célhelyét és a küszöbérték tárolási helyét jelölik.

### <a name="create-a-source-dataset"></a>Forrásadatkészlet létrehozása

1. Hozzon létre egy **SourceDataset.json** nevű JSON-fájlt ugyanebben a mappában az alábbi tartalommal: 

    ```json
    {  
        "name":"SourceDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"SqlServerLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  
    
            ],
            "type":"SqlServerTable",
            "schema":[  
    
            ]
        }
    }
   
    ```

    A teljes tábla betöltése helyett a folyamat másolási tevékenysége egy SQL-lekérdezést használ az adatok betöltéséhez.

1. Futtassa a **set-AzDataFactoryV2Dataset** parancsmagot az adatkészlet SourceDataset létrehozásához.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Itt látható a parancsmag mintakimenete:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Fogadó adatkészlet létrehozása

1. Hozzon létre egy **SinkDataset. JSON** nevű JSON-fájlt ugyanebben a mappában az alábbi tartalommal. A tableName elemet a folyamat állítja be dinamikusan, futásidőben. A folyamat ForEach tevékenysége végighalad a táblanevek listáján, és minden egyes ismétléskor átadja a táblanevet ennek az adatkészletnek. 

    ```json
    {  
        "name":"SinkDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"AzureSQLDatabaseLinkedService",
                "type":"LinkedServiceReference"
            },
            "parameters":{  
                "SinkTableName":{  
                    "type":"String"
                }
            },
            "annotations":[  
    
            ],
            "type":"AzureSqlTable",
            "typeProperties":{  
                "tableName":{  
                    "value":"@dataset().SinkTableName",
                    "type":"Expression"
                }
            }
        }
    }
    ```

1. Futtassa a **set-AzDataFactoryV2Dataset** parancsmagot az adatkészlet SinkDataset létrehozásához.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Itt látható a parancsmag mintakimenete:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-a-watermark"></a>Adatkészlet létrehozása a küszöbhöz
Ebben a lépésben egy adatkészletet hozunk létre a felső küszöbértékek tárolására. 

1. Hozzon létre egy **WatermarkDataset. JSON** nevű JSON-fájlt ugyanebben a mappában az alábbi tartalommal: 

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
1. Futtassa a **set-AzDataFactoryV2Dataset** parancsmagot az adatkészlet WatermarkDataset létrehozásához.
    
    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Itt látható a parancsmag mintakimenete:
    
    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Folyamat létrehozása
A folyamat táblanevek listáját használja paraméterként. A **foreach tevékenység** megismétli a táblák neveinek listáját, és végrehajtja a következő műveleteket: 

1. A **keresési tevékenység** lekéri a régi küszöbértéket (a kezdeti értéket, vagy azt, amelyet az utolsó iteráció során használt).

1. A **keresési tevékenység** lekéri az új küszöbértéket (a forrásoldali tábla vízjel oszlopának maximális értékét).

1. A **másolási tevékenység** segítségével másolja át az adatok között a két küszöbértéket a forrás-adatbázisból a céladatbázisbe.

1. A **StoredProcedure tevékenység** használatával frissítse a régi küszöbértéket, amelyet a következő iteráció első lépéseként kíván használni. 

### <a name="create-the-pipeline"></a>A folyamat létrehozása
1. Hozzon létre egy **IncrementalCopyPipeline. JSON** nevű JSON-fájlt ugyanebben a mappában az alábbi tartalommal: 

    ```json
    {  
        "name":"IncrementalCopyPipeline",
        "properties":{  
            "activities":[  
                {  
                    "name":"IterateSQLTables",
                    "type":"ForEach",
                    "dependsOn":[  
    
                    ],
                    "userProperties":[  
    
                    ],
                    "typeProperties":{  
                        "items":{  
                            "value":"@pipeline().parameters.tableList",
                            "type":"Expression"
                        },
                        "isSequential":false,
                        "activities":[  
                            {  
                                "name":"LookupOldWaterMarkActivity",
                                "type":"Lookup",
                                "dependsOn":[  
    
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"AzureSqlSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"WatermarkDataset",
                                        "type":"DatasetReference"
                                    }
                                }
                            },
                            {  
                                "name":"LookupNewWaterMarkActivity",
                                "type":"Lookup",
                                "dependsOn":[  
    
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}",
                                            "type":"Expression"
                                        }
                                    },
                                    "dataset":{  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    },
                                    "firstRowOnly":true
                                }
                            },
                            {  
                                "name":"IncrementalCopyActivity",
                                "type":"Copy",
                                "dependsOn":[  
                                    {  
                                        "activity":"LookupOldWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    },
                                    {  
                                        "activity":"LookupNewWaterMarkActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "source":{  
                                        "type":"SqlServerSource",
                                        "sqlReaderQuery":{  
                                            "value":"select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'",
                                            "type":"Expression"
                                        }
                                    },
                                    "sink":{  
                                        "type":"AzureSqlSink",
                                        "sqlWriterStoredProcedureName":{  
                                            "value":"@{item().StoredProcedureNameForMergeOperation}",
                                            "type":"Expression"
                                        },
                                        "sqlWriterTableType":{  
                                            "value":"@{item().TableType}",
                                            "type":"Expression"
                                        },
                                        "storedProcedureTableTypeParameterName":{  
                                            "value":"@{item().TABLE_NAME}",
                                            "type":"Expression"
                                        },
                                        "disableMetricsCollection":false
                                    },
                                    "enableStaging":false
                                },
                                "inputs":[  
                                    {  
                                        "referenceName":"SourceDataset",
                                        "type":"DatasetReference"
                                    }
                                ],
                                "outputs":[  
                                    {  
                                        "referenceName":"SinkDataset",
                                        "type":"DatasetReference",
                                        "parameters":{  
                                            "SinkTableName":{  
                                                "value":"@{item().TABLE_NAME}",
                                                "type":"Expression"
                                            }
                                        }
                                    }
                                ]
                            },
                            {  
                                "name":"StoredProceduretoWriteWatermarkActivity",
                                "type":"SqlServerStoredProcedure",
                                "dependsOn":[  
                                    {  
                                        "activity":"IncrementalCopyActivity",
                                        "dependencyConditions":[  
                                            "Succeeded"
                                        ]
                                    }
                                ],
                                "policy":{  
                                    "timeout":"7.00:00:00",
                                    "retry":0,
                                    "retryIntervalInSeconds":30,
                                    "secureOutput":false,
                                    "secureInput":false
                                },
                                "userProperties":[  
    
                                ],
                                "typeProperties":{  
                                    "storedProcedureName":"[dbo].[usp_write_watermark]",
                                    "storedProcedureParameters":{  
                                        "LastModifiedtime":{  
                                            "value":{  
                                                "value":"@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}",
                                                "type":"Expression"
                                            },
                                            "type":"DateTime"
                                        },
                                        "TableName":{  
                                            "value":{  
                                                "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}",
                                                "type":"Expression"
                                            },
                                            "type":"String"
                                        }
                                    }
                                },
                                "linkedServiceName":{  
                                    "referenceName":"AzureSQLDatabaseLinkedService",
                                    "type":"LinkedServiceReference"
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters":{  
                "tableList":{  
                    "type":"array"
                }
            },
            "annotations":[  
    
            ]
        }
    }
    ```
1. Futtassa a **set-AzDataFactoryV2Pipeline** parancsmagot a folyamat IncrementalCopyPipeline létrehozásához.
    
   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   Itt látható a minta kimenete: 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```
 
## <a name="run-the-pipeline"></a>A folyamat futtatása

1. Hozzon létre egy **Parameters. JSON** nevű paramétert ugyanabban a mappában az alábbi tartalommal:

    ```json
    {
        "tableList": 
        [
            {
                "TABLE_NAME": "customer_table",
                "WaterMark_Column": "LastModifytime",
                "TableType": "DataTypeforCustomerTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_customer_table"
            },
            {
                "TABLE_NAME": "project_table",
                "WaterMark_Column": "Creationtime",
                "TableType": "DataTypeforProjectTable",
                "StoredProcedureNameForMergeOperation": "usp_upsert_project_table"
            }
        ]
    }
    ```
1. Futtassa a folyamat IncrementalCopyPipeline a **hívás-AzDataFactoryV2Pipeline** parancsmag használatával. Cserélje le a helyőrzőket a saját erőforráscsoportja és adat-előállítója nevére.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>A folyamat figyelése

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com).

1. Kattintson a **Minden szolgáltatás** elemre, végezzen keresést az *Adat-előállítók* kulcsszóval, és válassza az **Adat-előállítók** lehetőséget. 

1. Keresse meg az adat-előállítóját az adat-előállítók listájában, és kattintson rá az **Adat-előállító** oldal megnyitásához. 

1. Az **adatfeldolgozó** lapon válassza a **Szerző & figyelő** lehetőséget Azure Data Factory elindításához egy külön lapon.

1. Az **első lépések** oldalon válassza a **figyelő** lehetőséget a bal oldalon. 
![folyamat fut](media/doc-common-process/get-started-page-monitor-button.png)    

1. Itt megtekintheti az összes folyamatfuttatást és azok állapotát. A következő példában a folyamatfuttatás állapota **Sikeres**. A **Paraméterek** oszlopban található hivatkozásra kattintva megtekintheti a folyamatnak átadott paramétereket. Hiba esetén egy hivatkozás jelenik meg a **Hiba** oszlopban.

    ![Folyamatfuttatások](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-pipeline-runs-4.png)    
1. Amikor kiválasztja a **műveletek** oszlopban található hivatkozást, megjelenik a folyamat összes tevékenység-futtatása. 

1. Ha vissza szeretne térni a **folyamat futtatási** nézetéhez, válassza a **minden folyamat futtatása**lehetőséget. 

## <a name="review-the-results"></a>Az eredmények áttekintése
Az SQL Server Management Studióban futtassa a következő lekérdezéseket a cél SQL-adatbázison annak ellenőrzéséhez, hogy a rendszer átmásolta-e az adatokat a forrástáblákból a céltáblákba: 

**Lekérdezés** 
```sql
select * from customer_table
```

**Kimenet**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           Alice   2017-09-03 02:36:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

**Lekérdezés**

```sql
select * from project_table
```

**Kimenet**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
```

**Lekérdezés**

```sql
select * from watermarktable
```

**Kimenet**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-05 08:06:00.000
project_table   2017-03-04 05:16:00.000
```

Megfigyelhető, hogy mindkét tábla küszöbértékei frissültek. 

## <a name="add-more-data-to-the-source-tables"></a>További adatok hozzáadása a forrástáblákhoz

Futtassa a következő lekérdezést a forrás SQL Server-adatbázison a customer_table meglévő sorának frissítéséhez. Szúrjon be egy új sort a project_table táblába. 

```sql
UPDATE customer_table
SET [LastModifytime] = '2017-09-08T00:00:00Z', [name]='NewName' where [PersonID] = 3

INSERT INTO project_table
(Project, Creationtime)
VALUES
('NewProject','10/1/2017 0:00:00 AM');
``` 

## <a name="rerun-the-pipeline"></a>A folyamat újbóli futtatása

1. Futtassa újból a folyamatot a következő PowerShell-parancs végrehajtásával:

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupname -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"
    ```
1. Monitorozza folyamatfuttatásokat [A folyamat figyelése](#monitor-the-pipeline) szakasz utasításait alapján. Ha a folyamat állapota **folyamatban**van, egy másik műveleti hivatkozás jelenik meg a **műveletek** területen a folyamat futtatásának megszakításához. 

1. Válassza ki a **Frissítés** elemet, hogy a lista folyamatosan frissüljön, amíg a folyamat futtatása be nem fejeződik. 

1. Szükség esetén kattintson a **Tevékenységfuttatások megtekintése** hivatkozásra a **Műveletek** területen a folyamatfuttatáshoz társított tevékenységfuttatások megtekintéséhez. 

## <a name="review-the-final-results"></a>A végső eredmények áttekintése
Az SQL Server Management Studióban futtassa a következő lekérdezéseket a céladatbázison annak ellenőrzéséhez, hogy a rendszer átmásolta-e a frissített/új adatokat a forrástáblákból a céltáblákba. 

**Lekérdezés** 
```sql
select * from customer_table
```

**Kimenet**
```
===========================================
PersonID    Name    LastModifytime
===========================================
1           John    2017-09-01 00:56:00.000
2           Mike    2017-09-02 05:23:00.000
3           NewName 2017-09-08 00:00:00.000
4           Andy    2017-09-04 03:21:00.000
5           Anny    2017-09-05 08:06:00.000
```

A 3. számú **PersonID** új **Name** és **LastModifytime** értékkel rendelkezik. 

**Lekérdezés**

```sql
select * from project_table
```

**Kimenet**

```
===================================
Project     Creationtime
===================================
project1    2015-01-01 00:00:00.000
project2    2016-02-02 01:23:00.000
project3    2017-03-04 05:16:00.000
NewProject  2017-10-01 00:00:00.000
```

A project_table táblához hozzá lett adva a **NewProject** bejegyzés. 

**Lekérdezés**

```sql
select * from watermarktable
```

**Kimenet**

```
======================================
TableName       WatermarkValue
======================================
customer_table  2017-09-08 00:00:00.000
project_table   2017-10-01 00:00:00.000
```

Megfigyelhető, hogy mindkét tábla küszöbértékei frissültek.
     
## <a name="next-steps"></a>További lépések
Az oktatóanyagban az alábbi lépéseket hajtotta végre: 

> [!div class="checklist"]
> * Forrás- és céladattárak előkészítése.
> * Adat-előállító létrehozása
> * Helyi Integration Runtime (IR) létrehozása.
> * Az Integration Runtime telepítése.
> * Társított szolgáltatások létrehozása. 
> * Forrás-, fogadó- és küszöbadatkészletek létrehozása.
> * Folyamat létrehozása, futtatása és figyelése.
> * Itt tekintheti meg a művelet eredményét.
> * Adatok hozzáadása vagy frissítése a forrástáblákban.
> * A folyamat újrafuttatása és monitorozása.
> * A végső eredmények áttekintése.

Folytassa a következő oktatóanyaggal, amelyben az adatok Azure Spark-fürtök használatával való átalakítását ismerheti meg:

> [!div class="nextstepaction"]
>[Adatok növekményes betöltése az Azure SQL Database-ből az Azure Blob Storage-ba a változáskövetési technológia használatával](tutorial-incremental-copy-change-tracking-feature-powershell.md)


