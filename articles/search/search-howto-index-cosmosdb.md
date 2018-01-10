---
title: "Egy Azure Cosmos DB SQL API adatforrás indexelése az Azure Search |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan hozzon létre egy Azure Search-indexelőt, egy Azure Cosmos DB (SQL API-t) az adatforrás."
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 
ms.service: search
ms.devlang: rest-api
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: search
ms.date: 01/08/2018
ms.author: eugenesh
robot: noindex
ms.openlocfilehash: e449f13adcd1a3651e1cac852b23f21d0227038a
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2018
---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>Az Azure Search használatával az indexelők Cosmos DB csatlakozás

[Az Azure Cosmos DB](../cosmos-db/introduction.md) Microsoft globálisan elosztott, több modellre adatbázis. A saját [SQL API](../cosmos-db/sql-api-introduction.md), Azure Cosmos DB gazdag és ismerős SQL lekérdezési képességeket konzisztens kis késleltetést biztosítanak a séma nélküli JSON-adatokat. Az Azure Search zökkenőmentesen integrálható az SQL API-t. Közvetlenül be egy Azure Search index használatával képes lekérni a JSON-dokumentumok egy [Azure Search-indexelőt](search-indexer-overview.md), kifejezetten Azure Cosmos DB SQL API tervezték. 

Ebből a cikkből megtudhatja, hogyan:

> [!div class="checklist"]
> * Azure Search adatforrásként egy Azure Cosmos DB SQL API-adatbázis használatára konfigurálja. Igény szerint adja meg egy lekérdezést egy részhalmazát kiválasztásához.
> * Hozzon létre egy keresési indexszel JSON-kompatibilis adattípusokat.
> * Az indexelő igény szerinti és ismétlődő indexelő konfigurálása.
> * Növekményesen frissítse az index az alapjául szolgáló adatok végbement változások alapján.

> [!NOTE]
> Az Azure Cosmos DB SQL API következő generációja DocumentDB van. Bár a termék neve módosul, a `documentdb` Azure keresési indexelő szintaxist még létezik a visszamenőleges kompatibilitás az Azure Search API-k és a portál lapjai. Indexelő konfigurálásakor ügyeljen arra, hogy adja meg a `documentdb` szintaxis Ez a cikk útmutatását.

<a name="supportedAPIs"></a>

## <a name="supported-api-types"></a>Támogatott API-típusok

Bár Azure Cosmos DB támogatják a különböző adatmodellekben és API-k, csak az SQL API indexelő támogatási terjeszti ki. 

További API-k támogatása verziója. Először támogatásához munkaterületek sorrendjének meghatározásához előbb konvertálja a felhasználó hang webhelyen:

* [Tábla API-adatok forrás támogatás](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab)
* [Graph API adatok forrás támogatása](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4)
* [MongoDB API adatok forrás támogatása](https://feedback.azure.com/forums/263029-azure-search/suggestions/18861421-documentdb-indexer-should-be-able-to-index-mongodb)
* [Apache Cassandra API-adatok forrás támogatás](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu)

## <a name="prerequisites"></a>Előfeltételek

Az Azure Cosmos DB indexelő beállításához rendelkeznie kell egy [Azure Search szolgáltatás](search-create-service-portal.md), és hozzon létre egy index, adatforrás, és végül az indexelő. Ezek az objektumok használatával hozhat létre a [portal](search-import-data-portal.md), [.NET SDK](/dotnet/api/microsoft.azure.search), vagy [REST API](/rest/api/searchservice/) az összes nem-.NET nyelvet. 

Ha úgy dönt, a portálhoz, a [adatok importálása varázsló](search-import-data-portal.md) végigvezeti Önt az összes ezeket az erőforrásokat, beleértve az index létrehozását.

> [!TIP]
> Az Azure Cosmos DB irányítópultjáról elindíthatja az **Adatok importálása** varázslót, amellyel egyszerűbbé válik az adatforrás indexelése. A kezdéshez lépjen a bal oldali navigációs menüben a **Gyűjtemények** > **Azure Search hozzáadása** elemre.

<a name="Concepts"></a>

## <a name="azure-search-indexer-concepts"></a>Az Azure keresési indexelő fogalmak
Az Azure keresési támogatja a létrehozását és kezelését az adatok adatforrásokat (beleértve az Azure Cosmos DB SQL API-val) és adatforrások üzemeltető indexelők.

A **adatforrás** határozza meg az adatok index, a hitelesítő adatok és a házirendek alapján azonosítja az adatokat (például a gyűjtemény módosított vagy törölt dokumentumok) változásait. Az adatforrás független erőforrásként van definiálva, így több indexelők használható.

Egy **indexelő** ismerteti, hogyan az adatok bemenetként szolgál az adatforrásból származó cél search-index. Az indexelő használható:

* Hajtsa végre az adatokat tölthet fel indexet egyszeri másolatát.
* Egy index ugyanezzel az adatforrás ütemezés szerint módosítások szinkronizálása. Az ütemezés az indexelő definíció részét képezi.
* Igény szerinti frissítések igény szerint indexek meghívni.

<a name="CreateDataSource"></a>

## <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása
Hozzon létre egy adatforrást, tegye a FELADÁS egy vagy több:

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

A kérelem törzsében az adatforrás-definíciót, amely az alábbi mezőket kell tartalmaznia:

* **név**: válassza az egyetlen határoz meg az adatbázis nevét.
* **típus**: kell `documentdb`.
* **hitelesítő adatok**:
  
  * **connectionString**: kötelező. A következő formátumban adja meg a kapcsolati adatokat az Azure Cosmos DB adatbázishoz:`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`
* **tároló**:
  
  * **név**: kötelező. Adja meg az azonosítót, az adatbázis-gyűjtemény indexelése.
  * **lekérdezés**: nem kötelező. Megadhat egy lekérdezést egy tetszőleges JSON-dokumentumok egybesimítására Azure Search indexelheti strukturálatlan sémába.
* **dataChangeDetectionPolicy**: ajánlott. Lásd: [módosított dokumentumokat indexelő](#DataChangeDetectionPolicy) szakasz.
* **dataDeletionDetectionPolicy**: nem kötelező. Lásd: [törölt dokumentumok indexelő](#DataDeletionDetectionPolicy) szakasz.

### <a name="using-queries-to-shape-indexed-data"></a>Lekérdezések alakzathoz indexelt adatokat
Adjon meg egy SQL-lekérdezést egybesimítására beágyazott tulajdonságok vagy a tömbök, a projekt JSON tulajdonságait, és szűrje az adatokat indexelése. 

Példa a dokumentum:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "documentdb", "search"]
    }

Szűrő lekérdezés:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Lekérdezés egybesimítását:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Leképezési lekérdezést:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


A tömb simítási lekérdezést:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>2. lépés: Index létrehozása
A cél Azure Search-index létrehozása, ha Ön nem rendelkezik ilyennel. Egy index használatával hozhat létre a [Azure portál felhasználói felületének](search-create-index-portal.md), a [Index REST API létrehozása](/rest/api/searchservice/create-index) vagy [osztály Index](/dotnet/api/microsoft.azure.search.models.index).

Az alábbi példa létrehoz egy indexet és azonosító és a Leírás mezőt:

    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }

Gondoskodjon arról, hogy a cél index sémája kompatibilis a forrás JSON-dokumentumok a séma vagy az egyéni lekérdezés leképezése kimenetét.

> [!NOTE]
> A particionált gyűjtemények, az alapértelmezett dokumentum kulcs Azure Cosmos DB `_rid` tulajdonság, amely lekérdezi átnevezésre `rid` az Azure Search. Emellett Azure Cosmos DB tartozó `_rid` érték az Azure Search kulcsok érvénytelen karaktereket tartalmaz. Emiatt a `_rid` értékei a Base64-kódolású.
> 
> 

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>JSON az adattípusokat és az Azure Search adattípusok közötti leképezést
| JSON-adattípus | Kompatibilis index mező céltípusok |
| --- | --- |
| logikai érték |Edm.Boolean, Edm.String |
| Hasonló egész szám |Edm.Int32, Edm.Int64, Edm.String |
| Számok, hogy keresse meg például a lebegőpontos pontok |Edm.Double, Edm.String |
| Karakterlánc |Edm.String |
| Ha például ["a", "b", "c"] egyszerű típusú tömbök |Collection(Edm.String) |
| Karakterláncok, dátumok hasonló |Edm.DateTimeOffset, Edm.String |
| GeoJSON objektumok, például {"type": "Point", "coordinates": [hosszú, lat]} |Edm.GeographyPoint |
| Más JSON-objektumok |– |

<a name="CreateIndexer"></a>

## <a name="step-3-create-an-indexer"></a>3. lépés:, Hozzon létre egy indexelőt

Az index és az adatforrás létrehozása után készen áll az indexelő létrehozásához:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Az indexelő futása két óránként (ütemezési időköz értéke "PT2H"). Az indexelő 30 percenként "PT30M" intervallum be. A legrövidebb támogatott időköz értéke 5 perc. Az ütemezés nem kötelező – Ha nincs megadva, az indexelő futása csak egyszer, amikor létrejön. Azonban bármikor indexelő igény szerinti is futtathatja.   

Hozzon létre indexelő API további részletekért tekintse meg [létrehozása indexelő](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

<a id="RunIndexer"></a>
### <a name="running-indexer-on-demand"></a>Az indexelő igény szerinti futtatása
Rendszeres időközönként futó ütemezett, mellett az indexelő is el az igény szerinti:

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2016-09-01
    api-key: [Search service admin key]

> [!NOTE]
> API futtatása sikeresen adja vissza, ha indexelő hívása ütemezése megtörtént, de a tényleges feldolgozását aszinkron módon történik. 

Az indexelő állapotát a portál vagy az API-val beolvasása indexelő állapota, amelyek azt ismertetik, ezután figyelheti. 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>Az indexelő állapotának beolvasása
Az indexelő állapotát és végrehajtási előzményeinek le:

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2016-09-01
    api-key: [Search service admin key]

A válasz indexelő általános állapotát, az utolsó (vagy folyamatban) indexelő meghívása és az indexelő legutóbbi indítások előzményeit tartalmazza.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2014-11-26T03:37:18.853Z",
            "endTime":"2014-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Futtatási előzményei legfeljebb az 50 legutóbbi befejezett végrehajtások, amely fordított időrendi sorrendben rendezi a (így a legújabb végrehajtási származik a válaszban szereplő első) tartalmazza.

<a name="DataChangeDetectionPolicy"></a>
## <a name="indexing-changed-documents"></a>Az indexelő módosított dokumentumok
A szabályzat adatok módosítása célja megváltozott adatelemek hatékonyan azonosításához. Az egyetlen támogatott házirend jelenleg a `High Water Mark` házirend használatával a `_ts` Azure Cosmos DB, amely a következő által biztosított (időbélyeg)-tulajdonság:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

A házirenddel kifejezetten ajánljuk, hogy helyes indexelő teljesítmény biztosítása érdekében. 

Ha egy egyéni lekérdezést használ, győződjön meg arról, hogy a `_ts` tulajdonság a lekérdezés által van vetítve.

<a name="IncrementalProgress"></a>
### <a name="incremental-progress-and-custom-queries"></a>Növekményes folyamatát és egyéni lekérdezések
Növekményes folyamat során indexelő biztosítja, hogy ha indexelő végrehajtása megszakad átmeneti hibák vagy a végrehajtási időt, az indexelő is onnan folytathatja az adatgyűjtést, ahol abbahagyta következő futásakor, nem muszáj indexelik újra a teljes gyűjteményt a teljesen. Ez akkor különösen fontosak akkor, ha nagy gyűjteményekre indexelő. 

Engedélyezi a növekményes folyamatban van, egy egyéni lekérdezés használata esetén, győződjön meg arról, hogy a lekérdezés rendelések az eredményeket a `_ts` oszlop. Ez lehetővé teszi, hogy rendszeres ellenőrzés felé, amely az Azure Search használatával biztosítja a növekményes folyamatban hibák esetén.   

Egyes esetekben, még akkor is, ha a lekérdezés tartalmaz egy `ORDER BY [collection alias]._ts` záradék, az Azure Search előfordulhat, hogy nem következtethető ki, hogy a lekérdezés alapján van rendezve a `_ts`. Megadható, hogy a Azure Search, hogy az eredmények rendezve vannak-e a a `assumeOrderByHighWaterMarkColumn` konfigurációs tulajdonság. Adja meg a mutatót, illetve módosíthatja az indexelő az alábbiak szerint: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>
## <a name="indexing-deleted-documents"></a>Dokumentumok indexelő törlése
Miután sorok törlődnek a gyűjteményből, általában szeretné azokat a sorokat törölni, valamint a search-index. A szabályzat adatok törlését célja törölt adatelemek hatékonyan azonosításához. Az egyetlen támogatott házirend jelenleg a `Soft Delete` házirend (valamiféle jelölővel törlésre jelölt), amelyhez van megadva az alábbiak szerint:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Ha egy egyéni lekérdezést használ, győződjön meg arról, hogy a tulajdonság által hivatkozott `softDeleteColumnName` hívásával a lekérdezést.

A következő példa egy adatforrást egy helyreállítható törlési házirendet hoz létre:

    POST https://[Search service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myDocDbEndpoint.documents.azure.com;AccountKey=myDocDbAuthKey;Database=myDocDbDatabaseId"
        },
        "container": { "name": "myDocDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }

## <a name="NextSteps"></a>Következő lépések
Gratulálunk! Azure Cosmos DB integrálása az Azure Search-indexelőt segítségével bejárható és tölthet fel dokumentumokat az SQL adatmodellt megtanulhatta.

* Azure Cosmos DB kapcsolatos további tudnivalókért tekintse meg a [Azure Cosmos DB oldalát](https://azure.microsoft.com/services/cosmos-db/).
* Azure Search kapcsolatos további tudnivalókért tekintse meg a [keresési szolgáltatás lapján](https://azure.microsoft.com/services/search/).
