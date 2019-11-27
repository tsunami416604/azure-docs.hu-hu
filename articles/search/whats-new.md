---
title: Új funkció bejelentései
titleSuffix: Azure Cognitive Search
description: Új és továbbfejlesztett funkciókkal kapcsolatos bejelentések, beleértve a Azure Search az Azure Cognitive Search-ba történő átnevezését.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f35fcc2604866c2ff5a330b65ae844be0eed9476
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422395"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Az Azure Cognitive Search újdonságai

Ismerje meg a szolgáltatás újdonságait. Könyvjelző ezen az oldalon naprakészen tarthatja a szolgáltatást.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Új szolgáltatás neve Azure Search

A Azure Search most átnevezve lett az **Azure Cognitive Searchra** , hogy tükrözze a kognitív képességek és az AI-feldolgozás kibővített használatát az alapvető műveletekben. Míg a kognitív képességek új képességeket vesznek fel, az AI használata szigorúan nem kötelező. Továbbra is használhatja az Azure Cognitive Search AI-t anélkül, hogy gazdag, teljes szöveges keresési megoldásokat építsen ki magán-, különböző-és szöveges tartalommal a felhőben létrehozott és kezelt indexekben. 

Az API-verziók, a Nuget-csomagok, a névterek és a végpontok változatlanok. A szolgáltatás nevének változása nem érinti a meglévő keresési megoldásokat.

## <a name="feature-announcements"></a>Szolgáltatások hirdetményei

2019. november 4. – Ignite konferencia

+ A [növekményes indexelés (előzetes verzió)](cognitive-search-incremental-indexing-conceptual.md) lehetővé teszi a kiválasztását, és kiválaszthatja, hogy mely lépéseket kell újradolgozni a dúsítási folyamat módosításakor. A növekményes indexelés akkor hasznos, ha olyan képtartalommal rendelkezik, amelyet korábban elemezett. A költséges elemzés kimenete tárolva lesz, és a rendszer a további indexelés vagy bővítés alapjául szolgál.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ A [dokumentumok kinyerése (előzetes verzió)](cognitive-search-skill-document-extraction.md) az indexelés során használt kognitív képesség, amely lehetővé teszi egy fájl tartalmának kicsomagolását egy készségkészlet belülről. Korábban a készségkészlet végrehajtása előtt csak a csinos dokumentum történt. Ennek a képességnek a hozzáadásával ezt a műveletet a készségkészlet végrehajtásán belül is végrehajthatja.

+ A [szöveg fordítása (előzetes verzió)](cognitive-search-skill-text-translation.md) a szöveget kiértékelő indexelés során használt kognitív képesség, amely minden egyes rekord esetében a megadott nyelvre fordított szöveget adja vissza.

+ A [Power bi-sablonok](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) a Power bi Desktopban található Tudásbázisban megadhatják a gazdagított tartalmak vizualizációit és elemzését. Ez a sablon az [adatimportálás varázsló](knowledge-store-create-portal.md)segítségével létrehozott Azure Table-kivetítésekhez készült.

+ A [Azure Data Lake Storage Gen2 (előzetes verzió)](search-howto-index-azure-data-lake-storage.md), a [Cosmos db Gremlin API (előzetes verzió)](search-howto-index-cosmosdb.md)és a [Cosmos db Cassandra API (előzetes verzió)](search-howto-index-cosmosdb.md) mostantól támogatott az indexelő szolgáltatásban. [Az űrlap](https://aka.ms/azure-cognitive-search/indexer-preview)használatával regisztrálhat. Ha elfogadja az előzetes programot, egy visszaigazoló e-mailt fog kapni.

Július 24 2019

+ Általánosan elérhető [Azure Government felhőben](https://docs.microsoft.com/azure/azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search).

## <a name="service-updates"></a>Szolgáltatási hírek

Az Azure Cognitive Search [Service Update-hirdetményei](https://azure.microsoft.com/updates/?product=search&status=all) megtalálhatók az Azure webhelyén.