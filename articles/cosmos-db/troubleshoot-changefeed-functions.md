---
title: Problémák diagnosztizálása és hibaelhárítása az Azure Functions az Azure Cosmos DB-eseményindító használata során
description: Gyakori problémák, megkerülő megoldások és diagnosztikai lépésekre, az Azure Cosmos DB-eseményindító használata az Azure Functions használatával
author: ealsur
ms.service: cosmos-db
ms.date: 04/16/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5416c576e6392b7c5714ef83f152453aeff8d964
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59685852"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-trigger-in-azure-functions"></a>Problémák diagnosztizálása és hibaelhárítása az Azure Functions az Azure Cosmos DB-eseményindító használata során

Ez a cikk ismerteti gyakoribb problémákhoz, a lehetséges megoldások és a diagnosztikai lépések, ha használja a [Azure Cosmos DB-eseményindító](change-feed-functions.md) az Azure Functions használatával.

## <a name="dependencies"></a>Függőségek

Az Azure Cosmos DB-eseményindító és kötések függ a bővítménycsomagok keresztül az alapszintű Azure Functions futtatókörnyezettel. Mindig tartsa a csomagok frissítésekor, előfordulhat, hogy tartalmazzák a javításokat és új funkciók, a címet előfordulhat, hogy a bármely felmerülő esetleges problémák:

* Az Azure Functions V2, lásd: [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB).
* Az Azure Functions V1, lásd: [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB).

Ez a cikk fog mindig tekintse meg az Azure Functions V2, amikor a futtatókörnyezet említik, kivéve, ha explicit módon megadott.

## <a name="consuming-the-cosmos-db-sdk-separately-from-the-trigger-and-bindings"></a>A Cosmos DB SDK elkülönítve a triggerét és kötéseit felhasználása

A bővítmény csomag a főbb funkciókat, hogy támogatást nyújt az Azure Cosmos DB triggerét és kötéseit. Ezenkívül tartalmazza a [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md), ez akkor hasznos, ha azt szeretné, programozott módon kezelheti az Azure Cosmos DB a eseményindítók és kötések használata nélkül.

Ha a szeretné használni az Azure Cosmos DB SDK-t, győződjön meg arról, hogy nem ad hozzá a projekt NuGet-csomag egy másik hivatkozást. Ehelyett **lehetővé teszik az SDK-leírás keresztül az Azure Functions-kiterjesztési csomag megoldásához**.

Emellett ha manuálisan hoz létre a saját példányát a [Azure Cosmos DB SDK-ügyfél](./sql-api-sdk-dotnet-core.md), kövesse a mintát, hogy az ügyfél csak egy példánya [egyszeres minta módszerével](../azure-functions/manage-connections.md#documentclient-code-example-c) . Ez a folyamat elkerüli a potenciális szoftvercsatorna problémák egy részét a műveletek.

## <a name="common-known-scenarios-and-workarounds"></a>Ismert gyakori forgatókönyvek és megoldások

### <a name="azure-function-fails-with-error-message-either-the-source-collection-collection-name-in-database-database-name-or-the-lease-collection-collection2-name-in-database-database2-name-does-not-exist-both-collections-must-exist-before-the-listener-starts-to-automatically-create-the-lease-collection-set-createleasecollectionifnotexists-to-true"></a>Azure-függvény hibaüzenettel meghiúsul "vagy a forrás gyűjtemény"gyűjtemény-name"(az adatbázis (adatbázis-name)) vagy a bérletek gyűjteményének"2. gyűjtemény-name"(az adatbázis"2. adatbázis-name") nem létezik. Gyűjtemények a figyelő megkezdése előtt léteznie kell. A bérletek gyűjteményének automatikus létrehozásához "CreateLeaseCollectionIfNotExists" beállítása "true" "

Ez azt jelenti, hogy egyik vagy mindkét az Azure Cosmos-tárolókat az eseményindító működéséhez szükséges nem létezik, vagy nem érhetők el az Azure-függvénynek. **A hiba, maga megtudhatja, melyik Azure-Cosmos-adatbázis, és tárolók az eseményindító keres** a konfiguráció alapján.

1. Ellenőrizze a `ConnectionStringSetting` attribútum és az általa **hivatkozik egy beállítást, amely megtalálható az Azure-Függvényalkalmazás**. Ennek az attribútumnak az értéke nem lehet a kapcsolati karakterlánc magát, de a konfigurációs beállítás nevét.
2. Ellenőrizze, hogy a `databaseName` és `collectionName` az Azure Cosmos-fiókban található. Ha használja az automatikus érték lecseréléséről (használatával `%settingName%` minták), ellenőrizze, hogy a beállítás neve szerepel az Azure-Függvényalkalmazást.
3. Ha nem ad meg egy `LeaseCollectionName/leaseCollectionName`, az alapértelmezett érték "bérletek". Győződjön meg arról, hogy létezik-e ilyen tároló. Igény szerint beállíthatja a `CreateLeaseCollectionIfNotExists` az eseményindító-attribútumban `true` automatikus létrehozásához.
4. Ellenőrizze a [tűzfal-konfiguráció az Azure Cosmos-fiók](how-to-configure-firewall.md) megtekintéséhez, hogy nincs megtekintéséhez nem blokkolja az Azure-függvény.

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure-függvényt a következővel kell kezdődnie meghiúsul, "megosztott átviteli gyűjtemény kell rendelkeznie a partíciós kulcs"

Az Azure Cosmos DB-bővítmény a korábbi verziói nem támogatták a használatával egy bérleteket tároló, amelyben létrehozták a [megosztott átviteli adatbázis](./set-throughput.md#set-throughput-on-a-database). A probléma megoldásához frissítse a [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) bővítmény a legújabb verzió beszerzéséhez.

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure-függvényt a következővel kell kezdődnie meghiúsul, "a bérletek gyűjteményének particionálva, ha a partíciókulccsal kell rendelkeznie azonosító egyenlő."

Ez a hiba azt jelenti, hogy az aktuális bérleteket tároló particionálva van, de a partíciós kulcs elérési útja nem `/id`. A probléma megoldásához, létre kell hoznia a bérletek tároló `/id` partíciókulcsként.

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>Megjelenik egy "értéke nem lehet null értékű. Paraméter neve: o "az Azure Functions naplókban, amikor a Trigger futtatásakor

A probléma akkor jelenik meg, ha az Azure portált használja, és megpróbálja válassza ki a **futtatása** gombra a képernyő, ha egy Azure-triggert használó függvényt vizsgálatával. Az eseményindító nem igényel választható indításához futtassa az Azure-függvény üzembe helyezésekor automatikusan elindul. Ha azt szeretné, ellenőrizze az Azure Portalon az Azure-függvény naplófolyam, csak a figyelt tároló megnyitása és néhány új elem beszúrása, automatikusan megjelenik az eseményindító végrehajtása.

### <a name="my-changes-take-too-long-be-received"></a>A módosítások végezze el a túl hosszú fogadása

Ebben a forgatókönyvben több oka is van, és ezek mindegyike ellenőrizni kell:

1. Az Azure Cosmos-fiók ugyanabban a régióban üzemel az Azure-függvény? Az optimális hálózati késés az Azure-függvény, mind az Azure Cosmos-fiók kell lennie védelmicsoport-készletek ugyanazon Azure-régióban.
2. Az Azure Cosmos-tárolóban, folyamatos vagy szórványos történik a módosításokat?
Ha az utóbbi, némi késedelemmel tárolása a módosításokat, és kiválasztotta őket az Azure-függvény között lehet. Ennek oka az, belsőleg, a trigger ellenőrzi a módosításokat az Azure Cosmos-tárolóban, és megkeresi nincs függőben lévő kell olvasni, azt fogja alszik egy konfigurálható ideig (5 másodperc, alapértelmezés szerint) (magas fogyasztott elkerülésére) új módosítások ellenőrzése előtt. Az alvási idő keresztül konfigurálhatja a `FeedPollDelay/feedPollDelay` beállítását a [konfigurációs](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration) a trigger (az érték várható ezredmásodpercben).
3. Lehet, hogy az Azure Cosmos-tároló [sebessége korlátozott](./request-units.md).
4. Használhatja a `PreferredLocations` az Azure-régiók és a egy egyéni előnyben részesített csatlakozási sorrend meghatározása vesszővel elválasztott listáját adja meg az eseményindító-attribútumban.

### <a name="some-changes-are-missing-in-my-trigger"></a>Néhány módosítást hiányoznak a saját eseményindító

Ha azt tapasztalja, hogy a módosításokat, és ismételje meg az Azure Cosmos-tárolóban némelyike nem folyamatban mértékének növelése az Azure-függvény, van egy kezdeti vizsgálat lépés, hogy meg kell történnie.

Amikor az Azure-függvény megkapja a módosításokat, azt gyakran feldolgozza azokat, és sikerült igény szerint, az eredmény küld egy másik cél. Ha hiányzó módosítások vizsgált, ellenőrizze, hogy **mérték milyen módosításokat fogadja az Adatbetöltési pont,** (amikor az Azure-függvény kezdődik), nem pedig a cél.

Ha a cél-néhány módosítást hiányzik, ez azt jelentheti, hogy-e valamilyen hiba történik az Azure-függvény végrehajtása során, a módosítások lettek fogadását követően.

Ebben az esetben a legjobb megoldás érdekében, hogy adjon hozzá `try/catch blocks` a kódban, és a hurkok, előfordulhat, hogy feldolgozni a módosításokat, minden hiba elemek egy adott alegységénél észlelésére, és ennek megfelelően kezelje azokat belül (elküldheti azokat egy másik tárolási további az elemzés vagy újrapróbálkozási). 

> **Az Azure Cosmos DB-eseményindító, alapértelmezés szerint nem fog újra módosítások kötegelt Ha nem kezelt kivétel történt** a kód végrehajtása során. Ez azt jelenti, hogy az az oka, hogy a módosítások nem érkezett meg a célhelyen, mert, hogy sikertelen feldolgozhassa őket.

Ha úgy találja, hogy néhány módosítást az eseményindító nem érkeztek minden, a leggyakoribb eset, hogy nincs **más Azure-függvényt futtató**. Lehet, hogy egy másik Azure-ban üzembe helyezett Azure-függvény, vagy egy Azure-függvény helyben fut, egy fejlesztői számítógép, amely rendelkezik **pontosan ugyanazt a konfigurációt** (azonos figyelni és a címbérlet-tárolók) és az Azure-függvény ellophatják van egy a módosítások feldolgozása az Azure-függvény hiányol részhalmazát.

Ezenkívül a forgatókönyv érvényesíthető, ha tudja, hány Azure-Függvényalkalmazás-példányt futtató. Ha a bérletek tároló vizsgálata és belül, a különböző értékeket bérleti elemek számát a `Owner` bennük tulajdonságot meg kell egyeznie a Függvényalkalmazás példányainak számát. További tulajdonosok, mint az Azure-Függvényalkalmazás ismert példányok esetén az azt jelenti, hogy ezek további tulajdonosok közé tartoznak a "lopásának megjelölése" a módosításokat.

Megkerülő megoldás egyik egyszerű módja ebben az esetben, a alkalmazni egy `LeaseCollectionPrefix/leaseCollectionPrefix` új/eltérő értékkel a függvényt, illetve azt is megteheti, egy új bérleteket tárolóval.

## <a name="next-steps"></a>További lépések

* [Az Azure Functions figyelés engedélyezése](../azure-functions/functions-monitoring.md)
* [Az Azure Cosmos DB .NET SDK hibáinak elhárítása](./troubleshoot-dot-net-sdk.md)