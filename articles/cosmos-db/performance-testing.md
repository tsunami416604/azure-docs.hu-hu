---
title: Teljesítmény- és mérettesztelés az Azure Cosmos DB tesztelése
description: Ismerje meg, hogyan skálázhatja és az Azure Cosmos DB teljesítménytesztelési. Majd kiértékelheti, hogy a nagy teljesítményű alkalmazás-forgatókönyvek az Azure Cosmos DB funkcióit.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 6d2863e39b5f28c56e2b9045513aa83326d8b8c7
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043192"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Teljesítmény- és mérettesztelés az Azure Cosmos DB tesztelése

Teljesítmény- és mérettesztelés az alkalmazásfejlesztés kulcsfontosságú lépés. Sok alkalmazás esetén az adatbázisszint az általános teljesítmény és méretezhetőség jelentős hatással van. Ezért fontos a Teljesítménytesztelés kritikus összetevője. [Az Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) kifejezetten robotfejlesztési célra készült, rugalmasan méretezhető és megbízható teljesítményt. Ezek a képességek teszik, hogy egy kiválóan alkalmas nagy teljesítményű adatbázis-rétegből igénylő alkalmazások esetében. 

Ez a cikk eszköztáblára teljesítményének tesztelése csomagok az Azure Cosmos DB számítási feladatai végrehajtási fejlesztők számára. Azt is használható nagy teljesítményű alkalmazás-forgatókönyvek az Azure Cosmos DB kiértékeléséhez. Elsősorban az adatbázis elkülönített Teljesítménytesztelés összpontosít, de is tartalmazza az éles környezetben használt alkalmazásokhoz ajánlott eljárások.

Ez a cikk elolvasása után is elérheti az alábbi kérdések megválaszolásához: 

* Hol találhatok egy mintául szolgáló .NET ügyféloldali alkalmazás teljesítményének tesztelése az Azure Cosmos DB? 
* Hogyan érhet el az Azure Cosmos DB magas átviteli sebességekhez a saját ügyfélalkalmazás?

Kód használatának megkezdéséhez töltse le a projektet a [Azure Cosmos DB teljesítménytesztelési minta](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> Ez az alkalmazás az a célja, hogy bemutatják, hogyan lehet beszerezni a legjobb teljesítmény érdekében az Azure Cosmos DB, a ügyfélgépek kis számú. A minta célja nem a maximális átviteli kapacitással (amely minden korlátokat nélkül méretezhető több is) az Azure Cosmos DB eléréséhez.
> 
> 

Ha az Azure Cosmos DB-teljesítmény javítása érdekében: ügyféloldali konfiguráció lehetőségeket keres, tekintse meg [Azure Cosmos DB teljesítménnyel kapcsolatos tippek](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>A Teljesítmény alkalmazás tesztelése
Első lépések a leggyorsabb mód az fordítása és futtatása a .NET-minta a következő lépésben ismertetett módon. Tekintse át a forráskódot, és hasonló konfigurációkat megvalósítani a saját ügyfélalkalmazásait is.

**1. lépés:** Töltse le a projektet a [Azure Cosmos DB teljesítménytesztelési minta](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark), vagy a GitHub-tárház elágaztatása.

**2. lépés:** Módosítsa a beállításokat az EndpointUrl, AuthorizationKey, CollectionThroughput és DocumentTemplate az App.config fájlban (nem kötelező).

> [!NOTE]
> Mielőtt üzembe helyezi és nagy teljesítményű gyűjteményeket, tekintse meg a [díjszabási oldalunkon](https://azure.microsoft.com/pricing/details/cosmos-db/) gyűjteményenként költségek becslése érdekében. Az Azure Cosmos DB keresztül számláz az adott tárolási és átviteli sebesség függetlenül órákra lebontva. Költségeket takaríthat törlése vagy a tesztelés után az Azure Cosmos DB-gyűjtemények átviteli kapacitást csökkentését.
> 
> 

**3. lépés:** Fordítsa le és futtassa a konzolalkalmazást a parancssorból. A következőhöz hasonló kimenetnek kell megjelennie:

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


**4. lépés (ha szükséges):** A jelentett átviteli sebesség (RU/s) az eszköz legyen ugyanaz vagy magasabb, mint a kiosztott átviteli sebesség, a gyűjtemény vagy egy gyűjteményt. Ha nem így van, kis lépésekben az Analyticsunits növelése segíthetnek eléri a korlátot. Ha az átviteli sebesség az ügyfélalkalmazás trületek, indítsa el az alkalmazás több példánya további ügyfélszámítógépeken. Ehhez a lépéshez segítségre van szüksége, ha e-mailt askcosmosdb@microsoft.com vagy a támogatási jegyet a [az Azure portal](https://portal.azure.com).

Miután az alkalmazást futtató, próbálkozzon másik [indexelési szabályzataihoz](index-policy.md) és [konzisztenciaszintek](consistency-levels.md) átviteli sebességgel és késéssel gyakorolt hatásuk megértéséhez. Tekintse át a forráskódot is, és a saját tesztelési megvásárlásával vagy éles üzemi alkalmazások pedig hasonló konfigurációk megvalósításához.

## <a name="next-steps"></a>További lépések
Ebben a cikkben azt tekintett meg, hogyan hajthat végre a teljesítmény és méretezhetőség tesztelése az Azure Cosmos DB .NET-Konzolalkalmazás használatával. További információkért tekintse át a következő cikkeket:

* [Az Azure Cosmos DB-teljesítménytesztelési minta](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Ügyfél-konfigurációs beállításai az Azure Cosmos DB-teljesítmény javítása érdekében](performance-tips.md)
* [Kiszolgálóoldali particionálást az Azure Cosmos DB-ben](partition-data.md)


