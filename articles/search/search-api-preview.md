---
title: A REST API előzetes verziójának funkciói
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search Service REST API 2019-05-06-es verziója – az előzetes verzió olyan kísérleti funkciókat tartalmaz, mint például a Knowledge Store és az indexelő gyorsítótárazása a növekményes bővítéshez.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: db941152186127302680b5e659e43cd2d82a8908
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77162276"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Az Azure előzetes verziójának szolgáltatásai Cognitive Search

Ez a cikk a jelenleg előzetes verzióban elérhető funkciókat sorolja fel. A rendszer eltávolítja a listából az előzetes verzióról az általános elérhetőségre átváltott funkciókat. Megtekintheti a [szolgáltatások frissítéseit](https://azure.microsoft.com/updates/?product=search) , illetve a hirdetmények [újdonságait](whats-new.md) az általános elérhetőséggel kapcsolatban.

Előfordulhat, hogy néhány előzetes verziójú funkció elérhető a Portálon és a .NET SDK-ban, a REST API mindig rendelkezik előzetes verziójú szolgáltatásokkal.

+ A keresési műveletek [**`2019-05-06-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview) esetében az aktuális előzetes verzió.
+ A felügyeleti műveletek [**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) esetében az aktuális előzetes verzió.

> [!IMPORTANT]
> Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="ai-enrichment-features"></a>AI-dúsítási funkciók

Ismerje meg az AI-bővítés legújabb fejlesztéseit az [előzetes verziójú keresési API](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview)-n keresztül.

|||
|-|-|
| [Egyéni entitás keresési képességei (előzetes verzió)](cognitive-search-skill-custom-entity-lookup.md ) | Egy kognitív képesség, amely a szavak és kifejezések egyéni, felhasználó által definiált listájáról keres szöveget. Ezzel a listával minden olyan dokumentumot felcímkéz, amely minden egyező entitással rendelkezik. A képesség emellett olyan zavaros egyezést is támogat, amely a hasonló, de nem pontos egyezések keresésére is alkalmazható. | 
| [Személyes adatok észlelésének jártassága (előzetes verzió)](cognitive-search-skill-pii-detection.md) | Az indexelés során használt kognitív képesség, amellyel a rendszer beolvassa a személyazonosításra alkalmas adatokat egy bemeneti szövegből, és lehetővé teszi, hogy az adott szövegtől különböző módokon maszkot adjon.| 
| [Növekményes gazdagodás (előzetes verzió)](cognitive-search-incremental-indexing-conceptual.md) | Gyorsítótárazást ad hozzá egy alkoholtartalom-növelési folyamathoz, amely lehetővé teszi a meglévő kimenet újrafelhasználását, ha egy célként megadott módosítás, például egy készségkészlet vagy egy másik objektum frissítése nem módosítja a tartalmat. A gyorsítótárazás csak a készségkészlet által létrehozott dúsított dokumentumokra vonatkozik.| 
| [Knowledge Store (előzetes verzió)](knowledge-store-concept-intro.md) | Egy mesterséges intelligencia-alapú dúsítási folyamat új célja. A fizikai adatstruktúra létezik az Azure Blob Storage-ban és az Azure Table Storage-ban, és akkor jön létre és töltődik fel, amikor olyan indexelő futtat, amely egy kapcsolódó kognitív készségkészlet rendelkezik. A rendszer egy készségkészlet-definíción belül megadta a Tudásbázis definícióját. A Tudásbázis definícióján belül az adatai fizikai szerkezetét az adatformátumot meghatározó *kivetítési* elemekkel szabályozhatja, hogy az adatokat a Table Storage vagy a blob Storage tárolja-e, és hogy van-e több nézet.| 

## <a name="indexing-and-query-features"></a>Indexelési és lekérdezési funkciók

Az indexelő előzetes verziójának funkciói elérhetők az előzetes verziójú keresési API-ban. 

|||
|-|-|
| [Cosmos DB indexelő](search-howto-index-cosmosdb.md) | A MongoDB API (előzetes verzió), a Gremlin API (előzetes verzió) és a Cassandra API (előzetes verzió) API-típusok támogatása. | 
|  [Azure Data Lake Storage Gen2 indexelő (előzetes verzió)](search-howto-index-azure-data-lake-storage.md) | Tartalom és metaadatok indexelése Data Lake Storage Gen2ból.| 
| [moreLikeThis lekérdezési paraméter (előzetes verzió)](search-more-like-this.md) | Megkeresi az adott dokumentumhoz kapcsolódó dokumentumokat. Ez a funkció korábbi előzetes verziókban található. | 

## <a name="management-features"></a>Felügyeleti funkciók

|||
|-|-|
| [Privát végpontok támogatása](service-create-private-endpoint.md) | Létrehozhat egy biztonságos ügyféllel (például virtuális géppel) rendelkező virtuális hálózatot, majd létrehozhat egy privát végpontot használó keresési szolgáltatást. |
| IP-hozzáférés korlátozása | A [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) felügyeleti REST API használatával olyan szolgáltatásokat hozhat létre, amelyek korlátozásokkal rendelkeznek, amelyek IP-címei engedélyezve vannak. |

## <a name="earlier-preview-features"></a>Korábbi előzetes funkciók

A korábbi előzetes verziókban bejelentett funkciók, ha azok nem az általánosan elérhetővé váltanak, még nyilvános előzetes verzióban érhetők el. Ha olyan API-t hív meg, amely korábban előzetes verziójú API-verziót használ, továbbra is használhatja ezt a verziót `2019-05-06-Preview` , vagy váltson a szolgáltatásra a várt működés módosítása nélkül.

## <a name="how-to-call-a-preview-api"></a>Előzetes verziójú API meghívása

A régebbi előzetes verziók továbbra is működőképesek, de idővel elavultak lesznek. Ha a kód `api-version=2016-09-01-Preview` meghívja `api-version=2017-11-11-Preview`a vagy a-t, a hívások még érvényesek. Azonban csak a legújabb előzetes verzió frissült a újdonságokkal. 

A következő példa szintaxisa az előzetes verziójú API-verzió hívását mutatja be.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Az Azure Cognitive Search szolgáltatás több verzióban is elérhető. További információ: API- [verziók](search-api-versions.md).

## <a name="next-steps"></a>További lépések

Tekintse át a Search REST API dokumentációját. Ha problémákba ütközik, kérjen segítséget a [StackOverflow](https://stackoverflow.com/) , vagy [forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Keresési szolgáltatás REST API referenciája](https://docs.microsoft.com/rest/api/searchservice/)