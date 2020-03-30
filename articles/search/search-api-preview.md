---
title: A REST API előzetes verzióinak funkciói
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search szolgáltatás REST API-verziója 2019-05-06-Preview kísérleti funkciókat tartalmaz, például a tudástárolót és az indexelő gyorsítótárazását a növekményes bővítéshez.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: db941152186127302680b5e659e43cd2d82a8908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162276"
---
# <a name="preview-features-in-azure-cognitive-search"></a>Az Azure Cognitive Search előzetes verziói

Ez a cikk az előzetes verzióban elérhető funkciókat sorolja fel. Az előnézetről az általános elérhetőségre áttűnő funkciók törlődnek a listából. Az általános elérhetőségre vonatkozó hirdetmények értése a [Szervizfrissítések](https://azure.microsoft.com/updates/?product=search) vagy az [Újdonságok](whats-new.md) oldalon található.

Bár egyes előzetes verziójú funkciók elérhetők lehetnek a portálon és a .NET SDK-ban, a REST API mindig rendelkezik előzetes verziójú funkciókkal.

+ A keresési műveletek, [**`2019-05-06-Preview`**](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview) a jelenlegi előnézeti verzió.
+ A felügyeleti műveletek, [**`2019-10-01-Preview`**](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) a jelenlegi előzetes verzió.

> [!IMPORTANT]
> Az előzetes verzió funkció szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="ai-enrichment-features"></a>AI dúsítási funkciók

Fedezze fel a a ai-bővítés legújabb fejlesztéseit az [előzetes Keresési API-val.](https://docs.microsoft.com/rest/api/searchservice/index-2019-05-06-preview)

|||
|-|-|
| [Egyéni entitáskövetési szakértelem (előzetes verzió)](cognitive-search-skill-custom-entity-lookup.md ) | Olyan kognitív szakértelem, amely egy egyéni, felhasználó által definiált szavak és kifejezések listájából származó szöveget keres. A lista használatával minden dokumentumot címkéz, amely bármilyen egyező entitással van ellátva. A készség is támogatja bizonyos fokú fuzzy megfelelő, hogy lehet alkalmazni, hogy megtalálja mérkőzések, amelyek hasonlóak, de nem egészen pontos. | 
| [Személyazonosításra vonatkozó észlelési képesség (előzetes verzió)](cognitive-search-skill-pii-detection.md) | Az indexelés során használt kognitív szakértelem, amely kinyeri a személyes azonosításra alkalmas adatokat egy bemeneti szövegből, és lehetővé teszi, hogy maszk, hogy a szöveg különböző módokon.| 
| [Növekményes dúsítás (előzetes verzió)](cognitive-search-incremental-indexing-conceptual.md) | Gyorsítótárazást ad hozzá egy dúsítási folyamathoz, így újra felhasználhatja a meglévő kimenetet, ha egy célzott módosítás, például egy skillset vagy egy másik objektum frissítése nem módosítja a tartalmat. A gyorsítótárazás csak a skillset által készített bővített dokumentumokra vonatkozik.| 
| [Tudástároló (előzetes verzió)](knowledge-store-concept-intro.md) | A mi-alapú dúsítási folyamat új célállomása. A fizikai adatstruktúra létezik az Azure Blob storage és az Azure Table storage, és jön létre, és feltölti, ha egy indexelő, amely egy csatolt kognitív skillset. A tudástároló definíciója egy skillset definícióban van meghatározva. A tudástároló definícióján belül az adatok fizikai struktúráit *vetítési* elemeksegítségével szabályozhatja, amelyek meghatározzák az adatok alakításának módját, az adatok táblázat- vagy Blob-tárolóban való tárolását, valamint azt, hogy több nézet van-e.| 

## <a name="indexing-and-query-features"></a>Indexelési és lekérdezési szolgáltatások

Az Indexelő előzetes verziófunkciói az előzetes keresési API-ban érhetők el. 

|||
|-|-|
| [Cosmos DB indexelő](search-howto-index-cosmosdb.md) | MongoDB API (előzetes verzió), Gremlin API (előzetes verzió) és Cassandra API (előzetes verzió) API-típusok támogatása. | 
|  [Azure Data Lake Storage Gen2 indexelő (előzetes verzió)](search-howto-index-azure-data-lake-storage.md) | Tartalom és metaadatok indexelése a Data Lake Storage Gen2-ből.| 
| [moreLikeThis lekérdezési paraméter (előzetes verzió)](search-more-like-this.md) | Megkeresi az adott dokumentumhoz kapcsolódó dokumentumokat. Ez a funkció már a korábbi előzetesek. | 

## <a name="management-features"></a>Felügyeleti funkciók

|||
|-|-|
| [Privát végpont támogatása](service-create-private-endpoint.md) | Létrehozhat egy virtuális hálózatot egy biztonságos ügyféllel (például egy virtuális géppel), majd létrehozhat egy privát végpontot használó keresési szolgáltatást. |
| IP-hozzáférés korlátozása | A [`api-version=2019-10-01-Preview`](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) Felügyeleti REST API használatával létrehozhat egy olyan szolgáltatást, amely korlátozza, hogy mely IP-címek férhetnek hozzá. |

## <a name="earlier-preview-features"></a>Korábbi előzetes funkciók

A korábbi előzetesekben bejelentett funkciók, ha nem váltak át az általános elérhetőségre, továbbra is nyilvános előzetes verzióban vannak. Ha egy korábbi előzetes api-verzióval rendelkező API-t hív meg, továbbra `2019-05-06-Preview` is használhatja ezt a verziót, vagy átválthat a várt viselkedés módosítása nélkül.

## <a name="how-to-call-a-preview-api"></a>Preview API-hívás a hogyan

A régebbi előzetesek még mindig működőképesek, de idővel elavulttá válnak. Ha a `api-version=2016-09-01-Preview` kód `api-version=2017-11-11-Preview`hívások vagy , ezek a hívások továbbra is érvényesek. Azonban csak a legújabb előzetes verzió frissül a fejlesztésekkel. 

Az alábbi példa szintaxisa az előzetes API-verzió hívását mutatja be.

    GET https://[service name].search.windows.net/indexes/[index name]/docs?search=*&api-version=2019-05-06-Preview

Az Azure Cognitive Search szolgáltatás több verzióban érhető el. További információ: [API-verziók](search-api-versions.md).

## <a name="next-steps"></a>További lépések

Tekintse át a Search REST API referenciadokumentációját. Ha problémákba ütközik, kérjen segítséget a [StackOverflow-val](https://stackoverflow.com/) kapcsolatban, vagy [forduljon az ügyfélszolgálathoz.](https://azure.microsoft.com/support/community/?product=search)

> [!div class="nextstepaction"]
> [KERESÉSszolgáltatás REST API-jának hivatkozása](https://docs.microsoft.com/rest/api/searchservice/)