---
title: Keresési eredmények az Azure Search - index sorolására alkalmazott szűrők
description: Szűrheti a felhasználó biztonsági azonosítóját, nyelvi, földrajzihely- vagy numerikus értékek keresési eredményei között az Azure Search szolgáltatásban a Microsoft Azure-ban üzemeltetett felhőalapú keresési szolgáltatás lekérdezések csökkentése érdekében.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: d99196e231d122fcb0e707d30aed4d3b3eb2b89d
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310351"
---
# <a name="filters-in-azure-search"></a>Szűrők az Azure Search szolgáltatásban 

A *szűrő* kiválasztásakor egy Azure Search lekérdezésben használt dokumentumok szempontokat tartalmazza. Szűrés nélkül keresési az index összes dokumentum tartalmazza. Egy szűrőt egy részhalmazára dokumentumok keresési lekérdezés hatóköröket. Például egy szűrő sikerült korlátozza a teljes szöveges keresés csak az adott termék, egy adott márkájú vagy színes, ár időpontokban egy meghatározott küszöbérték feletti.

Néhány keresési funkciókat szűrő követelményeket írnak elő a megvalósítás részeként, de használhatja a szűrők korlátozhatja a keresés használatával megváltoztathatja *értékalapú* feltételek (hatókörkezelési keresése a termék típusa "könyvek" category" nem-fantasztikus"által közzétett"Simon és Schuster").

Ha inkább a cél bizonyos célzott keresést *struktúrák* (ügyfél-ellenőrzések mezőre hatókörkezelési keresése), alternatív módszert, az alábbiakban.

## <a name="when-to-use-a-filter"></a>Ha a szűrő

Szűrők több keresési funkciókat, beleértve a "Keresés a közelben", és a jellemzőalapú navigáció, biztonsági szűri, hogy csak ezeket a dokumentumokat a felhasználók számára engedélyezett lásd: az alapvető. Ha ezek a tapasztalatok bármelyike implementálásához szűrő megadása kötelező. A szűrőt a keresési lekérdezést, amely a földrajzi koordináták biztosít a felhasználó vagy a kérelmező biztonsági Azonosítóját a kiválasztott értékkorlátozás kategória csatolva.

Példaforgatókönyvek a következők:

1. Szűrő használatával szeletelheti az indexben lévő az index alapján. Adja meg egy séma a város, a lakáshelyzet típusa és az eszközök, létrehozhat egy szűrőt, hogy explicit módon válassza ki a dokumentumok, amelyek megfelelnek a feltételeknek (a budapesti, a condos, az i partszakasz). 

  A teljes szöveges keresés az ugyanazokkal a be gyakran a hasonló eredményt ad, de szűrő pontosabb abban, hogy pontos egyezés az indexben lévő tartalom szűrési kifejezés van szükség. 

2. Használjon szűrőt, ha a keresési funkciót tartalmaz, a szűrő követelmény:

 * [Jellemzőalapú navigáció](search-faceted-navigation.md) át vissza a felhasználó által kiválasztott értékkorlátozás kategória szűrőt használ.
 * Földrajzi keresés szűrőt használ a "Keresés a közelben" az aktuális hely koordinátái alkalmazások át. 
 * Biztonsági szűrők adja át a biztonsági azonosítók szűrőfeltételeket, mint ahol az egyezés az indexben a hozzáférési jogosultsága ahhoz, hogy a dokumentum-proxyként funkcionál.

3. Használjon szűrőt, ha azt szeretné, hogy a keresési feltételeknek, egy numerikus mezőben. 

  Numerikus mező lekérhető a dokumentum, és a keresési eredmények között szerepelhetnek, de ez nem kereshető (függvényében a teljes szöveges keresés) külön-külön. Ha kiválasztási feltételek numerikus adatokon alapuló, használjon szűrőt.

### <a name="alternative-methods-for-reducing-scope"></a>Alternatív módszerek az hatókör csökkentése

Ha azt szeretné, a keresési eredmények szűkítő hatása, szűrők nem lesznek az egyetlen lehetősége. Ezek az alternatívák jobban megfelel, attól függően, a cél lehet:

 + `searchFields` lekérdezési paraméter értékén rögzíti a search az egyes mezők. Például ha az index külön mezőket is tartalmazza az angol és spanyol leírások, használhatja searchFields, amelyekre a teljes szöveges keresés használandó mezőket. 

+ `$select` Adja meg az eredményt szerepeltetendő mezőket beállítása, hatékonyan a válasz-csonkolás mielőtt elküldené a hívó alkalmazás paraméter használható. Ez a paraméter nem pontosítsa a lekérdezést, vagy csökkentse a dokumentum egy dokumentumgyűjteményben, de a pontos válasz a cél az, ha ezt a paramétert, fontolja meg. 

Mindkét paraméter kapcsolatos további információkért lásd: [dokumentumok keresése > kérelem > lekérdezési paramétereket](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="filters-in-the-query-pipeline"></a>A lekérdezési folyamat szűrői

Lekérdezéskor egy szűrő elemző feltételeket fogad bemenetként, a kifejezés alakítja át atomi logikai kifejezésen és összeállít egy szűrő fa, amely ezután kiértékeli az indexben lévő szűrhető mezők fölé.  

Szűrés keresési, a feltételeknek megfelelő mely dokumentumokat, például az alsóbb feldolgozási a dokumentum beolvasásához és a relevancia alapján végzett kiértékelés előtt történik. A keresési karakterláncot párosítva a szűrő hatékonyan csökkenti a későbbi keresési műveletet felületének. Ha önállóan használható (például, ha a lekérdezési karakterlánc értéke üres where `search=*`), a szűrési feltételeket a rendszer egyetlen bemeneti. 

## <a name="filter-definition"></a>Szűrődefiníció

Szűrőket OData-kifejezések használatával csuklós egy [támogatott az Azure Search OData V4-szintaxis részhalmazát](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Megadhat egy szűrőt minden **keresési** műveletet, de maga a szűrő több mező, több feltételt is tartalmazhat, és ha egy **ismatch** funkció, több olyan kifejezés. A több részből álló szűrőkifejezés predikátumok bármilyen sorrendben is megadhat. Nincs észrevehető nyereség teljesítmény van, ha adott sorrendben predikátumok átrendezheti próbál.

A rögzített korlátját egy kifejezést a maximális korlátot, a kérésre. A kérelem teljes, amelynek részét alkotják az a szűrőt, legfeljebb 16 MB, a POST vagy GET 8 KB-os lehet. A szűrőkifejezés záradékai száma kapcsolható ideiglenes korlátok. Jó tapasztalatok, hogy ha több száz szolgáló szerződéses klauzulák, áll: a korlát veszélye. Azt javasoljuk, hogy az alkalmazás úgy, hogy azt nem generál korlátlan méretű szűrők tervezése.

A következő példák mutatják be prototípusos szűrő definíciók több API-k.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2017-11-11

# Option 2: Use filter for POST and pass it in the header
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2017-11-11
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

## <a name="filter-design-patterns"></a>Tervezési minták szűrése

Az alábbi példák bemutatják, számos tervezési minták a szűrő forgatókönyveket. További ötleteket találhat [OData-kifejezések szintaxisa > Példák](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples).

+ Önálló **$filter**, anélkül, hogy egy lekérdezési karakterláncot, akkor hasznos, ha a szűrőkifejezés viszont fontos dokumentumok teljes minősítéséhez. A lekérdezési karakterlánc nélkül nincs nincs lexikális vagy nyelvi elemzés, nincs pontozási és nincs ennek a területnek. Figyelje meg a keresési karakterlánc üres.

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Lekérdezési karakterlánc kombinációjának és **$filter**, amelyben a szűrő a következő részhalmazt hoz létre, és a lekérdezési karakterlánc kifejezés bemenetei biztosít a teljes szöveges keresés a szűrt részhalmazát keresztül. A leggyakrabban használt kód a minta egy szűrő használata a lekérdezési karakterlánc.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Összetett lekérdezéseket, elválasztott "vagy", mindegyik a saját szűrési feltételeket (például "beagles" a "kutya") vagy "siamese" a "cat". VAGY kellett kifejezések kiértékelése külön-külön, mindegyikhez egy választ küld vissza a hívó alkalmazás összesítve jelenik meg a választ. Ebben a kialakításban search.ismatch funkción keresztül érhető el. A nem pontozási (search.ismatch) vagy a pontozási verzióját (search.ismatchscoring) is használhatja.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

Nyomon követheti az alábbi cikkek átfogó útmutatást adott használati eseteket:

+ [Jellemzők szűrői](search-filters-facets.md)
+ [Nyelvi szűrők](search-filters-language.md)
+ [Biztonsági elrejtés](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Szűrés mező követelményei

A REST API-t a szűrhető a *a* alapértelmezés szerint. A szűrhető mezők növelhető a méret index; meg kell adni `filterable=FALSE` mező, amelyet nem szeretné, hogy a szűrő ténylegesen használ. Meződefiníciók beállításaival kapcsolatos további információkért lásd: [a Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

A .NET SDK-ban, a szűrhető van *ki* alapértelmezés szerint. Az API a szűrhető tulajdonság beállításához [IsFilterable](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). A set BaseRate mező meghatározására az alábbi példában.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="reindexing-requirements"></a>Újraindexelés követelmények

Ha egy mező nem szűrhető, és azt szeretné, hogy szűrhető legyen, akkor adjon hozzá egy új mezőt, és építse újra a meglévő mező. Egy mező definíció módosítása megváltoztatja az index fizikai struktúráját. Az Azure Search szolgáltatásban az összes engedélyezett elérési utak kibővítése a lekérdezési sebessége, ami szükségessé teszi a adatstruktúrák újjáépítést Meződefiníciók változásakor vannak indexelve. 

Egyes mezők újraépítése egy kis hatás művelet, csak egy küldi el a meglévő dokumentum kulcs és a hozzájuk társított értékeket az indexbe, minden egyes dokumentum további része érintetlenül merge műveletet igénylő lehet. Ha Újraépítés követelmény, tekintse meg az utasításokat az alábbi hivatkozásokat:

 + [Indexelési műveleteket a .NET SDK használatával](https://docs.microsoft.com/azure/search/search-import-data-dotnet#decide-which-indexing-action-to-use)
 + [Indexelési műveletet a REST API használatával](https://docs.microsoft.com/azure/search/search-import-data-rest-api#decide-which-indexing-action-to-use)

## <a name="text-filter-fundamentals"></a>Szöveges szűrő – alapok

Szövegszűrők érvényesek a karakterláncot tartalmazó mezők esetében, ahonnan lekérni az egyes dokumentumok keresési forrásgyűjteményébe belüli értékei alapján tetszőleges gyűjteménye.

Mikroszolgáltatásokból álló karakterláncok Szövegszűrők, az nincs lexikális elemzés vagy szavakra, ezért összehasonlítások csak pontos egyezések. Tegyük fel például, egy mezőt *f* tartalmazza a "sunny day"," `$filter=f eq 'Sunny'`nem egyezik, de `$filter=f eq 'Sunny day'` lesz. 

Karakterlánc csak kis-és nagybetűket. Nincs alsó – kis-és felső Case alakú szavakat van: `$filter=f eq 'Sunny day'` nem "napsütéses nap".


| A módszer | Leírás | 
|----------|-------------|
| [Search.in()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Egy függvényt, így egy adott mező karakterláncok vesszővel tagolt listája. A karakterláncok alkotják a szűrési feltételt, amely érvénybe lépnek minden mező a lekérdezés hatókörébe. <br/><br/>`search.in(f, ‘a, b, c’)` szemantikailag egyenértékűnek `f eq ‘a’ or f eq ‘b’ or f eq ‘c’`, azzal a különbséggel, hogy végrehajtása sokkal gyorsabb, ha az értékek nagy.<br/><br/>Javasoljuk, hogy a **javításával** működőképesek [biztonsági szűrők](search-security-trimming-for-azure-search.md) és a szűrők egyezést kell keresni egy adott mező a nyers szöveg tevődik össze. Ez a megközelítés sebesség lett tervezve. Több száz értékek ezer subsecond válaszideje várható. Bár nem explicit a függvénynek átadható elemek száma korlátozott, a késés növekedése adnia karakterláncok száma arányos. | 
| [Search.ismatch()](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Függvény, amely lehetővé teszi, hogy a teljes szöveges keresés műveleteit kizárólag logikai típusú szűrő az azonos szűrőkifejezésben kombinálhatók. Egyetlen kérelem több lekérdezésszűrő kombináció lehetővé teszi. Használhatja azt egy *tartalmaz* szűrőt a részleges karakterlánc nagyobb karakterláncon belül. |  
| [$filter = mező operátor karakterlánc](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search) | Egy felhasználó által definiált kifejezés tevődik össze mezőket, operátorokat és értékeket. | 

## <a name="numeric-filter-fundamentals"></a>Numerikus szűrő – alapok

Numerikus mező `searchable` teljes szöveges keresés kontextusában. Csak karakterláncok vonatkoznak rá a teljes szöveges keresés. Például 99,99 egy keresési kifejezést adja meg, ha nem kap vissza elemek $99,99 díjszabása. Ehelyett jelennének meg a számot 99 karakterlánc mezőket a dokumentum rendelkező elemek. Így ha a numerikus adatokat, feltételezzük, hogy használhatja ezeket a szűrőket, beleértve a tartományokat, értékkorlátozással, csoportok és így tovább. 

Numerikus mezők (ára, méret, SKU, azonosítója) tartalmazó dokumentumok adja meg ezeket az értékeket a keresési eredmények között, ha a négyzet be van jelölve `retrievable`. Itt az pont, hogy maga a teljes szöveges keresés nem alkalmazható a mező numerikus típusú.

## <a name="next-steps"></a>További lépések

Először próbálja meg **keresési ablak** a portálon, a lekérdezések elküldése **$filter** paramétereket. A [valós szűrőként mintaindex](search-get-started-portal.md) érdekes eredményeket biztosít a következő lekérdezések szűrve, amikor, illessze be őket a keresősávban:

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

További példák dolgozni, lásd: [OData szűrési kifejezés szintaxisa > Példák](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#bkmk_examples).

## <a name="see-also"></a>Lásd még

+ [Teljes szöveges keresés működése az Azure Search szolgáltatásban](search-lucene-query-architecture.md)
+ [REST API-val dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Támogatott adattípusok](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
