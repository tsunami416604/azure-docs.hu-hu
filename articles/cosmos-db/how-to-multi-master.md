---
title: Több főkiszolgálós konfigurálása az Azure Cosmos DB-ben
description: Az Azure Cosmos DB-ben az alkalmazások több főkiszolgálós konfigurálása
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: 84c8e2921602bb653c0b1ef0adffd3d89e91bd78
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312140"
---
# <a name="how-to-configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Több főkiszolgálós konfigurálása az Azure Cosmos DB használó alkalmazásokban

Több főkiszolgálós szolgáltatások használata az alkalmazások a szüksége több naplórekordjait engedélyezheti és konfigurálhatja a többhelyűségével funkció. Tagként az aktuális régióban, ahol az alkalmazás üzemel beállításával van konfigurálva.

## <a id="netv2"></a>.NET SDK-val v2

Ahhoz, hogy az alkalmazások csoportban több főkiszolgálós `UseMultipleWriteLocations` igaz értékre, és konfigurálhatja `SetCurrentLocation` , amelyben a rendszer telepíti az alkalmazást, és a rendszer replikálja az Azure Cosmos DB-régió.

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

Ahhoz, hogy az alkalmazások több főkiszolgálós konfigurálása `UseCurrentRegion` -régió, amelyben az alkalmazás üzembe helyezéséhez, és a Cosmos DB a rendszer replikálja.

```csharp
CosmosConfiguration config = new CosmosConfiguration("endpoint", "key");
config.UseCurrentRegion("West US");
CosmosClient client = new CosmosClient(config);
```

## <a id="java"></a>Java Async SDK

Ahhoz, hogy az alkalmazások csoportban több főkiszolgálós `policy.setUsingMultipleWriteLocations(true)` igaz értékre, és konfigurálhatja `policy.setPreferredLocations` -régió, amelyben az alkalmazás üzembe helyezéséhez, és a Cosmos DB a rendszer replikálja.

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

## <a id="javascript"></a>NODE.js, JavaScript, TypeScript SDK

Ahhoz, hogy az alkalmazások csoportban több főkiszolgálós `connectionPolicy.UseMultipleWriteLocations` igaz értékre, és konfigurálhatja `connectionPolicy.PreferredLocations` -régió, amelyben az alkalmazás üzembe helyezéséhez, és a Cosmos DB a rendszer replikálja.

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

Ahhoz, hogy az alkalmazások csoportban több főkiszolgálós `connection_policy.UseMultipleWriteLocations` igaz értékre, és konfigurálhatja `connection_policy.PreferredLocations` -régió, amelyben az alkalmazás üzembe helyezéséhez, és a Cosmos DB a rendszer replikálja.

```python
connection_policy = documents.ConnectionPolicy()
connection_policy.UseMultipleWriteLocations = True
connection_policy.PreferredLocations = [region]

client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Session)
```

## <a name="next-steps"></a>További lépések

További információ a több főkiszolgálós, globális terjesztés és konzisztencia az Azure Cosmos DB-hez. Lásd az alábbi cikkeket:

* [Munkamenet-jogkivonatok kezeléséhez az Azure Cosmos DB konzisztencia vételéhez](how-to-manage-consistency.md#utilize-session-tokens)

* [Ütközés típusa és a megoldási házirendek az Azure Cosmos DB](conflict-resolution-policies.md)

* [Az Azure Cosmos DB magas rendelkezésre állás](high-availability.md)

* [Az Azure Cosmos DB-ben a megfelelő konzisztenciaszint kiválasztása](consistency-levels-choosing.md)

* [Konzisztencia, a rendelkezésre állás és teljesítmény kompromisszumot kínál a Azure Cosmos DB-ben](consistency-levels-tradeoffs.md)
