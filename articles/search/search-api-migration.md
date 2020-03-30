---
title: REST API-verziók frissítése
titleSuffix: Azure Cognitive Search
description: Tekintse át az API-verziók közötti különbségeket, és ismerje meg, hogy mely műveletek szükségesek a meglévő kód átgréséhez az Azure Cognitive Search szolgáltatás REST API-verziójába.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: edb45eebc2c4eacc2f30d13988943f097a7190fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112173"
---
# <a name="upgrade-to-the-latest-azure-cognitive-search-service-rest-api-version"></a>Frissítés az Azure Cognitive Search szolgáltatás LEGÚJABB REST API-verziójára

Ha a [Search REST API](https://docs.microsoft.com/rest/api/searchservice/)egy korábbi verzióját használja, ez a cikk segít az alkalmazás frissítésében a legújabb, általánosan elérhető API-verzió, 2019-05-06 használatával.

A REST API 2019-05-06-os verziója tartalmaz néhány módosítást a korábbi verziókhoz. Ezek többnyire visszamenőlegesen kompatibilisek, ezért a kód módosításához csak minimális erőfeszítésre van szükség, attól függően, hogy melyik verziót használta korábban. [A frissítés lépései](#UpgradeSteps) ismertetik az új szolgáltatások használatához szükséges kódmódosításokat.

> [!NOTE]
> Az Azure Cognitive Search szolgáltatáspéldány a REST API-verziók számos verzióját támogatja, beleértve a korábbiakat is. Továbbra is használhatja ezeket az API-verziókat, de javasoljuk, hogy migrálja a kódot a legújabb verzióra, hogy új képességeket érhethessen el.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>A 2019-05-06-os verzió újdonságai
A 2019-05-06-os verzió a REST API legújabb, általánosan elérhető kiadása. Az API-verzió általánosan elérhető állapotára áttért szolgáltatások a következők:

* [Az automatikus kiegészítés](index-add-suggesters.md) egy typeahead szolgáltatás, amely egy részben megadott kifejezésbevitelt végez.

* [Az összetett típusok](search-howto-complex-data-types.md) natív támogatást nyújtanak a strukturált objektumadatokhoz a keresési indexben.

* [JsonLines elemzési módok](search-howto-index-json-blobs.md), része az Azure Blob indexelés, létrehoz egy keresési dokumentumot json entitás, amely egy újvonal választja el.

* [A i dúsítása indexelést](cognitive-search-concept-intro.md) biztosít, amely kihasználja a Cognitive Services AI-dúsító motorjait.

Számos előzetes funkciókiadás egybeesik ezzel az általánosan elérhető frissítéssel. Az új előzetes verziójú funkciók listájának megtekintéséhez olvassa el a [Search REST api-version 2019-05-06-Preview című témakört.](search-api-preview.md)

## <a name="breaking-changes"></a>Kompatibilitástörő változások

A következő funkciókat tartalmazó meglévő kód az api-version=2019-05-06-on megszakad.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Indexelő az Azure Cosmos DB - adatforrás most "típus": "cosmosdb"

[Ha Cosmos DB indexelőt](search-howto-index-cosmosdb.md )használ, `"type": "documentdb"` a `"type": "cosmosdb"`-ra kell váltania.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Az indexelő végrehajtási eredményhibáinak már nincs állapotuk

Az indexelő végrehajtásának hibastruktúrájának korábban volt eleme. `status` Ez az elem azért lett eltávolítva, mert nem adott meg hasznos információkat.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Az Indexelő adatforrás API-ja már nem ad vissza kapcsolati karakterláncokat

Az API 2019-05-06 és 2019-05-06-preview-verzióktól kezdve az adatforrás API már nem ad vissza kapcsolati karakterláncokat bármely REST-művelet válaszában. A korábbi API-verziókban a POST használatával létrehozott adatforrások esetében az Azure Cognitive Search **201-et** adott vissza, majd az OData-választ, amely egyszerű szövegként tartalmazta a kapcsolati karakterláncot.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>A megnevezett entitásfelismerési kognitív szakértelem megszűnt

Ha a kódban [meghívja](cognitive-search-skill-named-entity-recognition.md) a Name Entity Recognition skill-t, a hívás sikertelen lesz. A cserefunkció az [Entitás felismerése](cognitive-search-skill-entity-recognition.md). A szakértelem-referenciát más módosítással nem helyettesítheti. Az API-aláírás mindkét verzió esetében azonos. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>A frissítés lépései
Ha egy korábbi, 2017-11-11-es vagy 2016-09-01-es ga-verzióról frissít, valószínűleg nem kell módosítania a kódot, csak a verziószám módosítását. Az egyetlen olyan helyzet, amelyben szükség lehet a kód módosítására, amikor:

* A kód sikertelen, ha ismeretlen tulajdonságokat ad vissza egy API-válasz. Alapértelmezés szerint az alkalmazás figyelmen kívül hagyja a tulajdonságokat, hogy nem érti.

* A kód megőrzi az API-kérelmeket, és megpróbálja újraelküldeni őket az új API-verzióra. Ez például akkor fordulhat elő, ha az alkalmazás megőrzi a Keresési API-ból visszaadott folytatási jogkivonatokat (további információkért keresse meg a Keresési `@search.nextPageParameters` [API-referencia](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)mezőben).

Ha a következő helyzetek bármelyike vonatkozik Önre, akkor lehet, hogy ennek megfelelően kell módosítania a kódot. Ellenkező esetben nincs szükség módosításokra, kivéve, ha a 2019-05-06-os verzió [új funkcióit](#WhatsNew) szeretné használni.

Ha egy előzetes API-verzióról frissít, a fentiek is érvényesek, de azt is tudnia kell, hogy egyes előzetes verziójú funkciók nem érhetők el a 2019-05-06-os verzióban:

* ["Több, mint ez" lekérdezések](search-more-like-this.md)
* [CSV-blob indexelése](search-howto-index-csv-blobs.md)
* [A MongoDB API támogatása a Cosmos DB indexelőkszámára](search-howto-index-cosmosdb.md)

Ha a kód ezeket a funkciókat használja, nem fog tudni frissíteni a 2019-05-06 API-verzióra anélkül, hogy eltávolítaná a használatukat.

> [!IMPORTANT]
> Az előzetes verziójú API-k tesztelésre és kiértékelésre szolgálnak, és nem használhatók éles környezetben.
> 

### <a name="upgrading-complex-types"></a>Összetett típusok frissítése

Ha a kód összetett típusokat használ a régebbi 2017-11-11-preview vagy 2016-09-01-preview verzióval, a 2019-05-06-os verzióban vannak új és módosított korlátozások, amelyekről tudnia kell:

+ Az almezők mélységére és az indexenkénti összetett gyűjtemények számára vonatkozó korlátok csökkentek. Ha olyan indexeket hozott létre, amelyek meghaladják ezeket a korlátokat az előzetes verziók használatával, a 2019-05-06-os API-verzióval történő frissítésükre vagy újbóli létrehozására tett minden kísérlet sikertelen lesz. Ha ez vonatkozik Önre, újra kell terveznie a sémát, hogy elférjen az új korlátok között, majd újra kell építenie az indexet.

+ A 2019-05-06 api-verzióban új korlát van az összetett gyűjtemények dokumentumonkénti elemeinek számára vonatkozóan. Ha olyan indexeket hozott létre, amelyek a határértékeket meghaladó dokumentumokat léptek létre az előzetes verziók használatával, az adatok api-version 2019-05-06 használatával történő újraindexelése sikertelen lesz. Ha ez vonatkozik Önre, az adatok újraindexelése előtt csökkentenie kell az összetett gyűjteményelemek számát dokumentumonként.

További információ: [Service limits for Azure Cognitive Search](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Egy régi összetett típusstruktúra frissítése

Ha a kód összetett típusokat használ a régebbi előzetes API-verziók egyikével, akkor lehet, hogy a következőhez hasonló indexdefiníciós formátumot használ:

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

Az indexmezők definiálásának újabb faszerű formátumát vezették be az API 2017-11-11-preview verziójában. Az új formátumban minden összetett mezőrendelkezik egy mezőgyűjteménysel, ahol az almezők definiálva vannak. A 2019-05-06 API-verzióban ez az új formátum kizárólag használatos, és a régi formátumhasználatával index létrehozása vagy frissítése sikertelen lesz. Ha a régi formátummal létrehozott indexeket a régi formátummal hozta létre, akkor a 2017-11-11-Preview API-verziót kell használnia az új formátumra való frissítéshez, mielőtt az API 2019-05-06-os verziójával kezelhetők lennének.

A "lapos" indexeket az új formátumra frissítheti a következő lépésekkel az API 2017-11-11-Preview verziójával:

1. Get-kérés végrehajtása az index lekéréséhez. Ha már az új formátumban van, akkor kész.

2. Fordítsa le az indexet a "lapos" formátumból az új formátumba. Ehhez kódot kell írnia, mivel az írás időpontjában nincs mintakód.

3. Put-kérés végrehajtása az index új formátumra való frissítéséhez. Győződjön meg arról, hogy nem módosítja az index egyéb részleteit, például a mezők kereshetőségét/szűrését, mivel ezt az Index frissítése API nem engedélyezi.

> [!NOTE]
> Nem lehet kezelni az Azure Portalrégi "lapos" formátumú indexeket. Kérjük, frissítse indexek a "lapos" képviselet a "fa" képviselet a lehető leghamarabb.

## <a name="next-steps"></a>További lépések

Tekintse át a Search REST API referenciadokumentációját. Ha problémákba ütközik, kérjen segítséget a [StackOverflow-val](https://stackoverflow.com/) kapcsolatban, vagy [forduljon az ügyfélszolgálathoz.](https://azure.microsoft.com/support/community/?product=search)

> [!div class="nextstepaction"]
> [KERESÉSszolgáltatás REST API-jának hivatkozása](https://docs.microsoft.com/rest/api/searchservice/)

