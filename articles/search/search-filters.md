---
title: Keresési eredmények az Azure Search - index sorolására alkalmazott szűrők
description: Szűrheti a felhasználó biztonsági azonosítóját, nyelvi, földrajzihely- vagy numerikus értékek keresési eredményei között az Azure Search szolgáltatásban a Microsoft Azure-ban üzemeltetett felhőalapú keresési szolgáltatás lekérdezések csökkentése érdekében.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 1871fee2734d347ff54d6aa70d90d1c28bd1f6f1
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597288"
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

+ `$select` Adja meg az eredményt szerepeltetendő mezőket beállítása, hatékonyan a válasz-csonkolás mielőtt elküldené a hívó alkalmazás paraméter használható. Ez a paraméter nem pontosítsa a lekérdezést, vagy csökkentse a dokumentum egy dokumentumgyűjteményben, de ha egy kisebb válasz a cél, ezt a paramétert, fontolja meg. 

Mindkét paraméter kapcsolatos további információkért lásd: [dokumentumok keresése > kérelem > lekérdezési paramétereket](https://docs.microsoft.com/rest/api/searchservice/search-documents#request).


## <a name="how-filters-are-executed"></a>Szűrők végrehajtásának módját

Lekérdezéskor egy szűrő elemző feltételeket fogad bemenetként, a kifejezés alakít át egy fa-kiszolgálókként atomi logikai kifejezésen és összeveti a szűrő-fa indexet a szűrhető mezők fölé.

Szűrés való kereséssel, mely dokumentumokat, például az alsóbb feldolgozási a dokumentum beolvasásához és a relevancia alapján végzett kiértékelés feltételeknek megfelelő párhuzamosan történik. A keresési karakterláncot párosítva a szűrő hatékonyan csökkenti a visszaírási készletét a későbbi keresési műveletet. Ha önállóan használható (például, ha a lekérdezési karakterlánc értéke üres where `search=*`), a szűrési feltételeket a rendszer egyetlen bemeneti. 

## <a name="defining-filters"></a>Szűrők megadása

Szűrőket OData-kifejezések használatával csuklós egy [támogatott az Azure Search OData V4-szintaxis részhalmazát](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search). 

Megadhat egy szűrőt minden **keresési** műveletet, de maga a szűrő több mező, több feltételt is tartalmazhat, és ha egy **ismatch** funkció, több teljes szöveges keresési kifejezések. A több részből álló szűrőkifejezés predikátumok (függvényében precedencia szabályainak) bármilyen sorrendben is megadhat. Nincs észrevehető nyereség teljesítmény van, ha adott sorrendben predikátumok átrendezheti próbál.

A szűrőkifejezés korlátait egyik, a kérés maximális méretkorlátot. A kérelem teljes, amelynek részét alkotják az a szűrőt, legfeljebb 16 MB, a POST vagy GET 8 KB-os lehet. A szűrőkifejezés záradékai száma korlátozva is van. Jó tapasztalatok, hogy ha több száz szolgáló szerződéses klauzulák, áll: a korlát veszélye. Azt javasoljuk, hogy az alkalmazás úgy, hogy azt nem generál korlátlan méretű szűrők tervezése.

A következő példák mutatják be prototípusos szűrő definíciók több API-k.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?search=*&$filter=baseRate lt 150&$select=hotelId,description&api-version=2019-05-06

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
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

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Használati minták szűrése

Az alábbi példák bemutatják, szűrő célokra többféle használati minták. További ötleteket találhat [OData-kifejezések szintaxisa > Példák](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

+ Önálló **$filter**, anélkül, hogy egy lekérdezési karakterláncot, akkor hasznos, ha a szűrőkifejezés viszont fontos dokumentumok teljes minősítéséhez. A lekérdezési karakterlánc nélkül nincs nincs lexikális vagy nyelvi elemzés, nincs pontozási és nincs ennek a területnek. Figyelje meg a keresési karakterlánc csak egy csillag, ami azt jelenti, hogy az "összes dokumentum egyezik".

   ```
   search=*&$filter=(baseRate ge 60 and baseRate lt 300) and accommodation eq 'Hotel' and city eq 'Nogales'
   ```

+ Lekérdezési karakterlánc kombinációjának és **$filter**, amelyben a szűrő a következő részhalmazt hoz létre, és a lekérdezési karakterlánc kifejezés bemenetei biztosít a teljes szöveges keresés a szűrt részhalmazát keresztül. Egy szűrőt a lekérdezési karakterlánc használata leggyakoribb használati módja.

   ```
   search=hotels ocean$filter=(baseRate ge 60 and baseRate lt 300) and city eq 'Los Angeles'
   ```

+ Összetett lekérdezéseket, elválasztott "vagy", mindegyik a saját szűrési feltételeket (például "beagles" a "kutya") vagy "siamese" a "cat". Kifejezésekre kombinált `or` külön-külön, a Uniója adja vissza a válaszként küldött mindegyik kifejezésnek megfelelő dokumentumok értékeli. Ez a használati minta a gazdafájlon keresztül, a `search.ismatchscoring` függvény. A pontozás verziója is használhatja `search.ismatch`.

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'description') or category eq 'Luxury'
   ```

  Úgy, hogy teljes szöveges keresés használatával lehetőség arra is `search.ismatchscoring` szűrők használatával `and` helyett `or`, ez funkcionális szempontból egyenértékű használatával azonban a `search` és `$filter` paramétereket egy keresési kérelmet. Például a következő két lekérdezést előállításához ugyanaz az eredmény:

  ```
  $filter=search.ismatchscoring('pool') and rating ge 4

  search=pool&$filter=rating ge 4
  ```

Nyomon követheti az alábbi cikkek átfogó útmutatást adott használati eseteket:

+ [Jellemzők szűrői](search-filters-facets.md)
+ [Nyelvi szűrők](search-filters-language.md)
+ [Biztonsági elrejtés](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>Szűrés mező követelményei

A REST API-t a szűrhető a *a* egyszerű mezők alapértelmezés szerint. A szűrhető mezők növelhető a méret index; meg kell adni `"filterable": false` mező, amelyet nem szeretné, hogy a szűrő ténylegesen használ. Meződefiníciók beállításaival kapcsolatos további információkért lásd: [a Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index).

A .NET SDK-ban, a szűrhető van *ki* alapértelmezés szerint. Meghatározhat egy mező szűrhető beállításával a [IsFilterable tulajdonság](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) , a megfelelő [mező](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) az objektum `true`. Azt is teheti deklaratív használatával a [IsFilterable attribútum](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute). Az alábbi példában az attribútum van beállítva a `BaseRate` egy modell osztály, amely az Indexdefiníció van leképezve.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Így szűrhető existující Pole

Meglévő mezők szűrhető, így nem módosítható. Ehelyett kell adjon hozzá egy új mezőt, vagy az index újraépítése. Az index újraépítése vagy mezők elszaporításával kapcsolatos további információkért lásd: [hogyan építse újra az Azure Search-index](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Szöveges szűrő – alapok

Szövegszűrők karakterlánc mezőket az Ön által megadott szűrő szövegkonstansok felel meg. Ellentétben a teljes szöveges keresés nincs lexikális elemzés vagy szavakra Szövegszűrők, ezért összehasonlítások csak pontos egyezések. Tegyük fel például, egy mezőt *f* tartalmazza a "sunny day"," `$filter=f eq 'Sunny'` nem egyezik, de `$filter=f eq 'sunny day'` lesz. 

Karakterlánc csak kis-és nagybetűket. Nincs alsó – kis-és felső Case alakú szavakat van: `$filter=f eq 'Sunny day'` nem találja a "sunny day".

### <a name="approaches-for-filtering-on-text"></a>Megközelítések a szöveg a szűréshez

| A módszer | Leírás | A következő esetekben használja | 
|----------|-------------|-------------|
| [search.in](query-odata-filter-orderby-syntax.md) | Függvény, amely megfelel egy karakterláncok tagolt listáját tartalmazó mezőt. | Ajánlott [biztonsági szűrők](search-security-trimming-for-azure-search.md) és a szűrőket. Ha a nyers szöveg sok értéket kell karakterlánc típusú egyeztetését. A **javításával** függvény sebesség lett tervezve, és sokkal gyorsabb az a mező minden karakterlánc tanúsítványokkal szemben explicit módon összehasonlítása `eq` és `or`. | 
| [search.ismatch](query-odata-filter-orderby-syntax.md) | Függvény, amely lehetővé teszi, hogy a teljes szöveges keresés műveleteit kizárólag logikai típusú szűrő az azonos szűrőkifejezésben kombinálhatók. | Használat **search.ismatch** (vagy pontozási egyenértékű **search.ismatchscoring**) Ha azt szeretné, hogy egy kérelem több keresési-szűrő kombináció. Használhatja azt egy *tartalmaz* szűrőt a részleges karakterlánc nagyobb karakterláncon belül. |
| [$filter = mező operátor karakterlánc](query-odata-filter-orderby-syntax.md) | Egy felhasználó által definiált kifejezés tevődik össze mezőket, operátorokat és értékeket. | Akkor használja, ha a keresett karakterlánc típusú és a egy karakterláncértéket között pontos egyezés. |

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

További példák dolgozni, lásd: [OData szűrési kifejezés szintaxisa > Példák](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search#filter-examples).

## <a name="see-also"></a>Lásd még

+ [Teljes szöveges keresés működése az Azure Search szolgáltatásban](search-lucene-query-architecture.md)
+ [REST API-val dokumentumok keresése](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Támogatott adattípusok](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
