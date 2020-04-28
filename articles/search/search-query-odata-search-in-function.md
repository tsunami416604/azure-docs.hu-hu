---
title: OData search.in függvény referenciája
titleSuffix: Azure Cognitive Search
description: Szintaxis és dokumentáció a search.in függvény Azure Cognitive Search lekérdezésekben való használatáról.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "74113116"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>OData `search.in` függvény az Azure Cognitive Search

A [OData szűrési kifejezések](query-odata-filter-orderby-syntax.md) egyik gyakori forgatókönyve, hogy az egyes dokumentumok egyetlen mezője egyenlő-e a lehetséges értékek valamelyikével. Például, hogy egyes alkalmazások hogyan valósítják meg a [biztonsági körülvágást](search-security-trimming-for-azure-search.md) – egy vagy több egyszerű azonosítót tartalmazó mező ellenőrzésével a lekérdezést kiállító felhasználót jelölő egyszerű azonosítók listájára. A (z) és [`eq`](search-query-odata-comparison-operators.md) [`or`](search-query-odata-logical-operators.md) az operátorok használata az alábbi módon írható le:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Ezt azonban a `search.in` függvény használatával rövidebb idő alatt írhatja:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Amellett, hogy rövidebb és könnyebben olvasható is `search.in` , a használata [teljesítménybeli előnyöket](#bkmk_performance) biztosít, és elkerüli a [szűrők bizonyos méretre vonatkozó korlátozásait](search-query-odata-filter.md#bkmk_limits) , ha több száz vagy akár több ezer érték szerepel a szűrőben. Ezért javasoljuk, hogy az egyenlőségi kifejezések `search.in` összetettebb leválasztása helyett használja a használatát.

> [!NOTE]
> A OData standard 4,01-es verziója nemrég bemutatta a [ `in` kezelőt](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), amely hasonló viselkedést tartalmaz `search.in` az Azure Cognitive Search függvényében. Az Azure Cognitive Search azonban nem támogatja ezt az operátort, ezért Ehelyett a `search.in` függvényt kell használnia.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([bővített Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) a `search.in` függvény nyelvtanát határozza meg:

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

A `search.in` függvény ellenőrzi, hogy egy adott sztring mező vagy tartomány változó egyenlő-e az értékek egy adott listájával. A változó és a listában szereplő értékek közötti egyenlőség a kis-és nagybetűket megkülönböztető módon határozza meg, ugyanúgy, mint a `eq` kezelő esetében. Ezért a hasonló `search.in(myfield, 'a, b, c')` kifejezés `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, amely `search.in` sokkal jobb teljesítményt eredményez.

A `search.in` függvénynek két túlterhelése van:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

A paraméterek a következő táblázatban vannak meghatározva:

| Paraméter neve | Típus | Leírás |
| --- | --- | --- |
| `variable` | `Edm.String` | Egy karakterlánc-mezőre mutató hivatkozás (vagy egy tartománybeli változó egy karakterlánc-gyűjtési `search.in` mezőn keresztül abban `any` az `all` esetben, ha az egy vagy kifejezésen belül használatos). |
| `valueList` | `Edm.String` | A `variable` paraméternek megfelelő értékek tagolt listáját tartalmazó karakterlánc. Ha a `delimiters` paraméter nincs megadva, az alapértelmezett határolójelek a szóköz és a vessző. |
| `delimiters` | `Edm.String` | Karakterlánc, amelyben minden karakter elválasztó van kezelve a `valueList` paraméter elemzésekor. Ennek a paraméternek az alapértelmezett értéke `' ,'` , ami azt jelenti, hogy a szóközökkel és/vagy vesszővel elválasztott értékek külön lesznek elkülönítve. Ha a szóközöktől és a vesszőtől eltérő elválasztókat kell használnia, mert az értékek magukban foglalják a karaktereket, alternatív határolójeleket is megadhat, például ebben `'|'` a paraméterben. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Teljesítmény`search.in`

Ha használja `search.in`, akkor várható, hogy a második paraméterben a másodpercben megadott idő több száz vagy ezer értékből álló listát tartalmaz. Az átadható elemek száma nincs kifejezett korláttal `search.in`, de a kérések maximális mérete továbbra is korlátozott. Azonban a késés növekedni fog, ahogy az értékek száma növekszik.

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

## <a name="next-steps"></a>További lépések  

- [Szűrők az Azure Cognitive Search](search-filters.md)
- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
