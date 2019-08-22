---
title: OData teljes szöveges keresési függvény referenciája – Azure Search
description: A OData teljes szöveges keresési függvények, keresés. ismatch és Search. ismatchscoring, Azure Search lekérdezésekben.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: c3b28c8799b09ddfe008df8539709c5a704ac6b4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648018"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>OData teljes szöveges keresési függvények a Azure Search- `search.ismatch` és`search.ismatchscoring`

Azure Search támogatja a `search.ismatch` teljes szöveges keresést a [OData-szűrési kifejezések](query-odata-filter-orderby-syntax.md) és `search.ismatchscoring` függvények használatával. Ezek a függvények lehetővé teszik, hogy a teljes szöveges keresést szigorú logikai szűréssel kombinálja olyan módokon, amelyek nem csak a `search` [Search API](https://docs.microsoft.com/rest/api/searchservice/search-documents)legfelső szintű paraméterével használhatók.

> [!NOTE]
> A `search.ismatch` és`search.ismatchscoring` a függvények csak a [Search API](https://docs.microsoft.com/rest/api/searchservice/search-documents)-ban lévő szűrőkben támogatottak. Az [ajánlott](https://docs.microsoft.com/rest/api/searchservice/suggestions) és az [automatikus kiegészítési](https://docs.microsoft.com/rest/api/searchservice/autocomplete) API-k nem támogatják őket.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([bővített Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) a `search.ismatch` és `search.ismatchscoring` a függvények nyelvtanát határozza meg:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> A teljes EBNF [Azure Search a OData kifejezés szintaxisát](search-query-odata-syntax-reference.md) ismertető témakörben talál.

### <a name="searchismatch"></a>Keresés. ismatch

A `search.ismatch` függvény egy teljes szöveges keresési lekérdezést értékel ki egy szűrési kifejezés részeként. A keresési lekérdezésnek megfelelő dokumentumokat a rendszer az eredményhalmazban adja vissza. A függvény következő túlterhelései érhetők el:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

A paraméterek a következő táblázatban vannak meghatározva:

| Paraméternév | Type | Leírás |
| --- | --- | --- |
| `search` | `Edm.String` | A keresési lekérdezés ( [egyszerű](query-simple-syntax.md) vagy [teljes](query-lucene-syntax.md) Lucene lekérdezési szintaxis). |
| `searchFields` | `Edm.String` | A kereshető mezők vesszővel tagolt listája a kereséshez; az alapértelmezett érték az index összes kereshető mezőjénél. Ha a [](query-lucene-syntax.md#bkmk_fields) `search` paraméterben a mezőn belüli keresés szerepel, a Lucene lekérdezés mezőiben szereplő mezők felülbírálják az ebben a paraméterben megadott mezőket. |
| `queryType` | `Edm.String` | `'simple'`vagy `'full'`; alapértelmezett érték `'simple'`:. Megadja, hogy melyik lekérdezési nyelvet használták `search` a paraméter. |
| `searchMode` | `Edm.String` | `'any'`vagy `'all'`alapértelmezett értéke `'any'`:. Azt jelzi, hogy a `search` paraméterben szereplő keresési kifejezések bármelyike vagy mindegyike egyezik-e a dokumentum egyezésként való megszámlálásához. Az [Lucene logikai operátorok](query-lucene-syntax.md#bkmk_boolean) használatakor a `search` paraméter elsőbbséget élvez a paraméterrel szemben. |

A fenti paraméterek a [keresési API megfelelő keresési kérelmi paramétereinek](https://docs.microsoft.com/rest/api/searchservice/search-documents)felelnek meg.

A `search.ismatch` függvény egy típusú `Edm.Boolean`értéket ad vissza, amely lehetővé teszi, hogy más szűrő alkifejezésekkel kombinálja a logikai [logikai operátorok](search-query-odata-logical-operators.md)használatával.

> [!NOTE]
> A Azure Search nem támogatja a `search.ismatch` `search.ismatchscoring` lambda kifejezéseket. Ez azt jelenti, hogy nem lehet szűrőket írni olyan objektumok gyűjteményei között, amelyek összekapcsolhatók a teljes szöveges keresési egyezésekkel az azonos objektumon lévő szigorú szűrési egyezésekkel. A korlátozással és a példákkal kapcsolatos további részletekért lásd: [a gyűjtemény szűrőinek hibaelhárítása Azure Search](search-query-troubleshoot-collection-filters.md). A korlátozás létezésével kapcsolatos további információkért lásd: a [gyűjtemény szűrőinek ismertetése a Azure Searchban](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>Keresés. ismatchscoring

A `search.ismatchscoring` függvény, például a `search.ismatch` függvény, visszaadja `true` azokat a dokumentumokat, amelyek megfelelnek a paraméterként átadott teljes szöveges keresési lekérdezésnek. Ezek közötti különbség az, hogy a `search.ismatchscoring` lekérdezésnek megfelelő dokumentumok relevanciás pontszáma hozzájárul a teljes dokumentum pontszámához, míg a `search.ismatch`esetében a dokumentum pontszáma nem változik. A függvény alábbi túlterhelései a következőhöz hasonló `search.ismatch`paraméterekkel érhetők el:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

A és `search.ismatch` `search.ismatchscoring` a függvények is ugyanabban a szűrő kifejezésben használhatók.

## <a name="examples"></a>Példák

Keresse meg a "Waterfront" szót tartalmazó dokumentumokat. Ez a szűrési lekérdezés megegyezik egy [keresési](https://docs.microsoft.com/rest/api/searchservice/search-documents) kérelemmel `search=waterfront`a következővel:.

    search.ismatchscoring('waterfront')

Keresse meg a "Hostel" szót tartalmazó dokumentumokat, vagy a 4-es vagy annál nagyobb minősítést, vagy a "Motel" szót tartalmazó dokumentumokat, valamint az 5 értékű minősítést. Megjegyzés: Ez a kérés nem fejezhető be a `search.ismatchscoring` függvény nélkül.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Dokumentumok keresése a "luxus" szó nélkül.

    not search.ismatch('luxury')

Megkeresheti az "Ocean View" kifejezéssel vagy 5 értékkel egyenlő minősítéssel rendelkező dokumentumokat. A `search.ismatchscoring` lekérdezés csak a mezőkre `HotelName` és a- `Rooms/Description`ra lesz végrehajtva.

Azok a dokumentumok, amelyek megfelelnek a kivonásnak, csak a második záradékkal lesznek visszaadva `Rating` . Annak egyértelművé tétele érdekében, hogy ezek a dokumentumok nem egyeznek a kifejezés egyik pontján sem, a pontszám értéke nulla lesz.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Megkeresheti azokat a dokumentumokat, amelyekben a "Hotel" és a "repülőtér" kifejezés a szálloda leírásában szereplő 5 szóból áll, és ahol a dohányzás nem engedélyezett legalább néhány szobában. Ez a lekérdezés a [teljes Lucene lekérdezési nyelvet](query-lucene-syntax.md)használja.

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>További lépések  

- [Szűrők a Azure Searchban](search-filters.md)
- [A OData kifejezés nyelvének áttekintése Azure Search](query-odata-filter-orderby-syntax.md)
- [Azure Search OData-kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok &#40;keresése Azure Search szolgáltatás REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
