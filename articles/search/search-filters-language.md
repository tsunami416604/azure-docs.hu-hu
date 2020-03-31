---
title: Szűrés nyelv szerint a keresési indexben
titleSuffix: Azure Cognitive Search
description: A többnyelvű keresés támogatásához szűrje a feltételeket, és a lekérdezés végrehajtását nyelvspecifikus mezőkre kell szűrni.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 5dbf32610e54df4ff009d4cb0a0b080babb4ec73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112061"
---
# <a name="how-to-filter-by-language-in-azure-cognitive-search"></a>Szűrés nyelv szerint az Azure Cognitive Search-ben 

A többnyelvű keresőalkalmazások egyik legfontosabb követelménye a felhasználók saját nyelvén történő keresés és eredmények beolvasása. Az Azure Cognitive Search, egy többnyelvű alkalmazás nyelvi követelményeinek teljesítésének egyik módja, hogy hozzon létre egy sor mezőt, amely egy adott nyelven tárolja a karakterláncokat, és majd korlátozza a teljes szöveges keresést csak ezekre a mezőkre lekérdezési időben.

A kérelem lekérdezési paraméterei a keresési művelet hatóköréhez is használhatók, majd levágják azon mezők eredményeit, amelyek nem biztosítanak olyan tartalmat, amely kompatibilis a kívánt keresési felülettel.

| Paraméterek | Cél |
|-----------|--------------|
| **keresési mezők** | A teljes szöveges keresést az elnevezett mezők listájára korlátozza. |
| **$select** | A válasz csak a megadott mezőket tartalmazza. Alapértelmezés szerint a program minden visszakereshető mezőt visszaad. A **$select** paraméter lehetővé teszi, hogy kiválassza, melyiket adja vissza. |

Ennek a technikának a sikere a mezőtartalom integritásán múlik. Az Azure Cognitive Search nem fordítja le a karakterláncokat, és nem hajt végre nyelvfelismerést. Ez rajtad múlik, hogy győződjön meg arról, hogy a mezők tartalmazzák a húrok vár.

## <a name="define-fields-for-content-in-different-languages"></a>Mezők definiálása a különböző nyelvű tartalomhoz

Az Azure Cognitive Search, lekérdezések célja egyetlen index. Azok a fejlesztők, akik egyetlen keresési élményben szeretnének nyelvspecifikus karakterláncokat biztosítani, általában dedikált mezőket határoznak meg az értékek tárolására: egy mező az angol karakterláncok, egy francia és így tovább. 

A mintáinkban, beleértve az alábbi [ingatlanmintát](search-get-started-portal.md) is, előfordulhat, hogy az alábbi képernyőképhez hasonló meződefiníciókat látott. Figyelje meg, hogy ez a példa hogyan jeleníti meg az index mezőinek nyelvi elemzőhozzárendeléseit. A karakterláncokat tartalmazó mezők jobban teljesítenek a teljes szöveges keresésben, ha a célnyelv nyelvi szabályainak kezelésére tervezett analizátorsal párosítják.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> A nyelvi elemzőket tartalmazó meződefiníciókat bemutató kódpéldákat az [Index (.NET) definiálása](https://docs.microsoft.com/azure/search/search-create-index-dotnet) és [Az index (REST) definiálása című](search-create-index-rest-api.md)témakörben találja.

## <a name="build-and-load-an-index"></a>Index létrehozása és betöltése

Egy köztes (és talán nyilvánvaló) lépés az, hogy létre kell adnia és fel kell [népesítenie az indexet a](https://docs.microsoft.com/azure/search/search-create-index-dotnet) lekérdezés létrehozása előtt. A teljesség gel együtt megemlítjük ezt a lépést. Az index rendelkezésre áll-e annak meghatározásához, hogy a [portálon](https://portal.azure.com)az indexek listáját ellenőrzi.

## <a name="constrain-the-query-and-trim-results"></a>A lekérdezés és a vágás eredményének korlátozása

A lekérdezés paraméterei a keresés adott mezőkre való korlátozására, majd a forgatókönyv szempontjából hasznos mezők eredményeinek levágására szolgálnak. Ha a keresést francia karakterláncokat tartalmazó mezőkre korlátozza, a **searchFields** segítségével az adott nyelven karakterláncokat tartalmazó mezőkre célozhatja meg a lekérdezést. 

Alapértelmezés szerint a keresés minden olyan mezőt visszaad, amely visszakereshetőként van megjelölve. Ezért érdemes kizárni azokat a mezőket, amelyek nem felelnek meg a biztosítani kívánt nyelvspecifikus keresési élménynek. Pontosabban, ha egy francia karakterláncú mezőre csak korlátozott anamnézist szeretne keresni, akkor valószínűleg ki szeretné zárni az angol karakterláncokat az eredményekből. A **$select** lekérdezési paraméter segítségével szabályozhatja, hogy mely mezők et adja vissza a rendszer a hívó alkalmazásnak.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Bár nincs $filter argumentum a lekérdezésben, ez a használati eset erősen kapcsolódik a szűrő fogalmak, ezért bemutatjuk azt a szűrési forgatókönyv.

## <a name="see-also"></a>Lásd még

+ [Szűrők az Azure Cognitive Search szolgáltatásban](search-filters.md)
+ [Nyelvi elemzők](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [A teljes szöveges keresés működése az Azure Cognitive Searchben](search-lucene-query-architecture.md)
+ [Dokumentumok keresése – REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)

