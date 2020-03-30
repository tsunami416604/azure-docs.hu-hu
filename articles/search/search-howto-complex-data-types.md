---
title: Összetett adattípusok modellezése
titleSuffix: Azure Cognitive Search
description: A beágyazott vagy hierarchikus adatstruktúrák modellezhetők az Azure Cognitive Search indexében a ComplexType és a Collections adattípusok használatával.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 2edd62825de08becf22f2f953a63a7f89f55e0a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283055"
---
# <a name="how-to-model-complex-data-types-in-azure-cognitive-search"></a>Összetett adattípusok modellezése az Azure Cognitive Search szolgáltatásban

Az Azure Cognitive Search-index feltöltéséhez használt külső adatkészletek számos alakzatban kaphatók. Néha hierarchikus vagy beágyazott alstruktúrákat is tartalmaznak. Ilyen lehet például egy ügyfél több címe, egy termékváltozat több színe és mérete, egy könyv több szerzője és így tovább. Modellezési szempontból ezeket a struktúrákat *összetett,* *összetett,* *összetett*vagy *összesített* adattípusoknak nevezzük. Az Azure Cognitive Search kifejezés összetett **típus.** Az Azure Cognitive Search összetett típusai **összetett mezők**használatával vannak modellezve. Az összetett mező olyan mező, amely gyermekeket (almezőket) tartalmaz, amelyek bármilyen típusúak lehetnek, beleértve más összetett típusokat is. Ez hasonló módon működik, mint a strukturált adattípusok egy programozási nyelven.

Az összetett mezők az adattípustól függően a dokumentum egyetlen objektumát vagy egy objektumtömböt jelölnek. A típusú `Edm.ComplexType` mezők egyetlen objektumot `Collection(Edm.ComplexType)` jelölnek, míg a típusú mezők az objektumok tömbjeit jelölik.

Az Azure Cognitive Search natív módon támogatja az összetett típusokat és gyűjteményeket. Ezek a típusok lehetővé teszik, hogy az Azure Cognitive Search indexszinte bármely JSON-struktúráját modellezze. Az Azure Cognitive Search API-k korábbi verzióiban csak összeolvasztott sorkészletek importálhatók. A legújabb verzióban az index most már jobban megfelela forrásadatoknak. Más szóval, ha a forrásadatok összetett típusúak, az index nek is lehetnek összetett típusai.

Első lépésekhez javasoljuk a [Szállodák adatkészletet,](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md)amelyet betölthet az **Adatok importálása** varázslóban az Azure Portalon. A varázsló észleli a forrásban lévő összetett típusokat, és az észlelt struktúrákon alapuló indexsémát javasol.

> [!Note]
> Az összetett típusok támogatása `api-version=2019-05-06`általában a ban érhető el. 
>
> Ha a keresési megoldás egy gyűjteményben lévő összeolvasztott adatkészletek korábbi kerülő megoldásaira épül, módosítsa az indexet úgy, hogy a legújabb API-verzióban támogatott összetett típusokat tartalmazzon. Az API-verziók frissítéséről a [Frissítés a legújabb REST API-verzióra](search-api-migration.md) vagy a Frissítés a legújabb [.NET SDK verzióra című témakörben](search-dotnet-sdk-migration-version-9.md)talál további információt.

## <a name="example-of-a-complex-structure"></a>Példa összetett szerkezetre

A következő JSON-dokumentum egyszerű mezőkből és összetett mezőkből áll. Az összetett mezők, például `Address` az és `Rooms`a, almezőkkel rendelkeznek. `Address`egyetlen értékkészletet tartalmaz ezekhez az almezőkhöz, mivel ez egyetlen objektum a dokumentumban. Ezzel szemben `Rooms` az almezőkhöz több értékhalmaz tartozik, egyet a gyűjtemény minden objektumához.

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Description": "Ideally located on the main commercial artery of the city in the heart of New York.",
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY"
  },
  "Rooms": [
    {
      "Description": "Budget Room, 1 Queen Bed (Cityside)",
      "Type": "Budget Room",
      "BaseRate": 96.99
    },
    {
      "Description": "Deluxe Room, 2 Double Beds (City View)",
      "Type": "Deluxe Room",
      "BaseRate": 150.99
    },
  ]
}
```

## <a name="creating-complex-fields"></a>Összetett mezők létrehozása

Mint minden indexdefiníció, a portál, a [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)vagy a [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) használatával összetett típusokat tartalmazó sémát hozhat létre. 

A következő példa egy JSON indexsémát mutat be egyszerű mezőkkel, gyűjteményekkel és összetett típusokkal. Figyelje meg, hogy egy összetett típuson belül minden almezőnek van egy típusa, és lehetnek attribútumai, ahogy a legfelső szintű mezők is. A séma megfelel a fenti példaadatoknak. `Address`egy összetett mező, amely nem gyűjtemény (a szállodának egy címe van). `Rooms`egy komplex gyűjtőmező (a szálloda sok szobával rendelkezik).

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true }
      ]
    }
  ]
}
```

## <a name="updating-complex-fields"></a>Összetett mezők frissítése

Az általános mezőkre vonatkozó [újraindexelési szabályok](search-howto-reindex.md) mindegyike továbbra is összetett mezőkre vonatkozik. A fő szabályok közül néhány újbóli visszaállítása itt, a mező hozzáadása nem igényel index-újraépítést, de a legtöbb módosítás igen.

### <a name="structural-updates-to-the-definition"></a>A fogalommeghatározás strukturális frissítései

Az összetett mezőkhöz bármikor hozzáadhat új almezőket anélkül, hogy index-újraépítésre lenne szükség. Például a "ZipCode" `Address` hozzáadása a "Kényelemhez" vagy `Rooms` a "Kényelem" is megengedett, mint egy felső szintű mező hozzáadása az indexhez. A meglévő dokumentumok értéke az új mezőké, amíg az adatok frissítésével explicit módon fel nem kell őket nagyítani.

Figyelje meg, hogy egy összetett típuson belül minden almezőnek van egy típusa, és lehetnek attribútumai, ahogy a legfelső szintű mezők is.

### <a name="data-updates"></a>Adatfrissítések

Az indexben lévő dokumentumok `upload` frissítése a művelettel ugyanúgy működik az összetett és egyszerű mezők esetében – minden mező lecserélésre került. Azonban `merge` (vagy `mergeOrUpload` ha egy meglévő dokumentumra van alkalmazva) nem működik minden mezőben. Pontosabban `merge` nem támogatja az elemek egyesítését egy gyűjteményen belül. Ez a korlátozás primitív típusú és összetett gyűjtemények gyűjtemények létezik. Egy gyűjtemény frissítéséhez be kell olvasnia a teljes gyűjtemény értékét, módosításokat kell végrehajtania, majd az új gyűjteményt fel kell tüntetnie az Index API-kérelemben.

## <a name="searching-complex-fields"></a>Összetett mezők keresése

A szabad formátumú keresési kifejezések a várt módon működnek az összetett típusoknál. Ha a dokumentum bármely kereshető mezője vagy almezője megegyezik, akkor maga a dokumentum egyezik.

A lekérdezések árnyaltabbak lesznek, ha több kifejezésés operátor van, és egyes kifejezések mezőnevei meg vannak adva, ahogy a [Lucene szintaxislehetséges.](query-lucene-syntax.md) Ez a lekérdezés például két kifejezéssel ("Portland" és "OR" ) próbálja meg egyeztetni a Cím mező két almezőjét:

    search=Address/City:Portland AND Address/State:OR

Az ilyen lekérdezések *nem kapcsolódnak* a teljes szöveges kereséshez, ellentétben a szűrőkkel. A szűrőkben az összetett gyűjtemény almezőire vonatkozó lekérdezések a [ `any` `all`tartományváltozók ](search-query-odata-collection-operators.md)használatával korrelálnak a vagy a alkalmazásban. A fenti Lucene-lekérdezés a "Portland, Maine" és a "Portland, Oregon" (Portland, Oregon) és más oregoni városokat tartalmazó dokumentumokat ad vissza. Ez azért történik, mert minden egyes záradék a teljes dokumentum ban a területének minden értékére vonatkozik, így nincs fogalma az "aktuális aldokumentumnak". Erről további információt az [Azure Cognitive Search OData-gyűjteményszűrőinek ismertetése című témakörben talál.](search-query-understand-collection-filters.md)

## <a name="selecting-complex-fields"></a>Összetett mezők kijelölése

A `$select` paraméter segítségével választhatja ki, hogy mely mezőket adja vissza a keresési eredmények között. Ha ezt a paramétert szeretné használni egy összetett mező adott almezőinek kiválasztásához,`/`adja meg a szülőmezőt és az almezőt perjellel ( ).

    $select=HotelName, Address/City, Rooms/BaseRate

Ha szeretné, hogy a mezők megjelenjenek az indexben, a mezőket visszakereshetőként kell megjelölni, ha azt szeretné, hogy megjelenjenek a keresési eredmények között. Csak a visszakereshetőként megjelölt `$select` mezők használhatók az utasításban.

## <a name="filter-facet-and-sort-complex-fields"></a>Összetett mezők szűrése, aspektusaés rendezése

A szűréshez és a mezőalapú kereséshez használt [OData elérési út szintaxisa](query-odata-filter-orderby-syntax.md) is használható a keresési kérelem mezőinek kifejezészésére, rendezésére és kijelölésére. Összetett típusok esetén szabályok vonatkoznak arra, hogy mely almezők jelölhetők meg rendezhetőként vagy lapozhatóként. Ezekről a szabályokról további információt az [Index API-kézikönyv létrehozása című](/rest/api/searchservice/create-index)témakörben talál.

### <a name="faceting-sub-fields"></a>Részmezők kifejezésre vonása

Bármely almező megjelölhető lapra, kivéve, `Collection(Edm.GeographyPoint)`ha az vagy a típusa. `Edm.GeographyPoint`

A számmező-eredményekben visszaadott bizonylatok száma a szülődokumentumra (egy szállodára) lesz kiszámítva, nem pedig egy összetett gyűjtemény (szobák) aldokumentumaira. Tegyük fel például, hogy egy szállodában 20 "suite" típusú szoba található. Mivel ez a `facet=Rooms/Type`facet paraméter , a facet szám lesz az egyik a szálloda, nem 20 a szobákban.

### <a name="sorting-complex-fields"></a>Összetett mezők rendezése

A rendezési műveletek a dokumentumokra (szállodákra) vonatkoznak, az aldokumentumokra (Szobákra) nem. Ha összetett típusú gyűjteményed van, például a Szobák, fontos felismerni, hogy egyáltalán nem rendezhet szobákat. Valójában nem válogathat szanaszét egyetlen gyűjteményt sem.

A rendezési műveletek akkor működnek, ha a mezők dokumentumonként egyetlen értékkel rendelkeznek, függetlenül attól, hogy a mező egyszerű mező vagy összetett típusú almező. Például `Address/City` megengedett, hogy rendezhető, mert csak egy cím `$orderby=Address/City` egy szálloda, így rendezni szállodák város.

### <a name="filtering-on-complex-fields"></a>Szűrés összetett mezőkön

Szűrőkifejezésben összetett mezők almezőire hivatkozhat. Csak használja ugyanazt az [OData elérési út szintaxist,](query-odata-filter-orderby-syntax.md) amely a mezők kifejezészéséhez, rendezéséhez és kijelöléséhez használatos. A következő szűrő például az összes kanadai szállodát visszaadja:

    $filter=Address/Country eq 'Canada'

Összetett gyűjteménymező szűréséhez használhat **lambda kifejezést** a [ `any` és `all` operátorokkal.](search-query-odata-collection-operators.md) Ebben az esetben a lambda kifejezés **tartományváltozója** almezőkkel rendelkező objektum. Ezekre az almezőkre a szabványos OData elérési út szintaxissal hivatkozhat. Például a következő szűrő minden olyan szállodát visszaad, ahol legalább egy deluxe szoba és minden nemdohányzó szoba található:

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

A legfelső szintű egyszerű mezőkhöz ugyanúgy az összetett mezők egyszerű almezői csak akkor szerepelhetnek a szűrőkben, ha a **szűrőattribútum** az `true` indexdefinícióban van beállítva. További információt az [Index API létrehozása hivatkozáscímű témakörben talál.](/rest/api/searchservice/create-index)

## <a name="next-steps"></a>További lépések

Próbálja ki a [Szállodák adatkészletet](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) az **Adatok importálása** varázslóban. Az adatok eléréséhez szüksége lesz a Cosmos DB-kapcsolat információira a fájlban.

Ezzel az információval a kezében, az első lépés a varázsló egy új Azure Cosmos DB adatforrás létrehozása. A varázsló ban a célindex lap megnyitásakor megjelenik egy összetett típusú index. Hozza létre és töltse be ezt az indexet, majd hajtson végre lekérdezéseket az új struktúra megértéséhez.

> [!div class="nextstepaction"]
> [Gyorsútmutató: portálvarázsló importáláshoz, indexeléshez és lekérdezésekhez](search-get-started-portal.md)
