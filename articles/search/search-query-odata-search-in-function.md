---
title: OData search.in függvény referenciája
titleSuffix: Azure Cognitive Search
description: OData search.in függvény az Azure Cognitive Search lekérdezésekben.
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
ms.openlocfilehash: f641e50554e720d273735fd20032e60444cb198a
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793280"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>OData `search.in` függvény az Azure-ban Cognitive Search

A [OData szűrési kifejezések](query-odata-filter-orderby-syntax.md) egyik gyakori forgatókönyve, hogy az egyes dokumentumok egyetlen mezője egyenlő-e a lehetséges értékek valamelyikével. Például, hogy egyes alkalmazások hogyan valósítják meg a [biztonsági körülvágást](search-security-trimming-for-azure-search.md) – egy vagy több egyszerű azonosítót tartalmazó mező ellenőrzésével a lekérdezést kiállító felhasználót jelölő egyszerű azonosítók listájára. Az alábbihoz hasonló lekérdezéseket az [`eq`](search-query-odata-comparison-operators.md) és [`or`](search-query-odata-logical-operators.md) operátorok használatával lehet írni:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Ezt azonban a `search.in` függvény használatával rövidebb idő alatt írhatja:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> A `search.in` használatával a rövidebb és a könnyebb olvashatóság érdekében is [teljesítménybeli előnyökkel jár](#bkmk_performance) , és elkerülheti a [szűrők bizonyos méretbeli korlátozásait](search-query-odata-filter.md#bkmk_limits) , ha több száz vagy akár több ezer érték szerepel a szűrőben. Ezért javasoljuk, hogy az egyenlőségi kifejezések összetettebb elvégzése helyett `search.in` használjon.

> [!NOTE]
> A OData standard 4,01-es verziója nemrég vezette be a [`in` operátort](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), amely hasonló viselkedéssel rendelkezik az Azure Cognitive Search `search.in` függvényében. Az Azure Cognitive Search azonban nem támogatja ezt az operátort, ezért Ehelyett a `search.in` függvényt kell használnia.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([bővített Naur-űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) a `search.in` függvény nyelvtanát határozza meg:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Az interaktív szintaxis diagram is elérhető:

> [!div class="nextstepaction"]
> [Az Azure Cognitive Search OData szintaxisának diagramja](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Tekintse meg az [Azure Cognitive Search OData-kifejezés szintaxisának referenciáját](search-query-odata-syntax-reference.md) a teljes EBNF.

A `search.in` függvény azt teszteli, hogy egy adott sztring mező vagy tartomány változó egyenlő-e az értékek egy adott listájával. A változó és a listában szereplő értékek közötti egyenlőség a kis-és nagybetűket megkülönböztető módon határozza meg, ugyanúgy, mint a `eq` operátor esetében. Ezért az olyan kifejezések, mint a `search.in(myfield, 'a, b, c')`, a `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`nak felelnek meg, azzal a különbséggel, hogy a `search.in` sokkal jobb teljesítményt eredményez.

A `search.in` függvénynek két túlterhelése van:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

A paraméterek a következő táblázatban vannak meghatározva:

| Paraméter neve | Type (Típus) | Leírás |
| --- | --- | --- |
| `variable` | `Edm.String` | Egy karakterlánc-mező hivatkozása (vagy a karakterlánc-gyűjtési mezőn keresztüli tartomány változója abban az esetben, ha a `search.in` egy `any` vagy `all` kifejezésen belül van használatban). |
| `valueList` | `Edm.String` | Az `variable` paraméternek megfelelő értékek tagolt listáját tartalmazó karakterlánc. Ha a `delimiters` paraméter nincs megadva, az alapértelmezett határolójelek a szóköz és a vessző. |
| `delimiters` | `Edm.String` | Karakterlánc, amelyben minden karakter elválasztó van kezelve a `valueList` paraméter elemzésekor. Ennek a paraméternek az alapértelmezett értéke `' ,'`, ami azt jelenti, hogy a közöttük található szóközökkel és/vagy vesszővel elválasztott értékek el lesznek választva egymástól. Ha a szóközöktől és a vesszőtől eltérő elválasztókat kell használnia, mert az értékek tartalmazzák ezeket a karaktereket, alternatív határolójeleket is megadhat, például `'|'` ebben a paraméterben. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>`search.in` teljesítménye

Ha `search.in`használ, akkor várható, hogy a második paraméter több száz vagy ezer értékből álló listát tartalmaz. A `search.in`számára átadható elemek számának nincs kifejezett korlátja, de a kérések maximális mérete továbbra is korlátozott. Azonban a késés növekedni fog, ahogy az értékek száma növekszik.

## <a name="examples"></a>Példák

Az összes olyan Hotel megkeresése, amelynek neve egyenlő a "Sea View Motel" vagy a "Budget Hotel" névvel. A kifejezések szóközöket tartalmaznak, amelyek alapértelmezett határolójelek. Az idézőjelek között egy alternatív határolójelet is megadhat a harmadik sztring paraméterként:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Az összes olyan Hotel megkeresése, amelynek neve egyenlő a "Sea View Motel" vagy a "Budget Hotel" névvel, a következővel elválasztva: "|".

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

A "WiFi" vagy a "kád" címkével rendelkező összes Hotel megkeresése:

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Megtalálhatja a gyűjteményen belüli kifejezéseket, például a "fűtött törölköző állványok" vagy a "hajszárító" címkét.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

A "Motel" vagy a "cabin'" címke nélküli összes Hotel megkeresése:

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Következő lépések  

- [Szűrők az Azure Cognitive Search](search-filters.md)
- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok &#40;keresése az Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
