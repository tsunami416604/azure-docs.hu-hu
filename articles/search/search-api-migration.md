---
title: Frissítse a legújabb Azure Search szolgáltatás REST API-verzióra – Azure Search
description: Tekintse át az API-verziók közötti különbségek, és ismerje meg, milyen műveleteket végezzen a meglévő kód áttelepítése a legújabb Azure Search szolgáltatás REST API-verzióra van szükség.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: 286d8bbc01b5916e842c196aed5a49ef1c76bc3c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025200"
---
# <a name="upgrade-to-the-latest-azure-search-service-rest-api-version"></a>Frissítse a legújabb Azure Search szolgáltatás REST API-verzióra
Ha egy korábbi verzióját használja, a [Azure Search szolgáltatás REST API](https://docs.microsoft.com/rest/api/searchservice/), ez a cikk segít az alkalmazás használhatja a legújabb általánosan elérhető API-verzió a 2019-05-06 frissítése.

REST API-verzió a 2019-05-06 tartalmaz néhány módosítást korábbi verzióit. Ezek a leginkább visszamenőlegesen kompatibilis, így a kód módosítása csak minimális erőfeszítéssel, attól függően, melyik verziót használja előtt érdemes beállítani. [A frissítési lépéseket](#UpgradeSteps) felvázolja a kód módosításait az új funkciók használatához szükséges.

> [!NOTE]
> Az Azure Search-szolgáltatáspéldányhoz támogatja egy tartományt a REST API-verziók, többek között a korábbiakat. Továbbra is ezen API-verziót használnak, de javasoljuk, hogy a kód áttelepítése a legújabb verzióra, hogy az új funkciókat érheti el.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2019-05-06"></a>A 2019-05-06 verzió újdonságai
Verzióját 2019-05-06-a az Azure Search szolgáltatás REST API általánosan elérhető legújabb kiadása. Ebben a verzióban API általánosan elérhető állapotba ügyfelét funkciók a következők:

* [Az automatikus kiegészítés](index-add-suggesters.md) typeahead funkció, amely egy részben megadott kifejezés bemeneti befejeződik.

* [Komplexní typy](search-howto-complex-data-types.md) natív támogatást nyújt az Azure Search-index strukturált objektum adatokat.

* [Elemzés módok JsonLines](search-howto-index-json-blobs.md)részét képező Azure BLOB indexelést, egy keresési új dokumentumot hoz létre egy JSON-entitás, amely egy új sor választja el egymástól.

* [A kognitív keresés](cognitive-search-concept-intro.md) biztosít, amely a Cognitive Services mesterséges Intelligencia Adatbővítés motorok indexelés.

Több előzetes funkciók kiadása a általánosan elérhető frissítés egybe. Tekintse át az új előzetes verziójú funkciók listáját, tekintse meg [Search REST api-verzió a 2019-05-06-Preview](search-api-preview.md).

## <a name="breaking-changes"></a>Kompatibilitástörő változások

Az alábbi funkciókat tartalmazó meglévő kódot megszakítja az api-version = a 2019-05-06.

### <a name="indexer-for-azure-cosmos-db---datasource-is-now-type-cosmosdb"></a>Az Azure Cosmos DB - indexelő adatforrás van most "type": "cosmos DB"

Ha használ olyan [Cosmos DB-indexelő](search-howto-index-cosmosdb.md ), módosítania kell `"type": "documentdb"` való `"type": "cosmosdb"`.

### <a name="indexer-execution-result-errors-no-longer-have-status"></a>Az indexelő végrehajtási eredménye hibák már nincs állapot

Az indexelő végrehajtási hiba struktúráját korábban rendelkezett egy `status` elemet. Ez nem nyújtott hasznos információkat az elem el lett távolítva.

### <a name="indexer-data-source-api-no-longer-returns-connection-strings"></a>Az indexelő adatforrás API már nem ad vissza a kapcsolati karakterláncok

API-ból a 2019-05-06 és a 2019-05-06-előzetes verzió frissítésétől kezdve az adatforrás API verziója már nem ad vissza kapcsolati karakterláncok bármely REST-művelet a válaszban. Előző API-verziókban az adatforrásokat, POST, használatával létrehozott Azure Search visszaadott **201-es** a OData választ, amelyek tartalmazzák a kapcsolati karakterlánc egyszerű szöveges követ.

### <a name="named-entity-recognition-cognitive-skill-is-now-discontinued"></a>Megnevezett entitások felismerése cognitive szakértelem megszűnt

Ha felhívja [neve entitások felismerése](cognitive-search-skill-named-entity-recognition.md) szakértelem a kódban, a hívás sikertelen lesz. Csere a funkció [entitások felismerése](cognitive-search-skill-entity-recognition.md). Meg kell tudni cserélje le a képzettségi hivatkozás más módosítása nélkül. Az API-aláírás a mindkét verzió esetében megegyezik. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Frissítési lépései
Ha frissít egy előző verzióról a végleges verzió, 2017-11-11 vagy 2016-09-01, valószínűleg nem kell semmilyen módosítást a kód más, a verziószám módosításához. A csak olyan helyzetekben, amelyben szükség lehet kódot, amikor:

* A kód sikertelen lesz, ha egy API-válaszban visszaadott ismeretlen tulajdonságait. Alapértelmezés szerint az alkalmazás figyelmen kívül hagyja-tulajdonságok, amely nem érthető.

* A kód továbbra is fennáll az API-kérések, és próbálja meg újból elküldeni őket az új API-verzióra. Például Ez azért fordulhat elő, ha az alkalmazás továbbra is fennáll, a Search API által visszaadott folytatási token (további információért keressen `@search.nextPageParameters` a a [Search API-referencia](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)).

Ha ezekben a helyzetekben egyikét alkalmazza, majd szükség lehet, ennek megfelelően módosítania kell a kódot. Ellenkező esetben módosítása nélkül lehet szükség, ha el szeretné indítani a használatával a [új funkciók](#WhatsNew) 2019-05-06-verzió.

Ha API előzetes verziójáról frissít, a fenti is vonatkozik, de is figyelembe kell venni, hogy bizonyos előzetes verziójú funkciók nem érhetők el a verzió a 2019-05-06:

* ["Nagyobb, mint a" lekérdezések](search-more-like-this.md) csak előzetes funkció továbbra is.

Ha a kódot használja ezeket a funkciókat, nem tudja frissíteni az API-verzió a 2019-05-06 anélkül, hogy eltávolítaná a használatát.

> [!IMPORTANT]
> Előzetes API-k tesztelési és értékelési célokra szolgálnak, és nem éles környezetekben használható.
> 

### <a name="upgrading-complex-types"></a>Komplexní typy frissítése

Ha a kódot a régebbi verzióival előzetes API 2017-11-11-Preview vagy 2016-09-01-Preview komplexní typy használ, néhány új és módosított korlátozva van verzióban, amelynek szüksége, érdemes figyelembe vennie a 2019-05-06:

+ Almező és indexenkénti összetett gyűjtemények száma mélysége korlátait csökkentette lett. Ha hozott létre, amelyek használatával az előzetes api-verziók kvótákra indexeket, bármely próbál frissítse vagy hozza létre őket API-verzióval a 2019-05-06 sikertelen lesz. Ha ez érvényes Önre, szüksége lesz újra kell tervezni a sémát, majd építse újra az index és az új keretein belül.

+ Új korlátozva van az api-version 2019-05-06 összetett gyűjtemény hozható létre a dokumentum elemeinek száma. Ha indexeket a dokumentumok, amelyek használatával az előzetes api-verziók kvótákra hozta létre, api-verzió a 2019-05-06 adatok újraindexelése tett bármilyen kísérlet sikertelen lesz. Ha ez érvényes Önre, szüksége lesz a dokumentum egy összetett gyűjtemény elemei számának csökkentése előtt az adatok újraindexelés.

További információkért lásd: [szolgáltatási korlátozásaival, az Azure Search](search-limits-quotas-capacity.md).

### <a name="how-to-upgrade-an-old-complex-type-structure"></a>Egy régi komplex típus struktúra frissítése

Ha a kódot a korábbi előzetes API-verziók egyikével komplexní typy használ, előfordulhat, hogy használni egy index definíciója formátuma a következőhöz hasonló:

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

Egy újabb faszerkezetben formátumát meghatározó index mezőire API verziója 2017. 11. 11 – előzetes verzió a jelent meg. Az új formátumban az egyes összetett Pole literálu a mezők gyűjteménye ahol definiálva vannak az al mezőjét. Az API-verzió a 2019-05-06 az új formátum kizárólag szolgál, és próbál létrehozni vagy frissíteni a régi formátumot használó index művelet sikertelen lesz. Ha a régi formátumot használatával létrehozott indexeket, szüksége API verziója 2017. 11. 11 – előzetes verzió használata előtt felügyelhetők, API-verzióval a 2019-05-06 frissíteni őket az új formátumra.

Az új formátumra "egyszerű" indexek frissítheti az alábbi lépéseket követve API verziója 2017. 11. 11 – előzetes verzió használata:

1. Hajtsa végre az indexet beolvasni egy GET kéréssel. Ha már szerepel az új formátumra, akkor végzett.

2. Az index az "egyszerű" formátumból az új formátumra fordítja le. Ez a kód írása, mivel nincs mintakód elérhető a írásának időpontjában kell.

3. Hajtsa végre a PUT-kérések az index frissítéséhez az új formátumra. Ellenőrizze, hogy nem módosítható a kereshetőségét/filterability mezők, például az index összes többi részletet, mert ez nem engedélyezett a frissítés Index API.

> [!NOTE]
> Nem alkalmas a régi "egyszerű" formátumban, az Azure Portalról létrehozott indexeket lehet kezelni. Frissítsen az indexek a "egyszerű" leképezése az Önnek legkorábbi, a "fa" megjelenítését.

## <a name="next-steps"></a>További lépések

Tekintse át az Azure Search szolgáltatás REST API dokumentációja. Ha problémákat tapasztal, megkérdezi a Súgó a [StackOverflow](https://stackoverflow.com/) vagy [forduljon az ügyfélszolgálathoz](https://azure.microsoft.com/support/community/?product=search).

> [!div class="nextstepaction"]
> [Keresési szolgáltatás REST API-referencia](https://docs.microsoft.com/rest/api/searchservice/)

