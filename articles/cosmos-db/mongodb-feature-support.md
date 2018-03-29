---
title: Az Azure Cosmos DB által nyújtott szolgáltatások támogatásáról mongodb |} Microsoft Docs
description: További tudnivalók a által nyújtott szolgáltatások támogatásáról az Azure Cosmos DB MongoDB API MongoDB 3.4 biztosít.
services: cosmos-db
author: alekseys
manager: jhubbard
editor: ''
documentationcenter: ''
ms.assetid: 29b6547c-3201-44b6-9e0b-e6f56e473e24
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: alekseys
ms.openlocfilehash: 22ae56d9ebb2ea39acbd9f4536ef9ea5508b9867
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="mongodb-api-support-for-mongodb-features-and-syntax"></a>MongoDB-szolgáltatások és szintaxis MongoDB API támogatása

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Az adatbázis MongoDB API bármely, a nyílt forráskódú MongoDB ügyfél keresztül kommunikálhat [illesztőprogramok](https://docs.mongodb.org/ecosystem/drivers). A MongoDB API lehetővé teszi, hogy a meglévő ügyfél-illesztőprogramok használata a MongoDB való tartva [hozzá kell fűznie protokoll](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Az Azure Cosmos DB MongoDB API használatával a MongoDB API-k által használt, az összes Azure Cosmos DB biztosít a vállalati lehetőségeinek előnyeit élvezheti: [globális terjesztési](distribute-data-globally.md), [automatikus horizontális](partition-data.md), rendelkezésre állást és a késés garanciák, automatikus minden mező, a többi, valamint biztonsági mentés és még sok más titkosítási indexelése.

## <a name="mongodb-query-language-support"></a>MongoDB lekérdezés nyelvi támogatás

Az Azure Cosmos DB MongoDB API átfogó támogatást nyújt a MongoDB lekérdezés nyelvi szerkezetek. Az alábbiakban található a jelenleg támogatott műveletek, a kezelők, a fázisból áll, a parancsok és a beállítások részletes listáját.


## <a name="database-commands"></a>Adatbázis-parancsok

Azure Cosmos-adatbázis a következő adatbázis-parancsokat az összes fiókot a MongoDB API támogatja. 

### <a name="query-and-write-operation-commands"></a>Lekérdezés és az írási művelet parancsok
- törlés
- Keresés
- findAndModify
- getLastError
- getMore
- Beszúrása
- Frissítés

### <a name="authentication-commands"></a>Hitelesítési parancsok
- kijelentkezés
- authenticate
- getnonce

### <a name="administration-commands"></a>Felügyeleti parancsok
- dropDatabase
- listCollections
- legördülő
- létrehozás
- filemd5
- createIndexes
- listIndexes
- dropIndexes
- connectionStatus
- reIndex

### <a name="diagnostics-commands"></a>Diagnosztika parancsok
- buildInfo
- collStats
- dbStats
- hostInfo
- listDatabases
- whatsmyuri

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Összesítési folyamat</a>

Azure Cosmos DB összesítési csővezeték nyilvános előzetes verziójában támogatja. Tekintse meg a [Azure blog](https://aka.ms/mongodb-aggregation) útmutatást a nyilvános előzetes bevezetni.

### <a name="aggregation-commands"></a>Összesítési parancsok
- aggregate
- darab
- Különböző

### <a name="aggregation-stages"></a>Összesítési szakaszból
- $project
- $match
- $limit
- $skip
- $Kifejtési
- $group
- $sample
- $sort
- $lookup
- $out
- $count
- $addFields

### <a name="aggregation-expressions"></a>Összesítő kifejezések

#### <a name="boolean-expressions"></a>Logikai kifejezésen
- $és
- $vagy
- $not

#### <a name="set-expressions"></a>Set kifejezések
- $setEquals
- $setIntersection
- $setUnion
- $setDifference
- $setIsSubset
- $anyElementTrue
- $allElementsTrue

#### <a name="comparison-expressions"></a>Összehasonlítási kifejezésekben
- $cmp
- $eq
- $gt
- $gte
- $lt
- $lte
- $ne

#### <a name="arithmetic-expressions"></a>Aritmetikai kifejezésekben
- $abs
- $hozzáadása
- $ceil
- $divide
- $exp
- $floor
- $ln
- $log
- $log10
- $mod
- a szorzási $
- $pow
- $sqrt
- $kivonása
- $trunc

#### <a name="string-expressions"></a>Karakterlánc kifejezése
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

#### <a name="array-expressions"></a>Tömböt létrehozó kifejezések használata
- $arrayElemAt
- $concatArrays
- $filter
- $indexOfArray
- $isArray
- $range
- $reverseArray
- $size
- $slice
- a $

#### <a name="date-expressions"></a>Dátum kifejezések
- $dayOfYear
- $dayOfMonth
- $dayOfWeek
- $year
- $month
- $week
- $hour
- $minute
- $második
- $millisecond
- $isoDayOfWeek
- $isoWeek

#### <a name="conditional-expressions"></a>A feltételes kifejezések
- $cond
- $ifNull

## <a name="aggregation-accumulators"></a>Összesítési akkumulátorok
- $sum
- $avg
- $első
- $utolsó
- $max
- $min
- $push
- $addToSet

## <a name="operators"></a>Operátorok

Következő operátorok használatukat megfelelő példák használata támogatott. Vegye figyelembe a minta dokumentum szerepel az alábbi lekérdezéseket:

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

Operátor | Példa |
--- | --- |
$eq | ``` { "Volcano Name": { $eq: "Rainier" } } ``` |  | -
$gt | ``` { "Elevation": { $gt: 4000 } } ``` |  | -
$gte | ``` { "Elevation": { $gte: 4392 } } ``` |  | -
$lt | ``` { "Elevation": { $lt: 5000 } } ``` |  | -
$lte | ``` { "Elevation": { $lte: 5000 } } ``` | | -
$ne | ``` { "Elevation": { $ne: 1 } } ``` |  | -
a $ | ``` { "Volcano Name": { $in: ["St. Helens", "Rainier", "Glacier Peak"] } } ``` |  | -
$nin | ``` { "Volcano Name": { $nin: ["Lassen Peak", "Hood", "Baker"] } } ``` | | -
$vagy | ``` { $or: [ { Elevation: { $lt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$és | ``` { $and: [ { Elevation: { $gt: 4000 } }, { "Volcano Name": "Rainier" } ] } ``` |  | -
$not | ``` { "Elevation": { $not: { $gt: 5000 } } } ```|  | -
$sem | ``` { $nor: [ { "Elevation": { $lt: 4000 } }, { "Volcano Name": "Baker" } ] } ``` |  | -
$létezik | ``` { "Status": { $exists: true } } ```|  | -
$type | ``` { "Status": { $type: "string" } } ```|  | -
$mod | ``` { "Elevation": { $mod: [ 4, 0 ] } } ``` |  | -
$regex | ``` { "Volcano Name": { $regex: "^Rain"} } ```|  | -

### <a name="notes"></a>Megjegyzések

$Regex lekérdezésekben balra rögzített kifejezések index keresés engedélyezése. Azonban használja az "i" módosító (kis) és vagyok "módosító (többsoros) a gyűjtemény vizsgálat hatására az összes kifejezésében.
Ha egy szükséges belefoglalni a "$" vagy "|}", legjobb, ha két (vagy több) regex lekérdezések létrehozása. Ha például adott a következő eredeti lekérdezés: ```find({x:{$regex: /^abc$/})```, kell módosítani az alábbiak szerint: ```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.
Az első részt az index használatával korlátozza a keresési verziótól kezdve a dokumentumok ^ abc, a második rész fog egyezni a pontos bejegyzéseket. A sáv operátor "|}" úgy működik, mint egy "vagy" függvény – a lekérdezés ```find({x:{$regex: /^abc|^def/})``` megegyezik a dokumentumok, mely mezőben "x" értéket tartalmaz a "abc" vagy "def" karakterlánccal kezdődik. Az index használatára, javasoljuk a lekérdezés felosztása két különböző lekérdezéseket a $vagy üzemeltető tartományhoz való: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="update-operators"></a>Frissítés operátorok

#### <a name="field-update-operators"></a>A mező frissítés operátorok
- $inc
- $mul
- $rename
- $setOnInsert
- $set
- vonja $
- $min
- $max
- $currentDate

#### <a name="array-update-operators"></a>A tömb frissítés operátorok
- $addToSet
- $pop
- $pullAll
- $pull (Megjegyzés: a feltétellel $pull nem támogatott)
- $pushAll
- $push
- minden egyes $
- $slice
- $sort
- $position

#### <a name="bitwise-update-operator"></a>Bitenkénti frissítés operátor
- $bit

### <a name="geospatial-operators"></a>A földrajzi operátorok

Operátor | Példa 
--- | --- |
$geoWithin | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Igen
$geoIntersects |  ```{ "Location.coordinates": { $geoIntersects: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Igen
kis hatótávolságú $ | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Igen
$nearSphere | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Igen
$geometry | ```{ "Location.coordinates": { $geoWithin: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Igen
$minDistance | ```{ "Location.coordinates": { $nearSphere : { $geometry: {type: "Point", coordinates: [ -121, 46 ]}, $minDistance: 1000, $maxDistance: 1000000 } } }``` | Igen
$maxDistance | ```{ "Location.coordinates": { $nearSphere : [ -121, 46  ], $maxDistance: 0.50 } }``` | Igen
$center | ```{ "Location.coordinates": { $geoWithin: { $center: [ [-121, 46], 1 ] } } }``` | Igen
$centerSphere | ```{ "Location.coordinates": { $geoWithin: { $centerSphere: [ [ -121, 46 ], 5 ] } } }``` | Igen
$box | ```{ "Location.coordinates": { $geoWithin: { $box:  [ [ 0, 0 ], [ -122, 47 ] ] } } }``` | Igen
$polygon | ```{ "Location.coordinates": { $near: { $geometry: { type: "Polygon", coordinates: [ [ [ -121.9, 46.7 ], [ -121.5, 46.7 ], [ -121.5, 46.9 ], [ -121.9, 46.9 ], [ -121.9, 46.7 ] ] ] } } } }``` | Igen

## <a name="additional-operators"></a>További operátorok

Operátor | Példa | Megjegyzések 
--- | --- | --- |
$all | ```{ "Location.coordinates": { $all: [-121.758, 46.87] } }``` | 
$elemMatch | ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } } }``` |  
$size | ```{ "Location.coordinates": { $size: 2 } }``` | 
$comment |  ```{ "Location.coordinates": { $elemMatch: {  $lt: 0 } }, $comment: "Negative values"}``` | 
$text |  | Nem támogatott. Helyette a $regex 

### <a name="methods"></a>Metódusok

Következő módszer használható:

#### <a name="cursor-methods"></a>Kurzor módszerek

Módszer | Példa | Megjegyzések 
--- | --- | --- |
cursor.sort() | ```cursor.sort({ "Elevation": -1 })``` | Dokumentumok rendezési kulcs nélkül nem adja vissza

## <a name="unique-indexes"></a>Egyedi indexek

Azure Cosmos-adatbázis-dokumentumokban alapértelmezés szerint az adatbázisba írt minden mezőnek indexeket. Egyedi indexek győződjön meg arról, hogy egy adott mező egy gyűjtemény összes dokumentum nem tartalmaz ismétlődő értékeket, hasonló módon egyediségét megmaradnak az alapértelmezett "_id" kulcshoz. Most már Azure Cosmos DB egyéni indexek is létrehozhat, ha a paranccsal createIndex, beleértve az "egyedi" korlátozás.

Egyedi indexek érhetők el az összes MongoDB API-fiók.

## <a name="time-to-live-ttl"></a>Idő élettartamát (TTL)

Azure Cosmos DB relatív-élettartama (TTL) alapján a dokumentum az időbélyeg támogatja. Élettartam MongoDB API gyűjtemények használatával engedélyezhető a [Azure-portálon](https://portal.azure.com).

## <a name="user-and-role-management"></a>Felhasználó-és szerepkörkezelés

Azure Cosmos-adatbázis még nem támogatja a felhasználók és szerepkörök. Az Azure Cosmos DB támogatja a szerepköralapú hozzáférés-vezérlést (RBAC) és az olvasási és írási és olvasási a jelszavakkal/kulcsokkal keresztül elérhető a [Azure-portálon](https://portal.azure.com) (csatlakozási karakterlánc oldal).

## <a name="replication"></a>Replikáció

Azure Cosmos-adatbázis automatikus, natív replikáció a lehető legalacsonyabb rétegek támogatja. A működési elvet ki van bővítve kimenő alacsony késésű, globális replikációs eléréséhez. Azure Cosmos-adatbázis nem támogatja a manuális replikációs parancsokat.

## <a name="sharding"></a>Sharding

Azure Cosmos-adatbázis automatikus, kiszolgálóoldali horizontális támogatja. Azure Cosmos-adatbázis nem támogatja a manuális horizontális parancsokat.

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan [Studio 3T használja](mongodb-mongochef.md) egy API-t a MongoDB-adatbázist.
- Megtudhatja, hogyan [Robo 3T használja](mongodb-robomongo.md) egy API-t a MongoDB-adatbázist.
- Azure Cosmos DB megismerkedhet a mongodb-protokolltámogatással rendelkező [minták](mongodb-samples.md).
