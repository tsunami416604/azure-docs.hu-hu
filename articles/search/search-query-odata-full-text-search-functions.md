---
title: OData teljes szöveges keresési funkció referencia – Azure Search
description: OData teljes szöveges keresési funkciókat, search.ismatch és search.ismatchscoring, az Azure Search-lekérdezéseket.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
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
ms.openlocfilehash: 158312a7afe88e7b9885376c5d28b01958acbbfb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079807"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>OData teljes szöveges keresési funkciók az Azure Search - `search.ismatch` és `search.ismatchscoring`

Az Azure Search környezetében támogatja a teljes szöveges keresés [OData szűrési kifejezésekben](query-odata-filter-orderby-syntax.md) keresztül a `search.ismatch` és `search.ismatchscoring` funkciók. Ezek a függvények engedélyezése, hogy a teljes szöveges keresés kombinálva szigorú logikai szűrést, amelyek használatával a legfelső szintű nem lehetséges módokon `search` paraméterében a [Search API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> A `search.ismatch` és `search.ismatchscoring` függvények csak a szűrők használata támogatott a [Search API](https://docs.microsoft.com/rest/api/searchservice/search-documents). Ezek nem támogatottak a [javaslat](https://docs.microsoft.com/rest/api/searchservice/suggestions) vagy [automatikus kiegészítés](https://docs.microsoft.com/rest/api/searchservice/autocomplete) API-k.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([kiterjesztett Backus-Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) határozza meg a nyelvtani, a `search.ismatch` és `search.ismatchscoring` funkciók:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Egy interaktív szintaxisdiagramja is érhető el:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisdiagramja](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Lásd: [OData kifejezés szintaxisának referenciája az Azure Search](search-query-odata-syntax-reference.md) a teljes EBNF számára.

### <a name="searchismatch"></a>Search.ismatch

A `search.ismatch` függvény kiértékeli a teljes szöveges keresési lekérdezést a szűrőkifejezés részeként. Az eredményhalmaz a dokumentumokat, a keresési lekérdezésnek megfelelő lesznek visszaadva. Ez a függvény a következő túlterhelésekkel érhetők el:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

Az alábbi táblázat a paraméterek vannak meghatározva:

| Paraméter neve | Típus | Leírás |
| --- | --- | --- |
| `search` | `Edm.String` | A keresési lekérdezés (akár [egyszerű](query-simple-syntax.md) vagy [teljes](query-lucene-syntax.md) Lucene lekérdezési szintaxis). |
| `searchFields` | `Edm.String` | A keresés; kereshető mezőket vesszővel elválasztott listája az alapértelmezett beállítás az összes kereshető mezőt az indexben. Használata esetén [keresési fielded](query-lucene-syntax.md#bkmk_fields) a a `search` paramétert, a Lucene-lekérdezésben a mező specifikátory felülbírálása ebben a paraméterben megadott mezőket. |
| `queryType` | `Edm.String` | `'simple'` vagy `'full'`; az alapértelmezett `'simple'`. Itt adhatja meg, milyen lekérdezési nyelv használták a `search` paraméter. |
| `searchMode` | `Edm.String` | `'any'` vagy `'all'`, alapértelmezés szerint a `'any'`. Azt jelzi, hogy a keresés egy részének vagy egészének a feltételek a `search` paraméterrel egyeztetni annak érdekében, hogy a dokumentum, amely száma. Használatakor a [Lucene logikai operátorok](query-lucene-syntax.md#bkmk_boolean) a a `search` paramétert, akkor elsőbbséget élveznek majd ezt a paramétert. |

A fenti paraméterek egyenértékűek a megfelelő [kérelem paramétereinek keresni a Search API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

A `search.ismatch` függvény típusú értéket ad vissza `Edm.Boolean`, amely lehetővé teszi, hogy az összeállítás, az egyéb szűrő alkifejezések használatával a logikai [logikai operátorokat](search-query-odata-logical-operators.md).

> [!NOTE]
> Az Azure Search nem támogatja a használatával `search.ismatch` vagy `search.ismatchscoring` lambda-kifejezésekkel belül. Ez azt jelenti, nem lehet írni a szűrők tartozó objektumok, amelyek kapcsolhatja össze a teljes szöveges keresés egyezése szigorú a szűrő megfelel ugyanazon az objektumon keresztül. Ez a korlátozás, valamint a példák a további részletekért lásd: [hibáinak elhárítása az Azure Search szolgáltatásban a fájlgyűjtési szűrők](search-query-troubleshoot-collection-filters.md). Részletesebb információt arról, hogy miért van erre a korlátozásra, lásd: [ismertetése az Azure Search szolgáltatásban a fájlgyűjtési szűrők](search-query-understand-collection-filters.md).


### <a name="searchismatchscoring"></a>Search.ismatchscoring

A `search.ismatchscoring` működni, mint a `search.ismatch` függvény visszaad `true` dokumentumok, a teljes szöveges keresési lekérdezésnek megfelelő paraméterként. Az egymás közötti különbség, hogy a relevancia alapján pontszámot rendelni az egyező dokumentumok a `search.ismatchscoring` lekérdezés hozzájárul a teljes dokumentum pontszám, a kis-és `search.ismatch`, a dokumentumok pontszámát nem módosítható. Ez a függvény a következő túlterhelésekkel érhetők el paraméterek azonosak `search.ismatch`:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Mind a `search.ismatch` és `search.ismatchscoring` funkciók is használható ugyanazon a szűrőkifejezést.

## <a name="examples"></a>Példák

Keresse meg az "i partszakasz" szót tartalmazó dokumentumokat. Ez a szűrő-lekérdezés megegyezik egy [keresési kérelmet](https://docs.microsoft.com/rest/api/searchservice/search-documents) a `search=waterfront`.

    search.ismatchscoring('waterfront')

Keresse meg a dokumentumot a word "hostel" és a nagyobb vagy egyenlő "amelyben" szót tartalmazó dokumentumok vagy 4 minősítés és minősítés megegyezik az 5. Vegye figyelembe, a kérelem nem fejezik nélkül a `search.ismatchscoring` függvény.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Keresse meg a dokumentumok szó nélkül "engedélyezhető".

    not search.ismatch('luxury')

Keresse meg a dokumentumok minősítési értéknek 5 vagy "óceán view" kifejezés helyett szerepel. A `search.ismatchscoring` lekérdezés hajtani, csak a mező alapján `HotelName` és `Rooms/Description`.

Dokumentumok, amely megfelel a vagy műveletet csak a második záradék visszaad túl – a "Hotels" `Rating` megegyezik az 5. Ahhoz, hogy ezeket a dokumentumokat bármely pontozott részei a kifejezés nem megfelelő, azok vissza kell adni a pontszám nullánál nem kisebb törlése.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Dokumentumok keresése, ahol a feltételek "hotel" és "repülőtér" 5 szó egymástól Szálloda leírásában belül, és ahol fogyasztási nem engedélyezett, legalább a termek néhányat. Ez a lekérdezés használ a [teljes Lucene lekérdezési nyelv](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>További lépések  

- [Szűrők az Azure Search szolgáltatásban](search-filters.md)
- [Az Azure Search OData kifejezés nyelvi – áttekintés](query-odata-filter-orderby-syntax.md)
- [Az Azure Search OData kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
