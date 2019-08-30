---
title: Többnyelvű indexelés a nem angol nyelvű keresési lekérdezésekhez – Azure Search
description: A Azure Search támogatja az 56 nyelv használatát, a Lucene és a természetes nyelvi feldolgozási technológiából származó nyelvi elemzők kihasználása a Microsofttól.
author: yahnoosh
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: jlembicz
ms.openlocfilehash: 9d2e6418eb925f0d113b7e9a91463951ca52031a
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186561"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-search"></a>Index létrehozása több nyelvhez Azure Search

Az indexek olyan mezőket tartalmazhatnak, amelyek több nyelvből származó tartalmat tartalmaznak, például egyéni mezők létrehozása a nyelvspecifikus karakterláncokhoz. Az indexelés és a lekérdezés során a legjobb eredmények elérése érdekében rendeljen hozzá egy nyelvi elemzőt, amely a megfelelő nyelvi szabályokat tartalmazza. 

A Azure Search a Lucene és a Microsofttól származó nyelvi elemzők széles választékát kínálja, amelyeket az analizátor tulajdonság használatával rendelhet hozzá az egyes mezőkhöz. Az ebben a cikkben leírtak szerint megadhat egy Language Analyzert is a portálon.

## <a name="add-analyzers-to-fields"></a>Elemzők hozzáadása a mezőkhöz

Egy adott mező létrehozásakor nyelvi analizátor van megadva. Az analizátor meglévő mezőhöz való hozzáadásához felül kell írni (és újra kell betölteni) az indexet, vagy egy új mezőt kell létrehoznia, amely megegyezik az eredetivel, de egy Analyzer-hozzárendeléssel. Ezután törölheti a nem használt mezőt a kényelemben.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és keresse meg a keresési szolgáltatást.
1. Kattintson az **index hozzáadása** lehetőségre a szolgáltatás irányítópultjának tetején található parancssáv új index elindításához, vagy nyisson meg egy meglévő indexet, hogy a meglévő indexhez hozzáadott új mezőkön állítson be egy elemzőt.
1. Egy mező definíciójának elindításához adjon meg egy nevet.
1. Válassza ki a EDM. String adattípust. Csak a karakterlánc mezők teljes szöveges kereshetők.
1. A **kereshető** attribútum beállításával engedélyezheti az analizátor tulajdonságot. A szövegmezőnek Text-alapúnak kell lennie ahhoz, hogy használni lehessen a nyelvi elemzőt.
1. Válasszon egyet a rendelkezésre álló elemzők közül. 

![Nyelvi elemzők kiosztása a mező meghatározása során](media/search-language-support/select-analyzer.png "Nyelvi elemzők kiosztása a mező meghatározása során")

Alapértelmezés szerint minden kereshető mező a [szabványos Lucene](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) -elemzőt használja, amely nyelvtől független. A támogatott elemzők teljes listájának megtekintéséhez lásd: [nyelvi elemzők hozzáadása egy Azure Search indexhez](index-add-language-analyzers.md).

A portálon az elemzők célja, hogy használhatók legyenek. Ha testreszabásra vagy szűrők és tokenizers egyedi konfigurációra van szüksége, hozzon [létre egy egyéni elemzőt](index-add-custom-analyzers.md) a kódban. A portál nem támogatja az egyéni elemzők kijelölését és konfigurálását.

## <a name="query-language-specific-fields"></a>Nyelvspecifikus mezők lekérdezése

Miután kiválasztotta a Language Analyzert egy mezőhöz, a rendszer az adott mezőhöz tartozó összes indexelési és keresési kéréssel együtt használja. Ha egy lekérdezés több mezőhöz lett kiadva különböző elemzők használatával, a lekérdezés az egyes mezőkhöz rendelt elemzők egymástól függetlenül lesznek feldolgozva.

Ha a lekérdezést kiállító ügynök nyelve ismert, a keresési kérelem hatóköre egy adott mezőre is kiterjed a **searchFields** lekérdezési paraméter használatával. A következő lekérdezés csak a lengyel nyelvű leírásban lesz kibocsátva:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

Az indexet lekérdezheti a portálról, a [**keresési ablak**](search-explorer.md) használatával beillesztheti a fent láthatóhoz hasonló lekérdezést.

## <a name="boost-language-specific-fields"></a>Nyelvi specifikus mezők fellendítése

Előfordulhat, hogy a lekérdezést kiállító ügynök nyelve nem ismert, ebben az esetben a lekérdezés egyszerre adható ki az összes mezővel. Ha szükséges, az eredmények adott nyelven való megadását [pontozási profilok](index-add-scoring-profiles.md)használatával lehet meghatározni. Az alábbi példában az angol nyelvű leírásban található egyezések a lengyel és francia nyelvekhez képest magasabb pontszámot kapnak:

    "scoringProfiles": [
      {
        "name": "englishFirst",
        "text": {
          "weights": { "description_en": 2 }
        }
      }
    ]

`https://[service name].search.windows.net/indexes/[index name]/docs?search=Microsoft&scoringProfile=englishFirst&api-version=2019-05-06`

## <a name="next-steps"></a>További lépések

Ha .NET-fejlesztőként dolgozik, vegye figyelembe, hogy a [Azure Search .net SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) és az [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) tulajdonság használatával konfigurálhatja a nyelvi elemzőket. 