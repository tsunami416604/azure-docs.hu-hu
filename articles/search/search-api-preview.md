---
title: A REST API előzetes verziójának funkciói
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search Service REST API 2019-05-06-es verziója – az előzetes verzió olyan kísérleti funkciókat tartalmaz, mint például a Knowledge Store és az indexelő gyorsítótárazása a növekményes bővítéshez.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 9985e7ac70c5851699839a95d1e23af4dcca35e7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76935100"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Az Azure előzetes verziójának szolgáltatásai Cognitive Search

Ez a cikk a jelenleg előzetes verzióban elérhető funkciókat sorolja fel. A rendszer eltávolítja a listából az előzetes verzióról az általános elérhetőségre átváltott funkciókat. Megtekintheti a [szolgáltatások frissítéseit](https://azure.microsoft.com/updates/?product=search) , illetve a hirdetmények [újdonságait](whats-new.md) az általános elérhetőséggel kapcsolatban.

Előfordulhat, hogy néhány előzetes verziójú funkció elérhető a Portálon és a .NET SDK-ban, a REST API mindig rendelkezik előzetes verziójú szolgáltatásokkal. Az aktuális előzetes verzió API-verziója `2019-05-06-Preview`.

> [!IMPORTANT]
> Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="features-in-public-preview"></a>A nyilvános előzetes verzióban elérhető funkciók

+ Az [egyéni entitások keresése (előzetes verzió)](cognitive-search-skill-custom-entity-lookup.md ) szöveget keres a szavak és kifejezések egyéni, felhasználó által meghatározott listájából. Ezzel a listával minden olyan dokumentumot felcímkéz, amely minden egyező entitással rendelkezik. A képesség emellett olyan zavaros egyezést is támogat, amely a hasonló, de nem pontos egyezések keresésére is alkalmazható. 

+ A személyes adatok [észlelése (előzetes verzió)](cognitive-search-skill-pii-detection.md) az indexelés során használt kognitív képesség, amely a személyazonosításra alkalmas adatokat egy bemeneti szövegből kigyűjti, és lehetővé teszi, hogy az adott szövegtől különböző módokon maszkot adjon.

+ A [növekményes bővítés (előzetes verzió)](cognitive-search-incremental-indexing-conceptual.md) egy alkoholtartalom-növelési folyamatba helyezi a gyorsítótárazást, lehetővé téve a meglévő kimenet újrafelhasználását, ha egy célként megadott módosítás, például egy készségkészlet vagy egy másik objektum frissítése nem módosítja a tartalmat. A gyorsítótárazás csak a készségkészlet által létrehozott dúsított dokumentumokra vonatkozik.

+ [Cosmos db indexelő](search-howto-index-cosmosdb.md) támogatja a MongoDB API (előzetes verzió), a Gremlin API (előzetes verzió) és a Cassandra API (előzetes verzió) használatát.

+ [Azure Data Lake Storage Gen2 indexelő (előzetes verzió)](search-howto-index-azure-data-lake-storage.md) képes a tartalom és a metaadatok indexelésére Data Lake Storage Gen2.

+ A [Knowledge Store (előzetes verzió)](knowledge-store-concept-intro.md) a mesterséges intelligencia-alapú dúsítási folyamat új célja. A fizikai adatstruktúra létezik az Azure Blob Storage-ban és az Azure Table Storage-ban, és akkor jön létre és töltődik fel, amikor olyan indexelő futtat, amely egy kapcsolódó kognitív készségkészlet rendelkezik. A rendszer egy készségkészlet-definíción belül megadta a Tudásbázis definícióját. A Tudásbázis definícióján belül az adatai fizikai szerkezetét az adatformátumot meghatározó *kivetítési* elemekkel szabályozhatja, hogy az adatokat a Table Storage vagy a blob Storage tárolja-e, és hogy van-e több nézet.

+ a [moreLikeThis lekérdezési paramétere (előzetes verzió)](search-more-like-this.md) megkeresi az adott dokumentumhoz kapcsolódó dokumentumokat. Ez a funkció korábbi előzetes verziókban található. 

## <a name="earlier-preview-features"></a>Korábbi előzetes funkciók

A korábbi előzetes verziókban bejelentett funkciók, ha azok nem az általánosan elérhetővé váltanak, még nyilvános előzetes verzióban érhetők el. Ha egy korábbi előzetes verziójú API-verzióval rendelkező API-t hív meg, továbbra is használhatja ezt a verziót, vagy átválthat `2019-05-06-Preview`re a várt viselkedés módosítása nélkül.

## <a name="how-to-call-a-preview-api"></a>Előzetes verziójú API meghívása

A régebbi előzetes verziók továbbra is működőképesek, de idővel elavultak lesznek. Ha a kód meghívja `api-version=2016-09-01-Preview` vagy `api-version=2017-11-11-Preview`, akkor a hívások még érvényesek. Azonban csak a legújabb előzetes verzió frissült a újdonságokkal. 

A következő példa szintaxisa az előzetes verziójú API-verzió hívását mutatja be.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Az Azure Cognitive Search szolgáltatás több verzióban is elérhető. További információ: API- [verziók](search-api-versions.md).

## <a name="next-steps"></a>Következő lépések

Tekintse át a Search REST API dokumentációját. Ha problémákba ütközik, kérjen segítséget a [StackOverflow](https://stackoverflow.com/) , vagy [forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Keresési szolgáltatás REST API referenciája](https://docs.microsoft.com/rest/api/searchservice/)