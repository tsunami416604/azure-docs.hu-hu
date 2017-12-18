---
title: "Több tábla növekményes másolása az Azure Data Factory használatával | Microsoft Docs"
description: "Az oktatóanyag során egy Azure Data Factory-folyamatot hoz létre, amely egy helyszíni SQL Server több táblájának módosított adatait másolja növekményesen egy Azure SQL Database-be. "
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
ms.date: 12/01/2017
ms.author: jingwang
ms.openlocfilehash: 2d9213a74fd881a7be52f51ff8ebb49171c77283
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="incrementally-load-data-from-multiple-tables-in-sql-server-to-azure-sql-database"></a>Adatok növekményes betöltése az SQL Server több táblájából az Azure SQL Database adatbázisba
Az oktatóanyag során egy Azure-beli adat-előállítót hoz létre egy olyan folyamattal, amely változásadatokat tölt be egy helyszíni SQL Server több táblájából egy Azure SQL Database-be.    

Az oktatóanyagban az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Forrás- és céladattárak előkészítése.
> * Adat-előállító létrehozása
> * Helyi integrációs modul (IR) létrehozása
> * Integrációs modul telepítése 
> * Társított szolgáltatások létrehozása. 
> * Forrás-, fogadó- és küszöbadatkészletek létrehozása.
> * Folyamat létrehozása, futtatása és figyelése.
> * Eredmények áttekintése
> * Adatok hozzáadása vagy frissítése a forrástáblákban
> * A folyamat újrafuttatása és figyelése
> * A végeredmény áttekintése 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd [a Data Factory 1. verziójának dokumentációját](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="overview"></a>Áttekintés
Az alábbiak a megoldás kialakításának leglényegesebb lépései: 

1. **A küszöb oszlopának kiválasztása**.
    Jelölje ki az adatforrás egyes táblázatainak egy-egy oszlopát, amely alapján az új és a frissített rekordok minden egyes futtatáskor azonosíthatóak. Normális esetben az ebben a kiválasztott oszlopban (például: last_modify_time vagy ID) lévő adatok a sorok létrehozásával vagy frissítésével folyamatosan növekednek. Az ebben az oszlopban lévő legnagyobb érték szolgál a küszöbként.
2. **Egy adatraktár előkészítése a küszöbértékek tárolására**.   
    Ebben az oktatóanyagban a küszöbértékeket egy Azure SQL-adatbázisban tároljuk.
3. **Egy folyamat létrehozása a következő tevékenységekkel:** 
    
    1. Egy **ForEach** tevékenység létrehozása, amely végighalad a forrástáblanevek listáján, amelyet a rendszer paraméterként ad át a folyamatnak. Minden forrástáblához elindítja a következő tevékenységeket a változásadatok betöltéséhez az adott tábla esetében. 
    2. Két **keresési** tevékenység létrehozása. Az első keresési tevékenység az utolsó küszöbértéket kéri le. A második keresési tevékenység az új küszöbértéket kéri le. Ezeket a küszöbértékeket a rendszer átadja a másolási tevékenységnek. 
    3. Egy **másolási tevékenység** létrehozása, amely a küszöbértéket tartalmazó oszlopban a régi küszöbértéknél magasabb, az új küszöbértéknél alacsonyabb értékkel rendelkező sorokat másolja át a forrásadattárból. Ezután a módosított adatokat a forrásadattárból új fájlként egy blobtárolóba másolja. 
    4. Egy **tárolt eljárási tevékenység** létrehozása, amely frissíti a küszöbértékeket a folyamat következő futtatásához. 

        Itt látható a megoldás összefoglaló jellegű ábrája: 

        ![Adatok növekményes betöltése](media\tutorial-incremental-copy-multiple-tables-powershell\high-level-solution-diagram.png)


Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="prerequisites"></a>Előfeltételek
* **Egy SQL Server**. Ebben az oktatóanyagban egy helyszíni SQL Server-adatbázist használ **forrásadattárként**. 
* **Azure SQL Database** Egy Azure SQL Database adatbázist használ **fogadóadattárként**. Ha még nem rendelkezik Azure SQL Database-adatbázissal, a létrehozás folyamatáért lásd az [Azure SQL-adatbázis létrehozását](../sql-database/sql-database-get-started-portal.md) ismertető cikket. 

### <a name="create-source-tables-in-your-sql-server-database"></a>Forrástáblák létrehozása az SQL Server-adatbázisban

1. Indítsa el az **SQL Server Management Studiót**, és csatlakozzon a helyszíni SQL Serverhez. 
2. A **Kiszolgálókeresőben** kattintson a jobb gombbal az adatbázisra, és válassza az **Új lekérdezés** elemet.
3. Futtassa a következő SQL-parancsot az adatbázison a `customer_table` és a `project_table` nevű tábla létrehozásához.

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

### <a name="create-destination-tables-in-your-azure-sql--database"></a>Céltáblák létrehozása az Azure SQL Database-ben
1. Indítsa el az **SQL Server Management Studiót**, és csatlakozzon az Azure SQL Serverhez. 
2. A **Kiszolgálókezelőben** kattintson a jobb gombbal az **adatbázisra**, és válassza az **Új lekérdezés** elemet.
3. Futtassa a következő SQL-parancsot az Azure SQL Database-ben a `customer_table` és a `project_table` nevű tábla létrehozásához.  
    
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

### <a name="create-another-table-in-azure-sql-database-to-store-the-high-watermark-value"></a>Egy másik tábla létrehozása az Azure SQL Database-ben a felső küszöbértékek tárolására
1. Futtassa a következő SQL-parancsot az Azure SQL-adatbázison egy tábla `watermarktable` néven, a küszöbértékek tárolására történő létrehozásához.  
    
    ```sql
    create table watermarktable
    (
    
        TableName varchar(255),
        WatermarkValue datetime,
    );
    ```
3. Szúrja be a két forrástábla kezdeti küszöbértékeit a küszöbértékek táblájába.

    ```sql

    INSERT INTO watermarktable
    VALUES 
    ('customer_table','1/1/2010 12:00:00 AM'),
    ('project_table','1/1/2010 12:00:00 AM');
    
    ```

### <a name="create-a-stored-procedure-in-azure-sql-database"></a>Tárolt eljárás létrehozása az Azure SQL-adatbázisban 

Az alábbi parancs futtatásával hozzon létre egy tárolt eljárást az Azure SQL-adatbázisban. Ez a tárolt eljárás minden folyamatfuttatás után frissíti a küszöbértéket. 

```sql
CREATE PROCEDURE sp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

    UPDATE watermarktable
    SET [WatermarkValue] = @LastModifiedtime 
WHERE [TableName] = @TableName

END

```

### <a name="create-data-types-and-additional-stored-procedures"></a>Adattípusok és további tárolt eljárások létrehozása
Hozzon létre két tárolt eljárást és két adattípust az Azure SQL Database-ben a következő lekérdezés futtatásával. Ezek az eljárások összevonják a forrástáblák adatait a céltáblákba.

```sql
CREATE TYPE DataTypeforCustomerTable AS TABLE(
    PersonID int,
    Name varchar(255),
    LastModifytime datetime
);

GO

CREATE PROCEDURE sp_upsert_customer_table @customer_table DataTypeforCustomerTable READONLY
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

CREATE PROCEDURE sp_upsert_project_table @project_table DataTypeforProjectTable READONLY
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
Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-azurerm-ps) ismertető cikkben szereplő utasításokat a legújabb Azure PowerShell-modulok telepítéséhez.

## <a name="create-a-data-factory"></a>Data factory létrehozása
1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) nevét idézőjelek között, majd futtassa a parancsot. Például: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$resourceGroupName` változóhoz, majd futtassa újra a parancsot
2. Adjon meg egy változót az adat-előállító helyéhez: 

    ```powershell
    $location = "East US"
    ```
3. Futtassa az alábbi parancsot az Azure-erőforráscsoport létrehozásához: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$resourceGroupName` változóhoz, majd futtassa újra a parancsot. 
3. Adjon meg egy változót az adat-előállító nevéhez. 

    > [!IMPORTANT]
    >  Frissítse az adat-előállító nevét, hogy globálisan egyedi legyen. Például: ADFIncMultiCopyTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFIncMultiCopyTutorialFactory";
    ```
5. Az adat-előállító létrehozásához futtassa az alábbi **Set-AzureRmDataFactoryV2** parancsmagot: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

Vegye figyelembe a következő szempontokat:

* Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.

    ```
    The specified Data Factory name 'ADFIncMultiCopyTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory-példányok létrehozásához a felhasználói fióknak, amellyel belép az Azure-ba, a **közreműködő** vagy **tulajdonos** szerepkörök tagjának, vagy az Azure-előfizetés **rendszergazdájának** kell lennie.
* A Data Factory 2-es verziója jelenleg csak az USA keleti régiójában, az USA 2. keleti régiójában és a nyugat-európai régióban teszi lehetővé adat-előállítók létrehozását. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

[!INCLUDE [data-factory-create-install-integration-runtime](../../includes/data-factory-create-install-integration-runtime.md)]



## <a name="create-linked-services"></a>Társított szolgáltatások létrehozása
Társított szolgáltatásokat hoz létre egy adat-előállítóban az adattárak és a számítási szolgáltatások adat-előállítóval történő társításához. Ebben a szakaszban a helyszíni SQL Server-adatbázissal és az Azure SQL Database-zel társított szolgáltatásokat hoz létre. 

### <a name="create-sql-server-linked-service"></a>SQL Server társított szolgáltatásának létrehozása.
Ebben a lépésben a helyszíni SQL Servert társítja az adat-előállítóval.

1. Hozzon létre egy **SqlServerLinkedService.json** nevű JSON-fájlt a **C:\ADFTutorials\IncCopyMultiTableTutorial** mappában a következő tartalommal. Válassza ki a megfelelő szakaszt az SQL Serverhez való csatlakozáshoz használt **hitelesítési** módszernek megfelelően. Hozza létre a helyi mappákat, ha még nem léteznek. 

    > [!IMPORTANT]
    > Válassza ki az SQL Serverhez való kapcsolódáshoz használt **hitelesítési** módszernek megfelelő szakaszt.

    **SQL-hitelesítés (sa) használata esetén másolja a következő JSON-definíciót:**

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
    **Windows-hitelesítés használata esetén másolja a következő JSON-definíciót:**

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<server>;Database=<database>;Integrated Security=True"
                },
                "userName": "<user> or <domain>\\<user>",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
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
    > [!IMPORTANT]
    > - Válassza ki az SQL Serverhez való kapcsolódáshoz használt **hitelesítési** módszernek megfelelő szakaszt.
    > - Cserélje le az **&lt;integration** **runtime** **name>** értéket az integrációs modul nevére.
    > - A fájl mentése előtt a **&lt;servername>**, **&lt;databasename>**, **&lt;username>** és **&lt;password>** értékeket cserélje le az SQL Server értékeire.
    > - Ha perjel karaktert (`\`) kell használnia a felhasználói fiók vagy a kiszolgáló nevében, használja az escape-karaktert (`\`). Például: `mydomain\\myuser`.

2. Az **Azure PowerShellben** váltson az **C:\ADFTutorials\IncCopyMultiTableTutorial** mappára.
3. Futtassa a **Set-AzureRmDataFactoryV2LinkedService** parancsmagot az **AzureStorageLinkedService** társított szolgáltatás létrehozásához. A következő példában a **ResourceGroupName** és a **DataFactoryName** paraméter értékeit fogja megadni. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerLinkedService" -File ".\SqlServerLinkedService.json"
    ```

    Itt látható a minta kimenete:

    ```json
    LinkedServiceName : SqlServerLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerLinkedService
    ```

### <a name="create-azure-sql-database-linked-service"></a>Azure SQL Database-beli társított szolgáltatás létrehozása.
1. Hozzon létre egy **AzureSQLDatabaseLinkedService.json** nevű JSON-fájlt a **C:\ADFTutorials\IncCopyMultiTableTutorial** mappában az alábbi tartalommal (ha még nem létezik, hozza létre az ADF mappát). Mielőtt mentené a fájlt, a **&lt;server&gt;, a &lt;database name&gt;, a &lt;user id&gt; és a &lt;password&gt;** helyőrzőt cserélje le az Azure SQL Server nevére, az adatbázis nevére, a felhasználói azonosítóra és a jelszóra. 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name>; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. Az **Azure PowerShellben** futtassa a **Set-AzureRmDataFactoryV2LinkedService** parancsmagot az **AzureSqlDatabaseLinkedService** társított szolgáltatás létrehozásához. 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Itt látható a minta kimenete:

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Adatkészletek létrehozása
Ebben a lépésben adatkészleteket hoz létre, amelyek az adatforrást, az adatcél helyét és a küszöbértékek tárolási helyét jelölik.

### <a name="create-a-source-dataset"></a>Forrásadatkészlet létrehozása

1. Hozzon létre egy **SourceDataset.json** nevű JSON-fájlt ugyanebben a mappában az alábbi tartalommal: 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dummyName"
            },
            "linkedServiceName": {
                "referenceName": "SqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
   
    ```

    A tábla neve egy helyőrző. A teljes tábla betöltése helyett a folyamat másolási tevékenysége egy SQL-lekérdezést használ az adatok betöltéséhez. 
1.  Futtassa a Set-AzureRmDataFactoryV2Dataset parancsmagot a SourceDataset adatkészlet létrehozásához.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Itt látható a parancsmag mintakimenete:
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Fogadó adatkészlet létrehozása

1. Hozzon létre egy **SinkDataset.json** nevű JSON-fájlt ugyanebben a mappában az alábbi tartalommal. A tableName értéket a folyamat állítja be dinamikusan futásidőben. A folyamat ForEach tevékenysége végighalad a táblanevek listáján, és minden egyes ismétléskor átadja a táblanevet ennek az adatkészletnek. 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().SinkTableName}",
                    "type": "Expression"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "parameters": {
                "SinkTableName": {
                    "type": "String"
                }
            }
        }
    }
    ```

2.  Futtassa a Set-AzureRmDataFactoryV2Dataset parancsmagot a SinkDataset adatkészlet létrehozásához.
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Itt látható a parancsmag mintakimenete:
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-watermark"></a>Adatkészlet létrehozása a küszöbhöz
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
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : <data factory name>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Folyamat létrehozása
A folyamat táblanevek listáját használja paraméterként. A **ForEach tevékenység** végighalad a táblanevek listáján, és elvégzi a következő műveleteket: 

1. A **keresési tevékenység** lekéri a régi küszöbértéket (a kezdeti értéket, vagy az utolsó ismétlés során használt értéket).
2. A **keresési tevékenység** lekéri az új küszöbértéket (a forrástábla küszöbértéket tartalmazó oszlopának legnagyobb értékét).
3. A **másolási tevékenység** az előbbi két küszöbérték közötti adatokat másolja a forrásadatbázisból a céladatbázisba. 
4. A **tárolt eljárási tevékenység** frissíti a régi küszöbértéket, hogy a következő ismétlés első lépése azt használja. 

### <a name="create-the-pipeline"></a>A folyamat létrehozása
1. Hozzon létre egy IncrementalCopyPipeline.json nevű JSON-fájlt ugyanebben a mappában az alábbi tartalommal: 

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [{
    
                "name": "IterateSQLTables",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "false",
                    "items": {
                        "value": "@pipeline().parameters.tableList",
                        "type": "Expression"
                    },
    
                    "activities": [
                        {
                            "name": "LookupOldWaterMarkActivity",
                            "type": "Lookup",
                            "typeProperties": {
                                "source": {
                                    "type": "SqlSource",
                                    "sqlReaderQuery": "select * from watermarktable where TableName  =  '@{item().TABLE_NAME}'"
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
                                    "sqlReaderQuery": "select MAX(@{item().WaterMark_Column}) as NewWatermarkvalue from @{item().TABLE_NAME}"
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
                                    "sqlReaderQuery": "select * from @{item().TABLE_NAME} where @{item().WaterMark_Column} > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and @{item().WaterMark_Column} <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                                },
                                "sink": {
                                    "type": "SqlSink",
                                    "SqlWriterTableType": "@{item().TableType}",
                                    "SqlWriterStoredProcedureName": "@{item().StoredProcedureNameForMergeOperation}"
                                }
                            },
                            "dependsOn": [{
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
    
                            "inputs": [{
                                "referenceName": "SourceDataset",
                                "type": "DatasetReference"
                            }],
                            "outputs": [{
                                "referenceName": "SinkDataset",
                                "type": "DatasetReference",
                                "parameters": {
                                    "SinkTableName": "@{item().TABLE_NAME}"
                                }
                            }]
                        },
    
                        {
                            "name": "StoredProceduretoWriteWatermarkActivity",
                            "type": "SqlServerStoredProcedure",
                            "typeProperties": {
    
                                "storedProcedureName": "sp_write_watermark",
                                "storedProcedureParameters": {
                                    "LastModifiedtime": {
                                        "value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}",
                                        "type": "datetime"
                                    },
                                    "TableName": {
                                        "value": "@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}",
                                        "type": "String"
                                    }
                                }
                            },
    
                            "linkedServiceName": {
                                "referenceName": "AzureSQLDatabaseLinkedService",
                                "type": "LinkedServiceReference"
                            },
    
                            "dependsOn": [{
                                "activity": "IncrementalCopyActivity",
                                "dependencyConditions": [
                                    "Succeeded"
                                ]
                            }]
                        }
    
                    ]
    
                }
            }],
    
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
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
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : ADFIncMultiCopyTutorialFactory1201
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```
 
## <a name="run-the-pipeline"></a>A folyamat futtatása

1. Hozzon létre egy **Parameters.json** nevű paraméterfájlt ugyanebben a mappában az alábbi tartalommal:

    ```json
    {
        "tableList": 
        [
            {
                "TABLE_NAME": "customer_table",
                "WaterMark_Column": "LastModifytime",
                "TableType": "DataTypeforCustomerTable",
                "StoredProcedureNameForMergeOperation": "sp_upsert_customer_table"
            },
            {
                "TABLE_NAME": "project_table",
                "WaterMark_Column": "Creationtime",
                "TableType": "DataTypeforProjectTable",
                "StoredProcedureNameForMergeOperation": "sp_upsert_project_table"
            }
        ]
    }
    ```
2. Futtassa az **IncrementalCopyPipeline** folyamatot az **Invoke-AzureRmDataFactoryV2Pipeline** parancsmag használatával. Cserélje le a helyőrzőket a saját erőforráscsoportja és adat-előállítója nevére.

    ```powershell
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"        
    ``` 

## <a name="monitor-the-pipeline"></a>A folyamat figyelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson a **További szolgáltatások** elemre, végezzen keresést a `data factories` kulcsszóval, és válassza az **Adat-előállítók** lehetőséget. 

    ![Adat-előállítók menü](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-data-factories-menu-1.png)
3. Keresse meg az **adat-előállítóját** az adat-előállítók listájában, és kattintson rá az Adat-előállító lap megnyitásához. 

    ![Az adat-előállító keresése](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-search-data-factory-2.png)
4. Az Adat-előállító lapon kattintson a **Figyelés és felügyelet** csempére. 

    ![Monitor & Manage csempe](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-monitor-manage-tile-3.png)    
5. Megnyílik az **adatintegrációs alkalmazás** egy új lapon. Itt megtekintheti az összes **folyamatfuttatást** és azok állapotát. A következő példában a folyamatfuttatás állapota **Sikeres**. A **Paraméterek** oszlopban található hivatkozásra kattintva megtekintheti a folyamatnak átadott paramétereket. Hiba esetén egy hivatkozás jelenik meg a **Hiba** oszlopban. Kattintson a **Műveletek** oszlopban található hivatkozásra. 

    ![Folyamatfuttatások](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-4.png)    
6. Ha a **Műveletek** oszlopban található hivatkozásra kattint, megjelenik a következő lap, amely megmutatja a folyamat összes **tevékenységfuttatását**. 

    ![Tevékenységfuttatások](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-activity-runs-5.png)
7. A **Folyamatfuttatások** nézetre való visszaváltáshoz kattintson a **Folyamatok** lehetőségre a képen látható módon. 

## <a name="review-the-results"></a>Az eredmények áttekintése
Az SQL Server Management Studióban futtassa a következő lekérdezéseket a cél Azure SQL Database-en annak ellenőrzéséhez, hogy a rendszer átmásolta-e az adatokat a forrástáblákból a céltáblákba. 

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

**Lekérdezés:**

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

Mindét tábla küszöbértékei frissültek. 

## <a name="add-more-data-to-the-source-tables"></a>További adatok hozzáadása a forrástáblákhoz

Futtassa a következő lekérdezést a forrás SQL Server-adatbázison a customer_table meglévő sorának frissítéséhez és új sor beszúrásához a project_table táblába. 

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
    $RunId = Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupname -dataFactoryName $dataFactoryName -ParameterFile ".\Parameters.json"
    ```
2. Figyelje a folyamatfuttatásokat [A folyamat figyelése](#monitor-the-pipeline) szakasz utasításait követve. Mivel a folyamat állapota **Folyamatban**, egy másik művelethivatkozás jelenik meg a **Műveletek** alatt, amellyel megszakíthatja a folyamat futtatását. 

    ![Folyamatfuttatások](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-6.png)    
3. Kattintson a **Frissítés** elemre, hogy a lista folyamatosan frissüljön, amíg a folyamat futtatása be nem fejeződik. 

    ![Folyamatfuttatások](media\tutorial-incremental-copy-multiple-tables-powershell\monitor-pipeline-runs-succeded-7.png)
4. (nem kötelező) kattintson a **Tevékenységfuttatások megtekintése** hivatkozásra (ikonra) a Műveletek alatt a folyamatfuttatáshoz társított tevékenységfuttatások megtekintéséhez. 

## <a name="review-final-results"></a>A végeredmény áttekintése
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

A 3. számú PersonID új Name és LastModifytime értékkel rendelkezik. 

**Lekérdezés:**

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

A project_table táblához hozzá lett adva a NewProject bejegyzés. 

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

Mindét tábla küszöbértékei frissültek.
     
## <a name="next-steps"></a>Következő lépések
Az oktatóanyagban az alábbi lépéseket hajtotta végre: 

> [!div class="checklist"]
> * Forrás- és céladattárak előkészítése.
> * Adat-előállító létrehozása
> * Helyi integrációs modul (IR) létrehozása
> * Integrációs modul telepítése 
> * Társított szolgáltatások létrehozása. 
> * Forrás-, fogadó- és küszöbadatkészletek létrehozása.
> * Folyamat létrehozása, futtatása és figyelése.
> * Eredmények áttekintése
> * Adatok hozzáadása vagy frissítése a forrástáblákban
> * A folyamat újrafuttatása és figyelése
> * A végeredmény áttekintése 

Folytassa a következő oktatóanyaggal, amelyben az adatok Azure Spark-fürtök használatával való átalakítását ismerheti meg:

> [!div class="nextstepaction"]
>[Adatok növekményes betöltése az Azure SQL Database-ből az Azure Blob Storage-ba a változáskövetési technológia használatával](tutorial-incremental-copy-change-tracking-feature-powershell.md)


