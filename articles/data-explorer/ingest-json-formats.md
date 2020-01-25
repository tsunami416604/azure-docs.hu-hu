---
title: JSON formátumú adat betöltése az Azure-ba Adatkezelő
description: Ismerje meg, hogyan lehet JSON formátumú adatot betölteni az Azure Adatkezelőba.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: ef5c7de782d833aad96516d3e5357a0ed575a781
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722875"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>JSON formátumú mintaadatok betöltése az Azure-ba Adatkezelő

Ez a cikk bemutatja, hogyan végezheti el a JSON formátumú adatbevitelt egy Azure Adatkezelő-adatbázisba. A RAW és a leképezett JSON egyszerű példáit fogja használni, folytassa a többsoros JSON-t, majd kezelje a tömböket és szótárakat tartalmazó összetettebb JSON-sémákat. 

## <a name="prerequisites"></a>Előfeltételek

[Egy tesztfürt és egy adatbázis](create-cluster-database-portal.md)

## <a name="the-json-format"></a>A JSON formátuma

Az Azure Adatkezelő két JSON fájlformátumot támogat:
* `json`: sorok elválasztva JSON. A bemeneti adatok minden sora pontosan egy JSON-rekorddal rendelkezik.
* `multijson`: többsoros JSON. Az elemző figyelmen kívül hagyja a sorok elválasztóit, és beolvas egy rekordot az előző pozícióból egy érvényes JSON végére.

### <a name="ingest-and-map-json-formatted-data"></a>JSON formátumú adat betöltése és leképezése

A JSON formátumú adat betöltéséhez meg kell adnia a *formátumot* a betöltési [tulajdonság](/azure/kusto/management/data-ingestion/index#ingestion-properties)használatával. A JSON-adat betöltéséhez [leképezés](/azure/kusto/management/mappings)szükséges, amely egy JSON-forrás bejegyzést képez a célként megadott oszlopra. Az adatfeldolgozás során használja az előre definiált `jsonMappingReference` betöltési tulajdonságot, vagy a `jsonMapping`betöltés tulajdonságot. Ez a cikk a betöltéshez használt táblázaton előre definiált `jsonMappingReference` betöltési tulajdonságot fogja használni. Az alábbi példákban először a JSON-rekordok nyers adatként való betöltését fogjuk egy egyoszlopos táblába. Ezután a leképezés használatával betöltjük az egyes tulajdonságokat a leképezett oszlopba. 

### <a name="simple-json-example"></a>Egyszerű JSON-példa

Az alábbi példa egy egyszerű JSON, amely lapos struktúrával rendelkezik. Az adatok hőmérséklet-és páratartalom-információval rendelkeznek, amelyet számos eszköz gyűjt. Minden rekord AZONOSÍTÓval és időbélyeggel van megjelölve.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>Nyers JSON-rekordok beolvasása 

Ebben a példában a JSON-rekordokat nyers adatként kell bevenni egy egyoszlopos táblába. Az adatfeldolgozás, a lekérdezések használata és a frissítési szabályzat az adatgyűjtés után történik.

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

A Kusto lekérdezési nyelv használatával nyers JSON formátumú adatbevitelt végezhet.

1. Jelentkezzen be itt: [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Válassza a **Fürt hozzáadása** lehetőséget.

1. A **fürt hozzáadása** párbeszédpanelen adja meg a fürt URL-címét az űrlapon `https://<ClusterName>.<Region>.kusto.windows.net/`, majd válassza a **Hozzáadás**lehetőséget.

1. Illessze be a következő parancsot, és válassza a **Futtatás** elemet a tábla létrehozásához.

    ```Kusto
    .create table RawEvents (Event: dynamic)
    ```

    Ez a lekérdezés egy olyan táblát hoz létre, amely egy [dinamikus](/azure/kusto/query/scalar-data-types/dynamic) adattípus egyetlen `Event` oszlopát tartalmazza.

1. Hozza létre a JSON-leképezést.

    ```Kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    Ez a parancs létrehoz egy leképezést, és leképezi a JSON-gyökér elérési útját `$` a `Event` oszlopra.

1. Adatbevitel a `RawEvents` táblába.

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

    > [!NOTE]
    > Itt láthatja a motor végpontján közvetlenül végrehajtott `ingest`-vezérlési parancsokat. Éles környezetben a rendszer a betöltést az adatkezelés szolgáltatáshoz az ügyfél-kódtárak vagy az adatkapcsolatok használatával hajtja végre. Az [azure adatkezelő Python Library használatával](/azure/data-explorer/python-ingest-data) beolvashatja az adatgyűjtést, és [Az Azure adatkezelő .NET Standard SDK használatával](/azure/data-explorer/net-standard-ingest-data) betöltheti az adatgyűjtést az ezekkel az ügyféloldali kódtárakkal történő adatfeldolgozással kapcsolatban.

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

Nyers C# JSON formátumú adatbevitelre használható.

1. Hozza létre a `RawEvents` táblát.

    ```C#
    var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
    var kustoConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder);

    var table = "RawEvents";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Events", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Hozza létre a JSON-leképezést.
    
    ```C#
    var tableMapping = "RawEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                new JsonColumnMapping {ColumnName = "Events", JsonPath = "$"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```
    Ez a parancs létrehoz egy leképezést, és leképezi a JSON-gyökér elérési útját `$` a `Event` oszlopra.

1. Adatbevitel a `RawEvents` táblába.

    ```C#
    var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var ingestConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder);
    
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

> [!NOTE]
> Az adatokat a [kötegelt házirend](/azure/kusto/concepts/batchingpolicy)alapján összesíti a rendszer, ami néhány perc késéssel jár.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python használatával nyers JSON formátumú adatbevitelt használhat.

1. Hozza létre a `RawEvents` táblát.

    ```Python
    KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)
    KUSTO_CLIENT = KustoClient(KCSB_DATA)
    TABLE = "RawEvents"

    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Events: dynamic)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Hozza létre a JSON-leképezést.

    ```Python
    MAPPING = "RawEventMapping"
    CREATE_MAPPING_COMMAND = ".create table " + TABLE + " ingestion json mapping '" + MAPPING + """' '[{"column":"Event","path":"$"}]'"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Adatbevitel a `RawEvents` táblába.

    ```Python
    INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(INGEST_URI, AAD_TENANT_ID)
    INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    > [!NOTE]
    > Az adatokat a [kötegelt házirend](/azure/kusto/concepts/batchingpolicy)alapján összesíti a rendszer, ami néhány perc késéssel jár.

---

## <a name="ingest-mapped-json-records"></a>Leképezett JSON-rekordok betöltése

Ebben a példában a JSON-rekordok adatait tölti be. Az egyes JSON-tulajdonságok a tábla egyetlen oszlopára vannak leképezve. 

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Hozzon létre egy új táblázatot, amely hasonló sémával rendelkezik a JSON bemeneti adatokhoz. Ezt a táblázatot a következő példákhoz fogjuk használni, és betöltjük a parancsokat. 

    ```Kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. Hozza létre a JSON-leképezést.

    ```Kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    Ebben a leképezésben a tábla sémája által meghatározottak szerint a `timestamp` bejegyzések az oszlopba lesznek betöltve, `Time` `datetime` adattípusként.

1. Adatbevitel a `Events` táblába.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    Az "Simple. JSON" fájl néhány vesszővel elválasztott JSON-rekordot tartalmaz. A formátum `json`, és a betöltési parancsban használt leképezés a létrehozott `FlatEventMapping`.

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Hozzon létre egy új táblázatot, amely hasonló sémával rendelkezik a JSON bemeneti adatokhoz. Ezt a táblázatot a következő példákhoz fogjuk használni, és betöltjük a parancsokat. 

    ```C#
    var table = "Events";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Time", "System.DateTime"),
                Tuple.Create("Device", "System.String"),
                Tuple.Create("MessageId", "System.String"),
                Tuple.Create("Temperature", "System.Double"),
                Tuple.Create("Humidity", "System.Double"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Hozza létre a JSON-leképezést.

    ```C#
    var tableMapping = "FlatEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.timestamp"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.deviceId"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.messageId"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.temperature"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.humidity"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

    Ebben a leképezésben a tábla sémája által meghatározottak szerint a `timestamp` bejegyzések az oszlopba lesznek betöltve, `Time` `datetime` adattípusként.    

1. Adatbevitel a `Events` táblába.

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

    Az "Simple. JSON" fájl néhány vesszővel elválasztott JSON-rekordot tartalmaz. A formátum `json`, és a betöltési parancsban használt leképezés a létrehozott `FlatEventMapping`.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Hozzon létre egy új táblázatot, amely hasonló sémával rendelkezik a JSON bemeneti adatokhoz. Ezt a táblázatot a következő példákhoz fogjuk használni, és betöltjük a parancsokat. 

    ```Python
    TABLE = "RawEvents"
    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Hozza létre a JSON-leképezést.

    ```Python
    MAPPING = "FlatEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Adatbevitel a `Events` táblába.

    ```Python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    Az "Simple. JSON" fájl néhány sorba bontott JSON-rekordot tartalmaz. A formátum `json`, és a betöltési parancsban használt leképezés a létrehozott `FlatEventMapping`.    
---

## <a name="ingest-multi-lined-json-records"></a>Többsoros JSON-rekordok beolvasása

Ebben a példában a többsoros JSON-rekordok betöltését végezheti el. Az egyes JSON-tulajdonságok a tábla egyetlen oszlopára vannak leképezve. A "többsoros. JSON" fájl néhány behúzott JSON-rekordot tartalmaz. A formátum `multijson` azt jelzi, hogy a motor a JSON-struktúra alapján olvassa be a rekordokat.

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

Adatbevitel a `Events` táblába.

```Kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

Adatbevitel a `Events` táblába.

```C#
var tableMapping = "FlatEventMapping";
var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
var properties =
    new KustoQueuedIngestionProperties(database, table)
    {
        Format = DataSourceFormat.multijson,
        IngestionMappingReference = tableMapping
    };

ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Adatbevitel a `Events` táblába.

```Python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>Tömböket tartalmazó JSON-rekordok betöltése

A tömb típusú adattípusok értékek rendezett gyűjteményei. A JSON-tömb betöltését [frissítési szabályzat](/azure/kusto/management/update-policy)végzi. A JSON-t egy közbenső táblába tölti be. A frissítési szabályzat egy előre definiált függvényt futtat a `RawEvents` táblában, és visszatölti az eredményeket a célként megadott táblába. Az alábbi struktúrával rendelkező adatmennyiséget fogjuk bevezetni:

```json
{
    "records": 
    [
        {
            "timestamp": "2019-05-02 15:23:50.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "7f316225-839a-4593-92b5-1812949279b3",
            "temperature": 31.0301639051317,
            "humidity": 62.0791099602725
        },
        {
            "timestamp": "2019-05-02 15:23:51.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "57de2821-7581-40e4-861e-ea3bde102364",
            "temperature": 33.7529423105311,
            "humidity": 75.4787976739364
        }
    ]
}
```

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Hozzon létre egy `update policy` függvényt, amely kibővíti `records` gyűjteményét, hogy a gyűjtemény minden értéke külön sort kapjon a `mv-expand` operátor használatával. A Table `RawEvents`t használjuk forrástáblaként, és `Events` célként.

    ```Kusto
    .create function EventRecordsExpand() {
        RawEvents
        | mv-expand records = Event
        | project
            Time = todatetime(records["timestamp"]),
            Device = tostring(records["deviceId"]),
            MessageId = tostring(records["messageId"]),
            Temperature = todouble(records["temperature"]),
            Humidity = todouble(records["humidity"])
    }
    ```

1. A függvény által fogadott sémának meg kell egyeznie a céltábla sémájával. `getschema` operátor használatával tekintse át a sémát.

    ```Kusto
    EventRecordsExpand() | getschema
    ```

1. Adja hozzá a frissítési szabályzatot a cél táblához. Ez a szabályzat automatikusan futtatja a lekérdezést a `RawEvents` köztes táblában lévő összes újonnan betöltött adattal, és betölti az eredményeket a `Events` táblába. Definiáljon egy nulla adatmegőrzési szabályt a köztes tábla megőrzése érdekében.

    ```Kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. Adatbevitel a `RawEvents` táblába.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. Tekintse át az `Events` táblában található adatelemzést.

    ```Kusto
    Events
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Hozzon létre egy frissítési függvényt, amely kibővíti `records` gyűjteményét, hogy a gyűjtemény minden értéke külön sort kapjon a `mv-expand` operátor használatával. A Table `RawEvents`t használjuk forrástáblaként, és `Events` célként.   

    ```C#
    var command =
        CslCommandGenerator.GenerateCreateFunctionCommand(
            "EventRecordsExpand",
            "UpdateFunctions",
            string.Empty,
            null,
            @"RawEvents
                | mv-expand records = Event
                | project
                    Time = todatetime(records['timestamp']),
                    Device = tostring(records['deviceId']),
                    MessageId = tostring(records['messageId']),
                    Temperature = todouble(records['temperature']),
                    Humidity = todouble(records['humidity'])",
            ifNotExists: false);

    kustoClient.ExecuteControlCommand(command);
    ```

    > [!NOTE]
    > A függvény által fogadott sémának meg kell egyeznie a céltábla sémájával.

1. Adja hozzá a frissítési szabályzatot a cél táblához. Ez a szabályzat automatikusan futtatja a lekérdezést a `RawEvents` köztes táblában lévő összes újonnan betöltött adattal, és betölti az eredményeket a `Events` táblázatba. Definiáljon egy nulla adatmegőrzési szabályt a köztes tábla megőrzése érdekében.

    ```C#
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. Adatbevitel a `RawEvents` táblába.

    ```C#
    var table = "RawEvents";
    var tableMapping = "RawEventMapping";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```
    
1. Tekintse át az `Events` táblában található adatelemzést.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Hozzon létre egy frissítési függvényt, amely kibővíti `records` gyűjteményét, hogy a gyűjtemény minden értéke külön sort kapjon a `mv-expand` operátor használatával. A Table `RawEvents`t használjuk forrástáblaként, és `Events` célként.   

    ```Python
    CREATE_FUNCTION_COMMAND = 
        '''.create function EventRecordsExpand() { 
            RawEvents
            | mv-expand records = Event
            | project
                Time = todatetime(records["timestamp"]),
                Device = tostring(records["deviceId"]),
                MessageId = tostring(records["messageId"]),
                Temperature = todouble(records["temperature"]),
                Humidity = todouble(records["humidity"])
            }'''
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_FUNCTION_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

    > [!NOTE]
    > A függvény által fogadott sémának meg kell egyeznie a céltábla sémájával.

1. Adja hozzá a frissítési szabályzatot a cél táblához. Ez a szabályzat automatikusan futtatja a lekérdezést a `RawEvents` köztes táblában lévő összes újonnan betöltött adattal, és betölti az eredményeket a `Events` táblázatba. Definiáljon egy nulla adatmegőrzési szabályt a köztes tábla megőrzése érdekében.

    ```Python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Adatbevitel a `RawEvents` táblába.

    ```Python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. Tekintse át az `Events` táblában található adatelemzést.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>Szótárakat tartalmazó JSON-rekordok beolvasása

A szótárban strukturált JSON kulcs-érték párokat tartalmaz. A JSON-rekordok betöltési leképezést alkalmaznak a `JsonPath`logikai kifejezés használatával. Az alábbi struktúrával végezheti el az adatgyűjtést:

```json
{
    "event": 
    [
        {
            "Key": "timestamp",
            "Value": "2019-05-02 15:23:50.0000000"
        },
        {
            "Key": "deviceId",
            "Value": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71"
        },
        {
            "Key": "messageId",
            "Value": "7f316225-839a-4593-92b5-1812949279b3"
        },
        {
            "Key": "temperature",
            "Value": 31.0301639051317
        },
        {
            "Key": "humidity",
            "Value": 62.0791099602725
        }
    ]
}
```

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Hozzon létre egy JSON-leképezést.

    ```Kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. Adatbevitel a `Events` táblába.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Hozzon létre egy JSON-leképezést.

    ```C#
    var tableName = "Events";
    var tableMapping = "KeyValueEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.event[?(@.Key == 'timestamp')]"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.event[?(@.Key == 'deviceId')]"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.event[?(@.Key == 'messageId')]"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.event[?(@.Key == 'temperature')]"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.event[?(@.Key == 'humidity')]"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Adatbevitel a `Events` táblába.

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Hozzon létre egy JSON-leképezést.

    ```Python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Adatbevitel a `Events` táblába.

     ```Python
    MAPPING = "KeyValueEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)u
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

---    

## <a name="next-steps"></a>Következő lépések

* [Adatfeldolgozás áttekintése](ingest-data-overview.md)
* [Lekérdezések írása](write-queries.md)