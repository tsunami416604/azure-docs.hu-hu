---
title: A szűrők és order by záradékok – Azure Search OData-kifejezések szintaxisa
description: Szűrő és order by kifejezés OData-szűrőszintaxis Azure Search-lekérdezésekhez.
ms.date: 01/31/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
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
ms.openlocfilehash: 1f1fbc49a42ec9b72ebe74a96ee099630d7416b1
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57570651"
---
# <a name="odata-expression-syntax-for-filters-and-order-by-clauses-in-azure-search"></a>A szűrők és az Azure Search szolgáltatásban az order by záradékok OData-kifejezések szintaxisa

Az Azure Search az OData-kifejezések szintaxisa a egy részét támogatja **$filter** és **$orderby** kifejezéseket. Szűrési kifejezésekben értékeli ki a lekérdezés-elemzés, search az egyes mezők neve vagy indexe vizsgálatok során használt egyezési feltételek hozzáadása során. Order by kifejezésnek alkalmazza a rendszer egy utólagos feldolgozási lépést keresztül egy eredményhalmaz. Szűrők és a order by kifejezésnek szerepelnek a lekérdezési kérést, és független az OData-szűrőszintaxis tartja a [egyszerű](query-simple-syntax.md) vagy [teljes](query-lucene-syntax.md) lekérdezési szintaxist használja egy **keresési** a paraméter. Ez a cikk a szűrők és a rendezési kifejezésben használt OData-kifejezések dokumentációja nyújt.

## <a name="filter-syntax"></a>Szintaxis szűrése

A **$filter** kifejezés önálló, teljes körűen kifejezett lekérdezésként hajtható végre, vagy pontosítsa a lekérdezést, amely olyan további paraméterekkel rendelkezik. Az alábbi példák bemutatják, néhány főbb forgatókönyvek megvalósítását. Az első példában a szűrő az anyag a lekérdezés.


```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11
    {
      "filter": "(baseRate ge 60 and baseRate lt 300) or hotelName eq 'Fancy Stay'"
    }
```

Egy másik gyakori használati eset Kombinált szűrők értékkorlátozás, ahol a szűrő csökkenti a lekérdezési felületének egy felhasználó-központú értékkorlátozó navigációs választott beállítás alapján:

```POST
POST /indexes/hotels/docs/search?api-version=2017-11-11
    {
      "search": "test",
      "facets": [ "tags", "baseRate,values:80|150|220" ],
      "filter": "rating eq 3 and category eq 'Motel'"
    }
```

### <a name="filter-operators"></a>Szűrési operátorokhoz  

-   Logikai operátorok (és, vagy sem).  

-   Összehasonlítási kifejezésekben (`eq, ne, gt, lt, ge, le`). A sztring-összehasonlítások megkülönböztetik a kis- és nagybetűket.  

-   Támogatott állandók [Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM) típusok (lásd: [által támogatott adattípusokkal &#40;Azure Search&#41; ](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) támogatott típusok listája). Gyűjtemény típusú állandók használata nem támogatott.  

-   Mező nevére hivatkozik. Csak `filterable` mezőt a szűrőkifejezésekben is használható.  

-   `any` Paraméterek nélkül. Ez teszteli, hogy típusú mező `Collection(Edm.String)` elemeket tartalmaz.  

-   `any` és `all` korlátozott lambda kifejezés támogatásával. 
    
    -   `any/all` támogatottak a típusú mezők `Collection(Edm.String)`. 
    
    -   `any` csak akkor használható, az egyszerű egyenlőségi kifejezés vagy egy `search.in` függvény. Egyszerű kifejezések például állhat egyetlen mezőt és a egy konstans érték összehasonlítását `Title eq 'Magna Carta'`.
    
    -   `all` csak akkor használható, az egyszerű egyenlótlenség kifejezések vagy egy `not search.in`.   

-   A térinformatikai függvények `geo.distance` és `geo.intersects`. A `geo.distance` függvényt ad vissza a távolságot adja meg kilométerben két pont között, egy egy mezőt, és a egy folyamatban van egy állandó átadott szűrő részeként. A `geo.intersects` függvény igaz értéket ad vissza egy adott időpontra van egy adott sokszög belül, amelyben a pont egy mezőt, és a sokszög egy szűrő részeként átadott konstansként van megadva.  

    A sokszög az egy kétdimenziós felület tárolt pontok sorozataként meghatározása egy határoló csörögni (lásd az alábbi példát). A sokszög kell zárni, tehát az első és utolsó pont csoportok azonosnak kell lennie. [Egy sokszög pontjainak járásával sorrendben kell](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    `geo.distance` az Azure Search kilométerben távolságot adja vissza. Ez eltér más szolgáltatásokkal, amelyek támogatják az OData térinformatikai műveleteket, amelyeket általában mérőszámok távolságot adja vissza.  

    > [!NOTE]  
    >  Ha geo.distance szűrőt használ, a távolság egy állandó használatakor a függvény által visszaadott össze kell hasonlítania `lt`, `le`, `gt`, vagy `ge`. Az operátorok `eq` és `ne` távokat összehasonlítása nem támogatott. Például ez az egy megfelelő használatának geo.distance: `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`.  

-   A `search.in` függvény azt teszteli-e egy adott karakterlánc típusú megegyezik egy adott értékek listáját. Azt is segítségével bármely vagy összes gyűjtemény karakterlánc típusú értékeket az adott listáját egyetlen érték összehasonlítására. A mező és a lista minden egyes érték közötti egyenlőségi határozza meg a kis-és nagybetűket módon, ugyanúgy mint a `eq` operátor. Ezért, például egy kifejezést `search.in(myfield, 'a, b, c')` egyenértékű `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, azzal a különbséggel, hogy `search.in` sokkal jobb teljesítményt eredményez. 

    Az első paraméterként a `search.in` függvény a sztring mezőhivatkozásnak (vagy egy karakterlánc-gyűjtemény mezők abban az esetben tartomány változó ahol `search.in` használnak egy `any` vagy `all` kifejezés). A második paraméter megadása az értékek, szóközöket és/vagy a vesszővel elválasztott listáját tartalmazó karakterlánc. Eltérő szóközöket és vesszőket elválasztók használható, mert a tartalmazzák-e ezeket a karaktereket kell, ha egy nem kötelező harmadik paramétert is megadhat `search.in`. 

    A harmadik paramétere egy karakterláncot, ahol minden karaktert a karakterlánc, vagy ez a karakterlánc részét úgy viselkedik, mint az elválasztó elemzésekor a második paraméterben szereplő értékek listáját.

    > [!NOTE]   
    > Bizonyos helyzetekben szükséges összehasonlítása egy mezőt egy állandó értékek nagy számú ellen. Például biztonsági elrejtés végrehajtása szűrőkkel szükség lehet összehasonlítása a dokumentum azonosító mező ellen, amelyhez a kérelmező felhasználó van olvasási hozzáférést azonosítók listáját. Ilyen esetekben erősen javasoljuk a `search.in` függvényt egy bonyolultabb vagy műveletet egyenlőség kifejezések helyett. Például `search.in(Id, '123, 456, ...')` helyett `Id eq 123 or Id eq 456 or ....`. 
    >
    > Ha `search.in`, várható másodperc törtrésze válaszideje, amikor a második paraméter több száz vagy ezer értékek listáját tartalmazza. Ne feledje, hogy van-e explicit adhat át elemek száma korlátlan `search.in`, bár továbbra is korlátozza a kérelem maximális méretét. Azonban a késési értékek számának növekedésével növekszik.

-   A `search.ismatch` függvény kiértékeli a keresési lekérdezésnek egy szűrőkifejezés részeként. Az eredményhalmaz a dokumentumokat, a keresési lekérdezésnek megfelelő lesznek visszaadva. Ez a függvény a következő túlterhelésekkel érhetők el:
    - `search.ismatch(search)`
    - `search.ismatch(search, searchFields)`
    - `search.ismatch(search, searchFields, queryType, searchMode)`

    Ahol: 
  
    - `search`: a keresési lekérdezés (akár [egyszerű](query-simple-syntax.md) vagy [teljes](query-lucene-syntax.md) lekérdezés szintaxisa). 
    - `queryType`: "egyszerű" vagy "teljes", alapértelmezett értéke "Simple". Itt adhatja meg, milyen lekérdezési nyelv használták a `search` paraméter.
    - `searchFields`: a keresés, kereshető mezőket vesszővel elválasztott listája alapértelmezés szerint az összes kereshető mezőt az indexben.    
    - `searchMode`: "bármely" vagy "all", az alapértelmezett érték "bármely". Azt jelzi, hogy a keresési feltételeket egy részének vagy egészének egyeztetni annak érdekében, hogy a dokumentum, amely száma.

    A fenti paraméterek egyenértékűek a megfelelő [keresse a kérelem paramétereinek](https://docs.microsoft.com/rest/api/searchservice/search-documents).

-   A `search.ismatchscoring` működni, mint a `search.ismatch` működik, dokumentumok, amely megfelel a keresési lekérdezés paraméterként igaz értéket ad vissza. Az egymás közötti különbség, hogy a relevancia alapján pontszámot rendelni az egyező dokumentumok a `search.ismatchscoring` lekérdezés hozzájárul a teljes dokumentum pontszám, a kis-és `search.ismatch`, a dokumentumok pontszámát nem módosítható. Ez a függvény a következő túlterhelésekkel érhetők el paraméterek azonosak `search.ismatch`:
    - `search.ismatchscoring(search)`
    - `search.ismatchscoring(search, searchFields)`
    - `search.ismatchscoring(search, searchFields, queryType, searchMode)`

  A `search.ismatch` és `search.ismatchscoring` funkciók egymással, és a többi a szűrő algebra teljes merőleges. Ez azt jelenti, hogy mindkét függvény is használható ugyanazon a szűrőkifejezést. 

### <a name="geospatial-queries-and-polygons-spanning-the-180th-meridian"></a>A térinformatikai lekérdezések és a 180th délkörének átfedés poligonok  
 Számos térinformatikai lekérdezés kidolgozásában egy lekérdezést, amely magában foglalja a (közel az Dátumvonali) 180th délkörének kódtárak, mint off-limits vagy egy megkerülő megoldás, például a sokszög felosztása két, egyet a hosszúság egyik oldalán igényel.  

 Az Azure Search szolgáltatásban 180 fokos hosszúsági tartalmazó térinformatikai lekérdezések fog a várt módon működik-e a lekérdezés alakzat téglalap alakú, és a koordináták a szélességi és hosszúsági mentén rácsos elrendezés igazítás (például `geo.intersects(location, geography'POLYGON((179 65,179 66,-179 66,-179 65,179 65))'`). Ellenkező esetben nem téglalap vagy nem igazított alakzatok, fontolja meg a vegyes sokszög megközelítés.  

<a name="bkmk_limits"></a>

## <a name="filter-size-limitations"></a>Szűrő fájlméretre vonatkozó korlátozások 

 A méret és összetettségét, melyet elküldhet az Azure Search szűrőkifejezésekben korlátozva van. A korlátok nagyjából a szűrőkifejezést záradékai száma alapulnak. Jó tapasztalatok, hogy ha több száz szolgáló szerződéses klauzulák, áll: a korlát veszélye. Azt javasoljuk, hogy az alkalmazás úgy, hogy azt nem generál korlátlan méretű szűrők tervezése.  


## <a name="filter-examples"></a>Példák a szűrők  

 Keresse meg azt az alapdíj mellett minden hotels kisebb, mint 100 USD, vagy afölött 4 értékelése:  

```
$filter=baseRate lt 100.0 and rating ge 4
```

 Keresse meg az összes hotels "Roach amelyben" kivételével, amely rendelkezik lett felújított 2010 óta:  

```
$filter=hotelName ne 'Roach Motel' and lastRenovationDate ge 2010-01-01T00:00:00Z
```

 Keresse meg az összes hotels alap arány kisebb, mint 200 USD értékű, amely rendelkezik lett felújított 2012, a dátumból/időből konstans, amely tartalmazza az időzóna-információk a csendes-óceáni téli idő óta:  

```
$filter=baseRate lt 200 and lastRenovationDate ge 2012-01-01T00:00:00-08:00
```

 Keresse meg az összes "Hotels", amely rendelkezik a várakozást a csomagban foglalt, és ne engedélyezze a fogyasztási:  

```
$filter=parkingIncluded and not smokingAllowed
```

 \- VAGY –  

```
$filter=parkingIncluded eq true and smokingAllowed eq false
```

 Keresse meg az összes hotels engedélyezhető illetve ideiglenes közé tartozik és rendelkezik minősítéssel az 5-ből:  

```
$filter=(category eq 'Luxury' or parkingIncluded eq true) and rating eq 5
```

 Keresse meg a "Wi-Fi" címkével ellátott összes hotels (ahol minden egyes Szálloda tároló Collection(Edm.String) mezőt címkékkel rendelkezik):  

```
$filter=tags/any(t: t eq 'wifi')
```

 Keresse meg az összes hotels "amelyben" címke nélkül:  

```
$filter=tags/all(t: t ne 'motel')
```

 Az összes "Hotels" bármely címke található:  

```
$filter=tags/any()
```

Keresse meg, amelyek nem rendelkeznek a címkék az összes hotels:  

```
$filter=not tags/any()
```


 Keresse meg az összes hotels belül (amennyiben hely Edm.GeographyPoint típusú mező a) megadott hivatkozási pont 10 kilométerben:  

```
$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10
```

 Egy adott nézőpont sokszög fürtkezelőben összes hotels (ahol hely Edm.GeographyPoint típusú mező a) található. Vegye figyelembe, hogy bezárja-e a sokszög (az első és utolsó pont csoportok azonosnak kell lennie), és [szerepelnie kell a pontok járásával sorrendben](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

```
$filter=geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

 Keresse meg vagy amelyeknek nincs értéke "description" mező a "Hotels", vagy értéket explicit módon van beállítva a null:  

```
$filter=description eq null
```

Keresse meg az összes "Hotels" nevű vagy Roach amelyben egyenlő "vagy"Költségvetés hotel"):  

```
$filter=search.in(name, 'Roach motel,Budget hotel', ',')
```

Egyenlő vagy Roach amelyben található összes "Hotels" nevű "vagy"Költségvetés hotel"elválasztva ' |}"):  

```
$filter=search.in(name, 'Roach motel|Budget hotel', '|')
```

Keresse meg a címke "Wi-Fi" vagy "készlet" összes "Hotels":  

```
$filter=tags/any(t: search.in(t, 'wifi, pool'))
```

Az összes hotels nélkül a címke "amelyben" és "kézi" nem található:  

```
$filter=tags/all(t: not search.in(t, 'motel, cabin'))
```

Keresse meg az "i partszakasz" szót tartalmazó dokumentumokat. Ez a szűrő-lekérdezés megegyezik egy [keresési kérelmet](https://docs.microsoft.com/rest/api/searchservice/search-documents) a `search=waterfront`.

```
$filter=search.ismatchscoring('waterfront')
```

Keresse meg a dokumentumot a word "hostel" és a nagyobb vagy egyenlő "amelyben" szót tartalmazó dokumentumok vagy 4 minősítés és minősítés megegyezik az 5. Vegye figyelembe, a kérelem nem fejezik nélkül a `search.ismatchscoring` függvény.

```
$filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

Keresse meg a dokumentumok szó nélkül "engedélyezhető".

```
$filter=not search.ismatch('luxury') 
```

Keresse meg a dokumentumok minősítési értéknek 5 vagy "óceán view" kifejezés helyett szerepel. A `search.ismatchscoring` lekérdezés hajtani, csak a mező alapján `hotelName` és `description`.
Vegye figyelembe, hogy a vagy műveletet csak a második záradék egyező dokumentumok túl vissza kell - minősítéssel rendelkező hotels 5 egyenlő. Hogy ezeket a dokumentumokat egyértelmű legyen nem felel meg a kifejezés a pontozott részeit, akkor adja vissza a pontszám nullánál.

```
$filter=search.ismatchscoring('"ocean view"', 'description,hotelName') or rating eq 5
```

Dokumentumok keresése, ahol a feltételek "hotel" és "repülőtér" Szálloda leírásában egymástól 5 szó belül, és ahol fogyasztási nem engedélyezett. Ez a lekérdezés használ a [teljes Lucene lekérdezési nyelv](query-lucene-syntax.md).

```
$filter=search.ismatch('"hotel airport"~5', 'description', 'full', 'any') and not smokingAllowed 
```

## <a name="order-by-syntax"></a>Az Order by szintaxis

A **$orderby** paraméter az űrlap legfeljebb 32 kifejezések vesszővel tagolt listáját fogadja el `sort-criteria [asc|desc]`. A rendezési feltételek lehet nevét egy `sortable` Pole nebo hívása célpontjának a `geo.distance` vagy a `search.score` funkciók. Használhat `asc` vagy `desc` explicit módon kell megadni a rendezési sorrendet. Az alapértelmezett iránya növekvő.

Ha több dokumentumot rendezési ugyanezeket a feltételeket és `search.score` függvény nem szolgál (például akkor, ha egy numerikus szerinti rendezés `rating` mező és három dokumentumok összes rendelkezik egy minősítése 4), ties oszlanak dokumentum pontszám szerint csökkenő sorrendben. A dokumentum pontszámok azonosak (például, ha nincs megadva a kérelemben szereplő teljes szöveges keresési lekérdezés), majd a kapcsolt dokumentumok relatív sorrendjének esetén meghatározatlan.
 
Több rendezési feltételt is megadhat. Kifejezések sorrendjét a végső rendezési sorrend határozza meg. Például csökkenő pontszám, kiegészítve a minősítést, szerint rendezheti a szintaxis lenne `$orderby=search.score() desc,rating desc`.

A szintaxist a `geo.distance` a **$orderby** van ugyanaz, mint az **$filter**. Használata esetén `geo.distance` a **$orderby**, amelyekre vonatkozik a mező típusúnak kell lennie `Edm.GeographyPoint` kell lennie, és `sortable`.  

A szintaxist a `search.score` a **$orderby** van `search.score()`. A függvény `search.score` nem használ paramétereket.  
 

## <a name="order-by-examples"></a>Az Order by példák

Alapdíj szerint növekvő rendezés hotels:

```
$orderby=baseRate asc
```

A "Hotels" minősítés, majd az alapdíj mellett szerint növekvő szerint csökkenő rendezés (ne feledje, hogy a növekvő sorrendben az alapértelmezett):

```
$orderby=rating desc,baseRate
```

A "Hotels" minősítés, akkor az adott fordítani a távolság szerint növekvő szerint csökkenő rendezési:

```
$orderby=rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

Rendezés csökkenő sorrendben search.score és minősítése, majd a növekvő sorrendben távolság szerint az adott koordinátáit, így között két hotels azonos besorolású, a legközelebbire legyen az első hotels:

```
$orderby=search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```
<a name="bkmk_unsupported"></a>

## <a name="unsupported-odata-syntax"></a>Nem támogatott OData-szűrőszintaxis

-   Aritmetikai kifejezések  

-   Függvények (és a térinformatikai függvények metszi saját távolság kivételével)  

-   `any/all` tetszőleges lambda-kifejezésekkel  

## <a name="see-also"></a>Lásd még  

+ [Jellemzőalapú navigáció az Azure Search szolgáltatásban](search-faceted-navigation.md) 
+ [Szűrők az Azure Search szolgáltatásban](search-filters.md) 
+ [Dokumentumok keresése &#40;az Azure Search szolgáltatás REST API-ja&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Lucene lekérdezési szintaxis](query-lucene-syntax.md)
+ [Az Azure Search egyszerű lekérdezési szintaxis](query-simple-syntax.md)   
