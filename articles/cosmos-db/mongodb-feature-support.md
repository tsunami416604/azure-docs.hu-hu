---
title: Az Azure Cosmos DB API-ja a MongoDB-hoz (3.2-es verzió) által támogatott funkciók és szintaxis
description: Ismerje meg az Azure Cosmos DB MongoDB-hoz (3.2-es verzió) támogatott szolgáltatásait és szintaxisát.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/16/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 94b65b4e7947bc02b1fdaae90c8f774ec216e7bb
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80981885"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-32-version-supported-features-and-syntax"></a>MongoDB-hez készült Azure Cosmos DB API (3.2-es verzió): támogatott funkciók és szintaxis

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Az Azure Cosmos DB MongoDB-alapú API-jával kommunikálhat a nyílt forráskódú MongoDB [ügyfél-illesztőprogramok](https://docs.mongodb.org/ecosystem/drivers)bármelyikével. Az Azure Cosmos DB MongoDB-hoz való API-ja lehetővé teszi a meglévő ügyfél-illesztőprogramok használatát a MongoDB [vezetékes protokoll](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)betakarásával.

Az Azure Cosmos DB MongoDB-hoz készült API-jának használatával élvezheti a megszokott MongoDB előnyeit a Cosmos DB által biztosított összes vállalati képességgel: [globális terjesztés,](distribute-data-globally.md) [automatikus horizontális,](partition-data.md)rendelkezésre állás és késés, minden mező automatikus indexelése, inaktív titkosítás, biztonsági mentések és még sok más.

> [!NOTE]
> Ez a cikk az Azure Cosmos DB MongoDB 3.2-es API-jához érhető el. A MongoDB 3.6-os verziójáról a [MongoDB 3.6 támogatott szolgáltatásai és szintaxisa című témakörben](mongodb-feature-support-36.md)található.

## <a name="protocol-support"></a>Protokoll támogatás

Az Azure Cosmos DB MongoDB-hoz való API-jának minden új fiókja kompatibilis a MongoDB **3.6-os**verziójával. Ez a cikk a MongoDB 3.2-es verzióját ismerteti. A támogatott operátorok, valamint a korlátozások és kivételek listája alább található. Minden olyan ügyfélillesztőnek, amely ismeri ezeket a protokollokat, tudnia kell kapcsolódnia a MongoDB-hez készült Azure Cosmos DB API-hoz.

## <a name="query-language-support"></a>Lekérdezési nyelv támogatása

Az Azure Cosmos DB MongoDB-hoz készült API-ja átfogó támogatást nyújt a MongoDB lekérdezési nyelvi konstrukcióihoz. Alább egy részletes listát találhat a jelenleg támogatott műveletekről, operátorokról, fázisokról, parancsokról és beállításokról.

## <a name="database-commands"></a>Adatbázisparancsok

Az Azure Cosmos DB MongoDB-hoz való API-ja a következő adatbázis-parancsokat támogatja:

### <a name="query-and-write-operation-commands"></a>Lekérdezési és írási műveletek parancsai

- delete
- find
- findAndModify
- getLastError
- getMore
- insert
- update

### <a name="authentication-commands"></a>Hitelesítési parancsok

- logout
- authenticate
- getnonce

### <a name="administration-commands"></a>Adminisztrációs parancsok

- dropDatabase
- listCollections
- drop
- létrehozás
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>Diagnosztikai parancsok

- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Összesítési folyamat</a>

A Cosmos DB támogatja a MongoDB 3.2 összesítési folyamatát nyilvános előzetes verzióban. A nyilvános előzetes verzióra való regisztrációval kapcsolatos útmutatásért tekintse meg az [Azure blogot](https://azure.microsoft.com/blog/azure-cosmosdb-extends-support-for-mongodb-aggregation-pipeline-unique-indexes-and-more/).

### <a name="aggregation-commands"></a>Összesítési parancsok

- aggregate
- count
- distinct

### <a name="aggregation-stages"></a>Összesítési fázisok

- $project
- $match
- $limit
- $skip
- $unwind
- $group
- $sample
- $sort
- $lookup
- $out
- $count
- $addFields

### <a name="aggregation-expressions"></a>Összesítő kifejezések

#### <a name="boolean-expressions"></a>Logikai kifejezések

- $and
- $or
- $not

#### <a name="set-expressions"></a>Halmazkifejezések

- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Összehasonlító kifejezések

- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Aritmetikai kifejezések

- $abs
- $add
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- $multiply
- $pow
- $sqrt
- $subtract
- $trunc

#### <a name="string-expressions"></a>Sztringkifejezések

- $concat
- $indexOfBytes
- $indexOfCP
- $split
- $strLenBytes
- $strLenCP
- $strcasecmp
- $substr
- $substrBytes
- $substrCP
- $toLower
- $toUpper

#### <a name="array-expressions"></a>Tömbkifejezések

- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- $in

#### <a name="date-expressions"></a>Dátumkifejezések

- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $second
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>Feltételes kifejezések

- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Összesítő gyűjtők

- $sum
- $avg
- $first
- $last
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>Operátorok

Alább a támogatott operátorok láthatók a használatukat bemutató megfelelő példákkal. Tekintse meg a lenti lekérdezésekben használt mintadokumentumot:

```json
{
  "Volcano Name": "Rainier",
  "Country": "United States",
  "Region": "US-Washington",
  "Location": {
    "type": "Point",
    "coordinates": [
      -121.758,
      46.87
    ]
  },
  "Elevation": 4392,
  "Type": "Stratovolcano",
  "Status": "Dendrochronology",
  "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

Művelet | Példa |
--- | --- |
$eq | `{ "Volcano Name": { $eq: "Rainier" } }` |  | -
$gt | `{ "Elevation": { $gt: 4000 } }` |  | -
$gte | `{ "Elevation": { $gte: 4392 } }` |  | -
$lt | `{ "Elevation": { $lt: 5000 } }` |  | -
$lte | `{ "Elevation": { $lte: 5000 } }` | | -
$ne | `{ "Elevation": { $ne: 1 } }` |  | -
$in | `{ "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } }` |  | -
$nin | `{ "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } }` | | -
$or | `{ $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |  | -
$and | `{ $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] }` |  | -
$not | `{ "Elevation": { $not: { $gt: 5000 } } }`|  | -
$nor | `{ $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] }` |  | -
$exists | `{ "Status": { $exists: true } }`|  | -
$type | `{ "Status": { $type: "string" } }`|  | -
$mod | `{ "Elevation": { $mod: [ 4, 0 ] } }` |  | -
$regex | `{ "Volcano Name": { $regex: "^Rain"} }`|  | -

### <a name="notes"></a>Megjegyzések

A $regex lekérdezésekben a balra horgonyzott kifejezések engedélyezik az indexben való keresést. Azonban az „i” módosító (kis- és nagybetűk megkülönböztetése nélkül) és az „m” módosító (többsoros) használatakor a gyűjtemény az összes kifejezésben keres.
Ha bele kell foglalni a „$” vagy a „|” karaktert, célszerű két (vagy több) regex lekérdezést létrehozni.
Például ha az eredeti lekérdezés a következő: ```find({x:{$regex: /^abc$/})```, a következőképpen kell módosítani: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
Az első rész az indexet fogja használni a keresés ^abc kezdetű dokumentumokra való korlátozásához, a második rész pedig meg fogja feleltetni a pontos bejegyzéseket.
A „|” sávoperátor „vagy” függvényként működik – a(z) ```find({x:{$regex: /^abc|^def/})``` lekérdezés megfelelteti azokat a dokumentumokat, amelyekben az „x” mezőben „abc” vagy „def” sztringgel kezdődő értékek találhatók. Az index használatához ajánlott a lekérdezést két külön lekérdezésre bontani, amelyeket az $or operátor kapcsol össze: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="update-operators"></a>Frissítési operátorok

#### <a name="field-update-operators"></a>Mezőfrissítő operátorok

- $inc
- $mul
- $rename
- $setOnInsert
- $set
- $unset
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>Tömbfrissítő operátorok

- $addToSet
- $pop
- $pullAll
- $pull  (Megjegyzés: a $pull feltétellel együtt való használata nem támogatott)
- $pushAll
- $push
- $each
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>Bitenként frissítő operátor

- $bit

### <a name="geospatial-operators"></a>Térinformatikai operátorok

Művelet | Példa | |
--- | --- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Igen |
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Igen |
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Igen |
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Igen |
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Igen |
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Igen |
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Igen |
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Igen |
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Igen |
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Igen |
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Igen |

## <a name="sort-operations"></a>Rendezési műveletek

A művelet `findOneAndUpdate` használatakor a rendezési műveletek egyetlen mezőben támogatottak, de több mezőrendezési műveletei nem támogatottak.

## <a name="additional-operators"></a>További operátorok

Művelet | Példa | Megjegyzések
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` |
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |
$size | ```{ "Location.coordinates": { $size: 2 } }``` |
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` |
$text |  | Nem támogatott. A $regex használható helyette.

## <a name="unsupported-operators"></a>Nem támogatott operátorok

A Cosmos DB nem támogatja a ```$where``` és az ```$eval``` operátort.

### <a name="methods"></a>Metódusok

A következő metódusok támogatottak:

#### <a name="cursor-methods"></a>Kurzormetódusok

Módszer | Példa | Megjegyzések
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | A rendszer nem adja vissza a rendezési kulccsal nem rendelkező dokumentumokat

## <a name="unique-indexes"></a>Egyedi indexek

A Cosmos DB indexeli az adatbázisba alapértelmezés szerint írt dokumentumok minden mezőjét. Az egyedi indexek biztosítják, hogy egy adott mező ne rendelkezzen ismétlődő értékekkel a gyűjtemény összes `_id` dokumentumában, hasonlóan ahhoz, ahogyan az egyediség megmarad az alapértelmezett kulcson. Egyéni indexeket hozhat létre a Cosmos DB-ben a createIndex paranccsal, beleértve az "egyedi" megkötést is.

Egyedi indexek érhetők el az Azure Cosmos DB MongoDB API-jával az összes Cosmos-fiókhoz.

## <a name="time-to-live-ttl"></a>Élettartam (TTL)

A Cosmos DB támogatja az élő (TTL) időt a dokumentum időbélyege alapján. A TTL az [Azure Portalon](https://portal.azure.com)keresztül engedélyezhető a beszedéshez.

## <a name="user-and-role-management"></a>Felhasználó- és szerepkörkezelés

Cosmos DB még nem támogatja a felhasználók és szerepkörök. A Cosmos DB azonban támogatja a szerepköralapú hozzáférés-vezérlést (RBAC), valamint az [Azure Portalon](https://portal.azure.com) (Connection String lap) beszerezhető írási és írásvédett jelszavakat/kulcsokat.

## <a name="replication"></a>Replikáció

Cosmos DB támogatja az automatikus, natív replikáció a legalacsonyabb rétegeken. A rendszer az alacsony késésű, globális replikáció elérése érdekében kiterjeszti ezt a logikát. A Cosmos DB nem támogatja a manuális replikációs parancsokat.

## <a name="write-concern"></a>Írási szempont

Egyes alkalmazások [írási probléma,](https://docs.mongodb.com/manual/reference/write-concern/) amely meghatározza a válaszok száma szükséges egy írási művelet során. Mivel a Cosmos DB a replikációt a háttérben kezeli, alapértelmezés szerint minden írás automatikusan kvórum. Az ügyfélkód által megadott írási problémákat a rendszer figyelmen kívül hagyja. További információk: [A rendelkezésre állás és a teljesítmény maximalizálása a konzisztenciaszintek használatával](consistency-levels.md).

## <a name="sharding"></a>Sharding

Az Azure Cosmos DB támogatja az automatikus, kiszolgálóoldali horizontális skálázást. Automatikusan kezeli a szegmensek létrehozását, elhelyezését és kiegyensúlyozását. Az Azure Cosmos DB nem támogatja a manuális horizontális műveletek et, ami azt jelenti, hogy nem kell meghívnia a parancsokat, például a shardCollection, addShard, balancerStart, moveChunk stb. Csak meg kell adnia a szegmenskulcsot a tárolók létrehozása vagy az adatok lekérdezése közben.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan használhatja a [Studio 3T-t](mongodb-mongochef.md) az Azure Cosmos DB MongoDB-hoz való API-jával.
- Ismerje meg, hogyan használhatja a [Robo 3T-t](mongodb-robomongo.md) az Azure Cosmos DB MongoDB-hoz való API-jával.
- Fedezze fel a [MongoDB-mintákat](mongodb-samples.md) az Azure Cosmos DB MongoDB-hoz kapcsolódó API-jával.

<sup>Megjegyzés: Ez a cikk az Azure Cosmos DB szolgáltatását ismerteti, amely a MongoDB-adatbázisokkal való vezetékes protokollkompatibilitást biztosítja. A Microsoft nem futtat MongoDB adatbázisokat a szolgáltatás biztosításához. Az Azure Cosmos DB nem áll kapcsolatban a MongoDB, Inc. vállalattal.</sup>
