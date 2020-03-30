---
title: SQL Server tárolt eljárási tevékenysége
description: Megtudhatja, hogyan használhatja az SQL Server tárolt eljárástevékenység egy tárolt eljárást egy Azure SQL-adatbázisban vagy az Azure SQL Data Warehouse egy Data Factory-folyamat.
services: data-factory
documentationcenter: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: 45aa49de51f42b26c653b15e79c865e3f5647c39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74931627"
---
# <a name="sql-server-stored-procedure-activity"></a>SQL Server tárolt eljárási tevékenysége
> [!div class="op_single_selector" title1="Átalakítási tevékenységek"]
> * [Hive-tevékenység](data-factory-hive-activity.md)
> * [Sertés tevékenység](data-factory-pig-activity.md)
> * [MapReduce tevékenység](data-factory-map-reduce.md)
> * [Hadoop streaming tevékenység](data-factory-hadoop-streaming-activity.md)
> * [Szikratevékenység](data-factory-spark.md)
> * [Machine Learning kötegelt végrehajtási tevékenység](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning Update-erőforrástevékenység](data-factory-azure-ml-update-resource-activity.md)
> * [Tárolt eljárási tevékenység](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-tevékenység](data-factory-usql-activity.md)
> * [.NET egyéni tevékenység](data-factory-use-custom-activities.md)

> [!NOTE]
> Ez a cikk az Azure Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, olvassa el az [adatok átalakítása tárolt eljárástevékenység használatával a Data Factory alkalmazásban című témakört.](../transform-data-using-stored-procedure.md)

## <a name="overview"></a>Áttekintés
A Data [Factory-folyamat](data-factory-create-pipelines.md) adatátalakítási tevékenységeivel átalakíthatja és feldolgozhatja a nyers adatokat előrejelzésekké és elemzési adatokká. A tárolt eljárás tevékenység a Data Factory által támogatott átalakítási tevékenységek egyike. Ez a cikk az [adatátalakítási tevékenységek](data-factory-data-transformation-activities.md) cikkre épül, amely általános áttekintést nyújt az adatok átalakításáról és a Data Factory támogatott átalakítási tevékenységeiről.

A tárolt eljárási tevékenység segítségével meghívhat egy tárolt eljárást a vállalat alábbi adattasztatúráinak egyikében vagy egy Azure virtuális gépen(VM):

- Azure SQL Database
- Azure SQL Data Warehouse
- SQL Server adatbázis. SQL Server használata esetén telepítse az Adatkezelési átjárót ugyanarra a számítógépre, amelyen az adatbázis található, vagy egy külön számítógépre, amely hozzáfér az adatbázishoz. Az Adatkezelési átjáró egy olyan összetevő, amely biztonságos és felügyelt módon kapcsolja össze a helyszíni/Azure-beli virtuális gépek adatforrásait a felhőszolgáltatásokkal. A részleteket az [Adatkezelési átjáró](data-factory-data-management-gateway.md) ról szóló cikkben találja.

> [!IMPORTANT]
> Amikor adatokat másol az Azure SQL Database vagy az SQL Server, beállíthatja az **SqlSink** másolási tevékenység meghívására tárolt eljárás használatával **az sqlWriterStoredProcedureName** tulajdonság. További információ: [A tárolt eljárás meghívása másolási tevékenységből](data-factory-invoke-stored-procedure-from-copy-activity.md)című témakörben talál. A tulajdonságról a következő összekötőcikkekben talál: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), SQL [Server](data-factory-sqlserver-connector.md#copy-activity-properties). Nem támogatott a tárolt eljárás meghívása az Azure SQL Data Warehouse-ba másolása közben egy másolási tevékenység használatával. De használhatja a tárolt eljárás tevékenység meghívására egy tárolt eljárást egy SQL Data Warehouse.
>
> Amikor adatokat másol az Azure SQL Database vagy az SQL Server vagy az Azure SQL Data Warehouse, beállíthatja **sqlSource** másolási tevékenység meghívására tárolt eljárást adatok at a forrás adatbázisból az **sqlReaderStoredProcedureName** tulajdonság használatával. További információt a következő összekötő cikkekben talál: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), SQL [Server](data-factory-sqlserver-connector.md#copy-activity-properties), Azure SQL [Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

A következő forgatókönyv a tárolt eljárási tevékenység egy folyamatban egy azure SQL-adatbázisban tárolt eljárás meghívásához.

## <a name="walkthrough"></a>Útmutatás
### <a name="sample-table-and-stored-procedure"></a>Mintatábla és tárolt eljárás
1. Hozza létre az alábbi **táblázatot** az Azure SQL Database-ben az SQL Server Management Studio vagy bármely más, önnek kényelmes eszközzel. A datetimestamp oszlop a megfelelő azonosító létrehozásának dátuma és időpontja.

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
    Id az egyedi azonosított és a datetimestamp oszlop a dátum és az idő, amikor a megfelelő azonosító jön létre.
    
    ![Mintaadatok](./media/data-factory-stored-proc-activity/sample-data.png)

    Ebben a példában a tárolt eljárás egy Azure SQL-adatbázisban található. Ha a tárolt eljárás egy Azure SQL Data Warehouse és az SQL Server Database, a megközelítés hasonló. SQL Server adatbázis esetén [adatkezelési átjárót](data-factory-data-management-gateway.md)kell telepíteni.
2. Hozza létre a következő **tárolt eljárást,** amely adatokat szúr be a **mintatáblába.**

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > A paraméter **nevének** és **be- és besorolásának** (ebben a példában a DateTime) meg kell egyeznie a jsonban megadott paraméterrel. A tárolt eljárás definíciójában **\@** győződjön meg arról, hogy a paraméter előtagjaként van használva.

### <a name="create-a-data-factory"></a>Data factory létrehozása
1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Kattintson a bal oldali menü **ÚJ parancsára,** kattintson az **Intelligencia + Elemzés**parancsra, majd a Data **Factory**parancsra.

    ![Új adat-előállító](media/data-factory-stored-proc-activity/new-data-factory.png)
3. Az **Új adatgyár** panelen adja meg az **SProcDF** parancsot a névhez. Az Azure Data Factory-nevek **globálisan egyediek.** A gyár sikeres létrehozásához előtaggal kell előtagoznia az adatgyár nevét.

   ![Új adat-előállító](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Válassza ki az **Azure-előfizetését**.
5. Az **Erőforráscsoportban** hajtsa végre a következő lépések egyikét:
   1. Kattintson **az Új létrehozása gombra,** és adja meg az erőforráscsoport nevét.
   2. Kattintson **a Meglévő használata** elemre, és jelöljön ki egy meglévő erőforráscsoportot.
6. Válassza ki a Data Factory **helyét**.
7. Válassza **a Rögzítés az irányítópultra** lehetőséget, hogy a következő bejelentkezéskor láthassa az adatgyárat az irányítópulton.
8. Kattintson a **Create** (Létrehozás) elemre a **New data factory** (Új data factory) panelen.
9. Láthatja, hogy az adatgyár az Azure Portal **irányítópultján** jön létre. A data factory sikeres létrehozása után megjelenik a data factory oldal, amely megjeleníti a data factory tartalmát.

   ![A Data Factory kezdőlapja](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Azure SQL-alapú szolgáltatás létrehozása
Az adat-előállító létrehozása után hozzon létre egy Azure SQL-kapcsolt szolgáltatás, amely összeköti az Azure SQL-adatbázis, amely tartalmazza a mintatábla tábla és usp_sample tárolt eljárás, az adat-előállító.

1. Kattintson a Szerző elemre, **és telepítse** a **Data Factory** panel **sprocDF** elindításához a Data Factory Editor.
2. Kattintson az **Új adattár elemre** a parancssávon, és válassza az **Azure SQL Database**lehetőséget. Meg kell jelennie a JSON-parancsfájl egy Azure SQL-kapcsolt szolgáltatás létrehozásához a szerkesztőben.

   ![Új adattár](media/data-factory-stored-proc-activity/new-data-store.png)
3. A JSON-parancsfájlban hajtsa végre a következő módosításokat:

   1. Cserélje `<servername>` le az Azure SQL Database-kiszolgáló nevét.
   2. Cserélje `<databasename>` le arra az adatbázisra, amelyben létrehozta a táblát és a tárolt eljárást.
   3. Cserélje `<username@servername>` le az adatbázishoz hozzáféréssel rendelkező felhasználói fiókkal.
   4. Cserélje `<password>` le a felhasználói fiók jelszavával.

      ![Új adattár](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. A csatolt szolgáltatás központi telepítéséhez kattintson a **központi telepítés gombra** a parancssávon. Győződjön meg arról, hogy az AzureSqlLinkedService a bal oldali fanézetben.

    ![fanézet csatolt szolgáltatással](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Kimeneti adatkészlet létrehozása
Meg kell adnia egy kimeneti adatkészletet a tárolt eljárástevékenységhez, még akkor is, ha a tárolt eljárás nem hoz létre adatokat. Ez azért van, mert a kimeneti adatkészlet, amely hajtja a tevékenység ütemezését (milyen gyakran fut a tevékenység - óránkénti, napi stb.). A kimeneti adatkészlet kell használnia egy **összekapcsolt szolgáltatás,** amely egy Azure SQL-adatbázis vagy egy Azure SQL Data Warehouse vagy egy SQL Server-adatbázis, amelyben szeretné, hogy a tárolt eljárás futtatásához. A kimeneti adatkészlet szolgálhat egy módja annak, hogy adja át a tárolt eljárás eredményét későbbi feldolgozásra egy másik tevékenység[(láncolási tevékenységek](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) a folyamatban. A Data Factory azonban nem írja automatikusan a tárolt eljárás kimenetét ebbe az adatkészletbe. Ez az a tárolt eljárás, amely egy SQL-táblába ír, amelyre a kimeneti adatkészlet mutat. Bizonyos esetekben a kimeneti adatkészlet lehet egy **dummy adatkészlet** (olyan adatkészlet, amely egy olyan táblára mutat, amely nem igazán tartalmazza a tárolt eljárás kimenetét). Ez a dummy adatkészlet csak a tárolt eljárási tevékenység futtatásának ütemezésének megadására szolgál.

1. Kattintson **a ... További információk** az eszköztáron kattintson az **Új adatkészlet gombra**, majd az **Azure SQL**parancsra. **Új adatkészlet** a parancssávon, és válassza az **Azure SQL**lehetőséget.

    ![fanézet csatolt szolgáltatással](media/data-factory-stored-proc-activity/new-dataset.png)
2. Másolja/illessze be a következő JSON-parancsfájlt a JSON-szerkesztőbe.

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
3. Az adatkészlet központi telepítéséhez kattintson a **Központi telepítés gombra** a parancssávon. Ellenőrizze, hogy az adatkészlet megjelenik-e a fanézetben.

    ![fanézet csatolt szolgáltatásokkal](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Folyamat létrehozása SqlServerStoredProcedure tevékenységgel
Most hozzon létre egy folyamatot egy tárolt eljárási tevékenységgel.

Figyelje meg a következő tulajdonságokat:

- A **típustulajdonság** **sqlServerStoredProcedure**.
- A type properties **nevű storedProcedureName** érték **usp_sample** (a tárolt eljárás neve).
- A **storedProcedureParameters** szakasz egy **DateTime**nevű paramétert tartalmaz. A JSON paraméter ének nevének és burkolatának meg kell egyeznie a paraméter nevével és burkolatával a tárolt eljárás definíciójában. Ha null paraméterre van szüksége, használja `"param1": null` a szintaxist: (minden kisbetű).

1. Kattintson **a ... További információk** a parancssávon, és kattintson **az Új folyamat gombra.**
2. Másolja/illessze be a következő JSON-kódrészletet:

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
3. A folyamat üzembe helyezéséhez kattintson az eszköztár **Telepítése** gombjára.

### <a name="monitor-the-pipeline"></a>A folyamat figyelése
1. A Data Factory Editor paneljeinek a bezárásához és a Data Factory panelre való visszatéréshez kattintson az **X**, majd a **Diagram** elemre.

    ![diagram mozaikja](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. A **Diagram nézetben**az oktatóanyagban használt folyamatok és adatkészletek áttekintése látható.

    ![diagram mozaikja](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. Diagram nézetben kattintson duplán `sprocsampleout`az adatkészletre. A szeletek Kész állapotban jelennek meg. Öt szeletnek kell lennie, mert a JSON kezdő és záró időpontja között minden órához egy szelet keletkezik.

    ![diagram mozaikja](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. Ha egy szelet **Ready** állapotban `select * from sampletable` van, futtasson egy lekérdezést az Azure SQL-adatbázisban, és ellenőrizze, hogy az adatokat a tárolt eljárás szúrta-e be a táblába.

   ![Kimeneti adatok](./media/data-factory-stored-proc-activity/output.png)

   Az Azure Data Factory-folyamatok figyeléséről a [folyamat figyelése](data-factory-monitor-manage-pipelines.md) című témakörben talál részletes információt.

## <a name="specify-an-input-dataset"></a>Bemeneti adatkészlet megadása
A forgatókönyvben a tárolt eljárástevékenység nem rendelkezik bemeneti adatkészletek. Ha megad egy bemeneti adatkészletet, a tárolt eljárástevékenység nem fut, amíg a bemeneti adatkészlet szelete elérhető (Kész állapotban). Az adatkészlet lehet egy külső adatkészlet (amelyet nem egy másik tevékenység állít elő ugyanabban a folyamatban) vagy egy belső adatkészlet, amelyet egy upstream tevékenység (a tevékenység előtt futó tevékenység) hoz létre. A tárolt eljárási tevékenységhez több bemeneti adatkészletet is megadhat. Ha így tesz, a tárolt eljárástevékenység csak akkor fut, ha az összes bemeneti adatkészletszelet elérhető (Kész állapotban). A bemeneti adatkészlet nem használható fel a tárolt eljárásban paraméterként. Csak a függőség ellenőrzésére szolgál a tárolt eljárási tevékenység megkezdése előtt.

## <a name="chaining-with-other-activities"></a>Láncolás más tevékenységekkel
Ha ezzel a tevékenységgel egy upstream tevékenységet szeretne láncolni, adja meg a felfelé irányuló tevékenység kimenetét ennek a tevékenységnek a bemeneteként. Ha így tesz, a tárolt eljárástevékenység nem fut, amíg a felső-folyási tevékenység be nem fejeződik, és a felső-folyási tevékenység kimeneti adatkészlete elérhetővé nem válik (Kész állapot). Több upstream tevékenység kimeneti adatkészleteit is megadhatja a tárolt eljárástevékenység bemeneti adatkészleteként. Ha így tesz, a tárolt eljárástevékenység csak akkor fut, ha az összes bemeneti adatkészlet-szelet elérhető.

A következő példában a másolási tevékenység kimenete: OutputDataset, amely a tárolt eljárás tevékenység bemenete. Ezért a tárolt eljárás tevékenység nem fut, amíg a másolási tevékenység befejeződik, és a OutputDataset szelet érhető el (Kész állapotban). Ha több bemeneti adatkészletet ad meg, a tárolt eljárástevékenység nem fut, amíg az összes bemeneti adatkészletszelet elérhető (Kész állapotban). A bemeneti adatkészletek nem használhatók közvetlenül a tárolt eljárástevékenység paramétereként.

A tevékenységek láncolásával kapcsolatos további információkért tekintse meg [a folyamatban lévő több tevékenységet](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

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

Hasonlóképpen, ha az tárolási eljárás tevékenységét a **későbbi tevékenységekkel** (a tárolt eljárási tevékenység befejezése után futó tevékenységekkel) szeretné összekapcsolni, adja meg a tárolt eljárástevékenység kimeneti adatkészletét a folyamat alsóbb rétegbeli tevékenységének bemeneteként.

> [!IMPORTANT]
> Amikor adatokat másol az Azure SQL Database vagy az SQL Server, beállíthatja az **SqlSink** másolási tevékenység meghívására tárolt eljárás használatával **az sqlWriterStoredProcedureName** tulajdonság. További információ: [A tárolt eljárás meghívása másolási tevékenységből](data-factory-invoke-stored-procedure-from-copy-activity.md)című témakörben talál. A tulajdonsággal kapcsolatos részletekért tekintse meg a következő összekötő cikkeket: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), SQL [Server](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> Amikor adatokat másol az Azure SQL Database vagy az SQL Server vagy az Azure SQL Data Warehouse, beállíthatja **sqlSource** másolási tevékenység meghívására tárolt eljárást adatok at a forrás adatbázisból az **sqlReaderStoredProcedureName** tulajdonság használatával. További információt a következő összekötő cikkekben talál: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), SQL [Server](data-factory-sqlserver-connector.md#copy-activity-properties), Azure SQL [Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>JSON formátum
Itt van a JSON formátum a tárolt eljárási tevékenység meghatározásához:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs": [ { "name": "inputtable" } ],
    "outputs": [ { "name": "outputtable" } ],
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

Az alábbi táblázat a következő JSON-tulajdonságokat ismerteti:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| név | A tevékenység neve |Igen |
| leírás |A tevékenységet leíró szöveg |Nem |
| type | A következőt kell tenni: **SqlServerStoredProcedure** | Igen |
| Bemenetek | Választható. Ha megad egy bemeneti adatkészletet, annak elérhetőnek kell lennie ("Kész" állapotban) a tárolt eljárási tevékenység futtatásához. A bemeneti adatkészlet nem használható fel a tárolt eljárásban paraméterként. Csak a függőség ellenőrzésére szolgál a tárolt eljárási tevékenység megkezdése előtt. |Nem |
| Kimenetek | Meg kell adnia egy kimeneti adatkészletet egy tárolt eljárástevékenységhez. A kimeneti adatkészlet a tárolt eljárási tevékenység **ütemezését** határozza meg (óránkénti, heti, havi stb.). <br/><br/>A kimeneti adatkészlet kell használnia egy **összekapcsolt szolgáltatás,** amely egy Azure SQL-adatbázis vagy egy Azure SQL Data Warehouse vagy egy SQL Server-adatbázis, amelyben szeretné, hogy a tárolt eljárás futtatásához. <br/><br/>A kimeneti adatkészlet szolgálhat egy módja annak, hogy adja át a tárolt eljárás eredményét későbbi feldolgozásra egy másik tevékenység[(láncolási tevékenységek](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) a folyamatban. A Data Factory azonban nem írja automatikusan a tárolt eljárás kimenetét ebbe az adatkészletbe. Ez az a tárolt eljárás, amely egy SQL-táblába ír, amelyre a kimeneti adatkészlet mutat. <br/><br/>Bizonyos esetekben a kimeneti adatkészlet lehet egy **dummy adatkészlet,** amely csak a tárolt eljárási tevékenység futtatásának ütemezését adja meg. |Igen |
| storedProcedureName |Adja meg a tárolt eljárás nevét az Azure SQL-adatbázisban vagy az Azure SQL Data Warehouse vagy az SQL Server adatbázisban, amelyet a kimeneti tábla által használt csatolt szolgáltatás képvisel. |Igen |
| storedProcedureParameters |Adja meg a tárolt eljárásparamétereinek értékeit. Ha null értéket kell átadnia egy paraméterhez, használja a következő szintaxist: "param1": null (minden kisbetű). A tulajdonság használatáról az alábbi mintában olvashat. |Nem |

## <a name="passing-a-static-value"></a>Statikus érték átadása
Most vegyük fontolóra egy "Forgatókönyv" nevű oszlop hozzáadását a "Dokumentumminta" nevű statikus értéket tartalmazó táblában.

![2. mintaadat](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Táblázat:**

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

Most adja át a **forgatókönyv** paramétert és a tárolt eljárástevékenység értékét. Az előző minta **TypeProperties** szakasza a következő kódrészletre néz:

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

**Adatgyári folyamat**

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
