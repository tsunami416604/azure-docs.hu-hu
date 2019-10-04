---
title: Azure Stream Analytics-kimenet a Cosmos DB-hez
description: Ez a cikk ismerteti az Azure Stream Analytics használatával az Azure Cosmos DB JSON kimeneti adatok archiválása és kis késleltetésű lekérdezéseket a strukturálatlan JSON-adatok mentése.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/11/2019
ms.custom: seodec18
ms.openlocfilehash: 52bbb52b13a3606e3ddc8deca2da8505233c9352
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70062015"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Az Azure Cosmos DB Azure Stream Analytics-kimenet  
Stream Analytics célként [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) JSON-kimenet, az adatok archiválás és kis késleltetésű lekérdezéseket a strukturálatlan JSON-adatok engedélyezése. Ez a dokumentum áttekint néhány ajánlott eljárást a konfiguráció implementálása a.

Azok számára, aki ismeri az, Cosmos DB, vessen egy pillantást [képzési terv az Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) a kezdéshez. 

> [!Note]
> Jelenleg az Azure Stream Analytics csak kapcsolatot támogat az Azure Cosmos DB használatával **SQL API**.
> Más Azure Cosmos DB API-k még nem támogatott. Ha pont Azure Stream Analytics az Azure Cosmos DB-fiókokhoz létrehozott más API-kkal, az adatok esetleg nem megfelelően tárolni. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Cosmos DB-kimenet célként alapjai
A Stream Analytics Azure Cosmos DB kimenete lehetővé teszi, hogy a stream feldolgozásának eredményét JSON-kimenetként írja be a Cosmos DB tárolóba. Stream Analytics nem hoz létre tárolókat az adatbázisban, hanem előzetesen létre kell hoznia őket. Így a Cosmos DB tárolók számlázási költségeit Ön vezérli, és így közvetlenül a [Cosmos db API](https://msdn.microsoft.com/library/azure/dn781481.aspx)-k használatával hangolhatja be a tárolók teljesítményét, következetességét és kapacitását.

> [!Note]
> Az engedélyezett IP-címek listájához hozzá kell adni a 0.0.0.0 elemet az Azure Cosmos DB tűzfalon.

A Cosmos DB-tároló lehetőségeinek némelyike alább látható.

## <a name="tune-consistency-availability-and-latency"></a>Konzisztencia, a rendelkezésre állás és a késleltetés hangolása
Az alkalmazás követelményeinek való megfelelés érdekében Azure Cosmos DB lehetővé teszi az adatbázis és a tárolók finomhangolását, valamint a konzisztencia, a rendelkezésre állás, a késés és az átviteli sebesség közötti kompromisszumok biztosítását. Attól függően, hogy milyen szintű olvasás következetes ellen a forgatókönyv igényeinek olvassa el, és írási késés, választhatja a konzisztenciaszint a fiókot a. Az átviteli sebesség javítható a tárolón a kérelmek egységének (RUs) méretezésével. Alapértelmezés szerint a Azure Cosmos DB lehetővé teszi az egyes szifilisz-műveletek szinkron indexelését a tárolón. Ez a szabályozhatja az írási/olvasási teljesítmény az Azure Cosmos DB egy másik hasznos lehetőség. További információkért tekintse át a [módosítása az adatbázis és a lekérdezés konzisztenciaszintek](../cosmos-db/consistency-levels.md) cikk.

## <a name="upserts-from-stream-analytics"></a>A Stream Analytics Upserts
Stream Analytics integrációja Azure Cosmos DB lehetővé teszi a tárolóban lévő rekordok beszúrását vagy frissítését egy adott dokumentum-azonosító oszlop alapján. Ez is nevezik egy *Upsert*.

Stream Analytics egy optimista upsert módszert használ, ahol frissítéseit csak végzett insert dokumentumazonosító ütközést tartalmazó meghibásodása esetén. A 1,0-es kompatibilitási szint esetében ez a frissítés JAVÍTÁSként kerül végrehajtásra, így lehetővé teszi a dokumentum részleges frissítését, vagyis az új tulajdonságok hozzáadását, illetve egy meglévő tulajdonság cseréjét Növekményesen hajtja végre. A tömb tulajdonságokat a JSON-dokumentum eredményt, a tömb első felülírja, teljes tömb a módosítások azonban nem részeként történik. A 1,2-es upsert-viselkedés a dokumentum beszúrására vagy cseréjére van módosítva. Ezt az alábbi kompatibilitási szint 1,2 szakasza ismerteti.

Ha a bejövő JSON-dokumentum egy meglévő azonosító mezőben, hogy a mező automatikusan használja a dokumentumazonosító oszlop Cosmos DB-ben, és bármely érkező későbbi írások kezeli, így az ezekben a helyzetekben egyik vezető rendelkezik:
- egyedi azonosítók érdeklődő beszúrása
- ismétlődő azonosítók és "Dokumentumazonosító" értékre az "ID" vezet az upsert művelet
- ismétlődő azonosítók és a "Dokumentumazonosító" nem hiba, az első dokumentum után vezet beállítása

Ha menteni szeretné <i>összes</i> duplikált azonosítójú, azokat is beleértve dokumentumok nevezze át a mezőt a a lekérdezés (AS kulcsszóval), és lehetővé teszik a Cosmos DB létrehozása az azonosító mezőben, vagy egy másik oszlop értékét cserélje le a azonosítója (AS kulcsszó használatával vagy a "Dokumentumazonosító" beállítás használatával).

## <a name="data-partitioning-in-cosmos-db"></a>Adatparticionálás Cosmos DB-ben
Azure Cosmos DB [korlátlan számú](../cosmos-db/partition-data.md) tároló az adatok particionálásának ajánlott módja, mivel a Azure Cosmos db a számítási feladatok alapján automatikusan méretezi a partíciókat. Korlátlan tárolók írásakor a Stream Analytics annyi párhuzamos írókat használ, mint az előző lekérdezési lépés vagy a bemeneti particionálási séma.
> [!NOTE]
> Jelenleg Azure Stream Analytics csak a legfelső szinten található partíciós kulcsokkal rendelkező korlátlan tárolókat támogatja. Ha például `/region` használata támogatott. A beágyazott partíciókulcsok (pl. `/region/name`) használata nem támogatott. 

A kiválasztható partíciós kulcstól függően a következő _Figyelmeztetés_jelenhet meg:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Fontos, hogy olyan partíciós kulcs-tulajdonságot válasszon, amely számos különböző értékkel rendelkezik, és lehetővé teszi a számítási feladatok egyenletes elosztását ezen értékek között. A particionálás természetes összetevője, amely az azonos partíciós kulcsra vonatkozó kérelmeket egyetlen partíció maximális átviteli sebessége korlátozza. Emellett az ugyanahhoz a partíciós kulcshoz tartozó dokumentumok tárolási mérete 10 GB-ra van korlátozva. Az ideális partíciós kulcs egy olyan, amely gyakran a lekérdezésekben szűrőként jelenik meg, és a megoldás elégséges mértékű, hogy a megoldás skálázható legyen.

A partíciós kulcs a DocumentDB tárolt eljárásaiban és eseményindítókban lévő tranzakciók határa is. A partíciós kulcsot úgy kell kiválasztani, hogy a tranzakciókban együtt előforduló dokumentumok ugyanazt a partíciós kulcs értékét használják. A particionálás [](../cosmos-db/partitioning-overview.md) a Cosmos DBban című cikk részletesen ismerteti a partíciós kulcs kiválasztását.

A rögzített Azure Cosmos DB tárolók esetében a Stream Analytics a teljes méretig nem lehet vertikális felskálázást végezni. 10 GB-os és 10 000 RU/s átviteli sebesség felső korlátja rendelkeznek.  Az adatok áttelepíthetők rögzített tárolóba (például egy legalább 1000 RU/s és a egy partíciókulcsot) korlátlan tárolóra, kell használnia a [adatáttelepítési eszköz](../cosmos-db/import-data.md) vagy a [módosítási hírcsatorna könyvtár](../cosmos-db/change-feed.md).

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



## <a name="cosmos-db-settings-for-json-output"></a>JSON-kimenet a cosmos DB beállításai

Cosmos DB létrehozása a Stream Analytics kimenetként állít elő, információkat az alább látható módon egy parancssort. Ez a szakasz a Tulajdonságok definíció magyarázattal szolgál.

![a documentdb a stream analytics kimeneti képernyő](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Mező           | Leírás|
|-------------   | -------------|
|Kimeneti alias    | Alias, amely a kimenetet a ASA-lekérdezésben utalja.|
|Subscription    | Válassza ki az Azure-előfizetést.|
|Fiókazonosító      | A Azure Cosmos DB fiók neve vagy végpontjának URI azonosítója.|
|Fiókkulcs     | A Azure Cosmos DB-fiókhoz tartozó megosztott elérési kulcs.|
|Adatbázis        | A Azure Cosmos DB adatbázis neve.|
|Tároló neve | A használandó tároló neve. `MyContainer`egy minta érvényes bemenet – egy nevű `MyContainer` tárolónak léteznie kell.  |
|Dokumentumazonosító     | Választható. A kimeneti eseményekben a melyik beszúrási vagy frissítési műveletek kell alapozni egyedi kulcsaként használt oszlopok neve. Ha üres, az összes esemény kerül beillesztésre, nincs frissítési lehetőség.|

## <a name="error-handling-and-retries"></a>Hibakezelés és újrapróbálkozások

Átmeneti hiba esetén a szolgáltatás nem áll rendelkezésre, vagy szabályozást végez az események Cosmos DB történő küldése közben, Stream Analytics az ismételt próbálkozások határozatlan ideig nem sikerült végrehajtani a műveletet. Bizonyos hibák előfordulása esetén a rendszer nem próbálkozik újra. Ezek a következők:

- Nem engedélyezett (http-hibakód: 401)
- NotFound (http-hibakód: 404)
- Tiltott (403-es HTTP-hibakód)
- BadRequest (http-hibakód: 400)
