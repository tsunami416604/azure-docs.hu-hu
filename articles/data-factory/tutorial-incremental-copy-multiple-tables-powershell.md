---
title: Több tábla növekményes másolása a PowerShell használatával
description: Ebben az oktatóanyagban hozzon létre egy Azure Data Factory-folyamatot, amely a különbözeti adatokat növekményesen másolja egy helyszíni SQL Server-adatbázis több táblájából egy Azure SQL-adatbázisba.
services: data-factory
ms.author: yexu
author: dearandyxu
manager: anandsub
ms.reviewer: douglasl, maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/30/2020
ms.openlocfilehash: 5654e1f8b8a55c705798368df70ce300241c9dff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76989083"
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-an-azure-sql-database"></a>Az SQL Server több táblájából származó adatok növekményes betöltése egy Azure SQL-adatbázisba

Ebben az oktatóanyagban egy Azure-adatfeldolgozó üzemet hoz létre egy folyamattal, amely a helyszíni SQL Server több táblájából a különbözeti adatokat egy Azure SQL-adatbázisba tölti be.    

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

    Jelöljön ki egy oszlopot a forrásadattár minden táblájához, amely minden futtatáshoz azonosíthatja az új vagy frissített rekordokat. Normális esetben az ebben a kiválasztott oszlopban (például: last_modify_time vagy ID) lévő adatok a sorok létrehozásával vagy frissítésével folyamatosan növekednek. Az ebben az oszlopban lévő legnagyobb érték szolgál a küszöbként.

2. **Egy adatraktár előkészítése a küszöbértékek tárolására**.

    Ebben az oktatóanyagban a küszöbértékeket egy SQL-adatbázisban tároljuk.

3. **Folyamat létrehozása a következő tevékenységekkel:**
    
    a. Egy ForEach tevékenység létrehozása, amely végighalad a forrástáblanevek listáján, amelyet a rendszer paraméterként ad át a folyamatnak. Minden forrástáblához elindítja a következő tevékenységeket a változásadatok betöltéséhez az adott tábla esetében.

    b. Két keresési tevékenység létrehozása. Az első keresési tevékenység az utolsó küszöbértéket kéri le. A második keresési tevékenység az új küszöbértéket kéri le. Ezeket a küszöbértékeket a rendszer átadja a másolási tevékenységnek.

    c. Egy másolási tevékenység létrehozása, amely a küszöbértéket tartalmazó oszlopban a régi küszöbértéknél magasabb, az új küszöbértéknél alacsonyabb értékkel rendelkező sorokat másolja át a forrásadattárból. Ezután a módosított adatokat a forrásadattárból új fájlként az Azure Blob Storage-ba másolja.

    d. Egy StoredProcedure tevékenység létrehozása, amely frissíti a küszöbértékeket a folyamat következő futtatásához. 

    Itt látható a megoldás összefoglaló jellegű ábrája: 

    ![Adatok növekményes betöltése](media/tutorial-incremental-copy-multiple-tables-powershell/high-level-solution-diagram.png)


Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* **SQL Server**kiszolgáló . Ebben az oktatóanyagban egy helyszíni SQL Server-adatbázist használ forrásadattárként. 
* **Az Azure SQL Database .** Egy SQL-adatbázist használ fogadóadattárként. Ha még nem rendelkezik SQL-adatbázissal, a létrehozás folyamatáért lásd az [Azure SQL-adatbázis létrehozását](../sql-database/sql-database-get-started-portal.md) ismertető cikket. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Forrástáblák létrehozása az SQL Server-adatbázisban

1. Nyissa meg [az SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) vagy az Azure Data [Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)alkalmazást, és csatlakozzon a helyszíni SQL Server-adatbázishoz.

2. A **Kiszolgálókezelőben (SSMS)** vagy a **Kapcsolatok ablaktáblán (Azure Data Studio)** kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés**parancsot.

3. Futtassa a következő SQL-parancsot az adatbázison a `customer_table` és a `project_table` nevű tábla létrehozásához:

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

1. Nyissa meg [az SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) vagy az Azure Data [Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio)alkalmazást, és csatlakozzon a helyszíni SQL Server-adatbázishoz.

2. A **Kiszolgálókezelőben (SSMS)** vagy a **Kapcsolatok ablaktáblán (Azure Data Studio)** kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés**parancsot.

3. Futtassa a következő SQL-parancsot az SQL-adatbázison a `customer_table` és a `project_table` nevű tábla létrehozásához:  

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

### <a name="create-another-table-in-the-azure-sql-database-to-store-the-high-watermark-value"></a>Hozzon létre egy másik táblát az Azure SQL Database-ben a magas vízjel értékének tárolásához

1. Futtassa a következő SQL-parancsot az SQL-adatbázison egy `watermarktable` nevű, a küszöbértékek tárolására szolgáló tábla létrehozásához: 
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
2. Szúrja be a két forrástábla kezdeti küszöbértékeit a küszöbértékek táblájába.

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

Annak érdekében, hogy az utazás könnyen indulhasson, ezeket a Tárolt eljárásokat közvetlenül használjuk, amelyek a delta adatokat egy táblaváltozón keresztül továbbítják, majd egyesítik azokat a céltárolóba. Legyen óvatos, hogy nem számít a táblaváltozóban "nagy" számú (több mint 100) delta sor tárolására.  

Ha nagy számú delta sort kell egyesítenie a céltárolóba, javasoljuk, hogy másolási tevékenység használatával másolja át az összes különbözeti adatot egy ideiglenes "átmeneti" táblába a céltárolóban, majd a saját tárolt eljárást anélkül, hogy táblaváltozót használna az "átmeneti" táblából a "végleges" táblába való egyesítéshez. 


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

1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) nevét dupla idézőjelek között, majd futtassa a parancsot. Például: `"adfrg"`.

    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$resourceGroupName` változóhoz, majd futtassa újra a parancsot.

2. Adjon meg egy változót az adat-előállító helyéhez. 

    ```powershell
    $location = "East US"
    ```
3. Futtassa az alábbi parancsot az Azure-erőforráscsoport létrehozásához: 

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ``` 
    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$resourceGroupName` változóhoz, majd futtassa újra a parancsot.

4. Adjon meg egy változót az adat-előállító nevéhez. 

    > [!IMPORTANT]
    >  Frissítse az adat-előállító nevét, hogy globálisan egyedi legyen. Például: ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
5. Az adat-előállító létrehozásához futtassa a következő **Set-AzDataFactoryV2** parancsmagát: 
    
    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Vegye figyelembe a következő szempontokat:

* Az adat-előállító nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra:

    ```powershell
    Set-AzDataFactoryV2 : HTTP Status Code: Conflict
    Error Code: DataFactoryNameInUse
    Error Message: The specified resource name 'ADFIncMultiCopyTutorialFactory' is already in use. Resource names must be globally unique.
    ```

* Adatelőállító-példányok létrehozásához a felhasználói fióknak, amellyel bejelentkezik az Azure-ba, a közreműködő vagy tulajdonos szerepkörök tagjának, vagy az Azure-előfizetés rendszergazdájának kell lennie.

* Azon Azure-régiók megtekintéséhez, amelyekben jelenleg elérhető a Data Factory, a következő lapon válassza ki az Önt érdeklő régiókat, majd bontsa ki az **Elemzés** részt, és keresse meg a **Data Factory**: [Elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) szakaszt. Az adat-előállítók által használt adattárak (Azure Storage, SQL Database stb.) és számítási erőforrások (Azure HDInsight stb.) más régiókban is lehetnek.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]

## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása

Társított szolgáltatásokat hoz létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóval történő társításához. Ebben a szakaszban a helyszíni SQL Server-adatbázishoz és az Azure SQL Database-hez kapcsolódó szolgáltatásokat hoz létre. 

### <a name="create-the-sql-server-linked-service"></a>Az SQL Server társított szolgáltatásának létrehozása

Ebben a lépésben a helyszíni SQL Server-adatbázist társítja az adat-előállítóval.

1. Hozzon létre egy **SqlServerLinkedService.json** nevű JSON-fájlt a C:\ADFTutorials\IncCopyMultiTableTutorial mappában (hozza létre a helyi mappákat, ha még nem léteznek) a következő tartalommal. Válassza ki az SQL Serverhez való kapcsolódáshoz használt hitelesítési módszernek megfelelő szakaszt.  

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

2. A PowerShellben futtassa a következő parancsmamot a C:\ADFTutorials\IncCopyMultiTableTutorial mappára való váltáshoz.

    ```powershell
    Set-Location 'C:\ADFTutorials\IncCopyMultiTableTutorial'
    ```

3. Futtassa a **Set-AzDataFactoryV2LinkedService** parancsmag a csatolt szolgáltatás AzureStorageLinkedService létrehozásához. A következő példában a *ResourceGroupName* és a *DataFactoryName* paraméterek értékeit adja meg: 

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

1. Hozzon létre egy **AzureSQLDatabaseLinkedService.json** nevű JSON-fájlt a C:\ADFTutorials\IncCopyMultiTableTutorial mappában a következő tartalommal. (Hozza létre az ADF mappát, ha még nem létezik.) A &lt;fájl&gt; &lt;mentése&gt;előtt &lt;cserélje&gt;le &lt;&gt; a kiszolgálónevet , az adatbázis nevét , a felhasználónevet és a jelszót az SQL Server adatbázis nevére, az adatbázis nevére, a felhasználónévre és a jelszóra. 

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
2. A PowerShellben futtassa a **Set-AzDataFactoryV2LinkedService** parancsmagot az AzureSQLDatabaseLinkedService csatolt szolgáltatás létrehozásához. 

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

2. Futtassa a **Set-AzDataFactoryV2Dataset** parancsmag létrehozásához az adatkészlet SourceDataset.
    
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

1. Hozzon létre egy **SinkDataset.json** nevű JSON-fájlt ugyanabban a mappában, amelynek tartalma a következő. A tableName elemet a folyamat állítja be dinamikusan, futásidőben. A folyamat ForEach tevékenysége végighalad a táblanevek listáján, és minden egyes ismétléskor átadja a táblanevet ennek az adatkészletnek. 

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

2. Futtassa a **Set-AzDataFactoryV2Dataset** parancsmag létrehozásához az adatkészlet SinkDataset.
    
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

1. Hozzon létre egy **WatermarkDataset.json** nevű JSON-fájlt ugyanabban a mappában a következő tartalommal: 

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
2. Futtassa a **Set-AzDataFactoryV2Dataset** parancsmag létrehozásához az adatkészlet VízmarkDataset.
    
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

A folyamat táblanevek listáját használja paraméterként. A **ForEach tevékenység** végighalad a táblanevek listáján, és a következő műveleteket hajtja végre: 

1. A **Keresés tevékenység segítségével** olvassa be a régi vízjel értékét (a kezdeti értéket vagy az utolsó iterációban használt értéket).

2. A **Keresés tevékenységgel** beolvassa az új vízjel értéket (a forrástábla vízjeloszlopának maximális értékét).

3. A **Másolás tevékenységgel** adatokat másolhat a forrásadatbázisból a céladatbázisba a két vízjel érték között.

4. A **StoredProcedure tevékenységgel** frissítheti a következő iteráció első lépésében használandó régi vízjelértéket. 

### <a name="create-the-pipeline"></a>A folyamat létrehozása

1. Hozzon létre egy **IncrementalCopyPipeline.json** nevű JSON-fájlt ugyanabban a mappában a következő tartalommal: 

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
2. Futtassa a **Set-AzDataFactoryV2Pipeline** parancsmag létrehozása a folyamat IncrementalCopyPipeline.
    
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

1. Hozzon létre egy **Parameters.json** nevű paraméterfájlt ugyanabban a mappában a következő tartalommal:

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
2. Futtassa a folyamatot IncrementalCopyPipeline az **Invoke-AzDataFactoryV2Pipeline** parancsmag használatával. Cserélje le a helyőrzőket a saját erőforráscsoportja és adat-előállítója nevére.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>A folyamat figyelése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

2. Kattintson a **Minden szolgáltatás** elemre, végezzen keresést az *Adat-előállítók* kulcsszóval, és válassza az **Adat-előállítók** lehetőséget. 

3. Keresse meg az adat-előállítóját az adat-előállítók listájában, és kattintson rá az **Adat-előállító** oldal megnyitásához. 

4. A **Data factory (Gyári kapcsolat)** lapon válassza a **Szerzői & monitor** lehetőséget az Azure Data Factory külön lapon való elindításához.

5. A **Let's get started (Kezdjük)** lapon válassza a Bal oldali **Monitor** lehetőséget. 
![Folyamatfuttatások](media/doc-common-process/get-started-page-monitor-button.png)    

6. Itt megtekintheti az összes folyamatfuttatást és azok állapotát. A következő példában a folyamatfuttatás állapota **Sikeres**. A **Paraméterek** oszlopban található hivatkozásra kattintva megtekintheti a folyamatnak átadott paramétereket. Hiba esetén egy hivatkozás jelenik meg a **Hiba** oszlopban.

    ![Folyamatfuttatások](media/tutorial-incremental-copy-multiple-tables-powershell/monitor-pipeline-runs-4.png)    
7. Amikor kiválasztja a **hivatkozást** a Műveletek oszlopban, láthatja, hogy a folyamat összes tevékenysége fut. 

8. Ha vissza szeretne lépni a **Folyamatfuttatások** nézetbe, válassza az **Összes folyamatfuttatás lehetőséget.** 

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
2. Monitorozza folyamatfuttatásokat [A folyamat figyelése](#monitor-the-pipeline) szakasz utasításait alapján. Ha a folyamat állapota **Folyamatban**van, a Folyamat futtatásának megszakításához **a Műveletek** csoportban egy másik műveletkapcsolat jelenik meg. 

3. Válassza ki a **Frissítés** elemet, hogy a lista folyamatosan frissüljön, amíg a folyamat futtatása be nem fejeződik. 

4. Szükség esetén kattintson a **Tevékenységfuttatások megtekintése** hivatkozásra a **Műveletek** területen a folyamatfuttatáshoz társított tevékenységfuttatások megtekintéséhez. 

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


