---
title: Az értékkorlátozó szűrők az alkalmazások – Azure Search a Keresés a navigációban
description: Szűrési feltétel felhasználó biztonsági azonosítóját, földrajzihely-vagy numerikus értékek keresési eredményei között az Azure Search szolgáltatásban a Microsoft Azure-ban üzemeltetett felhőalapú keresési szolgáltatás lekérdezések csökkentése érdekében.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 5/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 88171487fd180931d4659390f0db3c8619fb2d62
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653453"
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>Az Azure Search értékkorlátozás szűrő létrehozása 

Jellemzőalapú navigáció szolgál a lekérdezési eredmények keresési alkalmazásban, ahol az alkalmazás felhasználói felületi vezérlők hatókörkezelési keresés csoportokhoz dokumentumok (például kategóriák vagy márkái) kínál, és az Azure Search biztosít az adatok struktúrája biztonsági önállóan Irányított szűrés a felhasználói élményt. Ez a cikk gyors áttekintését egy jellemzőalapú navigációs struktúrát a keresési élményt szeretne biztosítani biztonsági létrehozásának alapvető lépéseit. 

> [!div class="checklist"]
> * A szűréshez és a jellemzőalapú mezők kiválasztása
> * A mező attribútumainak beállítása
> * Hozhat létre az index és az adatok betöltése
> * Az értékkorlátozó szűrők lekérdezés hozzáadása
> * Az eredmények kezelése

Értékkorlátozással dinamikus és a egy lekérdezést a visszaadott. Search válaszai a értékkorlátozó kategóriákat ad vissza az eredmények használt bring velük. Ha nem ismeri az aspektusokat, az alábbi példában egy jellemzőalapú navigációs szerkezetben ábrája.

  ![](./media/search-filters-facets/facet-nav.png)

További részletek új, a jellemzőalapú navigáció, és szeretné? Lásd: [jellemzőalapú navigáció megvalósítása az Azure Search](search-faceted-navigation.md).

## <a name="choose-fields"></a>Mezők kiválasztása

Egyetlen érték mezők, valamint a gyűjtemények értékkorlátozással is vetítve. Mezőkkel, amelyek működnek a legjobban a jellemzőalapú navigáció alacsony Számosság rendelkezik: egy kis számú különböző értékeket, amelyek ismételje meg a keresési forrásgyűjteményébe (például a színeket, országok/régiók vagy márkanevek listájában) található dokumentumok teljes. 

Jellemzőalapú mező szerint történik engedélyezve van, az index létrehozásakor állítsa az `facetable` attribútumot `true`. Általánosan is állítsa be a `filterable` attribútumot `true` például, hogy ezek a mezők alapján, amely a felhasználó kiválasztja értékkorlátozással szűrheti a keresőalkalmazás mezők esetében. 

A REST API használatával bármely index létrehozásakor [mezőbe írja be](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) , amelyek esetleg használható lenne a jellemzőalapú navigáció van megjelölve `facetable` alapértelmezés szerint:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ A mező numerikus típusok: `Edm.Int32`, `Edm.Int64`, `Edm.Double`
+ A fenti típusú gyűjtemények (például `Collection(Edm.String)` vagy `Collection(Edm.Double)`)

Nem használhat `Edm.GeographyPoint` vagy `Collection(Edm.GeographyPoint)` jellemzőalapú navigációs mezők. Értékkorlátozással működnek a legjobban a mezők alacsony számossága. A földrajzi koordináták felbontása, mert ritka, hogy bármely fordítani két készletnyi egyenlő egy adott adatkészlet lesz. Értékkorlátozással mint ilyen, a földrajzi koordináták nem támogatottak. Egy hely szerint értékkorlátozás vagy régiót mezőt kell lennie.

## <a name="set-attributes"></a>Attribútumainak beállítása

Vezérelheti a mező használatát index attribútumairól az index egyedi Meződefiníciók kerülnek. A következő példában alacsony számosságú, értékkorlátozás, hasznos, ha a mezők állhat: `category` (Szálloda, amelyben, hostel) `tags`, és `rating`. Ezeknek a mezőknek a `filterable` és `facetable` attribútumok explicit módon a következő set szemléltető célokra. 

> [!Tip]
> Ajánlott eljárásként a teljesítmény- és tárolási optimalizálása kikapcsolása jellemzőkezelés értékkorlátozásként soha nem használt mezőket. Különösen egyedi értékeket, például egy azonosító vagy a termék nevét, a karakterlánc típusú meg `"facetable": false` jellemzőalapú navigációs véletlen (és hatástalan) használati elkerülése érdekében.


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
> Az index definícióját átmásolva [létrehozása az Azure Search-index REST API használatával](https://docs.microsoft.com/azure/search/search-create-index-rest-api). Ez megegyezik a Meződefiníciók felületi eltérések kivételével. A `filterable` és `facetable` attribútumok explicit módon kerülnek a `category`, `tags`, `parkingIncluded`, `smokingAllowed`, és `rating` mezőket. A gyakorlatban `filterable` és `facetable` ezen mezők alapértelmezés szerint engedélyezve lenne, ha a REST API használatával. A .NET SDK használata esetén ezek az attribútumok explicit módon engedélyeznie kell.

## <a name="build-and-load-an-index"></a>Hozhat létre és index betöltése

Egy közbenső (és talán nyilvánvaló) lépés az kell, hogy [hozhat létre, és töltse fel az index](https://docs.microsoft.com/azure/search/search-get-started-dotnet#1---create-index) mielőtt egy lekérdezést. Ebben a lépésben Itt a teljesség megemlíteni azt. Egyik módja határozza meg, hogy rendelkezésre áll-e az index, indexeket lista ellenőrzésével a [portál](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Az értékkorlátozó szűrők lekérdezés hozzáadása

Az alkalmazás kódjában hozhatnak létre egy lekérdezést, amely meghatározza egy érvényes lekérdezést, beleértve a keresési kifejezéseket, értékkorlátozással, szűrőket, pontozási profilok – semmi állítson össze egy kérelmet használt minden része. Az alábbi példa egy kérelmet, amely létrehozza az értékkorlátozó navigációs vendéglátóipar, besorolásával és egyéb készülékek típusa alapján hoz létre.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>A visszaadott eredmény kattintási események

Amikor a végfelhasználó számára megadott értékkorlátozási értéknek kattint, a kezelő kattintson esemény egy kifejezést használja valósíthat meg a felhasználói szándékot. Megadott egy `category` értékkorlátozás, kattintson a "amelyben" kategória implementálása a `$filter` kifejezés, amely az adott típusú szálláshelyigényt választja ki. Ha egy felhasználó "amelyben" jelzi, hogy megjelenjen-e csak motelek kattint, a következő lekérdezés az alkalmazás elküld tartalmaz `$filter=category eq 'motel'`.

A következő kódrészletet, ha a felhasználó kiválaszt egy értéket a kategória aspektus ad hozzá a szűrő kategória.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Ha a felhasználó a megadott értékkorlátozási értéknek egy gyűjtemény mezőhöz hasonlóan `tags`, például a "készlet" értéket, az alkalmazás a következő szintaxist kell használnia: `$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Tippek és megkerülő megoldások

### <a name="initialize-a-page-with-facets-in-place"></a>Helyben értékkorlátozással tartalmazó oldalt inicializálása

Szeretné inicializálni a helyen értékkorlátozással tartalmazó oldalt, ha a lekérdezés használatával ültet be az oldal egy kezdeti értékkorlátozás struktúrával se inicializace stránky részeként küldhet.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>A szűrt eredmények aszinkron módon jellemzőalapú navigációs szerkezetben megőrzése

Az értékkorlátozó navigációs az Azure Search szolgáltatásban a legnagyobb kihívás, hogy létezik-e értékkorlátozással csak az aktuális eredményt. A gyakorlatban szokás megőrzése értékkorlátozással statikus készletét, hogy a felhasználó navigálhat fordított, a Visszalépés lépések végrehajtásával Fedezze fel az alternatív útvonalait a tartalom keresése. 

Bár ez egy gyakori alkalmazási helyzet, már nem valami a jellemzőalapú navigációs szerkezetben jelenleg nyújt a-beépített. A fejlesztők számára statikus értékkorlátozással általában két szűrt lekérdezések kiállításával megkerüléséhez a korlátozás: egyet az eredmények hatóköre, a másik hozzon létre egy statikus értékkorlátozással navigációs célokra használt.

## <a name="see-also"></a>Lásd még

+ [Szűrők az Azure Search szolgáltatásban](search-filters.md)
+ [Index REST API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [REST API-val dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents)
