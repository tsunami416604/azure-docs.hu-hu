---
title: Rendszerdokumentum-tulajdonságok elérése Azure Cosmos DB gráfon keresztül
description: Útmutató Cosmos DB rendszerdokumentum tulajdonságainak olvasásához és írásához a Gremlin API-n keresztül
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/10/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 4ed7e67ae0ef027b260d0e0f0407e4e05ed5a8f4
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78898307"
---
# <a name="system-document-properties"></a>Rendszerdokumentum tulajdonságai

Azure Cosmos DB olyan [rendszertulajdonságokkal](https://docs.microsoft.com/rest/api/cosmos-db/databases) rendelkezik, mint a ```_ts```, ```_self```, ```_attachments```, ```_rid```és ```_etag``` minden dokumentumon. Ezenkívül a Gremlin-motor az élekhez az ```inVPartition``` és az ```outVPartition``` tulajdonságokat is hozzárendeli. Alapértelmezés szerint ezek a tulajdonságok a bejáráshoz érhetők el. Azonban a Gremlin bejárásakor lehetséges, hogy a megadott tulajdonságokat vagy azok mindegyikét is tartalmazza.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-tag

Ez a tulajdonság az optimista egyidejűség vezérléséhez szükséges. Ha az alkalmazásnak néhány különálló bejárásokat kell megszüntetnie a műveletet, az eTag tulajdonság használatával elkerülhető az adatvesztés az egyidejű írásokban.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>Élettartam (TTL)

Ha a gyűjteményben engedélyezve van a dokumentum lejárata, és a dokumentumok ```ttl``` tulajdonsággal rendelkeznek, akkor ez a tulajdonság a Gremlin bejárásakor lesz elérhető, mint a normál csúcspont vagy Edge tulajdonság. a ```ProjectionStrategy``` nem szükséges az élettartam-tulajdonság expozíciójának engedélyezéséhez.

Az alábbi bejárással létrehozott csúcs **123 másodperc** múlva automatikusan törölve lesz.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>További lépések
* [A Cosmos DB optimista egyidejűség-kezelése](faq.md#how-does-the-sql-api-provide-concurrency)
* Élettartam [(TTL)](time-to-live.md) Azure Cosmos db
