---
title: Több főkiszolgáló konfigurálása Azure Cosmos DBban
description: Megtudhatja, hogyan konfigurálhat több főkiszolgálót az alkalmazásaihoz a Azure Cosmos DB különböző SDK-k használatával.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.custom: tracking-python
ms.openlocfilehash: 792b0ef3f31bfb9dd26c153d5bb0eeab6ba8baf9
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84677984"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Több főkiszolgálós konfiguráció konfigurálása a Azure Cosmos DBt használó alkalmazásokban

Ha egy fiók több írási régióval lett létrehozva, akkor az alkalmazásban két módosítást kell végeznie a DocumentClient ConnectionPolicy, hogy lehetővé váljon a több főkiszolgálós és több-önvezérlési funkció a Azure Cosmos DBban. A ConnectionPolicy belül állítsa a UseMultipleWriteLocations értéket True értékre, és adja meg annak a régiónak a nevét, ahol az alkalmazás telepítve van a SetCurrentLocation. Ezzel feltölti a PreferredLocations tulajdonságot az átadott hely földrajzi közelsége alapján. Ha az új régiót később hozzáadják a fiókhoz, az alkalmazást nem kell frissíteni vagy újratelepíteni, a rendszer automatikusan felismeri a szorosabb régiót, és lehetővé teszi, hogy a helyi esemény történjen.

> [!Note]
> Az egyírásos régióval eredetileg konfigurált Cosmos-fiókok több írási régióra (azaz több főkiszolgálóra) is konfigurálhatók a nulla leállási idővel. További információ: [több írási régió konfigurálása](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

Ha engedélyezni szeretné a több főkiszolgálót az alkalmazásban, állítsa a következőre: `UseMultipleWriteLocations` `true` . Azt a régiót is állítsa be, amelyben `SetCurrentLocation` az alkalmazást telepíti, és ahol a Azure Cosmos db replikálja:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a name="net-sdk-v3"></a><a id="netv3"></a>.NET SDK v3

Ha engedélyezni szeretné a több főkiszolgálós alkalmazást az alkalmazásban, állítsa `ApplicationRegion` azt a régiót, amelyben az alkalmazást üzembe helyezi, és ahol a Cosmos db replikálja:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Igény szerint a és a használatával is `CosmosClientBuilder` `WithApplicationRegion` elérheti ugyanazt az eredményt:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-v4-sdk"></a><a id="java4-multi-master"></a>Java v4 SDK

Ha engedélyezni szeretné a több főkiszolgálós alkalmazást az alkalmazásban, hívja meg a (z) és a (z `.multipleWriteRegionsEnabled(true)` `.preferredRegions(preferredRegions)` ) ügyfelet, ahol a egy `preferredRegions` `List` elemet tartalmaz – ez az a régió, amelyben az alkalmazás üzembe helyezése zajlik, és ahol a Cosmos db replikálódik:

# <a name="async"></a>[Aszinkron](#tab/api-async)

   [Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) aszinkron API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=ConfigureMultimasterAsync)]

# <a name="sync"></a>[Szinkronizálás](#tab/api-sync)

   [Java SDK v4](sql-api-sdk-java-v4.md) (Maven [com. Azure:: Azure-Cosmos](https://mvnrepository.com/artifact/com.azure/azure-cosmos)) Sync API

   [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=ConfigureMultimasterSync)]

--- 

## <a name="async-java-v2-sdk"></a><a id="java2-milti-master"></a>Aszinkron Java v2 SDK

A Java v2 SDK a Maven [com. microsoft. Azure-t használta:: Azure-cosmosdb](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb). Ha engedélyezni szeretné a több főkiszolgálós alkalmazást az alkalmazásban, állítsa be `policy.setUsingMultipleWriteLocations(true)` és állítsa be `policy.setPreferredLocations` azt a régiót, amelyben az alkalmazást üzembe helyezi, és ahol a Cosmos db replikálja:

```java
ConnectionPolicy policy = new ConnectionPolicy();
policy.setUsingMultipleWriteLocations(true);
policy.setPreferredLocations(Collections.singletonList(region));

AsyncDocumentClient client =
    new AsyncDocumentClient.Builder()
        .withMasterKeyOrResourceToken(this.accountKey)
        .withServiceEndpoint(this.accountEndpoint)
        .withConsistencyLevel(ConsistencyLevel.Eventual)
        .withConnectionPolicy(policy).build();
```

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node.js, JavaScript és írógéppel SDK-k

Ha engedélyezni szeretné a több főkiszolgálót az alkalmazásban, állítsa a következőre: `connectionPolicy.UseMultipleWriteLocations` `true` . Azt a régiót is állítsa be, amelyben `connectionPolicy.PreferredLocations` az alkalmazást telepíti, és ahol a Cosmos db replikálja:

```javascript
const connectionPolicy: ConnectionPolicy = new ConnectionPolicy();
connectionPolicy.UseMultipleWriteLocations = true;
connectionPolicy.PreferredLocations = [region];

const client = new CosmosClient({
  endpoint: config.endpoint,
  auth: { masterKey: config.key },
  connectionPolicy,
  consistencyLevel: ConsistencyLevel.Eventual
});
```

## <a name="python-sdk"></a><a id="python"></a>Python SDK

Ha engedélyezni szeretné a több főkiszolgálót az alkalmazásban, állítsa a következőre: `connection_policy.UseMultipleWriteLocations` `true` . Állítsa be `connection_policy.PreferredLocations` azt a régiót is, amelyben az alkalmazást üzembe helyezi, és ahol a Cosmos db replikálva van.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>Következő lépések

Olvassa el a következő cikkeket:

* [Munkamenet-tokenek használata a Azure Cosmos DB konzisztenciájának kezeléséhez](how-to-manage-consistency.md#utilize-session-tokens)
* [Ütközési típusok és megoldási szabályzatok a Azure Cosmos DBban](conflict-resolution-policies.md)
* [Magas rendelkezésre állás a Azure Cosmos DBban](high-availability.md)
* [Azure Cosmos DB konzisztenciáji szintjei](consistency-levels.md)
* [Válassza ki a megfelelő konzisztencia-szintet Azure Cosmos DB](consistency-levels-choosing.md)
* [A konzisztencia, a rendelkezésre állás és a teljesítménybeli kompromisszumok Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Rendelkezésre állási és teljesítménybeli kompromisszumok különböző konzisztencia-szintekhez](consistency-levels-tradeoffs.md)
* [Kiosztott átviteli sebesség globális méretezése](scaling-throughput.md)
* [Globális terjesztés: a motorháztető alatt](global-dist-under-the-hood.md)
