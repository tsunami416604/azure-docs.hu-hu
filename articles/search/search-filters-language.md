---
title: Nyelvi szűrők az Azure Search szolgáltatásban |} A Microsoft Docs
description: Szűrési feltételek szerint a felhasználó biztonsági azonosítóját, nyelvi, földrajzihely- vagy numerikus értékek keresési eredményei között az Azure Search szolgáltatásban a Microsoft Azure-ban üzemeltetett felhőalapú keresési szolgáltatás lekérdezések csökkentése érdekében.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 10/23/2017
ms.author: heidist
ms.openlocfilehash: 2bacffe64fed3e2ee0cc2eb983776b4ab7086e51
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49466585"
---
# <a name="how-to-filter-by-language-in-azure-search"></a>Az Azure Search nyelven szűrése 

A legfontosabb követelmény, többnyelvű keresés-alkalmazásokban, kereshet, és a felhasználó saját nyelvén eredmények lekéréséhez. Az Azure Search szolgáltatásban egy nyelvet a többnyelvű alkalmazások követelményeinek módja hozzon létre egy dedikált karakterláncok tárolásához egy adott nyelven mezők sorozatát, és ezután korlátozhatja a teljes szöveges keresés csak ezekhez a mezőkhöz lekérdezéskor.

Lekérdezési paraméterek a kérésben mind a keresési műveletet terjed ki, és az eredmények, amelyek nem biztosítják a tartalmat a keresési funkciót szeretne kompatibilis mezőket, majd trim szolgálnak.

| Paraméterek | Cél |
|-----------|--------------|
| **searchFields** | Korlátok teljes szöveges keresés elnevezett mezők listája. |
| **$select** | A válasz csak a megadott mezőket levágja. Alapértelmezés szerint minden lekérdezhető mezők vissza. A **$select** paraméter lehetővé teszi válassza ki, melyiket adja vissza. |

Ezzel a technikával sikere attól függ, hogy integritását, mező tartalmát. Az Azure Search nem karakterláncok fordítása, és hajtsa végre a nyelvfelismerés. Feladata, hogy győződjön meg arról, hogy a mezőket a várt karakterláncokat tartalmaznak.

## <a name="define-fields-for-content-in-different-languages"></a>Tartalom mezők a különböző nyelvekhez

Az Azure Search szolgáltatásban a lekérdezések egyetlen index célként. A fejlesztők számára az egyetlen keresési funkciókat nyelvspecifikus karakterláncok általában biztosít az értékek tárolásához dedikált mezők definiálása: egy mezőt az angol nyelvű tájékoztatáshoz karakterláncok, egy a francia, és így tovább. 

A minták tallózása, beleértve a [ingatlan minta](search-get-started-portal.md) alább látható, előfordulhat, hogy látott Meződefiníciók az alábbi képernyőfelvételhez hasonlóan. Figyelje meg, hogyan Ez a példa bemutatja a nyelvi elemző eszköz-hozzárendelések a mezők az index. Karakterláncokat tartalmazó mezők egy elemző fejthetők vissza a Célnyelv nyelvi szabályainak kezeléséhez párosítva a teljes szöveges keresés jobban hajtsa végre.

  ![](./media/search-filters-language/lang-fields.png)

> [!Note]
> Hitelesítésikód-példák Meződefiníciók nyelvek esetén a megjelenítő, lásd: [(.NET) index definiálása](https://docs.microsoft.com/azure/search/search-create-index-dotnet#define-your-azure-search-index) és [definiálása az indexekben (REST)](https://docs.microsoft.com/azure/search/search-create-index-rest-api#define-your-azure-search-index-using-well-formed-json).

## <a name="build-and-load-an-index"></a>Hozhat létre és index betöltése

Egy közbenső (és talán nyilvánvaló) lépés az kell, hogy [hozhat létre, és töltse fel az index](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index) mielőtt egy lekérdezést. Ebben a lépésben Itt a teljesség megemlíteni azt. Egyik módja határozza meg, hogy rendelkezésre áll-e az index, indexeket lista ellenőrzésével a [portál](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>A lekérdezés megtartása és a tároló visszaigénylésének eredmények

A lekérdezés a paraméterek használhatók korlátozza a keresési bizonyos mezők, és ezután trim nem hasznos a forgatókönyvéhez mezőket eredményeit. Omezující vlastnost keresés cél adott francia karakterláncokat tartalmazó mezők, használhat, **searchFields** , amelyekre az adott nyelveken karakterláncokat tartalmazó mezők a lekérdezésnek. 

Alapértelmezés szerint a keresés minden mező lekérdezhetőként jelölt adja vissza. Ezért érdemes kizárja a mezőket, amelyek nem felelnek meg a nyelvspecifikus keresés élményt szeretne biztosítani. Pontosabban a mező francia karakterláncok a keresést, hogy csak korlátozott, valószínűleg szeretné-e angol nyelvű karakterláncot tartalmazó mezők kizárni az eredményeket. Használatával a **$select** lekérdezési paraméter lehetővé teszi szabályozhatja, hogy a hívó alkalmazás visszaadott mezők fölé.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Bár a lekérdezés no $filter argumentuma van, ezt a használati esetet erősen kapcsolt szűrő fogalmakat, így azt megjelenítheti azokat egy szűrési forgatókönyv.

## <a name="see-also"></a>Lásd még

+ [Szűrők az Azure Search szolgáltatásban](search-filters.md)
+ [Nyelvi elemzők](https://docs.microsoft.com/rest/api/searchservice/language-support)
+ [Teljes szöveges keresés működése az Azure Search szolgáltatásban](search-lucene-query-architecture.md)
+ [REST API-val dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents)

