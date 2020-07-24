---
title: Azure Cosmos DB API a MongoDB-hez (3,6-es verzió) támogatott funkciók és szintaxis
description: Ismerkedjen meg Azure Cosmos DB API-MongoDB (3,6 verzió) támogatott funkciókkal és szintaxissal.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 07/15/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: bd59b27b5af92d7aa90851c592ba4de495e41283
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076839"
---
# <a name="azure-cosmos-dbs-api-for-mongodb-36-version-supported-features-and-syntax"></a>MongoDB-hez készült Azure Cosmos DB API (3.6-os verzió): támogatott funkciók és szintaxis

Az Azure Cosmos DB a Microsoft globálisan elosztott többmodelles adatbázis-szolgáltatása. A MongoDB Azure Cosmos DB API-jával a nyílt forráskódú MongoDB [-ügyfelek](https://docs.mongodb.org/ecosystem/drivers)bármelyikének használatával kommunikálhat. A MongoDB Azure Cosmos DB API-je lehetővé teszi a meglévő ügyféloldali illesztőprogramok használatát az MongoDB [Wire protokoll](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)betartásával.

A Azure Cosmos DB API-MongoDB való használatával élvezheti a használt MongoDB előnyeit, és az összes olyan vállalati funkciót, amelyet a Cosmos DB biztosít: [globális elosztás](distribute-data-globally.md), [automatikus](partition-data.md)horizontális felskálázás, rendelkezésre állási és késési garanciák, titkosítás nyugalmi állapotban, biztonsági mentések és sok más.

## <a name="protocol-support"></a>Protokollok támogatása

A MongoDB Azure Cosmos DB API-ját az MongoDB Server **3,6** -es verziójának az új fiókok esetében alapértelmezés szerint kompatibilisnek kell lennie. A támogatott operátorok, valamint a korlátozások és kivételek listája alább található. Minden olyan ügyfélillesztőnek, amely ismeri ezeket a protokollokat, tudnia kell kapcsolódnia a MongoDB-hez készült Azure Cosmos DB API-hoz. Vegye figyelembe, hogy a MongoDB-fiókok Azure Cosmos DB API-ját használva a fiókok 3,6-es verziója a végpontot használja, `*.mongo.cosmos.azure.com` míg a fiókok 3,2-es verziójának formátuma a végpont `*.documents.azure.com` .

## <a name="query-language-support"></a>Nyelvi támogatás lekérdezése

A Azure Cosmos DB API-MongoDB átfogó támogatást nyújt a MongoDB lekérdezési nyelvi szerkezetekhez. Az alábbiakban megtalálhatja a jelenleg támogatott műveletek, operátorok, szakaszok, parancsok és beállítások részletes listáját.

## <a name="database-commands"></a>Adatbázisparancsok

Azure Cosmos DB API-MongoDB a következő adatbázis-parancsokat támogatja:

### <a name="query-and-write-operation-commands"></a>Lekérdezési és írási műveletek parancsai

|Parancs  |Támogatott |
|---------|---------|
|törlés | Yes |
|find | Yes     |
|findAndModify | Yes  |
|getLastError|   Yes |
|getMore  |  Yes  |
|getPrevError | No  |
|insert  |   Yes  |
|parallelCollectionScan  | Yes   |
|resetError |    No  |
|update  |   Yes  |
|[Streamek módosítása](mongodb-change-streams.md)  |  Yes  |
|GridFS |   Yes  |

### <a name="authentication-commands"></a>Hitelesítési parancsok

|Parancs  |Támogatott |
|---------|---------|
|authenticate    |   Yes      |
|logout    |      Yes   |
|getnonce   |    Yes     |


### <a name="administration-commands"></a>Adminisztrációs parancsok

|Parancs  |Támogatott |
|---------|---------|
|Korlátozott gyűjtemények   |   No      |
|cloneCollectionAsCapped     |   No      |
|collMod     |   No      |
|collMod: expireAfterSeconds   |   No      |
|convertToCapped   |  No       |
|copydb     |  No       |
|létrehozás   |    Yes     |
|createIndexes     |  Yes       |
|currentOp     |  Yes       |
|drop     |   Yes      |
|dropDatabase     |  Yes       |
|dropIndexes     |   Yes      |
|filemd5    |   Yes      |
|killCursors    |  Yes       |
|Mintálné     |   No      |
|listCollections     |  Yes       |
|listDatabases     |  Yes       |
|listIndexes     |  Yes       |
|reIndex     |    Yes     |
|renameCollection     |    No     |
|connectionStatus    |     No    |

### <a name="diagnostics-commands"></a>Diagnosztikai parancsok

|Parancs  |Támogatott |
|---------|---------|
|buildInfo         |   Yes      |
|collStats    |  Yes       |
|connPoolStats     |  No       |
|connectionStatus     |  No       |
|dataSize     |   No      |
|dbHash    |    No     |
|dbStats     |   Yes      |
|megmagyarázni     |   Yes      |
|magyarázat: executionStats     |   Yes      |
|funkciók     |    No     |
|hostInfo     |   No      |
|listDatabases         |   Yes      |
|Parancslista elemre     |  No       |
|Profiler     |  No       |
|serverStatus     |  No       |
|top     |    No     |
|whatsmyuri     |   Yes      |

<a name="aggregation-pipeline"></a>

## <a name="aggregation-pipelinea"></a>Összesítési folyamat</a>

### <a name="aggregation-commands"></a>Összesítési parancsok

|Parancs  |Támogatott |
|---------|---------|
|aggregate |   Yes  |
|count     |   Yes  |
|distinct  | Yes |
|mapReduce | No |

### <a name="aggregation-stages"></a>Összesítési fázisok

|Parancs  |Támogatott |
|---------|---------|
|$collStats    |No|
|$project    |Yes|
|$match    |Yes|
|$redact|    Yes|
|$limit    |Yes|
|$skip    |Yes|
|$unwind|    Yes|
|$group    |    Yes|
|$sample|        Yes|
|$sort    |Yes|
|$geoNear|    No|
|$lookup    |    Yes|
|$out        |Yes|
|$indexStats|        No|
|$facet    |No|
|$bucket|    No|
|$bucketAuto|    No|
|$sortByCount|    Yes|
|$addFields    |Yes|
|$replaceRoot|    Yes|
|$count    |Yes|
|$currentOp|    No|
|$listLocalSessions    |No|
|$listSessions    |No|
|$graphLookup    |No|

### <a name="boolean-expressions"></a>Logikai kifejezések

|Parancs  |Támogatott |
|---------|---------|
|$and| Yes|
|$or|Yes|
|$not|Yes|

### <a name="set-expressions"></a>Halmazkifejezések

|Parancs  |Támogatott |
|---------|---------|
| $setEquals | Yes|
|$setIntersection|Yes|
| $setUnion|Yes|
| $setDifference|Yes|
| $setIsSubset|Yes|
| $anyElementTrue|Yes|
| $allElementsTrue|Yes|

### <a name="comparison-expressions"></a>Összehasonlító kifejezések

|Parancs  |Támogatott |
|---------|---------|
|$cmp     |  Yes       |
|$eq|    Yes| 
|$gt |    Yes| 
|$gte|    Yes| 
|$lt    |Yes|
|$lte|    Yes| 
|$ne    |    Yes| 
|$in    |    Yes| 
|$nin    |    Yes| 

### <a name="arithmetic-expressions"></a>Aritmetikai kifejezések

|Parancs  |Támogatott |
|---------|---------|
|$abs |  Yes       |
| $add |  Yes       |
| $ceil |  Yes       |
| $divide |  Yes       |
| $exp |  Yes       |
| $floor |  Yes       |
| $ln |  Yes       |
| $log |  Yes       |
| $log10 |  Yes       |
| $mod |  Yes       |
| $multiply |  Yes       |
| $pow |  Yes       |
| $sqrt |  Yes       |
| $subtract |  Yes       |
| $trunc |  Yes       |

### <a name="string-expressions"></a>Sztringkifejezések

|Parancs  |Támogatott |
|---------|---------|
|$concat |  Yes       |
| $indexOfBytes|  Yes       |
| $indexOfCP|  Yes       |
| $split|  Yes       |
| $strLenBytes|  Yes       |
| $strLenCP|  Yes       |
| $strcasecmp|  Yes       |
| $substr|  Yes       |
| $substrBytes|  Yes       |
| $substrCP|  Yes       |
| $toLower|  Yes       |
| $toUpper|  Yes       |

### <a name="text-search-operator"></a>Szöveges keresési operátor

|Parancs  |Támogatott |
|---------|---------|
| $meta | No|

### <a name="array-expressions"></a>Tömbkifejezések

|Parancs  |Támogatott |
|---------|---------|
|$arrayElemAt    |    Yes|
|$arrayToObject|    Yes|
|$concatArrays    |    Yes|
|$filter    |    Yes|
|$indexOfArray    |Yes|
|$isArray    |    Yes|
|$objectToArray    |Yes|
|$range    |Yes|
|$reverseArray    |    Yes|
|$reduce|    Yes|
|$size    |    Yes|
|$slice    |    Yes|
|$zip    |    Yes|
|$in    |    Yes|

### <a name="variable-operators"></a>Változó operátorok

|Parancs  |Támogatott |
|---------|---------|
|$map    |No|
|$let    |Yes|

### <a name="system-variables"></a>Rendszerváltozók

|Parancs  |Támogatott |
|---------|---------|
|$ $CURRENT|    Yes|
|$ $DESCEND|        Yes|
|$ $KEEP        |Yes|
|$ $PRUNE    |    Yes|
|$ $REMOVE    |Yes|
|$ $ROOT        |Yes|

### <a name="literal-operator"></a>Literális operátor

|Parancs  |Támogatott |
|---------|---------|
|$literal    |Yes|

### <a name="date-expressions"></a>Dátumkifejezések

|Parancs  |Támogatott |
|---------|---------|
|$dayOfYear    |Yes    |
|$dayOfMonth|    Yes    |
|$dayOfWeek    |Yes    |
|$year    |Yes    |
|$month    |Yes|    
|$week    |Yes    |
|$hour    |Yes    |
|$minute|    Yes|    
|$second    |Yes    |
|$millisecond|    Yes|    
|$dateToString    |Yes    |
|$isoDayOfWeek    |Yes    |
|$isoWeek    |Yes    |
|$dateFromParts|    No|    
|$dateToParts    |No    |
|$dateFromString|    No|
|$isoWeekYear    |Yes    |

### <a name="conditional-expressions"></a>Feltételes kifejezések

|Parancs  |Támogatott |
|---------|---------|
| $cond| Yes|
| $ifNull| Yes|
| $switch |Yes|

### <a name="data-type-operator"></a>Adattípus-operátor

|Parancs  |Támogatott |
|---------|---------|
| $type| Yes|

### <a name="accumulator-expressions"></a>Gyűjtői kifejezések

|Parancs  |Támogatott |
|---------|---------|
|$sum    |Yes    |
|$avg    |Yes    |
|$first|    Yes|
|$last    |Yes    |
|$max    |Yes    |
|$min    |Yes    |
|$push|    Yes|
|$addToSet|    Yes|
|$stdDevPop|    No    |
|$stdDevSamp|    No|

### <a name="merge-operator"></a>Egyesítési operátor

|Parancs  |Támogatott |
|---------|---------|
| $mergeObjects | Yes|

## <a name="data-types"></a>Adattípusok

|Parancs  |Támogatott |
|---------|---------|
|Dupla    |Yes    |
|Sztring    |Yes    |
|Objektum    |Yes    |
|Tömb    |Yes    |
|Bináris adatértékek    |Yes|    
|ObjectId    |Yes    |
|Logikai érték    |Yes    |
|Dátum    |Yes    |
|Null    |Yes    |
|32 bites egész szám (int)    |Yes    |
|Timestamp    |Yes    |
|64 bites egész szám (hosszú)    |Yes    |
|MinKey    |Yes    |
|MaxKey    |Yes    |
|Decimal128    |Yes|    
|Reguláris kifejezés    |Yes|
|JavaScript    |Yes|
|JavaScript (hatókörrel)|    Yes    |
|Nem definiált    |Yes    |

## <a name="indexes-and-index-properties"></a>Indexek és index tulajdonságai

### <a name="indexes"></a>Indexek

|Parancs  |Támogatott |
|---------|---------|
|Egyetlen mező indexe    |Yes    |
|Összetett index    |Yes    |
|Multikey index    |Yes    |
|Szöveges index    |No|
|2dsphere    |Yes    |
|2D-index    |No    |
|Kivonatos index    | Yes|

### <a name="index-properties"></a>Index tulajdonságai

|Parancs  |Támogatott |
|---------|---------|
|TTL|    Yes    |
|Egyedi    |Yes|
|Részleges|    No|
|Kis-és nagybetűk megkülönböztetése    |No|
|Ritka    |No |
|Háttér|    Yes |

## <a name="operators"></a>Operátorok

### <a name="logical-operators"></a>Logikai operátorok

|Parancs  |Támogatott |
|---------|---------|
|$or    |    Yes|
|$and    |    Yes|
|$not    |    Yes|
|$nor    |    Yes| 

### <a name="element-operators"></a>Elem operátorai

|Parancs  |Támogatott |
|---------|---------|
|$exists|    Yes|
|$type    |    Yes|

### <a name="evaluation-query-operators"></a>Kiértékelési lekérdezési operátorok

|Parancs  |Támogatott |
|---------|---------|
|$expr    |    No|
|$jsonSchema    |    No|
|$mod    |    Yes|
|$regex |    Yes|
|$text    | Nem (nem támogatott. Ehelyett használja a $regex.)| 
|$where    |No| 

A $regex lekérdezésekben a bal oldali rögzített kifejezések lehetővé teszik az indexek keresését. Azonban az „i” módosító (kis- és nagybetűk megkülönböztetése nélkül) és az „m” módosító (többsoros) használatakor a gyűjtemény az összes kifejezésben keres.

Ha bele kell foglalni a „$” vagy a „|” karaktert, célszerű két (vagy több) regex lekérdezést létrehozni. Ha például a következő eredeti lekérdezést adta meg: ```find({x:{$regex: /^abc$/})``` , azt a következőképpen kell módosítani:

```find({x:{$regex: /^abc/, x:{$regex:/^abc$/}})```.

Az első rész az indexet fogja használni a keresés ^abc kezdetű dokumentumokra való korlátozásához, a második rész pedig meg fogja feleltetni a pontos bejegyzéseket. A „|” sávoperátor „vagy” függvényként működik – a(z) ```find({x:{$regex: /^abc|^def/})``` lekérdezés megfelelteti azokat a dokumentumokat, amelyekben az „x” mezőben „abc” vagy „def” sztringgel kezdődő értékek találhatók. Az index használatához ajánlott a lekérdezést két külön lekérdezésre bontani, amelyeket az $or operátor kapcsol össze: ```find( {$or : [{x: $regex: /^abc/}, {$regex: /^def/}] })```.

### <a name="array-operators"></a>Tömb operátorai

|Parancs  |Támogatott | 
|---------|---------|
| $all | Yes| 
| $elemMatch | Yes| 
| $size | Yes | 

### <a name="comment-operator"></a>Megjegyzés operátora

|Parancs  |Támogatott | 
|---------|---------|
$comment |Yes| 

### <a name="projection-operators"></a>Kivetítési operátorok

|Parancs  |Támogatott |
|---------|---------|
|$elemMatch    |Yes|
|$meta|    No|
|$slice    | Yes|

### <a name="update-operators"></a>Frissítési operátorok

#### <a name="field-update-operators"></a>Mezőfrissítő operátorok

|Parancs  |Támogatott |
|---------|---------|
|$inc    |    Yes|
|$mul    |    Yes|
|$rename    |    Yes|
|$setOnInsert|    Yes|
|$set    |Yes|
|$unset| Yes|
|$min    |Yes|
|$max    |Yes|
|$currentDate    | Yes|

#### <a name="array-update-operators"></a>Tömbfrissítő operátorok

|Parancs  |Támogatott |
|---------|---------|
|$    |Yes|
|$[]|    Yes|
|$[<identifier>]|    Yes|
|$addToSet    |Yes|
|$pop    |Yes|
|$pullAll|    Yes|
|$pull    |Yes|
|$push    |Yes|
|$pushAll| Yes|


#### <a name="update-modifiers"></a>Módosítók frissítése

|Parancs  |Támogatott |
|---------|---------|
|$each    |    Yes|
|$slice    |Yes|
|$sort    |Yes|
|$position    |Yes|

#### <a name="bitwise-update-operator"></a>Bitenként frissítő operátor

|Parancs  |Támogatott |
|---------|---------|
| $bit    |    Yes|    
|$bitsAllSet    |    No|
|$bitsAnySet    |    No|
|$bitsAllClear    |No|
|$bitsAnyClear    |No|

### <a name="geospatial-operators"></a>Térinformatikai operátorok

Operátor | Támogatott| 
--- | --- |
$geoWithin | Yes |
$geoIntersects | Yes | 
$near |  Yes |
$nearSphere |  Yes |
$geometry |  Yes |
$minDistance | Yes |
$maxDistance | Yes |
$center | Yes |
$centerSphere | Yes |
$box | Yes |
$polygon |  Yes |

## <a name="cursor-methods"></a>Kurzormetódusok

|Parancs  |Támogatott |
|---------|---------|
|cursor.batchSize ()    |    Yes|
|kurzor. Bezárás ()    |Yes|
|cursor. isClosed ()|        Yes|
|kurzor. rendezés ()|    No|
|kurzor. comment ()    |Yes|
|kurzor. Count ()    |Yes|
|kurzor. magyarázat ()|    No|
|cursor. forEach ()    |Yes|
|cursor. hasNext ()    |Yes|
|cursor. Hint ()    |Yes|
|cursor. isExhausted ()|    Yes|
|cursor. itcount ()    |Yes|
|kurzor. limit ()    |Yes|
|kurzor. map ()    |Yes|
|cursor. maxScan ()    |Yes|
|cursor. maxTimeMS ()|    Yes|
|kurzor. max ()    |Yes|
|kurzor. min ()    |Yes|
|kurzor. Next ()|    Yes|
|cursor. noCursorTimeout ()    |No|
|cursor. objsLeftInBatch ()    |Yes|
|kurzor. Pretty ()|    Yes|
|cursor. readConcern ()|    Yes|
|cursor. readPref ()        |Yes|
|kurzor. rekulcsrakész ()    |No|
|cursor. showRecordId ()|    No|
|kurzor. Size ()    |Yes|
|kurzor. skip ()    |Yes|
|cursor.sort()    |    Yes|
|kurzor. farok ()|    No|
|cursor. toArray ()    |Yes|

## <a name="sort-operations"></a>Rendezési műveletek

A művelet használatakor a `findOneAndUpdate` rendezési műveletek egyetlen mező esetén támogatottak, de a rendezési műveletek több mezőn nem támogatottak.

## <a name="unique-indexes"></a>Egyedi indexek

Az [egyedi indexek](mongodb-indexing.md#unique-indexes) biztosítják, hogy egy adott mező ne legyen duplikált érték a gyűjtemény összes dokumentumában, hasonlóan ahhoz, ahogyan az egyediség megmarad az alapértelmezett "_id" kulcson. A Cosmos DBban egyedi indexeket hozhat létre a `createIndex` parancs és a `unique` megkötés paraméter használatával:

```javascript
globaldb:PRIMARY> db.coll.createIndex( { "amount" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

## <a name="compound-indexes"></a>Összetett indexek

Az [összetett indexek](mongodb-indexing.md#compound-indexes-mongodb-server-version-36) lehetővé teszik, hogy legfeljebb 8 mezőből álló csoportok számára hozzon létre indexet. Ez a típusú index eltér a natív MongoDB összetett indextől. Azure Cosmos DB az összetett indexek a több mezőre alkalmazott rendezési műveletekhez használatosak. Összetett index létrehozásához több tulajdonságot kell megadnia paraméterként:

```javascript
globaldb:PRIMARY> db.coll.createIndex({"amount": 1, "other":1})
{
        "createdCollectionAutomatically" : false, 
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 2,
        "ok" : 1
}
```

## <a name="time-to-live-ttl"></a>Élettartam (TTL)

A Cosmos DB a dokumentum időbélyegzője alapján támogatja a élettartamot (TTL). A TTL engedélyezhető a gyűjtemények számára, ha a [Azure Portalra](https://portal.azure.com)kattint.

## <a name="user-and-role-management"></a>Felhasználó- és szerepkörkezelés

A Cosmos DB még nem támogatja a felhasználókat és a szerepköröket. A Cosmos DB azonban támogatja a szerepköralapú hozzáférés-vezérlést (RBAC), valamint az írható és olvasható jelszavakat/kulcsokat, amelyeket a [Azure Portal](https://portal.azure.com) (kapcsolati karakterlánc oldalon) lehet beszerezni.

## <a name="replication"></a>Replikáció

Cosmos DB támogatja az automatikus és a natív replikálást a legalacsonyabb rétegeken. A rendszer az alacsony késésű, globális replikáció elérése érdekében kiterjeszti ezt a logikát. A Cosmos DB nem támogatja a manuális replikálási parancsokat.

## <a name="write-concern"></a>Írási szempont

Egyes alkalmazások [írási aggodalomra](https://docs.mongodb.com/manual/reference/write-concern/) számíthatnak, amely meghatározza az írási művelet során szükséges válaszok számát. Mivel a Cosmos DB a replikációt a háttérben kezeli, alapértelmezés szerint minden írás automatikusan kvórum. Az ügyfél kódja által meghatározott írási aggályok figyelmen kívül lesznek hagyva. További információk: [A rendelkezésre állás és a teljesítmény maximalizálása a konzisztenciaszintek használatával](consistency-levels.md).

## <a name="sharding"></a>Sharding

Az Azure Cosmos DB támogatja az automatikus, kiszolgálóoldali horizontális skálázást. A szegmensek létrehozását, elhelyezését és kiegyensúlyozását automatikusan kezeli. Azure Cosmos DB nem támogatja a manuális horizontális skálázási parancsokat, ami azt jelenti, hogy nem kell olyan parancsokat meghívnia, mint például a addShard, a balancerStart, a moveChunk stb. A tárolók létrehozásakor vagy az adatlekérdezés során csak a szegmens kulcsát kell megadnia.

## <a name="sessions"></a>Munkamenetek

A Azure Cosmos DB még nem támogatja a kiszolgálóoldali munkamenet-parancsokat.

## <a name="next-steps"></a>További lépések

- További információ: [Mongo 3,6 version features](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
- Ismerje meg, hogyan [használhatja a Studio 3T](mongodb-mongochef.md) Azure Cosmos db API-ját a MongoDB.
- Ismerje meg, hogyan [használhatja a Robo 3T](mongodb-robomongo.md) -t a Azure Cosmos db API-MongoDB.
- Ismerkedjen meg a MongoDB- [mintákkal](mongodb-samples.md) Azure Cosmos db API-val a MongoDB.

<sup>Megjegyzés: Ez a cikk az Azure Cosmos DB egy olyan szolgáltatását ismerteti, amely a MongoDB-adatbázisokkal való, vezetékes protokoll-kompatibilitást biztosít. A Microsoft nem futtat MongoDB-adatbázisokat a szolgáltatás biztosításához. Azure Cosmos DB nem kapcsolódik a MongoDB, Inc. szolgáltatáshoz.</sup>
