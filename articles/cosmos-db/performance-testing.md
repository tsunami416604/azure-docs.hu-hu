---
title: Teljesítmény- és méretezési tesztelés az Azure Cosmos DB-vel
description: Ismerje meg, hogyan csinálhat méretezési és teljesítménytesztelést az Azure Cosmos DB segítségével. Ezután kiértékelheti az Azure Cosmos DB funkcióit a nagy teljesítményű alkalmazásforgatókönyvekhez.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: fb510c5628913fb3fa37b572c4409aee5d1028ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76313751"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Teljesítmény- és méretezési tesztelés az Azure Cosmos DB-vel

A teljesítmény- és méretarány-tesztelés kulcsfontosságú lépés az alkalmazásfejlesztésben. Számos alkalmazás esetében az adatbázisszint jelentős hatással van az általános teljesítményre és a méretezhetőségre. Ezért ez a teljesítménytesztelés kritikus összetevője. [Az Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) rugalmas méretezési és kiszámítható teljesítményre készült. Ezek a képességek teszik, hogy egy nagy illeszkedést alkalmazások, amelyek nagy teljesítményű adatbázis-réteg. 

Ez a cikk egy referencia a fejlesztők számára, amelyek teljesítményteszt-csomagokat valósítanak meg az Azure Cosmos DB-munkaterheléseikhez. Az Azure Cosmos DB nagy teljesítményű alkalmazási forgatókönyvek kiértékelésére is használható. Elsősorban az adatbázis elkülönített teljesítménytesztelésére összpontosít, de az éles alkalmazásokra vonatkozó bevált gyakorlatokat is tartalmazza.

A cikk elolvasása után a következő kérdésekre válaszolhat: 

* Hol találok egy minta .NET ügyfélalkalmazást az Azure Cosmos DB teljesítményteszteléséhez? 
* Hogyan érhetek el magas átviteli szintet az Azure Cosmos DB-vel az ügyfélalkalmazásból?

A kód elvégzéséhez töltse le a projektet az [Azure Cosmos DB teljesítménytesztelési mintából.](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark) 

> [!NOTE]
> Az alkalmazás célja annak bemutatása, hogyan érhető el a legjobb teljesítmény az Azure Cosmos DB kis számú ügyfélgéppel. A minta célja nem az Azure Cosmos DB csúcsátviteli kapacitásának elérése (amely korlátok nélkül skálázható).
> 
> 

Ha ügyféloldali konfigurációs lehetőségeket keres az Azure Cosmos DB teljesítményének javítása érdekében, olvassa el az [Azure Cosmos DB teljesítménytippjeit.](performance-tips.md)

## <a name="run-the-performance-testing-application"></a>A teljesítménytesztelő alkalmazás futtatása
A kezdéshez a leggyorsabban úgy lehet hozzá, hogy lefordítja és futtatja a .NET mintát, ahogy azt az alábbi lépések ismertetik. Megtekintheti a forráskódot is, és hasonló konfigurációkat valósíthat meg a saját ügyfélalkalmazásain.

**1. lépés:** Töltse le a projektet az [Azure Cosmos DB teljesítménytesztelési mintából,](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)vagy a GitHub-tárházból.

**2. lépés:** Módosítsa az EndpointUrl, a AuthorizationKey, a CollectionThroughput és a DocumentTemplate (nem kötelező) beállításait az App.config alkalmazásban.

> [!NOTE]
> A nagy átviteli sebességű gyűjtemények kiépítése előtt tekintse meg a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/cosmos-db/) a gyűjteményenkénti költségek becsléséhez. Az Azure Cosmos DB óránkénti tárolást és átviteli kapacitást számlál. Az Azure Cosmos-tárolók átviteli-átviteli átviteli keresztüli áteresztőrészének tesztelés után történő csökkentésével költségeket takaríthat meg.
> 
> 

**3. lépés:** A konzolalkalmazás lefordítása és futtatása a parancssorból. A kimenetnek a következőhez hasonlóan kell látnia:

    C:\Users\cosmosdb\Desktop\Benchmark>DocumentDBBenchmark.exe
    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://arramacquerymetrics.documents.azure.com:443/
    Collection : db.data at 100000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: -1
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Found collection data with 100000 RU/s
    Starting Inserts with 100 tasks
    Inserted 4503 docs @ 4491 writes/s, 47070 RU/s (122B max monthly 1KB reads)
    Inserted 17910 docs @ 8862 writes/s, 92878 RU/s (241B max monthly 1KB reads)
    Inserted 32339 docs @ 10531 writes/s, 110366 RU/s (286B max monthly 1KB reads)
    Inserted 47848 docs @ 11675 writes/s, 122357 RU/s (317B max monthly 1KB reads)
    Inserted 58857 docs @ 11545 writes/s, 120992 RU/s (314B max monthly 1KB reads)
    Inserted 69547 docs @ 11378 writes/s, 119237 RU/s (309B max monthly 1KB reads)
    Inserted 80687 docs @ 11345 writes/s, 118896 RU/s (308B max monthly 1KB reads)
    Inserted 91455 docs @ 11272 writes/s, 118131 RU/s (306B max monthly 1KB reads)
    Inserted 102129 docs @ 11208 writes/s, 117461 RU/s (304B max monthly 1KB reads)
    Inserted 112444 docs @ 11120 writes/s, 116538 RU/s (302B max monthly 1KB reads)
    Inserted 122927 docs @ 11063 writes/s, 115936 RU/s (301B max monthly 1KB reads)
    Inserted 133157 docs @ 10993 writes/s, 115208 RU/s (299B max monthly 1KB reads)
    Inserted 144078 docs @ 10988 writes/s, 115159 RU/s (298B max monthly 1KB reads)
    Inserted 155415 docs @ 11013 writes/s, 115415 RU/s (299B max monthly 1KB reads)
    Inserted 166126 docs @ 10992 writes/s, 115198 RU/s (299B max monthly 1KB reads)
    Inserted 173051 docs @ 10739 writes/s, 112544 RU/s (292B max monthly 1KB reads)
    Inserted 180169 docs @ 10527 writes/s, 110324 RU/s (286B max monthly 1KB reads)
    Inserted 192469 docs @ 10616 writes/s, 111256 RU/s (288B max monthly 1KB reads)
    Inserted 199107 docs @ 10406 writes/s, 109054 RU/s (283B max monthly 1KB reads)
    Inserted 200000 docs @ 9930 writes/s, 104065 RU/s (270B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 200000 docs @ 9928 writes/s, 104063 RU/s (270B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.
    Press any key to exit...


**4. lépés (ha szükséges):** Az eszköz által jelentett átviteli -nak (RU/s) azonosnak vagy magasabbnak kell lennie, mint a gyűjtemény vagy a gyűjtemények egy készletének kiosztott átviteli fóka. Ha nem, a DegreeOfParallelism kis lépésekben való növelése segíthet elérni a határt. Ha az átviteli az ügyfélalkalmazás fennsíkok, indítsa el az alkalmazás több példányát további ügyfélgépeken. Ha segítségre van szüksége ezzel a lépéssel, az [Azure Portalon](https://portal.azure.com)támogatási jegyet kell benyújtania.

Miután az alkalmazás fut, megpróbálhat különböző [indexelési szabályzatok](index-policy.md) és [konzisztenciaszintek,](consistency-levels.md) hogy megértsék azok hatását az átviteli és késési. Megtekintheti a forráskódot is, és a saját tesztcsomagjaihoz vagy éles alkalmazásaihoz hasonló konfigurációkat valósíthat meg.

## <a name="next-steps"></a>További lépések
Ebben a cikkben azt vizsgálta, hogyan végezhet teljesítmény- és méretezési tesztelést az Azure Cosmos DB-vel egy .NET konzolalkalmazás használatával. További információkért tekintse át a következő cikkeket:

* [Az Azure Cosmos DB teljesítménytesztelési mintája](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Ügyfélkonfigurációs lehetőségek az Azure Cosmos DB teljesítményének javításához](performance-tips.md)
* [Kiszolgálóoldali particionálás az Azure Cosmos DB-ben](partition-data.md)


