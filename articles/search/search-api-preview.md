---
title: REST API Version 2019-05-06 – előzetes verzió
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search Service REST API 2019-05-06-es verziója – az előzetes verzió olyan kísérleti funkciókat tartalmaz, mint például a Knowledge Store és az indexelő gyorsítótárazása a növekményes bővítéshez.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/04/2020
ms.openlocfilehash: eb73d614ca94bc1fa007a14f3705e50c74ab9e4f
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922472"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>Azure Cognitive Search Service REST API – Version 2019-05-06 – előzetes verzió

Ez a cikk a Search Service REST API `api-version=2019-05-06-Preview` verzióját ismerteti, amely még nem általánosan elérhető kísérleti funkciókat kínál.

> [!NOTE]
> Az előzetes verziójú funkciók tesztelésre és kísérletezésre használhatók a visszajelzések összegyűjtésének céljával, és változhatnak. Határozottan javasoljuk, hogy az előzetes verziójú API-kat éles alkalmazásokban használja.


## <a name="new-in-2019-05-06-preview"></a>Új a 2019-05-06-ben – előzetes verzió

+ A [növekményes bővítés (előzetes verzió)](cognitive-search-incremental-indexing-conceptual.md) egy alkoholtartalom-növelési folyamatba helyezi a gyorsítótárazást, lehetővé téve a meglévő kimenet újrafelhasználását, ha egy célként megadott módosítás, például egy készségkészlet vagy egy másik objektum frissítése nem módosítja a tartalmat. A gyorsítótárazás csak a készségkészlet által létrehozott dúsított dokumentumokra vonatkozik.

+ [Cosmos db indexelő](search-howto-index-cosmosdb.md) támogatja a MongoDB API (előzetes verzió), a Gremlin API (előzetes verzió) és a Cassandra API (előzetes verzió) használatát.

+ [Azure Data Lake Storage Gen2 indexelő (előzetes verzió)](search-howto-index-azure-data-lake-storage.md) képes a tartalom és a metaadatok indexelésére Data Lake Storage Gen2.

+ A [dokumentumok kinyerése (előzetes verzió)](cognitive-search-skill-document-extraction.md) az indexelés során használt kognitív képesség, amely lehetővé teszi egy fájl tartalmának kicsomagolását egy készségkészlet belülről. Korábban a készségkészlet végrehajtása előtt csak a csinos dokumentum történt. Ennek a képességnek a hozzáadásával ezt a műveletet a készségkészlet végrehajtásán belül is végrehajthatja.

+ A [szöveg fordítása (előzetes verzió)](cognitive-search-skill-text-translation.md) a szöveget kiértékelő indexelés során használt kognitív képesség, amely minden egyes rekord esetében a megadott nyelvre fordított szöveget adja vissza.

+ A [Knowledge Store](knowledge-store-concept-intro.md) a mesterséges intelligencia-alapú alkoholtartalom-növelési folyamat új célja. A fizikai adatstruktúra létezik az Azure Blob Storage-ban és az Azure Table Storage-ban, és akkor jön létre és töltődik fel, amikor olyan indexelő futtat, amely egy kapcsolódó kognitív készségkészlet rendelkezik. A rendszer egy készségkészlet-definíción belül megadta a Tudásbázis definícióját. A Tudásbázis definícióján belül az adatai fizikai szerkezetét az adatformátumot meghatározó *kivetítési* elemekkel szabályozhatja, hogy az adatokat a Table Storage vagy a blob Storage tárolja-e, és hogy van-e több nézet.

## <a name="earlier-preview-features"></a>Korábbi előzetes funkciók

A korábbi előzetes verziókban bejelentett funkciók még nyilvános előzetes verzióban érhetők el. Ha egy korábbi előzetes verziójú API-verzióval rendelkező API-t hív meg, továbbra is használhatja ezt a verziót, vagy átválthat `2019-05-06-Preview`re a várt viselkedés módosítása nélkül.

+ a [moreLikeThis lekérdezési paraméter](search-more-like-this.md) megkeresi az adott dokumentumhoz kapcsolódó dokumentumokat. Ez a funkció korábbi előzetes verziókban található. 

+ A [CSV-blob indexelése](search-howto-index-csv-blobs.md) soronként egy dokumentumot hoz létre, a szöveges Blobok egy dokumentuma helyett.

## <a name="how-to-call-a-preview-api"></a>Előzetes verziójú API meghívása

A régebbi előzetes verziók továbbra is működőképesek, de idővel elavultak lesznek. Ha a kód meghívja `api-version=2016-09-01-Preview` vagy `api-version=2017-11-11-Preview`, akkor a hívások még érvényesek. Azonban csak a legújabb előzetes verzió frissült a újdonságokkal. 

A következő példa szintaxisa az előzetes verziójú API-verzió hívását mutatja be.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Az Azure Cognitive Search szolgáltatás több verzióban is elérhető. További információ: API- [verziók](search-api-versions.md).

## <a name="next-steps"></a>Következő lépések

Tekintse át a Search REST API dokumentációját. Ha problémákba ütközik, kérjen segítséget a [StackOverflow](https://stackoverflow.com/) , vagy [forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Keresési szolgáltatás REST API referenciája](https://docs.microsoft.com/rest/api/searchservice/)