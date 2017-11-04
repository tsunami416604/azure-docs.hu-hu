---
title: "Az Azure Search értékkorlátozás szűrők |} Microsoft Docs"
description: "Szűrési feltételek felhasználó biztonsági azonosítóját, nyelvi, földrajzihely-vagy numerikus értékek lekérdezések az Azure Search, egy üzemeltetett felhőalapú keresőszolgáltatás, a Microsoft Azure keresési eredményeket csökkentése érdekében."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/13/2017
ms.author: heidist
ms.openlocfilehash: 5b4d88cb9c9662fe45de8c11534232a2905cf5a4
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="how-to-build-a-facet-filter-in-azure-search"></a>Az Azure Search értékkorlátozás szűrő létrehozása 

Jellemzőalapú navigációs szolgál a lekérdezési eredmények keresési alkalmazásban, ahol az alkalmazás felhasználói felületi vezérlők csoportokhoz dokumentumok (például a kategóriák és márkákat) tartalmazó Search kínál, és Azure Search biztosít a biztonsági adatstruktúra irányuló szűréséhez a felhasználói élmény. Ebben a cikkben tekintse át gyorsan az alapvető lépéseken, egy biztonsági a keresési élményt szeretne biztosítani jellemzőalapú navigációs szerkezetben létrehozásához. 

> [!div class="checklist"]
> * Szűrés és értékkorlátozás mezők kiválasztása
> * A mező attribútumainak beállítása
> * Az index és az adatok betöltése összeállítása
> * A lekérdezés értékkorlátozás szűrők hozzáadása
> * Eredmények kezelése

Értékkorlátozás dinamikus és lekérdezés visszaadott. Keresési válaszok velük kapcsolja a értékkorlátozó kategóriákat használatával keresse meg az eredményeket. Ha nem ismeri a Decimal típushoz, a következő példa olyan bemutatásáért jellemzőalapú navigációs szerkezetben.

  ![](./media/search-filters/facet-nav.png)

Most ismerkedik jellemzőalapú navigációs és a kívánt több részletességi? Lásd: [jellemzőalapú navigációs megvalósítható az Azure Search](search-faceted-navigation.md).

## <a name="choose-fields"></a>Mezők kiválasztása

Egyetlen értéket mezők, valamint a gyűjtemények keresztül értékkorlátozás kerülhet sor. Mezőket, amelyeknek a legmegfelelőbb jellemzőalapú navigációs rendelkezik alacsony számossága: kevés eltérő érték, amely ismételje meg a keresési corpus (például listája színek, országokban vagy védjegyek) a dokumentumok teljes. 

Értékkorlátozás mező által alapon engedélyezve van, az index létrehozásakor a következő attribútum TRUE értékre állításával: `filterable`, `facetable`. Csak a szűrhető mezők értéke korlátozható.

Bármely [mező típusa](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) , amelyek esetleg használható lenne a jellemzőalapú navigáció "kategorizálható" jelölésű:

+ Edm.String
+ Edm.DateTimeOffset
+ Edm.Boolean
+ Edm.Collections (lásd: [értékkorlátozás összetett adattípusú hogyan](#facet-complex-fields) című cikkben.)
+ Numerikus típusú: Edm.Int32, Edm.Int64, Edm.Double

Nem használhatja a Edm.GeographyPoint jellemzőalapú navigáció. Értékkorlátozás emberi olvasható szöveget és számokat össze. Ilyen értékkorlátozás nem támogatottak a földrajzi koordináta. Egy hely szerint értékkorlátozás vagy régiót mezőt kell.

## <a name="set-attributes"></a>Attribútumainak beállítása

Az index egyedi mező definíciói mező felhasznált szabályozó indexattribútumok kerülnek. A következő példában kulcsattribútumával alacsony, értékkorlátozás, hasznos mezők alkotják: kategória (Szálloda, motel, hostel), eszközök és értékeléseket. 

A .NET API-t, a szűrő attribútumok állítható be explicit módon kell. A REST API-ban, értékkorlátozás, szűrésével, és alapértelmezés szerint engedélyezve vannak, ami azt jelenti, hogy csak kell explicit módon beállítva az attribútumokat, ha meg szeretné kikapcsolni őket. Bár nem kötelező technikailag, megmutatjuk, az adatok az alábbi többi példában oktatási célokra. 

> [!Tip]
> Ajánlott eljárásként a teljesítmény- és tárolási optimalizálása kikapcsolása értékkorlátozás soha nem használható mint egy dimenzió mezőket. Különösen a singleton értékek, például az azonosító vagy a termék nevét, a karakterlánc típusú meg "Kategorizálható": hamis megelőzése érdekében a véletlen (és hatástalan) használja a jellemzőalapú navigáció.


```http
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String", "filterable": true, "facetable": true},
        {"name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true},
        {"name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

> [!Note]
> Az index definícióját a rendszer átmásolja az [létrehozása az Azure Search-index REST API használatával](https://docs.microsoft.com/azure/search/search-create-index-rest-api). A mező felületi különbséget kivételével azonos legyen. Szűrhető és kategorizálható attribútumok explicit módon kerülnek a kategóriát, címkék, parkingIncluded, smokingAllowed és minősítési mezők. A gyakorlatban szűrhető kap, és a kategorizálható szabad Edm.String Edm.Boolean és Edm.Int32 mező típusokon. 

## <a name="build-and-load-an-index"></a>Build és betölteni egy indexet

Egy közbenső (és esetleg nyilvánvaló) lépés, hogy kell [felépítéséhez és az index feltöltése](https://docs.microsoft.com/azure/search/search-create-index-dotnet#create-the-index) mielőtt egy lekérdezésben. Ebben a lépésben Itt a teljesség megemlíteni azt. Annak meghatározásához, hogy rendelkezésre áll-e az index egy úgy, hogy az indexek lista ellenőrzés a [portal](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>A lekérdezés értékkorlátozás szűrők hozzáadása

Az alkalmazás kódjában, amely meghatározza egy érvényes lekérdezés, beleértve a keresési kifejezéseket, értékkorlátozás, szűrők, pontozási profilok – semmit állítson össze egy kérelem használt minden részén lekérdezést készíteni. Az alábbi példa épít fel a kérelmeket, amelyek értékkorlátozás navigációs vendéglátóipar, besorolásával és egyéb készülékek típusa alapján hoz létre.

```csharp
SearchParameters sp = new SearchParameters()
{
  ...
  // Add facets
  Facets = new List<String>() { "category", "rating", "parkingIncluded", "smokingAllowed" },
};
```

### <a name="return-filtered-results-on-click-events"></a>A szűrt eredménye kattintson az események

A szűrőkifejezés kezeli az kattintson eseményt értékkorlátozás értéke. Egy kategória dimenzió megadott, kattintson a "motel" kategória keresztül történik egy `$filter` , ami az adott típusú szállások kifejezés. Amikor egy felhasználó "motelek" annak jelzésére, hogy megjelenjenek-e csak motelek kattint, a következő lekérdezés az alkalmazás tartalmazza-e a $filter = kategória eq 'motelek".

A következő kódrészletet, ha a felhasználó kiválaszt egy értéket a kategória dimenzió hozzáadja a szűrő kategóriát.

```csharp
if (categoryFacet != "")
  filter = "category eq '" + categoryFacet + "'";
```
A REST API használatával, a kérelem kellene lennie csuklós `$filter=category eq 'c1'`. Ahhoz, hogy a kategória több érték mező, használja a következő szintaxist:`$filter=category/any(c: c eq 'c1')`

## <a name="tips-and-workarounds"></a>Tippek és a lehetséges megoldások

### <a name="initialize-a-page-with-facets-in-place"></a>Egy helyen megnyilvánulása lap inicializálása

Ha azt szeretné, egy helyen megnyilvánulása lap inicializálása, a lekérdezés lap inicializálási rendezi a lap egy kezdeti értékkorlátozás struktúrával részeként is elküldheti.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Aszinkron módon szűrt eredményeinek jellemzőalapú navigációs szerkezetben megőrzése

A dimenzió navigációs az Azure Search kihívásai egyike, hogy létezik-e értékkorlátozás a csak az aktuális eredmény. A gyakorlatban esetében gyakori, rögzített értékkorlátozást megőrizni, hogy a felhasználó navigálhatnak visszafelé, Visszalépés az alternatív elérési utak keresztül keresés felfedezése lépéseket. 

Bár ez egy gyakori használati eset, nincs valamit a jellemzőalapú navigációs szerkezetben out-of-az-box tartalmaz. A fejlesztők számára statikus értékkorlátozás általában két szűrt lekérdezések kiállításával áthidalni a korlátozást: egyet az eredmények hatókörét, a másik navigációs célokra értékkorlátozás statikus listájának létrehozásához használt.

## <a name="see-also"></a>Lásd még:

+ [Az Azure Search szűrők](search-filters.md)
+ [Index REST API-t létrehozni](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [REST API-t dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents)

