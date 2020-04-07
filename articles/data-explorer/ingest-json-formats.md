---
title: JSON-formátumú adatok betöltése az Azure Data Explorerbe
description: Ismerje meg, hogyan lehet json formázott adatokat beadni az Azure Data Explorerbe.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: bcf6a0ccfc04890052f1a4bab19f97ee4e55f87a
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756615"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>JSON-formázott mintaadatok betöltése az Azure Data Explorerbe

Ez a cikk bemutatja, hogyan json formázott adatok at egy Azure Data Explorer adatbázisba. A nyers és leképezett JSON egyszerű példáival kezdi, folytatja a többvonalas JSON-t, majd összetettebb JSON-sémákat kezel, amelyek tömböket és szótárakat tartalmaznak.  A példák részletezik a JSON-formátumú adatok Kusto lekérdezési nyelv (KQL), C#vagy Python használatával történő betöltésének folyamatát. A Kusto `ingest` lekérdezési nyelv vezérlési parancsok végrehajtása közvetlenül a motor végpontjára. Éles környezetben a betöltés az adatkezelési szolgáltatásba ügyfélkódtárak vagy adatkapcsolatok használatával történik. Olvassa [el a betöltési adatokat az Azure Data Explorer Python-kódtár használatával,](/azure/data-explorer/python-ingest-data) és [az adatok betöltése az Azure Data Explorer .NET standard SDK használatával](/azure/data-explorer/net-standard-ingest-data) az adatok ezen ügyfélkódtárakkal való betöltésére vonatkozó végigolvasáshoz.

## <a name="prerequisites"></a>Előfeltételek

[Egy tesztfürt és egy adatbázis](create-cluster-database-portal.md)

## <a name="the-json-format"></a>A JSON formátum

Az Azure Data Explorer két JSON fájlformátumot támogat:
* `json`: A vonal elválasztotta a JSON-t. A bemeneti adatok minden sora pontosan egy JSON-rekorddal rendelkezik.
* `multijson`: Többvonalú JSON. Az elemző figyelmen kívül hagyja a sorelválasztókat, és beolvas egy rekordot az előző pozícióból egy érvényes JSON végéig.

### <a name="ingest-and-map-json-formatted-data"></a>JSON-formátumú adatok betöltése és leképezése

A JSON formátumú adatok betöltéséhez meg kell adnia a *formátumot* a [betöltési tulajdonság használatával.](/azure/kusto/management/data-ingestion/index#ingestion-properties) A JSON-adatok betöltéséhez [le képezésre](/azure/kusto/management/mappings)van szükség, amely egy JSON-forrásbejegyzést képez le a céloszlophoz. Adatok betöltésekor használja az előre `jsonMappingReference` meghatározott betöltési `jsonMapping`tulajdonságot, vagy adja meg a betöltési tulajdonságot. Ez a cikk `jsonMappingReference` a betöltési tulajdonságot fogja használni, amely előre definiálva van a betöltéshez használt táblában. Az alábbi példákban a JSON-rekordok nyers adatokként történő betöltésével kezdjük egyetlen oszloptáblába. Ezután a leképezés t használjuk az egyes tulajdonságok leképezett oszlopba történő betöltéséhez. 

### <a name="simple-json-example"></a>Egyszerű JSON példa

A következő példa egy egyszerű JSON, egy lapos szerkezetű. Az adatok hőmérsékleti és páratartalom-információkat tartalmaznak, amelyeket több eszköz gyűjt. Minden rekord azonosítóval és időbélyegzővel van megjelölve.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>Nyers JSON-rekordok betöltése 

Ebben a példában a JSON rekordokat nyers adatként egy oszloptáblába tollazhatja be. Az adatok kezelése, a lekérdezések használata és a frissítési házirend az adatok betöltése után történik.

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

A Kusto lekérdezési nyelv használatával nyers JSON-formátumban szeretne adatokat beadni.

1. Jelentkezzen be [https://dataexplorer.azure.com](https://dataexplorer.azure.com)a ikonra.

1. Válassza a **Fürt hozzáadása** lehetőséget.

1. A **Fürt hozzáadása** párbeszédpanelen adja meg a `https://<ClusterName>.<Region>.kusto.windows.net/`fürt URL-címét az űrlapon, majd válassza a **Hozzáadás lehetőséget.**

1. Illessze be a következő parancsot, és a táblázat létrehozásához válassza a **Futtatás** gombot.

    ```Kusto
    .create table RawEvents (Event: dynamic)
    ```

    Ez a lekérdezés [dinamikus](/azure/kusto/query/scalar-data-types/dynamic) `Event` adattípusú egyetlen oszlopot tartalmazó táblát hoz létre.

1. Hozza létre a JSON-leképezést.

    ```Kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    Ez a parancs létrehoz egy leképezést, és leképezi a JSON gyökérelérési útját `$` az `Event` oszlophoz.

1. Adatok betöltése `RawEvents` a táblába.

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

A C# függesztse fel az adatok nyers JSON formátumban történő betöltését.

1. Hozza `RawEvents` létre a táblát.

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
    Ez a parancs létrehoz egy leképezést, és leképezi a JSON gyökérelérési útját `$` az `Event` oszlophoz.

1. Adatok betöltése `RawEvents` a táblába.

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
> Az adatok [kötegelési házirend](/azure/kusto/concepts/batchingpolicy)szerint összesítve vannak, ami néhány perces késést eredményez.

# <a name="python"></a>[Python](#tab/python)

A Python használatával nyers JSON formátumban szeretne adatokat beadni.

1. Hozza `RawEvents` létre a táblát.

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

1. Adatok betöltése `RawEvents` a táblába.

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
    > Az adatok [kötegelési házirend](/azure/kusto/concepts/batchingpolicy)szerint összesítve vannak, ami néhány perces késést eredményez.

---

## <a name="ingest-mapped-json-records"></a>Betöltési leképezett JSON-rekordok

Ebben a példában a JSON rekordok rekordok rekordok adatokat. Minden JSON tulajdonság egyetlen oszlophoz van rendelve a táblázatban. 

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Hozzon létre egy új táblát, amely a JSON bemeneti adatokhoz hasonló sémával van elsajátítva. Ezt a táblázatot az alábbi példákhoz és a betöltési parancsokhoz használjuk. 

    ```Kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. Hozza létre a JSON-leképezést.

    ```Kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    Ebben a leképezésben a táblaséma `timestamp` meghatározása szerint a bejegyzések `Time` adattípusként `datetime` kerülnek az oszlopba.

1. Adatok betöltése `Events` a táblába.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    A fájl "simple.json" van néhány vonal-elválasztott JSON rekordokat. A formátum `json`a , és a betöltési `FlatEventMapping` parancsban használt leképezés a létrehozott.

# <a name="c"></a>[C#](#tab/c-sharp)

1. Hozzon létre egy új táblát, amely a JSON bemeneti adatokhoz hasonló sémával van elsajátítva. Ezt a táblázatot az alábbi példákhoz és a betöltési parancsokhoz használjuk. 

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

    Ebben a leképezésben a táblaséma `timestamp` meghatározása szerint a bejegyzések `Time` adattípusként `datetime` kerülnek az oszlopba.    

1. Adatok betöltése `Events` a táblába.

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

    A fájl "simple.json" van néhány vonal-elválasztott JSON rekordokat. A formátum `json`a , és a betöltési `FlatEventMapping` parancsban használt leképezés a létrehozott.

# <a name="python"></a>[Python](#tab/python)

1. Hozzon létre egy új táblát, amely a JSON bemeneti adatokhoz hasonló sémával van elsajátítva. Ezt a táblázatot az alábbi példákhoz és a betöltési parancsokhoz használjuk. 

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

1. Adatok betöltése `Events` a táblába.

    ```Python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    A fájl "simple.json" van néhány sor elválasztott JSON rekordokat. A formátum `json`a , és a betöltési `FlatEventMapping` parancsban használt leképezés a létrehozott.    
---

## <a name="ingest-multi-lined-json-records"></a>Többvonalas JSON-rekordok betöltése

Ebben a példában többvonalú JSON-rekordokat kell betöltése. Minden JSON tulajdonság egyetlen oszlophoz van rendelve a táblázatban. A fájl "multilined.json" van néhány behúzott JSON rekordokat. A `multijson` formátum arra utasítja a motort, hogy olvassa el a JSON-struktúra rekordjait.

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

Adatok betöltése `Events` a táblába.

```Kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="c"></a>[C#](#tab/c-sharp)

Adatok betöltése `Events` a táblába.

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

# <a name="python"></a>[Python](#tab/python)

Adatok betöltése `Events` a táblába.

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

A tömbadattípusok értékek rendezett gyűjteményei. A JSON-tömb betöltését [frissítési házirend](/azure/kusto/management/update-policy)végzi. A JSON-t ugyanúgy kell bedolgozni, ahogy van egy köztes táblába. A frissítési házirend egy előre definiált függvényt futtat a `RawEvents` táblán, és az eredményeket a céltáblába irányítja. A következő struktúrával fogjuk betöltési adatokat:

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

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Hozzon `update policy` létre egy függvényt, amely kibővíti a gyűjteményt, `records` hogy `mv-expand` a gyűjtemény minden egyes értéke külön sort kapjon az operátor használatával. A táblát `RawEvents` forrástáblaként és `Events` céltáblaként fogjuk használni.

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

1. A függvény által fogadott sémának meg kell egyeznie a céltábla sémájával. Az `getschema` operátor segítségével tekintse át a sémát.

    ```Kusto
    EventRecordsExpand() | getschema
    ```

1. Adja hozzá a frissítési házirendet a céltáblához. Ez a házirend automatikusan futtatja a lekérdezést `RawEvents` a köztes tábla újonnan betöltött adataiközött, és bevezeti az eredményeket a `Events` táblába. Nulla adatmegőrzési házirendet definiáljon a köztes tábla megőrzésének elkerülése érdekében.

    ```Kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. Adatok betöltése `RawEvents` a táblába.

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. Tekintse át `Events` a táblázatban szereplő adatokat.

    ```Kusto
    Events
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. Hozzon létre egy frissítési `records` függvényt, amely kibővíti a gyűjteménygyűjteményét, így a gyűjtemény minden egyes értéke külön sort kap az `mv-expand` operátor használatával. A táblát `RawEvents` forrástáblaként és `Events` céltáblaként fogjuk használni.   

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

1. Adja hozzá a frissítési házirendet a céltáblához. Ez a házirend automatikusan futtatja a lekérdezést `RawEvents` a köztes tábla újonnan betöltött adataiközött, és az eredményeket a `Events` táblába betöltése. Nulla adatmegőrzési házirendet definiáljon a köztes tábla megőrzésének elkerülése érdekében.

    ```C#
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. Adatok betöltése `RawEvents` a táblába.

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
    
1. Tekintse át `Events` a táblázatban szereplő adatokat.

# <a name="python"></a>[Python](#tab/python)

1. Hozzon létre egy frissítési `records` függvényt, amely kibővíti a gyűjteménygyűjteményét, így a gyűjtemény minden egyes értéke külön sort kap az `mv-expand` operátor használatával. A táblát `RawEvents` forrástáblaként és `Events` céltáblaként fogjuk használni.   

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

1. Adja hozzá a frissítési házirendet a céltáblához. Ez a házirend automatikusan futtatja a lekérdezést `RawEvents` a köztes tábla újonnan betöltött adataiközött, és az eredményeket a `Events` táblába betöltése. Nulla adatmegőrzési házirendet definiáljon a köztes tábla megőrzésének elkerülése érdekében.

    ```Python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Adatok betöltése `RawEvents` a táblába.

    ```Python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. Tekintse át `Events` a táblázatban szereplő adatokat.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>Szótárakat tartalmazó JSON-rekordok betöltése

Szótár strukturált JSON tartalmaz kulcs-érték párok. A Json-rekordok a beírási `JsonPath`leképezésen mennek keresztül a logikai kifejezés használatával a ban. Az adatokat a következő struktúrával lehet betöltéssel:

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

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. JSON-leképezés létrehozása.

    ```Kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. Adatok betöltése `Events` a táblába.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="c"></a>[C#](#tab/c-sharp)

1. JSON-leképezés létrehozása.

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

1. Adatok betöltése `Events` a táblába.

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

# <a name="python"></a>[Python](#tab/python)

1. JSON-leképezés létrehozása.

    ```Python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Adatok betöltése `Events` a táblába.

     ```Python
    MAPPING = "KeyValueEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)u
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

---    

## <a name="next-steps"></a>További lépések

* [Az adatbetöltés áttekintése](ingest-data-overview.md)
* [Lekérdezések írása](write-queries.md)
