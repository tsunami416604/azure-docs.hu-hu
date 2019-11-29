---
title: Azure Stream Analytics kimenet Cosmos DB
description: Ez a cikk azt ismerteti, hogyan lehet a Azure Stream Analytics használatával menteni a kimenetet a JSON-kimenetek Azure Cosmos DBához, az adatarchiváláshoz és a nem strukturált JSON-adatokat érintő kis késleltetésű lekérdezésekhez.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: aa4ac011a7b6258958ac1ac176fd63b18a4ef856
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74560187"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Azure Stream Analytics kimenet Azure Cosmos DB  
A Stream Analytics a JSON-kimenethez [Azure Cosmos db](https://azure.microsoft.com/services/documentdb/) , így lehetővé teszi az adatarchiválást és az alacsony késésű lekérdezéseket a strukturálatlan JSON-adatokat illetően. Ez a dokumentum a konfiguráció megvalósításának ajánlott eljárásait ismerteti.

Azok számára, akik nem ismerik a Cosmos DB, tekintse meg [Azure Cosmos db tanulási](https://azure.microsoft.com/documentation/learning-paths/documentdb/) tervét az első lépésekhez. 

> [!Note]
> Jelenleg a Azure Stream Analytics csak az **SQL API**-val való Azure Cosmos db-kapcsolatok használatát támogatja.
> Más Azure Cosmos DB API-k még nem támogatottak. Ha Azure Stream Analytics a más API-kkal létrehozott Azure Cosmos DB-fiókokra mutat, előfordulhat, hogy az adathalmazok nem lesznek megfelelően tárolva. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>A Cosmos DB alapjai a kimeneti célként
A Stream Analytics Azure Cosmos DB kimenete lehetővé teszi, hogy a stream feldolgozásának eredményét JSON-kimenetként írja be a Cosmos DB tárolóba. Stream Analytics nem hoz létre tárolókat az adatbázisban, hanem előzetesen létre kell hoznia őket. Így a Cosmos DB tárolók számlázási költségeit Ön vezérli, és így közvetlenül a [Cosmos db API](https://msdn.microsoft.com/library/azure/dn781481.aspx)-k használatával hangolhatja be a tárolók teljesítményét, következetességét és kapacitását.

> [!Note]
> Az engedélyezett IP-címek listájához hozzá kell adni a 0.0.0.0 elemet az Azure Cosmos DB tűzfalon.

A Cosmos DB-tároló lehetőségeinek némelyike alább látható.

## <a name="tune-consistency-availability-and-latency"></a>Konzisztencia, rendelkezésre állás és késés hangolása
Az alkalmazás követelményeinek való megfelelés érdekében Azure Cosmos DB lehetővé teszi az adatbázis és a tárolók finomhangolását, valamint a konzisztencia, a rendelkezésre állás, a késés és az átviteli sebesség közötti kompromisszumok biztosítását. Attól függően, hogy milyen szintű olvasási konzisztencia szükséges a forgatókönyvben az olvasási és írási késéssel szemben, kiválaszthatja az adatbázis-fiókban a konzisztencia szintjét. Az átviteli sebesség javítható a tárolón a kérelmek egységének (RUs) méretezésével. Alapértelmezés szerint a Azure Cosmos DB lehetővé teszi az egyes szifilisz-műveletek szinkron indexelését a tárolón. Ez egy másik hasznos lehetőség a Azure Cosmos DB írási/olvasási teljesítményének szabályozására. További információkért tekintse át az [adatbázis módosítása és a lekérdezési konzisztencia szintjei](../cosmos-db/consistency-levels.md) című cikket.

## <a name="upserts-from-stream-analytics"></a>Upsert Stream Analytics
Stream Analytics integrációja Azure Cosmos DB lehetővé teszi a tárolóban lévő rekordok beszúrását vagy frissítését egy adott dokumentum-azonosító oszlop alapján. Ezt *Upsert*is nevezzük.

A Stream Analytics optimista upsert megközelítést használ, ahol a módosítások csak akkor hajthatók végre, ha a Beszúrás sikertelen a dokumentum AZONOSÍTÓjának ütközésével. A 1,0-es kompatibilitási szint esetében ez a frissítés JAVÍTÁSként kerül végrehajtásra, így lehetővé teszi a dokumentum részleges frissítését, vagyis az új tulajdonságok hozzáadását, illetve egy meglévő tulajdonság cseréjét Növekményesen hajtja végre. A JSON-dokumentum tömb tulajdonságaiban szereplő értékek változásai azonban a teljes tömb felülírását eredményezik, azaz a tömb nincs egyesítve. A 1,2-es upsert-viselkedés a dokumentum beszúrására vagy cseréjére van módosítva. Ezt az alábbi kompatibilitási szint 1,2 szakasza ismerteti.

Ha a bejövő JSON-dokumentum egy meglévő azonosító mezővel rendelkezik, a rendszer automatikusan a Cosmos DB dokumentum-azonosító oszlopként használja a mezőt, és a további írásokat a rendszer a következő helyzetekben hajtja végre:
- az egyedi azonosítók a beszúráshoz vezetnek
- a duplikált azonosítók és a "Document ID" az "ID" értékre van állítva a következőre: upsert
- a duplikált azonosítók és a "Document ID" nincs beállítva hiba, az első dokumentum után

Ha az <i>összes</i> dokumentumot, köztük a DUPLIKÁLt azonosítót is szeretné menteni, nevezze át a lekérdezésben lévő azonosító mezőt (a as kulcsszóval), és Cosmos db hozza létre az azonosító mezőt, vagy cserélje le az azonosítót egy másik oszlop értékére (a as kulcsszóval vagy a "dokumentum azonosítója" beállítás használatával).

## <a name="data-partitioning-in-cosmos-db"></a>Adatparticionálás Cosmos DB
Azure Cosmos DB [korlátlan számú](../cosmos-db/partition-data.md) tároló az adatok particionálásának ajánlott módja, mivel a Azure Cosmos db a számítási feladatok alapján automatikusan méretezi a partíciókat. Korlátlan tárolók írásakor a Stream Analytics annyi párhuzamos írókat használ, mint az előző lekérdezési lépés vagy a bemeneti particionálási séma.
> [!NOTE]
> Jelenleg Azure Stream Analytics csak a legfelső szinten található partíciós kulcsokkal rendelkező korlátlan tárolókat támogatja. A `/region` például támogatott. A beágyazott partíciós kulcsok (például `/region/name`) nem támogatottak. 

A kiválasztható partíciós kulcstól függően a következő _Figyelmeztetés_jelenhet meg:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Fontos, hogy olyan partíciós kulcs-tulajdonságot válasszon, amely számos különböző értékkel rendelkezik, és lehetővé teszi a számítási feladatok egyenletes elosztását ezen értékek között. A particionálás természetes összetevője, amely az azonos partíciós kulcsra vonatkozó kérelmeket egyetlen partíció maximális átviteli sebessége korlátozza. Emellett az ugyanahhoz a partíciós kulcshoz tartozó dokumentumok tárolási mérete 10 GB-ra van korlátozva. Az ideális partíciós kulcs egy olyan, amely gyakran a lekérdezésekben szűrőként jelenik meg, és a megoldás elégséges mértékű, hogy a megoldás skálázható legyen.

A partíciós kulcs a DocumentDB tárolt eljárásaiban és eseményindítókban lévő tranzakciók határa is. A partíciós kulcsot úgy kell kiválasztani, hogy a tranzakciókban együtt előforduló dokumentumok ugyanazt a partíciós kulcs értékét használják. A particionálás a [Cosmos DBban](../cosmos-db/partitioning-overview.md) című cikk részletesen ismerteti a partíciós kulcs kiválasztását.

A rögzített Azure Cosmos DB tárolók esetében a Stream Analytics a teljes méretig nem lehet vertikális felskálázást végezni. A felső határérték 10 GB és 10 000 RU/s átviteli sebesség.  Ha egy rögzített tárolóból egy korlátlan tárolóba kívánja áttelepíteni az adatokból (például egy legalább 1 000 RU/s-val és egy partíciós kulccsal), akkor az [adatáttelepítési eszközt](../cosmos-db/import-data.md) vagy a [hírcsatorna módosítása könyvtárat](../cosmos-db/change-feed.md)kell használnia.

A több rögzített tárolóba való írás lehetősége elavult, és nem ajánlott a Stream Analytics feladatokhoz való horizontális felskálázásra.

## <a name="improved-throughput-with-compatibility-level-12"></a>Javított átviteli sebesség az 1,2-os kompatibilitási szinttel
A 1,2-es kompatibilitási szinttel Stream Analytics támogatja a natív integrációt a Cosmos DBba való tömeges íráshoz. Ez lehetővé teszi a hatékony írást Cosmos DB a maximális átviteli sebesség és a szabályozási kérelmek hatékony kezelése érdekében. A továbbfejlesztett írási mechanizmus egy új kompatibilitási szint alatt érhető el, mivel a upsert viselkedése eltér.  A 1,2 előtt a upsert viselkedése a dokumentum beillesztése vagy egyesítése. A 1,2-es upsert-viselkedés a dokumentum beszúrására vagy cseréjére van módosítva.

A 1,2 előtt egy egyéni tárolt eljárást használ a dokumentumok upsert való tömeges beírásához a Cosmos DBba, ahol a köteg tranzakcióként van írva. Még akkor is, ha egy rekord átmeneti hibát (szabályozást) észlel, a teljes köteget újra kell próbálkozni. Ez a forgatókönyvek viszonylag lassabban is ésszerű szabályozást tesznek elérhetővé. A következő összehasonlítás azt mutatja be, hogy az ilyen feladatok hogyan viselkednek a 1,2-vel.

Az alábbi példa két azonos Stream Analytics feladatot mutat be ugyanabból az Event hub-bemenetből. Mindkét Stream Analytics feladat [teljes mértékben particionálva](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) van egy átadó lekérdezéssel, és azonos CosmosDB-tárolóba ír. A bal oldali metrikák a 1,0 kompatibilitási szinttel konfigurált feladatból származnak, a jobb oldalon pedig a 1,2-es értékkel vannak konfigurálva. A Cosmos DB tároló partíciós kulcsa a bemeneti eseményből származó egyedi GUID.

![Stream Analytics-metrikák összehasonlítása](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Az Event hub bejövő eseményeinek száma 2x-nél nagyobb, mint Cosmos DB tárolók (20000 RUs) a bevitelre vannak konfigurálva, ezért a szabályozás várhatóan Cosmos DB. Azonban a 1,2-es feladatainak következetesen kell megírniuk egy magasabb átviteli sebességgel (kimeneti esemény/perc) és egy alacsonyabb átlagos SU%-os kihasználtsággal. A környezetében ez a különbség néhány tényezőtől függ, például az esemény formátuma, a bemeneti esemény/üzenet mérete, a partíciós kulcsok, a lekérdezés stb.

![Cosmos db-metrikák összehasonlítása](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

A 1,2-as verzióban a Stream Analytics sokkal intelligensebb a rendelkezésre álló átviteli sebesség 100%-ában Cosmos DB és a szabályozás/arány korlátozása miatt igen kevés újraküldéssel. Ez jobb felhasználói élményt nyújt más számítási feladatokhoz, például a tárolón futó lekérdezésekhez. Ha szeretné kipróbálni, hogy az ASA hogyan méretezi a Cosmos DBt az 1k-hoz és a 10k-üzenetekhez tartozó fogadóként, itt egy [Azure Samples-projekt](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) , amely lehetővé teszi ezt.
Vegye figyelembe, hogy Cosmos DB kimeneti átviteli sebesség megegyezik a 1,0-as és a 1,1-as értékkel. Mivel az 1,2-es verzió jelenleg nem alapértelmezett, a portálon vagy a [feladatok létrehozása REST API hívásával](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job)beállíthatja a stream Analyticsi feladatok [kompatibilitási szintjét](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) . *Erősen ajánlott* a 1,2-as kompatibilitási szint használata az ASA-ban Cosmos db.



## <a name="cosmos-db-settings-for-json-output"></a>A JSON-kimenet Cosmos DB beállításai

A Cosmos DB Stream Analytics kimenetként való létrehozásakor a rendszer az alább látható információkat jeleníti meg. Ez a szakasz a tulajdonságok definíciójának magyarázatát tartalmazza.

![documentdb stream Analytics kimeneti képernyő](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Mező           | Leírás|
|-------------   | -------------|
|Kimeneti alias    | Alias, amely a kimenetet a ASA-lekérdezésben utalja.|
|Előfizetés    | Válassza ki az Azure-előfizetést.|
|Fiókazonosító      | A Azure Cosmos DB fiók neve vagy végpontjának URI azonosítója.|
|Fiók kulcsa     | A Azure Cosmos DB-fiókhoz tartozó megosztott elérési kulcs.|
|Adatbázis        | A Azure Cosmos DB adatbázis neve.|
|Tárolónév | A használandó tároló neve. `MyContainer` egy érvényes bemenet – egy `MyContainer` nevű tárolónak léteznie kell.  |
|Dokumentum azonosítója     | Választható. A kimeneti események oszlopának neve, amelyet az INSERT vagy a Update műveleteken alapuló egyedi kulcsként kell használni. Ha üresen hagyja, a rendszer az összes eseményt beszúrja, és nem rendelkezik frissítési lehetőséggel.|

Miután konfigurálta a Cosmos DB kimenetét, a lekérdezésben a [into utasítás](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics)céljaként is használható. Ha Cosmos DB kimenetet használ, [a partíciós kulcsot explicit módon be kell állítani](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks). A kimeneti rekordnak tartalmaznia kell egy kis-és nagybetűket megkülönböztető oszlopot, amely a Cosmos DB partíciós kulcsának neve után van. Ha nagyobb párhuzamos szeretne elérni, az utasításhoz ugyanazt az oszlopot használó [Partition by záradékra](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) lehet szükség.

**Példa lekérdezésre**:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Hibakezelés és újrapróbálkozások

Átmeneti hiba esetén a szolgáltatás nem áll rendelkezésre, vagy szabályozást végez az események Cosmos DB történő küldése közben, Stream Analytics az ismételt próbálkozások határozatlan ideig nem sikerült végrehajtani a műveletet. Bizonyos hibák előfordulása esetén a rendszer nem próbálkozik újra. Ezek a következők:

- Nem engedélyezett (http-hibakód: 401)
- NotFound (http-hibakód: 404)
- Tiltott (403-es HTTP-hibakód)
- BadRequest (http-hibakód: 400)
