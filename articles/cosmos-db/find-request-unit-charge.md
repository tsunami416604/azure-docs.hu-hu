---
title: Keresés a kérelemegység (RU) ingyenesen az Azure Cosmos DB
description: Ismerje meg, hogyan keresse meg a kérelem egységek használata után egy Azure Cosmos-tárolóhoz ellen végrehajtott műveletek
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: thweiss
ms.openlocfilehash: 833f815f0c84584f084e4d4637c0318f7c2daec0
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59683834"
---
# <a name="find-the-request-unit-ru-charge-in-azure-cosmos-db"></a>Keresés a kérelemegység (RU) ingyenesen az Azure Cosmos DB

Ez a cikk bemutatja a különböző módokon található a [kérelemegység](request-units.md) minden olyan művelet egy Azure Cosmos-tárolóhoz elvégezni. A használat mérésére, vagy az Azure portal használatával, vagy vizsgálatával szerezheti be a választ küldött vissza az Azure Cosmos DB az SDK-k egyikével jelenleg lehetőség.

## <a name="sql-core-api"></a>SQL (Core) API

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Az Azure portal jelenleg lehetővé teszi a kérelem díja keresése egy SQL-lekérdezést.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-sql-api-dotnet.md#create-account) és hírcsatorna, adatok, vagy válasszon egy meglévő Azure Cosmos-fiókot, amely már tartalmaz adatokat.

1. Nyissa meg a **adatkezelő** ablaktáblán, és válassza ki a tárolót, hogy a használni kívánt.

1. Kattintson a **új SQL-lekérdezés**.

1. Adjon meg egy érvényes lekérdezést, majd kattintson a **lekérdezés végrehajtása**.

1. Kattintson a **lekérdezési statisztikák** a tényleges kérelem díja az imént futtatott kérelem megjelenítéséhez.

![Képernyőkép az SQL lekérdezési kérés ingyenesen az Azure Portalon](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk-v2"></a>A .NET SDK V2 használata

Tárolókból visszaküldött objektumok a [.NET SDK-val v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) (lásd: [ebben a rövid útmutatóban](create-sql-api-dotnet.md) a használattal kapcsolatos) teszi közzé egy `RequestCharge` tulajdonság.

```csharp
ResourceResponse<Document> fetchDocumentResponse = await client.ReadDocumentAsync(
    UriFactory.CreateDocumentUri("database", "container", "itemId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
var requestCharge = fetchDocumentResponse.RequestCharge;

StoredProcedureResponse<string> storedProcedureCallResponse = await client.ExecuteStoredProcedureAsync<string>(
    UriFactory.CreateStoredProcedureUri("database", "container", "storedProcedureId"),
    new RequestOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    });
requestCharge = storedProcedureCallResponse.RequestCharge;

IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
    UriFactory.CreateDocumentCollectionUri("database", "container"),
    "SELECT * FROM c",
    new FeedOptions
    {
        PartitionKey = new PartitionKey("partitionKey")
    }).AsDocumentQuery();
while (query.HasMoreResults)
{
    FeedResponse<dynamic> queryResponse = await query.ExecuteNextAsync<dynamic>();
    requestCharge = queryResponse.RequestCharge;
}
```

### <a name="use-the-java-sdk"></a>Use the Java SDK

Tárolókból visszaküldött objektumok a [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) (lásd: [ebben a rövid útmutatóban](create-sql-api-java.md) a használattal kapcsolatos) teszi közzé egy `getRequestCharge()` metódust.

```java
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<ResourceResponse<Document>> readDocumentResponse = client.readDocument(String.format("/dbs/%s/colls/%s/docs/%s", "database", "container", "itemId"), requestOptions);
readDocumentResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

Observable<StoredProcedureResponse> storedProcedureResponse = client.executeStoredProcedure(String.format("/dbs/%s/colls/%s/sprocs/%s", "database", "container", "storedProcedureId"), requestOptions, null);
storedProcedureResponse.subscribe(result -> {
    double requestCharge = result.getRequestCharge();
});

FeedOptions feedOptions = new FeedOptions();
feedOptions.setPartitionKey(new PartitionKey("partitionKey"));

Observable<FeedResponse<Document>> feedResponse = client
    .queryDocuments(String.format("/dbs/%s/colls/%s", "database", "container"), "SELECT * FROM c", feedOptions);
feedResponse.forEach(result -> {
    double requestCharge = result.getRequestCharge();
});
```

### <a name="use-the-nodejs-sdk"></a>A Node.js SDK-val

Tárolókból visszaküldött objektumok a [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) (lásd: [ebben a rövid útmutatóban](create-sql-api-nodejs.md) a használattal kapcsolatos) teszi közzé egy `headers` alobjektumban, amely az alapul szolgáló HTTP API által visszaadott összes fejléc van leképezve. A kérelem ingyenesen érhető el a `x-ms-request-charge` kulcsot.

```javascript
const item = await client
    .database('database')
    .container('container')
    .item('itemId', 'partitionKey')
    .read();
var requestCharge = item.headers['x-ms-request-charge'];

const storedProcedureResult = await client
    .database('database')
    .container('container')
    .storedProcedure('storedProcedureId')
    .execute({
        partitionKey: 'partitionKey'
    });
requestCharge = storedProcedureResult.headers['x-ms-request-charge'];

const query = client.database('database')
    .container('container')
    .items
    .query('SELECT * FROM c', {
        partitionKey: 'partitionKey'
    });
while (query.hasMoreResults()) {
    var result = await query.executeNext();
    requestCharge = result.headers['x-ms-request-charge'];
}
```

### <a name="use-the-python-sdk"></a>A Python SDK használata

A `CosmosClient` objektum a [Python SDK](https://pypi.org/project/azure-cosmos/) (lásd: [ebben a rövid útmutatóban](create-sql-api-python.md) a használattal kapcsolatos) tesz közzé egy `last_response_headers` szótár, amely az alapul szolgáló HTTP API által visszaadott összes fejléc leképezi a utolsó művelet végrehajtása. A kérelem ingyenesen érhető el a `x-ms-request-charge` kulcsot.

```python
response = client.ReadItem('dbs/database/colls/container/docs/itemId', { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure('dbs/database/colls/container/sprocs/storedProcedureId', None, { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']
```

## <a name="azure-cosmos-dbs-api-for-mongodb"></a>MongoDB-hez készült Azure Cosmos DB API

Kérelem egység díjat tesz elérhetővé egyéni [adatbázis-parancs](https://docs.mongodb.com/manual/reference/command/) nevű `getLastRequestStatistics.` Ez a parancs visszaadja a legutóbbi művelet végrehajtása a nevére, a kérelem díj és időtartama tartalmazó dokumentumot.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Az Azure portal jelenleg lehetővé teszi a kérelem díja keresése csak egy lekérdezést.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-mongodb-dotnet.md#create-a-database-account) és hírcsatorna, adatok, vagy kiválaszthat egy meglévő fiókot, amely már tartalmaz adatokat.

1. Nyissa meg a **adatkezelő** ablaktáblán, és válassza ki a gyűjteményt, amely a használni kívánt.

1. Kattintson a **új lekérdezés**.

1. Adjon meg egy érvényes lekérdezést, majd kattintson a **lekérdezés végrehajtása**.

1. Kattintson a **lekérdezési statisztikák** a tényleges kérelem díja az imént futtatott kérelem megjelenítéséhez.

![Képernyőkép a MongoDB lekérdezési kérés ingyenesen az Azure Portalon](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Használja a MongoDB .NET Driver-re

Használatakor a [MongoDB .NET Driver-re hivatalos](https://docs.mongodb.com/ecosystem/drivers/csharp/) (lásd: [ebben a rövid útmutatóban](create-mongodb-dotnet.md) a használattal kapcsolatos), parancsok meghívásával hajtható a `RunCommand` metódust egy `IMongoDatabase` objektum. Ezzel a módszerrel szükség van a `Command<>` absztrakt osztály.

```csharp
class GetLastRequestStatisticsCommand : Command<Dictionary<string, object>>
{
    public override RenderedCommand<Dictionary<string, object>> Render(IBsonSerializerRegistry serializerRegistry)
    {
        return new RenderedCommand<Dictionary<string, object>>(new BsonDocument("getLastRequestStatistics", 1), serializerRegistry.GetSerializer<Dictionary<string, object>>());
    }
}

Dictionary<string, object> stats = database.RunCommand(new GetLastRequestStatisticsCommand());
double requestCharge = (double)stats["RequestCharge"];
```

### <a name="use-the-mongodb-java-driver"></a>Használja a MongoDB Java Driver-re

Használatakor a [MongoDB Java Driver-re hivatalos](http://mongodb.github.io/mongo-java-driver/) (lásd: [ebben a rövid útmutatóban](create-mongodb-java.md) a használattal kapcsolatos), parancsok meghívásával hajtható a `runCommand` metódust egy `MongoDatabase` objektum.

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

### <a name="use-the-mongodb-nodejs-driver"></a>A MongoDB Node.js illesztőprogram

Használatakor a [hivatalos MongoDB Node.js illesztőprogram](https://mongodb.github.io/node-mongodb-native/) (lásd: [ebben a rövid útmutatóban](create-mongodb-nodejs.md) a használattal kapcsolatos), parancsok meghívásával hajtható a `command` metódust egy `db` objektum.

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

## <a name="cassandra-api"></a>Cassandra API

Ha műveleteket végeznek az Azure Cosmos DB Cassandra API, kérelem egységek használata után adja vissza a bejövő hasznos nevű mezőként `RequestCharge`.

### <a name="use-the-net-sdk"></a>A .NET SDK használata

Használatakor a [.NET SDK-val](https://www.nuget.org/packages/CassandraCSharpDriver/) (lásd: [ebben a rövid útmutatóban](create-cassandra-dotnet.md) a használattal kapcsolatos), a bejövő terhelés alatt lehet beolvasni a `Info` tulajdonságát egy `RowSet` objektum.

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"], 0);
```

### <a name="use-the-java-sdk"></a>Use the Java SDK

Használatakor a [Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core) (lásd: [ebben a rövid útmutatóban](create-cassandra-java.md) a használattal kapcsolatos), a bejövő hasznos meghívásával lekérhető a `getExecutionInfo()` metódust egy `ResultSet` objektum.

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

## <a name="gremlin-api"></a>Gremlin API

### <a name="use-drivers-and-sdk"></a>Használat illesztőprogramok és SDK-val

A Gremlin API által visszaadott fejlécek egyéni attribútumok, amelyek a Gremlin .NET és a Java SDK által jelenleg illesztett vannak leképezve. A kérelem ingyenesen érhető el a `x-ms-request-charge` kulcsot.

### <a name="use-the-net-sdk"></a>A .NET SDK használata

Használatakor a [Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/) (lásd: [ebben a rövid útmutatóban](create-graph-dotnet.md) a használattal kapcsolatos), állapot attribútumok alatt érhetők el a `StatusAttributes` tulajdonságát a `ResultSet<>` objektum.

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

### <a name="use-the-java-sdk"></a>Use the Java SDK

Használatakor a [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) (lásd: [ebben a rövid útmutatóban](create-graph-java.md) a használattal kapcsolatos), állapot attribútumok meghívásával lekérhető a `statusAttributes()` metódust a `ResultSet` objektum.

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

## <a name="table-api"></a>Tábla API

A csak az SDK jelenleg a table műveletek kérelem egységek használata után visszaadó a [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) (lásd: [ebben a rövid útmutatóban](create-table-dotnet.md) kapcsolatos annak használatát). A `TableResult` objektum rendelkezik egy `RequestCharge` tulajdonságot, amely tölti fel a rendszer az SDK-ban ellen az Azure Cosmos DB Table API használatakor.

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

## <a name="next-steps"></a>További lépések

További információ a kérelem-egységek felhasználását optimalizálása a következő cikkekben talál:

* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
* [Az Azure Cosmos DB kiosztott átviteli sebesség költségek optimalizálása](optimize-cost-throughput.md)
* [Az Azure Cosmos DB lekérdezési költségek optimalizálása](optimize-cost-queries.md)
* [Globális skálázása a kiosztott átviteli sebesség](scaling-throughput.md)
* [A tárolók és adatbázisok kiépítése átviteli](set-throughput.md)
* [Átviteli sebesség kiosztása tárolókhoz](how-to-provision-container-throughput.md)