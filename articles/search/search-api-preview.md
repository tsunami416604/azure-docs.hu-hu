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
ms.openlocfilehash: dff6473fd01fc2d41c5bb100eefd583afe4175b0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496476"
---
# <a name="azure-cognitive-search-service-rest-api-version-2019-05-06-preview"></a>Azure Cognitive Search Service REST API – Version 2019-05-06 – előzetes verzió

Ez a cikk a Search Service REST API `api-version=2019-05-06-Preview` verzióját ismerteti, amely még nem általánosan elérhető kísérleti funkciókat kínál.

> [!NOTE]
> Az előzetes verziójú funkciók tesztelésre és kísérletezésre használhatók a visszajelzések összegyűjtésének céljával, és változhatnak. Határozottan javasoljuk, hogy az előzetes verziójú API-kat éles alkalmazásokban használja.


## <a name="new-in-2019-05-06-preview"></a>Új a 2019-05-06-ben – előzetes verzió

[* * A növekményes indexelés](cognitive-search-incremental-indexing-conceptual.md) új mód az indexeléshez, amely az állapotot és a gyorsítótárazást is hozzáadja, így a meglévő kimenet újra felhasználható, ha az adatokat, indexelő és készségkészlet-definíciók változatlanok. Ez a funkció kizárólag kognitív készségkészlet keresztüli dúsításra vonatkozik.

A [**Knowledge Store**](knowledge-store-concept-intro.md) a mesterséges intelligencia-alapú alkoholtartalom-növelési folyamat új célja. Az indexen kívül az Azure Storage-ban az indexelés során létrehozott, feltöltött adatstruktúrák is megmaradhatnak. A Készségkészlet elemein keresztül vezérelheti az adatai fizikai szerkezetét, beleértve az adattárolás módját, az adatokat a Table Storage-ban vagy a blob Storage-ban, valamint azt, hogy több nézet van-e tárolva.

Az [**ügyfél által felügyelt titkosítási kulcsok**](search-security-manage-encryption-keys.md) a szolgáltatás-oldali titkosításhoz is új előzetes verziójú funkció. A Microsoft által felügyelt beépített titkosítás mellett további titkosítási réteget is alkalmazhat, amelyben Ön a kulcsok egyetlen tulajdonosa...

## <a name="other-preview-features"></a>Egyéb előzetes verziójú funkciók

A korábbi előzetes verziókban bejelentett funkciók még nyilvános előzetes verzióban érhetők el. Ha egy korábbi előzetes verziójú API-verzióval rendelkező API-t hív meg, továbbra is használhatja ezt a verziót, vagy átválthat `2019-05-06-Preview`re a várt viselkedés módosítása nélkül.

+ a [moreLikeThis lekérdezési paraméter](search-more-like-this.md) megkeresi az adott dokumentumhoz kapcsolódó dokumentumokat. Ez a funkció korábbi előzetes verziókban található. 
* A [CSV-blob indexelése](search-howto-index-csv-blobs.md) soronként egy dokumentumot hoz létre, a szöveges Blobok egy dokumentuma helyett.
* A [Cosmos db indexelő MongoDB API-támogatása előzetes verzióban](search-howto-index-cosmosdb.md) érhető el.


## <a name="how-to-call-a-preview-api"></a>Előzetes verziójú API meghívása

A régebbi előzetes verziók továbbra is működőképesek, de idővel elavultak lesznek. Ha a kód meghívja `api-version=2016-09-01-Preview` vagy `api-version=2017-11-11-Preview`, akkor a hívások még érvényesek. Azonban csak a legújabb előzetes verzió frissült a újdonságokkal. 

A következő példa szintaxisa az előzetes verziójú API-verzió hívását mutatja be.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Az Azure Cognitive Search szolgáltatás több verzióban is elérhető. További információ: API- [verziók](search-api-versions.md).

## <a name="next-steps"></a>További lépések

Tekintse át a Search REST API dokumentációját. Ha problémákba ütközik, kérjen segítséget a [StackOverflow](https://stackoverflow.com/) , vagy [forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Keresési szolgáltatás REST API referenciája](https://docs.microsoft.com/rest/api/searchservice/)