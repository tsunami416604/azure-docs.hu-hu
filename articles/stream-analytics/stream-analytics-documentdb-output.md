---
title: Azure Stream Analytics-kimenet a Cosmos DB-hez
description: Ez a cikk ismerteti az Azure Stream Analytics használatával az Azure Cosmos DB JSON kimeneti adatok archiválása és kis késleltetésű lekérdezéseket a strukturálatlan JSON-adatok mentése.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 95cfc7e6d9515274aa7a3c5fde382244f3b33fab
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "42058752"
---
# <a name="azure-stream-analytics-output-to-azure-cosmos-db"></a>Az Azure Cosmos DB Azure Stream Analytics-kimenet  
Stream Analytics célként [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) JSON-kimenet, az adatok archiválás és kis késleltetésű lekérdezéseket a strukturálatlan JSON-adatok engedélyezése. Ez a dokumentum áttekint néhány ajánlott eljárást a konfiguráció implementálása a.

Azok számára, aki ismeri az, Cosmos DB, vessen egy pillantást [képzési terv az Azure Cosmos DB](https://azure.microsoft.com/documentation/learning-paths/documentdb/) a kezdéshez. 

> [!Note]
> Jelenleg az Azure Stream Analytics csak kapcsolatot támogat az Azure Cosmos DB használatával **SQL API**.
> Más Azure Cosmos DB API-k még nem támogatott. Ha pont Azure Stream Analytics az Azure Cosmos DB-fiókokhoz létrehozott más API-kkal, az adatok esetleg nem megfelelően tárolni. 

## <a name="basics-of-cosmos-db-as-an-output-target"></a>Cosmos DB-kimenet célként alapjai
A Stream Analytics az Azure Cosmos DB kimeneti adatfolyam-feldolgozási eredményeket, JSON-kimenet a Cosmos DB próbaidőszakában való írása lehetővé teszi. Stream Analytics nem hoz létre gyűjteményeket az adatbázisban, ehelyett határozhat meg kell előre létrehoznia őket. Ez az, hogy az Ön által vezérelt Cosmos DB-gyűjtemények a számlázási költségeket, és úgy, hogy a teljesítmény, a konzisztencia és a kapacitás a gyűjtemények közvetlenül hangolhassa a [Cosmos DB API-k](https://msdn.microsoft.com/library/azure/dn781481.aspx). 

A Cosmos DB adatgyűjtési beállítások némelyike lásd lent.

## <a name="tune-consistency-availability-and-latency"></a>Konzisztencia, a rendelkezésre állás és a késleltetés hangolása
Az alkalmazás követelményeinek megfelelően, az Azure Cosmos DB lehetővé teszi jól adhatja meg az adatbázis és a gyűjtemények, és győződjön meg arról, konzisztencia, a rendelkezésre állás és a késleltetés közti kompromisszummal. Attól függően, hogy milyen szintű olvasás következetes ellen a forgatókönyv igényeinek olvassa el, és írási késés, választhatja a konzisztenciaszint a fiókot a. Is alapértelmezés szerint az Azure Cosmos DB lehetővé teszi, hogy szinkron indexelő egyes CRUD-műveletek a gyűjteményhez. Ez a szabályozhatja az írási/olvasási teljesítmény az Azure Cosmos DB egy másik hasznos lehetőség. További információkért tekintse át a [módosítása az adatbázis és a lekérdezés konzisztenciaszintek](../cosmos-db/consistency-levels.md) cikk.

## <a name="upserts-from-stream-analytics"></a>A Stream Analytics Upserts
Stream Analytics-integráció az Azure Cosmos DB lehetővé teszi a Beszúrás, vagy frissíthet rekordokat a gyűjtemény adott dokumentumazonosító oszlop alapján. Ez is nevezik egy *Upsert*.

Stream Analytics egy optimista upsert módszert használ, ahol frissítéseit csak végzett insert dokumentumazonosító ütközést tartalmazó meghibásodása esetén. A frissítés történik, így, amely lehetővé teszi a dokumentumban a részleges frissítési javítás, az új tulajdonságok vagy lecseréli a meglévő tulajdonságot Növekményesen történik. A tömb tulajdonságokat a JSON-dokumentum eredményt, a tömb első felülírja, teljes tömb a módosítások azonban nem részeként történik.

## <a name="data-partitioning-in-cosmos-db"></a>Adatparticionálás Cosmos DB-ben
Az Azure Cosmos DB [korlátlan](../cosmos-db/partition-data.md) van az ajánlott módszer az adatok particionálása, az Azure Cosmos DB automatikusan méretezi a számítási feladatok alapján. Korlátlan tárolók írásakor Stream Analytics tetszőleges számú párhuzamos írók előző lekérdezési lépés vagy a particionálási séma bemeneti használ.
> [!Note]
> Jelenleg az Azure Stream Analytics csak támogatja a korlátlan tárolók partíciókulcsok a legfelső szinten. Ha például `/region` használata támogatott. A beágyazott partíciókulcsok (pl. `/region/name`) használata nem támogatott. 

Rögzített Azure Cosmos DB-gyűjtemények a Stream Analytics lehetővé teszi a vertikális vagy horizontális, amint teljes semmilyen módon nem lehet. 10 GB-os és 10 000 RU/s átviteli sebesség felső korlátja rendelkeznek.  Az adatok áttelepíthetők rögzített tárolóba (például egy legalább 1000 RU/s és a egy partíciókulcsot) korlátlan tárolóra, kell használnia a [adatáttelepítési eszköz](../cosmos-db/import-data.md) vagy a [módosítási hírcsatorna könyvtár](../cosmos-db/change-feed.md).

Több állandó tároló való írás hamarosan elavulttá válik, és nem az ajánlott módszer a horizontális felskálázás a Stream Analytics-feladatot. A cikk [particionálás és skálázás az, Cosmos DB](../cosmos-db/sql-api-partition-data.md) további részleteket tartalmaz.

## <a name="cosmos-db-settings-for-json-output"></a>JSON-kimenet a cosmos DB beállításai
Cosmos DB létrehozása a Stream Analytics kimenetként állít elő, információkat az alább látható módon egy parancssort. Ez a szakasz a Tulajdonságok definíció magyarázattal szolgál.


![a documentdb a stream analytics kimeneti képernyő](media/stream-analytics-documentdb-output/stream-analytics-documentdb-output-1.png)

Mező           | Leírás 
-------------   | -------------
Output Alias (Kimeneti alias)    | Ez a kimenet az ASA-lekérdezésekben hivatkozni alias   
Fiók neve    | A név vagy a végpont URI-t az Azure Cosmos DB-fiók 
Fiókkulcs     | Az Azure Cosmos DB-fiókot a megosztott elérési kulcsot
Adatbázis        | Az Azure Cosmos DB-adatbázis neve
Gyűjtemény neve | A gyűjtemény nevét, a gyűjtemény használható. `MyCollection` Példa érvényes bemenet - nevű egy gyűjtemény `MyCollection` léteznie kell.  
Dokumentumazonosító     | Választható. A kimeneti eseményekben a melyik beszúrási vagy frissítési műveletek kell alapozni egyedi kulcsaként használt oszlopok neve. Ha üres, az összes esemény kerül beillesztésre, nincs frissítési lehetőség.
