---
title: Rendszerdokumentum-tulajdonságok elérése Azure Cosmos DB gráfon keresztül
description: Útmutató Cosmos DB rendszerdokumentum tulajdonságainak olvasásához és írásához a Gremlin API-n keresztül
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: 4354d37a32bde006a9bee70c39df1fee9b269365
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910655"
---
# <a name="system-document-properties"></a>Rendszerdokumentum tulajdonságai

Azure Cosmos db olyan [rendszertulajdonságokkal](https://docs.microsoft.com/rest/api/cosmos-db/databases) ```_ts``` ```_self``` ```_attachments```rendelkezik, mint a,, ```_etag``` , és minden dokumentumon. ```_rid``` Ezenkívül a Gremlin-motor az élekhez az ```inVPartition``` és az ```outVPartition``` tulajdonságokat is hozzárendeli. Alapértelmezés szerint ezek a tulajdonságok a bejáráshoz érhetők el. Azonban a Gremlin bejárásakor lehetséges, hogy a megadott tulajdonságokat vagy azok mindegyikét is tartalmazza.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-tag

Ez a tulajdonság az optimista egyidejűség vezérléséhez szükséges. Ha az alkalmazásnak néhány különálló bejárásokat kell megszüntetnie a műveletet, az eTag tulajdonság használatával elkerülhető az adatvesztés az egyidejű írásokban.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>Élettartam (TTL)

Ha a gyűjteményben engedélyezve van a dokumentum lejárata, és a dokumentumok tulajdonsága be van ```ttl``` állítva, akkor ez a tulajdonság a Gremlin bejárásakor lesz elérhető a normál csúcspont vagy Edge tulajdonságként. ```ProjectionStrategy```nincs szükség az élettartam-tulajdonság expozíciójának engedélyezésére.

Az alábbi bejárással létrehozott csúcs **123 másodperc** múlva automatikusan törölve lesz.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>További lépések
* [A Cosmos DB optimista egyidejűség-kezelése](faq.md#how-does-the-sql-api-provide-concurrency)
* Élettartam [(TTL)](time-to-live.md) Azure Cosmos db
