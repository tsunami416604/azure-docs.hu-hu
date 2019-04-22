---
title: Több főkiszolgálós konfigurálása az Azure Cosmos DB-ben
description: Az Azure Cosmos DB-ben az alkalmazások több főkiszolgálós konfigurálása
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: b862c59002369662d37b6d6a9de28370b0000497
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59682270"
---
# <a name="how-to-configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Több főkiszolgálós konfigurálása az Azure Cosmos DB használó alkalmazásokban

Több főkiszolgálós szolgáltatást használja az alkalmazást, szüksége több naplórekordjait engedélyezése és a többkiszolgálós funkció konfigurálása az Azure Cosmos DB. Többkiszolgálós a régió, ahol az alkalmazás központi telepítése úgy van konfigurálva.

## <a id="netv2"></a>.NET SDK-val v2

Ahhoz, hogy az alkalmazások csoportban több főkiszolgálós `UseMultipleWriteLocations` igaz értékre, és konfigurálja a `SetCurrentLocation` , amelyben a rendszer telepíti az alkalmazást, és a rendszer replikálja az Azure Cosmos DB-régió.

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

Ahhoz, hogy az alkalmazások csoportban több főkiszolgálós `policy.setUsingMultipleWriteLocations(true)` és konfigurálása `policy.setPreferredLocations` -régió, amelyben az alkalmazás üzembe helyezéséhez, és a Cosmos DB a rendszer replikálja.

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

Ezután az alábbi cikkekben olvashat:

* [Munkamenet-jogkivonatok kezeléséhez az Azure Cosmos DB konzisztencia vételéhez](how-to-manage-consistency.md#utilize-session-tokens)
* [Ütközés típusa és a megoldási házirendek az Azure Cosmos DB](conflict-resolution-policies.md)
* [Az Azure Cosmos DB magas rendelkezésre állás](high-availability.md)
* [Az Azure Cosmos DB-ben konzisztenciaszintek](consistency-levels.md)
* [Az Azure Cosmos DB-ben a megfelelő konzisztenciaszint kiválasztása](consistency-levels-choosing.md)
* [Konzisztencia, a rendelkezésre állás és teljesítmény kompromisszumot kínál a Azure Cosmos DB-ben](consistency-levels-tradeoffs.md)
* [Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény](consistency-levels-tradeoffs.md)
* [Globálisan a kiosztott átviteli kapacitás méretezése](scaling-throughput.md)
* [Globális terjesztés – technikai részletek](global-dist-under-the-hood.md)
