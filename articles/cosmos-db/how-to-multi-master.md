---
title: Több főkiszolgáló konfigurálása Azure Cosmos DBban
description: Megtudhatja, hogyan konfigurálhat több főkiszolgálót az alkalmazásaihoz a Azure Cosmos DB különböző SDK-k használatával.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 654baed649093add2aa62f4ba81bf6ce7c3e0df5
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873641"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Több főkiszolgálós konfiguráció konfigurálása a Azure Cosmos DBt használó alkalmazásokban

Ha egy fiók több írási régióval lett létrehozva, akkor az alkalmazásban két módosítást kell végeznie a DocumentClient ConnectionPolicy, hogy lehetővé váljon a több főkiszolgálós és több-önvezérlési funkció a Azure Cosmos DBban. A ConnectionPolicy belül állítsa a UseMultipleWriteLocations értéket True értékre, és adja meg annak a régiónak a nevét, ahol az alkalmazás telepítve van a SetCurrentLocation. Ezzel feltölti a PreferredLocations tulajdonságot az átadott hely földrajzi közelsége alapján. Ha az új régiót később hozzáadják a fiókhoz, az alkalmazást nem kell frissíteni vagy újratelepíteni, a rendszer automatikusan felismeri a szorosabb régiót, és lehetővé teszi, hogy a helyi esemény történjen.

> [!Note]
> Az egyírásos régióval eredetileg konfigurált Cosmos-fiókok több írási régióra (azaz több főkiszolgálóra) is konfigurálhatók a nulla leállási idővel. További információ: [több írási régió konfigurálása](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a id="netv2"></a>.NET SDK v2

Ha engedélyezni szeretné a több főkiszolgálós alkalmazást az alkalmazásban, állítsa a `UseMultipleWriteLocations` `true`re. Továbbá állítsa be `SetCurrentLocation`t arra a régióra, amelyben az alkalmazást üzembe helyezi, és ahol a Azure Cosmos DB replikálja:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>.NET SDK v3

Ha engedélyezni szeretné a több főkiszolgálós alkalmazást az alkalmazásban, állítsa `ApplicationRegion`t arra a régióra, amelyben az alkalmazást üzembe helyezi, és Cosmos DB replikálja a következőt:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Igény szerint a `CosmosClientBuilder` és a `WithApplicationRegion` is elérheti ugyanazt az eredményt:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a id="java"></a>Java Async SDK

Ha engedélyezni szeretné az alkalmazásban a több főkiszolgálót, állítsa be `policy.setUsingMultipleWriteLocations(true)` és állítsa be a `policy.setPreferredLocations`t arra a régióra, amelyben az alkalmazást üzembe helyezi, és ahol a Cosmos DB replikálja:

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

## <a id="javascript"></a>Node. js, JavaScript és írógéppel SDK-k

Ha engedélyezni szeretné a több főkiszolgálós alkalmazást az alkalmazásban, állítsa a `connectionPolicy.UseMultipleWriteLocations` `true`re. Továbbá állítsa be `connectionPolicy.PreferredLocations`t arra a régióra, amelyben az alkalmazást üzembe helyezi, és ahol a Cosmos DB replikálja:

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

## <a id="python"></a>Python SDK

Ha engedélyezni szeretné a több főkiszolgálós alkalmazást az alkalmazásban, állítsa a `connection_policy.UseMultipleWriteLocations` `true`re. Továbbá állítsa be `connection_policy.PreferredLocations`t arra a régióra, amelyben az alkalmazást üzembe helyezi, és ahol a Cosmos DB replikálva van.

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
* [A kiosztott átviteli sebesség globális skálázása](scaling-throughput.md)
* [Globális terjesztés: a motorháztető alatt](global-dist-under-the-hood.md)
