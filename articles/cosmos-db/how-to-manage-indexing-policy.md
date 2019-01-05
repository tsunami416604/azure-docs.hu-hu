---
title: Megismerheti, hogyan kezelhet adatbázisfiókokat az Azure Cosmos DB-ben.
description: Megismerheti, hogyan kezelhet adatbázisfiókokat az Azure Cosmos DB-ben.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/10/2018
ms.author: mjbrown
ms.openlocfilehash: c27cee4842c0e65e1737f100a215cff82a0fd439
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033094"
---
# <a name="manage-indexing-in-azure-cosmos-db"></a>Indexelés az Azure Cosmos DB kezelése

Az Azure Cosmos DB kiválaszthatja, hogy kívánja-e a tároló összes elem automatikus indexelését, vagy nem. Alapértelmezés szerint az Azure Cosmos-tároló összes elemének automatikusan indexelt, de a felhasználók bármikor kikapcsolhatják az Automatikus indexelés. Ha az indexelés ki van kapcsolva, elemek érhetők el azok önmagukra mutató hivatkozások vagy az elem, például egy dokumentum id azonosítója használatával a lekérdezések. Explicit módon kiszolgálására eredményeket ad index használata nélkül is kérhető **x-ms-documentdb-enable-vizsgálat** fejléc REST API-ban vagy a **EnableScanInQuery** kérhet a beállítás megadásával a. NET SDK-T.

Az Automatikus indexelés ki van kapcsolva, az index elemeit szelektív továbbra is hozzáadhat. Ezzel szemben Automatikus indexelés bekapcsolva hagyhatja, és szelektív ki szeretne zárni bizonyos elemek. Be-és kikapcsolása konfigurációk az indexelés akkor hasznos, ha rendelkezik, amelyeket kérdezhető le kell elemek egy részhalmazához.  

Az írási teljesítmény- és kérésegységek a csomagban foglalt készlet az indexelési szabályzat által megadott értékeket, amelyeket szeretne indexelni, számával arányos. Ha lekérdezési minták beható ismerete, explicit módon kiválaszthatja az elérési utak a írási teljesítmény növelése érdekében beszámítása vagy kihagyása részhalmazát.

## <a name="manage-indexing-using-azure-portal"></a>Indexelés az Azure portal használatával kezelése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Hozzon létre egy új Azure Cosmos-fiókot, vagy válasszon ki egy meglévő fiókot.

3. Nyissa meg a **adatkezelő** ablaktáblán.

4. Jelöljön ki egy meglévő tárolót, bontsa ki azt, és módosítsa a következő értékeket:

   * Nyissa meg a **méretezés és beállítások** ablak.
   * Változás **indexingMode** a *konzisztens* való *nincs* vagy az egyes elérési utakat az indexelő belefoglalása vagy kizárása.
   * A módosítások mentéséhez kattintson az **OK** gombra.

   ![Az Azure portal segítségével indexelési kezelése](./media/how-to-manage-indexing/how-to-manage-indexing-portal.png)

## <a name="manage-indexing-using-azure-sdks"></a>Indexelés az Azure SDK-k használatával kezelése

### <a id="dotnet"></a>.NET SDK

A következő minta bemutatja, hogyan tartalmazzák egy elem explicit módon a [SQL API .NET SDK-val](sql-api-sdk-dotnet.md) és a [RequestOptions.IndexingDirective](/dotnet/api/microsoft.azure.documents.client.requestoptions.indexingdirective) tulajdonság.

```csharp
// To override the default behavior to exclude (or include) a document in indexing,
// use the RequestOptions.IndexingDirective property.
client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new { id = "myDocumentId", isRegistered = true },
    new RequestOptions { IndexingDirective = IndexingDirective.Include });
```

## <a name="next-steps"></a>További lépések

További információ az indexelő az alábbi cikkeket:

* [Az indexelő áttekintése](index-overview.md)
* [Indexelési házirend](index-policy.md)
* [Index típusa](index-types.md)
* [Index elérési utak](index-paths.md)
