---
title: Rendszerdokumentum-tulajdonságok elérése Azure Cosmos DB gráfon keresztül
description: Útmutató Cosmos DB rendszerdokumentum tulajdonságainak olvasásához és írásához a Gremlin API-n keresztül
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/10/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: e762674936ab2fbdf198ca67f79acfa545127f02
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755070"
---
# <a name="system-document-properties"></a>Rendszerdokumentum tulajdonságai

Azure Cosmos DB olyan [rendszertulajdonságokkal](https://docs.microsoft.com/rest/api/cosmos-db/databases) rendelkezik, mint a ```_ts```, ```_self```, ```_attachments```, ```_rid``` és ```_etag``` minden dokumentumon. Ezenkívül a Gremlin-motor az élekhez az ```inVPartition``` és az ```outVPartition``` tulajdonságokat is hozzárendeli. Alapértelmezés szerint ezek a tulajdonságok a bejáráshoz érhetők el. Azonban a Gremlin bejárásakor lehetséges, hogy a megadott tulajdonságokat vagy azok mindegyikét is tartalmazza.

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

## <a name="next-steps"></a>Következő lépések
* [A Cosmos DB optimista egyidejűség-kezelése](faq.md#how-does-the-sql-api-provide-concurrency)
* Élettartam [(TTL)](time-to-live.md) Azure Cosmos db
