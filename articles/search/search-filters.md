---
title: Szűrés a keresési eredményekre
titleSuffix: Azure Cognitive Search
description: A Microsoft Azure üzemeltetett felhőalapú keresési szolgáltatásában, az Azure Cognitive Search szolgáltatásban található Azure Cognitive Search lekérdezések keresési eredményeinek csökkentéséhez szűrje a felhasználó biztonsági identitása, nyelve, földrajzi helye vagy numerikus értékei szerint.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 03333e853a2ab7606ebe60cc3f68bcb5facfbdb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191019"
---
# <a name="filters-in-azure-cognitive-search"></a>Szűrők az Azure Cognitive Search szolgáltatásban 

A *szűrő* az Azure Cognitive Search-lekérdezésben használt dokumentumok kiválasztásának feltétele. A szűretlen keresés az indexben lévő összes dokumentumot tartalmazza. A szűrő a keresési lekérdezést a dokumentumok egy részhalmazára sorakozni. Egy szűrő például korlátozhatja a teljes szöveges keresést csak azokra a termékekre, amelyek egy adott márkával vagy színnel rendelkeznek, egy bizonyos küszöbérték feletti árpontokon.

Egyes keresési élmények szűrőkövetelményeket írnak elő a megvalósítás részeként, de bármikor használhat szűrőket, amikor *értékalapú* feltételekkel korlátozni szeretné a keresést (a "Simon & Schuster által közzétett" "nem-fikciós" kategória "könyv" terméktípusára történő keresés hatóköre).

Ha ehelyett a cél a célzott keresés konkrét *adatstruktúrák* (hatókör-keresés egy ügyfél-értékelés mező), vannak alternatív módszerek, az alábbiakban ismertetjük.

## <a name="when-to-use-a-filter"></a>Mikor kell szűrőt használni?

A szűrők számos keresési élmény alapjai, például a "find near me", a faceted navigation és a biztonsági szűrők, amelyek csak azokat a dokumentumokat jelenítik meg, amelyeket a felhasználó csak azok nak láthat. Ha ezen élmények bármelyikét megvalósítja, szűrőre van szükség. A keresési lekérdezéshez csatolt szűrő biztosítja a földrajzi hely koordinátáit, a felhasználó által kiválasztott mezőkategóriát vagy a kérelmező biztonsági azonosítóját.

A példaforgatókönyvek a következők:

1. Szűrő használatával szeletelheti az indexet az indexben lévő adatértékek alapján. Adott séma a város, a ház típusa, és a kényelem, létrehozhat egy szűrőt, hogy kifejezetten válassza ki a dokumentumokat, amelyek megfelelnek a kritériumoknak (Seattle, öröklakás, vízparti). 

   Az azonos bemenetekkel végzett teljes szöveges keresés gyakran hasonló eredményeket eredményez, de a szűrő pontosabb, mivel a szűrőkifejezés pontos egyezését igényli az index tartalmával. 

2. Használjon szűrőt, ha a keresési élményhez szűrőkövetelmény tartozik:

   * [A köralapú navigáció](search-faceted-navigation.md) szűrővel adja vissza a felhasználó által kiválasztott facet kategóriát.
   * A földrajzi keresés szűrősegítségével adja át az aktuális hely koordinátáit a "find near me" alkalmazásokban. 
   * A biztonsági szűrők szűrőfeltételként adják át a biztonsági azonosítókat, ahol az indexben lévő egyezés a dokumentumhoz való hozzáférési jogok proxyjaként szolgál.

3. Használjon szűrőt, ha keresési feltételeket szeretne keresni egy numerikus mezőben. 

   A numerikus mezők visszakereshetők a dokumentumban, és megjelenhetnek a keresési eredmények között, de nem kereshetők (a teljes szöveges keresés függvényében). Ha numerikus adatokon alapuló kiválasztási feltételekre van szüksége, használjon szűrőt.

### <a name="alternative-methods-for-reducing-scope"></a>Alternatív módszerek a hatály csökkentésére

Ha szűkítő hatást szeretne elérni a keresési eredményekközött, nem csak a szűrők közül választhat. Ezek az alternatívák lehet jobban illeszkednek, attól függően, hogy a cél:

 + `searchFields`a lekérdezési paraméter adott mezőkre keres. Ha például az index külön mezőket biztosít az angol és a spanyol leíráshoz, a searchFields segítségével megcélozhatja, hogy mely mezőket használja a teljes szöveges kereséshez. 

+ `$select`a paraméter segítségével megadhatja, hogy mely mezőket kell szerepelni az eredményhalmazban, és hatékonyan vágja le a választ, mielőtt elküldené a hívó alkalmazásnak. Ez a paraméter nem finomítja a lekérdezést, és nem csökkenti a dokumentumgyűjteményt, de ha kisebb válasz a cél, akkor ez a paraméter egy lehetőség, hogy fontolja meg. 

A paraméterekről további információt a [Dokumentumok > kérelem > a lekérdezésparaméterei című témakörben talál.](/rest/api/searchservice/search-documents#query-parameters)


## <a name="how-filters-are-executed"></a>A szűrők végrehajtása

Lekérdezési időben a szűrőelemző elfogadja a feltételeket bemenetként, a kifejezést atomi logikai kifejezésekké alakítja, amelyek et fának jelölnek, majd kiértékeli a szűrőfát az index szűrhető mezői felett.

A szűrés a kereséssel párhuzamosan történik, és feljogosítja a dokumentum lekéréséhez és a relevancia-pontozáshoz a későbbi feldolgozásba bevonandó dokumentumokat. Ha egy keresési karakterlánccal párosítva a szűrő hatékonyan csökkenti a későbbi keresési művelet visszahívási készletét. Ha önmagában használja (például ha a `search=*`lekérdezési karakterlánc üres, ahol), a szűrő feltételek az egyetlen bemenet. 

## <a name="defining-filters"></a>Szűrők definiálása
A szűrők az Azure [Cognitive Search által támogatott OData V4 szintaxis részhalmazával](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)tagolt OData-kifejezések. 

Minden **keresési** művelethez megadhat egy szűrőt, de maga a szűrő több mezőt, több feltételt is tartalmazhat, és **ha ismatch** függvényt használ, több teljes szöveges keresési kifejezést is tartalmazhat. A többrészes szűrőkifejezésekben bármilyen sorrendben megadhatja a predikátumokat (az operátori elsőbbség szabályaitól függve). Nincs érzékelhető nyereség a teljesítményben, ha egy adott sorrendben próbálja átrendezni az előítéleteket.

A szűrőkifejezések egyik korlátozása a kérelem maximális méretkorlátja. A teljes kérelem, beleértve a szűrőt, legfeljebb 16 MB lehet a POST esetében, vagy 8 KB a GET esetében. A szűrőkifejezésben a záradékok száma is korlátozva van. Egy jó ökölszabály az, hogy ha több száz záradékot, akkor fennáll a veszélye, hogy fut be a határértéket. Azt javasoljuk, hogy úgy tervezze meg az alkalmazást, hogy az ne hozzon létre határtalan méretű szűrőket.

A következő példák prototipikus szűrődefiníciókat jelölnek több API-ban.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2019-05-06&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2019-05-06
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Használati minták szűrése

Az alábbi példák a szűrőforgatókönyvek több használati mintázatát mutatják be. További ötletek: [OData kifejezés szintaxis> példák](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples).

+ Önálló **$filter**, lekérdezési karakterlánc nélkül, akkor hasznos, ha a szűrőkifejezés képes teljes mértékben minősíteni az érdeklődésre számot tartó dokumentumokat. Lekérdezési karakterlánc nélkül nincs lexikális vagy nyelvi elemzés, nincs pontozás és nincs rangsorolás. Figyelje meg, hogy a keresési karakterlánc csak egy csillag, ami azt jelenti, hogy "egyezik az összes dokumentummal".

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ A lekérdezési karakterlánc és **a $filter**kombinációja, ahol a szűrő létrehozza a részhalmazt, és a lekérdezési karakterlánc biztosítja a kifejezésbemeneteket a szűrt részhalmaz teljes szöveges kereséséhez. A kifejezések (sétatávolságra mozik) hozzáadása bevezeti a keresési pontszámokat az eredményekben, ahol a feltételeknek leginkább megfelelő dokumentumok magasabbak. A leggyakoribb használati minta a lekérdezési karakterlánccal rendelkező szűrő használata.

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ Összetett lekérdezések, amelyeket "vagy" választanak el egymástól, és mindegyiknek saját szűrőkritériuma van (például "beagle" a "kutya" vagy "sziámi" a "macska" szövegben). A kifejezések `or` és a kifejezések egyenként kerülnek kiértékelésre, a válaszban visszaküldött minden egyes kifejezésnek megfelelő dokumentumok egyesítésével. Ez a használati minta `search.ismatchscoring` a funkción keresztül érhető el. Használhatja a nem pontozási verziót `search.ismatch`is, .

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  Lehetőség van a teljes szöveges keresés `search.ismatchscoring` kombinálására `and` is `or`a szűrőkkel a helyett, `search` `$filter` de ez funkcionálisan egyenértékű a keresési kérelem ben és a paraméterek használatával. A következő két lekérdezés például ugyanazt az eredményt adja:

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

Kövesse nyomon ezeket a cikkeket a konkrét felhasználási esetekre vonatkozó átfogó útmutatásért:

+ [Jellemzők szűrői](search-filters-facets.md)
+ [Nyelvi szűrők](search-filters-language.md)
+ [Biztonsági elrejtés](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>A szűrésre vonatkozó mezőkövetelmények

A REST API-ban a szűrőrendszer alapértelmezés szerint be van *kapcsolva* az egyszerű mezőkesetében. A szűrhető mezők növelik az index méretét; ügyeljen arra, `"filterable": false` hogy olyan mezőket állítson be, amelyeket nem kíván ténylegesen használni egy szűrőben. A meződefiníciók beállításairól az [Index létrehozása című](https://docs.microsoft.com/rest/api/searchservice/create-index)témakörben olvashat bővebben.

A .NET SDK-ban a szűrhető érték alapértelmezés szerint ki van *kapcsolva.* A mezőt úgy módosíthatja, hogy a megfelelő [Mezőobjektum](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) `true` [IsFilterable tulajdonságát](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) a értékre állítja. Ezt deklaratív módon is megteheti az [IsFilterable attribútum használatával.](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.isfilterableattribute) Az alábbi példában az attribútum egy `BaseRate` olyan modellosztály tulajdonságán van beállítva, amely leképezi az indexdefiníciót.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Meglévő mező szűrése

A meglévő mezők nem módosíthatók, hogy azok szűrhetőek legyenek. Ehelyett új mezőt kell hozzáadnia, vagy újra kell építenie az indexet. Az index újraépítéséről vagy a mezők újrafeltöltéséről az [Azure Cognitive Search-index újraépítése](search-howto-reindex.md)című témakörben olvashat bővebben.

## <a name="text-filter-fundamentals"></a>Szövegszűrő alapjai

A szövegszűrők a karakterláncmezőket a szűrőben megadott konstans karakterláncokkal egyeztetik. A teljes szöveges kereséssel ellentétben a szövegszűrőkesetében nincs lexikális elemzés vagy szótörés, így az összehasonlítások csak pontos egyezésekre szolgálnak. Tegyük fel például, hogy az `$filter=f eq 'Sunny'` *f* mező "napsütéses napot" tartalmaz, nem egyezik, hanem `$filter=f eq 'sunny day'` lesz. 

A szöveges karakterláncok ban a kis- és nagybetűk et is érzékenyen tartják. Nincs alsó burkolat a nagy betűs szavak: `$filter=f eq 'Sunny day'` nem fogja megtalálni a "napsütéses napot".

### <a name="approaches-for-filtering-on-text"></a>Szövegszűrési módszerek

| Módszer | Leírás | A következő esetekben használja |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Olyan függvény, amely egy mezőt a karakterláncok tagolt listájával egyeztet. | Biztonsági [szűrőkhöz](search-security-trimming-for-azure-search.md) és olyan szűrőkhöz ajánlott, ahol sok nyers szöveges értéket kell egykarakterlánc-mezővel egyeztetni. A **search.in** függvény traffipaxra van tervezve, és sokkal `or`gyorsabb, mint a mező kifejezett összehasonlítása az egyes karakterláncokkal és a használatával. `eq` | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Olyan függvény, amely lehetővé teszi a teljes szöveges keresési műveletek és a szigorúan logikai szűrőműveletek keverését ugyanabban a szűrőkifejezésben. | Használja **a search.ismatch** fájlt (vagy a pontozási megfelelőjét, **a search.ismatchscoring fájlt),** ha egy kérelemben több keresési szűrőkombinációt szeretne használni. Használhatja egy *tartalmaz* szűrőhöz is, hogy egy nagyobb karakterláncon belüli részleges karakterláncra szűrjön. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Mezőkből, operátorokból és értékekből álló, felhasználó által definiált kifejezés. | Ezt akkor használja, ha pontos egyezéseket szeretne találni egy karakterláncmező és egy karakterlánc-érték között. |

## <a name="numeric-filter-fundamentals"></a>Numerikus szűrő alapjai

A numerikus `searchable` mezők nincsenek a teljes szöveges keresés kontextusában. Csak a karakterláncok teljes szöveges keresésnek vannak kitéve. Ha például a 99,99-et adja meg keresési kifejezésként, akkor nem kapja vissza a 99,99 USD-t kiáras cikkeket. Ehelyett olyan elemeket láthatna, amelyek száma 99 a dokumentum karakterláncmezőiben. Így, ha numerikus adatokkal rendelkezik, a feltételezés az, hogy szűrőkhöz fogja használni őket, beleértve a tartományokat, a metszeteket, a csoportokat és így tovább. 

A numerikus mezőket (ár, méret, Termékváltozat, Azonosító) tartalmazó dokumentumok akkor `retrievable`adják meg ezeket az értékeket a keresési eredményekközött, ha a mező meg van jelölve. A lényeg itt az, hogy maga a teljes szöveges keresés nem alkalmazható numerikus mezőtípusokra.

## <a name="next-steps"></a>További lépések

Először próbálja meg a **Kereséskezelőt** a **portálon,** és küldjön $filter paraméterekkel rendelkező lekérdezéseket. Az [ingatlanminta-index](search-get-started-portal.md) érdekes eredményeket ad a következő szűrt lekérdezésekhez, amikor beilleszti őket a keresősávba:

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

További példákkal való kapcsolatról az [OData-szűrőkifejezés példái >.](https://docs.microsoft.com/azure/search/search-query-odata-filter#examples)

## <a name="see-also"></a>Lásd még

+ [A teljes szöveges keresés működése az Azure Cognitive Searchben](search-lucene-query-architecture.md)
+ [Dokumentumok keresése – REST API](https://docs.microsoft.com/rest/api/searchservice/search-documents)
+ [Egyszerű lekérdezési szintaxis](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene lekérdezés szintaxisa](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Támogatott adattípusok](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)
