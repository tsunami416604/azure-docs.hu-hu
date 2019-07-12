---
title: Összetett adattípusok modellezése – Azure Search hogyan
description: Beágyazott vagy hierarchikus datové struktury modellezhető az Azure Search-index typu ComplexType és gyűjtemények adattípusok használatával.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
tags: complex data types; compound data types; aggregate data types
services: search
ms.service: search
ms.topic: conceptual
ms.date: 06/13/2019
ms.custom: seodec2018
ms.openlocfilehash: e7e6ddefd13d669c949389bc4fad85fb6cff4d3a
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621362"
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Összetett adattípusok modellezése az Azure Search hogyan

Azure Search-index feltöltéséhez használt külső adathalmazok számos alakzatok kerülhet. Egyes esetekben a hierarchikus vagy beágyazott alépítményeit tartalmazzák. Például előfordulhat, hogy több cím tartalmazza az egyetlen ügyfél több színt és méretet egyetlen termékváltozat, egy könyv, több készítői és így tovább. Feltételek modellező, láthatja a továbbiakban ezen szerkezetek *összetett*, *összetett*, *összetett*, vagy *összesített* adattípusokat. A kifejezés a fogalom használja az Azure Search **komplex típus**. Az Azure Search szolgáltatásban komplex típusok vannak modellezve használatával **összetett mezők**. Egy összetett mező egy mezőt, amely bármilyen típusú adatot, beleértve a más komplex típusú lehet (almező) gyermekeket tartalmaz. Ez a módszer hasonló módon strukturált adattípusúként programozási nyelven.

Összetett mezők jelölik, a dokumentum egyetlen objektumot, vagy egy objektumtömb, az adatok típusától függően. Típusú mezők `Edm.ComplexType` egyetlen objektumok, miközben típusú mezők `Collection(Edm.ComplexType)` Pole binárních hodnot objektumokat képviseli.

Az Azure Search natív módon támogatja a komplex típusok és gyűjteményeket. Ezek a típusok lehetővé teszik az Azure Search-index szinte bármilyen JSON struktúrában modell. Az Azure Search API-k korábbi verziói esetén csak egybesimított sor csoportok importálása sikerült. A legújabb verzióban az index már jobban felelhet meg forrásadatokat. Más szóval ha a forrásadatok komplex típusok, az index lehet komplex típusok is.

Első lépésként javasoljuk, hogy a [Hotels adatkészlet](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md), amelyek betöltése a a **adatok importálása** varázsló az Azure Portalon. A varázsló észleli a komplex típusok a forrás, és az indexsémát az észlelt struktúrák alapján javasol.

> [!Note]
> Összetett típusok támogatása az általánosan elérhető a `api-version=2019-05-06`. 
>
> Ha a keresési megoldás egy gyűjtemény egybesimított adatkészletek korábbi megoldások épül, akkor módosítani kell az index összetett típusokat tartalmazza a legújabb API-verzióban támogatott. API-verziók frissítésével kapcsolatos további információkért lásd: [frissítsen a legújabb REST API-verzióra](search-api-migration.md) vagy [frissítsen a legújabb .NET SDK-verzióra](search-dotnet-sdk-migration-version-9.md).

## <a name="example-of-a-complex-structure"></a>Egy összetett struktúra – példa

A következő JSON-dokumentum egyszerű és összetett mezőket tevődik össze. Komplex mezők, például `Address` és `Rooms`, alárendelt mezővel rendelkezik. `Address` az adott alárendelt mezők értékei egyetlen készletével rendelkezik, mivel ez a dokumentum egyetlen objektumot. Ezzel szemben a `Rooms` több példányban alárendelt mezőinek értékeit, és az egyik minden objektum rendelkezik a gyűjteményben.

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

Bármely index definíciója és annak is használhatja a portálon, [REST API-val](https://docs.microsoft.com/rest/api/searchservice/create-index), vagy [.NET SDK-val](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) hozhat létre egy sémát, amely összetett típusokat tartalmazza. 

Az alábbi példa bemutatja egy egyszerű mezőket gyűjtemények és komplex típusok rendelkező JSON-index séma. Figyelje meg, hogy egy összetett típus belül minden alárendelt mező típusa, és előfordulhat, attribútumok, csak a legfelső szintű mezők. A séma felel meg a fenti példa adatok. `Address` van egy összetett mezőt, amely nem egy gyűjtemény (egy szállodai Vendég rendelkezik egy címet). `Rooms` (egy szállodai Vendég rendelkezik számos termek) összetett gyűjtemény mező kitöltése.

<!---
For indexes used in a [push-model data import](search-what-is-data-import.md) strategy, where you are pushing a JSON data set to an Azure Search index, you can only have the basic syntax shown here: single complex types like `Address`, or a `Collection(Edm.ComplexType)` like `Rooms`. You cannot have complex types nested inside other complex types in an index used for push-model data ingestion.

Indexers are a different story. When defining an indexer, in particular one used to build a knowledge store, your index can have nested complex types. An indexer is able to hold a chain of complex data structures in-memory, and when it includes a skillset, it can support highly complex data forms. For more information and an example, see [How to get started with knowledge store](knowledge-store-howto.md).
-->

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

Összes a [szabályok újraindexelés](search-howto-reindex.md) , amely érvényes mezők összetett mezők általánosságban továbbra is érvényesek. Néhány ismereteit itt, a fő szabályok mező hozzáadása egy index rebuild nem igényel, de a legtöbb módosítások tegye.

### <a name="structural-updates-to-the-definition"></a>A definíció strukturális frissítéséhez

Új alfeladat mezőket egy indexkészítés nélkül bármikor hozzáadhat egy összetett mezőt. Például hozzáadása "Irányítószám" `Address` vagy az "eszközök" `Rooms` engedélyezett, csakúgy, mint legfelső szintű mező hozzáadása egy indexbe. Meglévő dokumentumok új mezők null érték tartozik, amíg az adatok frissítésével explicit módon feltölti ezeknek a mezőknek.

Figyelje meg, hogy egy összetett típus belül minden alárendelt mező típusa, és előfordulhat, attribútumok, csak a legfelső szintű mezők

### <a name="data-updates"></a>Adatok frissítése

Egy index ugyanezzel a meglévő dokumentumok frissítése a `upload` művelet összetett és egyszerű mezők esetében ugyanúgy működik – minden mező cseréje. Azonban `merge` (vagy `mergeOrUpload` egy meglévő dokumentumot alkalmazva) nem azonos az összes mezőt működik. Pontosabban a `merge` példányösszevonás elemek a gyűjteményen belül nem támogatja. Ez a korlátozás az egyszerű típusok, gyűjtemények és összetett gyűjtemény létezik. Egy gyűjtemény frissítéséhez, meg fogjuk kell a teljes értékét a vizualizációhoz módosításokat, és hozzáadhatja az új gyűjtemény az Index API-kérelem.

## <a name="searching-complex-fields"></a>Összetett mezők keresése

Szabad formátumú keresési kifejezések a komplex típusok a várt módon működik. Ha bármely kereshető mezőjében vagy dokumentum alárendelt mező bárhol megegyezik, maga a dokumentum egyezés.

Lekérdezések get további feltételeket és a kezelők rendelkezik, és néhány olyan kifejezéssel rendelkeznek mezők nevét adja meg, lehetséges, a komplikáltabb a [Lucene szintaxis](query-lucene-syntax.md). Például ez a lekérdezés megkísérli megfeleltetni a két kifejezés, "Portland" és "OR", a cím mező két alárendelt mező alapján:

    search=Address/City:Portland AND Address/State:OR

Lekérdezésekben, mint ennek a következők *predikátumban* teljes szöveges keresés, ellentétben a szűrők. A szűrők, összetett gyűjtemény almező a lekérdezések közötti kapcsolatot a tartományban változók használata [ `any` vagy `all` ](search-query-odata-collection-operators.md). A fenti Lucene lekérdezési együtt más városok Oregon "Portland, Maine" és "Portland, Oregon" is olyan dokumentumokat ad vissza. Ez akkor történik, mivel minden egyes záradékot a mező a teljes dokumentumban szereplő összes érték érvényes, így a "jelenlegi alárendelt dokumentum" fogalma nem. Ezzel kapcsolatban további információkért lásd: [ismertetése OData fájlgyűjtési szűrők az Azure Search](search-query-understand-collection-filters.md).

## <a name="selecting-complex-fields"></a>Összetett mezők kiválasztása

A `$select` válassza ki a mezőket a keresési eredmények között visszaadott paraméter használható. Ez a paraméter használatával válassza ki az adott almező egy összetett mező, a fölérendelt és alárendelt mező perjellel elválasztva tartalmazza (`/`).

    $select=HotelName, Address/City, Rooms/BaseRate

Mezők kell megjelölni lekérhető az indexben, ha azt szeretné őket a keresési eredmények között. Csak a mezők lekérhető megjelölve használható egy `$select` utasítást.

## <a name="filter-facet-and-sort-complex-fields"></a>Szűrés, értékkorlátozás és rendezési összetett mezők

Azonos [OData syntaxe cesty](query-odata-filter-orderby-syntax.md) használja a szűréshez és fielded keresések értékkorlátozás, rendezés, majd válassza ki a mezőket a keresési kérelem esetében is használható. Pro komplexní typy szabályok vonatkoznak, amelyek az alárendelt mezők is megjelölhetők szerint rendezhető és kategorizálható szabályozzák. További információ a szabályokról, tekintse meg a [Index létrehozása API-referencia](https://docs.microsoft.com/rest/api/searchservice/create-index#request).

### <a name="faceting-sub-fields"></a>Almező jellemzőkezelés

Minden alárendelt mező kategorizálható megjelölhető, kivéve, ha típusú `Edm.GeographyPoint` vagy `Collection(Edm.GeographyPoint)`.

A dokumentumok számát adja vissza az értékkorlátozás eredményeket a szülő dokumentum (egy Szálloda), az összetett gyűjteményben (termek) nem a alárendelt dokumentumok számítjuk ki. Tegyük fel például, egy szállodai Vendég "csomag" típusú 20 termek rendelkezik. A facet paraméter megadott `facet=Rooms/Type`, értékkorlátozás száma egy Szálloda, nem 20-a termek a lesz.

### <a name="sorting-complex-fields"></a>Rendezési összetett mezők

Rendezési műveletekben dokumentumok ("Hotels"), és nem alárendelt dokumentumok (termek) vonatkoznak. Ha egy összetett típus gyűjtemény például termek, fontos vegye figyelembe, hogy nem rendezhetők termek egyáltalán. Sőt a gyűjtemény nem lehet rendezni.

Rendezési műveletekben működnek, ha a mezők a dokumentumban egyetlen értéket kell-e a mező egy egyszerű mezőt, vagy egy alárendelt mezőt összetett típusban. Ha például `Address/City` lehet rendezhető, így csak egy címet / Szálloda, mert `$orderby=Address/City` hotels város szerint rendezhető.

### <a name="filtering-on-complex-fields"></a>Összetett mezők szűrése

Almező egy kifejezést egy összetett mező is hivatkozunk. Használja ugyanazt [OData syntaxe cesty](query-odata-filter-orderby-syntax.md) értékkorlátozás, rendezés, majd válassza ki a mezőket a használt. Például a következő szűrőt ad vissza minden hotels Kanadában:

    $filter=Address/Country eq 'Canada'

Szűrés egy összetett gyűjtemény mező, használhatja a **lambda kifejezésnek** az a [ `any` és `all` operátorok](search-query-odata-collection-operators.md). Ebben az esetben a **tartomány változó** a lambda kifejezésnek az alárendelt mezőkkel objektum. Ezeket a standard szintű OData syntaxe cesty alárendelt mezőket is hivatkozunk. Ha például a következő szűrőt ad vissza deluxe legalább egy helyet az összes szállodák és az összes, nem fogyasztási termek:

    $filter=Rooms/any(room: room/Type eq 'Deluxe Room') and Rooms/all(room: not room/SmokingAllowed)

Legfelső szintű egyszerű mezőkkel összetett mezők egyszerű almező csak tartalmazhat szűrők, ha rendelkezik, a **szűrhető** attribútum beállítása `true` indexdefinícióban. További információkért lásd: a [Index létrehozása API-referencia](https://docs.microsoft.com/rest/api/searchservice/create-index#request).

## <a name="next-steps"></a>További lépések

Próbálja ki a [Hotels adatkészlet](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/README.md) a a **adatimportálás** varázsló. A fontos adatok eléréséhez a Cosmos DB kapcsolati információk kell.

Az aktuális adatnak az első lépés a varázslóban az Azure Cosmos DB új adatforrás létrehozása. További a a varázslóban, amikor a célként megadott index lapot a megjelennek az index a komplex típusok. Hozzon létre, és ez az index betöltése, majd hajtsa végre a lekérdezéseket az új struktúra megértéséhez.

> [!div class="nextstepaction"]
> [Gyors útmutató: importálás, az indexelés és a lekérdezések portál varázsló](search-get-started-portal.md)