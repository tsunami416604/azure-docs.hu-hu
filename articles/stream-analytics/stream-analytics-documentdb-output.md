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
ms.openlocfilehash: 734cf09869e5a2df5f9a505a3cb8ccc7bc2338d5
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470406"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Az Azure Cosmos DB Azure Stream Analytics-kimenet  
Stream Analytics célként [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) JSON-kimenet, az adatok archiválás és kis késleltetésű lekérdezéseket a strukturálatlan JSON-adatok engedélyezése. Ez a dokumentum áttekint néhány ajánlott eljárást a konfiguráció implementálása a.

Azok számára, aki ismeri az, Cosmos DB, vessen egy pillantást [képzési terv az Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) a kezdéshez. 

> [!Note]
> Jelenleg az Azure Stream Analytics csak kapcsolatot támogat az Azure Cosmos DB használatával **SQL API**.
> Más Azure Cosmos DB API-k még nem támogatott. Ha pont Azure Stream Analytics az Azure Cosmos DB-fiókokhoz létrehozott más API-kkal, az adatok esetleg nem megfelelően tárolni. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Cosmos DB-kimenet célként alapjai
A Stream Analytics az Azure Cosmos DB kimeneti adatfolyam-feldolgozási eredményeket, JSON-kimenet a Cosmos DB próbaidőszakában való írása lehetővé teszi. Stream Analytics nem hoz létre gyűjteményeket az adatbázisban, ehelyett határozhat meg kell előre létrehoznia őket. Ez az, hogy az Ön által vezérelt Cosmos DB-gyűjtemények a számlázási költségeket, és úgy, hogy a teljesítmény, a konzisztencia és a kapacitás a gyűjtemények közvetlenül hangolhassa a [Cosmos DB API-k](https://msdn.microsoft.com/library/azure/dn781481.aspx).

> [!Note]
> Hozzá kell adnia 0.0.0.0 az engedélyezett IP-címek listáját az Azure Cosmos DB-tűzfalon.

A Cosmos DB adatgyűjtési beállítások némelyike lásd lent.

## <a name="tune-consistency-availability-and-latency"></a>Konzisztencia, a rendelkezésre állás és a késleltetés hangolása
Az alkalmazás követelményeinek megfelelően, az Azure Cosmos DB teszi lehetővé az adatbázis és a gyűjtemények és győződjön meg arról, konzisztencia, a rendelkezésre állás és a késleltetés közti kompromisszummal. Attól függően, hogy milyen szintű olvasás következetes ellen a forgatókönyv igényeinek olvassa el, és írási késés, választhatja a konzisztenciaszint a fiókot a. Is alapértelmezés szerint az Azure Cosmos DB lehetővé teszi, hogy szinkron indexelő egyes CRUD-műveletek a gyűjteményhez. Ez a szabályozhatja az írási/olvasási teljesítmény az Azure Cosmos DB egy másik hasznos lehetőség. További információkért tekintse át a [módosítása az adatbázis és a lekérdezés konzisztenciaszintek](../cosmos-db/consistency-levels.md) cikk.

## <a name="upserts-from-stream-analytics"></a>A Stream Analytics Upserts
Stream Analytics-integráció az Azure Cosmos DB lehetővé teszi a Beszúrás, vagy frissíthet rekordokat a gyűjtemény adott dokumentumazonosító oszlop alapján. Ez is nevezik egy *Upsert*.

Stream Analytics egy optimista upsert módszert használ, ahol frissítéseit csak végzett insert dokumentumazonosító ütközést tartalmazó meghibásodása esetén. A kompatibilitási szint 1.0 a frissítés történik, PATCH, így részleges frissítéseit a dokumentumot, amely lehetővé teszi, az új tulajdonságok vagy lecseréli a meglévő tulajdonságot Növekményesen történik. A tömb tulajdonságokat a JSON-dokumentum eredményt, a tömb első felülírja, teljes tömb a módosítások azonban nem részeként történik. Az 1.2-es upsert viselkedés úgy módosul, helyezze be vagy cserélje le a dokumentumot. Ez az erről a kompatibilitási szint 1.2 részben olvashatja.

Ha a bejövő JSON-dokumentum egy meglévő azonosító mezőben, hogy a mező automatikusan használja a dokumentumazonosító oszlop Cosmos DB-ben, és bármely érkező későbbi írások kezeli, így az ezekben a helyzetekben egyik vezető rendelkezik:
- egyedi azonosítók érdeklődő beszúrása
- ismétlődő azonosítók és "Dokumentumazonosító" értékre az "ID" vezet az upsert művelet
- ismétlődő azonosítók és a "Dokumentumazonosító" nem hiba, az első dokumentum után vezet beállítása

Ha menteni szeretné <i>összes</i> duplikált azonosítójú, azokat is beleértve dokumentumok nevezze át a mezőt a a lekérdezés (AS kulcsszóval), és lehetővé teszik a Cosmos DB létrehozása az azonosító mezőben, vagy egy másik oszlop értékét cserélje le a azonosítója (AS kulcsszó használatával vagy a "Dokumentumazonosító" beállítás használatával).

## <a name="data-partitioning-in-cosmos-db"></a>Adatparticionálás Cosmos DB-ben
Az Azure Cosmos DB [korlátlan](../cosmos-db/partition-data.md) tárolók az ajánlott módszer számára az adatok particionálása, az Azure Cosmos DB automatikusan méretezi a számítási feladatok alapján. Korlátlan tárolók írásakor a Stream Analytics tetszőleges számú párhuzamos írók előző lekérdezési lépés vagy bemeneti particionálási sémát használ.
> [!Note]
> Jelenleg az Azure Stream Analytics csak támogatja a korlátlan tárolók partíciókulcsok a legfelső szinten. Ha például `/region` használata támogatott. A beágyazott partíciókulcsok (pl. `/region/name`) használata nem támogatott. 

Rögzített Azure Cosmos DB-gyűjtemények a Stream Analytics lehetővé teszi a vertikális vagy horizontális, amint teljes semmilyen módon nem lehet. 10 GB-os és 10 000 RU/s átviteli sebesség felső korlátja rendelkeznek.  Az adatok áttelepíthetők rögzített tárolóba (például egy legalább 1000 RU/s és a egy partíciókulcsot) korlátlan tárolóra, kell használnia a [adatáttelepítési eszköz](../cosmos-db/import-data.md) vagy a [módosítási hírcsatorna könyvtár](../cosmos-db/change-feed.md).

Több állandó tároló való írás hamarosan elavulttá válik, és nem az ajánlott módszer a horizontális felskálázás a Stream Analytics-feladatot. A cikk [particionálás és skálázás az, Cosmos DB](../cosmos-db/sql-api-partition-data.md) további részleteket tartalmaz.

## <a name="improved-throughput-with-compatibility-level-12"></a>Nagyobb átviteli sebességet a kompatibilitási szint 1.2-es verziójával
-Es kompatibilitási szintű 1.2-es a Stream Analytics támogatja a natív integráció tömeges az Cosmos DB-be írni. Ez lehetővé teszi a írnak hatékonyan a Cosmos DB a lehető legnagyobb átviteli sebességet, és hatékonyan leíró szabályozási kérelmeket. A továbbfejlesztett írása mechanizmus az upsert viselkedése miatt egy új kompatibilitási szint alatt érhető el.  1.2-es, mielőtt az upsert működése beszúrása vagy egyesíteni a dokumentumot. Az 1.2-es upserts viselkedés úgy módosul, helyezze be vagy cserélje le a dokumentumot. 

1.2-es, mielőtt használja Cosmos DB, ahol egy kötegelt írt tranzakcióként történő tömeges upsert dokumentumok partíciókulcson egyéni tárolt eljárás. Akkor is, ha egyetlen rekordot eléri a (szabályozás) átmeneti hiba, az egész batch meg kell ismételni. Ez arról, hogy az még elfogadható viszonylag lassabban szabályozási forgatókönyvek. A következő összehasonlító jeleníti meg, hogyan viselkednek a ilyen feladatok 1.2-es verziójával.

Telepítés alatt látható két azonos Stream Analytics-feladatok ugyanazon bemeneti (eseményközpontból) olvasásakor. Mindkét Stream Analytics-feladatok [teljes particionált](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#embarrassingly-parallel-jobs) továbbító lekérdezés, valamint egy írása azonos cosmos DB-gyűjteményekhez. A bal oldali metrikák a feladat-es kompatibilitási szintű 1.0 konfigurált, és azokat a jobb oldali meg van adva az 1.2-es. A cosmos DB-gyűjtemények partíciókulcs egy egyedi GUID azonosítót a bemeneti eseményt érkező.

![a Stream analytics mérőszámok összehasonlítása](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-3.png)

Eseményközpont bejövő események száma 2 x nagyobb, mint a Cosmos DB-gyűjtemények (20K ru-k) konfigurált kezelni, ezért szabályozás várt Cosmos DB-ben. Az 1.2-es, a feladat azonban egy magasabb átviteli sebesség (kimeneti események/perc), és a egy alacsonyabb átlagos SU százalékos kihasználtsága folyamatosan írja. A környezetben ezt a különbséget függ, néhány további tényező befolyásolja, például a választott esemény formátum, a bemeneti eseményüzenet mérete, a partíciókulcsok, a lekérdezés stb.

![cosmos db mérőszámok összehasonlítása](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-2.png)

Az 1.2-es a Stream Analytics a 100 %-a szabályozás/sebességkorlátozással nagyon kevés újraküldéseinek a Cosmos DB-ben elérhető átviteli sebesség használó intelligens. Más számítási feladatok, például a lekérdezések a gyűjtemény egy időben futó jobb felhasználói élményt biztosít. Abban az esetben kell próbálja ki, hogyan ASA elvégzi a horizontális felskálázást Cosmos DB-vel, a fogadó 1k – 10k az üzenetek/másodperc, Íme egy [azure-minták projekt](https://github.com/Azure-Samples/streaming-at-scale/tree/master/eventhubs-streamanalytics-cosmosdb) , amely lehetővé teszi, hogy.
Vegye figyelembe, hogy a Cosmos DB kimeneti átviteli sebesség megegyezik 1.0 és 1.1. 1.2 jelenleg nem az alapértelmezett, mivel is [beállítani a kompatibilitási szintet](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level) egy Stream Analytics-feladat portálon vagy az a [feladat REST API-hívás létrehozásához](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job). Rendelkezik *erősen ajánlott* kompatibilitási szint 1.2 ASA a Cosmos DB-vel használandó. 



## <a name="cosmos-db-settings-for-json-output"></a>JSON-kimenet a cosmos DB beállításai

Cosmos DB létrehozása a Stream Analytics kimenetként állít elő, információkat az alább látható módon egy parancssort. Ez a szakasz a Tulajdonságok definíció magyarázattal szolgál.

![a documentdb a stream analytics kimeneti képernyő](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

|Mező           | Leírás|
|-------------   | -------------|
|Kimeneti alias    | Tekintse meg ezt az ASA-lekérdezés kimenete egy alias.|
|Előfizetés    | Válassza ki az Azure-előfizetést.|
|Fiókazonosító      | A neve vagy URI-t az Azure Cosmos DB-fiók végpontját.|
|Fiókkulcs     | A közös hozzáférési kulcs az Azure Cosmos DB-fiókot.|
|Adatbázis        | Az Azure Cosmos DB-adatbázis nevét.|
|Gyűjteménynévminta | A gyűjtemény nevét, a gyűjtemény használható. `MyCollection` Példa érvényes bemenet - nevű egy gyűjtemény `MyCollection` léteznie kell.  |
|Dokumentumazonosító     | Választható. A kimeneti eseményekben a melyik beszúrási vagy frissítési műveletek kell alapozni egyedi kulcsaként használt oszlopok neve. Ha üres, az összes esemény kerül beillesztésre, nincs frissítési lehetőség.|
