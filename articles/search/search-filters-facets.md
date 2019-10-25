---
title: Az alkalmazásokban való kereséshez használt dimenziós szűrők
titleSuffix: Azure Cognitive Search
description: A feltételeket a felhasználói biztonsági identitás, a földrajzi hely vagy a numerikus értékek alapján szűrheti, így csökkenthető a keresési eredmények az Azure Cognitive Search-on futó felhőalapú keresési szolgáltatásban Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 082575a67ea43d62f322e177cff087e5bd572c27
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792896"
---
# <a name="how-to-build-a-facet-filter-in-azure-cognitive-search"></a>Dimenziós szűrő létrehozása az Azure-ban Cognitive Search 

A részletes Navigálás a lekérdezés eredményeinek önálló irányított szűrésére szolgál, ahol az alkalmazás felhasználói felületi vezérlőket biztosít a dokumentumok csoportjaihoz (például kategóriákhoz vagy márkákhoz) való kereséshez, és az Azure Cognitive Search biztosítja az adatszerkezetet a felhasználói élmény visszaállításához. Ebből a cikkből megtudhatja, hogyan hozhat létre részletes navigációs struktúrát a megadható keresési felület létrehozásához szükséges alapvető lépésekkel. 

> [!div class="checklist"]
> * Szűrési és aspektusi mezők kiválasztása
> * Attribútumok beállítása a mezőben
> * Az index létrehozása és az adatterhelés
> * Dimenziós szűrők hozzáadása egy lekérdezéshez
> * Az eredmények kezelése

A dimenziók dinamikusak, és egy lekérdezésen keresztül lesznek visszaadva. A keresési válaszok bemutatják azokat az eredmények eléréséhez használt aspektus-kategóriákat. Ha nem ismeri a dimenziókat, a következő példa egy dimenziós navigációs struktúra szemléltetése.

  ![](./media/search-filters-facets/facet-nav.png)

Új a csiszolatlan navigációhoz, és további részletekre van szüksége? Megtudhatja [, hogyan valósítható meg a részletes navigálás az Azure Cognitive Searchban](search-faceted-navigation.md).

## <a name="choose-fields"></a>Mezők kiválasztása

Az aspektusok az egyértékű mezőkön és a gyűjteményeken is kiszámíthatók. A leghatékonyabban felhasználható navigációs mezőkben kevés a különbség: kis számú különböző érték, amely megismétli a keresés során a dokumentumok között (például a színek, országok/régiók vagy márkanevek listája). 

Ha a `facetable` attribútumot `true`értékre állítja, a rendszer az adatmegjelenítést a mező alapján hozza létre. A `filterable` attribútumot általában úgy kell beállítani, hogy az ilyen mezőkhöz `true`, így a keresőalkalmazás a végfelhasználó által kiválasztott aspektusok alapján szűrheti ezeket a mezőket. 

Ha a REST API használatával hoz létre egy indexet, a rendszer alapértelmezés szerint a csiszolatlan Navigálás során esetlegesen felhasználható bármely [típusú mezőt](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) `facetable`ként jelöli meg:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Numerikus mezők típusai: `Edm.Int32`, `Edm.Int64`, `Edm.Double`
+ A fenti típusok gyűjteményei (például `Collection(Edm.String)` vagy `Collection(Edm.Double)`)

Nem használhat `Edm.GeographyPoint` vagy `Collection(Edm.GeographyPoint)` mezőket a sokoldalú navigálásban. Az aspektusok a legjobban a kis-és nagyszámú mezőkben működnek. A földrajzi koordináták feloldása miatt ritkán fordul elő, hogy a két közös koordinációs csoport egy adott adatkészletben egyenlő lesz. Ezért az aspektusok nem támogatottak a földrajzi koordináták esetében. Egy város vagy régió mezőre lesz szüksége a következő helyen: Face.

## <a name="set-attributes"></a>Attribútumok beállítása

Indexelő attribútumok, amelyek azt szabályozzák, hogy a rendszer hogyan használja a mezőket az index egyes mezőihez. A következő példában a kis-és nagybetűket tartalmazó mezők, amelyek hasznosak az aspektusokhoz, a következőkből állnak: `category` (Hotel, Motel, Hostel), `tags`és `rating`. Ezek a mezők a `filterable` és `facetable` attribútumok explicit módon vannak beállítva a következő példában szemléltető célokra. 

> [!Tip]
> A teljesítmény és a tárolás optimalizálásának ajánlott eljárása, ha olyan mezőkhöz kapcsolja ki a dimenziókat, amelyek soha nem használhatók dimenzióként. Az egyedi értékekhez (például AZONOSÍTÓhoz vagy terméknévhoz) tartozó karakterlánc-mezőket úgy kell beállítani, hogy `"facetable": false` legyenek, hogy a véletlen (és nem hatékony) használatuk elkerülhető legyen a sokoldalú navigációban.


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> Az index definícióját a rendszer [a REST API használatával másolja az Azure Cognitive Search index létrehozása](https://docs.microsoft.com/azure/search/search-create-index-rest-api)elemből. Ez a mező-definíciók esetében azonos, kivéve a felületi különbségeket. A `filterable` és `facetable` attribútumok explicit módon hozzáadódnak a `category`, `tags`, `parkingIncluded`, `smokingAllowed`és `rating` mezőkhöz. A gyakorlatban a `filterable` és az `facetable` a REST API használatakor alapértelmezés szerint engedélyezve lesznek ezeken a mezőkön. A .NET SDK használatakor ezeket az attribútumokat explicit módon kell engedélyezni.

## <a name="build-and-load-an-index"></a>Index létrehozása és betöltése

Egy közbenső (és talán nyilvánvaló) lépés az, hogy [az indexet fel kell építenie és fel kell töltenie a](https://docs.microsoft.com/azure/search/search-get-started-dotnet#1---create-index) lekérdezés kialakítása előtt. Ez a lépés a teljesség kedvéért van megemlítve. Az indexek elérhetővé tételének egyik módja a [portál](https://portal.azure.com)indexek listájának ellenőrzése.

## <a name="add-facet-filters-to-a-query"></a>Dimenziós szűrők hozzáadása egy lekérdezéshez

Az alkalmazás kódjában állítson össze egy olyan lekérdezést, amely egy érvényes lekérdezés összes részét megadja, beleértve a keresési kifejezéseket, a dimenziókat, a szűrőket, a pontozási profilokat – bármit, ami a kérés összeállításához használatos. Az alábbi példa egy olyan kérést készít, amely dimenziós navigálást hoz létre a szállás, a minősítés és más kényelmi szolgáltatások típusa alapján.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Szűrt eredmények visszaküldése a kattintási eseményekre

Ha a végfelhasználó egy dimenzióérték-értékre kattint, a click esemény kezelőjének egy szűrési kifejezést kell használnia a felhasználó szándékának felismeréséhez. Egy `category` aspektusban a "Motel" kategóriára kattintva egy `$filter` kifejezéssel van megvalósítva, amely kiválasztja az adott típusú szállást. Amikor a felhasználó a "Motel" gombra kattint, jelezve, hogy csak a motelek jelennek meg, az alkalmazás által küldött következő lekérdezés tartalmazza a `$filter=category eq 'motel'`.

A következő kódrészlet hozzáadja a szűrőt a szűrőhöz, ha a felhasználó egy értéket választ ki a kategória dimenzióból.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Ha a felhasználó egy olyan gyűjtemény mezőjére kattint, mint például a `tags`például a "pool" érték, az alkalmazásnak a következő szűrési szintaxist kell használnia: `$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Tippek és megkerülő megoldások

### <a name="initialize-a-page-with-facets-in-place"></a>Oldal inicializálása a helyi dimenziókkal

Ha egy olyan oldalt szeretne inicializálni, amelynek a helyén dimenziók vannak, akkor a lap inicializálásának részeként küldhet lekérdezést, amely kezdeti dimenziós struktúrával rendelkezik.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Dimenziós navigációs struktúra aszinkron módon történő megőrzése szűrt eredmények alapján

Az Azure Cognitive Searchban az egyik kihívás az, hogy az aktuális eredményekhez csak az összes aspektus létezik. A gyakorlatban gyakori a dimenziók statikus készletének megtartása, hogy a felhasználó visszafelé Tudjon navigálni, és újra nyomon követheti az alternatív útvonalakat a keresési tartalmakon. 

Bár ez egy gyakori használati eset, nem az, hogy a facet navigációs struktúra jelenleg azonnal elérhető. A statikus aspektusokat szeretnéő fejlesztők általában két szűrt lekérdezés kiadásával használják a korlátozásokat: egy hatókört az eredményekre, a másik pedig a dimenziók statikus listáját hozza létre navigációs célokra.

## <a name="see-also"></a>Lásd még:

+ [Szűrők az Azure Cognitive Search](search-filters.md)
+ [Index létrehozása REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Dokumentumok keresése REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
