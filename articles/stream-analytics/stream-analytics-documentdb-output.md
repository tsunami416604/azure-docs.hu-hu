---
title: Az Azure Stream Analytics kimenete az Azure Cosmos DB-hez
description: Ez a cikk ismerteti, hogyan azure Stream Analytics használatával az Azure Cosmos DB a JSON-kimenet, az adatok archiválása és a nem strukturált JSON-adatok alacsony késésű lekérdezések.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/2/2020
ms.custom: seodec18
ms.openlocfilehash: e58e36b3caa5a5ecd137cb9cb61dad7ddb95ff3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254442"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Az Azure Stream Analytics kimenete az Azure Cosmos DB-hez  
Az Azure Stream Analytics megcélozhatja [az Azure Cosmos DB-t](https://azure.microsoft.com/services/documentdb/) a JSON-kimenethez, lehetővé téve az adatarchiválást és a kis késésű lekérdezéseket a strukturálatlan JSON-adatokon. Ez a dokumentum a konfiguráció megvalósításának néhány gyakorlati tanácsát ismerteti.

Ha nem ismeri az Azure Cosmos DB-t, az első lépésekhez tekintse meg az [Azure Cosmos DB dokumentációját.](https://docs.microsoft.com/azure/cosmos-db/) 

> [!Note]
> A Stream Analytics jelenleg csak az *SQL API-n*keresztül támogatja az Azure Cosmos DB-vel való kapcsolatot.
> Más Azure Cosmos DB API-k még nem támogatottak. Ha a Stream Analytics-et más API-kkal létrehozott Azure Cosmos DB-fiókokra irányítja, előfordulhat, hogy az adatok nem megfelelően vannak tárolva. 

## <a name="basics-of-azure-cosmos-db-as-an-output-target"></a>Az Azure Cosmos DB alaperedményei kimeneti célként
Az Azure Cosmos DB kimenet a Stream Analytics lehetővé teszi az adatfolyam-feldolgozási eredmények írása JSON-kimenetként az Azure Cosmos DB tárolókba. 

A Stream Analytics nem hoz létre tárolókat az adatbázisban. Ehelyett megköveteli, hogy hozza létre őket előre. Ezután szabályozhatja az Azure Cosmos DB-tárolók számlázási költségeit. A tárolók teljesítményét, konzisztenciáját és kapacitását közvetlenül is finomhangolhatja az [Azure Cosmos DB API-k](https://msdn.microsoft.com/library/azure/dn781481.aspx)használatával.

> [!Note]
> Hozzá kell adnia a 0.0.0.0-s értéket az Azure Cosmos DB tűzfalon engedélyezett IP-k listájához.

A következő szakaszok ismertetik az Azure Cosmos DB tárolóbeállításait.

## <a name="tuning-consistency-availability-and-latency"></a>Konzisztencia, rendelkezésre állás és késés finomhangolása
Az alkalmazás követelményeinek megfelelően az Azure Cosmos DB lehetővé teszi az adatbázis és a tárolók finomhangolását, valamint a konzisztencia, a rendelkezésre állás, a késés és az átviteli képesség közötti kompromisszumot. 

Attól függően, hogy milyen szintű olvasási konzisztencia a forgatókönyv olvasási és írási késés, kiválaszthatja a konzisztencia szintet az adatbázis-fiókban. Javíthatja az átviteli teljesítmény a kérelemegységek (RUs) a tárolón. 

Az Azure Cosmos DB alapértelmezés szerint is lehetővé teszi a szinkronizálási indexelést minden CRUD-műveleten a tárolóhoz. Ez egy másik hasznos lehetőség az Azure Cosmos DB írási és olvasási teljesítményének szabályozására. 

További információt az [Adatbázis és a lekérdezés konzisztenciaszintjeinek módosítása](../cosmos-db/consistency-levels.md) című cikkben talál.

## <a name="upserts-from-stream-analytics"></a>Upserts a Stream Analytics-től
A Stream Analytics és az Azure Cosmos DB szolgáltatással való streamelés lehetővé teszi rekordok beszúrását vagy frissítését a tárolóban egy adott **Document ID** oszlop alapján. Ez is *nevezik upsert*.

A Stream Analytics optimista upsert megközelítést alkalmaz. A frissítések csak akkor történnek, ha a beszúrás dokumentumazonosító-ütközéssel sikertelen. 

Az 1.0-s kompatibilitási szinttel a Stream Analytics patch műveletként hajtja végre a frissítést, így lehetővé teszi a dokumentum részleges frissítését. A Stream Analytics új tulajdonságokat ad hozzá, vagy növekményesen cserél le egy meglévő tulajdonságot. A JSON-dokumentumban lévő tömbtulajdonságok értékeinek módosításai azonban a teljes tömb felülírását eredményezik. Ez azt illeti, a tömb nincs egyesítve. 

Az 1.2-es beállítással a upsert viselkedése módosul a dokumentum beszúrásához vagy cseréjéhez. Az 1.2-es kompatibilitási szintről szóló későbbi szakasz ezt a viselkedést ismerteti.

Ha a bejövő JSON-dokumentum rendelkezik egy meglévő azonosító mezővel, akkor a mező automatikusan az Azure Cosmos DB **Dokumentumazonosító** oszlopaként lesz használva. A későbbi írásokat a következőképpen kezeljük, ami az alábbi helyzetek egyikéhez vezet:

- Az egyedi azonosítók beszúráshoz vezetnek.
- Az **azonosítóra** beállított duplikált azonosítók és **dokumentumazonosítók** upsert-hez vezetnek.
- Az ismétlődő azonosítók és **a dokumentumazonosító** nem állítanak be hibát az első dokumentum után.

Ha *az összes* dokumentumot menteni szeretné, beleértve azokat is, amelyek ismétlődő azonosítóval rendelkeznek, nevezze át a lekérdezés azonosítómezőjét (az **AS** kulcsszó használatával). Hagyja, hogy az Azure Cosmos DB hozza létre az azonosító mezőt, vagy cserélje le az azonosítót egy másik oszlop értékére (az **AS** kulcsszó használatával vagy a **Document ID** beállítás használatával).

## <a name="data-partitioning-in-azure-cosmos-db"></a>Adatparticionálás az Azure Cosmos DB-ben
Az Azure Cosmos DB automatikusan skálázhatja a partíciókat a számítási feladatok alapján. Ezért azt [javasoljuk, korlátlan](../cosmos-db/partition-data.md) tárolók, mint az adatok particionálása. Amikor a Stream Analytics korlátlan tárolókba ír, annyi párhuzamos írót használ, mint az előző lekérdezési lépés vagy a bemeneti particionálási séma.

> [!NOTE]
> Az Azure Stream Analytics csak korlátlan tárolókat támogat partíciós kulcsokkal a legfelső szinten. Például `/region` támogatott. A beágyazott partíciókulcsok (például) `/region/name`nem támogatottak. 

A választott partíciókulcstól függően a _következő figyelmeztetés_jelenhet meg:

`CosmosDB Output contains multiple rows and just one row per partition key. If the output latency is higher than expected, consider choosing a partition key that contains at least several hundred records per partition key.`

Fontos, hogy válasszon egy partíciókulcs-tulajdonságot, amely számos különböző értékekkel rendelkezik, és amely lehetővé teszi a számítási feladatok egyenletes elosztását ezek között az értékek között. A particionálás természetes összetevőjeként az azonos partíciókulcsot tartalmazó kérelmeket egyetlen partíció maximális átviteli teljesítménye korlátozza. 

Az azonos partíciókulcshoz tartozó dokumentumok tárolási mérete 10 GB-ra korlátozódik. Az ideális partíciókulcs az egyik, amely gyakran jelenik meg szűrőként a lekérdezésekben, és elegendő számosságú ahhoz, hogy a megoldás méretezhető.

A partíciókulcs is a határ a tárolt eljárások és az Azure Cosmos DB eseményindítók tranzakciók határán. Válassza ki a partíciókulcsot, hogy a tranzakciókban együtt előforduló dokumentumok azonos partíciókulcs-értékkel rendelkezhessenek. A cikk [particionálás az Azure Cosmos DB](../cosmos-db/partitioning-overview.md) további részleteket a partíciókulcs kiválasztásával kapcsolatos további részleteket tartalmaz.

A rögzített Azure Cosmos DB-tárolók esetén a Stream Analytics nem teszi lehetővé a felskálázást vagy a kiskálázást, miután megteltek. 10 GB-os és 10 000 RU/s átviteli korláttal rendelkeznek. Ha az adatokat rögzített tárolóból korlátlan tárolóba szeretné áttelepíteni (például legalább 1000 RU/s-os tárolóval és partíciókat tartalmazótárolóra), használja az [adatáttelepítési eszközt](../cosmos-db/import-data.md) vagy a [módosítási hírcsatorna-könyvtárat.](../cosmos-db/change-feed.md)

A több rögzített tárolóba való írás lehetősége elavult. Nem javasoljuk, hogy a Stream Analytics-feladat horizontális felskálázásához.

## <a name="improved-throughput-with-compatibility-level-12"></a>Továbbfejlesztett átviteli teljesítmény az 1.2-es kompatibilitási szinttel
Az 1.2-es kompatibilitási szinttel a Stream Analytics támogatja az Azure Cosmos DB tömeges írásához szükséges natív integrációt. Ez az integráció lehetővé teszi a hatékony írást az Azure Cosmos DB számára, miközben maximalizálja az átviteli hatóhangot és hatékonyan kezeli a sávszélesség-kezelési kérelmeket. 

A továbbfejlesztett írási mechanizmus egy új kompatibilitási szint alatt érhető el a upsert viselkedés különbsége miatt. Az 1.2 előtti szintekkel a upsert viselkedés a dokumentum beszúrása vagy egyesítése. Az 1.2-es beállítással a upsert viselkedése módosul a dokumentum beszúrásához vagy cseréjéhez.

Az 1.2 előtti szintekkel a Stream Analytics egy egyéni tárolt eljárást használ a partíciókulcsonkénti upsert dokumentumok tömeges sérelmére az Azure Cosmos DB-be. Ott a köteg tranzakcióként van megírva. Még akkor is, ha egyetlen rekord átmeneti hibát (szabályozást) talál el, az egész köteget újra meg kell próbálni. Ez teszi a forgatókönyvek még ésszerű fojtás viszonylag lassú.

A következő példa két azonos Stream Analytics-feladatot mutat be, amelyek ugyanabból az Azure Event Hubs-bemenetből olvasnak. Mindkét Stream Analytics-feladat [teljesen particionált](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) egy átmenő lekérdezést, és írja az azonos Azure Cosmos DB-tárolók. A bal oldali metrikák az 1.0-s kompatibilitási szinttel konfigurált feladatból származnak. A jobb oldali metrikák 1.2-es értékkel vannak konfigurálva. Az Azure Cosmos DB-tároló partíciókulcsa egy egyedi GUID, amely a bemeneti eseményből származik.

![A Stream Analytics mérőszámainak összehasonlítása](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Az Event Hubs bejövő eseménysebessége kétszer nagyobb, mint az Azure Cosmos DB-tárolók (20 000 km-es) van konfigurálva, így az Azure Cosmos DB szabályozása várható. Az 1.2-es feladat azonban következetesen magasabb átviteli sebességgel (kimeneti események percenként) és alacsonyabb átlagos SU%-kihasználtsú írással ír. A környezetben ez a különbség néhány további tényezőtől függ. Ezek közé tartozik az eseményformátum, a beviteli esemény/üzenet méret, a partíciókulcsok és a lekérdezés kiválasztása.

![Az Azure Cosmos DB-mérőszámok összehasonlítása](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Az 1.2-es verzióval a Stream Analytics intelligensebb az Azure Cosmos DB rendelkezésre álló átviteli sebességének 100 százalékában, a sávszélesség-szabályozás vagy a sebességkorlátozás miatt imitáló újraküldésekkel. Ez jobb élményt nyújt más számítási feladatokhoz, például a tárolón egyidejűleg futó lekérdezésekhez. Ha szeretné látni, hogyan osztódik ki a Stream Analytics az Azure Cosmos DB-vel, mint 1000–10 000 üzenet másodpercenkénti fogadója, próbálja ki [ezt az Azure-mintaprojektet.](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb)

Az Azure Cosmos DB kimenet átviteli teljesítménye megegyezik az 1.0-s és az 1.1-es. *Javasoljuk,* hogy használja az 1.2-es kompatibilitási szintet a Stream Analytics és az Azure Cosmos DB számára.

## <a name="azure-cosmos-db-settings-for-json-output"></a>Az Azure Cosmos DB beállításai a JSON-kimenethez

Az Azure Cosmos DB használatával kimenetként a Stream Analytics a következő információt kér.

![Az Azure Cosmos DB kimeneti adatfolyamának információs mezői](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Mező           | Leírás|
|-------------   | -------------|
|Kimeneti alias    | Egy alias, hogy olvassa el ezt a kimenetet a Stream Analytics lekérdezésben.|
|Előfizetés    | Az Azure-előfizetés.|
|Fiókazonosító      | Az Azure Cosmos DB-fiók neve vagy végpontURI-ja.|
|Fiókkulcs     | Az Azure Cosmos DB-fiók megosztott hozzáférési kulcsa.|
|Adatbázis        | Az Azure Cosmos DB adatbázis neve.|
|Tárolónév | A tároló neve, `MyContainer`például . Egy elnevezett `MyContainer` tárolónak léteznie kell.  |
|Dokumentum azonosítója     | Választható. Az oszlopneve a kimeneti eseményekben, amely et az egyedi kulcsként használják, amelyen a beszúrási vagy frissítési műveleteknek alapulniuk kell. Ha üresen hagyja, az összes esemény bekerül a programba, frissítési lehetőség nélkül.|

Az Azure Cosmos DB kimenet konfigurálása után használhatja azt a lekérdezésben egy [INTO utasítás célként.](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics) Ha így azure Cosmos DB-kimenetet használ, [a partíciókulcsot explicit módon kell beállítani.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#partitions-in-sources-and-sinks) 

A kimeneti rekordnak tartalmaznia kell egy kis- és nagybetűket megkülönböztető oszlopot, amely az Azure Cosmos DB partíciókulcsa után lett elnevezve. A nagyobb párhuzamosság eléréséhez előfordulhat, hogy az utasításhoz olyan [PARTITION BY záradékra](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) van szükség, amely ugyanazt az oszlopot használja.

Íme egy mintalekérdezés:

```SQL
    SELECT TollBoothId, PartitionId
    INTO CosmosDBOutput
    FROM Input1 PARTITION BY PartitionId
``` 

## <a name="error-handling-and-retries"></a>Hibakezelés és újrapróbálkozások

Ha egy átmeneti hiba, szolgáltatás elérhetetlensége vagy szabályozása történik, miközben a Stream Analytics eseményeket küld az Azure Cosmos DB-nek, a Stream Analytics határozatlan ideig újrapróbálkozik a művelet sikeres befejezéséhez. De nem kísérli meg újrapróbálkozni a következő hibákesetén:

- Nem engedélyezett (HTTP-hibakód: 401)
- Nem található (HTTP-hibakód: 404)
- Tiltott (HTTP hibakód 403)
- BadRequest (HTTP-hibakód: 400)
