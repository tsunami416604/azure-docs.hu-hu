---
title: Több főkiszolgáló konfigurálása Azure Cosmos DBban
description: Megtudhatja, hogyan konfigurálhat több főkiszolgálót az alkalmazásaihoz a Azure Cosmos DB különböző SDK-k használatával.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.custom: tracking-python
ms.openlocfilehash: 62dedd4cf91143ee7b31b92880135ac6c7953ef9
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561248"
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

## <a name="java-async-sdk"></a><a id="java"></a>Java Async SDK

Ha engedélyezni szeretné a több főkiszolgálós alkalmazást az alkalmazásban, állítsa be `policy.setUsingMultipleWriteLocations(true)` és állítsa be `policy.setPreferredLocations` azt a régiót, amelyben az alkalmazást üzembe helyezi, és ahol a Cosmos db replikálja:

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

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node. js, JavaScript és írógéppel SDK-k

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
