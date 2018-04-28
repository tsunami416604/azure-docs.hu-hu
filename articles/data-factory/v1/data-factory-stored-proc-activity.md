---
title: SQL Server tárolt eljárási tevékenység
description: Ismerje meg, hogyan használhatja az SQL Server tárolt eljárási tevékenység meghívni a Data Factory-folyamat az az Azure SQL Database vagy az Azure SQL Data Warehouse tárolt eljárást.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: douglasl
robots: noindex
ms.openlocfilehash: a628374841126a750ebf6881f8adec66340c1d29
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="sql-server-stored-procedure-activity"></a>SQL Server tárolt eljárási tevékenység
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive-tevékenység](data-factory-hive-activity.md) 
> * [A Pig-tevékenység](data-factory-pig-activity.md)
> * [MapReduce művelethez](data-factory-map-reduce.md)
> * [Hadoop Streamelési tevékenységben](data-factory-hadoop-streaming-activity.md)
> * [A Spark-tevékenység](data-factory-spark.md)
> * [Machine Learning kötegelt végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning Update-erőforrástevékenység](data-factory-azure-ml-update-resource-activity.md)
> * [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md)
> * [.NET egyéni tevékenység](data-factory-use-custom-activities.md)

> [!NOTE]
> Ez a cikk az Azure Data Factory általánosan elérhető 1. verziójára vonatkozik. Lásd a 2-es verziójának a Data Factory szolgáltatásnak, amely jelenleg előzetes verzióban érhető, használatakor [tárolt eljárási tevékenység adat-előállítóban 2-es verzióját használó adatok](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Áttekintés
Adatok átalakítása tevékenységek használata egy adat-előállítóban [csővezeték](data-factory-create-pipelines.md) átalakító és előrejelzéseket és elemzések nyers adatok feldolgozása. A tárolt eljárási tevékenység, amely támogatja a Data Factory átalakítása tevékenységek egyike. Ez a cikk épít, a [adatok átalakítása tevékenységek](data-factory-data-transformation-activities.md) cikk, amelynek során az adatok átalakítása és a Data Factory támogatott átalakítása tevékenységek általános áttekintést.

A tárolt eljárási tevékenység segítségével meghívása tárolt eljárás valamelyik a következő adatokat tárolja, a vállalati vagy egy Azure virtuális gépen (VM): 

- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server-adatbázis.  Ha SQL Server használ, telepítse az adatkezelési átjáró ugyanazon a számítógépen, amelyen az adatbázis vagy egy külön számítógépen, amely hozzáféréssel rendelkezik az adatbázishoz. Az adatkezelési átjáró egy összetevő, amely összeköti az adatok a helyszínen vagy a Azure VM a cloud serviceshez adatforrásokat felügyelt és biztonságos módon. Lásd: [az adatkezelési átjáró](data-factory-data-management-gateway.md) cikkben alább.

> [!IMPORTANT]
> Ha az adatok másolása az Azure SQL Database vagy az SQL Server, konfigurálhatja a **SqlSink** a másolási tevékenység tárolt eljárás használatával meghívni a **sqlWriterStoredProcedureName** tulajdonság. További információkért lásd: [meghívása tárolt eljárás a másolási tevékenység](data-factory-invoke-stored-procedure-from-copy-activity.md). A tulajdonság kapcsolatos tudnivalókért lásd az alábbi összekötő cikkek: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). Adatok másolása az Azure SQL Data Warehouse a másolási tevékenység során a tárolt eljárás meghívása nem támogatott. De a tárolt eljárási tevékenység segítségével az SQL Data Warehouse tárolt eljárás hívása. 
>  
> Ha az adatok másolása az Azure SQL Database vagy az SQL Server vagy az Azure SQL Data Warehouse, konfigurálhatja a **SqlSource** a másolási tevékenység meghívni egy tárolt eljárás a forrás-adatbázis használatával adatokat olvasni az **sqlReaderStoredProcedureName** tulajdonság. További információkért tekintse meg a következő összekötő-cikkek: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)          


A következő forgatókönyv egy folyamaton belül a tárolt eljárási tevékenység segítségével Azure SQL-adatbázisban tárolt eljárás hívása. 

## <a name="walkthrough"></a>Útmutatás
### <a name="sample-table-and-stored-procedure"></a>A minta-tábla és tárolt eljárás
1. Hozza létre a következő **tábla** az Azure SQL adatbázis SQL Server Management Studio vagy bármilyen más ismeri a Feladatkezelő segítségével. A datetimestamp oszlop, a dátum és idő, amikor jön létre a megfelelő Azonosítóhoz.

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
    Az egyedi azonosított és a datetimestamp oszlop dátumát és időpontját a megfelelő Azonosítóhoz létrehozásakor.
    
    ![Mintaadatok](./media/data-factory-stored-proc-activity/sample-data.png)

    Ez a példa a tárolt eljárás az Azure SQL adatbázis. A tárolt eljárás egy Azure SQL Data warehouse-bA és az SQL Server-adatbázis, a megközelítést akkor hasonló. SQL Server-adatbázis, telepítenie kell egy [az adatkezelési átjáró](data-factory-data-management-gateway.md).
2. Hozza létre a következő **tárolt eljárás** , amely beszúrja az adatokat a **sampletable**.

    ```SQL
    CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **Név** és **kis-és** a paraméter (ebben a példában szereplő DateTime) meg kell egyeznie a feldolgozási sor/tevékenység JSON megadott paramétert. Ügyeljen arra, hogy a tárolt eljárás definícióban **@** paraméter előtagjaként szolgál.

### <a name="create-a-data-factory"></a>Data factory létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a **új** a bal oldali menüben kattintson **Eszközintelligencia + analitika**, és kattintson a **adat-előállító**.

    ![Új adat-előállító](media/data-factory-stored-proc-activity/new-data-factory.png)    
3. Az a **új adat-előállító** panelen adjon meg **SProcDF** nevét. Az Azure Data Factory neve **globálisan egyedi**. A névvel, a gyári sikeres létrehozásának engedélyezése a data factory neve előtag van szüksége.

   ![Új adat-előállító](media/data-factory-stored-proc-activity/new-data-factory-blade.png)         
4. Válassza ki a **Azure-előfizetés**.
5. Az **Erőforráscsoportban** hajtsa végre a következő lépések egyikét:
   1. Kattintson a **hozzon létre új** , és adja meg az erőforráscsoport nevét.
   2. Kattintson a **meglévő** , és válasszon ki egy meglévő erőforráscsoportot.  
6. Válassza ki a Data Factory **helyét**.
7. Válassza ki **rögzítés az irányítópulton** , hogy megjelenik a data factory az irányítópulton jelentkezik be a következő alkalommal.
8. Kattintson a **Create** (Létrehozás) elemre a **New data factory** (Új data factory) panelen.
9. Megjelenik a data factory létrehozása a **irányítópult** az Azure portálról. A data factory sikeres létrehozása után megjelenik a data factory oldal, amely megjeleníti a data factory tartalmát.

   ![Data Factory kezdőlap](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Azure SQL társított szolgáltatás létrehozása
Miután létrehozta a data factory, létrehozhat egy Azure SQL társított szolgáltatás, amely az Azure SQL-adatbázis, amely tartalmazza a sampletable tábla és a sp_sample tárolt eljárás, hogy a data factory.

1. Kattintson a **Szerző és központi telepítése** a a **Data Factory** paneljén **SProcDF** a Data Factory Editor elindításához.
2. Kattintson a **az új adattároló** a parancs megnyitásához, és válassza a **Azure SQL Database**. A JSON-parancsfájl létrehozásához Azure SQL társított szolgáltatásnak a szerkesztővel kell megjelennie.

   ![Új adattároló](media/data-factory-stored-proc-activity/new-data-store.png)
3. A JSON-parancsfájl a következő módosításokat:

   1. Cserélje le `<servername>` nevű, az Azure SQL Database-kiszolgálóhoz.
   2. Cserélje le `<databasename>` az adatbázissal, amelyben létrehozta a tábla és a tárolt eljárást.
   3. Cserélje le `<username@servername>` a felhasználói fiókkal, amely hozzáféréssel rendelkezik az adatbázishoz.
   4. Cserélje le `<password>` a felhasználói fiók jelszavával.

      ![Új adattároló](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. A társított szolgáltatás telepítéséhez kattintson **telepítés** a parancssávon. Győződjön meg arról, hogy megjelenik-e a bal oldali fanézetben AzureSqlLinkedService.

    ![fanézet, a társított szolgáltatás](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Kimeneti adatkészlet létrehozása
Meg kell adnia egy kimeneti adatkészlet egy tárolt eljárás tevékenység, még akkor is, ha a tárolt eljárás nem hozhatók létre adatokat. Amely, mert a kimeneti adatkészletet, amelyek a tevékenység (milyen gyakran a tevékenység fut - óránként, naponta, stb.) az ütemezés meghajtók van. A kimeneti adatkészlet kell használnia egy **társított szolgáltatás** , amely egy Azure SQL Database vagy az Azure SQL Data Warehouse vagy a tárolt eljárás futtatásához használni kívánt SQL Server-adatbázis hivatkozik. A kimeneti adatkészlet felelt meg a tárolt eljárás eredményét a későbbi feldolgozásra, amelyet egy másik tevékenység módon működhetnek ([tevékenységek láncolás](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) folyamatban. Azonban adat-előállító nem automatikusan kiírhatja a kimenetet tárolt eljárás ehhez a DataSet adatkészlethez. A tárolt eljárás, amely egy SQL táblázat, amely a kimeneti adatkészlet mutat ír. Bizonyos esetekben a kimeneti adatkészlet lehet egy **dummy dataset** (a DataSet adatkészlet mutat, a tárolt eljárás kimeneti valóban nem rendelkező tábla). Az üres adatkészlet csak ütemezés megadása a tárolt eljárási tevékenység fut szolgál. 

1. Ha nem látja ezt a gombot, kattintson a három pontot  **További** kattintson az eszköztár **új adatkészlet**, és kattintson a **Azure SQL**. **Új adatkészlet** a parancssávon, és válassza ki a **Azure SQL**.

    ![fanézet, a társított szolgáltatás](media/data-factory-stored-proc-activity/new-dataset.png)
2. Másolja és illessze be a következő JSON parancsfájl a JSON-szerkesztőbe.

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
3. A dataset telepítéséhez kattintson **telepítés** a parancssávon. Győződjön meg arról, hogy a fanézetben a dataset látni.

    ![Fanézet, a társított szolgáltatások](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Hozzon létre egy folyamatot SqlServerStoredProcedure tevékenység
Most hozzon létre egy folyamatot egy tárolt eljárás tevékenységet. 

Figyelje meg a következő tulajdonságokkal: 

- A **típus** tulajdonsága **SqlServerStoredProcedure**. 
- A **storedProcedureName** típus tulajdonságainak értéke **sp_sample** (a tárolt eljárás neve).
- A **storedProcedureParameters** szakasz nevű egy paraméter **DateTime**. Név és a kis-és a JSON-paraméter meg kell egyeznie a nevét, és a kis-és a tárolt eljárás definícióban paraméter. Ha az egyik paraméter null értékű kell átadni, használja a szintaxist: `"param1": null` (kisbetűket).
 
1. Ha nem látja ezt a gombot, kattintson a három pontot  **További** a parancssávon, majd kattintson a **új adatcsatorna**.
2. Másolja és illessze be a következő JSON kódrészletet:   

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "sp_sample",
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
3. Kattintson újra az adatcsatornát, **telepítés** az eszköztáron.  

### <a name="monitor-the-pipeline"></a>A folyamat figyelése
1. A Data Factory Editor paneljeinek a bezárásához és a Data Factory panelre való visszatéréshez kattintson az **X**, majd a **Diagram** elemre.

    ![Diagram csempe](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. A **diagramnézet** áttekintést nyújt az oktatóanyagban használt folyamatokról és adatkészletekről.

    ![Diagram csempe](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. A Diagram nézet megnyitásához kattintson duplán a dataset `sprocsampleout`. Megjelenik a szeletek üzemkész állapotban. Lehetnek öt szeletek mert szelet állítanak elő minden órában a kezdési és befejezési időpontja a JSON formátumból között.

    ![Diagram csempe](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. A szelet esetén a **készen** állapot, futtassa a `select * from sampletable` győződjön meg arról, hogy az adatok lett szúrja be a tábla a következő tárolt eljárást az Azure SQL-adatbázis lekérdezése.

   ![Kimeneti adatok](./media/data-factory-stored-proc-activity/output.png)

   Lásd: [a folyamat figyelése](data-factory-monitor-manage-pipelines.md) Azure Data Factory folyamatok figyelésével kapcsolatos részletes információk.  


## <a name="specify-an-input-dataset"></a>Adjon meg egy bemeneti adatkészlet
A forgatókönyv tárolt eljárási tevékenység nem rendelkezik a bemeneti adatkészletek. Ha megad egy bemeneti adatkészlet, a tárolt eljárási tevékenység befejezéséig nem fut le a szelet bemeneti adatkészlet érhető el (üzemkész állapotban). Az adatkészlet egy külső adatkészletet (nem által létrehozott azonos egy másik tevékenysége) vagy egy belső adatkészlet (a tevékenység, mielőtt ezt a tevékenységet futtató) felsőbb szintű tevékenység által létrehozott lehet. A tárolt eljárás tevékenység több bemeneti adatkészletet is megadhat. Ha így tesz, a tárolt eljárás tevékenység fut, csak akkor, ha a bemeneti adatkészlet szeleteket érhetők el (üzemkész állapotban). A bemeneti adatkészletet a tárolt eljárás nem használható paraméterként. Csak a tárolt eljárási tevékenység megkezdése előtt ellenőrizze, a függőség szolgál.

## <a name="chaining-with-other-activities"></a>Más tevékenységek láncolás
Ha azt szeretné, a tanúsítványlánc egy felsőbb szintű tevékenység ehhez a tevékenységhez, adja meg a felsőbb szintű tevékenység a tevékenység bemeneti adatokként. Ha így tesz, a tárolt eljárási tevékenység befejezéséig nem fut a felsőbb szintű tevékenység befejezése és a felsőbb szintű tevékenység kimeneti adatkészlet érhető el (a kész állapot). Megadhatja a tárolt eljárás tevékenység bemeneti adatkészletek több felsőbb szintű tevékenység kimeneti adatkészletek. Ha így tesz, a tárolt eljárás tevékenység fut, csak akkor, ha a bemeneti adatkészlet szeleteket érhetők el.  

A következő példában a másolási tevékenység eredménye: OutputDataset, amely a tárolt eljárás tevékenység bemenete. Ezért a tárolt eljárási tevékenység befejezéséig nem fut le a másolási tevékenység befejezése és a OutputDataset szelet érhető el (üzemkész állapotban). Több bemeneti adatkészletek ad meg, ha a tárolt eljárási tevékenység nem működik, amíg a bemeneti adatkészlet szeleteket érhetők el (üzemkész állapotban). A bemeneti adatkészletek közvetlenül a tárolt eljárás tevékenység paraméterei nem használható. 

A láncolás tevékenységek további információkért lásd: [több tevékenységet egy folyamaton belül](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

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

Hasonlóképpen a tárolási eljárás tevékenység csatolásához **alárendelt tevékenységek** (a futtató tevékenységek a tárolt eljárási tevékenység befejezése után), a folyamat az alárendelt tevékenység bemeneti adatokként adja meg a tárolt eljárás tevékenység kimeneti adatkészlet.

> [!IMPORTANT]
> Ha az adatok másolása az Azure SQL Database vagy az SQL Server, konfigurálhatja a **SqlSink** a másolási tevékenység tárolt eljárás használatával meghívni a **sqlWriterStoredProcedureName** tulajdonság. További információkért lásd: [meghívása tárolt eljárás a másolási tevékenység](data-factory-invoke-stored-procedure-from-copy-activity.md). A tulajdonság, lásd: a következő összekötő-cikkek: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
>  
> Ha az adatok másolása az Azure SQL Database vagy az SQL Server vagy az Azure SQL Data Warehouse, konfigurálhatja a **SqlSource** a másolási tevékenység meghívni egy tárolt eljárás a forrás-adatbázis használatával adatokat olvasni az **sqlReaderStoredProcedureName** tulajdonság. További információkért tekintse meg a következő összekötő-cikkek: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)          

## <a name="json-format"></a>JSON formátumban
A tárolt eljárási tevékenység meghatározásához a JSON formátum a következő:

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

A következő táblázat ismerteti ezeket a JSON-tulajdonságokat:

| Tulajdonság | Leírás | Szükséges |
| --- | --- | --- |
| név | A tevékenység neve. |Igen |
| leírás |Mire használható a tevékenységet leíró szöveg |Nem |
| type | Értékre kell állítani: **SqlServerStoredProcedure** | Igen |
| Bemenetek | Választható. Ha megad egy bemeneti adatkészlet, elérhetőnek kell lennie (a "Kész" állapotú) a tárolt eljárás tevékenység futtatásához. A bemeneti adatkészletet a tárolt eljárás nem használható paraméterként. Csak a tárolt eljárási tevékenység megkezdése előtt ellenőrizze, a függőség szolgál. |Nem |
| kimenetek | Meg kell adnia egy tárolt eljárás tevékenység egy kimeneti adatkészletet. Kimeneti adatkészlet határozza meg a **ütemezés** a tárolt eljárás tevékenység (óránként, heti, havi, stb.). <br/><br/>A kimeneti adatkészlet kell használnia egy **társított szolgáltatás** , amely egy Azure SQL Database vagy az Azure SQL Data Warehouse vagy a tárolt eljárás futtatásához használni kívánt SQL Server-adatbázis hivatkozik. <br/><br/>A kimeneti adatkészlet felelt meg a tárolt eljárás eredményét a későbbi feldolgozásra, amelyet egy másik tevékenység módon működhetnek ([tevékenységek láncolás](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) folyamatban. Azonban adat-előállító nem automatikusan kiírhatja a kimenetet tárolt eljárás ehhez a DataSet adatkészlethez. A tárolt eljárás, amely egy SQL táblázat, amely a kimeneti adatkészlet mutat ír. <br/><br/>Bizonyos esetekben a kimeneti adatkészlet lehet egy **dummy dataset**, amellyel csak a tárolt eljárási tevékenység fut ütemezése. |Igen |
| storedProcedureName |Adja meg a tárolt eljárás neve az Azure SQL database vagy az Azure SQL Data Warehouse vagy SQL Server adatbázis, amely a társított szolgáltatás, amely a bemeneti tábla használja. |Igen |
| storedProcedureParameters |Adja meg a tárolt eljárás paraméter értékét. Ha az egyik paraméter null értéket átadni van szüksége, használja a szintaxist: "param1": (összes kisbetű) NULL értékű. Tekintse meg az alábbi minta tájékozódhat az e tulajdonság használatával. |Nem |

## <a name="passing-a-static-value"></a>Egy statikus értékre továbbításához
Most tegyük vegyen fel egy másik oszlop, "A forgatókönyv" nevű "Dokumentum minta" nevű statikus értéket tartalmazó tábla.

![Mintaadatokat 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

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

**Tárolt eljárást:**

```SQL
CREATE PROCEDURE sp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Most, továbbítja a **forgatókönyv** paraméter és a tárolt eljárási tevékenység közötti értéket. A **typeProperties** szakasz az előző példa néz ki a következő kódrészletet:

```JSON
"typeProperties":
{
    "storedProcedureName": "sp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Data Factory adatkészlet:**

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

**Data Factory-folyamat**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample2",
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
