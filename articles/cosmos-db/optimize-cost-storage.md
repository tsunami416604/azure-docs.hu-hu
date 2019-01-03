---
title: Az Azure Cosmos DB-ben a tárolási költségek optimalizálása
description: Ez a cikk azt ismerteti, hogyan kezelheti az Azure Cosmos DB-ben tárolt adatokat a tárolási költségek
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: 618be936ef0326a1b9f5db426d4c37bb2bd1c760
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53535658"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>Az Azure Cosmos DB-ben a tárolási költségek optimalizálása

Az Azure Cosmos DB a korlátlan tárolási és átviteli sebességet kínál. Átviteli sebesség, amely az Azure Cosmos-tárolók vagy adatbázisok kiépítéséhez és konfigurálásához rendelkezik, ellentétben a storage szolgáltatás elszámolásának alapja a használata alapján. Fizetnie csak a felhasznált logikai tároló, és nem kell tárolási lefoglalhatja. Storage automatikusan skálázható felfelé és lefelé az adatok alapján, amely hozzáad vagy eltávolít egy Azure Cosmos DB-tárolóhoz.

## <a name="storage-cost"></a>Tárhely költsége

A GB egységet a díjszabásán számoljuk. Helyi SSD-alapú tárolást az adatok által használt és az indexelés. A használt tárterület teljes megegyezik a szükséges az adatok tárolási és indexekben használt minden régióban az Azure Cosmos DB használata. Ha globálisan replikálja egy Azure Cosmos-fiók három régióban, ki kell fizetnie ezen három régió tárterület teljes költségeit. Ha meg szeretné becsülni a tárolásra vonatkozó követelmények, lásd: [kapacitás planner](https://www.documentdb.com/capacityplanner) eszközt. Az Azure Cosmos DB storage költsége $0,25 GB/hónap, lásd: [díjszabási oldalunkon](https://azure.microsoft.com/pricing/details/cosmos-db/) legújabb frissítések. Beállíthat riasztásokat az Azure Cosmos-tároló által felhasznált tárterület meghatározásához a tároló figyelése: [figyelése az Azure Cosmos DB](monitor-accounts.md)) cikkben.

## <a name="optimize-cost-with-item-size"></a>Elem méretét a költségek optimalizálása

Az Azure Cosmos DB vár az elem méretét 2 MB vagy annál kisebb, optimális teljesítmény és költség előnyeit. Ha bármelyik a következők tárolására az adatok 2 MB-nál nagyobb méretű, fontolja meg az elem séma újratervezése. Az esemény ritkán fordul elő, hogy a séma nem alakíthatja át az elem felosztása Szoftverbeállítások és logikailag egy közös identifier(ID) kapcsolja őket. Minden Azure Cosmos DB szolgáltatás által az adott logikai azonosító horgonyzás egységesen működjenek.

## <a name="optimize-cost-with-indexing"></a>Az indexelő költségek optimalizálása

Alapértelmezés szerint az adatok automatikus indexelését, ami növelheti a teljes felhasznált tárterület. A terhelés csökkentése érdekében egyéni index szabályzatokat is alkalmazhat. Körülbelül 10 – 20 %-a cikk automatikus indexelést, amely a szabályzat nem amennyiben. Eltávolításával vagy index házirendek testreszabása, hogy nem kell fizetnie extra írási műveletek költségét, és nem igényel további átviteli kapacitással. Lásd: [indexelése az Azure Cosmos DB](indexing-policies.md) egyéni indexelési szabályzat konfigurálása. Ha dolgozott már relációs adatbázisokat korábban, előfordulhat, hogy véleménye szerint "mindent indexelni" azt jelenti, hogy kétszeresére tárolási vagy magasabb szintű. Az Azure Cosmos DB, a közepes esetben célszerű azonban sokkal alacsonyabb. Az Azure Cosmos DB az index indextárolási terheléssel jár azért általában alacsony (10 – 20 %) még az automatikus indexelést, egy alacsony tárolási erőforrás-igényű számára tervezték. További részletes módon, mivel kezeli az indexelési házirendet, szabályozhatja a kompromisszummal jár, index üzembe helyezésének előkészítése és a lekérdezési teljesítmény.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>Optimalizálhatja költségeit a live és a módosítási hírcsatornáról ideje

Után már nem szükséges az adatok biztonságosan törölheti azt az Azure Cosmos-fiókjából használatával [élettartam](time-to-live.md), [módosításcsatornáját](change-feed.md) vagy áttelepítheti a régi adatok egy másik adattárban, például az Azure blob storage-bA vagy az Azure data warehouse-bA. Élettartam vagy Élettartama alatt a Azure Cosmos DB lehetővé teszi elemek törlése automatikusan végre egy bizonyos idő elteltével. Alapértelmezés szerint be lehet állítani a tároló szintjén live és a konfigurációelem alapon érték felülírása idejét. Miután beállította az élettartam egy tároló vagy egy elem szinten, Azure Cosmos DB automatikusan eltávolítja ezeket az elemeket az adott időszakban az utolsó módosítás időpontja után. Csatorna módosítása segítségével áttelepítheti az adatokat az Azure Cosmos DB-ben vagy egy másik tárolóba vagy egy külső adattárba. Az áttelepítés nulla le időt vesz igénybe, és ha kész telepít át, vagy törölheti, vagy a forrás az Azure Cosmos-tároló törlése élettartam konfigurálása.

## <a name="optimize-cost-with-rich-media-data-types"></a>Multimédiás adattípusokkal költségek optimalizálása 

Ha szeretné tárolni a multimédiás típusok, például videók, képek, stb., hogy számos lehetőséget az Azure Cosmos DB. Az egyik lehetőség, hogy ezek multimédiás tárolhatók az Azure Cosmos elemekként. Elemenként 2 MB-os korlát, és az elem láncolási be több elem úgy kerülheti el ezt a korlátot. Vagy az Azure Blob storage-ban tárolja őket, és hivatkozni tudjon rájuk a az Azure Cosmos-elemek a metaadatok használatával. Vannak előnyei és hátrányai, ezzel a módszerrel több. Az első módszer beolvassa a legjobb teljesítmény tekintetében a késés: az átviteli sebesség SLA-k és a kulcsrakész globális disztribúció képességek mellett a normál Azure Cosmos-elemek multimédiás adattípusok. Azonban a támogatást magasabb áron érhető el. Media tárolja az Azure Blob storage-ban, akkor sikerült csökkentheti az általános költségeket. A késései kritikus következményekkel járnak, használhatja a premium storage az Azure Cosmos-elemek hivatkozott multimédiás fájlok. Ez integrálható a natív módon CDN-t szolgálja ki a peremhálózati kiszolgáló képeit geo-korlátozási megkerülésére alacsonyabb költségek. Ebben a forgatókönyvben lefelé mellett, hogy rendelkezik-e két szolgáltatás – Azure Cosmos DB és az Azure Blob storage, amely növelheti a működési költségek kezelésére. 

## <a name="check-storage-consumed"></a>Ellenőrizze a felhasznált tárterület

Egy Azure Cosmos-tároló tárolófelhasználásának ellenőrzéséhez futtassa egy ÁTJÁRÓ- vagy GET kérelem a tároló, és vizsgálja meg a `x-ms-request-quota` és a `x-ms-request-usage` fejlécek. Azt is megteheti, ha a .net SDK dolgozik, használhatja a [DocumentSizeQuota](https://docs.microsoft.com/previous-versions/azure/dn850325(v%3Dazure.100)), és [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) tulajdonságokat lekérni a felhasznált tárterület.

## <a name="using-sdk"></a>SDK-val

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>További lépések

Ezután folytassa további tudnivalók a költségek optimalizálása az Azure Cosmos DB az alábbi cikkeket:

* Tudjon meg többet [optimalizálása fejlesztéshez és teszteléshez](optimize-dev-test.md)
* Tudjon meg többet [az Azure Cosmos DB-elszámolások ismertetése](understand-your-bill.md)
* Tudjon meg többet [átviteli költségek optimalizálása](optimize-cost-throughput.md)
* Tudjon meg többet [olvasási és írási a költségek optimalizálása](optimize-cost-reads-writes.md)
* Tudjon meg többet [lekérdezések költségeinek optimalizálása](optimize-cost-queries.md)
* Tudjon meg többet [többrégiós Azure Cosmos-fiókok költségeinek optimalizálása](optimize-cost-regions.md)

