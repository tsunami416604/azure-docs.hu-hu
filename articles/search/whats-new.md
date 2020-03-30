---
title: Új funkcióközlemények
titleSuffix: Azure Cognitive Search
description: Új és továbbfejlesztett funkciók bejelentései, beleértve az Azure Search szolgáltatásátnevezést az Azure Cognitive Search szolgáltatásra.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/24/2020
ms.openlocfilehash: 475f89fc5b33948864fd83c39ee8058ab6908cad
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80247197"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Az Azure Cognitive Search újdonságai

Ismerje meg a szolgáltatás újdonságait. Könyvjelzővel ezt az oldalt, hogy naprakész legyen a szolgáltatással.

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Új szolgáltatásnév

Az Azure Search most átnevezése **Azure Cognitive Search-re,** hogy tükrözze a kognitív képességek és a a i-feldolgozás kibővített (még nem kötelező) használatát az alapvető műveletekben. Az API-verziók, a NuGet-csomagok, a névterek és a végpontok változatlanok maradnak. Az új és a meglévő keresési megoldásokat nem érinti a szolgáltatás névváltozása.

## <a name="feature-announcements"></a>Szolgáltatásközlemények

### <a name="march-2020"></a>2020. március

+ [A natív blob helyreállítható törlése (előzetes verzió)](search-howto-indexing-azure-blob-storage.md#incremental-indexing-and-deletion-detection) azt jelenti, hogy az Azure Cognitive Search ben az Azure Blob Storage indexelő je fel fogja ismerni a helyreállíthatóan törölt állapotú blobokat, és eltávolítja a megfelelő keresési dokumentumot az indexelés során.

+ Új stabil [Management REST API (2020-03-13)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) már elérhető. 

### <a name="february-2020"></a>2020. február

+ [Pii Detection (előzetes verzió)](cognitive-search-skill-pii-detection.md) egy kognitív szakértelem indexelés során használt, amely kinyeri a személyes azonosításra alkalmas adatokat egy bemeneti szöveget, és lehetővé teszi, hogy maszk, hogy a szöveg különböző módokon.

+ [Az egyéni entitáskeresés (előnézet)](cognitive-search-skill-custom-entity-lookup.md ) a szavak és kifejezések egyéni, felhasználó által definiált listájából származó szöveget keres. A lista használatával minden dokumentumot címkéz, amely bármilyen egyező entitással van ellátva. A készség is támogatja bizonyos fokú fuzzy megfelelő, hogy lehet alkalmazni, hogy megtalálja mérkőzések, amelyek hasonlóak, de nem egészen pontos. 

### <a name="january-2020"></a>2020. január

+ [Az ügyfél által felügyelt titkosítási kulcsok](search-security-manage-encryption-keys.md) mostantól általánosan elérhetők. Rest használata esetén a szolgáltatás a `api-version=2019-05-06`használatával érhető el. Felügyelt kód esetén a megfelelő csomag továbbra is [.NET SDK 8.0-preview verzió,](search-dotnet-sdk-migration-version-9.md) annak ellenére, hogy a szolgáltatás nem tekinthető meg. 

+ A keresési szolgáltatáshoz való privát hozzáférés két mechanizmuson keresztül érhető el, mindkettő jelenleg előzetes verzióban érhető el:

  + Korlátozhatja a hozzáférést adott IP-címek használatával `api-version=2019-10-01-Preview` a felügyeleti REST API-t a szolgáltatás létrehozásához. Az előzetes verziójú API új **IpRule** és **NetworkRuleSet** tulajdonságokkal rendelkezik a [CreateOrUpdate API-ban.](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service) Ez az előnézeti funkció a kiválasztott régiókban érhető el. További információ: [A Felügyeleti REST API használata.](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)

  + Jelenleg elérhető egy korlátozott hozzáférésű előzetes verzió, kiépíthet egy Azure Search-szolgáltatás, amely támogatja az Azure Private Endpoint az ügyfelek ugyanazon a virtuális hálózaton lévő kapcsolatokhoz. További információt a [Privát végpont létrehozása biztonságos kapcsolathoz](service-create-private-endpoint.md)című témakörben talál.

### <a name="december-2019"></a>2019. december

+ [Az alkalmazás létrehozása (előzetes verzió)](search-create-app-portal.md) egy új varázsló a portálon, amely letölthető HTML-fájlt hoz létre. A fájl jön-val beágyazott script, amely egy működő "localhost" stílusú webapp, kötve egy index a keresési szolgáltatás. A lapok konfigurálhatók a varázslóban, és tartalmazhatnak keresősávot, eredményterületet, oldalsáv-navigációt és typeahead lekérdezési támogatást. A HTML kapcsolat nélküli módban módosítható a munkafolyamat vagy a megjelenés kiterjesztéséhez vagy testreszabásához.

+ [Hozzon létre egy privát végpontot a biztonságos kapcsolatokhoz (előzetes verzió)](service-create-private-endpoint.md) elmagyarázza, hogyan állíthat be privát kapcsolatot a keresési szolgáltatáshoz való biztonságos kapcsolatokhoz. Ez az előzetes verziós funkció kérésre érhető el, és az [Azure Private Link](../private-link/private-link-overview.md) és az Azure Virtual [Network](../virtual-network/virtual-networks-overview.md) a megoldás részeként használja.

### <a name="november-2019---ignite-conference"></a>2019. november - Ignite konferencia

+ [A növekményes bővítés (előzetes verzió)](cognitive-search-incremental-indexing-conceptual.md) gyorsítótárazást és állapotteljességet ad hozzá a dúsítási folyamathoz, így adott lépéseken vagy fázisokon dolgozhat anélkül, hogy elveszítené a már feldolgozott tartalmat. Korábban a dúsítási folyamat minden módosítása teljes újraépítést igényelt. A növekményes dúsítással a költséges elemzés, különösen a képelemzés kimenete megmarad.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [A dokumentum kinyerése (előzetes verzió)](cognitive-search-skill-document-extraction.md) egy kognitív szakértelem indexelés során, amely lehetővé teszi, hogy kibontsa a fájl tartalmát egy skillset. Korábban a dokumentum feltörése csak a skillset végrehajtása előtt történt. Ezzel a szakértelemmel, akkor is végrehajthatja ezt a műveletet a skillset végrehajtás belül.

+ [A szövegfordítás](cognitive-search-skill-text-translation.md) egy kognitív szakértelem, amelyet az indexelés során használnak, és amely kiértékeli a szöveget, és minden rekordesetében a megadott célnyelvre lefordított szöveget adja vissza.

+ [A Power BI-sablonok](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) a Power BI Desktop tudástárolójában elindíthatják a vizualizációkat és a bővített tartalmak elemzését. Ez a sablon az [Adatok importálása varázslón](knowledge-store-create-portal.md)keresztül létrehozott Azure-táblavetésekhez készült.

+ [Az Azure Data Lake Storage Gen2 (előzetes verzió)](search-howto-index-azure-data-lake-storage.md), [a Cosmos DB Gremlin API (előzetes verzió)](search-howto-index-cosmosdb.md)és a [Cosmos DB Cassandra API (előzetes verzió)](search-howto-index-cosmosdb.md) már támogatott indexelők. Ezen [az űrlapon](https://aka.ms/azure-cognitive-search/indexer-preview)regisztrálhat. A visszaigazoló e-mailt megkapja, miután elfogadta az előnézeti programba.

### <a name="july-2019"></a>2019. július

+ Általánosan elérhető az [Azure Government Cloud](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search)ban.

## <a name="service-updates"></a>Szolgáltatási hírek

Az Azure Cognitive Search [szolgáltatásfrissítési hirdetményei](https://azure.microsoft.com/updates/?product=search&status=all) az Azure webhelyén találhatók.