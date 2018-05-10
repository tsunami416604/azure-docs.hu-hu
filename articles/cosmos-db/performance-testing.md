---
title: Azure Cosmos DB méretezés és teljesítmény tesztelése |} Microsoft Docs
description: Ismerje meg, hogyan hajthat végre a méretezés és teljesítmény az Azure Cosmos DB tesztelése
keywords: A teljesítmény tesztelése
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
documentationcenter: ''
ms.assetid: f4c96ebd-f53c-427d-a500-3f28fe7b11d0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: sngun
ms.openlocfilehash: e4995146433d0090849fe17175b152a6e45809f4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Teljesítmény és méretezhetőség, az Azure Cosmos DB tesztelése

Teljesítmény- és mérettesztelés az alkalmazásfejlesztés kulcsfontosságú lépés. Több alkalmazás az adatbázis-rétegből átfogó teljesítményét és méretezhetőségét jelentős hatással van. Ezért a teljesítmény tesztelése kritikus összetevője. [Az Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) erre a célra kialakított rugalmasan méretezhető és kiszámítható teljesítményt. Ezek a képességek egy nagy teljesítményű adatbázis-rétegből igénylő alkalmazásokhoz kiváló méretezése révén. 

Ez a cikk a fejlesztők számára az Azure Cosmos DB munkaterhelések teljesítményét teszt csomagok végrehajtási hivatkozás. Azt is használható Azure Cosmos DB kiértékelendő nagy teljesítményű alkalmazás-forgatókönyveket. Elsősorban az adatbázis vizsgálati elkülönített teljesítmény összpontosít, de ajánlott eljárások az üzemi környezetben működő alkalmazásokat is tartalmazza.

A cikk elolvasása után képes lesz a következő kérdések megválaszolásához: 

* Hol találnak egy minta .NET ügyfélalkalmazás Azure Cosmos DB teljesítmény teszteléshez? 
* Hogyan elérése a saját ügyfélalkalmazás Azure Cosmos DB magas teljesítmény szintek?

Első lépésként kóddal, töltse le a projektet a [Azure Cosmos DB teljesítménytesztelési minta](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> Ez az alkalmazás célja bemutatják, hogyan lehet lekérdezni a legjobb teljesítmény érdekében az Azure Cosmos Adatbázisból ügyfélgépek kis számú. A minta célja nem a maximális átviteli sebesség (Ez a korlátozás nélküli méretezhető) Azure Cosmos-adatbázis eléréséhez.
> 
> 

Ha Azure Cosmos DB teljesítmény javítása érdekében az ügyféloldali konfigurációs beállítások keres, tekintse meg [Azure Cosmos DB teljesítmény tippek](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Futtatható az alkalmazás tesztelése
Lásd az alábbi lépéseket: első lépések a leggyorsabban fordításához és futtatásához a .NET minta. Is ajánlott felülvizsgálni a forráskódot, és hasonló konfigurációkat megvalósítani a saját ügyfélalkalmazásait.

**1. lépés:** töltse le a projektet a [Azure Cosmos DB teljesítménytesztelési minta](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark), vagy oszthatja ketté a GitHub-tárházban.

**2. lépés:** módosíthatja a végponti URL-cím, AuthorizationKey, CollectionThroughput és DocumentTemplate az App.config fájlban (nem kötelező) beállításait.

> [!NOTE]
> Gyűjtemények nagy átviteli sebességgel kiépítése előtt tekintse meg a [árazás lap](https://azure.microsoft.com/pricing/details/cosmos-db/) becsléséhez gyűjteményenként költségeket. Az Azure Cosmos DB váltók tárolási és átviteli egymástól függetlenül óránkénti alapon. Költségeket takaríthat törlése, és így csökkenti az átviteli sebessége a Azure Cosmos DB gyűjtemények tesztelés után.
> 
> 

**3. lépés:** fordítsa le és futtassa a konzolalkalmazást a parancssorból. A következőhöz hasonló kimenetnek kell megjelennie:

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


**(Ha szükséges), a 4. lépés:** az eszköze (RU/mp) jelentett átviteli ugyanaz legyen, vagy nagyobb, mint a létesített átviteli sebesség a gyűjtemény vagy egy gyűjteményt. Ha nem, a kis lépésekkel DegreeOfParallelism növelése segíthetnek eléri a határértéket. Ha az ügyfél alkalmazásból átviteli trületek, indítsa el az alkalmazás több példánya további ügyfélszámítógépekre. Ha ebben a lépésben segítségre van szüksége, e-mailben askcosmosdb@microsoft.com vagy a támogatási jegy fájl a [Azure-portálon](https://portal.azure.com).

Miután az alkalmazás futását, próbálkozzon másik [házirendek indexelő](indexing-policies.md) és [konzisztenciaszintek](consistency-levels.md) átviteli sebesség és a késleltetés gyakorolt hatásuk megértéséhez. Is ajánlott felülvizsgálni a forráskódot, és a saját tesztelési programcsomagok vagy üzemi környezetben működő alkalmazásokhoz hasonló konfigurációkat valósítja meg.

## <a name="next-steps"></a>További lépések
Ez a cikk azt venni, hogyan hajthat végre a teljesítmény és méretezhetőség Azure Cosmos DB tesztelték a .NET-Konzolalkalmazás használatával. További információkért tekintse át a következő cikkeket:

* [Az Azure Cosmos adatbázis teljesítményének tesztelésekor minta](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Ügyfél-konfigurációs beállítások Azure Cosmos DB teljesítmény javítása érdekében](performance-tips.md)
* [Kiszolgálóoldali particionálás az Azure Cosmos-Adatbázisba](partition-data.md)


