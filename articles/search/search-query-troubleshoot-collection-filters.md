---
title: OData-gyűjtési szűrők hibaelhárítása
titleSuffix: Azure Cognitive Search
description: A OData-gyűjtési hibák elhárítása az Azure Cognitive Search lekérdezésekben.
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
ms.openlocfilehash: 0af2525a15618c6bfd9022b4388c547209ee957b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793184"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>OData-gyűjtési szűrők hibaelhárítása Az Azure-ban Cognitive Search

Az Azure Cognitive Search gyűjtemény mezőinek [szűréséhez](query-odata-filter-orderby-syntax.md) a [`any` és a `all` operátort](search-query-odata-collection-operators.md) **lambda kifejezésekkel**együtt használhatja. A lambda kifejezés a gyűjtemények egyes elemeire alkalmazott alszűrő.

A szűrési kifejezések nem minden funkciója érhető el lambda kifejezésen belül. A rendelkezésre álló szolgáltatások eltérőek lehetnek attól függően, hogy milyen adattípust szeretne szűrni a begyűjtési mező. Ez hibát okozhat, ha olyan lambda kifejezésben lévő szolgáltatást próbál használni, amely nem támogatott ebben a környezetben. Ha ilyen hibákba ütközik, miközben összetett szűrőt próbál meg gyűjteni a gyűjtemény mezőin, ez a cikk segítséget nyújt a probléma elhárításához.

## <a name="common-collection-filter-errors"></a>Gyakori gyűjtemény-szűrési hibák

A következő táblázat felsorolja azokat a hibákat, amelyek a gyűjteményi szűrő végrehajtásakor előfordulhatnak. Ezek a hibák akkor fordulnak elő, ha olyan szűrési kifejezéseket használ, amelyek nem támogatottak lambda kifejezésben. Az egyes hibákkal kapcsolatos útmutatást ad a szűrő újraírásához a hiba elkerüléséhez. A táblázat a jelen cikk vonatkozó szakaszának hivatkozását is tartalmazza, amely további információkat nyújt a hiba elkerüléséről.

| Hibaüzenet | Helyzet | További információkért lásd: |
| --- | --- | --- |
| A "ismatch" függvényhez nem tartozik paraméterek a (z) "" tartomány változóhoz. Csak a kötött mezőkre mutató hivatkozások támogatottak lambda kifejezésekben ("any" vagy "all"). Módosítsa a szűrőt úgy, hogy az "ismatch" függvény kívül legyen a lambda kifejezésen, és próbálkozzon újra. | `search.ismatch` vagy `search.ismatchscoring` használata lambda kifejezésen belül | [Az összetett gyűjtemények szűrésének szabályai](#bkmk_complex) |
| Érvénytelen lambda kifejezés. Megtalálta az egyenlőség vagy az egyenlőtlenség tesztelését, ha a rendszer egy olyan lambda kifejezésben várta az ellenkező értéket, amely egy gyűjtemény (EDM. String) típusú mező fölé kerül. Az "any" esetében használja az "x EQ y" vagy a "search.in (...)" formátumú kifejezéseket. Az "all" (összes) esetében használja az "x ne y", "not (x EQ y)" vagy "not search.in (...)" formátumú kifejezéseket. | Szűrés `Collection(Edm.String)` típusú mezőkön | [Szabályok a karakterlánc-gyűjtemények szűréséhez](#bkmk_strings) |
| Érvénytelen lambda kifejezés. Az összetett logikai kifejezés nem támogatott formáját találta. Az "any" esetében használjon "and" típusú kifejezéseket, más néven diszjunkt kötelezőségi normál alakot. Például: "(a és b), vagy (c és d)", ahol a, b, c és d összehasonlítási vagy egyenlőségi alkifejezések. Az "all" (összes) esetében használjon "and" típusú kifejezéseket, más néven Conjunctive normál formát. Például: "(a vagy b) és (c vagy d)", ahol a, b, c és d az összehasonlítási vagy egyenlőtlenségi alkifejezések. Példák az összehasonlító kifejezésekre: "x gt 5", "x le 2". Példa egy egyenlőségi kifejezésre: "x EQ 5". Példa az egyenlőtlenségi kifejezésre: "x ne 5". | `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`vagy `Collection(Edm.Int64)` típusú mezők szűrése | [Az összehasonlítható gyűjtemények szűrésének szabályai](#bkmk_comparables) |
| Érvénytelen lambda kifejezés. A Geo. Distance () vagy a Geo. metszetek () nem támogatott használatát észlelte egy olyan lambda kifejezésben, amely egy gyűjtemény (EDM. Geographypoint adattípuson) típusú mezőn keresztül ismétli meg. Az "any" esetében ügyeljen arra, hogy hasonlítsa össze a Geo. Distance () műveletet a "lt" vagy a "le" operátorral, és ügyeljen arra, hogy a Geo. metszetek () használata ne legyen megtagadva. Az "all" (összes) esetében ügyeljen arra, hogy összehasonlítsa a Geo. Distance () t a "gt" vagy a "GE" operátorral, és győződjön meg arról, hogy a Geo. metszetek () bármely használata meg lett tagadva. | Szűrés `Collection(Edm.GeographyPoint)` típusú mezőkön | [Geographypoint adattípuson-gyűjtemények szűrésének szabályai](#bkmk_geopoints) |
| Érvénytelen lambda kifejezés. Az összetett logikai kifejezések nem támogatottak olyan lambda-kifejezésekben, amelyek a következő típusú mezőket ismétlik meg: Collection (EDM. Geographypoint adattípuson). Az "any" esetében csatlakozzon az alkifejezésekhez a következővel: "vagy"; a "és a" nem támogatott. Az "all" (összes) esetében kérjük, csatlakozzon az alkifejezésekhez a következővel: "és"; a "vagy" nem támogatott. | Szűrés `Collection(Edm.String)` vagy `Collection(Edm.GeographyPoint)` típusú mezőknél | [Szabályok a karakterlánc-gyűjtemények szűréséhez](#bkmk_strings) <br/><br/> [Geographypoint adattípuson-gyűjtemények szűrésének szabályai](#bkmk_geopoints) |
| Érvénytelen lambda kifejezés. Összehasonlító operátor található (az egyik "lt", "le", "gt" vagy "GE"). Csak az esélyegyenlőségi operátorok engedélyezettek olyan lambda kifejezésekben, amelyek a types (EDM. String) mezőket használják. Az "any" esetében használjon "x EQ y" formátumú kifejezéseket. Az "all" (összes) esetében használja az "x ne y" vagy a "not (x EQ y)" formátumú kifejezéseket. | Szűrés `Collection(Edm.String)` típusú mezőkön | [Szabályok a karakterlánc-gyűjtemények szűréséhez](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Érvényes gyűjteményi szűrők írása

Az érvényes gyűjtési szűrők írásának szabályai különbözőek az egyes adattípusok esetében. A következő szakaszok ismertetik a szabályokat, ha példákat mutatnak arra, hogy mely szűrési funkciók támogatottak, és melyek nem:

- [Szabályok a karakterlánc-gyűjtemények szűréséhez](#bkmk_strings)
- [A logikai gyűjtemények szűrésének szabályai](#bkmk_bools)
- [Geographypoint adattípuson-gyűjtemények szűrésének szabályai](#bkmk_geopoints)
- [Az összehasonlítható gyűjtemények szűrésének szabályai](#bkmk_comparables)
- [Az összetett gyűjtemények szűrésének szabályai](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Szabályok a karakterlánc-gyűjtemények szűréséhez

A sztring-gyűjtemények esetében a lambda kifejezéseken belül az egyetlen használható összehasonlító operátor `eq` és `ne`.

> [!NOTE]
> Az Azure Cognitive Search nem támogatja a `lt`/`le`/`gt`/`ge` operátorok sztringeket, akár lambda kifejezésen belül, akár kívül is.

Egy `any` törzse csak akkor képes az egyenlőség tesztelésére, ha egy `all` törzse csak az egyenlőtlenségek tesztelésére használható.

Több kifejezést is össze lehet kapcsolni `or` használatával egy `any`törzsében, és egy `all`törzsének `and`ján keresztül. Mivel az `search.in` függvény egyenértékű az esélyegyenlőségi ellenőrzések `or`kel való kombinálásával, az `any`törzsében is engedélyezett. Ezzel szemben `not search.in` engedélyezett egy `all`törzsében.

Ezek a kifejezések engedélyezettek például:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

Ezek a kifejezések nem engedélyezettek:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>A logikai gyűjtemények szűrésének szabályai

A `Edm.Boolean` típus csak a `eq` és a `ne` operátort támogatja. Ennek megfelelően nem sok értelme van az olyan záradékok egyesítésének, amelyek ugyanazt a tartománybeli változót ellenőrizzék `and`/`or`, mivel ez mindig tautologies vagy ellentmondásokat eredményez.

Íme néhány példa a megengedett logikai gyűjtemények szűrésére:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

A karakterlánc-gyűjteményektől eltérően a logikai gyűjtemények nem korlátozzák, hogy melyik operátor használható a lambda kifejezésben. A `eq` és a `ne` is használható `any` vagy `all`törzsében.

A logikai gyűjtemények esetében a következő kifejezések nem engedélyezettek:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Geographypoint adattípuson-gyűjtemények szűrésének szabályai

Egy gyűjteményben lévő `Edm.GeographyPoint` típusú értékek nem hasonlíthatók össze közvetlenül egymással. Ehelyett paramétereket kell használni a `geo.distance` és a `geo.intersects` függvények számára. A `geo.distance` függvényt az összehasonlító operátorok egyikével kell összehasonlítani, `lt`, `le`, `gt`vagy `ge`használatával. Ezek a szabályok a nem gyűjteményes EDM. Geographypoint adattípuson mezőkre is érvényesek.

A karakterlánc-gyűjteményekhez hasonlóan a `Edm.GeographyPoint` gyűjtemények is bizonyos szabályokkal rendelkeznek, hogy a Geo-térbeli függvények hogyan használhatók és kombinálhatók a különböző típusú lambda-kifejezésekkel:

- A `geo.distance` függvénnyel együtt használható összehasonlító operátorok a lambda kifejezés típusától függenek. `any`csak `lt` vagy `le`használható. `all`csak `gt` vagy `ge`használható. Megtagadhatja a `geo.distance`t érintő kifejezéseket, de módosítania kell az összehasonlító operátort (`geo.distance(...) lt x` válik `not (geo.distance(...) ge x)` és `geo.distance(...) le x` válik `not (geo.distance(...) gt x)`).
- Egy `all`törzsében a `geo.intersects` függvényt meg kell tagadni. Ezzel szemben egy `any`törzsében a `geo.intersects` függvény nem vonható le.
- Egy `any`törzsében a Geo-térbeli kifejezések kombinálhatók `or`használatával. Egy `all`törzsében ilyen kifejezések kombinálhatók `and`használatával.

A fenti korlátozások hasonló okokból léteznek, mint a karakterlánc-gyűjtemények egyenlőségi/egyenlőtlenségi korlátozása. Tekintse meg a [OData-gyűjtési szűrők ismertetése az Azure-Cognitive Search](search-query-understand-collection-filters.md) részletesebben a következő szempontokat.

Íme néhány példa az engedélyezett `Edm.GeographyPoint`-gyűjtemények szűrésére:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

`Edm.GeographyPoint` gyűjtemények esetében a következő kifejezések nem engedélyezettek:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Az összehasonlítható gyűjtemények szűrésének szabályai

Ez a szakasz a következő adattípusokra vonatkozik:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

A `Edm.Int32` és a `Edm.DateTimeOffset` típusok mind a hat összehasonlító operátort támogatják: `eq`, `ne`, `lt`, `le`, `gt`és `ge`. Az ilyen típusú gyűjtemények lambda kifejezései tartalmazhatnak egyszerű kifejezéseket ezen operátorok bármelyikének használatával. Ez a `any`ra és a `all`is vonatkozik. Ezek a szűrők például engedélyezettek:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

Az ilyen összehasonlító kifejezések azonban korlátozásokkal kombinálhatók egy lambda kifejezésen belüli összetettebb kifejezésekkel:

- `any`vonatkozó szabályok:
  - Egyszerű egyenlőtlenségi kifejezések nem kombinálhatók más kifejezésekkel. Ez a kifejezés például engedélyezett:
    - `ratings/any(r: r ne 5)`

    Ez a kifejezés azonban nem:
    - `ratings/any(r: r ne 5 and r gt 2)`

    Ez a kifejezés azonban a megengedettnél nem hasznos, mert a feltételek átfedésben vannak:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - `eq`, `lt`, `le`, `gt`vagy `ge` összevonásával egyszerű összehasonlító kifejezések kombinálhatók `and`/`or`. Példa:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - A `and`ekkel összevont összehasonlító kifejezések (összekapcsolva) tovább kombinálhatók `or`használatával. Ez az űrlap a logikai logikában ismert "[diszjunkt kötelezőségi normál formában](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF). Példa:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- `all`vonatkozó szabályok:
  - Az egyszerű egyenlőségi kifejezések nem kombinálhatók más kifejezésekkel. Ez a kifejezés például engedélyezett:
    - `ratings/all(r: r eq 5)`

    Ez a kifejezés azonban nem:
    - `ratings/all(r: r eq 5 or r le 2)`

    Ez a kifejezés azonban a megengedettnél nem hasznos, mert a feltételek átfedésben vannak:
    - `ratings/all(r: r eq 5 and r le 7)`
  - `ne`, `lt`, `le`, `gt`vagy `ge` összevonásával egyszerű összehasonlító kifejezések kombinálhatók `and`/`or`. Példa:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - A `or` (leválasztással együtt) összevont összehasonlító kifejezések tovább kombinálhatók `and`használatával. Ez az űrlap a logikai logikában ismert "[Conjunctive normál formában](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (cnf). Példa:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Az összetett gyűjtemények szűrésének szabályai

Az összetett gyűjteményeknél a lambda kifejezések sokkal rugalmasabb szintaxist támogatnak, mint a lambda kifejezéseket a primitív típusú gyűjteményeknél. Egy olyan szűrő-összeállítást is használhat, amely egy olyan lambda kifejezésen belül használható, amelyet az egyiken kívül használhat, és csak két kivételt tartalmaz.

Először is a `search.ismatch` és `search.ismatchscoring` függvények nem támogatottak lambda-kifejezéseken belül. További információ: [a OData-gyűjtési szűrők ismertetése az Azure Cognitive Searchban](search-query-understand-collection-filters.md).

Másodszor, a tartomány változóhoz (ún. *ingyenes változók*) nem *kötött* mezőkre mutató hivatkozás nem engedélyezett. Vegyük például a következő két egyenértékű OData-szűrési kifejezést:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

Az első kifejezés engedélyezett lesz, míg a második űrlap el lesz utasítva, mert `details/margin` nem a tartomány változóhoz van kötve `s`.

Ez a szabály olyan kifejezésekre is kiterjed, amelyek egy külső hatókörben kötött változókkal rendelkeznek. Ezek a változók ingyenesen használhatók azon hatókör tekintetében, amelyben megjelennek. Például az első kifejezés engedélyezett, míg a második egyenértékű kifejezés nem engedélyezett, mert a `s/name` a tartomány változó hatókörére vonatkozóan ingyenes `a`:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Ez a korlátozás nem jelent problémát a gyakorlatban, mert mindig lehetséges szűrőket létrehozni, például a lambda kifejezések csak kötött változókat tartalmaznak.

## <a name="cheat-sheet-for-collection-filter-rules"></a>A gyűjtési szűrő szabályainak Cheat lapja

A következő táblázat összefoglalja az egyes gyűjteményi adattípusokhoz tartozó érvényes szűrők összeállításának szabályait.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Példák az érvényes szűrők összeállítására az egyes esetekben: az [érvényes gyűjteményi szűrők írása](#bkmk_examples).

Ha gyakran ír szűrőket, és megértette, hogy az első alapelvek szabályai jobban megjegyezik őket, tekintse meg a [OData-gyűjtési szűrők ismertetése az Azure Cognitive Search-ban](search-query-understand-collection-filters.md)című témakört.

## <a name="next-steps"></a>Következő lépések  

- [A OData-gyűjtési szűrők ismertetése az Azure Cognitive Search](search-query-understand-collection-filters.md)
- [Szűrők az Azure Cognitive Search](search-filters.md)
- [Az Azure Cognitive Search OData kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezési szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok &#40;keresése az Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
