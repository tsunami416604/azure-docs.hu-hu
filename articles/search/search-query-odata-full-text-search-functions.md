---
title: OData teljes szöveges keresési függvényének hivatkozása
titleSuffix: Azure Cognitive Search
description: OData teljes szöveges keresési funkciók, search.ismatch és search.ismatchscoring, az Azure Cognitive Search lekérdezések.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: 06eb29f2f3245d3f4fd047fb86b2b57fb1f0989e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793351"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>Az OData teljes szöveges keresési funkciói az Azure Cognitive Search-ben – `search.ismatch` és`search.ismatchscoring`

Az Azure Cognitive Search támogatja a teljes szöveges keresés az `search.ismatch` [OData-szűrőkifejezések](query-odata-filter-orderby-syntax.md) környezetében a és `search.ismatchscoring` a függvények. Ezek a funkciók lehetővé teszik, hogy a teljes szöveges keresést szigorú logikai szűréssel kombinálja olyan módon, amely nem lehetséges csak a Search `search` [API](https://docs.microsoft.com/rest/api/searchservice/search-documents)legfelső szintű paraméterének használatával.

> [!NOTE]
> A `search.ismatch` `search.ismatchscoring` és a függvényeket csak a [Keresési API](https://docs.microsoft.com/rest/api/searchservice/search-documents)szűrői támogatják. Ezek nem támogatottak a [javaslat](https://docs.microsoft.com/rest/api/searchservice/suggestions) vagy [az automatikus kiegészítés](https://docs.microsoft.com/rest/api/searchservice/autocomplete) API-k.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form) `search.ismatch` ) `search.ismatchscoring` határozza meg a nyelvtant és a funkciókat:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Interaktív szintaktikai diagram is elérhető:

> [!div class="nextstepaction"]
> [OData-szintaktikai diagram az Azure Cognitive Search szolgáltatáshoz](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Lásd: [OData kifejezés szintaxis referencia az Azure Cognitive Search](search-query-odata-syntax-reference.md) a teljes EBNF.

### <a name="searchismatch"></a>search.ismatch

A `search.ismatch` függvény egy szűrőkifejezés részeként kiértékeli a teljes szöveges keresési lekérdezést. A keresési lekérdezésnek megfelelő dokumentumok az eredményhalmazban jelennek meg. Ennek a funkciónak a következő túlterhelései érhetők el:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

A paramétereket a következő táblázat határozza meg:

| Paraméter neve | Típus | Leírás |
| --- | --- | --- |
| `search` | `Edm.String` | A keresési lekérdezés [(egyszerű](query-simple-syntax.md) vagy [teljes](query-lucene-syntax.md) Lucene lekérdezés szintaxisában). |
| `searchFields` | `Edm.String` | A kereshető mezők vesszővel tagolt listája; alapértelmezés szerint az index összes kereshető mezőjére. Ha [mezőalapú keresést](query-lucene-syntax.md#bkmk_fields) `search` használ a paraméterben, a Lucene lekérdezésmező-megadója felülírja az ebben a paraméterben megadott mezőket. |
| `queryType` | `Edm.String` | `'simple'`vagy `'full'`az ; alapértelmezés szerint `'simple'`a . Itt adható meg, hogy `search` milyen lekérdezési nyelvet használt a paraméterben. |
| `searchMode` | `Edm.String` | `'any'`vagy `'all'`, alapértelmezés `'any'`szerint. Azt jelzi, hogy a `search` paraméterben szereplő keresési kifejezések bármelyikét vagy mindegyikét egyeztetni kell-e ahhoz, hogy a dokumentumot egyezésnek lehessen tekinteni. Ha a [Lucene logikai operátorok](query-lucene-syntax.md#bkmk_boolean) a `search` paraméter, akkor elsőbbséget élveznek ezzel a paraméterrel szemben. |

A fenti paraméterek egyenértékűek a keresési API megfelelő [keresési kérelemparamétereivel.](https://docs.microsoft.com/rest/api/searchservice/search-documents)

A `search.ismatch` függvény típusértéket `Edm.Boolean`ad vissza, amely lehetővé teszi, hogy más szűrőalkifejezésekkel a logikai logikai operátorok használatával összeállítsa. [logical operators](search-query-odata-logical-operators.md)

> [!NOTE]
> Az Azure Cognitive Search `search.ismatch` `search.ismatchscoring` nem támogatja a lambda kifejezések használatát vagy belsejében. Ez azt jelenti, hogy nem lehet szűrőket írni olyan objektumgyűjtemények felett, amelyek a teljes szöveges keresési egyezéseket összetudják egyeznek az ugyanazon az objektumon található szigorú szűrőegyezésekkel. Erről a korlátozásról, valamint példákról további információt az [Azure Cognitive Search Gyűjteményszűrők hibaelhárítása című témakörében talál.](search-query-troubleshoot-collection-filters.md) A korlátozás létezésének okáról a [Gyűjteményszűrők ismertetése az Azure Cognitive Search szolgáltatásban](search-query-understand-collection-filters.md)című témakörben olvashat részletesen.


### <a name="searchismatchscoring"></a>search.ismatchscoring

A `search.ismatchscoring` függvény, `search.ismatch` a függvényhez hasonlóan, olyan dokumentumokhoz ad vissza, `true` amelyek megfelelnek a paraméterként átadott teljes szöveges keresési lekérdezésnek. A különbség közöttük az, hogy a `search.ismatchscoring` lekérdezésnek megfelelő dokumentumok relevancia-pontszáma hozzájárul a dokumentum teljes pontszámához, míg a dokumentum pontszáma `search.ismatch`nem változik. Ennek a funkciónak a következő túlterhelései `search.ismatch`állnak rendelkezésre a következő paraméterekkel, amelyek megegyeznek a következőkkel:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

A `search.ismatch` függvények és `search.ismatchscoring` a függvények egyaránt használhatók ugyanabban a szűrőkifejezésben.

## <a name="examples"></a>Példák

Keresse meg a "vízparti" szóval ellátott dokumentumokat. Ez a szűrőlekérdezés megegyezik `search=waterfront`a [keresési kérelemmel.](https://docs.microsoft.com/rest/api/searchservice/search-documents)

    search.ismatchscoring('waterfront')

Keresse meg a "hostel" szót és a 4-es minősítéssel rendelkező dokumentumokat, vagy a "motel" szóval és az 5-ös minősítéssel rendelkező dokumentumokat. Ne feledje, hogy ez `search.ismatchscoring` a kérés nem fejezhető ki a funkció nélkül.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Dokumentumok keresése a "luxus" szó nélkül.

    not search.ismatch('luxury')

Keresse meg az "óceánnézet" vagy az 5-ös minősítésű dokumentumokat. A `search.ismatchscoring` lekérdezés csak mezőkön `HotelName` `Rooms/Description`hajtható végre, és a.

Azok a dokumentumok, amelyek csak a rendelkezés második záradékának feleltek meg, szintén visszakerülnek - a szállodák 5-tel egyenlők. `Rating` Annak egyértelművé nyilvánítása érdekében, hogy ezek a dokumentumok nem egyeztek a kifejezés egyik pontozott részével sem, a rendszer nullával megegyező pontszámmal adja vissza őket.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Keresse meg azokat a dokumentumokat, ahol a "szálloda" és a "repülőtér" kifejezések 5 szón belül vannak egymástól a szálloda leírásában, és ahol a dohányzás nem megengedett legalább néhány szobában. Ez a lekérdezés a [teljes Lucene lekérdezési nyelvet](query-lucene-syntax.md)használja.

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>További lépések  

- [Szűrők az Azure Cognitive Search szolgáltatásban](search-filters.md)
- [Az Azure Cognitive Search OData-kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezés szintaxisának hivatkozása](search-query-odata-syntax-reference.md)
- [Az Azure Cognitive Search REST API-&#41;&#40;dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
