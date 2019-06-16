---
title: OData-fájlgyűjtési szűrők – Azure Search hibaelhárítása
description: -Hibáinak megoldásáról OData gyűjtemény szűrő az Azure Search-lekérdezéseket.
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
ms.openlocfilehash: c7fa00c82eea03a50bae22fcb1ad16e230aa5bcb
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67079625"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-search"></a>Hibaelhárítás az Azure Search szolgáltatásban az OData-fájlgyűjtési szűrők

A [szűrő](query-odata-filter-orderby-syntax.md) gyűjtemény mezők az Azure Search szolgáltatásban, használhatja a [ `any` és `all` operátorok](search-query-odata-collection-operators.md) együtt **lambda-kifejezésekkel**. Lambda kifejezésnek a alkalmazni egy gyűjtemény összes eleme alárendelt szűrő.

Nem minden funkciója szűrési kifejezésekben nem lambda kifejezésnek belül elérhető. Mely funkciók érhetők el a gyűjtemény mezőt, amely a szűrni kívánt adatok típusától függően eltérő. Emiatt a hibát próbál használni egy szolgáltatást egy lambda kifejezés, amely nem támogatott ebben a környezetben. Gyűjtemény mezők keresztül egy összetett szűrési írása közben az ilyen hibák lépnek fel, ha ez a cikk segítséget nyújt a probléma elhárítását.

## <a name="common-collection-filter-errors"></a>Gyakori hibák a gyűjtemény szűrése

Az alábbi táblázat egy Gyűjteményszűrő végrehajtása közben előforduló hibákat. Ezek a hibák történik, ha a szűrőkifejezésekben egyik funkciója, amely nem támogatott a lambda kifejezésnek belül használhatja. Minden egyes hibához meg, hogyan módosíthatja a szűrőt, hogy a hiba elkerüléséhez útmutatást biztosít. A tábla egy hivatkozást is tartalmaz a megfelelő szakaszt az ebben a cikkben további információt nyújt az adott hiba elkerülése érdekében.

| Hibaüzenet | A helyzet | További információkért lásd: |
| --- | --- | --- |
| A függvény "ismatch" nincs kötve a tartomány változót paraméterekkel rendelkezik ". Lambda-kifejezésekkel ("bármely" vagy "all") belüli hivatkozások támogatottak a mező csak kötve. Módosítsa a szűrőt úgy, hogy a "ismatch" függvény kívül a lambda kifejezésnek, és próbálkozzon újra. | Használatával `search.ismatch` vagy `search.ismatchscoring` lambda kifejezésnek belül | [Gyűjtemények összetett szűrési szabályok](#bkmk_complex) |
| Érvénytelen lambda kifejezésnek. Található egy teszt egyenlőség vagy mutassa, ahol a rendszer ennek az ellenkezője várta, amely Collection(Edm.String) típusú mező ismétel lambda kifejezésnek. Az "összes" használja az űrlap "x eq y" vagy "search.in(...)" kifejezéseket. Az "all", használja az űrlap "x ne y", "nem (x y eq)" vagy "nem search.in(...)" kifejezéseket. | Szűrés egy mező típusa `Collection(Edm.String)` | [Karakterlánc gyűjtemények szűrési szabályok](#bkmk_strings) |
| Érvénytelen lambda kifejezésnek. Egy összetett logikai kifejezés nem támogatott típusú található. Az "összes" kifejezéseket, amelyek "ORs, Equal", más néven diszjunkt normál űrlapot használja. Például: '(a and b) vagy (c a és d) ", ha a, b c d jsou összehasonlító vagy egyenlőségi az alkifejezések. Az "all", használja a kifejezéseket, amelyek az "Equal, ORs", más néven Conjunctive normál űrlap. Például: '(a or b) és (c vagy d) ", ha a, b c d jsou összehasonlító vagy egyenlótlenség az alkifejezések. Példák az összehasonlítási kifejezésekben: "x gt 5', ' le 2 x. A példában egy egyenlőségi kifejezés: "x eq 5'. A példában egy egyenlótlenség kifejezés: "x ne 5'. | Szűrés típusú mezők `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, vagy `Collection(Edm.Int64)` | [Hasonló gyűjtemények szűrési szabályok](#bkmk_comparables) |
| Érvénytelen lambda kifejezésnek. Egy nem támogatott használatát geo.distance() vagy geo.intersects() található, amely Collection(Edm.GeographyPoint) típusú mező ismétel lambda kifejezésnek. "Bármely" Ellenőrizze, hogy, hasonlítsa össze az "lt" vagy "le" operátorok használatával geo.distance(), és győződjön meg arról, hogy a rendszer nem negated geo.intersects() használatáért. Az "all", győződjön meg arról, hasonlítsa össze az "gt" vagy "ge" operátorok használatával geo.distance(), és győződjön meg arról, hogy negated van-e a geo.intersects() használatáért. | Szűrés egy mező típusa `Collection(Edm.GeographyPoint)` | [GeographyPoint gyűjtemények szűrési szabályok](#bkmk_geopoints) |
| Érvénytelen lambda kifejezésnek. Logikai összetett kifejezések nem támogatottak a Collection(Edm.GeographyPoint) típusú mezők ciklustevékenység lambda-kifejezésekkel. A "bármely", csatlakozzon az alkifejezések "vagy"; "és" nem támogatott. Az "all", csatlakozzon az alkifejezések 'és'; "vagy" nem támogatott. | Szűrés típusú mezők `Collection(Edm.String)` vagy `Collection(Edm.GeographyPoint)` | [Karakterlánc gyűjtemények szűrési szabályok](#bkmk_strings) <br/><br/> [GeographyPoint gyűjtemények szűrési szabályok](#bkmk_geopoints) |
| Érvénytelen lambda kifejezésnek. Egy összehasonlító operátor (egyik "lt", "le", "gt" vagy "ge") található. Csak egyenlőségi operátorok Collection(Edm.String) típusú mezők ciklustevékenység lambda-kifejezésekkel engedélyezettek. Az "bármely" használja az űrlap "x eq y" kifejezéseket. Az "all", használja az űrlap "x ne y" vagy "nem (x y eq)" kifejezéseket. | Szűrés egy mező típusa `Collection(Edm.String)` | [Karakterlánc gyűjtemények szűrési szabályok](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Hogyan írhat érvényes fájlgyűjtési szűrők

Érvényes fájlgyűjtési szűrők írására szolgáló szabályok eltérőek az egyes adattípusokhoz. A következő szakaszok ismertetik a szabályok szerint mely szűrő szolgáltatások támogatottak, és amelyek nem mutat be példát:

- [Karakterlánc gyűjtemények szűrési szabályok](#bkmk_strings)
- [Logikai gyűjtemények szűrési szabályok](#bkmk_bools)
- [GeographyPoint gyűjtemények szűrési szabályok](#bkmk_geopoints)
- [Hasonló gyűjtemények szűrési szabályok](#bkmk_comparables)
- [Gyűjtemények összetett szűrési szabályok](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Karakterlánc gyűjtemények szűrési szabályok

Lambda-kifejezésekkel karakterlánc gyűjtemények, belül a csak összehasonlító operátorok használható vannak `eq` és `ne`.

> [!NOTE]
> Az Azure Search nem támogatja a `lt` / `le` / `gt` / `ge` operátorokat karakterláncok, belül vagy kívül lambda kifejezésnek-e.

Törzse egy `any` hasonlítania az egyezés keresésekor törzse közben csak tesztelheti egy `all` egyenlótlenség csak tesztelheti.

Akkor is úgy, hogy több kifejezésének keresztül `or` törzsében egy `any`, és keresztül `and` törzsében egy `all`. Mivel a `search.in` függvény kombinálva egyenlőség ellenőrzi az egyenértékű `or`, törzsében is betölthet egy `any`. Ezzel szemben `not search.in` törzsében engedélyezett egy `all`.

Például ezek a kifejezések engedélyezettek:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

Bár ezek a kifejezések nem engedélyezett:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Logikai gyűjtemények szűrési szabályok

A típus `Edm.Boolean` csak támogatja a `eq` és `ne` operátorok. Mint ilyen, akkor nem értelme, az ilyen feltételek, amelyek ellenőrzik a ugyanazon tartomány változót kombinálásának engedélyezése `and` / `or` óta, amely mindig tautologies vagy ellentmondások vezet.

Íme néhány példa a szűrők a logikai a gyűjteményeket, amelyek használata engedélyezett:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

Karakterlánc-gyűjtemények, ellentétben a logikai gyűjtemények nem operátor használható korlátokkal rendelkeznek a lambda kifejezésnek milyen típusú. Mindkét `eq` és `ne` törzsében használható `any` vagy `all`.

Kifejezések, például a következő logikai gyűjtemények esetében nem engedélyezett:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>GeographyPoint gyűjtemények szűrési szabályok

Típusú értékek `Edm.GeographyPoint` egy gyűjtemény nem hasonlíthatók össze közvetlenül egymással. Ehelyett azok kell használni, a paramétereket a `geo.distance` és `geo.intersects` funkciók. A `geo.distance` függvény viszont össze kell hasonlítani a összehasonlító operátorok segítségével távolság értékre `lt`, `le`, `gt`, vagy `ge`. Ezek a szabályok nem gyűjtemény Edm.GeographyPoint mezők is vonatkozik.

Karakterlánc-gyűjtemények, például `Edm.GeographyPoint` a gyűjteményeknél hogyan a térinformatikai függvények használhatók és a lambda-kifejezésekkel a különböző típusú kombinált bizonyos szabályokat:

- Melyik összehasonlítási operátor használata a `geo.distance` függvény lambda kifejezésnek típusától függ. A `any`, csak `lt` vagy `le`. A `all`, csak `gt` vagy `ge`. Akkor is magában foglaló kifejezések negálandó `geo.distance`, de kell módosítani az összehasonlító operátor (`geo.distance(...) lt x` válik `not (geo.distance(...) ge x)` és `geo.distance(...) le x` válik `not (geo.distance(...) gt x)`).
- Törzsében egy `all`, a `geo.intersects` függvény negated kell lennie. Ezzel szemben a törzse egy `any`, a `geo.intersects` függvény nem kell negated.
- Törzsében egy `any`, térinformatikai kifejezések használatával egyesíthető `or`. Törzsében egy `all`, ezek a kifejezések használatával egyesíthető `and`.

A fenti korlátozások, a karakterlánc-gyűjtemények egyenlőség korlátozást létezik hasonló okok miatt. Lásd: [ismertetése OData fájlgyűjtési szűrők az Azure Search](search-query-understand-collection-filters.md) az alábbi okok miatt alaposabban.

Íme néhány példa a szűrők a `Edm.GeographyPoint` gyűjteményeket, amelyek használata engedélyezett:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Például a következő kifejezések használata nem engedélyezett a `Edm.GeographyPoint` gyűjtemények:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Hasonló gyűjtemények szűrési szabályok

Ebben a szakaszban a következőkre vonatkozik az összes alábbi adattípusok:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

Például típusokat `Edm.Int32` és `Edm.DateTimeOffset` támogatja mind a hat az összehasonlító operátorok: `eq`, `ne`, `lt`, `le`, `gt`, és `ge`. Ilyen típusú gyűjtemények keresztül lambda-kifejezésekkel ezen operátorok bármelyikével egyszerű kifejezést tartalmazhat. Ez egyaránt vonatkozik `any` és `all`. Például ezek a szűrők engedélyezettek:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

Vannak azonban az ilyen összehasonlítási kifejezésekben kombinálására belül lambda kifejezésnek összetettebb kifejezésekre vonatkozó korlátozások:

- A szabályok `any`:
  - Egyszerű egyenlótlenség kifejezések hasznos nem kombinálható más kifejezéseket. Ha például a kifejezés engedélyezett:
    - `ratings/any(r: r ne 5)`

    azonban ez a kifejezés nem:
    - `ratings/any(r: r ne 5 and r gt 2)`

    és ez a kifejezés használata engedélyezett, amíg nem hasznos, mert feltételek átfedik egymást:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Egyszerű összehasonlítási kifejezésekben érintő `eq`, `lt`, `le`, `gt`, vagy `ge` kombinálva `and` / `or`. Példa:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - Összehasonlítási kifejezésekben kombinálva `and` (kötőszavak) további kombinálható is használatával `or`. Az űrlap ismert logikai, az "[diszjunkt normál űrlap](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF). Példa:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- A szabályok `all`:
  - Egyszerű egyenlőségi kifejezés hasznos nem kombinálható más kifejezéseket. Ha például a kifejezés engedélyezett:
    - `ratings/all(r: r eq 5)`

    azonban ez a kifejezés nem:
    - `ratings/all(r: r eq 5 or r le 2)`

    és ez a kifejezés használata engedélyezett, amíg nem hasznos, mert feltételek átfedik egymást:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Egyszerű összehasonlítási kifejezésekben érintő `ne`, `lt`, `le`, `gt`, vagy `ge` kombinálva `and` / `or`. Példa:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Összehasonlítási kifejezésekben kombinálva `or` (disjunctions) további kombinálható is használatával `and`. Az űrlap ismert logikai, az "[Conjunctive normál űrlap](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (használható cnf Paraméterrel). Példa:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Gyűjtemények összetett szűrési szabályok

Lambda-kifejezésekkel összetett gyűjtemények keresztül, mint a lambda-kifejezésekkel jóval rugalmasabb szintaxist támogathatóak típusokban gyűjteményei. Használhat bármilyen szűrő szerkezet belül egy külső egy, két kivétellel használható lambda kifejezésnek.

First, a függvények `search.ismatch` és `search.ismatchscoring` lambda-kifejezésekkel belül nem támogatottak. További információkért lásd: [ismertetése OData fájlgyűjtési szűrők az Azure Search](search-query-understand-collection-filters.md).

A második, a mezők, amelyek nem hivatkozik *kötött* , a tartomány változót (úgynevezett *változók ingyenes*) nem engedélyezett. Vegyük példaként az alábbi két egyenértékű OData szűrési kifejezés:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

Az első kifejezés engedélyezett lesz, míg a második képernyő lesz utasítva, mert az `details/margin` nincs kötve a tartomány változót `s`.

Ez a szabály van kötve egy külső hatókörben található változók kifejezések is kiterjeszti. Az ilyen változók használata ingyenes szerepelnek a hatókör vonatkozóan. Ha például az első kifejezés engedélyezett, míg a második egyenértékű kifejezés nem engedélyezett, mert `s/name` ingyenes garanciát a tartomány változót hatókörének `a`:

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Ez a korlátozás nem lehet a gyakorlatban egy probléma, mivel mindig lehetséges olyan szűrőket úgy, hogy a lambda-kifejezésekkel csak kötött változókat tartalmaz.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Gyorsreferencia gyűjtemény Állapotszűrő szabályok

A következő táblázat összefoglalja a szabályokat hozhat létre, mindegyik gyűjteményt adattípus érvényes szűrőket.

[!INCLUDE [Limitations on OData lambda expressions in Azure Search](../../includes/search-query-odata-lambda-limitations.md)]

Példák érvényes szűrők minden esetben a létrehozására: [hogyan írhat érvényes fájlgyűjtési szűrők](#bkmk_examples).

Ha gyakran írási szűrőket, és a szabályokat az első elvek megismerése segíthet, több mint csak megjegyezni a őket, lásd: [ismertetése OData fájlgyűjtési szűrők az Azure Search](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>További lépések  

- [OData-fájlgyűjtési szűrők az Azure Search ismertetése](search-query-understand-collection-filters.md)
- [Szűrők az Azure Search szolgáltatásban](search-filters.md)
- [Az Azure Search OData kifejezés nyelvi – áttekintés](query-odata-filter-orderby-syntax.md)
- [Az Azure Search OData kifejezés szintaxisának referenciája](search-query-odata-syntax-reference.md)
- [Dokumentumok keresése &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
