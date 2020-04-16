---
title: Az Azure Cosmos DB API-ja a MongoDB-hoz (3.6-os verzió) által támogatott funkciók és szintaxis
description: Ismerje meg az Azure Cosmos DB MongoDB-hoz (3.6-os verzió) támogatott szolgáltatásait és szintaxisát.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 01/15/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 5df21b2c1926803a65eca911c66b059f36ee18aa
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393622"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>MongoDB-hez készült Azure Cosmos DB API (3.6-os verzió): támogatott funkciók és szintaxis

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. Az Azure Cosmos DB MongoDB-alapú API-jával kommunikálhat a nyílt forráskódú MongoDB [ügyfél-illesztőprogramok](https://docs.mongodb.org/ecosystem/drivers)bármelyikével. Az Azure Cosmos DB MongoDB-hoz való API-ja lehetővé teszi a meglévő ügyfél-illesztőprogramok használatát a MongoDB [vezetékes protokoll](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)betakarásával.

Az Azure Cosmos DB MongoDB-hoz készült API-jának használatával élvezheti a megszokott MongoDB előnyeit a Cosmos DB által biztosított összes vállalati képességgel: [globális terjesztés,](distribute-data-globally.md) [automatikus horizontális,](partition-data.md)rendelkezésre állás és késési garanciák, inaktív titkosítás, biztonsági mentések és még sok más.

## <a name="protocol-support"></a>Protokoll támogatás

Az Azure Cosmos DB MongoDB API-ja alapértelmezés szerint kompatibilis a MongoDB **3.6-os** verziójával az új fiókok esetében. A támogatott operátorok, valamint a korlátozások és kivételek listája alább található. Minden olyan ügyfélillesztőnek, amely ismeri ezeket a protokollokat, tudnia kell kapcsolódnia a MongoDB-hez készült Azure Cosmos DB API-hoz. Vegye figyelembe, hogy az Azure Cosmos DB MongoDB-fiókokhoz való API-jának használatakor a fiókok 3.6-os verziója a végpont formátumú, `*.mongo.cosmos.azure.com` míg a fiókok 3.2-es verziója a végpont formátumú. `*.documents.azure.com`

## <a name="query-language-support"></a>Lekérdezési nyelv támogatása

Az Azure Cosmos DB MongoDB-hoz készült API-ja átfogó támogatást nyújt a MongoDB lekérdezési nyelvi konstrukcióihoz. Az alábbiakban részletesen megtalálhatja a jelenleg támogatott műveletek, operátorok, szakaszok, parancsok és beállítások részletes listáját.

## <a name="database-commands"></a>Adatbázisparancsok

Az Azure Cosmos DB MongoDB-hoz való API-ja a következő adatbázis-parancsokat támogatja:

### <a name="query-and-write-operation-commands"></a>Lekérdezési és írási műveletek parancsai

|Parancs  |Támogatott |
|---------|---------|
|delete | Igen |
|find | Igen     |
|findAndModify | Igen  |
|getLastError|   Igen |
|getMore  |  Igen  |
|getPrevError | Nem  |
|insert  |   Igen  |
|parallelCollectionScan  | Igen   |
|resetError (hiba) |    Nem  |
|update  |   Igen  |
|[Streamek módosítása](mongodb-change-streams.md)  |  Igen  |
|GridFS |   Igen  |

### <a name="authentication-commands"></a>Hitelesítési parancsok

|Parancs  |Támogatott |
|---------|---------|
|authenticate    |   Igen      |
|logout    |      Igen   |
|getnonce   |    Igen     |


### <a name="administration-commands"></a>Adminisztrációs parancsok

|Parancs  |Támogatott |
|---------|---------|
|Maximált gyűjtemények   |   Nem      |
|cloneCollectionAsCapped     |   Nem      |
|collMod     |   Nem      |
|collMod: expireAfterSeconds   |   Nem      |
|convertToCapped   |  Nem       |
|copydb     |  Nem       |
|létrehozás   |    Igen     |
|createIndexes     |  Igen       |
|currentOp (jelenlegi     |  Igen       |
|drop     |   Igen      |
|dropDatabase     |  Igen       |
|dropIndexes     |   Igen      |
|filemd5    |   Igen      |
|killCursors    |  Igen       |
|killOp (gyilkos)     |   Nem      |
|listCollections     |  Igen       |
|listDatabases     |  Igen       |
|listIndexes     |  Igen       |
|reIndex     |    Igen     |
|átnevezésGyűjtemény     |    Nem     |
|connectionStatus    |     Nem    |

### <a name="diagnostics-commands"></a>Diagnosztikai parancsok

|Parancs  |Támogatott |
|---------|---------|
|buildInfo         |   Igen      |
|collStats    |  Igen       |
|connPoolStats     |  Nem       |
|connectionStatus     |  Nem       |
|dataSize (dataSize)     |   Nem      |
|dbHash között    |    Nem     |
|dbStats     |   Igen      |
|Magyarázni     |   Igen      |
|magyarázza: executionStats     |   Igen      |
|funkciók     |    Nem     |
|hostInfo     |   Nem      |
|listDatabases         |   Igen      |
|listParancsok     |  Nem       |
|Profiler     |  Nem       |
|kiszolgáló állapota     |  Nem       |
|felül     |    Nem     |
|whatsmyuri     |   Igen      |

<a name="aggregation-pipeline"/>

## <a name="aggregation-pipelinea"></a>Összesítési folyamat</a>

### <a name="aggregation-commands"></a>Összesítési parancsok

|Parancs  |Támogatott |
|---------|---------|
|aggregate |   Igen  |
|count     |   Igen  |
|distinct  | Igen |
|mapReduce | Nem |

### <a name="aggregation-stages"></a>Összesítési fázisok

|Parancs  |Támogatott |
|---------|---------|
|$collStats    |Nem|
|$project    |Igen|
|$match    |Igen|
|$redact|    Igen|
|$limit    |Igen|
|$skip    |Igen|
|$unwind|    Igen|
|$group    |    Igen|
|$sample|        Igen|
|$sort    |Igen|
|$geoNear|    Nem|
|$lookup    |    Igen|
|$out        |Igen|
|$indexStats|        Nem|
|$facet    |Nem|
|$bucket|    Nem|
|$bucketAuto|    Nem|
|$sortByCount|    Igen|
|$addFields    |Igen|
|$replaceRoot|    Igen|
|$count    |Igen|
|$currentOp|    Nem|
|$listLocalSessions    |Nem|
|$listSessions    |Nem|
|$graphLookup    |Nem|

### <a name="boolean-expressions"></a>Logikai kifejezések

|Parancs  |Támogatott |
|---------|---------|
|$and| Igen|
|$or|Igen|
|$not|Igen|

### <a name="set-expressions"></a>Halmazkifejezések

|Parancs  |Támogatott |
|---------|---------|
| $setEquals | Igen|
|$setIntersection|Igen|
| $setUnion|Igen|
| $setDifference|Igen|
| $setIsSubset|Igen|
| $anyElementTrue|Igen|
| $allElementsTrue|Igen|

### <a name="comparison-expressions"></a>Összehasonlító kifejezések

|Parancs  |Támogatott |
|---------|---------|
|$cmp     |  Igen       |
|$eq|    Igen| 
|$gt |    Igen| 
|$gte|    Igen| 
|$lt    |Igen|
|$lte|    Igen| 
|$ne    |    Igen| 
|$in    |    Igen| 
|$nin    |    Igen| 

### <a name="arithmetic-expressions"></a>Aritmetikai kifejezések

|Parancs  |Támogatott |
|---------|---------|
|$abs |  Igen       |
| $add |  Igen       |
| $ceil |  Igen       |
| $divide |  Igen       |
| $exp |  Igen       |
| $floor |  Igen       |
| $ln |  Igen       |
| $log |  Igen       |
| $log10 |  Igen       |
| $mod |  Igen       |
| $multiply |  Igen       |
| $pow |  Igen       |
| $sqrt |  Igen       |
| $subtract |  Igen       |
| $trunc |  Igen       |

### <a name="string-expressions"></a>Sztringkifejezések

|Parancs  |Támogatott |
|---------|---------|
|$concat |  Igen       |
| $indexOfBytes|  Igen       |
| $indexOfCP|  Igen       |
| $split|  Igen       |
| $strLenBytes|  Igen       |
| $strLenCP|  Igen       |
| $strcasecmp|  Igen       |
| $substr|  Igen       |
| $substrBytes|  Igen       |
| $substrCP|  Igen       |
| $toLower|  Igen       |
| $toUpper|  Igen       |

### <a name="text-search-operator"></a>Szövegkereső operátor

|Parancs  |Támogatott |
|---------|---------|
| $meta | Nem|

### <a name="array-expressions"></a>Tömbkifejezések

|Parancs  |Támogatott |
|---------|---------|
|$arrayElemAt    |    Igen|
|$arrayToObject|    Igen|
|$concatArrays    |    Igen|
|$filter    |    Igen|
|$indexOfArray    |Igen|
|$isArray    |    Igen|
|$objectToArray    |Igen|
|$range    |Igen|
|$reverseArray    |    Igen|
|$reduce|    Igen|
|$size    |    Igen|
|$slice    |    Igen|
|$zip    |    Igen|
|$in    |    Igen|

### <a name="variable-operators"></a>Változó operátorok

|Parancs  |Támogatott |
|---------|---------|
|$map    |Nem|
|$let    |Igen|

### <a name="system-variables"></a>Rendszerváltozók

|Parancs  |Támogatott |
|---------|---------|
|$$CURRENT|    Igen|
|$$DESCEND|        Igen|
|$$KEEP        |Igen|
|$$PRUNE    |    Igen|
|$$REMOVE    |Igen|
|$$ROOT        |Igen|

### <a name="literal-operator"></a>Literális operátor

|Parancs  |Támogatott |
|---------|---------|
|$literal    |Igen|

### <a name="date-expressions"></a>Dátumkifejezések

|Parancs  |Támogatott |
|---------|---------|
|$dayOfYear    |Igen    |
|$dayOfMonth|    Igen    |
|$dayOfWeek    |Igen    |
|$year    |Igen    |
|$month    |Igen|    
|$week    |Igen    |
|$hour    |Igen    |
|$minute|    Igen|    
|$second    |Igen    |
|$millisecond|    Igen|    
|$dateToString    |Igen    |
|$isoDayOfWeek    |Igen    |
|$isoWeek    |Igen    |
|$dateFromParts|    Nem|    
|$dateToParts    |Nem    |
|$dateFromString|    Nem|
|$isoWeekYear    |Igen    |

### <a name="conditional-expressions"></a>Feltételes kifejezések

|Parancs  |Támogatott |
|---------|---------|
| $cond| Igen|
| $ifNull| Igen|
| $switch |Igen|

### <a name="data-type-operator"></a>Adattípus operátor

|Parancs  |Támogatott |
|---------|---------|
| $type| Igen|

### <a name="accumulator-expressions"></a>Gyűjtőkifejezések

|Parancs  |Támogatott |
|---------|---------|
|$sum    |Igen    |
|$avg    |Igen    |
|$first|    Igen|
|$last    |Igen    |
|$max    |Igen    |
|$min    |Igen    |
|$push|    Igen|
|$addToSet|    Igen|
|$stdDevPop|    Nem    |
|$stdDevSamp|    Nem|

### <a name="merge-operator"></a>Egyesítési operátor

|Parancs  |Támogatott |
|---------|---------|
| $mergeObjects | Igen|

## <a name="data-types"></a>Adattípusok

|Parancs  |Támogatott |
|---------|---------|
|Double    |Igen    |
|Sztring    |Igen    |
|Objektum    |Igen    |
|Tömb    |Igen    |
|Bináris adatok    |Igen|    
|ObjectId    |Igen    |
|Logikai    |Igen    |
|Dátum    |Igen    |
|Null    |Igen    |
|32 bites egész szám (int)    |Igen    |
|Időbélyeg    |Igen    |
|64 bites egész (hosszú)    |Igen    |
|MinKey között    |Igen    |
|MaxKey (MaxKey)    |Igen    |
|Decimális 128    |Igen|    
|Reguláris kifejezés    |Igen|
|JavaScript    |Igen|
|JavaScript (hatókörrel)|    Igen    |
|Meghatározatlan    |Igen    |

## <a name="indexes-and-index-properties"></a>Indexek és indextulajdonságok

### <a name="indexes"></a>Indexek

|Parancs  |Támogatott |
|---------|---------|
|Egymezős index    |Igen    |
|Összetett index    |Igen    |
|Többkulcsos index    |Igen    |
|Szöveg tárgymutatója    |Nem|
|2dsphere (2dsphere)    |Igen    |
|2d index    |Nem    |
|Kivonatolt index    | Igen|

### <a name="index-properties"></a>Index tulajdonságai

|Parancs  |Támogatott |
|---------|---------|
|Élettartam|    Igen    |
|Egyedi    |Igen|
|Részleges|    Nem|
|A kis- és nagybetűk megkülönböztetése    |Nem|
|Ritka    |Nem |
|Háttér|    Igen |

## <a name="operators"></a>Operátorok

### <a name="logical-operators"></a>Logikai operátorok

|Parancs  |Támogatott |
|---------|---------|
|$or    |    Igen|
|$and    |    Igen|
|$not    |    Igen|
|$nor    |    Igen| 

### <a name="element-operators"></a>Elemoperátorok

|Parancs  |Támogatott |
|---------|---------|
|$exists|    Igen|
|$type    |    Igen|

### <a name="evaluation-query-operators"></a>Kiértékelési lekérdezés operátorai

|Parancs  |Támogatott |
|---------|---------|
|$expr    |    Nem|
|$jsonSchema    |    Nem|
|$mod    |    Igen|
|$regex |    Igen|
|$text    | Nem (Nem támogatott. Használja inkább $regex.)| 
|$where    |Nem| 

A $regex lekérdezésekben a bal oldali rögzített kifejezések lehetővé teszik az indexkeresést. Azonban az „i” módosító (kis- és nagybetűk megkülönböztetése nélkül) és az „m” módosító (többsoros) használatakor a gyűjtemény az összes kifejezésben keres.

Ha bele kell foglalni a „$” vagy a „|” karaktert, célszerű két (vagy több) regex lekérdezést létrehozni. A következő eredeti lekérdezés ```find({x:{$regex: /^abc$/})```esetén például a következőképpen kell módosítani:

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.

Az első rész az indexet fogja használni a keresés ^abc kezdetű dokumentumokra való korlátozásához, a második rész pedig meg fogja feleltetni a pontos bejegyzéseket. A „|” sávoperátor „vagy” függvényként működik – a(z) ```find({x:{$regex: /^abc|^def/})``` lekérdezés megfelelteti azokat a dokumentumokat, amelyekben az „x” mezőben „abc” vagy „def” sztringgel kezdődő értékek találhatók. Az index használatához ajánlott a lekérdezést két külön lekérdezésre bontani, amelyeket az $or operátor kapcsol össze: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="array-operators"></a>Tömboperátorok

|Parancs  |Támogatott | 
|---------|---------|
| $all | Igen| 
| $elemMatch | Igen| 
| $size | Igen | 

### <a name="comment-operator"></a>Megjegyzés operátora

|Parancs  |Támogatott | 
|---------|---------|
$comment |Igen| 

### <a name="projection-operators"></a>Vetítési operátorok

|Parancs  |Támogatott |
|---------|---------|
|$elemMatch    |Igen|
|$meta|    Nem|
|$slice    | Igen|

### <a name="update-operators"></a>Frissítési operátorok

#### <a name="field-update-operators"></a>Mezőfrissítő operátorok

|Parancs  |Támogatott |
|---------|---------|
|$inc    |    Igen|
|$mul    |    Igen|
|$rename    |    Igen|
|$setOnInsert|    Igen|
|$set    |Igen|
|$unset| Igen|
|$min    |Igen|
|$max    |Igen|
|$currentDate    | Igen|

#### <a name="array-update-operators"></a>Tömbfrissítő operátorok

|Parancs  |Támogatott |
|---------|---------|
|$    |Igen|
|$[]|    Igen|
|$[<identifier>]|    Igen|
|$addToSet    |Igen|
|$pop    |Igen|
|$pullAll|    Igen|
|$pull    |Igen|
|$push    |Igen|
|$pushAll| Igen|


#### <a name="update-modifiers"></a>Módosítók frissítése

|Parancs  |Támogatott |
|---------|---------|
|$each    |    Igen|
|$slice    |Igen|
|$sort    |Igen|
|$position    |Igen|

#### <a name="bitwise-update-operator"></a>Bitenként frissítő operátor

|Parancs  |Támogatott |
|---------|---------|
| $bit    |    Igen|    
|$bitsAllSet    |    Nem|
|$bitsAnySet    |    Nem|
|$bitsAllClear    |Nem|
|$bitsAnyClear    |Nem|

### <a name="geospatial-operators"></a>Térinformatikai operátorok

Művelet | Támogatott| 
--- | --- |
$geoWithin | Igen |
$geoIntersects | Igen | 
$near |  Igen |
$nearSphere |  Igen |
$geometry |  Igen |
$minDistance | Igen |
$maxDistance | Igen |
$center | Igen |
$centerSphere | Igen |
$box | Igen |
$polygon |  Igen |

## <a name="cursor-methods"></a>Kurzormetódusok

|Parancs  |Támogatott |
|---------|---------|
|kurzor.batchMéret()    |    Igen|
|kurzor.close()    |Igen|
|kurzor.isZárt()|        Igen|
|kurzor.collation()|    Nem|
|kurzor.megjegyzés()    |Igen|
|kurzor.count()    |Igen|
|kurzor.explain()|    Nem|
|kurzor.forEach()    |Igen|
|kurzor.hasNext()    |Igen|
|kurzor.hint()    |Igen|
|kurzor.isKimerült()|    Igen|
|kurzor.itcount()    |Igen|
|kurzor.limit()    |Igen|
|kurzor.térkép()    |Igen|
|kurzor.maxScan()    |Igen|
|kurzor.maxTimeMS()|    Igen|
|kurzor.max()    |Igen|
|kurzor.min()    |Igen|
|kurzor.next()|    Igen|
|kurzor.noCursorTimeout()    |Nem|
|kurzor.objsLeftInBatch()    |Igen|
|kurzor.pretty()|    Igen|
|kurzor.readConcern()|    Igen|
|kurzor.readPref()        |Igen|
|kurzor.returnKey()    |Nem|
|kurzor.showRecordId()|    Nem|
|kurzor.méret()    |Igen|
|kurzor.skip()    |Igen|
|cursor.sort()    |    Igen|
|kurzor.tailable()|    Nem|
|kurzor.toArray()    |Igen|

## <a name="sort-operations"></a>Rendezési műveletek

A művelet `findOneAndUpdate` használatakor a rendezési műveletek egyetlen mezőben támogatottak, de több mezőrendezési műveletei nem támogatottak.

## <a name="unique-indexes"></a>Egyedi indexek

Az egyedi indexek biztosítják, hogy egy adott mező ne rendelkezzen ismétlődő értékekkel a gyűjtemény összes dokumentumában, hasonlóan ahhoz, ahogyan az egyediség megmarad az alapértelmezett "_id" kulcson. Egyéni indexeket hozhat létre a Cosmos DB-ben a createIndex paranccsal, beleértve az "egyedi" megkötést is.

## <a name="time-to-live-ttl"></a>Élettartam (TTL)

A Cosmos DB támogatja az élő (TTL) időt a dokumentum időbélyege alapján. A TTL az [Azure Portalon](https://portal.azure.com)keresztül engedélyezhető a beszedéshez.

## <a name="user-and-role-management"></a>Felhasználó- és szerepkörkezelés

Cosmos DB még nem támogatja a felhasználók és szerepkörök. A Cosmos DB azonban támogatja a szerepköralapú hozzáférés-vezérlést (RBAC), valamint az [Azure Portalon](https://portal.azure.com) (Connection String lap) beszerezhető írási és írási és írásvédett jelszavakat/kulcsokat.

## <a name="replication"></a>Replikáció

Cosmos DB támogatja az automatikus, natív replikáció a legalacsonyabb rétegeken. A rendszer az alacsony késésű, globális replikáció elérése érdekében kiterjeszti ezt a logikát. A Cosmos DB nem támogatja a manuális replikációs parancsokat.

## <a name="write-concern"></a>Írási szempont

Egyes alkalmazások [írási probléma,](https://docs.mongodb.com/manual/reference/write-concern/) amely meghatározza a válaszok száma szükséges egy írási művelet során. Mivel a Cosmos DB a replikációt a háttérben kezeli, alapértelmezés szerint minden írás automatikusan kvórum. Az ügyfélkód által megadott írási problémákat a rendszer figyelmen kívül hagyja. További információk: [A rendelkezésre állás és a teljesítmény maximalizálása a konzisztenciaszintek használatával](consistency-levels.md).

## <a name="sharding"></a>Sharding

Az Azure Cosmos DB támogatja az automatikus, kiszolgálóoldali horizontális skálázást. Automatikusan kezeli a szegmensek létrehozását, elhelyezését és kiegyensúlyozását. Az Azure Cosmos DB nem támogatja a manuális horizontális műveletek et, ami azt jelenti, hogy nem kell meghívnia a parancsokat, például az addShard, balancerStart, moveChunk stb. Csak meg kell adnia a szegmenskulcsot a tárolók létrehozása vagy az adatok lekérdezése közben.

## <a name="sessions"></a>Munkamenetek

Az Azure Cosmos DB még nem támogatja a kiszolgáló oldali munkamenetek parancsait.

## <a name="next-steps"></a>További lépések

- További információkért ellenőrizze [Mongo 3.6 verzió funkciók](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
- Ismerje meg, hogyan használhatja a [Studio 3T-t](mongodb-mongochef.md) az Azure Cosmos DB MongoDB-hoz való API-jával.
- Ismerje meg, hogyan használhatja a [Robo 3T-t](mongodb-robomongo.md) az Azure Cosmos DB MongoDB-hoz való API-jával.
- Fedezze fel a [MongoDB-mintákat](mongodb-samples.md) az Azure Cosmos DB MongoDB-hoz kapcsolódó API-jával.

<sup>Megjegyzés: Ez a cikk az Azure Cosmos DB szolgáltatását ismerteti, amely a MongoDB-adatbázisokkal való vezetékes protokollkompatibilitást biztosítja. A Microsoft nem futtat MongoDB adatbázisokat a szolgáltatás biztosításához. Az Azure Cosmos DB nem áll kapcsolatban a MongoDB, Inc. vállalattal.</sup>
