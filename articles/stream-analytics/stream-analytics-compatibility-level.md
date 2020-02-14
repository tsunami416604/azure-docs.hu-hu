---
title: Azure Stream Analytics kompatibilitási szintek
description: Ismerje meg, hogyan állíthatja be az Azure Stream Analytics-feladat és a fontosabb változását foglalja össze a kompatibilitási szintje a legújabb kompatibilitási szinten
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 14b04f7d6068863c79c7060d29b58232be1f40cb
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201798"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Az Azure Stream Analytics-feladatok kompatibilitási szintje

Ez a cikk a Azure Stream Analytics kompatibilitási szintjének beállítását ismerteti. A Stream Analytics felügyelt szolgáltatás, amely rendszeres frissítésekkel és teljesítménnyel kapcsolatos újdonságokkal rendelkezik. A szolgáltatás futtatókörnyezetének legtöbb frissítése automatikusan elérhetővé válik a végfelhasználók számára. 

A szolgáltatás egyes új funkciói azonban jelentős változást is bevezethetnek, például egy meglévő feladat viselkedésének változását, vagy az adatok futtatásának módját a futó feladatok során. A meglévő Stream Analytics feladatok jelentős változtatások nélkül is megtarthatók, ha elhagyja a kompatibilitási szint beállítását. Ha készen áll a legújabb futásidejű viselkedésekre, a kompatibilitási szint növelésével engedélyezheti a funkciót. 

## <a name="choose-a-compatibility-level"></a>Kompatibilitási szint kiválasztása

Kompatibilitási szint szabályozza a stream analytics-feladat működését. 

A Azure Stream Analytics jelenleg három kompatibilitási szintet támogat:

* 1,0 – korábbi viselkedés
* 1,1 – alapértelmezett viselkedés
* 1,2 – a legújabb fejlemények a legújabb megoldásokkal

Az eredeti 1,0 kompatibilitási szint számos éve Azure Stream Analytics általánosan elérhető.

Amikor új Stream Analytics feladatot hoz létre, ajánlott azt a legújabb kompatibilitási szinttel létrehozni. Indítsa el a feladat kialakítását a legújabb viselkedések alapján, hogy a későbbiekben ne kelljen a változásokhoz és a bonyolultsághoz adni.

## <a name="set-the-compatibility-level"></a>Kompatibilitási szint beállítása

A Stream Analyticsi feladatokhoz tartozó kompatibilitási szint beállítható a Azure Portal vagy a [feladatok létrehozása REST API hívás](/rest/api/streamanalytics/stream-analytics-job)használatával.

A Azure Portal a feladatokhoz tartozó kompatibilitási szint frissítése:

1. A Stream Analytics feladatainak megkereséséhez használja a [Azure Portal](https://portal.azure.com) .
2. A kompatibilitási szint frissítése előtt **állítsa le** a feladatot. A kompatibilitási szint nem frissíthető, ha a feladat nem futó állapotban.
3. A **configure (Konfigurálás** ) fejléc alatt válassza a **kompatibilitási szint**lehetőséget.
4. Válassza ki a kívánt kompatibilitási szint értékét.
5. Kattintson a lap alján található **Mentés** gombra.

![Stream Analytics kompatibilitási szintje az Azure Portalon](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

A kompatibilitási szint frissítésekor a T-Compiler érvényesíti a feladatot a kiválasztott kompatibilitási szintnek megfelelő szintaxissal.

## <a name="compatibility-level-12"></a>Kompatibilitási szint 1,2

A kompatibilitási szint 1,2-as verziójában a következő jelentős változások következnek be:

###  <a name="amqp-messaging-protocol"></a>AMQP üzenetküldési protokoll

**1,2 szint**: Azure stream Analytics a [speciális üzenetsor-kezelési protokoll (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) üzenetkezelő protokollt használja a Service Bus várólistákba és témakörökbe való íráshoz. A AMQP lehetővé teszi többplatformos hibrid alkalmazások készítését egy nyílt szabványú protokoll használatával.

### <a name="geospatial-functions"></a>Térinformatikai funkciók

**Előző szintek:** Azure Stream Analytics használt földrajzi számítások.

**1,2 szint:** Azure Stream Analytics lehetővé teszi a geometriai tervezett földrajzi koordináták számítását. A térinformatikai függvények aláírása nem változik. Azonban a szemantikauk némileg eltérő, ami pontosabb számítást tesz lehetővé, mint korábban.

Azure Stream Analytics támogatja a térinformatikai segédletek indexelését. A térinformatikai elemeket tartalmazó hivatkozási adatokat egy gyorsabb összekapcsolási számításhoz lehet indexelni.

A frissített térinformatikai függvények a jól ismert Text (WKT) térinformatikai formátum teljes kifejező. Más térinformatikai összetevőket is megadhat, amelyek korábban nem támogatottak a GeoJson.

További információ: a [térinformatikai funkciók frissítései Azure stream Analytics – felhő és IoT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Több partíciót tartalmazó bemeneti források párhuzamos lekérdezésének végrehajtása

**Előző szintek:** Azure Stream Analytics lekérdezésekhez szükséges a PARTITION BY záradék használata, hogy integrálással a lekérdezések feldolgozását a bemeneti forrás partíciói között.

**1,2 szint:** Ha a lekérdezési logika párhuzamosan használható a bemeneti forrás partíciók között, Azure Stream Analytics különálló lekérdezési példányokat hoz létre, és párhuzamosan futtatja a számításokat.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Natív, tömeges API-integráció a CosmosDB kimenetével

**Előző szintek:** A upsert viselkedése *Beszúrás vagy egyesítés*volt.

**1,2 szint:** A CosmosDB-kimenettel való natív, tömeges API-integráció maximalizálja az átviteli sebességet, és hatékonyan kezeli a szabályozási kérelmeket. További információkért tekintse meg [a Azure Cosmos DB Azure stream Analytics kimenete lapot](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12).

A upsert viselkedése *INSERT vagy replace*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset SQL-kimenetre írásakor

**Előző szintek:** a [DATETIMEOFFSET](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) -típusok UTC értékre lettek igazítva.

**1,2 szint:** A DateTimeOffset már nincs módosítva.

### <a name="long-when-writing-to-sql-output"></a>Az SQL-kimenetre való írás hosszú ideig

**Előző szintek:** A cél típusa alapján a rendszer csonkolt értékeket adott meg.

**1,2 szint:** A célként megadott típushoz nem illeszkedő értékeket a kimeneti hiba házirendjének megfelelően kezeli a rendszer.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>Rekord és tömb szerializálása SQL-kimenet írásakor

**Előző szintek:** A rekordokat "Record"-ként írták, a tömbök pedig "Array"-ként lettek írva.

**1,2 szint:** A rekordok és tömbök JSON formátumban vannak szerializálva.

### <a name="strict-validation-of-prefix-of-functions"></a>A függvények előtagja szigorú ellenőrzése

**Előző szintek:** A függvény előtagjainak szigorú ellenőrzése nem történt meg.

**1,2 szint:** Azure Stream Analytics a függvények előtagjainak szigorú ellenőrzése. Egy előtag egy beépített függvényhez való hozzáadása hibát okoz. A`myprefix.ABS(…)` például nem támogatott.

Egy előtagnak a beépített összesítésekhez való hozzáadása hibát is eredményez. A `myprefix.SUM(…)` például nem támogatott.

A "System" előtag használata bármely felhasználó által definiált függvénynél hibát eredményez.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Tömb és objektum kulcs tulajdonságaiként való letiltása Cosmos DB kimeneti adapteren

**Előző szintek:** A tömb és az Objektumtípusok a Key tulajdonságként támogatottak.

**1,2 szint:** A tömb-és objektumtípusok már nem támogatottak Key tulajdonságként.

## <a name="compatibility-level-11"></a>Kompatibilitási szint 1,1

1\.1-kompatibilitási szinten a következő jelentős változások történtek:

### <a name="service-bus-xml-format"></a>Service Bus XML-formátum

**1,0 szint:** Azure Stream Analytics használt DataContractSerializer, így az üzenet tartalma XML-címkéket tartalmaz. Például:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1,1 szint:** Az üzenet tartalma közvetlenül tartalmazza a streamet, és nem tartalmaz további címkéket. Például:`{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Kis-és nagybetűk megkülönböztetése a mezőnevek esetében

**1,0 szint:** A mezők nevei a Azure Stream Analytics motor általi feldolgozáskor kisbetűre változtak.

**1,1 szint:** a kis-és nagybetűk megkülönböztetése megmarad a mezők neveinél, amikor azokat a Azure stream Analytics motor dolgozza fel.

> [!NOTE]
> Persisting Kisbetű/nagybetű megkülönböztetése még nem érhető el az Edge-környezetben üzemeltetett Stream elemzési feladatok. Ennek eredményeképpen minden mezőnevek vannak kisbetűssé konvertált, ha a feladat az Edge-ben üzemel.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**1,0 szint:** CREATE TABLE parancs nem szűrte az eseményeket NaN (nem-a-Number) értékkel. Például végtelenig, - Infinity) egy LEBEGŐPONTOS oszlop írja be, mert ezek a számok a dokumentált tartományon vannak.

**1,1 szint:** CREATE TABLE lehetővé teszi egy erős séma megadását. A Stream Analytics-motor ellenőrzi, hogy az adatok megfelel-e a séma. Ebben a modellben a parancs szűrheti NaN értéket tartalmazó eseményeket.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Automatikus leküldési idő letiltása a JSON-beli datetime-karakterláncokhoz

**1,0 szint:** A JSON-elemző automatikusan a dátum/idő/zóna információval rendelkező karakterlánc-értékeket a DateTime típusúra konvertálja, majd átalakítja az UTC értékre. Ez a viselkedés az időzóna-információk elvesztését eredményezte.

**1,1 szint:** A dátum/idő/zóna információval rendelkező karakterlánc-értékek nem rendelkeznek automatikusan DateTime típussal. Ennek eredményeképpen az időzónaadatok tartani.

## <a name="next-steps"></a>Következő lépések

* [Azure Stream Analytics bemenetek hibáinak megoldása](stream-analytics-troubleshoot-input.md)
* [Erőforrás-állapot Stream Analytics](stream-analytics-resource-health.md)
