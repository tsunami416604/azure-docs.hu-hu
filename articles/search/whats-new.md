---
title: A szolgáltatás újdonságai
titleSuffix: Azure Cognitive Search
description: Új és továbbfejlesztett funkciókkal kapcsolatos bejelentések, beleértve a Azure Search az Azure Cognitive Search-ba történő átnevezését.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e81eaf232e3234ac4de0cfb7412e23709f0c0b99
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73549096"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Az Azure Cognitive Search újdonságai

Ismerje meg a szolgáltatás újdonságait. A lapon lévő könyvjelzővel naprakészen tarthatja a szolgáltatást.

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Új szolgáltatás neve Azure Search

A Azure Search most átnevezve lett az **Azure Cognitive Searchra** , hogy tükrözze a kognitív képességek és az AI-feldolgozás kibővített használatát az alapvető műveletekben. Míg a kognitív képességek új képességeket vesznek fel, az AI használata szigorúan nem kötelező. Továbbra is használhatja az Azure Cognitive Search AI-t anélkül, hogy gazdag, teljes szöveges keresési megoldásokat építsen ki magán-, különböző-és szöveges tartalommal a felhőben létrehozott és kezelt indexekben. 

Az API-verziók, a Nuget-csomagok, a névterek és a végpontok változatlanok. A szolgáltatás nevének változása nem érinti a meglévő keresési megoldásokat.

## <a name="feature-announcements"></a>Szolgáltatások hirdetményei

2019. november 4. – Ignite konferencia

+ Az előzetes verzióban elérhető [növekményes indexelés](cognitive-search-incremental-indexing-conceptual.md)lehetővé teszi, hogy csak azokat a lépéseket dolgozza fel vagy dolgozza fel újra, amelyek a dúsítási folyamat módosításakor feltétlenül szükségesek. Ez különösen akkor hasznos, ha olyan képtartalommal rendelkezik, amelyet korábban elemezett. A költséges elemzés kimenete tárolva lesz, és a rendszer a további indexelés vagy bővítés alapjául szolgál.

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ A [dokumentumok kinyerése](cognitive-search-skill-document-extraction.md) az indexelés során használt kognitív képesség, amely lehetővé teszi egy fájl tartalmának kicsomagolását egy készségkészlet belülről. Korábban a készségkészlet végrehajtása előtt csak a csinos dokumentum történt. Ennek a képességnek a hozzáadásával ezt a műveletet a készségkészlet végrehajtásán belül is végrehajthatja.

+ A [szöveg fordítása](cognitive-search-skill-text-translation.md) olyan kognitív képesség, amely a szöveget kiértékelő indexelés során használatos, és minden rekord esetében a megadott nyelvre fordított szöveget adja vissza.

+ A [Power bi-sablonok](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md) a Power bi Desktopban található Tudásbázisban megadhatják a gazdagított tartalmak vizualizációit és elemzését. Ez a sablon az [adatimportálás varázsló](knowledge-store-create-portal.md)segítségével létrehozott Azure Table-kivetítésekhez készült.

## <a name="service-updates"></a>Szolgáltatási hírek

Az Azure Cognitive Search [Service Update-hirdetményei](https://azure.microsoft.com/updates/?product=search&status=all) megtalálhatók az Azure webhelyén.