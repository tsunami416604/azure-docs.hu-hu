---
title: Új funkció bejelentései
titleSuffix: Azure Cognitive Search
description: Új és továbbfejlesztett funkciókkal kapcsolatos bejelentések, beleértve a Azure Search az Azure Cognitive Search-ba történő átnevezését.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 0ce2884a2382c7dff2bdb90bd92934609675f314
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834392"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Az Azure Cognitive Search újdonságai

Ismerje meg a szolgáltatás újdonságait. Könyvjelző ezen az oldalon naprakészen tarthatja a szolgáltatást.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Új szolgáltatás neve Azure Search

A Azure Search most átnevezve lett az **Azure Cognitive Searchra** , hogy tükrözze a kognitív képességek és az AI-feldolgozás kibővített használatát az alapvető műveletekben. Míg a kognitív képességek új képességeket vesznek fel, az AI használata szigorúan nem kötelező. Továbbra is használhatja az Azure Cognitive Search AI-t anélkül, hogy gazdag, teljes szöveges keresési megoldásokat építsen ki magán-, különböző-és szöveges tartalommal a felhőben létrehozott és kezelt indexekben. 

Az API-verziók, a Nuget-csomagok, a névterek és a végpontok változatlanok. A szolgáltatás nevének változása nem érinti a meglévő keresési megoldásokat.

## <a name="feature-announcements"></a>Szolgáltatások hirdetményei

### <a name="january-2020"></a>Január 2020

+ Az [ügyfél által felügyelt titkosítási kulcsok](search-security-manage-encryption-keys.md) már általánosan elérhetők. Ha REST-t használ, `api-version=2019-05-06`használatával érheti el a szolgáltatást. A felügyelt kód esetében a megfelelő csomag még mindig a [.net SDK 8,0-es verziója – előzetes verzió](search-dotnet-sdk-migration-version-9.md) , bár a funkció nem előzetes verzió. 

+ A keresési szolgáltatás végpontján a *korlátozott IP-hozzáférés és a magánhálózati végpont (előzetes verzió)* már elérhető az **API-Version = 2019-10 -01-Preview-** ban. A biztonságos végpontot a [Létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/searchmanagement/services/createorupdate) felügyeleti REST API új **IpRule** és **NetworkRuleSet** tulajdonságaival állíthatja be. További információ az API-verziókról és a regionális elérhetőségről: [a felügyeleti REST API használata](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api).

### <a name="december-2019"></a>2019. december

+ Az [alkalmazás létrehozása (előzetes verzió)](search-create-app-portal.md) egy új varázsló a portálon, amely egy letölthető HTML-fájlt hoz létre. A fájl beágyazott parancsfájlt tartalmaz, amely egy operatív "localhost" stílusú webalkalmazást jelenít meg, amely egy indexhez van kötve a keresési szolgáltatásban. A lapok konfigurálhatók a varázslóban, és tartalmazhatnak egy keresősáv, az eredmények, az oldalsáv és a typeahead-lekérdezés támogatását. A HTML-t a munkafolyamat vagy a megjelenés kibővítéséhez vagy testreszabásához offline módon módosíthatja.

### <a name="november-2019---ignite-conference"></a>November 2019 – Ignite konferencia

+ A [növekményes bővítés (előzetes verzió)](cognitive-search-incremental-indexing-conceptual.md) gyorsítótárazást és statefullness tesz elérhetővé egy alkoholtartalom-növelési folyamathoz, így adott lépéseken vagy fázisokon dolgozhat a már feldolgozott tartalmak elvesztése nélkül. Korábban a dúsítási folyamat bármilyen módosítása teljes Újraépítés szükséges. A növekményes bővítéssel a költséges elemzések, különösen a képelemzések kimenete megmarad.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ A [dokumentumok kinyerése (előzetes verzió)](cognitive-search-skill-document-extraction.md) az indexelés során használt kognitív képesség, amely lehetővé teszi egy fájl tartalmának kicsomagolását egy készségkészlet belülről. Korábban a készségkészlet végrehajtása előtt csak a csinos dokumentum történt. Ennek a képességnek a hozzáadásával ezt a műveletet a készségkészlet végrehajtásán belül is végrehajthatja.

+ A [szöveg fordítása (előzetes verzió)](cognitive-search-skill-text-translation.md) a szöveget kiértékelő indexelés során használt kognitív képesség, amely minden egyes rekord esetében a megadott nyelvre fordított szöveget adja vissza.

+ A [Power bi-sablonok](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) a Power bi Desktopban található Tudásbázisban megadhatják a gazdagított tartalmak vizualizációit és elemzését. Ez a sablon az [adatimportálás varázsló](knowledge-store-create-portal.md)segítségével létrehozott Azure Table-kivetítésekhez készült.

+ A [Azure Data Lake Storage Gen2 (előzetes verzió)](search-howto-index-azure-data-lake-storage.md), a [Cosmos db Gremlin API (előzetes verzió)](search-howto-index-cosmosdb.md)és a [Cosmos db Cassandra API (előzetes verzió)](search-howto-index-cosmosdb.md) mostantól támogatott az indexelő szolgáltatásban. [Az űrlap](https://aka.ms/azure-cognitive-search/indexer-preview)használatával regisztrálhat. Ha elfogadja az előzetes programot, egy visszaigazoló e-mailt fog kapni.

### <a name="july-2019"></a>2019. július

+ Általánosan elérhető [Azure Government felhőben](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Szolgáltatási hírek

Az Azure Cognitive Search [Service Update-hirdetményei](https://azure.microsoft.com/updates/?product=search&status=all) megtalálhatók az Azure webhelyén.