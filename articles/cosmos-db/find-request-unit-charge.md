---
title: Keresés a kérelemegység (RU) ingyenesen az Azure Cosmos DB
description: Ismerje meg, hogy bármilyen műveletet elvégezni az Azure Cosmos-tárolókat a kérelemegység (RU) díj megkeresése.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/14/2019
ms.author: thweiss
ms.openlocfilehash: 3d088da4c771c828db9788817e424c4d89586dd6
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "67986150"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>Keresse meg a kérelem egységek használata után az Azure Cosmos DB-ben

Ez a cikk bemutatja a többféleképpen is megtalálhatja a [kérelemegység](request-units.md) (RU) használatalapú fel elleni egy tárolót az Azure Cosmos DB-ben végrehajtott műveletekhez. Jelenleg a használat mérésére, csak az Azure portal használatával, vagy a választ küldött vissza az Azure Cosmos DB az SDK-k egyikével vizsgálatával szerezheti be.

## <a name="sql-core-api"></a>SQL (Core) API

Ha az SQL API-t használ, a művelet egy Azure Cosmos-tárolót a fogyasztott kereséséhez több lehetősége van.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Jelenleg megtalálhatja a kérelem díja az Azure Portalon csak az SQL-lekérdezést.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-sql-api-dotnet.md#create-account) és hírcsatorna, adatok, vagy válasszon egy meglévő Azure Cosmos-fiókot, amely már tartalmaz adatokat.

1. Nyissa meg a **adatkezelő** panelre, és válassza ki a tárolót a működik.

1. Válassza ki **új SQL-lekérdezés**.

1. Adjon meg egy érvényes lekérdezést, és válassza ki **lekérdezés végrehajtása**.

1. Válassza ki **lekérdezési statisztikák** hajtja végre a kérést a tényleges kérelem díja megjelenítéséhez.

![Az SQL lekérdezési kérés díj az Azure Portal képernyőképe](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk"></a>A .NET SDK használata
### <a name="net-v2-sdk"></a>.Net V2 SDK

A visszaadott objektumok a [.NET SDK-val v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) közzé egy `RequestCharge` tulajdonság:

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

### <a name="net-v3-sdk"></a>.Net V3 SDK

A visszaadott objektumok a [.NET SDK-val v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) közzé egy `RequestCharge` tulajdonság:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

További információkért lásd: [a rövid útmutató: .NET-webalkalmazás összeállítása az Azure Cosmos DB SQL API-fiók használatával](create-sql-api-dotnet.md).

### <a name="use-the-java-sdk"></a>Use the Java SDK

A visszaadott objektumok a [Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) közzé egy `getRequestCharge()` metódus:

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

További információkért lásd: [a rövid útmutató: Java-alkalmazás létrehozása egy Azure Cosmos DB SQL API-fiók használatával](create-sql-api-java.md).

### <a name="use-the-nodejs-sdk"></a>A Node.js SDK-val

A visszaadott objektumok a [Node.js SDK](https://www.npmjs.com/package/@azure/cosmos) közzé egy `headers` alobjektum, amely az alapul szolgáló HTTP API által visszaadott összes fejléc van leképezve. A kérelem ingyenesen érhető el a `x-ms-request-charge` kulcs:

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

További információkért lásd: [a rövid útmutató: Node.js-alkalmazás létrehozása egy Azure Cosmos DB SQL API-fiók használatával](create-sql-api-nodejs.md). 

### <a name="use-the-python-sdk"></a>A Python SDK használata

A `CosmosClient` objektum a [Python SDK](https://pypi.org/project/azure-cosmos/) tesz közzé egy `last_response_headers` szótár, amely az utolsó művelet végrehajtása a mögöttes HTTP API által visszaadott összes fejléc van leképezve. A kérelem ingyenesen érhető el a `x-ms-request-charge` kulcs:

```python
response = client.ReadItem('dbs/database/colls/container/docs/itemId', { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure('dbs/database/colls/container/sprocs/storedProcedureId', None, { 'partitionKey': 'partitionKey' })
request_charge = client.last_response_headers['x-ms-request-charge']
```

További információkért lásd: [a rövid útmutató: Python-alkalmazás létrehozása egy Azure Cosmos DB SQL API-fiók használatával](create-sql-api-python.md). 

## <a name="azure-cosmos-db-api-for-mongodb"></a>MongoDB-hez készült Azure Cosmos DB API

A Kérelemegység díjat tesz elérhetővé egyéni [adatbázis-parancs](https://docs.mongodb.com/manual/reference/command/) nevű `getLastRequestStatistics`. A parancs egy dokumentumot, amely tartalmazza a legutóbbi művelet végrehajtása a nevére, a kérelem díj és időtartamát adja vissza. Ha használja az Azure Cosmos DB API a mongodb-hez, a RU díj beolvasásakor többféle módon is lehet.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Jelenleg találja a kérelem díja az Azure Portalon csak egy lekérdezést.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. [Hozzon létre egy új Azure Cosmos-fiókot](create-mongodb-dotnet.md#create-a-database-account) és hírcsatorna, adatok, vagy kiválaszthat egy meglévő fiókot, amely már tartalmaz adatokat.

1. Nyissa meg a **adatkezelő** panelre, és válassza ki a gyűjteményt, a használni kívánt.

1. Válassza a **New Query** (Új lekérdezés) lehetőséget.

1. Adjon meg egy érvényes lekérdezést, és válassza ki **lekérdezés végrehajtása**.

1. Válassza ki **lekérdezési statisztikák** hajtja végre a kérést a tényleges kérelem díja megjelenítéséhez.

![Képernyőkép a MongoDB lekérdezési kérés díj az Azure Portalon](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>Használja a MongoDB .NET Driver-re

Használatakor a [MongoDB .NET Driver-re hivatalos](https://docs.mongodb.com/ecosystem/drivers/csharp/), meghívásával futtathat parancsokat a `RunCommand` metódust egy `IMongoDatabase` objektum. Ezzel a módszerrel szükség van a `Command<>` absztrakt osztály:

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

További információkért lásd: [a rövid útmutató: .NET-webalkalmazás összeállítása az Azure Cosmos DB API használatával a mongodb-hez](create-mongodb-dotnet.md).

### <a name="use-the-mongodb-java-driver"></a>Használja a MongoDB Java Driver-re


Használatakor a [MongoDB Java Driver-re hivatalos](https://mongodb.github.io/mongo-java-driver/), meghívásával futtathat parancsokat a `runCommand` metódust egy `MongoDatabase` objektum:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

További információkért lásd: [a rövid útmutató: Webes alkalmazás készítése az Azure Cosmos DB API a MongoDB-hez és a Java SDK](create-mongodb-java.md).

### <a name="use-the-mongodb-nodejs-driver"></a>A MongoDB Node.js illesztőprogram

Használatakor a [hivatalos MongoDB Node.js illesztőprogram](https://mongodb.github.io/node-mongodb-native/), meghívásával futtathat parancsokat a `command` metódust egy `db` objektum:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

További információkért lásd: [a rövid útmutató: Egy meglévő MongoDB Node.js-webalkalmazás migrálása az Azure Cosmos DB](create-mongodb-nodejs.md).

## <a name="cassandra-api"></a>Cassandra API

Ellen az Azure Cosmos DB Cassandra API műveletet hajt végre, ha az RU díj adja vissza a bejövő hasznos nevű mezőként `RequestCharge`. Több lehetőség áll rendelkezésére a RU díj beolvasásakor.

### <a name="use-the-net-sdk"></a>A .NET SDK használata

Használatakor a [.NET SDK-val](https://www.nuget.org/packages/CassandraCSharpDriver/), kérheti le a bejövő terhelés alatt a `Info` tulajdonságát egy `RowSet` objektum:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

További információkért lásd: [a rövid útmutató: Cassandra alkalmazás felépítése a .NET SDK-t és az Azure Cosmos DB használatával](create-cassandra-dotnet.md).

### <a name="use-the-java-sdk"></a>Use the Java SDK

Használatakor a [Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core), meghívásával kérheti le a bejövő hasznos a `getExecutionInfo()` metódust egy `ResultSet` objektum:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

További információkért lásd: [a rövid útmutató: Cassandra alkalmazás felépítése a Java SDK-t és az Azure Cosmos DB használatával](create-cassandra-java.md).

## <a name="gremlin-api"></a>Gremlin API

A Gremlin API használata esetén több lehetőség áll rendelkezésére a fogyasztott ellen az Azure Cosmos-tárolókat egy művelet kereséséhez. 

### <a name="use-drivers-and-sdk"></a>Használat illesztőprogramok és SDK-val

A Gremlin API által visszaadott fejlécek egyéni attribútumok, amelyek a Gremlin .NET és a Java SDK által jelenleg illesztett vannak leképezve. A kérelem ingyenesen érhető el a `x-ms-request-charge` kulcsot.

### <a name="use-the-net-sdk"></a>A .NET SDK használata

Használatakor a [Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/), állapot attribútumok alatt érhetők el a `StatusAttributes` tulajdonságát a `ResultSet<>` objektum:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

További információkért lásd: [a rövid útmutató: A .NET-keretrendszer vagy Core-alkalmazás létrehozása egy Azure Cosmos DB Gremlin API-fiók használatával](create-graph-dotnet.md).

### <a name="use-the-java-sdk"></a>Use the Java SDK

Használatakor a [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver), állapot attribútumok meghívásával lehet lekérdezni a `statusAttributes()` metódust a `ResultSet` objektum:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

További információkért lásd: [a rövid útmutató: Az Azure Cosmos DB gráfadatbázis létrehozása a Java SDK-val](create-graph-java.md).

## <a name="table-api"></a>Tábla API

Az egyetlen SDK-t, amely visszaadja a tábla műveleti RU díját jelenleg a [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). A `TableResult` objektum rendelkezik egy `RequestCharge` tulajdonságot, amely az SDK-ban van feltöltve, szemben az Azure Cosmos DB Table API használatakor:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

További információkért lásd: [a rövid útmutató: A .NET SDK-t és az Azure Cosmos DB Table API-alkalmazás létrehozása](create-table-dotnet.md).

## <a name="next-steps"></a>További lépések

A fogyasztott optimalizálásával kapcsolatos további információkért tanulmányozza a következő cikkeket:

* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
* [A kiosztott átviteli sebesség költségeinek optimalizálása az Azure Cosmos DB-ben](optimize-cost-throughput.md)
* [Az Azure Cosmos DB lekérdezési költségek optimalizálása](optimize-cost-queries.md)
* [Globális skálázása a kiosztott átviteli sebesség](scaling-throughput.md)
* [A tárolók és adatbázisok kiépítése átviteli](set-throughput.md)
* [A tároló üzembe helyezése átviteli](how-to-provision-container-throughput.md)
* [A figyelő és hibakeresése az Azure Cosmos DB metrikákkal](use-metrics.md)
