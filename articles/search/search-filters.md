---
title: Keresési eredmények szűrése
titleSuffix: Azure Cognitive Search
description: Szűrés felhasználói biztonsági identitás, nyelv, földrajzi hely vagy numerikus értékek alapján, amelyekkel csökkenthető a keresési eredmények az Azure Cognitive Search-on futó felhőalapú keresési szolgáltatásban Microsoft Azureeken.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 75932acb740eeff6f95180cf2eaa332ad0f5fb6a
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923075"
---
# <a name="filters-in-azure-cognitive-search"></a>Szűrők az Azure Cognitive Search 

A *szűrők* az Azure Cognitive Search-lekérdezésekben használt dokumentumok kiválasztására vonatkozó feltételeket biztosítanak. A szűretlen keresés az index összes dokumentumát tartalmazza. A szűrési hatókörök a keresési lekérdezések a dokumentumok egy részhalmazára vonatkoznak. Egy szűrő például korlátozhatja a teljes szöveges keresést arra, hogy csak az adott márkával vagy színnel rendelkező termékek legyenek egy adott küszöbérték felett.

Egyes keresési funkciók a megvalósítás részeként határozzák meg a szűrési követelményeket, de a szűrőket bármikor használhatja, ha az *érték-alapú feltételek alapján* szeretné korlátozni a keresést (a "könyvekben" a "Simon & Schuster" kategóriába tartozó "nem fikció" kategóriájú "könyvek" kifejezésre való kereséssel).

Ha a cél az adott *adatstruktúrákra* irányuló célzott keresés (egy ügyfél-visszajelzési mezőben való keresés), akkor az alább ismertetett alternatív módszerek érhetők el.

## <a name="when-to-use-a-filter"></a>Mikor kell szűrőt használni

A szűrők számos keresési élményhez, többek között a "keresés a közelben", a sokoldalú navigáláshoz és a csak azokat a dokumentumokat megjelenítő biztonsági szűrőkhöz használhatók, amelyeken a felhasználók számára engedélyezett a megtekintés. Ha a fentiekben ismertetett tapasztalatok valamelyikét alkalmazza, szűrőt kell megadnia. Ez a keresési lekérdezéshez csatolt szűrő, amely a térinformatikai koordinátákat, a felhasználó által kiválasztott aspektusi kategóriát vagy a kérelmező biztonsági AZONOSÍTÓját adja meg.

Például a következő forgatókönyvek tartoznak ide:

1. Egy szűrő használatával az indexet az indexben lévő adatértékek alapján darabolhatja. A várossal, a lakhatási típussal és a kényelemmel rendelkező séma alapján létrehozhat egy szűrőt, amellyel explicit módon kiválaszthatja azokat a dokumentumokat, amelyek megfelelnek a feltételeknek (Seattle, Condos, Waterfront). 

   Az azonos bemenetekkel rendelkező teljes szöveges keresés gyakran hasonló eredményeket eredményez, de a szűrő precízebb, ha pontosan egyeznie kell a szűrő kifejezéssel az index tartalmával szemben. 

2. Szűrő használata, ha a keresési élményhez egy szűrőre vonatkozó követelmény tartozik:

   * A [sokoldalú Navigálás](search-faceted-navigation.md) egy szűrő használatával adja vissza a felhasználó által kiválasztott aspektusi kategóriát.
   * A Geo-Search egy szűrő használatával továbbítja a jelenlegi hely koordinátáit a "keresés a közeljövőben" alkalmazásokban. 
   * A biztonsági szűrők a biztonsági azonosítókat szűrési feltételként adják át, ahol az index egyezése proxyként szolgál a dokumentumhoz való hozzáférési jogokhoz.

3. Használjon szűrőt, ha numerikus mezőben keresési feltételeket szeretne használni. 

   A numerikus mezők beolvashatók a dokumentumban, és megjelenhetnek a keresési eredmények között, de nem kereshetők (a teljes szöveges kereséstől függően). Ha numerikus adat alapján kell megadnia a kiválasztási feltételeket, használjon szűrőt.

### <a name="alternative-methods-for-reducing-scope"></a>Alternatív módszerek a hatókör csökkentéséhez

Ha szűkítő hatást szeretne a keresési eredményekre, a szűrők nem az Ön egyetlen választása. Ezek az alternatívák jobban illeszkednek az Ön céljától függően:

 + `searchFields` a lekérdezési paraméter az adott mezőkre keres. Ha például az index külön mezőket biztosít az angol és a spanyol leíráshoz, a searchFields segítségével megcélozhatja, hogy mely mezők használhatók a teljes szöveges kereséshez. 

+ `$select` a paraméter használatával határozható meg, hogy mely mezők szerepeljenek egy eredményhalmaz számára, és így a válasz hatékonyan vágható a hívó alkalmazásba való küldés előtt. Ez a paraméter nem pontosítja a lekérdezést, vagy nem csökkenti a dokumentum-gyűjteményt, de ha a cél kisebb válasz, ez a paraméter egy megfontolandó lehetőség. 

A paraméterekkel kapcsolatos további információkért lásd: [dokumentumok keresése > kérelem > lekérdezési paraméterek](/rest/api/searchservice/search-documents#query-parameters).


## <a name="how-filters-are-executed"></a>A szűrők végrehajtása

A lekérdezési időpontnál a szűrő-elemző a feltételeket bemenetként fogadja el, átalakítja a kifejezést egy faszerkezetként jelölt atomi logikai kifejezésre, majd kiértékeli a szűrő faszerkezetét egy index szűrt mezőin.

A szűrés párhuzamosan történik a kereséssel, amely feljogosítja, hogy a dokumentumok lekéréséhez és a relevancia pontozásához mely dokumentumokat kell felvenni az alárendelt feldolgozásba. Keresési karakterlánccal párosítva a szűrő hatékonyan csökkenti a későbbi keresési művelet visszahívási készletét. Ha egyedül használja (például ha a lekérdezési karakterlánc üres, ahol `search=*` ), a szűrési feltételek az egyetlen bemenetek. 

## <a name="defining-filters"></a>Szűrők definiálása
A szűrők OData kifejezések, amelyek az [Azure Cognitive Search által támogatott OData v4-szintaxist](/rest/api/searchservice/odata-expression-syntax-for-azure-search)használnak. 

Megadhat egy szűrőt az egyes **keresési** műveletekhez, de maga a szűrő több mezőt is tartalmazhat, több feltételt, és ha **ismatch** függvényt használ, több teljes szöveges keresési kifejezés is használható. A többrészes szűrési kifejezésekben bármely sorrendben megadhat predikátumokat (az operátori prioritás szabályainak megfelelően). Ha egy adott sorozatban megpróbál átrendezni predikátumokat, a teljesítmény nem észlelhető.

A szűrési kifejezés egyik korlátja a kérelem maximális mérete. A teljes kérelem, amely tartalmazza a szűrőt, legfeljebb 16 MB lehet a POST számára, vagy 8 KB a GET értékhez. A szűrési kifejezésben a záradékok száma is korlátozott. A jó ökölszabály az, hogy ha több száz záradékkal rendelkezik, akkor fennáll a lehetősége, hogy a korláton fut. Javasoljuk, hogy az alkalmazást úgy tervezze meg, hogy ne állítson be nem kötött méretű szűrőket.

Az alábbi példák több API-ban prototípusos-szűrési definíciókat tartalmaznak.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2020-06-30&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2020-06-30
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

Az alábbi példák több használati mintát mutatnak be a szűrési forgatókönyvek esetében. További ötletek: OData- [kifejezés szintaxisa > példák](./search-query-odata-filter.md#examples).

+ Önálló **$Filter**lekérdezési karakterlánc nélkül, akkor hasznos, ha a szűrő kifejezés képes teljes mértékben minősíteni a dokumentumokat. A lekérdezési karakterláncok nélkül nincs lexikális vagy nyelvi elemzés, nincs pontozás vagy rangsorolás. Figyelje meg, hogy a keresési sztring csak egy csillag, ami az összes dokumentum egyezését jelenti.

   ```
   search=*&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu'
   ```

+ A lekérdezési karakterlánc és a **$Filter**kombinációja, ahol a szűrő létrehozza a részhalmazt, és a lekérdezési karakterlánc a teljes szöveges keresésre szolgáló bemeneteket biztosít a szűrt részhalmazon. A használati feltételek (Gyalogolási távolsági színházak) bemutatják a keresési pontszámokat az eredmények között, ahol a feltételeknek leginkább megfelelő dokumentumok rangsorolása magasabb. Egy lekérdezési sztringet tartalmazó szűrő használata a leggyakoribb használati minta.

   ```
  search=walking distance theaters&$filter=Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'&$count=true
   ```

+ A "vagy" karakterrel elválasztott összetett lekérdezések, amelyek mindegyike saját szűrési feltételekkel rendelkezik (például "Beagle" a "Dog" vagy "Sziámi" a "Cat"-ben). A összevont kifejezések `or` kiértékelése külön történik, és a válaszban visszaküldött összes kifejezésnek megfelelő dokumentumok Uniója. Ez a használati minta a függvényen keresztül érhető el `search.ismatchscoring` . A nem pontozási verziót is használhatja `search.ismatch` .

   ```
   # Match on hostels rated higher than 4 OR 5-star motels.
   $filter=search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

   # Match on 'luxury' or 'high-end' in the description field OR on category exactly equal to 'Luxury'.
   $filter=search.ismatchscoring('luxury | high-end', 'Description') or Category eq 'Luxury'&$count=true
   ```

  Az is lehetséges, hogy a teljes szöveges keresést a szűrők használatával is kombinálhatja a `search.ismatchscoring` `and` helyett `or` , de ez a funkció egyenértékű a `search` `$filter` keresési kérelemben szereplő és paraméterek használatával. Például a következő két lekérdezés ugyanazt az eredményt eredményezi:

  ```
  $filter=search.ismatchscoring('pool') and Rating ge 4

  search=pool&$filter=Rating ge 4
  ```

Az alábbi cikkekben részletes útmutatást talál az egyes használati esetekhez:

+ [Aspektus szűrői](search-filters-facets.md)
+ [Nyelvi szűrők](search-filters-language.md)
+ [Biztonsági elrejtés](search-security-trimming-for-azure-search.md) 

## <a name="field-requirements-for-filtering"></a>A szűréshez szükséges mezők

A REST APIban az egyszerű mezők esetében a szűrhető beállítás alapértelmezés szerint be van *kapcsolva* . Szűrhető mezők: az index mérete növekszik; Ügyeljen arra, hogy `"filterable": false` a szűrőben ténylegesen használni kívánt mezőkhöz legyen beállítva. További információ a mezőértékek beállításairól: [create index](/rest/api/searchservice/create-index).

A .NET SDK-ban a szűrhetőség alapértelmezés szerint *ki van kapcsolva* . A mező szűrhető úgy, hogy a megfelelő [mező](/dotnet/api/microsoft.azure.search.models.field?view=azure-dotnet) objektum [IsFilterable tulajdonságát](/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet) a értékre állítja `true` . Ezt a deklaratív [IsFilterable attribútum](/dotnet/api/microsoft.azure.search.isfilterableattribute)használatával is végrehajthatja. Az alábbi példában az attribútum `BaseRate` egy olyan modell osztály tulajdonságára van beállítva, amely az index definícióját képezi le.

```csharp
    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Meglévő mező szűrhetővé tétele

A meglévő mezők nem módosíthatók úgy, hogy szűrhetők legyenek. Ehelyett új mezőt kell felvennie, vagy újra kell építenie az indexet. Az indexek újraépítésével vagy a mezők újrafeltöltésével kapcsolatos további információkért lásd: [Azure Cognitive Search index](search-howto-reindex.md)újraépítése.

## <a name="text-filter-fundamentals"></a>A szöveg szűrő alapjai

A szöveges szűrők a szűrőben megadott literális karakterláncok alapján egyeznek meg a karakterlánc mezőivel. A teljes szöveges kereséstől eltérően a szöveges szűrők nem rendelkeznek lexikális analízissel vagy sortöréssel, így az összehasonlítások csak a pontos egyezésekre vonatkoznak. Tegyük fel például, hogy az *f* mező a "Sunny Day" kifejezést tartalmazza, `$filter=f eq 'Sunny'` nem egyezik, de a következő `$filter=f eq 'sunny day'` lesz:. 

A szöveges karakterlánc megkülönbözteti a kis-és nagybetűket. A felső rétegbeli szavak nem rendelkeznek alsó borítással: a `$filter=f eq 'Sunny day'` "Sunny Day" nem található.

### <a name="approaches-for-filtering-on-text"></a>A szöveg szűrésének módszerei

| Módszer | Leírás | A következő esetekben használja |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | Függvény, amely egy mezőnek felel meg a karakterláncok tagolt listáján. | Ajánlott [biztonsági szűrőkhöz](search-security-trimming-for-azure-search.md) és olyan szűrőkhöz, amelyekben sok nyers szöveges értéket kell összeegyeztetni egy sztring mezővel. A **Search.in** függvény sebességre lett tervezve, és sokkal gyorsabb, mint a mező explicit módon való összehasonlítása az egyes sztringekkel a és a használatával `eq` `or` . | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | Függvény, amely lehetővé teszi, hogy a teljes szöveges keresési műveleteket szigorúan logikai szűrési műveletekkel keverje ugyanabban a szűrő kifejezésben. | Használja a **Search. ismatch** (vagy annak pontozási egyenértéke, **Search. ismatchscoring**) kifejezést, ha egy kérelemben több keresési kombinációt szeretne használni. Azt is megteheti, hogy egy olyan szűrőt *tartalmaz* , amely egy nagyobb sztringen belüli részleges karakterláncot szűr. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Mezőkből, operátorokból és értékből álló, felhasználó által definiált kifejezés. | Akkor használja ezt a lehetőséget, ha pontos egyezést szeretne találni egy karakterlánc-mező és egy karakterlánc-érték között. |

## <a name="numeric-filter-fundamentals"></a>Numerikus szűrők alapjai

A numerikus mezők nincsenek `searchable` a teljes szöveges keresés kontextusában. Csak karakterláncok tartoznak a teljes szöveges keresésre. Ha például a 99,99 értéket adja meg keresési kifejezésként, akkor nem fog visszakerülni a $99,99-es díjszabású elemekbe. Ehelyett a dokumentum sztring mezőiben szereplő, 99 számú elemek jelennek meg. Így ha numerikus adattal rendelkezik, feltételezi, hogy a szűrőket, például a tartományokat, a dimenziókat, a csoportokat és így tovább használni fogja. 

A numerikus mezőket (ár, méret, SKU, ID) tartalmazó dokumentumok a keresési eredményekben adják meg ezeket az értékeket, ha a mező meg van jelölve `retrievable` . Itt az a pont, hogy a teljes szöveges keresés önmagában nem vonatkozik a numerikus mezők típusára.

## <a name="next-steps"></a>További lépések

Először a portálon keresse meg a **keresési Explorert** , hogy **$Filter** paraméterekkel küldje el a lekérdezéseket. A [Real-Estate-Sample index](search-get-started-portal.md) érdekes eredményeket biztosít a következő szűrt lekérdezésekhez, amikor beilleszti őket a keresősávba:

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

További Példákért lásd: [OData szűrési kifejezés szintaxisa > példák](./search-query-odata-filter.md#examples).

## <a name="see-also"></a>Lásd még

+ [A teljes szöveges keresés működése az Azure Cognitive Searchben](search-lucene-query-architecture.md)
+ [Dokumentumok keresése – REST API](/rest/api/searchservice/search-documents)
+ [Egyszerű lekérdezési szintaxis](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene lekérdezési szintaxis](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Támogatott adattípusok](/rest/api/searchservice/supported-data-types)