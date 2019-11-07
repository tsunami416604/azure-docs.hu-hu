---
title: REST API Version 2019-05-06 – előzetes verzió
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search Service REST API 2019-05-06-es verziója – az előzetes verzió olyan kísérleti funkciókat tartalmaz, mint például a Knowledge Store és az ügyfél által felügyelt titkosítási kulcsok.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 24e16942410c72640628bd4120d05a85e68de993
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720026"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>Azure Cognitive Search Service REST API – Version 2019-05-06 – előzetes verzió

Ez a cikk a Search Service REST API `api-version=2019-05-06-Preview` verzióját ismerteti, amely még nem általánosan elérhető kísérleti funkciókat kínál.

> [!NOTE]
> Az előzetes verziójú funkciók tesztelésre és kísérletezésre használhatók a visszajelzések összegyűjtésének céljával, és változhatnak. Határozottan javasoljuk, hogy az előzetes verziójú API-kat éles alkalmazásokban használja.


## <a name="new-in-2019-05-06-preview"></a>Új a 2019-05-06-ben – előzetes verzió

+ A [növekményes indexelés](cognitive-search-incremental-indexing-conceptual.md) új mód az indexeléshez, amely az állapotot és a gyorsítótárazást egy készségkészlet, így lehetővé teszi a meglévő kimenet újbóli felhasználását, ha a forrásadatok, az indexelő és a készségkészlet-definíciók változatlanok. Ez a funkció csak a kognitív készségkészlet meghatározott dúsításokra vonatkozik.

+ [Cosmos db indexelő](search-howto-index-cosmosdb.md) támogatja a MongoDB API-t, a Gremlin API-t és a Cassandra API.

+ [Azure Data Lake Storage Gen2 indexelő](search-howto-index-azure-data-lake-storage.md) képes a tartalom és a metaadatok indexelésére Data Lake Storage Gen2.

+ A [dokumentumok kinyerése (előzetes verzió)](cognitive-search-skill-document-extraction.md) az indexelés során használt kognitív képesség, amely lehetővé teszi egy fájl tartalmának kicsomagolását egy készségkészlet belülről. Korábban a készségkészlet végrehajtása előtt csak a csinos dokumentum történt. Ennek a képességnek a hozzáadásával ezt a műveletet a készségkészlet végrehajtásán belül is végrehajthatja.

+ A [szöveg fordítása (előzetes verzió)](cognitive-search-skill-text-translation.md) a szöveget kiértékelő indexelés során használt kognitív képesség, amely minden egyes rekord esetében a megadott nyelvre fordított szöveget adja vissza.

+ A [Knowledge Store](knowledge-store-concept-intro.md) a mesterséges intelligencia-alapú alkoholtartalom-növelési folyamat új célja. A fizikai adatstruktúra létezik az Azure Blob Storage-ban és az Azure Table Storage-ban, és akkor jön létre és töltődik fel, amikor olyan indexelő futtat, amely egy kapcsolódó kognitív készségkészlet rendelkezik. A rendszer egy készségkészlet-definíción belül megadta a Tudásbázis definícióját. A Tudásbázis definícióján belül az adatai fizikai szerkezetét az adatformátumot meghatározó *kivetítési* elemekkel szabályozhatja, hogy az adatokat a Table Storage vagy a blob Storage tárolja-e, és hogy van-e több nézet.

+ Az [ügyfél által felügyelt titkosítási kulcsok](search-security-manage-encryption-keys.md) a szolgáltatás-oldali titkosításhoz is új előzetes verziójú funkció. A Microsoft által felügyelt beépített titkosítás mellett további titkosítási réteget is alkalmazhat, amelyben Ön a kulcsok egyetlen tulajdonosa...

## <a name="earlier-preview-features"></a>Korábbi előzetes funkciók

A korábbi előzetes verziókban bejelentett funkciók még nyilvános előzetes verzióban érhetők el. Ha egy korábbi előzetes verziójú API-verzióval rendelkező API-t hív meg, továbbra is használhatja ezt a verziót, vagy átválthat `2019-05-06-Preview`re a várt viselkedés módosítása nélkül.

+ a [moreLikeThis lekérdezési paraméter](search-more-like-this.md) megkeresi az adott dokumentumhoz kapcsolódó dokumentumokat. Ez a funkció korábbi előzetes verziókban található. 

+ A [CSV-blob indexelése](search-howto-index-csv-blobs.md) soronként egy dokumentumot hoz létre, a szöveges Blobok egy dokumentuma helyett.

## <a name="how-to-call-a-preview-api"></a>Előzetes verziójú API meghívása

A régebbi előzetes verziók továbbra is működőképesek, de idővel elavultak lesznek. Ha a kód meghívja `api-version=2016-09-01-Preview` vagy `api-version=2017-11-11-Preview`, akkor a hívások még érvényesek. Azonban csak a legújabb előzetes verzió frissült a újdonságokkal. 

A következő példa szintaxisa az előzetes verziójú API-verzió hívását mutatja be.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Az Azure Cognitive Search szolgáltatás több verzióban is elérhető. További információ: API- [verziók](search-api-versions.md).

## <a name="next-steps"></a>További lépések

Tekintse át a Search REST API dokumentációját. Ha problémákba ütközik, kérjen segítséget a [StackOverflow](https://stackoverflow.com/) , vagy [forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Keresési szolgáltatás REST API referenciája](https://docs.microsoft.com/rest/api/searchservice/)