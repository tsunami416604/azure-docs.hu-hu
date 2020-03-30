---
title: Az alkalmazásokban történő keresési navigáció facet szűrői
titleSuffix: Azure Cognitive Search
description: A feltételeket felhasználói biztonsági identitás, földrajzi hely vagy numerikus értékek szerint szűrheti, hogy csökkentse a keresési eredményeket az Azure Cognitive Search, a Microsoft Azure üzemeltetett felhőalapú keresési szolgáltatásában.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 082575a67ea43d62f322e177cff087e5bd572c27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792896"
---
# <a name="how-to-build-a-facet-filter-in-azure-cognitive-search"></a>Facet szűrő létrehozása az Azure Cognitive Search szolgáltatásban 

A köralapú navigáció a lekérdezési eredmények önirányított szűréséhez használatos egy keresési alkalmazásban, ahol az alkalmazás felhasználói felületi vezérlőket kínál a dokumentumcsoportok (például kategóriák vagy márkák) közötti keresés hatóköréhez, és az Azure Cognitive Search biztosítja az adatstruktúrát hogy visszaadják az élményt. Ebben a cikkben gyorsan tekintse át a kívánt keresési élményt támogató, sokoldalú navigációs struktúra létrehozásának alapvető lépéseit. 

> [!div class="checklist"]
> * Mezők kiválasztása szűréshez és kifejezészéshez
> * Attribútumok beállítása a mezőben
> * Az index létrehozása és az adatok betöltése
> * Mezőszűrők hozzáadása lekérdezéshez
> * Az eredmények kezelése

A facet-ek dinamikusak, és egy lekérdezésre adják vissza. A keresési válaszok magukkal hozzák az eredmények közötti navigáláshoz használt aspektusakategóriákat. Ha nem ismeri a lapkákat, az alábbi példa egy facet navigációs struktúra illusztrációja.

  ![](./media/search-filters-facets/facet-nav.png)

Új a ráfelé irányuló navigációban, és további részleteket szeretne? Olvassa [el a kifejezéstelen navigáció megvalósítása az Azure Cognitive Search szolgáltatásban.](search-faceted-navigation.md)

## <a name="choose-fields"></a>Mezők kiválasztása

A lapadatok egyetlen értékmezők, valamint gyűjtemények alapján is kiszámíthatók. A főalapú navigációban a legjobban használható mezők számossága alacsony: a keresési korpuszban lévő dokumentumokban ismétlődő különböző értékek kis száma (például színek, országok/régiók vagy márkanevek listája). 

A faceting mezőenként engedélyezve van, amikor az indexet `facetable` úgy `true`hozza létre, hogy az attribútumot a értékre állítja. Általában az `filterable` ilyen mezők attribútumát `true` is be kell állítania, hogy a keresési alkalmazás szűrhessen ezekre a mezőkre a végfelhasználó által kiválasztott mezők alapján. 

Index létrehozásakor a REST API-t használva minden olyan [mezőtípus,](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) amely `facetable` a jellemzőalapú navigációban használható, alapértelmezés szerint a következőként lesz megjelölve:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Numerikus mezőtípusok: `Edm.Int32`, `Edm.Int64`,`Edm.Double`
+ A fenti típusú gyűjtemények (például `Collection(Edm.String)` vagy `Collection(Edm.Double)`)

A kifejezésalapú navigációban nem használható `Edm.GeographyPoint` vagy `Collection(Edm.GeographyPoint)` használhat mezőket. Az alacsony számosságú mezőkön a facets a legjobban működik. A földrajzi koordináták felbontása miatt ritkán fordul elő, hogy egy adott adatkészletben két koordináta-készlet egyenlő lesz. Így a földrajzi koordináták nem támogatottak. A hely szerint egy város- vagy régiómezőre van szükség.

## <a name="set-attributes"></a>Attribútumok beállítása

A mező használatát szabályozó indexattribútumok hozzáadódnak az index egyes meződefinícióihoz. A következő példában az alacsony számosságú mezők, amelyek `category` a következők: (hotel, motel, hostel), `tags`és `rating`. Ezek a `filterable` mezők `facetable` a következő példában szemléltető célokra explicit módon vannak beállítva és attribútumokkal. 

> [!Tip]
> A teljesítmény és a tárolás optimalizálása ajánlott eljárásként kapcsolja ki a mezőket, amelyeket soha nem szabad a facet-ként használni. Különösen az egyedi értékek, például az azonosító vagy a termék `"facetable": false` neve karakterláncmezőit kell beállítani, hogy megakadályozzák azok véletlen (és hatástalan) használatát a sokoldalú navigációban.


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
> Ezt az indexdefiníciót a [REST API használatával az Azure Cognitive Search index létrehozása című indexből](https://docs.microsoft.com/azure/search/search-create-index-rest-api)másolja a rendszer. Ez azonos, kivéve a meződefiníciók felületes különbségeit. A `filterable` `facetable` és az attribútumok `category` `tags`explicit `parkingIncluded` `smokingAllowed`módon `rating` kerülnek hozzáadásra a , , , , és mezőkhöz. A gyakorlatban, `filterable` és `facetable` alapértelmezés szerint engedélyezve lenne ezeken a mezőkön a REST API használatakor. A .NET SDK használatakor ezeket az attribútumokat explicit módon engedélyezni kell.

## <a name="build-and-load-an-index"></a>Index létrehozása és betöltése

Egy köztes (és talán nyilvánvaló) lépés az, hogy létre kell adnia és fel kell [népesítenie az indexet a](https://docs.microsoft.com/azure/search/search-get-started-dotnet#1---create-index) lekérdezés létrehozása előtt. A teljesség gel együtt megemlítjük ezt a lépést. Az index rendelkezésre áll-e annak meghatározásához, hogy a [portálon](https://portal.azure.com)az indexek listáját ellenőrzi.

## <a name="add-facet-filters-to-a-query"></a>Mezőszűrők hozzáadása lekérdezéshez

Az alkalmazáskódban hozhat létre egy lekérdezést, amely egy érvényes lekérdezés minden részét meghatározza, beleértve a keresési kifejezéseket, a részadatokat, a szűrőket, a pontozási profilokat – bármit, amit a kérelem megfogalmazásához használnak. A következő példa egy kérelmet hoz létre, amely a szállás típusa, a minősítés és egyéb szolgáltatások alapján létrehozza a facet navigációt.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Szűrt eredmények visszaadása kattintási eseményeken

Amikor a végfelhasználó egy előtértékre kattint, a kattintási esemény kezelőjének szűrőkifejezést kell használnia a felhasználó szándékának megvalósításához. Adott `category` egy aspektusa, kattintson a kategória "motel" hajtjuk végre egy `$filter` kifejezés, amely kiválasztja szállások az adott típusú. Amikor a felhasználó a "motel" gombra kattint, jelezve, hogy csak `$filter=category eq 'motel'`motelek jelenjenek meg, az alkalmazás által küldött következő lekérdezés tartalmazza a .

A következő kódrészlet hozzáadja a kategóriát a szűrőhöz, ha a felhasználó kiválaszt egy értéket a kategóriamezőből.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Ha a felhasználó egy gyűjteménymező , például `tags`a "készlet" értékére kattint, az alkalmazásnak a következő szűrőszintaxist kell használnia:`$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Tippek és kerülő megoldások

### <a name="initialize-a-page-with-facets-in-place"></a>Oldal inicializálása a helyén lévő lapokkal

Ha azt szeretnénk, hogy inicializálni egy oldalt a laptaránt, elküldheti a lekérdezés részeként az oldal inicializálása a mag az oldal egy kezdeti facet struktúra.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>A szűrt eredmények aszinkron módon megőrzi a mezőnavigációs struktúrát

Az Azure Cognitive Search ben az egyik kihívás a virtuális beviteli lehetőségek kel kapcsolatban, hogy csak az aktuális eredmények hez léteznek. A gyakorlatban gyakori, hogy megtartja a statikus lapkák at, hogy a felhasználó navigálhat fordított, nyomon követése lépéseket, hogy alternatív utakat a keresési tartalomon keresztül. 

Bár ez egy gyakori használati eset, ez nem valami a facet navigációs struktúra jelenleg biztosít out-of-the-box. Azok a fejlesztők, akik statikus részteteket szeretnének, általában két szűrt lekérdezés ek kiadásával kerülik meg a korlátozást: az egyik hatókörrel tartozik az eredményekhez, a másik pedig a navigációs célú laptatók statikus listájának létrehozásához.

## <a name="see-also"></a>Lásd még

+ [Szűrők az Azure Cognitive Search szolgáltatásban](search-filters.md)
+ [Index REST API létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Dokumentumok keresése – REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
