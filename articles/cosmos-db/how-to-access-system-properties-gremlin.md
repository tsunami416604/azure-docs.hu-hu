---
title: A rendszerdokumentum-tulajdonságok elérése az Azure Cosmos DB Graph segítségével
description: Ismerje meg, hogyan olvasható és írt Cosmos DB rendszer dokumentum tulajdonságait a Gremlin API-n keresztül
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/10/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 4ed7e67ae0ef027b260d0e0f0407e4e05ed5a8f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898307"
---
# <a name="system-document-properties"></a>Rendszerdokumentum tulajdonságai

Az Azure Cosmos DB [rendszertulajdonságokkal](https://docs.microsoft.com/rest/api/cosmos-db/databases) rendelkezik, ```_ts```például , ```_self```, ```_attachments```, ```_rid```és ```_etag``` minden dokumentumon. Ezenkívül a Gremlin-motor az élekhez az ```inVPartition``` és az ```outVPartition``` tulajdonságokat is hozzárendeli. Alapértelmezés szerint ezek a tulajdonságok bejárásra is rendelkezésre állnak. Azonban lehetséges, hogy adott tulajdonságokat, vagy az összes, a Gremlin bejárás.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-címke

Ez a tulajdonság az optimista egyidejűség vezérléséhez szükséges. Ha az alkalmazásnak néhány különálló bejárásra kell bontania a műveletet, az eTag tulajdonság galisszal elkerülheti az adatvesztést az egyidejű írások során.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>Élettartam (TTL)

Ha a gyűjteményben engedélyezve ```ttl``` van a dokumentum lejárata, és a dokumentumokon tulajdonság van beállítva, akkor ez a tulajdonság a Gremlin-bejárásban rendszeres csúcspontként vagy peremtulajdonságként lesz elérhető. ```ProjectionStrategy```nem szükséges az élő vagyoni kitettség engedélyezéséhez.

Az alábbi bejárással létrehozott csúcs **123 másodperc** múlva automatikusan törölve lesz.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>További lépések
* [A Cosmos DB optimista egyidejűség-kezelése](faq.md#how-does-the-sql-api-provide-concurrency)
* [Az élő adásideje (TTL)](time-to-live.md) az Azure Cosmos DB-ben
