---
title: Az Azure Stream Analytics kompatibilitási szintjei
description: Ismerje meg, hogyan állíthat be kompatibilitási szintet egy Azure Stream Analytics-feladathoz, és hogyan módosíthatja a legújabb kompatibilitási szintet
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 8f22b1ff97826dc318794aca58973b1276e74209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087858"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Kompatibilitási szint az Azure Stream Analytics-feladatokhoz

Ez a cikk ismerteti a kompatibilitási szint lehetőséget az Azure Stream Analytics. A Stream Analytics egy felügyelt szolgáltatás, amely rendszeres funkciófrissítésekkel és teljesítménybeli fejlesztésekkel rendelkezik. A szolgáltatás futási időszakának legtöbb frissítése automatikusan elérhetővé válik a végfelhasználók számára. 

A szolgáltatás néhány új funkciója azonban jelentős változást eredményezhet, például egy meglévő feladat viselkedésének változását vagy az adatok futási feladatokban való felhasználásának változását. A meglévő Stream Analytics-feladatok at jelentős módosítások nélkül is futtathatja, ha a kompatibilitási szint beállítását csökkenti. Ha készen áll a legújabb futásidejű viselkedésmódokra, a kompatibilitási szint növelésével engedélyezheti a jelentkezést. 

## <a name="choose-a-compatibility-level"></a>Kompatibilitási szint kiválasztása

A kompatibilitási szint szabályozza a streamelemzési feladat futásidejű viselkedését. 

Az Azure Stream Analytics jelenleg három kompatibilitási szintet támogat:

* 1.0 – Az Azure Stream Analytics általános elérhetősége során néhány évvel ezelőtt bevezetett eredeti kompatibilitási szint.
* 1.1 - Korábbi viselkedés
* 1.2 - Legújabb viselkedés a legújabb fejlesztésekkel

Amikor új Stream Analytics-feladatot hoz létre, ajánlott a legújabb kompatibilitási szint használatával létrehozni. Indítsa el a feladat tervezés támaszkodva a legújabb viselkedést, hogy elkerüljék a hozzáadott változás és összetettség később.

## <a name="set-the-compatibility-level"></a>A kompatibilitási szint beállítása

Beállíthatja a kompatibilitási szintet a Stream Analytics-feladat az Azure Portalon, vagy a [create job REST API-hívás](/rest/api/streamanalytics/stream-analytics-job)használatával.

A feladat kompatibilitási szintjének frissítése az Azure Portalon:

1. Az [Azure Portal](https://portal.azure.com) segítségével keresse meg a Stream Analytics-feladat.
2. **Állítsa le** a feladatot a kompatibilitási szint frissítése előtt. Nem frissítheti a kompatibilitási szintet, ha a feladat futó állapotban van.
3. A **Konfigurálás** cím alatt válassza a **Kompatibilitási szint lehetőséget.**
4. Válassza ki a kívánt kompatibilitási szintet.
5. Válassza a **Mentés** gombot a lap alján.

![A Stream Analytics kompatibilitási szintje az Azure Portalon](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

A kompatibilitási szint frissítésekor a T-fordító a kijelölt kompatibilitási szintnek megfelelő szintaxissal ellenőrzi a feladatot.

## <a name="compatibility-level-12"></a>Kompatibilitási szint 1.2

Az 1.2-es kompatibilitási szinten a következő jelentős változtatások vezetnek be:

###  <a name="amqp-messaging-protocol"></a>AMQP üzenetkezelési protokoll

**1.2 szint:** Az Azure Stream Analytics [advanced message queueing protocol (AMQP)](../service-bus-messaging/service-bus-amqp-overview.md) üzenetkezelési protokollt használ a Service Bus-várólistákba és témakörökbe való íráshoz. Az AMQP lehetővé teszi, hogy platformfüggetlen, hibrid alkalmazásokat hozzon létre egy nyílt szabványú protokoll használatával.

### <a name="geospatial-functions"></a>Térinformatikai funkciók

**Előző szintek:** Az Azure Stream Analytics földrajzi számításokat használt.

**1.2 szint:** Az Azure Stream Analytics lehetővé teszi a geometriai vetített földrajzi koordináták kiszámítását. Nincs változás a térinformatikai függvények aláírásában. A szemantikájuk azonban kissé eltérő, így pontosabb számítást tesz lehetővé, mint korábban.

Az Azure Stream Analytics támogatja a térinformatikai referenciaadatok indexelését. A térinformatikai elemeket tartalmazó referenciaadatok indexelhetők a gyorsabb illesztési számítás érdekében.

A frissített térinformatikai függvények a jól ismert szöveg (WKT) térinformatikai formátumának teljes kifejezőkészségét hozzák. Megadhat más térinformatikai összetevőket, amelyeket korábban nem támogatott a GeoJson.

További információ: [A térinformatikai funkciók frissítése az Azure Stream Analytics – Cloud és IoT Edge szolgáltatásban.](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Párhuzamos lekérdezésvégrehajtás több partícióval rendelkező bemeneti források hoz

**Előző szintek:** Az Azure Stream Analytics-lekérdezések a PARTITION BY záradék használatát kellett használniuk a lekérdezésfeldolgozás párhuzamossá tétele a bemeneti forráspartíciók között.

**1.2 szint:** Ha a lekérdezési logika párhuzamosítható a bemeneti forráspartíciók között, az Azure Stream Analytics külön lekérdezési példányokat hoz létre, és párhuzamosan futtatja a számításokat.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Natív tömeges API-integráció a CosmosDB kimenettel

**Előző szintek:** A upsert viselkedése *beszúrás vagy egyesítés*volt.

**1.2 szint:** A CosmosDB-kimenettel való natív tömeges API-integráció maximalizálja az átviteli teljesítményt, és hatékonyan kezeli a sávszélesség-szabályozási kérelmeket. További információ: [az Azure Stream Analytics kimenete az Azure Cosmos DB-nek.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12)

A upsert viselkedése *beszúrás vagy csere.*

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset sql kimenetre íráskor

**Előző szintek:** [A DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) típusok UTC-re módosultak.

**1.2 szint:** A DateTimeOffset már nem módosul.

### <a name="long-when-writing-to-sql-output"></a>Hosszú, ha SQL kimenetre ír

**Előző szintek:** Az értékek a céltípus alapján csonkolva voltak.

**1.2 szint:** A céltípusba nem illeszkedő értékeket a kimeneti hibaházirendnek megfelelően kezeli a rendszer.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>Rekord- és tömbszerializálás SQL-kimenetre íráskor

**Előző szintek:** A rekordokat "Record"-ként, a tömböket pedig "Tömb" néven írták.

**1.2 szint:** A rekordok és tömbök JSON formátumban vannak szerializálva.

### <a name="strict-validation-of-prefix-of-functions"></a>A funkciók előtagjának szigorú érvényesítése

**Előző szintek:** A függvényelőtagok nem voltak szigorú érvényesítése.

**1.2 szint:** Az Azure Stream Analytics a függvényelőtagok szigorú ellenőrzésével rendelkezik. Ha előtagot ad hozzá egy beépített függvényhez, az hibát okoz. Például`myprefix.ABS(…)` nem támogatott.

Ha előtagot ad hozzá a beépített összesítésekhez, az szintén hibát eredményez. Például `myprefix.SUM(…)` nem támogatott.

A "rendszer" előtag használata a felhasználó által definiált függvényekhez hibát eredményez.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>A tömb és az objektum leengedése kulcstulajdonságokként a Cosmos DB kimeneti adapterben

**Előző szintek:** A tömb- és objektumtípusokat kulcstulajdonságként támogatták.

**1.2 szint:** A tömb- és objektumtípusok már nem támogatottak kulcstulajdonságként.

## <a name="compatibility-level-11"></a>Kompatibilitási szint 1.1

Az 1.1-es kompatibilitási szinten a következő jelentős változtatások vezetnek be:

### <a name="service-bus-xml-format"></a>Service Bus XML formátuma

**1.0 szint:** Az Azure Stream Analytics a DataContractSerializer-t használta, így az üzenet tartalma XML-címkéket tartalmazott. Példa:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**1.1 szint:** Az üzenet tartalma közvetlenül tartalmazza az adatfolyamot további címkék nélkül. Például:`{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>A mezőnevek kis- és nagybetűk megkülönböztetésének megőrzése

**1.0 szint:** A mezőnevek kisbetűsre lettek módosítva, amikor az Azure Stream Analytics-motor feldolgozta.

**1.1 szint:** a kis- és nagybetűk megkülönböztetése megmarad a mezőneveknél, amikor azOkat az Azure Stream Analytics motor dolgozza fel.

> [!NOTE]
> A továbbra is fennálló kis- és nagybetűk megkülönböztetése még nem érhető el az Edge-környezet használatával üzemeltetett Stream Analytic feladatok hoz. Ennek eredményeképpen az összes mezőnév kisbetűssé alakul át, ha a feladat az Edge-en található.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationLetiltva

**1.0 szint:** A CREATE TABLE parancs nem szűrte az eseményeket NaN(Not-a-Number) értékkel. Például Infinity, -Infinity) float oszloptípusban, mert kívül esnek a számok dokumentált tartományán.

**1.1 szint:** CREATE TABLE lehetővé teszi, hogy erős sémát adjon meg. A Stream Analytics motor ellenőrzi, hogy az adatok megfelelnek-e ennek a sémának. Ezzel a modellel a parancs nan értékekkel szűrheti az eseményeket.

### <a name="disable-automatic-upcast-for-datetime-strings-in-json"></a>Automatikus frissítés letiltása a JSON-ban lévő datetime karakterláncok hoz

**1.0 szint:** A JSON-elemző automatikusan feldolgozza a karakterlánc-értékeket dátum/idő/zóna adatokkal DateTime típusúra, majd UTC-re konvertálja. Ez a viselkedés az időzóna-adatok elvesztését eredményezte.

**1.1 szint:** A dátum/idő/zóna adatokkal rendelkező karakterláncértékek nem jelennek meg többé automatikusan datetime típusúra. Ennek eredményeképpen az időzóna-információk megmaradnak.

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics-bemenetek – problémamegoldás](stream-analytics-troubleshoot-input.md)
* [Stream Analytics-erőforrás állapota](stream-analytics-resource-health.md)
