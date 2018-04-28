---
title: Az Azure Search nyelvi szűrők |} Microsoft Docs
description: Szűrési feltételek felhasználó biztonsági azonosítóját, nyelvi, földrajzihely-vagy numerikus értékek lekérdezések az Azure Search, egy üzemeltetett felhőalapú keresőszolgáltatás, a Microsoft Azure keresési eredményeket csökkentése érdekében.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.workload: search
ms.date: 10/23/2017
ms.author: heidist
ms.openlocfilehash: 6d7fa7ab6db1fe9f8e2d1530c2917f4716a38079
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-filter-by-language-in-azure-search"></a>Az Azure Search nyelv szűrése 

A többnyelvű alkalmazás fő követelmény a keresés során, és a felhasználó saját nyelven eredmények beolvasásához. Az Azure Search egy többnyelvű alkalmazás elégíteni a nyelv módja létrehozhat egy adott nyelven karakterláncok tárolására kijelölt mezők, és majd korlátozza a teljes szöveges keresés csak az adott mezők időben.

Lekérdezés-paraméterek a kérésben használt a keresési művelet hatókörét, és majd trim minden mezőt, amely nem kompatibilis a keresési élményt kézbesíteni szeretné a tartalom eredményeit.

| Paraméterek | Cél |
|-----------|--------------|
| **searchFields** | Korlátok teljes szöveges keresés elnevezett mezőket. |
| **$select** | Levágja a választ csak a megadott mezőket. Alapértelmezés szerint minden lekérhető mezőt adott vissza. A **$select** paraméter lehetővé teszi, hogy kiválasztja, melyeket való visszatéréshez. |

Ez a módszer sikere attól függ, hogy integritását, mező tartalmát. Az Azure Search nem karakterlánc vagy lefordítása nyelvi észlelés végrehajtására. Csak akkor lehet, hogy a mező a várt karakterláncok tartalmaz esetén.

## <a name="define-fields-for-content-in-different-languages"></a>Tartalom mezők a különböző nyelveken

Az Azure Search lekérdezések célként egyetlen index. A fejlesztők számára egy egyetlen keresési élményt nyújt nyelvspecifikus karakterláncok általában arra az értékek tárolásához dedikált mezők megadása: egy mezőt az angol nyelvű tájékoztatáshoz karakterláncok, egy a francia, és így tovább. 

Az általunk biztosított mintákat, beleértve a [ingatlan minta](search-get-started-portal.md) alább látható, előfordulhat, hogy láthatta mező definíciókat az alábbi képernyőfelvételhez hasonló. Figyelje meg, hogyan Ez a példa bemutatja a nyelvi analyzer hozzárendelések a mezők az index. Mezők, amelyek tartalmazzák a teljesítményt a teljes szöveges keresés, ha egy analyzer fejthetők vissza a megadott nyelv nyelvi szabályainak kezeléséhez.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Kód mező definíciója nyelvek elemzőkkel megjelenítő, tekintse meg a [(.NET) index definiálása az](https://docs.microsoft.com/azure/search/search-create-index-dotnet#define-your-azure-search-index) és [(REST) index definiálása az](https://docs.microsoft.com/azure/search/search-create-index-rest-api#define-your-azure-search-index-using-well-formed-json).

## <a name="build-and-load-an-index"></a>Build és betölteni egy indexet

Egy közbenső (és esetleg nyilvánvaló) lépés, hogy kell [felépítéséhez és az index feltöltése](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index) mielőtt egy lekérdezésben. Ebben a lépésben Itt a teljesség megemlíteni azt. Annak meghatározásához, hogy rendelkezésre áll-e az index egy úgy, hogy az indexek lista ellenőrzés a [portal](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>A lekérdezés korlátozni, és a vágás eredmények

A lekérdezési paraméterek segítségével bizonyos mezők keresés korlátozódjon, valamint majd trim minden mező nem lehet hasznos, adott esetben az eredmények. Adott cél korlátozási keresés francia karakterláncokat tartalmazó mezőket, használhatja **searchFields** , amelyekre a lekérdezésnek a végrehajtása ezen a nyelven karakterláncokat tartalmazó mezőket. 

Alapértelmezés szerint a keresés minden mező szerint lekérhető megjelölt adja vissza. Így előfordulhat, hogy kizárni kívánt mezőket, amelyek nem felelnek meg a nyelvspecifikus keresési élményt szeretne biztosítani. Pontosabban Ha korlátozott keresési francia karakterláncok mezőhöz, érdemes lehet mezőket a angol nyelvű karakterláncok kizárni az eredményeket. Használja a **$select** paraméterrel állítható be, mely mezők keresztül a rendszer visszairányítja a hívó alkalmazás lekérdezése.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Bár a lekérdezés no $filter argumentuma van, a használati eset erősen tagja-e szűrő fogalmakat, így azt jelenthet, mint egy szűrési forgatókönyv.

## <a name="see-also"></a>Lásd még

+ [Az Azure Search szűrők](search-filters.md)
+ [Nyelvi elemzők](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Hogyan teljes szöveges keresés az Azure Search működik](search-lucene-query-architecture.md)
+ [REST API-t dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents)

