---
title: Az Azure Search szűrők |} Microsoft Docs
description: Szűrés a felhasználó biztonsági azonosítóját, nyelvi, földrajzihely- vagy numerikus értékek lekérdezések az Azure Search, egy üzemeltetett felhőalapú keresőszolgáltatás, a Microsoft Azure keresési eredményeket csökkentése érdekében.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 10/19/2017
ms.author: heidist
ms.openlocfilehash: 82da742e6512e0acc8278a255c7e4e0516eaa8cb
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="filters-in-azure-search"></a>Szűrők az Azure Search szolgáltatásban 

A *szűrő* szempontokat tartalmazza az Azure Search lekérdezésben használt dokumentumok kijelöléséhez. Nem szűrt keresési az index összes dokumentum tartalmazza. A szűrő egy részének dokumentumok keresési lekérdezés hatóköröket. Például egy szűrő sikerült korlátozhatja a teljes szöveges keresés csak az adott termék, egy adott márkájú vagy színét, egyszer árlista egy meghatározott küszöbérték feletti.

Néhány keresési lép ugyanazok a szűrő követelményei végrehajtásának részeként, de használhatja a szűrők bármikor kívánt keresési használatával *érték-alapú* feltételek (tartalmazó keresse meg a termék típusa "könyvek" kategória" nem-példa"által közzétett"Simon & Schuster").

Ha inkább a, célzott keresés vonatkozó adatait a *struktúrák* (ügyfél-ellenőrzések mezőhöz tartalmazó keresési), alternatív módszert, az alábbiakban.

## <a name="when-to-use-a-filter"></a>Mikor érdemes használni a szűrő

Szűrők a következők eligazodást több keresési alkalmazásokban, beleértve a "Keresés közeli", jellemzőalapú navigáció és a biztonsági szűrők, hogy csak a dokumentumok a felhasználó engedélye van. Ezek az elemek egyikét sem alkalmazza, ha egy szűrőt kell. A szűrő a keresési lekérdezés, amely a földrajzi hely meghatározásának koordináták biztosít a felhasználó, vagy a kérelmező biztonsági Azonosítóját a kiválasztott dimenzió kategória csatolva.

Példaforgatókönyvek a következők:

1. Az index alapján a adatok értékkel szelet használjon szűrőt. Adott város, eszközök és lakáshelyzet típusú sémát, előfordulhat, hogy létrehozhat egy szűrőt, amely explicit módon válassza ki a dokumentumot, amely megfelel a feltételeknek (a budapesti, condos, partszakasz). 

  Teljes szöveges keresés az ugyanazon a forráson gyakran eredménye hasonló, de szűrő pontosabb abban, hogy pontos egyezés az indexben tartalom szűrő kifejezés van szükség. 

2. Szűrő használata, ha a keresési élményt szűrő követelmény tartalmaz:

 * [Jellemzőalapú navigációs](search-faceted-navigation.md) vissza felelt meg a felhasználó által kiválasztott dimenzió kategória szűrőt használja.
 * Földrajzi keresési szűrő felelt meg az aktuális hely "keresett közeli" koordináták alkalmazásokat használ. 
 * A biztonsági szűrők adja át a biztonsági azonosítók szűrési feltételeket, mint ahol egyezés az indexben a hozzáférési jogosultságot a dokumentumhoz proxyként funkcionál.

3. Szűrő használata, ha azt szeretné, hogy egy számmező a keresési feltételeket. 

  Numerikus mező lekérhető a dokumentumban, és jelenhet meg a keresési eredmények között, de nem kereshető (függvényében a teljes szöveges keresés) külön-külön. Ha módosítania kell a kiválasztási feltételek numerikus adatok alapján, a szűrő használata.

### <a name="alternative-methods-for-reducing-scope"></a>A hatókör csökkentésére alternatív módszerek

Ha egy szűkítő hatása a keresési eredmények között, szűrők a következők nem az egyetlen lehetősége. Másik módszert lehet felel meg leginkább, attól függően, hogy a cél:

 + `searchFields` lekérdezési paraméter értékén rögzíti a keresési bizonyos mezők. Például ha az index külön mezők biztosít angol és spanyol leírásokat, használhatja searchFields, amelyekre a teljes szöveges keresés használandó mezőket. 

+ `$select` paraméter használatával adja meg az eredményt mezők beállítása, hatékonyan díszítésre a válasz a hívó alkalmazás felé. Ez a paraméter nem pontosítsa a lekérdezést, vagy csökkentse a dokumentumgyűjteményt, de ha részletes válasz a cél, ezt a paramétert, fontolja meg. 

Vagy paraméterrel kapcsolatos további információkért lásd: [dokumentumok keresése > kérelem > lekérdezési paramétert](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="filters-in-the-query-pipeline"></a>A lekérdezés feldolgozási szűrők

Időben a szűrő elemző bemenetként feltételeket fogad el., konvertálja a kifejezés atomi logikai kifejezésen, és összeállít egy szűrő fa, amely majd kiértékelt index szűrhető mezők.  

Szűrés keresés a dokumentum beolvasásához alárendelt feldolgozási folyamatokat és relevanciájának pontozási foglalandó dokumentumok jogosult előtt következik be. Ha a keresési karakterláncot, a szűrő hatékonyan csökkenti az ezt követő keresési művelet felületének. Ha önállóan használható (például, ha a lekérdezési karakterlánc akkor üres, ha `search=*`), a szűrési feltételek nem az egyetlen bemeneti. 

## <a name="filter-definition"></a>Szűrődefiníció

Szűrők a következők OData kifejezések használatával csuklós egy [OData V4 szintaxis támogatott az Azure Search részhalmazát](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Egy szűrő megadhatja az egyes **keresési** műveletet, de maga a szűrő lehetnek több mező, több feltételt, és ha egy **ismatch** függvény, több. Egy több lépésből álló szűrőkifejezés predikátumok bármilyen sorrendben is megadhat. A teljesítmény nem érzékelhető nyereség van, ha adott sorrendben predikátumok átrendezését próbál meg.

A rögzített korlátját egy kifejezést a kérelem maximális korlátot. A szűrő, amelynek részét alkotják a teljes kérelemfeldolgozási legfeljebb 16 MB a FELADÁS egy vagy több, vagy a GET 8 KB lehet. Ideiglenes korlátoknak a szűrőkifejezést záradékok számú összefüggéseket. Jó tapasztalatok, ha több száz záradékok, áll kockázata, hogy a korlát rendszert futtató. Azt javasoljuk, hogy az alkalmazást úgy, hogy ez nem jelent szűrők unbounded méretének tervezésekor.

A következő példák mutatják be a több API-k prototypical szűrőkben megadott meghatározásoknak.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2016-09-01

# Option 2: Use filter for POST and pass it in the header
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2016-09-01
{
    "search": "*",
    "filter": "baseRate lt 150",
    "select": "hotelId,description"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "baseRate lt 150",
            Select = new[] { "hotelId", "description" }
        };

```

## <a name="filter-design-patterns"></a>Szűrő tervezési minták

A következő példák bemutatják tervminták több szűrő forgatókönyvek. További ötletek, lásd: [OData-kifejezésszintaxist > Példák](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples).

+ Önálló **$filter**, a lekérdezési karakterlánc, akkor hasznos, amikor a szűrőkifejezés képes teljes minősítéséhez az egyik fontos dokumentumok nélkül. A lekérdezési karakterlánc nélkül nincs nincs lexikális vagy nyelvi elemzés, nincs és nem rangsorolási. Figyelje meg, a keresési karakterláncot az üres.

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Lekérdezési karakterlánc kombinációja és **$filter**, ahol a szűrő hoz létre a részét, és a lekérdezési karakterlánc biztosítanak a kifejezés bemeneti adatokat a teljes szöveges keresés a szűrt részhalmaza. A leggyakoribb kódmintát a szűrő egy lekérdezési karakterlánc érték használata.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Összetett lekérdezések elválasztott "vagy", mindegyiket a saját szűrési feltételeket (például "beagles" a "kutya") vagy "siamese" a "cat". VAGY kellett kifejezések kiértékelése külön-külön választ, minden egyes egy egy választ küld vissza a hívó alkalmazás egyesítve. Ebben a kialakítási mintában a search.ismatch funkción keresztül valósul meg. A nem pontozási (search.ismatch) vagy a pontozási verziója (search.ismatchscoring) is használhatja.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

Nyomon követheti a cikkek átfogó útmutatást a konkrét használati esetek:

+ [Jellemzők szűrői](search-filters-facets.md)
+ [Nyelvi szűrők](search-filters-language.md)
+ [Biztonsági elrejtés](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Szűrés mező követelményei

A REST API-t a szűrhető a *a* alapértelmezés szerint. Szűrhető mezők növelje index méretét; meg kell adni `filterable=FALSE` használandó tényleges szűrő nem szereplő mezők. Mező definíciók beállításaival kapcsolatos további információkért lásd: [a Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

A .NET SDK-ban, a szűrhető van *ki* alapértelmezés szerint. Az API a szűrhető tulajdonság beállítása a van [IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). A készlet BaseRate mező meghatározására az alábbi példában.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="reindexing-requirements"></a>Újraindexelés követelmények

Ha egy mező nem szűrhető, és engedélyezni szeretné, szűrhető, hogy új mező hozzáadása, vagy építse újra a létező mező. Mező definíció módosítása megváltoztatja az index fizikai struktúráját. Az Azure Search görbékhez hozzáférése indexelt gyors lekérdezés sebességének, amely szükségessé teszi a adatstruktúrák egy újjáépítését mező definíciók módosításakor. 

Egyéni mezők újraépítése, lehet, hogy egy kis hatás műveletet, csak egy küldi el a meglévő dokumentum kulcs és a hozzájuk társított értékeket az indexbe, minden a dokumentumban érintetlenül egyesítési művelet igénylő. Ha rebuild követelmény, tekintse meg a további tájékoztatást a következő hivatkozások:

 + [A .NET SDK használatával indexelési műveletek](https://docs.microsoft.com/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use)
 + [A REST API használatával indexelési műveletek](https://docs.microsoft.com/azure/search/search-import-data-rest-api#decide-which-indexing-action-to-use)

## <a name="text-filter-fundamentals"></a>Szöveges szűrő – alapok

Szöveg szűrők érvényesek-e a karakterlánc típusú, amelyből el kívánja való lekérésére néhány keresési corpus belül értékek alapján dokumentumok tetszőleges gyűjteménye.

Álló karakterláncok szöveges szűrők nincs lexikális analysis vagy szóhatároló, így csak az összehasonlítást. Tegyük fel például, egy mező *f* "napfényes" tartalmaz `$filter=f eq 'Sunny'`nem egyezik, de `$filter=f eq 'Sunny day'` lesz. 

Karakterlánc csak kis-és nagybetűket. Nincs alsó-kis-és felső cased szó van: `$filter=f eq 'Sunny day'` nem fogják tudni "napfényes nyelvhez.


| Módszer | Leírás | 
|----------|-------------|
| [Search.in()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Egy függvény kezeléséről egy mező karakterláncok vesszővel tagolt listáját. A karakterláncok alkotják a szűrési feltételeket, a lekérdezés hatókörét minden mezőjére alkalmazott. <br/><br/>`search.in(f, ‘a, b, c’)` szemantikai szempontból egyenértékű `f eq ‘a’ or f eq ‘b’ or f eq ‘c’`, azzal a különbséggel, hogy sokkal gyorsabb végrehajtja az értékek listája nagy esetén.<br/><br/>Javasoljuk a **javításával** szolgáló funkciók [biztonsági szűrők](search-security-trimming-for-azure-search.md) és a szűrők álló nyers szöveg illeszkednie kell az adott mezőben található. Ez a megközelítés sebesség tervezték. Több ezer értékeket válaszideje subsecond számíthat. Bár nem explicit függvény átadhatók elemek száma korlátozott, a késés növekedése arányában megadta karakterláncok száma. | 
| [Search.ismatch()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Egy függvény, amely lehetővé teszi a teljes szöveges keresés műveleteit szigorúan logikai szűrő azonos szűrőkifejezésben található kombinálhatók. Ez lehetővé teszi több lekérdezés-szűrő kombináció egy kérelem. Használhatja az egy *tartalmaz* egy részleges karakterlánc nagyobb karakterláncon belüli szűrőt. |  
| [$filter = mező operátor karakterlánc](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | A felhasználó által definiált kifejezés mezők, operátorok és értékek állnak. | 

## <a name="numeric-filter-fundamentals"></a>Numerikus szűrő – alapok

A numerikus nincsenek `searchable` teljes szöveges keresés környezetében. Csak karakterláncok teljes szöveges keresés vonatkoznak. Például a kívánt keresőkifejezést, 99,99 megadásakor nem vissza $99,99 árú elemeket. Ehelyett elemek, amelyekhez a szám 99 karakterlánc mezőket a dokumentum mutatunk be. Így ha numerikus adatokat, a feltételezi, használt őket az szűrők, beleértve a tartományokat, értékkorlátozás, csoportok és így tovább. 

Numerikus mezők (ár, méret, SKU, azonosítója) tartalmazó dokumentumok ezeket az értékeket a találatok között adja meg, ha a mező jelölése `retrievable`. Itt az pont, maga a teljes szöveges keresés nincs numerikus típusú alkalmazandó.

## <a name="next-steps"></a>További lépések

Először próbálja meg **keresési ablak** tartalmazó lekérdezések elküldeni a portálon **$filter** paraméterek. A [valós-erőforrások-minta index](search-get-started-portal.md) érdekes eredményét mutatja az a következő lekérdezések szűrve, amikor Ön illessze be a keresési sávon:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

További példák dolgozni, lásd: [OData szűrő kifejezésszintaxist > Példák](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples).

## <a name="see-also"></a>Lásd még

+ [Hogyan teljes szöveges keresés az Azure Search működik](search-lucene-query-architecture.md)
+ [REST API-t dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Támogatott adattípusokat](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
