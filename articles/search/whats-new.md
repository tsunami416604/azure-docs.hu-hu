---
title: Új funkció bejelentései
titleSuffix: Azure Cognitive Search
description: Új és továbbfejlesztett funkciókkal kapcsolatos bejelentések, beleértve a Azure Search az Azure Cognitive Search-ba történő átnevezését.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 03/10/2020
ms.openlocfilehash: 3150eed063413d0665adfc104a603f9f2b5755c3
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129507"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Az Azure Cognitive Search újdonságai

Ismerje meg a szolgáltatás újdonságait. Könyvjelző ezen az oldalon naprakészen tarthatja a szolgáltatást.

<a name="new-service-name"></a>

## <a name="new-service-name"></a>Új szolgáltatás neve

A Azure Search most átnevezve lett az **Azure Cognitive Searchra** , hogy tükrözze a kognitív képességek kibővített (de opcionális) használatát a fő műveletekben. Az API-verziók, a NuGet-csomagok, a névterek és a végpontok változatlanok. Az új és a meglévő keresési megoldásokat nem érinti a szolgáltatás nevének változása.

## <a name="feature-announcements"></a>Szolgáltatások hirdetményei

### <a name="march-2020"></a>Március 2020

+ Most már elérhető az új stabil [felügyeleti REST API (2020-03-13)](https://docs.microsoft.com/rest/api/searchmanagement/management-api-versions) . 

### <a name="february-2020"></a>2020. február

+ A személyes adatok [észlelése (előzetes verzió)](cognitive-search-skill-pii-detection.md) az indexelés során használt kognitív képesség, amely a személyazonosításra alkalmas adatokat egy bemeneti szövegből kigyűjti, és lehetővé teszi, hogy az adott szövegtől különböző módokon maszkot adjon.

+ Az [egyéni entitások keresése (előzetes verzió)](cognitive-search-skill-custom-entity-lookup.md ) szöveget keres a szavak és kifejezések egyéni, felhasználó által meghatározott listájából. Ezzel a listával minden olyan dokumentumot felcímkéz, amely minden egyező entitással rendelkezik. A képesség emellett olyan zavaros egyezést is támogat, amely a hasonló, de nem pontos egyezések keresésére is alkalmazható. 

### <a name="january-2020"></a>2020. január

+ Az [ügyfél által felügyelt titkosítási kulcsok](search-security-manage-encryption-keys.md) már általánosan elérhetők. Ha REST-t használ, `api-version=2019-05-06`használatával érheti el a szolgáltatást. A felügyelt kód esetében a megfelelő csomag még mindig a [.net SDK 8,0-es verziója – előzetes verzió](search-dotnet-sdk-migration-version-9.md) , bár a funkció nem előzetes verzió. 

+ A keresési szolgáltatásokhoz való privát hozzáférés két, jelenleg előzetes verzióban elérhető mechanizmuson keresztül érhető el:

  + A szolgáltatás létrehozásához a felügyeleti REST API `api-version=2019-10-01-Preview` használatával korlátozhatja a hozzáférést adott IP-címekhez. Az előzetes verziójú API új **IpRule** és **NetworkRuleSet** tulajdonságokkal rendelkezik a [CreateOrUpdate API](https://docs.microsoft.com/rest/api/searchmanagement/2019-10-01-preview/createorupdate-service)-ban. Ez az előzetes verziójú funkció a kiválasztott régiókban érhető el. További információ: [a felügyeleti REST API használata](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

  + Jelenleg korlátozott hozzáférésű előzetes verzióban érhető el, ha olyan Azure Search szolgáltatást is kiépít, amely támogatja az Azure Private-végpontot az azonos virtuális hálózatban lévő ügyfelekkel létesített kapcsolatokhoz. További információ: [privát végpont létrehozása biztonságos kapcsolatban](service-create-private-endpoint.md).

### <a name="december-2019"></a>2019. december

+ Az [alkalmazás létrehozása (előzetes verzió)](search-create-app-portal.md) egy új varázsló a portálon, amely egy letölthető HTML-fájlt hoz létre. A fájl beágyazott parancsfájlt tartalmaz, amely egy operatív "localhost" stílusú webalkalmazást jelenít meg, amely egy indexhez van kötve a keresési szolgáltatásban. A lapok konfigurálhatók a varázslóban, és tartalmazhatnak egy keresősáv, az eredmények, az oldalsáv és a typeahead-lekérdezés támogatását. A HTML-t a munkafolyamat vagy a megjelenés kibővítéséhez vagy testreszabásához offline módon módosíthatja.

+ [Hozzon létre egy privát végpontot a biztonságos kapcsolatokhoz (előzetes verzió)](service-create-private-endpoint.md) ismerteti, hogyan állíthat be egy privát hivatkozást a keresési szolgáltatáshoz való biztonságos csatlakozáshoz. Ez az előzetes verziójú funkció igény szerint elérhető, és az Azure [Private link](../private-link/private-link-overview.md) és az [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) használja a megoldás részeként.

### <a name="november-2019---ignite-conference"></a>November 2019 – Ignite konferencia

+ A [növekményes bővítés (előzetes verzió)](cognitive-search-incremental-indexing-conceptual.md) gyorsítótárazást és statefullness tesz elérhetővé egy alkoholtartalom-növelési folyamathoz, így adott lépéseken vagy fázisokon dolgozhat a már feldolgozott tartalmak elvesztése nélkül. Korábban a dúsítási folyamat bármilyen módosítása teljes Újraépítés szükséges. A növekményes bővítéssel a költséges elemzések, különösen a képelemzések kimenete megmarad.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ A [dokumentumok kinyerése (előzetes verzió)](cognitive-search-skill-document-extraction.md) az indexelés során használt kognitív képesség, amely lehetővé teszi egy fájl tartalmának kicsomagolását egy készségkészlet belülről. Korábban a készségkészlet végrehajtása előtt csak a csinos dokumentum történt. Ennek a képességnek a hozzáadásával ezt a műveletet a készségkészlet végrehajtásán belül is végrehajthatja.

+ A [szöveg fordítása](cognitive-search-skill-text-translation.md) olyan kognitív képesség, amely a szöveget kiértékelő indexelés során használatos, és minden rekord esetében a megadott nyelvre fordított szöveget adja vissza.

+ A [Power bi-sablonok](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) a Power bi Desktopban található Tudásbázisban megadhatják a gazdagított tartalmak vizualizációit és elemzését. Ez a sablon az [adatimportálás varázsló](knowledge-store-create-portal.md)segítségével létrehozott Azure Table-kivetítésekhez készült.

+ A [Azure Data Lake Storage Gen2 (előzetes verzió)](search-howto-index-azure-data-lake-storage.md), a [Cosmos db Gremlin API (előzetes verzió)](search-howto-index-cosmosdb.md)és a [Cosmos db Cassandra API (előzetes verzió)](search-howto-index-cosmosdb.md) mostantól támogatott az indexelő szolgáltatásban. [Az űrlap](https://aka.ms/azure-cognitive-search/indexer-preview)használatával regisztrálhat. Ha elfogadja az előzetes programot, egy visszaigazoló e-mailt fog kapni.

### <a name="july-2019"></a>2019. július

+ Általánosan elérhető [Azure Government felhőben](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Szolgáltatási hírek

Az Azure Cognitive Search [Service Update-hirdetményei](https://azure.microsoft.com/updates/?product=search&status=all) megtalálhatók az Azure webhelyén.