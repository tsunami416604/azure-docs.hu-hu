---
title: Frissítés a legújabb Azure Search Service REST API verzióra – Azure Search
description: Tekintse át az API-verziók közötti különbségeket, és Ismerje meg, hogy mely műveletek szükségesek a meglévő kód legújabb Azure Search Service REST API verzióra való átváltásához.
author: brjohnstmsft
manager: nitinme
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: 6c1f7fdb1f349c9e31ba63d79a9b9e26ea9f09da
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182388"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>Frissítés a legújabb Azure Search Service REST API verzióra
Ha a [Azure Search Service REST API](https://docs.microsoft.com/rest/api/searchservice/)egy korábbi verzióját használja, ez a cikk segítséget nyújt az alkalmazás frissítéséhez a legújabb általánosan elérhető API-verzió (2019-05-06) használatára.

A REST API 2019-05-06-es verziója a korábbi verziók néhány módosítását tartalmazza. Ezek többnyire visszamenőlegesen kompatibilisek, ezért a kód módosítása csak minimális erőfeszítést igényelhet, attól függően, hogy melyik verziót használta. A [verziófrissítés lépései](#UpgradeSteps) az új funkciók használatához szükséges kód módosításait ismertetik.

> [!NOTE]
> Az Azure Search Service-példányok számos REST API-verziót támogatnak, köztük a korábbikat is. Továbbra is használhatja ezeket az API-verziókat, de javasoljuk, hogy a kód áttelepítését a legújabb verzióra, hogy hozzáférhessen az új funkciókhoz.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>Az 2019-05-06-es verzió újdonságai
Az 2019-05-06-es verzió a Azure Search Service REST API legújabb általánosan elérhető kiadása. Az API-verzió általánosan elérhető állapotára váltott funkciók a következők:

* Az [automatikus kiegészítés](index-add-suggesters.md) egy typeahead funkció, amely egy részben megadott kifejezési bemenetet hajt végre.

* Az [összetett típusok](search-howto-complex-data-types.md) natív támogatást biztosítanak a strukturált objektumok számára egy Azure Search indexben.

* A [JsonLines-elemzési módok](search-howto-index-json-blobs.md), az Azure Blob-indexelés részeként egy keresési dokumentumot hoz létre, amely egy sortöréssel elválasztott JSON-entitáson alapul.

* A [kognitív keresés](cognitive-search-concept-intro.md) olyan indexelést biztosít, amely kihasználja Cognitive Services mesterséges intelligencia-gazdagító motorját.

Az előzetes verzió számos szolgáltatása egybeesik az általánosan elérhető frissítéssel. Az új előzetes verziójú funkciók listájának áttekintését lásd: [REST API – version 2019-05-06 – előzetes verzió](search-api-preview.md).

## <a name="breaking-changes"></a>Kompatibilitástörő változások

A következő funkciókat tartalmazó meglévő kód megszakad az API-Version = 2019-05-06 esetén.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>A Azure Cosmos db-DataSource indexelő most "type": "cosmosdb"

Ha [Cosmos db](search-howto-index-cosmosdb.md )indexelő használ, a `"type": "documentdb"` `"type": "cosmosdb"`következőre kell váltania:.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Az indexelő végrehajtási eredményeinek hibái már nem rendelkeznek állapottal

Az indexelő végrehajtásához használt hiba szerkezete korábban egy `status` elemmel rendelkezett. Ez az elem el lett távolítva, mert nem volt hasznos információ.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Az indexelő adatforrás API többé nem adja vissza a kapcsolatok karakterláncait

A 2019-05-06-es és 2019-05-06-os API-verzióktól kezdve az adatforrás API többé nem adja vissza a kapcsolódási karakterláncokat a REST-műveletek válaszában. A korábbi API-verziókban a POST paranccsal létrehozott adatforrások esetében a Azure Search a **201** értéket adta vissza, amelyet a OData válasz követ, amely egyszerű szövegként szerepelt a kapcsolatok karakterláncában.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Az elnevezett entitások felismerésének kognitív készsége már megszűnt

Ha meghívja a [név entitás](cognitive-search-skill-named-entity-recognition.md) -felismerési képességet a kódban, a hívás sikertelen lesz. A helyettesítő funkció [](cognitive-search-skill-entity-recognition.md)az entitások felismerése. A szaktudás hivatkozását más változtatások nélkül is le kell cserélni. Az API-aláírás mindkét verzió esetében azonos. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>A frissítés lépései
Ha egy korábbi, 2017-11-11-es vagy 2016-09-01-es verzióról frissít, valószínűleg nem kell módosítania a kódot, kivéve a verziószámot. A következő esetekben lehet szükség a kód módosítására:

* A kód meghiúsul, ha a rendszer ismeretlen tulajdonságokat ad vissza egy API-válaszban. Alapértelmezés szerint az alkalmazás figyelmen kívül hagyja az általa nem értelmezhető tulajdonságokat.

* A kód megőrzi az API-kérelmeket, és megkísérli az új API-verzióra való újraküldést. Ez például akkor fordulhat elő, ha az alkalmazás a keresési API által visszaadott folytatási tokeneket is megőrzi (további információért `@search.nextPageParameters` keresse a [Search API](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)-referenciát).

Ha bármelyik ilyen helyzet Önre vonatkozik, akkor előfordulhat, hogy a kódot ennek megfelelően kell módosítania. Ellenkező esetben nincs szükség módosításra, kivéve, ha az 2019-05-06-es verzió [új funkcióit](#WhatsNew) szeretné használni.

Ha egy előzetes verziójú API-verzióról frissít, a fentiek is érvényesek, de a 2019-05-06-es verzióban nem érhető el az előzetes verziójú szolgáltatások:

* ["Több, mint a" lekérdezés](search-more-like-this.md)
* [CSV-blob indexelése](search-howto-index-csv-blobs.md)
* [MongoDB API-támogatás Cosmos db indexelő számára](search-howto-index-cosmosdb.md)

Ha a kód ezeket a funkciókat használja, nem fogja tudni frissíteni a 2019-05-06-es API-verzióra a használat eltávolítása nélkül.

> [!IMPORTANT]
> Az előzetes verziójú API-k tesztelési és értékelési célokra szolgálnak, és nem használhatók éles környezetekben.
> 

### <a name="upgrading-complex-types"></a>Összetett típusok frissítése

Ha a kód összetett típusokat használ a korábbi előzetes verziójú API 2017-11-11 – Preview vagy 2016-09-01-Preview verzióhoz, akkor a 2019-05-06-as verzióban néhány új és megváltozott korlátozás szerepel, amelyekről tudnia kell:

+ Az almezők mélységének és az indexekben lévő összetett gyűjtemények számának korlátozása csökkent. Ha olyan indexeket hozott létre, amelyek túllépik ezeket a korlátozásokat az előzetes verziójú API-verziók használatával, akkor az API 2019-05-06-es verziójának használatával végzett frissítés vagy újbóli létrehozás sikertelen lesz. Ha ez Önre vonatkozik, újra kell terveznie a sémát, hogy az megfeleljen az új korlátoknak, majd újjáépítse az indexet.

+ Az API-Version 2019-05-06 új korlátja a dokumentumok összetett gyűjtemények elemeinek száma. Ha olyan dokumentumokkal hozta létre az indexeket, amelyek túllépik ezeket a korlátozásokat az előzetes verziójú API-verziók használatával, az API-Version 2019-05-06-es verzióval végzett újraindexelési kísérlet sikertelen lesz. Ha ez Önre vonatkozik, csökkentenie kell az összetett gyűjtemények elemeinek számát, mielőtt újraindexeli az adatokat.

További információ: [Azure Search szolgáltatási korlátai](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Régi komplex típusú struktúra frissítése

Ha a kód összetett típusokat használ a korábbi előzetes verziójú API-verziók egyikével, lehetséges, hogy az alábbihoz hasonló index-definíció formátumot használ:

```json
{
  "name": "hotels",  
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.microsoft" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true, "analyzer": "tagsAnalyzer" },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true },
    { "name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType" },
    { "name": "Address/StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
    { "name": "Address/City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/PostalCode", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Address/Country", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
    { "name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)" }, 
    { "name": "Rooms/Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene" },
    { "name": "Rooms/Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "Rooms/Type", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
    { "name": "Rooms/BedOptions", "type": "Edm.String", "searchable": true },
    { "name": "Rooms/SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "Rooms/SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Rooms/Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "facetable": true, "analyzer": "tagsAnalyzer" }
  ]
}  
```

Az index mezőinek definiálásához egy újabb faszerkezetű formátum lett bevezetve az API 2017-11-11-es verziójában. Az új formátumban minden összetett mezőhöz tartozik egy mező gyűjtemény, amelyben az almezők definiálva vannak. Az API 2019-05-06-es verziójában ez az új formátum kizárólag a régi formátummal rendelkező indexek létrehozására vagy frissítésére tett kísérlet sikertelen lesz. Ha a régi formátummal létrehozott indexekkel rendelkezik, akkor az API 2017-11-11-es verziójának előzetes verzióját kell használnia az új formátumra való frissítéshez, mielőtt azok a 2019-05-06-es API-verzióval felügyelhetők legyenek.

A "Flat" indexeket az új formátumra frissítheti a következő lépésekkel az API 2017-11-11-es verziójának használatával:

1. Az index lekéréséhez hajtson végre egy GET-kérelmet. Ha már az új formátumban van, elkészült.

2. Fordítsa le az indexet a "Flat" formátumból az új formátumra. Ehhez kódot kell írnia, mivel az írás időpontjában nincs elérhető mintakód.

3. Helyezzen el egy PUT-kérést az index új formátumra való frissítéséhez. Ügyeljen arra, hogy ne módosítsa az index egyéb részleteit, például a mezők kereshetőség/szűrését, mert ezt a frissítési index API nem engedélyezi.

> [!NOTE]
> Nem lehetséges a régi "Flat" formátummal létrehozott indexek kezelése a Azure Portal. Frissítse az indexeket a "Flat" ábrázolásból a "Tree" kifejezésre a lehető leghamarabb.

## <a name="next-steps"></a>További lépések

Tekintse át a Azure Search Service REST API dokumentációját. Ha problémákba ütközik, kérjen segítséget a [StackOverflow](https://stackoverflow.com/) , vagy [forduljon](https://azure.microsoft.com/support/community/?product=search)az ügyfélszolgálathoz.

> [!div class="nextstepaction"]
> [Keresési szolgáltatás REST API referenciája](https://docs.microsoft.com/rest/api/searchservice/)

