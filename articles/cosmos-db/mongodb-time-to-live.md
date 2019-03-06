---
title: A MongoDB dokumentumra vonatkozó TTL-funkciók az Azure Cosmos DB-ben
description: Ismerje meg, az élő értékét az Azure Cosmos DB MongoDB API használatával automatikusan kiürítése őket a rendszer bizonyos idő után dokumentumok idő beállítása.
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.devlang: javascript
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: cd6cb68014eea00077328f39c2c9bf0a1f7fb679
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436289"
---
# <a name="expire-data-with-azure-cosmos-dbs-api-for-mongodb"></a>Hamarosan lejár az adatokat az Azure Cosmos DB API a mongodb-hez

Az élettartam- (TTL-) funkció lehetővé teszi, hogy az adatbázis adatai automatikusan elévüljenek. Az Azure Cosmos DB MongoDB API-élettartam alapképességek Cosmos DB használja. Két mód támogatott: alapértelmezett TTL-érték beállítása az egész gyűjteményhez és egyedi TTL-érték megadása az egyes dokumentumokhoz. A TTL-indexek és a dokumentumra vonatkozó TTL szabályozó logikát a mongodb a Cosmos DB API-értékei a [egyezik a kérésben Cosmos DB](../cosmos-db/mongodb-indexing.md).

## <a name="ttl-indexes"></a>TTL-indexek
A TTL gyűjteményben történő univerzális engedélyezéséhez [TTL-index (élettartamindex)](../cosmos-db/mongodb-indexing.md) létrehozása szükséges. A TTL-index a _ts mező indexe expireAfterSeconds értékkel.

Példa:
```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : true,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

A fenti példában lévő parancs létrehoz egy TTL-funkcióval rendelkező indexet. Miután létrejött az index, az adatbázis automatikusan töröl minden olyan dokumentumot a gyűjteményből, amely nem lett módosítva az elmúlt 10 másodpercben. 

> [!NOTE]
> A **_ts** egy Cosmos DB-specifikus mező, amely nem érhető el a MongoDB-ügyfelekből. Ez egy fenntartott (rendszer-) tulajdonság, amely tartalmazza a dokumentum utolsó módosításának időbélyegét.
>
    
Egy további C#-példa: 

```csharp
var options = new CreateIndexOptions {ExpireAfter = TimeSpan.FromSeconds(10)}; 
var field = new StringFieldDefinition<BsonDocument>("_ts"); 
var indexDefinition = new IndexKeysDefinitionBuilder<BsonDocument>().Ascending(field); 
await collection.Indexes.CreateOneAsync(indexDefinition, options); 
``` 

## <a name="set-time-to-live-value-for-a-document"></a>Dokumentum élettartamértékének megadása 
A dokumentumonkénti TTL-értékek is támogatottak. A dokumentumnak tartalmaznia kell egy gyökérszintű „ttl” tulajdonságot (kisbetűs), és a gyűjteményhez létre kell hozni egy TTL-indexet a fent leírtak alapján. A dokumentumhoz beállított TTL-értékek felülbírálják a gyűjtemény TTL-értékét.

Az élettartamnak int32 típusú értéknek kell lennie. Vagy lehet egy int32-be illő int64 vagy egy tizedesjeggyel nem rendelkező dupla, amely illik az int32-be. Az ezeknek a specifikációknak nem megfelelő TTL-tulajdonságértékek is engedélyezettek, de nem minősülnek értelmezhető TTL-dokumentumértéknek.

A dokumentum TTL-értékének megadása nem kötelező, TTL-értékkel nem rendelkező dokumentumok is beszúrhatók a gyűjteménybe.  Ebben az esetben a gyűjtemény TTL-értéke lesz figyelembe véve. 

A következő dokumentumok érvényes TTL-értékekkel rendelkeznek. A dokumentumok egészül ki, ha a dokumentum TTL-értékek felülbírálják a gyűjtemény élettartam-értékek. Tehát a dokumentumok 20 másodperc után el lesznek távolítva.   

```JavaScript 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: 20.0}) 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberInt(20)}) 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberLong(20)}) 
```

A következő dokumentumok TTL-értéke érvénytelen. A dokumentumok be lesznek szúrva, de a dokumentumok TTL-értéke nem lesz figyelembe véve. Tehát a dokumentumok 10 másodperc után el lesznek távolítva a gyűjtemény TTL-értéke miatt. 

```JavaScript 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: 20.5}) //TTL value contains non-zero decimal part. 
globaldb:PRIMARY> db.coll.insert({id:1, location: "Paris", ttl: NumberLong(2147483649)}) //TTL value is greater than Int32.MaxValue (2,147,483,648). 
``` 

## <a name="how-to-activate-the-per-document-ttl-feature"></a>A dokumentumonkénti TTL-funkció aktiválása

A dokumentumra vonatkozó TTL szolgáltatás is aktiválni az Azure Cosmos DB API a mongodb-hez.

![Képernyőkép a portálon a Per-dokumentum TTL szolgáltatás aktiválása](./media/mongodb-ttl/mongodb_portal_ttl.png) 

## <a name="next-steps"></a>További lépések
* [Adatok az Azure Cosmos DB automatikusan az élettartam elévülése](../cosmos-db/time-to-live.md)
* [A mongodb-hez készült Azure Cosmos DB API-val konfigurált Cosmos database indexelése](../cosmos-db/mongodb-indexing.md)
