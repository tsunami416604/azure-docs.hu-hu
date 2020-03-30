---
title: Többnyelvű indexelés nem angol nyelvű keresési lekérdezésekhez
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search 56 nyelvet támogat, kihasználva a Lucene és a Microsoft Természetes nyelvi feldolgozási technológiájának nyelvi elemzőit.
manager: nitinme
author: yahnoosh
ms.author: jlembicz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ca2bc66c755da2011cc7016f37b194caa6200d9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793594"
---
# <a name="how-to-create-an-index-for-multiple-languages-in-azure-cognitive-search"></a>Index létrehozása több nyelvhez az Azure Cognitive Search szolgáltatásban

Az indexek tartalmazhatnak több nyelvű tartalmat tartalmazó mezőket, például egyedi mezőket hozhatnak létre a nyelvspecifikus karakterláncokhoz. Az indexelés és a lekérdezés során a legjobb eredmény érdekében rendeljen hozzá egy nyelvi elemzőt, amely biztosítja a megfelelő nyelvi szabályokat. 

Az Azure Cognitive Search a Lucene és a Microsoft nyelvi elemzőinek széles választékát kínálja, amelyek az Elemző tulajdonságot használó egyes mezőkhöz rendelhetők hozzá. A portálon is megadhat egy nyelvi elemzőt, a cikkben leírtak szerint.

## <a name="add-analyzers-to-fields"></a>Analizátorok hozzáadása mezőkhöz

A mező létrehozásakor meg van adva egy nyelvi elemző. Egy analizátor hozzáadása egy meglévő meződefinícióhoz felül írja felül (és újratölti) az indexet, vagy létre kell létrehozni egy új mezőt, amely megegyezik az eredetivel, de egy elemző hozzárendeléssel. Ezután törölheti a fel nem használt mezőt, ha önnek megfelel.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg a keresési szolgáltatást.
1. Új index indításához kattintson az **Index hozzáadása** gombra a szolgáltatás irányítópultjának tetején lévő parancssávon, vagy nyisson meg egy meglévő indexet egy meglévő indexhez hozzáadott új mezők elemzőjének beállításához.
1. Meződefiníció indítása név megadásával.
1. Válassza az Edm.String adattípust. Csak a karakterláncmezők kereshetőek teljes szöveges kereséssel.
1. Állítsa be a **Kereshető** attribútumot az Analyzer tulajdonság engedélyezéséhez. A nyelvi elemző használatához a mezőnek szövegalapúnak kell lennie.
1. Válasszon egyet a rendelkezésre álló elemzők közül. 

![Nyelvi elemzők hozzárendelése a meződefiníció során](media/search-language-support/select-analyzer.png "Nyelvi elemzők hozzárendelése a meződefiníció során")

Alapértelmezés szerint az összes kereshető mező a [Standard Lucene analizátort](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/analysis/standard/StandardAnalyzer.html) használja, amely nyelv-független. A támogatott elemzők teljes listájának megtekintéséhez olvassa el a [Nyelvi elemzők hozzáadása az Azure Cognitive Search indexhez című témakört.](index-add-language-analyzers.md)

A portálon az analizátorokat a következőképpen kívánják használni. Ha testreszabásra vagy a szűrők és tokenizerek adott konfigurációjára van szüksége, [hozzon létre egy egyéni elemzőt](index-add-custom-analyzers.md) a kódban. A portál nem támogatja az egyéni elemzők kiválasztását vagy konfigurálását.

## <a name="query-language-specific-fields"></a>Nyelvspecifikus mezők lekérdezése

Miután kiválasztotta a nyelvi elemzőt egy mezőhöz, a program az adott mező höz szükséges minden indexelési és keresési kérelemhez használni fogja. Ha egy lekérdezést több, különböző elemzőket használó mező ellen adnak ki, a lekérdezést az egyes mezőkhöz hozzárendelt elemzők egymástól függetlenül dolgozzák fel.

Ha a lekérdezést kiállító ügynök nyelve ismert, a search request a **searchFields** lekérdezési paraméter használatával egy adott mezőre is hatókört kaphat. A következő lekérdezés csak a leírás sal szemben kerül kiadásra lengyel nyelven:

`https://[service name].search.windows.net/indexes/[index name]/docs?search=darmowy&searchFields=PolishContent&api-version=2019-05-06`

Lekérdezheti az indexet a portálról, a [**Kereséskezelő**](search-explorer.md) segítségével beillesztheti a fent láthatóhoz hasonló lekérdezést.

## <a name="boost-language-specific-fields"></a>Nyelvspecifikus mezők kiemelése

Előfordulhat, hogy a lekérdezést kiállító ügynök nyelve nem ismert, amely esetben a lekérdezés egyszerre minden mezőre kiadható. Szükség esetén az eredmények adott nyelven való beállítása [pontozási profilok](index-add-scoring-profiles.md)használatával definiálható. Az alábbi példában az angol nyelvű leírásban található egyezések a lengyel és a francia egyezéshez képest magasabb pontszámot kapnak:

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

Ha Ön .NET-fejlesztő, vegye figyelembe, hogy az [Azure Cognitive Search .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Search) és az [Analyzer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzer?view=azure-dotnet) tulajdonság használatával konfigurálhatja a nyelvi elemzőket. 