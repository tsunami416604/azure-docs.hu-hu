---
title: A kérelemegység (RU) díjának megkeresése az Azure Cosmos DB-ben
description: Ismerje meg, hogyan található meg a kérelemegység (RU) díja az Azure Cosmos-tárolóval végrehajtott műveletekért.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/01/2019
ms.author: thweiss
ms.openlocfilehash: c5699bb851bd0a818a987228155c62683e93f51a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585900"
---
# <a name="find-the-request-unit-charge-in-azure-cosmos-db"></a>A kérelemegység díjának megkeresése az Azure Cosmos DB-ben

Ez a cikk bemutatja a különböző módokon megtalálhatja a [kérelemegység](request-units.md) (RU) felhasználása az Azure Cosmos DB tárolón végrehajtott bármely művelethez. Jelenleg ezt a felhasználást csak az Azure Portalon keresztül mérheti, vagy az Azure Cosmos DB-ből az SDK-k egyikén keresztül visszaküldött válasz vizsgálatával.

## <a name="sql-core-api"></a>SQL (Core) API

Ha az SQL API-t használja, több lehetőség is van az Azure Cosmos-tárolóval való művelet ru-felhasználásának megkeresésére.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Jelenleg csak egy SQL-lekérdezés esetén találhatja meg a kérelemdíj az Azure Portalon.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. [Hozzon létre egy új Azure Cosmos-fiókot,](create-sql-api-dotnet.md#create-account) és adja át adatokkal, vagy válasszon ki egy meglévő Azure Cosmos-fiókot, amely már tartalmaz adatokat.

1. Nyissa meg az **Adatkezelő** ablaktábláját, és jelölje ki azt a tárolót, amelyen dolgozni szeretne.

1. Válassza az **Új SQL-lekérdezés** lehetőséget.

1. Írjon be egy érvényes lekérdezést, majd válassza **a Lekérdezés végrehajtása lehetőséget.**

1. Válassza a **Lekérdezési statisztika lehetőséget** a végrehajtott kérelem tényleges kérési díjának megjelenítéséhez.

![Képernyőkép egy SQL-lekérdezési kérelem díjáról az Azure Portalon](./media/find-request-unit-charge/portal-sql-query.png)

### <a name="use-the-net-sdk"></a>A .NET SDK használata
### <a name="net-v2-sdk"></a>.Net V2 SDK

A [.NET SDK v2-ről](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/) visszaadott objektumok tulajdonságot `RequestCharge` fednek fel:

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

A [.NET SDK v3-ról](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) visszaadott objektumok tulajdonságot `RequestCharge` fednek fel:

[!code-csharp[](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos/tests/Microsoft.Azure.Cosmos.Tests/SampleCodeForDocs/CustomDocsSampleCode.cs?name=GetRequestCharge)]

További információ: [Rövid útmutató: .NET webalkalmazás létrehozása SQL API-fiók használatával az Azure Cosmos DB-ben.](create-sql-api-dotnet.md)

### <a name="use-the-java-sdk"></a>A Java SDK használata

A [Java SDK-ból](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) visszaadott `getRequestCharge()` objektumok egy módszert jelentenek:

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

További információ: [Rövid útmutató: Java-alkalmazás létrehozása Egy Azure Cosmos DB SQL API-fiók használatával.](create-sql-api-java.md)

### <a name="use-the-nodejs-sdk"></a>A Node.js SDK használata

A [Node.js SDK-ból](https://www.npmjs.com/package/@azure/cosmos) visszaadott `headers` objektumok olyan alobjektumot jelentenek be, amely leképezi az alapul szolgáló HTTP API által visszaadott összes fejlécet. A kérelem díja `x-ms-request-charge` a kulcs alatt érhető el:

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

További információ: [Rövid útmutató: Node.js alkalmazás létrehozása Egy Azure Cosmos DB SQL API-fiók használatával.](create-sql-api-nodejs.md) 

### <a name="use-the-python-sdk"></a>A Python SDK használata

A `CosmosClient` Python [SDK-ból](https://pypi.org/project/azure-cosmos/) származó `last_response_headers` objektum egy szótárat fed le, amely leképezi az alapul szolgáló HTTP API által visszaadott összes fejlécet az utolsó végrehajtott művelethez. A kérelem díja `x-ms-request-charge` a kulcs alatt érhető el:

```python
response = client.ReadItem(
    'dbs/database/colls/container/docs/itemId', {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']

response = client.ExecuteStoredProcedure(
    'dbs/database/colls/container/sprocs/storedProcedureId', None, {'partitionKey': 'partitionKey'})
request_charge = client.last_response_headers['x-ms-request-charge']
```

További információ: [Rövid útmutató: Python-alkalmazás létrehozása Egy Azure Cosmos DB SQL API-fiók használatával.](create-sql-api-python.md) 

## <a name="azure-cosmos-db-api-for-mongodb"></a>MongoDB-hez készült Azure Cosmos DB API

A RU-díjat egy egyéni [adatbázisparancs](https://docs.mongodb.com/manual/reference/command/) teszi elérhetővé, amelynek neve `getLastRequestStatistics`. A parancs egy olyan dokumentumot ad vissza, amely tartalmazza az utolsó végrehajtott művelet nevét, a kérelemdíját és időtartamát. Ha az Azure Cosmos DB API-t használja a MongoDB-hoz, több lehetősége van a RU-díj beolvasására.

### <a name="use-the-azure-portal"></a>Az Azure Portal használata

Jelenleg csak egy lekérdezésesetén találhatja meg a kérelemdíj az Azure Portalon.

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. [Hozzon létre egy új Azure Cosmos-fiókot,](create-mongodb-dotnet.md#create-a-database-account) és adja át adatokkal, vagy válasszon ki egy meglévő fiókot, amely már tartalmaz adatokat.

1. Nyissa meg az **Adatkezelő** ablaktábláját, és jelölje ki azt a tárolót, amelyen dolgozni szeretne.

1. Válassza a **New Query** (Új lekérdezés) lehetőséget.

1. Írjon be egy érvényes lekérdezést, majd válassza **a Lekérdezés végrehajtása lehetőséget.**

1. Válassza a **Lekérdezési statisztika lehetőséget** a végrehajtott kérelem tényleges kérési díjának megjelenítéséhez.

![Képernyőkép egy MongoDB-lekérdezési kérelem díjáról az Azure Portalon](./media/find-request-unit-charge/portal-mongodb-query.png)

### <a name="use-the-mongodb-net-driver"></a>A MongoDB .NET illesztőprogram használata

A hivatalos [MongoDB .NET illesztőprogram](https://docs.mongodb.com/ecosystem/drivers/csharp/)használata esetén a `RunCommand` parancsokat `IMongoDatabase` egy objektum metódusának hívásával hajthatja végre. Ez a módszer megköveteli `Command<>` az absztrakt osztály megvalósítását:

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

További információ: [Rövid útmutató: .NET webalkalmazás létrehozása a MongoDB Azure Cosmos DB API használatával című témakörben.](create-mongodb-dotnet.md)

### <a name="use-the-mongodb-java-driver"></a>A MongoDB Java illesztőprogram használata


A hivatalos [MongoDB Java illesztőprogram](https://mongodb.github.io/mongo-java-driver/)használatakor parancsokat `runCommand` hajthat `MongoDatabase` végre egy objektum metódusának hívásával:

```java
Document stats = database.runCommand(new Document("getLastRequestStatistics", 1));
Double requestCharge = stats.getDouble("RequestCharge");
```

További információ: [Rövid útmutató: Webalkalmazás létrehozása az Azure Cosmos DB API-val mongoDB és a Java SDK](create-mongodb-java.md)használatával.

### <a name="use-the-mongodb-nodejs-driver"></a>A MongoDB Node.js illesztőprogram használata

A hivatalos [MongoDB Node.js illesztőprogram](https://mongodb.github.io/node-mongodb-native/)használatakor parancsokat `command` hajthat `db` végre egy objektum metódusának hívásával:

```javascript
db.command({ getLastRequestStatistics: 1 }, function(err, result) {
    assert.equal(err, null);
    const requestCharge = result['RequestCharge'];
});
```

További információ: [Rövid útmutató: Áttelepítés egy meglévő MongoDB Node.js webalkalmazás az Azure Cosmos](create-mongodb-nodejs.md)DB.For.

## <a name="cassandra-api"></a>Cassandra API

Amikor műveleteket hajt végre az Azure Cosmos DB Cassandra API-val, a RU-díj a bejövő hasznos adatformájában kerül vissza a bejövő hasznos adatra. `RequestCharge` Többféleképpen is lekérheti a kérelemegység-költséget.

### <a name="use-the-net-sdk"></a>A .NET SDK használata

A [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/)használatakor a bejövő hasznos adat `Info` egy `RowSet` objektum tulajdonsága alatt olvasható be:

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

További információ: [Rövid útmutató: Cassandra-alkalmazás létrehozása a .NET SDK és az Azure Cosmos DB használatával.](create-cassandra-dotnet.md)

### <a name="use-the-java-sdk"></a>A Java SDK használata

A Java [SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core)használatakor a bejövő hasznos adat lekéréséhez hívja meg a `getExecutionInfo()` metódust egy `ResultSet` objektumon:

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

További információ: [Rövid útmutató: Cassandra-alkalmazás létrehozása a Java SDK és az Azure Cosmos DB használatával.](create-cassandra-java.md)

## <a name="gremlin-api"></a>Gremlin API

A Gremlin API használata esetén több lehetőség is van az Azure Cosmos-tárolóval való művelet hez való ru-felhasználás megkeresésére. 

### <a name="use-drivers-and-sdk"></a>Illesztőprogramok és SDK használata

A Gremlin API által visszaadott fejlécek egyéni állapotattribútumokhoz vannak rendelve, amelyeket jelenleg a Gremlin .NET és a Java SDK felszínre hoz. A kérés díja `x-ms-request-charge` a kulcs alatt áll rendelkezésre.

### <a name="use-the-net-sdk"></a>A .NET SDK használata

Az Gremlin.NET [SDK](https://www.nuget.org/packages/Gremlin.Net/)használatakor az állapotattribútumok `StatusAttributes` az `ResultSet<>` objektum tulajdonsága alatt érhetők el:

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

További információ: [Rövid útmutató: .](create-graph-dotnet.md)

### <a name="use-the-java-sdk"></a>A Java SDK használata

A [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver)használatakor az állapotattribútumokat `statusAttributes()` az `ResultSet` objektum metódusának hívásával kérheti le:

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

További információ: [Rövid útmutató: Graph adatbázis létrehozása az Azure Cosmos DB-ben a Java SDK használatával.](create-graph-java.md)

## <a name="table-api"></a>Tábla API

Jelenleg az egyetlen SDK, amely a táblaműveletek ru díját adja vissza, a [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). Az `TableResult` objektum elérhetővé teszi az SDK által feltöltött `RequestCharge` tulajdonságot, amikor az Azure Cosmos DB Table API-val használja:

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

További információ: [Rövid útmutató: Table API-alkalmazás létrehozása a .NET SDK és az Azure Cosmos DB használatával.](create-table-dotnet.md)

## <a name="next-steps"></a>További lépések

A ru-felhasználás optimalizálásáról az alábbi cikkekben olvashat:

* [Kérelemegységek és átviteli sebesség az Azure Cosmos DB-ben](request-units.md)
* [A kiosztott átviteli sebesség költségeinek optimalizálása az Azure Cosmos DB-ben](optimize-cost-throughput.md)
* [A lekérdezési költségek optimalizálása az Azure Cosmos DB-ben](optimize-cost-queries.md)
* [Kiosztott átviteli sebesség globális skálázása](scaling-throughput.md)
* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md)
* [Átviteli átbocsátás egy tárolóhoz](how-to-provision-container-throughput.md)
* [Az Azure Cosmos DB metrikák figyelése és hibakeresése](use-metrics.md)
