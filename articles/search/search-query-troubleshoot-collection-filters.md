---
title: OData-gyűjteményszűrők hibaelhárítása
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search-lekérdezésekben az OData-adatgyűjtési szűrőhibák megoldásának megközelítései.
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
ms.openlocfilehash: e82fa00226c964d5ba774cdf06f5b0f3898bdc55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113078"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>OData-gyűjteményszűrők hibaelhárítása az Azure Cognitive Search szolgáltatásban

Az Azure Cognitive Search gyűjteménymezőinek [szűréséhez](query-odata-filter-orderby-syntax.md) használhatja a [ `any` és `all` ](search-query-odata-collection-operators.md) az operátorokat **lambda kifejezésekkel**együtt. A lambda kifejezés egy alszűrő, amely a gyűjtemény minden elemére vonatkozik.

A szűrőkifejezések nem minden tulajdonsága érhető el a lambda kifejezésben. A rendelkezésre álló szolgáltatások a szűrni kívánt gyűjteménymező adattípusától függően eltérőek lehetnek. Ez hibát okozhat, ha olyan szolgáltatást próbál használni egy lambda kifejezésben, amely ebben a környezetben nem támogatott. Ha ilyen hibákat tapasztal, miközben összetett szűrőt próbál írni a gyűjtőmezőkre, ez a cikk segít a probléma elhárításában.

## <a name="common-collection-filter-errors"></a>Gyakori gyűjteményszűrő-hibák

Az alábbi táblázat azokat a hibákat sorolja fel, amelyekkel a gyűjteményszűrő végrehajtása során találkozhat. Ezek a hibák akkor fordulnak elő, ha olyan szűrőkifejezéseket használ, amely nem támogatott a lambda kifejezésben. Minden hiba ad némi útmutatást, hogyan lehet átírni a szűrőt, hogy elkerüljék a hibát. A táblázat a cikk megfelelő szakaszára mutató hivatkozást is tartalmaz, amely további információt nyújt a hiba elkerüléséről.

| Hibaüzenet | Helyzet | További információkért lásd: |
| --- | --- | --- |
| Az "ismatch" függvénynek nincsenek paraméterei az "s" tartományváltozóhoz kötve. Csak a kötött mezőhivatkozások támogatottak a lambda kifejezésekben ("bármely" vagy "all" kifejezések). Módosítsa a szűrőt úgy, hogy az "ismatch" függvény kívül legyen a lambda kifejezésen, majd próbálkozzon újra. | Lambda-kifejezés használata `search.ismatch` vagy `search.ismatchscoring` belsejében | [Az összetett gyűjtemények szűrésére vonatkozó szabályok](#bkmk_complex) |
| Érvénytelen lambda kifejezés. Talált egy egyenlőségi vagy egyenlőtlenségi tesztet, ahol az ellenkezőjét várták egy lambda kifejezésben, amely a Collection (Edm.String) típusú mezőre felé jegel. Az "any" kifejezésnél kérjük, hogy az "x eq y" vagy "search.in(...)" kifejezéseket használja. Az "összes" kifejezésnél kérjük, hogy az "x ne y", "not (x eq y)" vagy "not search.in(...)" kifejezéseket használja. | Szűrés egy típusú mezőn`Collection(Edm.String)` | [Karakterlánc-gyűjtemények szűrésének szabályai](#bkmk_strings) |
| Érvénytelen lambda kifejezés. Összetett logikai kifejezés nem támogatott formája. Az "any" esetében kérjük, használjon olyan kifejezéseket, amelyek "AND-k legkülső vizsgálatai", más néven disjunctive normal form. Például: "(a és b) vagy (c és d)" ha az a, b, c és d összehasonlítási vagy egyenlőségi alkifejezések. Az "összes" kifejezésnél kérjük, használjon "a legkülső régiók AND-jeit", más néven kötőhártya-féle normál formát. Például: "(a vagy b) és (c vagy d)" ha az a, b, c és d összehasonlítási vagy egyenlőtlenségi részkifejezések. Példák az összehasonlítási kifejezésekre: "x gt 5", "x le 2". Példa egyenlőségi kifejezésre: "x eq 5". Példa egy egyenlőtlenségi kifejezésre: "x ne 5". | Szűrés a `Collection(Edm.DateTimeOffset)`, , `Collection(Edm.Double)` `Collection(Edm.Int32)`, vagy`Collection(Edm.Int64)` | [Az összehasonlítható gyűjtemények szűrésére vonatkozó szabályok](#bkmk_comparables) |
| Érvénytelen lambda kifejezés. Talált egy nem támogatott geo.distance() vagy geo.intersects() nem támogatott használatát egy lambda kifejezésben, amely gyűjtemény(Edm.GeographyPoint) típusú mezőn keresztül iterálja. Az "any" esetében győződjön meg arról, hogy az "lt" vagy "le" operátorokkal összehasonlítja a geo.distance() operátorokat, és győződjön meg arról, hogy a geo.intersects() használata nem tagadható. Az "all" esetében győződjön meg arról, hogy a geo.distance() a "gt" vagy "ge" operátorokkal hasonlítja össze, és győződjön meg arról, hogy a geo.intersects() minden használata nincs megcáfolva. | Szűrés egy típusú mezőn`Collection(Edm.GeographyPoint)` | [A GeographyPoint-gyűjtemények szűrésének szabályai](#bkmk_geopoints) |
| Érvénytelen lambda kifejezés. A Gyűjtemény (Edm.GeographyPoint) típusú mezőkön iterált lambda kifejezések nem támogatják az összetett logikai kifejezéseket. Az "any" esetében kérjük, csatlakozzon az alkifejezésekhez a "vagy" alkifejezéssel; "és" nem támogatott. Az "all" kifejezésnél kérjük, csatlakozzon az "és" alkifejezésekhez; "vagy" nem támogatott. | Szűrés a vagy `Collection(Edm.String)` azonos típusú mezőkön`Collection(Edm.GeographyPoint)` | [Karakterlánc-gyűjtemények szűrésének szabályai](#bkmk_strings) <br/><br/> [A GeographyPoint-gyűjtemények szűrésének szabályai](#bkmk_geopoints) |
| Érvénytelen lambda kifejezés. Talált egy összehasonlító operátort (az egyik "lt", "le", "gt", vagy "ge"). Csak egyenlőségoperátorok engedélyezettek lambda kifejezésekben, amelyek a Collection (Edm.String) típusú mezőkön keresztül iterálnak. Az "any" kifejezésnél kérjük, használja az "x eq y" nyomtatvány kifejezéseit. Az "all" kifejezésnél kérjük, hogy az "x ne y" vagy "not (x eq y)" kifejezéseket használja. | Szűrés egy típusú mezőn`Collection(Edm.String)` | [Karakterlánc-gyűjtemények szűrésének szabályai](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Érvényes gyűjteményszűrők írása

Az érvényes gyűjteményszűrők írására vonatkozó szabályok minden adattípusesetében eltérőek. A következő szakaszok a szabályokat ismertetik, és példákat mutatnak be arra, hogy mely szűrőfunkciók támogatottak, és melyek nem:

- [Karakterlánc-gyűjtemények szűrésének szabályai](#bkmk_strings)
- [Logikai gyűjtemények szűrésének szabályai](#bkmk_bools)
- [A GeographyPoint-gyűjtemények szűrésének szabályai](#bkmk_geopoints)
- [Az összehasonlítható gyűjtemények szűrésére vonatkozó szabályok](#bkmk_comparables)
- [Az összetett gyűjtemények szűrésére vonatkozó szabályok](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Karakterlánc-gyűjtemények szűrésének szabályai

A karakterlánc-gyűjtemények lambda kifejezésein belül csak a `eq` és `ne`a .

> [!NOTE]
> Az Azure Cognitive Search `lt` / `le` / `gt` / `ge` nem támogatja a karakterláncok operátorait, akár egy lambda kifejezésen belül, akár azon kívül.

A test `any` egy csak teszt az egyenlőség, `all` míg a szervezet egy csak teszt egyenlőtlenség.

Az is lehetséges, hogy összekapcsolják `or` több kifejezések `any`keresztül a `and` test egy `all`, és keresztül a szervezetben egy . Mivel `search.in` a funkció egyenértékű az egyenlőségi ellenőrzések kombinálásával, `or`a törzsében is `any`megengedett. Ezzel szemben `not search.in` megengedett a testegy `all`.

Például ezek a kifejezések engedélyezettek:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

bár ezek a kifejezések nem engedélyezettek:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Logikai gyűjtemények szűrésének szabályai

A `Edm.Boolean` típus csak `eq` a `ne` és az operátorokat támogatja. Mint ilyen, nincs sok értelme, hogy az ilyen záradékok kombinálása, amelyek ellenőrzik az azonos tartományváltozót, `and` / `or` mivel ez mindig tautologies vagy ellentmondásokhoz vezetne.

Íme néhány példa a logikai gyűjtemények engedélyezett szűrőire:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

A karakterlánc-gyűjteményekkel ellentétben a logikai gyűjtemények nem korlátoznak, hogy melyik operátor használható a lambda kifejezés típusában. `eq` Mindkettő, `ne` és használható a `any` szervezetben vagy `all`.

A logikai gyűjtemények nem engedélyezettek az alábbihoz hasonló kifejezések:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>A GeographyPoint-gyűjtemények szűrésének szabályai

A gyűjtemény `Edm.GeographyPoint` típusának értékei nem hasonlíthatók össze közvetlenül egymással. Ehelyett ezeket kell használni, mint `geo.distance` `geo.intersects` a paramétereket a és a függvények. A `geo.distance` funkciót viszont össze kell hasonlítani egy távolságértékkel `gt`az `ge`összehasonlítási operátorok `lt`, `le`, , vagy . Ezek a szabályok a nem gyűjtemény Edm.GeographyPoint mezőkre is vonatkoznak.

A karakterlánc-gyűjteményekhez hasonlóan a gyűjtemények is rendelkeznek néhány szabálysal arra vonatkozóan, `Edm.GeographyPoint` hogy a földrajzi térinformatikai függvények hogyan használhatók és kombinálhatók a különböző lambda kifejezésekben:

- A függvényhasználatával használható összehasonlító `geo.distance` operátorok a lambda kifejezés típusától függnek. A `any`esetén csak `lt` a `le`vagy a használható. A `all`esetén csak `gt` a `ge`vagy a használható. A `geo.distance`bekövetkező kifejezéseket tagadhatja, de meg kell változtatnia az összehasonlító operátort (`geo.distance(...) lt x` válik `not (geo.distance(...) ge x)` és `geo.distance(...) le x` válik `not (geo.distance(...) gt x)`).
- A testben `all`a `geo.intersects` funkciót ki kell zárni. Ezzel szemben a testben `any`a `geo.intersects` funkció nem tagadható.
- A törzsben `any`a térbeli kifejezések kombinálhatók `or`a használatával. A törzsben `all`az ilyen kifejezések kombinálhatók `and`a használatával.

A fenti korlátozások hasonló okokból léteznek, mint a karakterlánc-gyűjtemények egyenlőség/egyenlőtlenség korlátozása. [Lásd: Az Azure Cognitive Search odata-gyűjteményszűrőinek ismertetése](search-query-understand-collection-filters.md) című témakörben mélyebben megtekintheti ezeket az okokat.

Íme néhány példa az `Edm.GeographyPoint` engedélyezett gyűjtemények szűrőire:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

A `Edm.GeographyPoint` gyűjtemények nem engedélyezettek az alábbihoz hasonló kifejezések:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Az összehasonlítható gyűjtemények szűrésére vonatkozó szabályok

Ez a szakasz az alábbi adattípusok mindegyikére vonatkozik:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

Az összehasonlítási operátorok közül mind a `eq` `ne`hathoz `gt`hasonló `ge` `Edm.Int32` és `Edm.DateTimeOffset` támogató típusok: , `lt`, `le`, , és . Lambda kifejezések gyűjtemények az ilyen típusú tartalmazhat nak egyszerű kifejezéseket használ ezek a operátorok. Ez egyaránt `any` `all`vonatkozik ara, és a. Ezek a szűrők például engedélyezettek:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

A lambda kifejezésen belül azonban vannak korlátozások arra vonatkozóan, hogy az ilyen összehasonlító kifejezések hogyan kombinálhatók összetettebb kifejezésekké:

- A `any`következők re vonatkozó szabályok:
  - Az egyszerű egyenlőtlenségi kifejezések nem kombinálhatók más kifejezésekkel. Ez a kifejezés például a következő:
    - `ratings/any(r: r ne 5)`

    de ez a kifejezés nem:
    - `ratings/any(r: r ne 5 and r gt 2)`

    és bár ez a kifejezés engedélyezett, nem hasznos, mert a feltételek átfedésben vannak:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Egyszerű összehasonlító kifejezések `eq` `lt`, `le` `gt`, `ge` , , `and` / `or`, vagy kombinálhatók a . Példa:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - Az összehasonlító kifejezések `and` és a (kötőszavak) `or`kombinálhatók a használatával. Ezt az űrlapot a logikai logika "[Disjunctive Normal Form](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF) néven ismeri. Példa:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- A `all`következők re vonatkozó szabályok:
  - Az egyszerű egyenlőségi kifejezések nem kombinálhatók más kifejezésekkel. Ez a kifejezés például a következő:
    - `ratings/all(r: r eq 5)`

    de ez a kifejezés nem:
    - `ratings/all(r: r eq 5 or r le 2)`

    és bár ez a kifejezés engedélyezett, nem hasznos, mert a feltételek átfedésben vannak:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Egyszerű összehasonlító kifejezések `ne` `lt`, `le` `gt`, `ge` , , `and` / `or`, vagy kombinálhatók a . Példa:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Az összehasonlító kifejezések `or` és a (letiltások) `and`kombinálhatók a használatával. Ezt az űrlapot a logikai logika "[Kötőhártya normál alak](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (CNF) néven ismeri. Példa:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Az összetett gyűjtemények szűrésére vonatkozó szabályok

Az összetett gyűjtemények lambda kifejezései sokkal rugalmasabb szintaxist támogatnak, mint a lambda kifejezések a primitív típusok gyűjteményei felett. Egy ilyen lambda kifejezésen belül bármilyen szűrőszerkezetet használhat, amelyet egy kivétellel használhat az egyiken kívül.

Először is, a függvények, `search.ismatch` és `search.ismatchscoring` nem támogatott belül lambda kifejezéseket. További információt az [Azure Cognitive Search OData-adatgyűjtési szűrőinek ismertetése című témakörben talál.](search-query-understand-collection-filters.md)

Másodszor, a *tartományváltozóhoz* nem kötött mezők (úgynevezett *szabad változók)* hivatkozása nem engedélyezett. Vegyük például a következő két egyenértékű OData szűrőkifejezést:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

Az első kifejezés engedélyezett lesz, míg a második `details/margin` űrlap elutasításra kerül, `s`mert nincs kötve a tartományváltozóhoz .

Ez a szabály olyan kifejezésekre is kiterjed, amelyek hez változók vannak kötve egy külső hatókörhöz. Az ilyen változók a megjelenésük terjedelmét illetően ingyenesek. Például az első kifejezés engedélyezett, míg a második egyenértékű `s/name` kifejezés nem engedélyezett, mert szabad `a`a tartományváltozó hatóköréhez képest:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Ez a korlátozás a gyakorlatban nem lehet probléma, mivel mindig lehetséges a szűrők összeállítása, hogy a lambda kifejezések csak kötött változókat tartalmazzanak.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Cheat lap gyűjtemény szűrő szabályok

Az alábbi táblázat az egyes adatgyűjtési adatok érvényes szűrőinek összeállítására vonatkozó szabályokat foglalja össze.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Az egyes esethez érvényes szűrők létrehozásának példáit az [Érvényes gyűjteményszűrők írása (Útmutató az érvényes gyűjteményszűrők írása) témakörben talál.](#bkmk_examples)

Ha gyakran ír szűrőket, és az első alapelvek ből való szabályok megértése nem csak a memorizálásukban segít, olvassa el [az OData-gyűjteményszűrőinek ismertetése az Azure Cognitive Search című témakört.](search-query-understand-collection-filters.md)

## <a name="next-steps"></a>További lépések  

- [Az OData-adatgyűjtési szűrők ismertetése az Azure Cognitive Search szolgáltatásban](search-query-understand-collection-filters.md)
- [Szűrők az Azure Cognitive Search szolgáltatásban](search-filters.md)
- [Az Azure Cognitive Search OData-kifejezés nyelvének áttekintése](query-odata-filter-orderby-syntax.md)
- [Az Azure Cognitive Search OData-kifejezés szintaxisának hivatkozása](search-query-odata-syntax-reference.md)
- [Az Azure Cognitive Search REST API-&#41;&#40;dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
