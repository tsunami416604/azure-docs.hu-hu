---
title: Az SQL Server tárolt eljárási tevékenység
description: Ismerje meg, hogyan használhatja az SQL Server tárolt eljárási tevékenység egy tárolt eljárást az Azure SQL Database vagy Azure SQL Data Warehouse a Data Factory-folyamatok meghívásához.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: douglasl
robots: noindex
ms.openlocfilehash: 3f13cb2626394d16a127b172bb69c4ab88121cdb
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352529"
---
# <a name="sql-server-stored-procedure-activity"></a>Az SQL Server tárolt eljárási tevékenység
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-tevékenység](data-factory-hive-activity.md) 
> * [Pig-tevékenység](data-factory-pig-activity.md)
> * [MapReduce-tevékenység](data-factory-map-reduce.md)
> * [Hadoop Streamelési tevékenységben](data-factory-hadoop-streaming-activity.md)
> * [Spark-tevékenység](data-factory-spark.md)
> * [Machine Learning kötegelt végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning Update-erőforrástevékenység](data-factory-azure-ml-update-resource-activity.md)
> * [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md)
> * [.NET egyéni tevékenység](data-factory-use-custom-activities.md)

> [!NOTE]
> Ez a cikk az Azure Data Factory 1-es verziójára vonatkozik. Ha a jelenlegi verzió a Data Factory szolgáltatás használ, tekintse meg [adatok átalakítása a tárolt eljárási tevékenység használatával a Data Factory](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Áttekintés
Adat-átalakítási tevékenységeket használ egy Data factoryben [folyamat](data-factory-create-pipelines.md) átalakításához, és a nyers adatok feldolgozása az előrejelzési díjat és az elemzések. A tárolt eljárási tevékenység, amely támogatja a Data Factory az Adatátalakítási tevékenységek egyike. Ez a cikk épül, amely a [adat-átalakítási tevékenységeket](data-factory-data-transformation-activities.md) című cikket, amely megadja az adatok átalakítását és a támogatott Adatátalakítási tevékenységek a Data Factory általános áttekintése.

A tárolt eljárási tevékenység használatával a következő adattárakat egyikét a tárolt eljárás meghívása a vállalati vagy egy Azure virtuális gépen (VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- Az SQL Server-adatbázis.  Ha az SQL Servert használ, telepítse az adatkezelési átjáró ugyanarra a gépre, amelyen az adatbázis vagy egy külön számítógépen, amely hozzáféréssel rendelkezik az adatbázishoz. Az adatkezelési átjáró, egy összetevő, amely kapcsolódik az adatokat a helyszíni és az Azure VM cloud services használatával adatforrások egy biztonságos és felügyelt módon. Lásd: [adatkezelési átjáró](data-factory-data-management-gateway.md) részleteivel.

> [!IMPORTANT]
> Ha az adatok másolása az Azure SQL Database vagy SQL Server, konfigurálhatja úgy a **SqlSink** a másolási tevékenység meghívni egy tárolt eljárás használatával a **sqlWriterStoredProcedureName** tulajdonság. További információkért lásd: [másolási tevékenység a tárolt eljárás meghívása](data-factory-invoke-stored-procedure-from-copy-activity.md). A tulajdonság kapcsolatos információkért lásd az alábbi összekötő cikkek: [Az Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [az SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). Tárolt eljárás meghívása közben az adatok másolása az Azure SQL Data Warehouse-bA a másolási tevékenység használatával nem támogatott. De a tárolt eljárási tevékenység használatával egy SQL Data Warehouse a tárolt eljárás meghívása. 
>  
> Ha az adatok másolása az Azure SQL Database vagy SQL Server vagy az Azure SQL Data Warehouse, konfigurálhatja a **SqlSource** a másolási tevékenység használatával adatokat olvasni a forrásadatbázis tárolt eljárás meghívása a  **sqlReaderStoredProcedureName** tulajdonság. További információkért tekintse meg a következő összekötő cikkeket: [Az Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [az SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [az Azure SQL Data warehouse-bA](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)          


A következő bemutató egy folyamatban a tárolt eljárási tevékenység használatával egy Azure SQL database-ben tárolt eljárás meghívása. 

## <a name="walkthrough"></a>Útmutatás
### <a name="sample-table-and-stored-procedure"></a>Minta tábla és a tárolt eljárás
1. Hozza létre a következő **tábla** az SQL Server Management Studio vagy más eszköz teljesebb Azure SQL-adatbázisban. A datetimestamp oszlop, a dátum és idő, amikor jön létre a megfelelő azonosító.

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    Az azonosított egyedi azonosítóval rendelkezik, és a datetimestamp oszlop a dátum és idő, amikor jön létre a megfelelő azonosító.
    
    ![Mintaadatok](./media/data-factory-stored-proc-activity/sample-data.png)

    Ebben a példában a tárolt eljárás van egy Azure SQL Database-ben. Ha a tárolt eljárás egy Azure SQL Data Warehouse és az SQL Server-adatbázis, a módszer nagyon hasonlít. Az SQL Server-adatbázis, telepítenie kell egy [adatkezelési átjáró](data-factory-data-management-gateway.md).
2. Hozza létre a következő **tárolt eljárás** , amely az adatok beszúrása a **sampletable**.

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **Név** és **kis-és** paraméter (dátum és idő ebben a példában) meg kell egyeznie a folyamat/tevékenység a JSON a megadott paraméter. Ellenőrizze, hogy a tárolt eljárás definíciójában **@** paraméter előtagjaként is szolgál.

### <a name="create-a-data-factory"></a>Data factory létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a **új** a bal oldali menüben kattintson a **intelligencia és elemzés**, és kattintson a **adat-előállító**.

    ![Új adat-előállító](media/data-factory-stored-proc-activity/new-data-factory.png)    
3. Az a **új adat-előállító** panelen adja meg **SProcDF** neve. Az Azure Data Factory-nevek a következők **globálisan egyedi**. A nevére, az előállító sikeres létrehozása engedélyezése az adat-előállító neve előtag kell.

   ![Új adat-előállító](media/data-factory-stored-proc-activity/new-data-factory-blade.png)         
4. Válassza ki az **Azure-előfizetését**.
5. Az **Erőforráscsoportban** hajtsa végre a következő lépések egyikét:
   1. Kattintson a **új létrehozása** , és adja meg az erőforráscsoport nevét.
   2. Kattintson a **meglévő** , és válasszon ki egy meglévő erőforráscsoportot.  
6. Válassza ki a Data Factory **helyét**.
7. Válassza ki **rögzítés az irányítópulton** így láthatja a data factory az irányítópulton a következő bejelentkezéskor.
8. Kattintson a **Create** (Létrehozás) elemre a **New data factory** (Új data factory) panelen.
9. A létrehozás alatt álló data factory láthatja a **irányítópult** az Azure Portal. A data factory sikeres létrehozása után megjelenik a data factory oldal, amely megjeleníti a data factory tartalmát.

   ![Data Factory kezdőlap](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Egy Azure SQL társított szolgáltatás létrehozása
Miután létrehozta az adat-előállítót, létrehozhat egy Azure SQL társított szolgáltatást, amely az Azure SQL database, amely tartalmazza a sampletable tábla és usp_sample tárolt eljárást, az adat-előállítóhoz.

1. Kattintson a **Szerző és üzembe helyezése** a a **adat-előállító** paneljén **SProcDF** , indítsa el a Data Factory Editor.
2. Kattintson a **új adattároló** a parancs sávot, és válassza **Azure SQL Database**. A szerkesztőben Azure SQL társított szolgáltatás létrehozására szolgáló JSON-parancsfájl kell megjelennie.

   ![Új adattároló](media/data-factory-stored-proc-activity/new-data-store.png)
3. A JSON-parancsfájl a következő módosításokat:

   1. Cserélje le `<servername>` az Azure SQL Database-kiszolgáló nevét.
   2. Cserélje le `<databasename>` az adatbázissal, amelyben létrehozta a tábla és a tárolt eljárást.
   3. Cserélje le `<username@servername>` a felhasználói fiókkal, amely hozzáféréssel rendelkezik az adatbázishoz.
   4. Cserélje le `<password>` a felhasználói fiókhoz tartozó jelszót.

      ![Új adattároló](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. A társított szolgáltatás üzembe helyezéséhez kattintson **telepítés** a parancssávon. Győződjön meg arról, hogy megjelenik-e az AzureSqlLinkedService a bal oldali fanézetben.

    ![fanézet a társított szolgáltatás](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Kimeneti adatkészlet létrehozása
Meg kell adnia egy tárolt eljárási tevékenység a kimeneti adatkészletet, akkor is, ha a tárolt eljárás nem állít elő semmilyen adatot. A kimeneti adatkészletet, amely az ütemezését, a tevékenység (milyen gyakran a tevékenység futtatása – óránként, naponta stb.), mert ez. A kimeneti adatkészlet kell használnia egy **társított szolgáltatás** , amely egy Azure SQL Database vagy az Azure SQL Data Warehouse vagy a tárolt eljárás futtatásához használni kívánt SQL Server-adatbázis utal. A kimeneti adatkészlet szolgálhat arra, hogy adja át az eredmény a tárolt eljárás az ezt követő feldolgozásának egy másik tevékenység ([láncolási tevékenységek](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) a folyamat. Azonban a Data Factory nem automatikusan írni a tárolt eljárás kimenete ehhez az adatkészlethez. A tárolt eljárást, amely egy SQL-tábla kimeneti adatkészlete mutató ír. Bizonyos esetekben a kimeneti adatkészlet lehet egy **helyőrző adatkészlet** (egy adatkészletet, amely egy táblát, amely nem igazán tartalmaz a tárolt eljárás kimenetet mutat). Ez az adatkészlet helyőrző szolgál arra, hogy csak adja meg a tárolt eljárás tevékenység futtatásának ütemezését. 

1. Ha nem látja ezt a gombot, kattintson a három pontot  **További** kattintson az eszköztár **új adatkészlet**, és kattintson a **Azure SQL**. **Új adatkészlet** a parancssávon, majd válassza a **Azure SQL**.

    ![fanézet a társított szolgáltatás](media/data-factory-stored-proc-activity/new-dataset.png)
2. Másolja és illessze be a következő JSON-parancsfájl a, a JSON-szerkesztőt.

    ```JSON
    {                
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Az adatkészlet üzembe helyezéséhez kattintson **telepítés** a parancssávon. Győződjön meg arról, hogy az adatkészlet fanézetben.

    ![fanézet a társított szolgáltatások](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>SqlServerStoredProcedure tevékenységgel rendelkező folyamat létrehozása
Most hozzunk létre egy tárolt eljárási tevékenység a folyamat. 

Figyelje meg a következő tulajdonságokkal: 

- A **típus** tulajdonsága **SqlServerStoredProcedure**. 
- A **storedProcedureName** típus tulajdonságainak értéke **usp_sample** (a tárolt eljárás neve).
- A **storedProcedureParameters** szakasz tartalmaz egy paramétert nevű **DateTime**. Név és a kis-és a JSON-paraméter meg kell egyeznie a nevét és a kis-és a paraméter a tárolt eljárás-definícióban. Ha az egyik paraméter null értékű kell átadni, használja a szintaxist: `"param1": null` (csak kisbetűvel).
 
1. Ha nem látja ezt a gombot, kattintson a három pontot  **További** a parancssávon, majd **új adatcsatorna**.
2. Másolja és illessze be a következő JSON-kódrészletre:   

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "usp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
             "start": "2017-04-02T00:00:00Z",
             "end": "2017-04-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```
3. A folyamat üzembe helyezéséhez kattintson **telepítés** az eszköztáron.  

### <a name="monitor-the-pipeline"></a>A folyamat figyelése
1. A Data Factory Editor paneljeinek a bezárásához és a Data Factory panelre való visszatéréshez kattintson az **X**, majd a **Diagram** elemre.

    ![Diagram csempe](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. A **diagramnézet** áttekintést nyújt az oktatóanyagban használt folyamatokról és adatkészletekről.

    ![Diagram csempe](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. A diagramnézetben kattintson duplán a az adatkészlet `sprocsampleout`. Láthatja, hogy a szelet kész állapotban. Lehetnek öt szeletek, mert a szelet előállítása minden óra között a kezdő és záró időpontot a JSON-ból.

    ![Diagram csempe](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Ha a szelet szerepel **készen** állapotáról, futtassa egy `select * from sampletable` -lekérdezést az Azure SQL database, győződjön meg arról, hogy az adatok állapotszolgáltatást nem található a táblába a tárolt eljárás.

   ![Kimeneti adatok](./media/data-factory-stored-proc-activity/output.png)

   Lásd: [a folyamat figyelése](data-factory-monitor-manage-pipelines.md) részletes információ az Azure Data Factory-folyamatok figyelése.  


## <a name="specify-an-input-dataset"></a>Adja meg a bemeneti adatkészlet
A forgatókönyv a tárolt eljárási tevékenység nem rendelkezik minden olyan bemeneti adatkészletek. Ha megad egy bemeneti adatkészlet, a tárolt eljárási tevékenység befejezéséig nem fut a bemeneti adatkészlet adatszeletének érhető el (kész állapot). Az adatkészlet külső adatkészlet (azaz nem hozzák az azonos folyamat egy másik tevékenységének) és a egy belső adatkészletet (a tevékenység előtt ezt a tevékenységet futtató) felsőbb rétegbeli tevékenység által előállított is lehetnek. A tárolt eljárás tevékenység több bemeneti adatkészletet is megadhat. Ha így tesz, a tárolt eljárási tevékenység fut, csak akkor, ha a bemeneti adatkészlet összes szelet érhető el (kész állapot). A bemeneti adatkészlet paraméterként az a tárolt eljárás nem felhasznált. Csak a tárolt eljárási tevékenység megkezdése előtt ellenőrizze, a függőség szolgál.

## <a name="chaining-with-other-activities"></a>Láncolás – más tevékenységek
Ha azt szeretné, ezt a tevékenységet a felsőbb rétegbeli tevékenység összekapcsolja, adja meg a felsőbb szintű tevékenység kimenetét, ez a tevékenység bemeneteként. Ha így tesz, a tárolt eljárási tevékenység befejezéséig nem fut a felsőbb szintű tevékenység befejezése és a felsőbb rétegbeli tevékenység kimeneti adatkészlete (kész állapot) érhető el. Megadhatja a kimeneti adatkészletek több felsőbb rétegbeli tevékenység bemeneti adatkészletek a tárolt eljárás tevékenység. Ha így tesz, a tárolt eljárási tevékenység fut, csak akkor, ha a bemeneti adatkészlet összes szelet érhetők el.  

A következő példában a másolási tevékenység kimenete: OutputDataset, amely a tárolt eljárás tevékenység bemeneti. Ezért a tárolt eljárási tevékenység befejezéséig nem fut a másolási tevékenység befejezése és a OutputDataset szelet érhető el (a kész állapot). Ha több bemeneti adatkészletet ad meg, a tárolt eljárási tevékenység befejezéséig nem fut a bemeneti adatkészlet összes szelet érhető el (kész állapot). A bemeneti adatkészlet nem használható közvetlenül a tárolt eljárási tevékenység paraméterekként. 

A láncolási tevékenységek további információkért lásd: [több tevékenységet a folyamat](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

```json
{

    "name": "ADFTutorialPipeline",
    "properties": {
        "description": "Copy data from a blob to blob",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [ { "name": "InputDataset" } ],
                "outputs": [ { "name": "OutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst"
                },
                "name": "CopyFromBlobToSQL"
            },
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "SPSproc"
                },
                "inputs": [ { "name": "OutputDataset" } ],
                "outputs": [ { "name": "SQLOutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunStoredProcedure"
            }

        ],
        "start": "2017-04-12T00:00:00Z",
        "end": "2017-04-13T00:00:00Z",
        "isPaused": false,
    }
}
```

Hasonlóképpen a tároló eljárás tevékenységet mutató hivatkozást **alsóbb rétegbeli tevékenység** (a tevékenység, amely a tárolt eljárási tevékenység befejezése után), adja meg a kimeneti adatkészlet a tárolt eljárás tevékenység bemeneteként a alsóbb rétegbeli tevékenység a folyamat.

> [!IMPORTANT]
> Ha az adatok másolása az Azure SQL Database vagy SQL Server, konfigurálhatja úgy a **SqlSink** a másolási tevékenység meghívni egy tárolt eljárás használatával a **sqlWriterStoredProcedureName** tulajdonság. További információkért lásd: [másolási tevékenység a tárolt eljárás meghívása](data-factory-invoke-stored-procedure-from-copy-activity.md). A tulajdonságra vonatkozó további információért tekintse meg a következő összekötő cikkeket: [Az Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [az SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
>  
> Ha az adatok másolása az Azure SQL Database vagy SQL Server vagy az Azure SQL Data Warehouse, konfigurálhatja a **SqlSource** a másolási tevékenység használatával adatokat olvasni a forrásadatbázis tárolt eljárás meghívása a  **sqlReaderStoredProcedureName** tulajdonság. További információkért tekintse meg a következő összekötő cikkeket: [Az Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [az SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [az Azure SQL Data warehouse-bA](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)          

## <a name="json-format"></a>JSON-formátumban
Itt látható a JSON-formátumban, egy tárolt eljárási tevékenység definiálása:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs":  [ { "name": "inputtable"  } ],
    "outputs":  [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":  
        {
            "param1": "param1Value"
            …
        }
    }
}
```

A következő táblázat ismerteti a JSON-tulajdonságokról:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| név | A tevékenység neve |Igen |
| leírás |Mire használható a tevékenységet leíró szöveg |Nem |
| type | Értékre kell állítani: **SqlServerStoredProcedure** | Igen |
| bemenetek | Választható. Ha megad egy bemeneti adatkészlet, elérhetőnek kell lennie (a "Kész" állapot) a tárolt eljárás tevékenység futtatásához. A bemeneti adatkészlet paraméterként az a tárolt eljárás nem felhasznált. Csak a tárolt eljárási tevékenység megkezdése előtt ellenőrizze, a függőség szolgál. |Nem |
| kimenetek | Meg kell adnia egy tárolt eljárási tevékenység a kimeneti adatkészletet. Kimeneti adatkészlet határozza meg a **ütemezés** a tárolt eljárás tevékenység (óránként, heti, havi, stb.). <br/><br/>A kimeneti adatkészlet kell használnia egy **társított szolgáltatás** , amely egy Azure SQL Database vagy az Azure SQL Data Warehouse vagy a tárolt eljárás futtatásához használni kívánt SQL Server-adatbázis utal. <br/><br/>A kimeneti adatkészlet szolgálhat arra, hogy adja át az eredmény a tárolt eljárás az ezt követő feldolgozásának egy másik tevékenység ([láncolási tevékenységek](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) a folyamat. Azonban a Data Factory nem automatikusan írni a tárolt eljárás kimenete ehhez az adatkészlethez. A tárolt eljárást, amely egy SQL-tábla kimeneti adatkészlete mutató ír. <br/><br/>Bizonyos esetekben a kimeneti adatkészlet lehet egy **helyőrző adatkészlet**, amely segítségével csak adja meg a tárolt eljárás tevékenység futtatásának ütemezését. |Igen |
| storedProcedureName |Adja meg a tárolt eljárás neve az Azure SQL database vagy Azure SQL Data Warehouse vagy SQL Server-adatbázis, amely a kimeneti tábla használja a társított szolgáltatás által jelölt. |Igen |
| storedProcedureParameters |Adja meg a tárolt eljárás paramétereihez tartozó értékek. Adja át az egyik paraméter null értékű kell, ha a szintaxissal: "param1": null (csupa kisbetű). Tekintse meg a következő mintát, ez a tulajdonság használatát mutatja. |Nem |

## <a name="passing-a-static-value"></a>Állandó érték átadása
Most vegyünk egy másik, a "Forgatókönyv" nevű oszlop hozzáadása a nevezett dokumentum-minta statikus értéket tartalmazó tábla.

![Mintaadatok 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Tábla:**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**Tárolt eljárás:**

```SQL
CREATE PROCEDURE usp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Most adja át a **forgatókönyv** paraméter és a tárolt eljárási tevékenység az értékét. A **typeProperties** szakaszban az előző példában a következő kódrészlethez hasonló néz ki:

```JSON
"typeProperties":
{
    "storedProcedureName": "usp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Data Factory-adatkészlet:**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Data Factory-folyamatok**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```
