---
title: Több főkiszolgálós konfigurálása az Azure Cosmos DB-ben
description: Ismerje meg, hogy több főkiszolgálós konfigurálása az Azure Cosmos DB-ben az alkalmazások.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: 86f5d64391dd5312d8c51a5b639b790e62b6710d
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560260"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Több főkiszolgálós konfigurálása az Azure Cosmos DB használó alkalmazásokban

Az alkalmazás a több főkiszolgálós szolgáltatását használja, többrégiós írási műveletek engedélyezése, és a többkiszolgálós funkció konfigurálása az Azure Cosmos DB-ben. Többkiszolgálós konfigurálásához állítsa a régió, ahol az alkalmazás üzemel.

## <a id="netv2"></a>.NET SDK-val v2

Az alkalmazás több főkiszolgálós engedélyezéséhez állítsa `UseMultipleWriteLocations` való `true`. Ezenkívül állítsa `SetCurrentLocation` -régió, ahol az alkalmazás üzembe helyezéséhez, illetve ahol a rendszer replikálja az Azure Cosmos DB:

```csharp
ConnectionPolicy policy = new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp,
        UseMultipleWriteLocations = true
    };
policy.SetCurrentLocation("West US 2");
```

## <a id="netv3"></a>.NET SDK-val v3 (előzetes verzió)

Az alkalmazás több főkiszolgálós engedélyezéséhez állítsa `UseCurrentRegion` -régió, ahol az alkalmazás üzembe helyezéséhez, illetve ahol a rendszer replikálja a Cosmos DB:

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Java Async SDK

Az alkalmazás több főkiszolgálós engedélyezéséhez állítsa `policy.setUsingMultipleWriteLocations(true)` és `policy.setPreferredLocations` -régió, ahol az alkalmazás üzembe helyezéséhez, illetve ahol a rendszer replikálja a Cosmos DB:

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

## <a id="javascript"></a>NODE.js és a TypeScript SDK-k

Az alkalmazás több főkiszolgálós engedélyezéséhez állítsa `connectionPolicy.UseMultipleWriteLocations` való `true`. Ezenkívül állítsa `connectionPolicy.PreferredLocations` -régió, ahol az alkalmazás üzembe helyezéséhez, illetve ahol a rendszer replikálja a Cosmos DB:

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

Az alkalmazás több főkiszolgálós engedélyezéséhez állítsa `connection_policy.UseMultipleWriteLocations` való `true`. Ezenkívül állítsa `connection_policy.PreferredLocations` a rendszer telepíti az alkalmazást, és ahol a rendszer replikálja a Cosmos DB-régió.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>További lépések

Olvassa el a következő cikkeket:

* [Munkamenet-jogkivonat konzisztencia az Azure Cosmos DB kezelése](how-to-manage-consistency.md#utilize-session-tokens)
* [Ütközés típusa és a megoldási házirendek az Azure Cosmos DB](conflict-resolution-policies.md)
* [Az Azure Cosmos DB magas rendelkezésre állás](high-availability.md)
* [Az Azure Cosmos DB-ben konzisztenciaszintek](consistency-levels.md)
* [Válassza ki a megfelelő konzisztenciaszint Azure Cosmos DB-ben](consistency-levels-choosing.md)
* [Konzisztencia, a rendelkezésre állás és a teljesítmény és kompromisszumot kínál a Azure Cosmos DB-ben](consistency-levels-tradeoffs.md)
* [Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény](consistency-levels-tradeoffs.md)
* [Globálisan a kiosztott átviteli kapacitás méretezése](scaling-throughput.md)
* [Globális terjesztés: Technikai részletek](global-dist-under-the-hood.md)
