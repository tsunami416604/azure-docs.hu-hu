---
title: Többfőkiszolgálókonfigurálás az Azure Cosmos DB-ben
description: Ismerje meg, hogyan konfigurálhatja a többfőes az alkalmazások segítségével különböző SDK-k az Azure Cosmos DB használatával.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 654baed649093add2aa62f4ba81bf6ce7c3e0df5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873641"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Többfőkiszolgálós konfigurálás az Azure Cosmos DB-t használó alkalmazásokban

Miután létrehozott egy fiókot, amelyen több írási régió engedélyezve van, két módosítást kell végrehajtania az alkalmazásban a DocumentClient ConnectionPolicy alkalmazásában, hogy engedélyezze a többfős és többhelyű képességeket az Azure Cosmos DB-ben. A ConnectionPolicy-en belül állítsa a UseMultipleWriteLocations értéket true értékre, és adja át annak a régiónak a nevét, ahol az alkalmazás telepítve van a SetCurrentLocation szolgáltatásba. Ez feltölti a PreferredLocations tulajdonságot a beadott hely földrajzi közelsége alapján. Ha egy új régiót később hozzáad a fiókhoz, az alkalmazást nem kell frissíteni vagy újratelepíteni, automatikusan észleli a közelebbi régiót, és regionális esemény bekövetkezése esetén automatikusan hazafog települni hozzá.

> [!Note]
> Cosmos-fiókok eredetileg konfigurált egy írási régió konfigurálható több írási régiók (azaz több főkiszolgáló) nulla állásidő. További információ megtekintéséhez konfigurálja a [többírású régiókat](how-to-manage-database-account.md#configure-multiple-write-regions)

## <a name="net-sdk-v2"></a><a id="netv2"></a>.NET SDK v2

Ha engedélyezni szeretné a többfőkiszolgálós alkalmazást, állítsa a beállítását. `UseMultipleWriteLocations` `true` Azt a `SetCurrentLocation` régiót is, amelyben az alkalmazás üzembe helyezése folyamatban van, és ahol az Azure Cosmos DB replikálása történik:

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

A többfőkiszolgálós alkalmazás engedélyezéséhez állítsa be `ApplicationRegion` azt a régiót, amelyben az alkalmazás üzembe helyezése folyamatban van, és ahol a Cosmos DB replikálása történik:

```csharp
CosmosClient cosmosClient = new CosmosClient(
    "<connection-string-from-portal>", 
    new CosmosClientOptions()
    {
        ApplicationRegion = Regions.WestUS2,
    });
```

Opcionálisan használhatja a `CosmosClientBuilder` `WithApplicationRegion` és elérni ugyanazt az eredményt:

```csharp
CosmosClientBuilder cosmosClientBuilder = new CosmosClientBuilder("<connection-string-from-portal>")
            .WithApplicationRegion(Regions.WestUS2);
CosmosClient client = cosmosClientBuilder.Build();
```

## <a name="java-async-sdk"></a><a id="java"></a>Java Async SDK

A többfős kiszolgáló engedélyezése `policy.setUsingMultipleWriteLocations(true)` az `policy.setPreferredLocations` alkalmazásban, állítsa be, és állítsa be a régióban, ahol az alkalmazás telepítése folyamatban van, és ahol a Cosmos DB replikálása:

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

## <a name="nodejs-javascript-and-typescript-sdks"></a><a id="javascript"></a>Node.js, JavaScript és TypeScript SDK-k

Ha engedélyezni szeretné a többfőkiszolgálós alkalmazást, állítsa a beállítását. `connectionPolicy.UseMultipleWriteLocations` `true` Azt a `connectionPolicy.PreferredLocations` régiót is, amelyben az alkalmazás üzembe helyezése folyamatban van, és ahol a Cosmos DB replikálása történik:

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

Ha engedélyezni szeretné a többfőkiszolgálós alkalmazást, állítsa a beállítását. `connection_policy.UseMultipleWriteLocations` `true` Azt a `connection_policy.PreferredLocations` régiót, amelyben az alkalmazás üzembe helyezése folyamatban van, és ahol a Cosmos DB replikálása van beállítva.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>További lépések

Olvassa el a következő cikkeket:

* [Munkamenet-jogkivonatok használata az Azure Cosmos DB konzisztenciájának kezeléséhez](how-to-manage-consistency.md#utilize-session-tokens)
* [Ütközéstípusok és megoldási szabályzatok az Azure Cosmos DB-ben](conflict-resolution-policies.md)
* [Magas rendelkezésre állás az Azure Cosmos DB-ben](high-availability.md)
* [Konzisztenciaszintek az Azure Cosmos DB-ben](consistency-levels.md)
* [Válassza ki a megfelelő konzisztenciaszintet az Azure Cosmos DB-ben](consistency-levels-choosing.md)
* [Konzisztencia, rendelkezésre állás és teljesítmény kompromisszumok az Azure Cosmos DB-ben](consistency-levels-tradeoffs.md)
* [Rendelkezésre állási és teljesítménykompromisszumok a különböző konzisztenciaszintekhez](consistency-levels-tradeoffs.md)
* [Kiosztott átviteli sebesség globális méretezése](scaling-throughput.md)
* [Globális disztribúció: A motorháztető alatt](global-dist-under-the-hood.md)
