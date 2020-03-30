---
title: OData search.in függvény hivatkozási
titleSuffix: Azure Cognitive Search
description: Szintaxis és referencia dokumentáció az Azure Cognitive Search search.in függvényének használatára.
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
ms.openlocfilehash: b43c46599cbacaf40bc9583e364d088fa27a3ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113116"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>OData-függvény `search.in` az Azure Cognitive Search szolgáltatásban

[Az OData-szűrőkifejezésekben](query-odata-filter-orderby-syntax.md) gyakori eset annak ellenőrzése, hogy minden dokumentumegyetlen mezője megegyezik-e a lehetséges értékek egyikével. Egyes alkalmazások például így valósítják meg a [biztonsági tisztítást](search-security-trimming-for-azure-search.md) – egy vagy több egyszerű azonosítót tartalmazó mezőt ellenőrizve a lekérdezést kiállító felhasználót képviselő egyszerű azonosítók listájával. Az egyik módja annak, hogy írjon egy lekérdezést, mint ez, hogy használja a [`eq`](search-query-odata-comparison-operators.md) és [`or`](search-query-odata-logical-operators.md) az operátorok:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Van azonban egy rövidebb módja annak, `search.in` hogy ezt írja, a funkció használatával:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Amellett, hogy rövidebb és `search.in` könnyebben olvasható, használata is [nyújt teljesítmény előnyöket,](#bkmk_performance) és elkerüli bizonyos [méret korlátozások szűrők,](search-query-odata-filter.md#bkmk_limits) ha több száz vagy akár több ezer értéket kell tartalmaznia a szűrőt. Ezért erősen javasoljuk az `search.in` egyenlőségi kifejezések összetettebb eltiltása helyett.

> [!NOTE]
> Az OData-szabvány 4.01-es verziója a közelmúltban vezette be `search.in` az [ `in` operátort,](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230)amely hasonló viselkedéssel rendelkezik, mint az Azure Cognitive Search függvénye. Azonban az Azure Cognitive Search nem támogatja ezt `search.in` az operátort, ezért a függvényt kell használnia helyette.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([Extended Backus-Naur Form)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)határozza meg a `search.in` függvény nyelvtanát:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Interaktív szintaktikai diagram is elérhető:

> [!div class="nextstepaction"]
> [OData-szintaktikai diagram az Azure Cognitive Search szolgáltatáshoz](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Lásd: [OData kifejezés szintaxis referencia az Azure Cognitive Search](search-query-odata-syntax-reference.md) a teljes EBNF.

A `search.in` függvény azt vizsgálja, hogy egy adott karakterláncmező vagy tartományváltozó megegyezik-e egy adott értéklistával. A változó és a lista egyes értékei közötti egyenlőséget a kis- és `eq` nagybetűk megkülönböztetésével határozzák meg, ugyanúgy, mint az operátor esetében. Ezért egy `search.in(myfield, 'a, b, c')` kifejezés, `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`mint egyenértékű `search.in` a , kivéve, hogy fog sokkal jobb teljesítményt.

A `search.in` függvénynek két túlterhelése van:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

A paramétereket a következő táblázat határozza meg:

| Paraméter neve | Típus | Leírás |
| --- | --- | --- |
| `variable` | `Edm.String` | Karakterlánc-mezőhivatkozás (vagy egy karakterlánc-gyűjtemény mező feletti `search.in` tartományváltozó abban `any` `all` az esetben, ha egy vagy kifejezésen belül használatos). |
| `valueList` | `Edm.String` | A `variable` paraméterrel egyeztetve egyeztetett értékek tagolt listáját tartalmazó karakterlánc. Ha `delimiters` a paraméter nincs megadva, az alapértelmezett határolók a szóköz és a vessző. |
| `delimiters` | `Edm.String` | Olyan karakterlánc, amelyben minden karakter tévalválasztóként lesz `valueList` kezelve a paraméter elemzésekor. Ennek a paraméternek `' ,'` az alapértelmezett értéke azt jelenti, hogy a szóközökkel és/vagy vesszőkkel rendelkező értékek ellesznek választva. Ha szóközökön és vesszőkön kívül más elválasztót is kell használnia, mert az `'|'` értékek tartalmazzák ezeket a karaktereket, alternatív határolójeleket adhat meg, például ebben a paraméterben. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Teljesítménye`search.in`

Ha a `search.in`lehetőséget használja, akkor számíthat a másodperc alatti válaszidőre, ha a második paraméter több száz vagy több ezer értéket tartalmazó listát tartalmaz. Nincs kifejezett korlát az elemek számát lehet `search.in`átadni, bár még mindig korlátozza a maximális kérelem mérete. A késés azonban az értékek számának növekedésével nő.

## <a name="examples"></a>Példák

Keresse meg az összes olyan hotelt, amelynek neve megegyezik a "Sea View motel" vagy a "Budget hotel" névvel. A kifejezések szóközöket tartalmaznak, ami egy alapértelmezett határolójel. Harmadik karakterlánc-paraméterként egy idézőjelben alternatív határolószert adhat meg:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Keresse meg az összes olyan szállodát, amelynek neve megegyezik a "Sea View motel" vagy a "Budget hotel" névvel, a következővel elválasztva: '|'):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Keresse meg az összes szálloda szoba, amely a tag "wifi" vagy "kád":

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Keressen egyezést a gyűjteményen belüli kifejezéseken, például a "fűtött törölközőtartók" vagy a "hajszárító szerepel" címkékben.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Keresse meg az összes szálloda nélkül a tag "motel" vagy "kabin":

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>További lépések  

- [Szűrők az Azure Cognitive Search szolgáltatásban](search-filters.md)
- [Az Azure Cognitive Search OData-kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezés szintaxisának hivatkozása](search-query-odata-syntax-reference.md)
- [Az Azure Cognitive Search REST API-&#41;&#40;dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
