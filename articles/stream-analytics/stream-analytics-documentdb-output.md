---
title: Az Azure Stream Analytics kimeneti Cosmos DB
description: A cikkből megtudhatja, hogyan Azure Stream Analytics segítségével Azure Cosmos DB JSON kimeneti adatok archiválása és alacsony késésű lekérdezései strukturálatlan JSON-adatok mentése.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: ff2071a703b0b5e94cd68122a878b51e9d97669a
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37112751"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Az Azure Stream Analytics kimeneti az Azure Cosmos-Adatbázishoz  
A Stream Analytics célba [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) JSON-kimenetét, engedélyezi az archiválási és alacsony késésű kérelmek strukturálatlan JSON-adatokat. Ez a dokumentum ismertet néhány gyakorlati tanácsok a konfiguráció alkalmazásához.

Azok számára, akiknek nem ismeri az Cosmos DB, vessen egy pillantást [Azure Cosmos DB képzési terv](https://azure.microsoft.com/documentation/learning-paths/documentdb/) a kezdéshez. 

> [!Note]
> Ilyenkor Azure Stream Analytics csak kapcsolatot támogat az Azure Cosmos DB használatával **SQL API**.
> Egyéb Azure Cosmos DB API-k még nem támogatott. Ha pont Azure Stream Analytics az Azure Cosmos DB fiókok létrehozása, más API-khoz, az adatok nem megfelelően tárolódhat. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Egy kimeneti célként Cosmos DB alapjai
Az Azure Cosmos DB kimeneti a Stream Analytics lehetővé teszi, hogy írása a streamfeldolgozási eredmények JSON kimenetként a Cosmos DB következő gyűjtemény(ek) készleteit szinkronizálja azokat. A Stream Analytics nem gyűjtemények létrehozása az adatbázis ehelyett nincs szükség kell létrehoznia őket előzetes megfizetése esetén. Ez az, hogy az Ön által vezérelt Cosmos DB gyűjtemények számlázási költségeit, és úgy, hogy a teljesítmény, a konzisztencia és a kapacitás közvetlenül a gyűjtemények észlelheti a [Cosmos DB API-k](https://msdn.microsoft.com/library/azure/dn781481.aspx). 

A Cosmos DB adatgyűjtési beállítások némelyike részleteket lejjebb olvashatja.

## <a name="tune-consistency-availability-and-latency"></a>Konzisztencia, a rendelkezésre állás és a késleltetés hangolása
Saját alkalmazás igényeinek Azure Cosmos DB lehetővé teszi, hogy jól hangolja az adatbázis és a gyűjtemények és konzisztencia, a rendelkezésre állás és a késleltetés közötti kompromisszumot. Attól függően, hogy milyen szintű olvasási konzisztenciát elleni forgatókönyv igényeinek és olvasási késés, kiválaszthatja a konzisztenciaszint adatbázis fiókja. Alapértelmezés szerint is Azure Cosmos DB engedélyezi szinkron indexelő minden CRUD-műveletnek a gyűjteményhez. Ez a beállítás egy másik hasznos vezérlésére az Azure Cosmos DB írási/olvasási teljesítményt. További információkért tekintse át a [módosítsa az adatbázis és a lekérdezés konzisztenciaszintek](../cosmos-db/consistency-levels.md) cikk.

## <a name="upserts-from-stream-analytics"></a>A Stream Analytics Upserts
Azure Cosmos DB Stream Analytics integrációja lehetővé teszi beszúrásához vagy frissítéséhez rögzíti a gyűjtemény egy adott dokumentum azonosító oszlop alapján. Ez más néven a egy *Upsert*.

A Stream Analytics használ optimista upsert megközelítés, ahol frissítések csak végzett insert Dokumentumazonosítója ütközést tartalmazó meghibásodása esetén. A frissítés történik, a javítás, így lehetővé teszi, hogy a részleges frissítési a dokumentumhoz, ez azt jelenti, az új tulajdonságok vagy cseréje a meglévő tulajdonságot Növekményesen történik. A tömb-tulajdonságokat a JSON dokumentum eredmény ez azt jelenti, hogy a tömb első felülírja, teljes tömbben szereplő értékek változásait azonban nem összefésülése megtörténjen.

## <a name="data-partitioning-in-cosmos-db"></a>A Cosmos DB adatparticionálás
Az Azure Cosmos DB [korlátlan](../cosmos-db/partition-data.md) az ajánlott módszer az adatok particionálása Azure Cosmos DB, automatikusan méretezi a számítási feladatok alapján létrehozott partícióknak van. Korlátlan tárolók írásakor Stream Analytics használ tetszőleges számú párhuzamos írók, például előző lekérdezés lépést vagy bemeneti particionálási sémát.

Rögzített Azure Cosmos DB gyűjtemények Stream Analytics lehetővé teszi, hogy semmilyen módon nem vertikális vagy horizontális, miután teljes fontosságúak. 10 GB és 10000 RU/mp-átviteli felső korlátja rendelkeznek.  A rögzített tároló egy korlátlan tárolóhoz (például egy legalább 1000 RU/mp és a partíciós kulcs) az adatok áttelepítéséhez kell használnia a [adatáttelepítési eszköz](../cosmos-db/import-data.md) vagy a [módosítás hírcsatorna könyvtár](../cosmos-db/change-feed.md).

Több rögzített tároló írás is elavult, és nem az ajánlott módszer az kiterjesztése a Stream Analytics-feladat. A cikk [particionálás és Cosmos DB skálázás](../cosmos-db/sql-api-partition-data.md) további részleteket tartalmaz.

## <a name="cosmos-db-settings-for-json-output"></a>JSON kimeneti cosmos DB beállításai
Az alább látható információt adatkérést Cosmos-adatbázis létrehozása a Stream Analytics kimenetként állít elő. Ez a szakasz a Tulajdonságok definíció magyarázattal szolgál.


![a documentdb stream analytics kimeneti képernyő](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

Mező           | Leírás 
-------------   | -------------
A kimeneti Alias    | Tekintse meg a kimenetet a ASA lekérdezés aliast   
Fiók neve    | A name vagy a végpont URI-azonosítója az Azure Cosmos DB fiók 
Fiókkulcs     | A megosztott elérési kulcsot az Azure Cosmos DB fiókhoz
Adatbázis        | Az Azure Cosmos DB adatbázis neve
Gyűjtemény neve | A gyűjtemény nevét, a gyűjtemény használható. `MyCollection` Példa érvényes bemeneti - nevű egy gyűjtemény `MyCollection` léteznie kell.  
Dokumentumazonosító     | Választható. A kimeneti eseményekben a egyedi kulcs, mely Beszúrás vagy frissítés szabványra kell épülniük műveletek oszlop neve. Ha üres, az összes esemény a program beszúrja, nincs frissítés lehetőség.
