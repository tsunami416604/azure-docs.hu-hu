---
title: OData javításával függvényhivatkozás – Azure Search
description: OData-javításával függvény az Azure Search-lekérdezéseket.
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
ms.openlocfilehash: f72a59aac448796cf15220e15a3c8a4f12803bb5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079729"
---
# <a name="odata-searchin-function-in-azure-search"></a>OData `search.in` függvényt az Azure Search szolgáltatásban

Gyakran [OData szűrési kifejezésekben](query-odata-filter-orderby-syntax.md) , hogy ellenőrizze, hogy az egyes dokumentumok egyetlen mező számos lehetséges értékeket egyenlő. Például azt, hogyan egyes alkalmazások megvalósításához [biztonsági elrejtés](search-security-trimming-for-azure-search.md) – szemben a lekérdezés kiadó felhasználó jelölő egyszerű azonosítók listáját egy vagy több egyszerű azonosítókat tartalmazó ellenőrzésével. Az írási egy ilyen lekérdezés használata egyik módja a [ `eq` ](search-query-odata-comparison-operators.md) és [ `or` ](search-query-odata-logical-operators.md) kezelők:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Van azonban egy rövidebb módja a használatával, ezt a `search.in` függvény:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Rövidebb, és könnyebben olvasható, használatával amellett `search.in` is biztosít [teljesítménybeli előnyök](#bkmk_performance) és elkerülhető az egyes [szűrők korlátozásai méretezés](search-query-odata-filter.md#bkmk_limits) Ha több száz vagy akár több ezer értékek a szűrő tartalmazza. Ebből kifolyólag javasoljuk, hogy használatával `search.in` helyett egy összetettebb vagy műveletet egyenlőség kifejezésekre.

> [!NOTE]
> OData-szabványt 4.01 verziója nemrég bevezetett rendelkezik a [ `in` operátor](http://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), hasonló viselkedést, amelynek a `search.in` függvényt az Azure Search szolgáltatásban. Azonban az Azure Search nem támogatja ez az operátor így kell használni a `search.in` inkább működik.

## <a name="syntax"></a>Szintaxis

A következő EBNF ([kiterjesztett Backus-Naur űrlap](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) határozza meg a nyelvtani, a `search.in` függvény:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Egy interaktív szintaxisdiagramja is érhető el:

> [!div class="nextstepaction"]
> [Azure Search OData szintaxisdiagramja](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Lásd: [OData kifejezés szintaxisának referenciája az Azure Search](search-query-odata-syntax-reference.md) a teljes EBNF számára.

A `search.in` függvény azt teszteli, hogy a megadott karakterlánc típusú vagy a tartomány változót megegyezik egy adott értékek listáját. A változót és a lista minden egyes érték közötti egyenlőségi határozza meg a kis-és nagybetűket módon, ugyanúgy mint a `eq` operátor. Ezért, például egy kifejezést `search.in(myfield, 'a, b, c')` egyenértékű `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, azzal a különbséggel, hogy `search.in` sokkal jobb teljesítményt eredményez.

Két túlterhelésekkel a `search.in` függvény:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Az alábbi táblázat a paraméterek vannak meghatározva:

| Paraméter neve | Típus | Leírás |
| --- | --- | --- |
| `variable` | `Edm.String` | A karakterlánc mezőhivatkozásnak (vagy egy karakterlánc-gyűjtemény mezők abban az esetben tartomány változó ahol `search.in` használnak egy `any` vagy `all` kifejezés). |
| `valueList` | `Edm.String` | Az egyeztetéshez értékek tagolt listáját tartalmazó karakterlánc a `variable` paraméter. Ha a `delimiters` paraméter nincs megadva, az alapértelmezett elválasztó karakterek lemezterület és a vessző. |
| `delimiters` | `Edm.String` | Egy karakterláncot, ahol minden egyes karakter számít az elválasztó elemzésekor a `valueList` paraméter. Az alapértelmezett érték a paraméter `' ,'` ami azt jelenti, hogy minden olyan értéket, szóközöket és/vagy elválasztva választható el. Ha elválasztók szóközöket és vesszőket nem használható, mert a tartalmazzák-e ezeket a karaktereket kell, megadhat alternatív elválasztó karakterek például `'|'` ebben a paraméterben. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Teljesítményét `search.in`

Ha `search.in`, várható másodperc törtrésze válaszideje, amikor a második paraméter több száz vagy ezer értékek listáját tartalmazza. Nem lehet átadni elemek száma explicit korlátozott `search.in`, bár továbbra is korlátozza a kérelem maximális méretét. Azonban a késési értékek számának növekedésével növekszik.

## <a name="examples"></a>Példák

Keresse meg az összes "Hotels" nevű "Tenger nézet amelyben" vagy "Költségvetés hotel" egyenlő. Kifejezések tartalmazhatnak szóközt, amelynek egy alapértelmezett elválasztó. A harmadik paramétereként karakterlánc egy másik elválasztó szimpla idézőjelben adhatja meg:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Keresse meg az összes "Hotels" nevű egyenlő "Tenger nézet amelyben" vagy a "Budget hotel" elválasztva ' |} "):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Keresse meg a címke "Wi-Fi" vagy "vödör" kiszolgálótermek az összes hotels:

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Címkék-gyűjteményekben, például "fűtött törülköző állványt" vagy "hajszárító tartalmaz" kifejezések egyezés található.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Keresse meg az összes "Hotels" címke "amelyben" vagy "kézi" nélkül:

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>További lépések  

- [Szűrők az Azure Search szolgáltatásban](search-filters.md)
- [Az Azure Search OData kifejezés nyelvi – áttekintés](query-odata-filter-orderby-syntax.md)
- [Az Azure Search OData kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
