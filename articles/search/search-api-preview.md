---
title: Előzetes verzió REST API Azure Search 2019-05-06 – előzetes verzió – Azure Search
description: A Azure Search Service REST API 2019-05-06-es verziója – az előzetes verzió olyan kísérleti funkciókat tartalmaz, mint például a Knowledge Store és az ügyfél által felügyelt titkosítási kulcsok.
services: search
author: HeidiSteen
manager: nitinme
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: search
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 7fa280742556b7bc42d2c7fb30c880f836eef62b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69649978"
---
# <a name="azure-search-service-rest-api-version-2019-05-06-preview"></a>Azure Search szolgáltatás REST API-verziója 2019-05-06 – előzetes verzió
Ez a cikk a `api-version=2019-05-06-Preview` Azure Search Service REST API verzióját ismerteti, amely még nem általánosan elérhető kísérleti funkciókat kínál.

> [!NOTE]
> Az előzetes verziójú funkciók tesztelésre és kísérletezésre használhatók a visszajelzések összegyűjtésének céljával, és változhatnak. Határozottan javasoljuk, hogy az előzetes verziójú API-kat éles alkalmazásokban használja.


## <a name="new-in-2019-05-06-preview"></a>Új a 2019-05-06-ben – előzetes verzió

A [**Knowledge Store**](knowledge-store-concept-intro.md) a mesterséges intelligencia-alapú alkoholtartalom-növelési folyamat új célja. Az indexen kívül az Azure Storage-ban az indexelés során létrehozott, feltöltött adatstruktúrák is megmaradhatnak. A Készségkészlet elemein keresztül vezérelheti az adatai fizikai szerkezetét, beleértve az adattárolás módját, az adatokat a Table Storage-ban vagy a blob Storage-ban, valamint azt, hogy több nézet van-e tárolva.

Az [**ügyfél által felügyelt titkosítási kulcsok**](search-security-manage-encryption-keys.md) a szolgáltatás-oldali titkosításhoz is új előzetes verziójú funkció. A Microsoft által felügyelt beépített titkosítás mellett további titkosítási réteget is alkalmazhat, amelyben Ön a kulcsok egyetlen tulajdonosa...

## <a name="other-preview-features"></a>Egyéb előzetes verziójú funkciók

A korábbi előzetes verziókban bejelentett funkciók még nyilvános előzetes verzióban érhetők el. Ha olyan API-t hív meg, amely korábban előzetes verziójú API-verziót használ, továbbra is használhatja ezt a verziót `2019-05-06-Preview` , vagy váltson a szolgáltatásra a várt működés módosítása nélkül.

+ a [moreLikeThis lekérdezési paraméter](search-more-like-this.md) megkeresi az adott dokumentumhoz kapcsolódó dokumentumokat. Ez a funkció korábbi előzetes verziókban található. 
* A [CSV-blob indexelése](search-howto-index-csv-blobs.md) soronként egy dokumentumot hoz létre, a szöveges Blobok egy dokumentuma helyett.
* A [Cosmos db indexelő MongoDB API-támogatása](search-howto-index-cosmosdb.md) előzetes verzióban érhető el.


## <a name="how-to-call-a-preview-api"></a>Előzetes verziójú API meghívása

A régebbi előzetes verziók továbbra is működőképesek, de idővel elavultak lesznek. Ha a kód `api-version=2016-09-01-Preview` meghívja `api-version=2017-11-11-Preview`a vagy a-t, a hívások még érvényesek. Azonban csak a legújabb előzetes verzió frissült a újdonságokkal. 

A következő példa szintaxisa az előzetes verziójú API-verzió hívását mutatja be.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Azure Search szolgáltatás több verzióban is elérhető. További információ: API- [verziók](search-api-versions.md).

## <a name="next-steps"></a>További lépések

Tekintse át a Azure Search Service REST API dokumentációját. Ha problémákba ütközik, kérjen segítséget a [StackOverflow](https://stackoverflow.com/) , vagy [forduljon](https://azure.microsoft.com/support/community/?product=search)az ügyfélszolgálathoz.

> [!div class="nextstepaction"]
> [Keresési szolgáltatás REST API referenciája](https://docs.microsoft.com/rest/api/searchservice/)