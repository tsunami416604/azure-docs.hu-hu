---
title: A tárolási költség optimalizálása az Azure Cosmos DB-ben
description: Ez a cikk bemutatja, hogyan kezelheti az Azure Cosmos DB-ben tárolt adatok tárolási költségeit
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 1508adda761fcba7ba70df3bb212d3eb4e32f242
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72754943"
---
# <a name="optimize-storage-cost-in-azure-cosmos-db"></a>A tárolási költség optimalizálása az Azure Cosmos DB-ben

Az Azure Cosmos DB korlátlan tárhelyet és átviteli kapacitást kínál. Az átviteli kapacitással ellentétben, amelyet az Azure Cosmos-tárolókon vagy adatbázisokon kell kiépítenie/konfigurálnia, a tárolás számlázása a felhasználás alapján történik. A számlázás csak a felhasznált logikai tárolóért van kiszámlázva, és nem kell előre lefoglalnia a tárhelyet. A storage automatikusan fel-le skálázódik az Azure Cosmos-tárolóhoz hozzáadott vagy eltávolított adatok alapján.

## <a name="storage-cost"></a>Tárolási költség

A tárolás számlázása a GB-k egységével történik. A helyi SSD-alapú tárolót az adatok és az indexelés használják. A teljes felhasznált tárterület megegyezik az összes olyan régióban használt adatok és indexek által igényelt tárterület, ahol az Azure Cosmos DB-t használja. Ha globálisan replikál egy Azure Cosmos-fiókot három régióban, akkor a teljes tárolási költség ért kell fizetnie a három régió mindegyikében. A tárolási igény becsléséhez tekintse meg a [kapacitástervező](https://www.documentdb.com/capacityplanner) eszközt. Az Azure Cosmos DB-ben a tárolás költsége $0.25 GB/hó, tekintse meg a legújabb frissítések [díjszabása oldalon.](https://azure.microsoft.com/pricing/details/cosmos-db/) Riasztásokat állíthat be az Azure Cosmos-tároló által használt tárterület meghatározásához, a storage figyeléséhez, [lásd: Az Azure Cosmos DB monitorozása](monitor-accounts.md)) című cikket.

## <a name="optimize-cost-with-item-size"></a>Költség optimalizálása cikkmérettel

Az Azure Cosmos DB az optimális teljesítmény és költségelőnyök érdekében 2 MB-os vagy annál kisebb elemméretet vár. Ha 2 MB-nál nagyobb adatok tárolására van szüksége, fontolja meg az elemséma újratervezését. Abban a ritka esetben, ha nem tudja újratervezni a sémát, feloszthatja az elemet alelemekre, és logikailag összekapcsolhatja őket egy közös azonosítóval(ID). Az Azure Cosmos DB összes szolgáltatása következetesen működik a logikai azonosítóhoz való rögzítéssel.

## <a name="optimize-cost-with-indexing"></a>Költség optimalizálása indexeléssel

Alapértelmezés szerint az adatok automatikusan indexelt, ami növelheti a teljes felhasznált tárterület. Azonban egyéni indexházirendeket is alkalmazhat a terhelés csökkentéséhez. A házirenden keresztül nem hangolt automatikus indexelés az elemméret körülbelül 10–20%-a. Az indexházirendek eltávolításával vagy testreszabásával nem kell külön költséget fizetnie az írásokért, és nincs szükség további átviteli kapacitásra. Az Egyéni indexelési szabályzatok konfigurálásához tekintse [indexelés az Azure Cosmos DB-ben.](indexing-policies.md) Ha korábban már dolgozott relációs adatbázisokkal, azt gondolhatja, hogy az "index mindent" a tárhely megduplázását vagy magasabb összeírását jelenti. Azonban az Azure Cosmos DB, a medián esetben, ez sokkal alacsonyabb. Az Azure Cosmos DB-ben az index tárolási terhelése általában alacsony (10–20%) még az automatikus indexelés, mert úgy tervezték, hogy az alacsony tárolási helyigény. Az indexelési házirend kezelésével szabályozhatja az indexalapkontúr és a lekérdezési teljesítmény kompromisszumot részletesebben.

## <a name="optimize-cost-with-time-to-live-and-change-feed"></a>A költségek optimalizálása az élethez és a hírcsatorna módosításához

Ha már nincs szüksége az adatokra, szabályosan törölheti azokat az Azure Cosmos-fiókból [az élettartam](time-to-live.md)használatával, módosíthatja a [hírcsatornát,](change-feed.md) vagy áttelepítheti a régi adatokat egy másik adattárba, például az Azure blobstorage-ba vagy az Azure-adatraktárba. Az élő vagy TTL idő, az Azure Cosmos DB lehetővé teszi, hogy egy adott időszak után automatikusan törölje az elemeket egy tárolóból. Alapértelmezés szerint beállíthatja, hogy az idő a tároló szintjén éljen, és felülbírálja az értéket cikkenként. Miután beállította a TTL egy tárolóban vagy egy elem szintjén, az Azure Cosmos DB automatikusan eltávolítja ezeket az elemeket az utolsó módosítás uk óta eltelt idő után. Módosítási hírcsatorna használatával adatokat telepíthet át az Azure Cosmos DB egy másik tárolóba vagy egy külső adattárba. Az áttelepítés nulla leadási időt vesz igénybe, és ha befejezte az áttelepítést, törölheti vagy konfigurálhatja az élő ideig a forrás Azure Cosmos-tároló törléséhez.

## <a name="optimize-cost-with-rich-media-data-types"></a>A költségek optimalizálása multimédiás adattípusokkal 

Ha multimédiás típusokat szeretne tárolni, például videókat, képeket stb., számos lehetőség közül választhat az Azure Cosmos DB-ben. Az egyik lehetőség az, hogy ezeket a multimédiás típusokat Azure Cosmos-elemekként tárolja. Elemenként 2 MB-os korlát van, és ezt a korlátot elkerülheti, ha az adatelemet több alelemhez láncolja. Vagy tárolhatja őket az Azure Blob storage-ban, és a metaadatok segítségével hivatkozhat rájuk az Azure Cosmos-elemekből. Számos előnye és hátránya ezzel a megközelítéssel. Az első megközelítés a késés, az átviteli kapacitás SL-ek és a multimédiás adattípusok kulcsrakész globális terjesztési képességei tekintetében a legjobb teljesítményt kapja a rendszeres Azure Cosmos-elemek mellett. Azonban a támogatás elérhető magasabb áron. Az azure Blob-tárolóban tárolt adathordozók tárolásával csökkentheti a teljes költségeket. Ha a késés kritikus fontosságú, prémium szintű storage-ot használhat az Azure Cosmos-elemekből hivatkozott multimédiás fájlokhoz. Ez natívan integrálható a CDN-nel, hogy a peremhálózati kiszolgálóról származó képeket alacsonyabb költséggel szolgálja ki a földrajzi korlátozás megkerülése érdekében. A forgatókönyv hátránya, hogy két szolgáltatással kell foglalkoznia – az Azure Cosmos DB-vel és az Azure Blob storage-tal, amelyek növelhetik a működési költségeket. 

## <a name="check-storage-consumed"></a>Felhasznált tárhely ellenőrzése

Az Azure Cosmos-tároló tárolási felhasználásának ellenőrzéséhez futtathat egy HEAD vagy GET `x-ms-request-quota` kérelmet a tárolón, és vizsgálja meg a és a `x-ms-request-usage` fejlécek. Másik lehetőségként a .NET SDK használatakor a [DocumentSizeQuota](https://docs.microsoft.com/previous-versions/azure/dn850325(v%3Dazure.100))és a [DocumentSizeUsage](https://msdn.microsoft.com/library/azure/dn850324.aspx) tulajdonságokkal is lefoglalhatja a felhasznált tárolót.

## <a name="using-sdk"></a>Az SDK használata

```csharp
// Measure the item size usage (which includes the index size)
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));   

Console.WriteLine("Item size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

## <a name="next-steps"></a>További lépések

Ezután az alábbi cikkekkel többet tudhat meg a költségoptimalizálásról az Azure Cosmos DB-ben:

* További információ [az optimalizálásról fejlesztési és tesztelési célokra](optimize-dev-test.md)
* További információ [az Azure Cosmos DB-számlájának ismertetéséről](understand-your-bill.md)
* További információ az [átviteli költség optimalizálásáról](optimize-cost-throughput.md)
* További információ [az olvasási és írási költségek optimalizálásáról](optimize-cost-reads-writes.md)
* További információ [a lekérdezések költségének optimalizálásáról](optimize-cost-queries.md)
* További információ a több régióra kiterjedő [Azure Cosmos-fiókok költségeinek optimalizálásáról](optimize-cost-regions.md)

