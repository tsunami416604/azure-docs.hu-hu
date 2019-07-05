---
title: Több főkiszolgálós konfigurálása az Azure Cosmos DB-ben
description: Ismerje meg, hogy több főkiszolgálós konfigurálása az Azure Cosmos DB-ben az alkalmazások.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: 646706eabf1b3a33c3143410f0e922a03e6a8ad6
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565906"
---
# <a name="configure-multi-master-in-your-applications-that-use-azure-cosmos-db"></a>Több főkiszolgálós konfigurálása az Azure Cosmos DB használó alkalmazásokban

Fiók létrehozása után a több írási régióval engedélyezve van, módosítania kell két a ConnectionPolicy az alkalmazás számára a DocumentClient engedélyezése az Azure Cosmos DB a több főkiszolgálós, és többkiszolgálós képességekkel. A ConnectionPolicy belül állítsa igaz értékre, és a régiót, ahol az alkalmazás központi telepítése történik SetCurrentLocation nevét UseMultipleWriteLocations. Ez kitölti a PreferredLocations tulajdonság átadott helyről geo-közelsége alapján. Ha egy új régióban később ad hozzá a fiókot, az alkalmazás nem rendelkezik frissítése vagy újratelepítése, automatikusan észleli a közelebb régióban, és fog automatikus-home, be kell egy regionális esemény következik be.

> [!Note]
> Egyetlen írási régió kezdetben konfigurálva cosmos-fiókok beállítható úgy, hogy több írási régió (azaz több főkiszolgálós) leállás nulla. További tudnivalókért tekintse meg, [több írási régiók konfigurálása](how-to-manage-database-account.md#configure-multiple-write-regions)

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
