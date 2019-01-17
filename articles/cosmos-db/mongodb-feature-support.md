---
title: Használja az Azure Cosmos DB API a MongoDB-funkciók támogatása
description: További információ a MongoDB 3.4 biztosító Azure Cosmos DB MongoDB API-funkciók támogatása.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 08638f0b55c84a6b0e42d3782246ef5b2ce833f3
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359068"
---
# <a name="use-azure-cosmos-dbs-api-for-mongodb-support-for-mongodb-features-and-syntax"></a>Használja az Azure Cosmos DB API a MongoDB támogatására, a MongoDB funkcióihoz és szintaxisához

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Az Azure Cosmos DB API a mongodb-hez a nyílt forráskódú MongoDB-ügyfél valamelyik használatával kommunikálhat [illesztőprogramok](https://docs.mongodb.org/ecosystem/drivers). Az Azure Cosmos DB MongoDB API lehetővé teszi, hogy a meglévő ügyfélillesztők használatát a MongoDB ügyfélillesztők [protokoll fűznie](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

API-val az Azure Cosmos DB MongoDB-hez, a MongoDB jártas, az összes, a nagyvállalati funkciókat, Cosmos DB által biztosított előnyöket élvezheti: [globális terjesztés](distribute-data-globally.md), [automatikus árnyalását](partition-data.md), rendelkezésre állás és a késés garanciákat, automatikus indexelése minden mező, a titkosítás a rest, a biztonsági mentések és még sok más.

## <a name="protocol-support"></a>Protokolltámogatás

Az Azure Cosmos DB MongoDB API a MongoDB-kiszolgáló verziója kompatibilis **3.2-es** alapértelmezés szerint. A támogatott operátorok, valamint a korlátozások és kivételek listája alább található. A MongoDB **3.4**-es verziójának új funkciói és lekérdezési operátorai jelenleg előzetes verzióként érhetők el. Lehet, hogy minden ügyfél-illesztőprogram, amely együttműködik a ezeket a protokollokat tud csatlakozni az Azure Cosmos DB API a mongodb-hez.

A [MongoDB összesítési folyamata](#aggregation-pipeline) jelenleg szintén elérhető egy különálló előzetes verzióban.

## <a name="query-language-support"></a>Lekérdezési nyelvi támogatás

Az Azure Cosmos DB MongoDB API-teljes körű támogatást nyújt a MongoDB lekérdezési nyelvi szerkezeteket. Alább egy részletes listát találhat a jelenleg támogatott műveletekről, operátorokról, fázisokról, parancsokról és beállításokról.

## <a name="database-commands"></a>Adatbázisparancsok

Az Azure Cosmos DB MongoDB API támogatja a következő adatbázis-parancsok:

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

A cosmos DB aggregációs folyamat támogatja a nyilvános előzetes verzióban érhető el. A nyilvános előzetes verzióra való regisztrációval kapcsolatos útmutatásért tekintse meg az [Azure blogot](https://aka.ms/mongodb-aggregation).

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

#### <a name="string-expressions"></a>Karakteres kifejezések
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
$eq | ``` { "Volcano Name": { $eq: "Rainier" } } ``` |  | -
$gt | ``` { "Elevation": { $gt: 4000 } } ``` |  | -
$gte | ``` { "Elevation": { $gte: 4392 } } ``` |  | -
$lt | ``` { "Elevation": { $lt: 5000 } } ``` |  | -
$lte | ``` { "Elevation": { $lte: 5000 } } ``` | | -
$ne | ``` { "Elevation": { $ne: 1 } } ``` |  | -
$in | ``` { "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } } ``` |  | -
$nin | ``` { "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } } ``` | | -
$or | ``` { $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$and | ``` { $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$not | ``` { "Elevation": { $not: { $gt: 5000 } } } ```|  | -
$nor | ``` { $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] } ``` |  | -
$exists | ``` { "Status": { $exists: true } } ```|  | -
$type | ``` { "Status": { $type: "string" } } ```|  | -
$mod | ``` { "Elevation": { $mod: [ 4, 0 ] } } ``` |  | -
$regex | ``` { "Volcano Name": { $regex: "^Rain"} } ```|  | -

### <a name="notes"></a>Megjegyzések

A $regex lekérdezésekben a balra horgonyzott kifejezések engedélyezik az indexben való keresést. Azonban az „i” módosító (kis- és nagybetűk megkülönböztetése nélkül) és az „m” módosító (többsoros) használatakor a gyűjtemény az összes kifejezésben keres.
Ha bele kell foglalni a „$” vagy a „|” karaktert, célszerű két (vagy több) regex lekérdezést létrehozni. Például ha az eredeti lekérdezés a következő: ```find({x:{$regex: /^abc$/})```, a következőképpen kell módosítani: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
Az első rész az indexet fogja használni a keresés ^abc kezdetű dokumentumokra való korlátozásához, a második rész pedig meg fogja feleltetni a pontos bejegyzéseket. A „|” sávoperátor „vagy” függvényként működik – a(z) ```find({x:{$regex: /^abc|^def/})``` lekérdezés megfelelteti azokat a dokumentumokat, amelyekben az „x” mezőben „abc” vagy „def” sztringgel kezdődő értékek találhatók. Az index használatához ajánlott a lekérdezést két külön lekérdezésre bontani, amelyeket az $or operátor kapcsol össze: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

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

Művelet | Példa 
--- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Igen
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Igen
$near | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Igen
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Igen
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Igen
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Igen
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Igen
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Igen
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Igen
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Igen
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Igen

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

A cosmos DB indexeli, amely alapértelmezés szerint az adatbázisba írt dokumentumokban minden mező. Az egyedi indexek biztosítják, hogy egy adott mező ne rendelkezzen duplikált értékekkel a gyűjtemény minden dokumentumában, hasonlóképpen ahhoz, ahogy az alapértelmezett „_id” kulcs is megőrzi az egyediséget. Cosmos DB-ben egyéni indexet hozhat létre az createIndex paranccsal, többek között a "unique" korlátozás.

Egyedi indexek összes Cosmos-fiók mongodb-hez készült Azure Cosmos DB API használatával érhetők el.

## <a name="time-to-live-ttl"></a>Élettartam (TTL)

A cosmos DB támogatja az egy idő-az-élettartam (TTL) alapján történő küldés időbélyegzője a dokumentumot. A TTL is engedélyezhető a gyűjteményekhez a [az Azure portal](https://portal.azure.com).

## <a name="user-and-role-management"></a>Felhasználó- és szerepkörkezelés

A cosmos DB még nem támogatja felhasználók és szerepkörök. Azonban a Cosmos DB támogatja a szerepköralapú hozzáférés-vezérlés (RBAC) és az olvasási és írási és olvasási jelszavakkal/kulcsokkal keresztül elérhető a [az Azure portal](https://portal.azure.com) (kapcsolati karakterlánc oldalán).

## <a name="replication"></a>Replikáció

A cosmos DB támogatja az automatikus, natív replikációt a legalacsonyabb rétegeken. A rendszer az alacsony késésű, globális replikáció elérése érdekében kiterjeszti ezt a logikát. A cosmos DB támogatja a manuális replikációs parancsokat.

## <a name="write-concern"></a>Írási szempont

Egyes alkalmazások egy [írási aggodalomra](https://docs.mongodb.com/manual/reference/write-concern/) amely megadja, hogy az írási művelet során szükséges válaszok száma. Mivel a Cosmos DB a replikációt a háttérben kezeli, alapértelmezés szerint minden írás automatikusan kvórum. Minden írási az Ügyfélkód által megadott szempont a rendszer figyelmen kívül hagyja. További információk: [A rendelkezésre állás és a teljesítmény maximalizálása a konzisztenciaszintek használatával](consistency-levels.md).

## <a name="sharding"></a>Sharding

A cosmos DB támogatja az automatikus, kiszolgálóoldali horizontális skálázást. A cosmos DB nem támogatja a horizontális skálázás manuális parancsokat.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [a Studio 3T használata](mongodb-mongochef.md) az Azure Cosmos DB API a mongodb-hez.
- Ismerje meg, hogyan [Robo 3T használata](mongodb-robomongo.md) az Azure Cosmos DB API a mongodb-hez.
- Ismerkedés a MongoDB [minták](mongodb-samples.md) az Azure Cosmos DB API a mongodb-hez.

<sup>Megjegyzés: Ez a cikk ismerteti az Azure Cosmos DB által biztosított átviteli protokoll kompatibilitás MongoDB adatbázisok funkciója. A Microsoft nem fut a szolgáltatás a MongoDB-adatbázisokat. Az Azure Cosmos DB sincs társítva a MongoDB, Inc.</sup>
