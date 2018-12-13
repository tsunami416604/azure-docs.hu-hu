---
title: Egy Azure Cosmos DB adatforrás - az Azure Search index
description: Feltérképezi az Azure Cosmos DB az adatforrást, és kiolvasni az adatokat az Azure Search a kereshető teljes szöveges index. Az indexelők automatizálni adatbetöltés a kijelölt adatforrásokhoz, például az Azure Cosmos DB.
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
robot: noindex
ms.custom: seodec2018
ms.openlocfilehash: 80759394ac920907c74f67cf9ee6dfcb52bfd9a8
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53311813"
---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>Csatlakozás a Cosmos DB az Azure Search indexelők használatával

Ebből a cikkből megtudhatja, hogyan lehet:

> [!div class="checklist"]
> * Konfigurálása [Azure Search-indexelőt](search-indexer-overview.md) , amely egy Azure Cosmos DB-gyűjtemények adatforrásként használ.
> * A search-index létrehozása JSON-kompatibilis adattípusokkal.
> * Igény szerinti és ismétlődő indexelő az indexelő konfigurálását.
> * Növekményes frissítési az index az alapul szolgáló adatok változásai alapján.

> [!NOTE]
> Azure Cosmos DB a DocumentDB következő generációja. Bár a termék neve megváltozott, a `documentdb` szintaxis az Azure Search indexelők még létezik-e a visszamenőleges kompatibilitás az Azure Search API-k és a portál oldalainak. Az indexelők konfigurálásakor ügyeljen arra, hogy adja meg a `documentdb` szintaxis ebben a cikkben szereplő utasítások szerint.

Az alábbi videó az Azure Cosmos DB Programigazgatója Andrew Liu Azure Search-index hozzáadása egy Azure Cosmos DB-tárolók mutatja be.

>[!VIDEO https://www.youtube.com/embed/OyoYu1Wzk4w]

<a name="supportedAPIs"></a>
## <a name="supported-api-types"></a>Támogatott API-típusok

Bár az Azure Cosmos DB támogatja a különböző adatmodellek és API-k, csak az SQL API Azure Search-indexelő terméktámogatást terjed ki. MongoDB API támogatása jelenleg nyilvános előzetes verzióban érhető el.  

További API-k támogatása az azonnali. Annak érdekében, fontossági sorrendjének megállapításában, melyiket támogatja az első, leadott a szavazatát, a felhasználói visszajelzési webhelyen:

* [Tábla API adatforrás-támogatást](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab)
* [Adatforrás-támogatást a Graph API](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4)
* [Adatforrás-támogatást az Apache Cassandra API-hoz](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu)

## <a name="prerequisites"></a>Előfeltételek

A Cosmos DB-fiók mellett rendelkeznie kell egy [Azure Search szolgáltatás](search-create-service-portal.md). 

A Cosmos DB-fiók választhatja ki kívánja-e a gyűjtemény összes dokumentum automatikusan indexelése. Alapértelmezés szerint minden dokumentum automatikusan indexelt, de a felhasználók bármikor kikapcsolhatják az automatikus indexeléshez. Ha az indexelés ki van kapcsolva, dokumentumok csak keresztül elérhető azok önmagukra mutató hivatkozások, vagy a lekérdezések használatával, hogy a dokumentum azonosítója. Az Azure Search van szükség a Cosmos DB automatikus indexelést, a gyűjtemény, amely indexelését az Azure Search által működnie kell. 

<a name="Concepts"></a>
## <a name="azure-search-indexer-concepts"></a>Az Azure Search-indexelő alapfogalmai

A **adatforrás** megadja az index, hitelesítő adatok és az adatok (például a gyűjtemény belül módosított vagy törölt dokumentumok) módosítása azonosítására szolgáló házirendek adatait. Az adatforrás egy független erőforrásként van definiálva, hogy több indexelők használható.

Egy **indexelő** ismerteti, hogy az adatfolyamok az adatforrásból a cél keresési indexbe. Az indexelő segítségével:

* Végezze el az adatokat tölthet fel indexeket egyszeri példányát.
* Szinkronizálja a módosításokat az adatforrásban, ütemezés szerint az index.
* Igény szerinti frissítéséhez az index, igény szerint meghívása.

Az Azure Cosmos DB-indexelő beállításával kapcsolatban szeretne létrehozni, index, adatforrás, és végül az indexelő. Ezek az objektumok használatával is létrehozhat a [portál](search-import-data-portal.md), [.NET SDK-val](/dotnet/api/microsoft.azure.search), vagy [REST API-val](/rest/api/searchservice/). 

Ez a cikk bemutatja, hogyan használható a REST API. Ha úgy dönt, a portálhoz, a [adatok importálása varázsló](search-import-data-portal.md) végigvezeti Önt az összes erőforrás, beleértve az index létrehozását.

> [!TIP]
> Az Azure Cosmos DB irányítópultjáról elindíthatja az **Adatok importálása** varázslót, amellyel egyszerűbbé válik az adatforrás indexelése. A kezdéshez lépjen a bal oldali navigációs menüben a **Gyűjtemények** > **Azure Search hozzáadása** elemre.

> [!NOTE] 
> Egyelőre nem vagy szerkesztésekor **MongoDB** adatforrások az Azure Portal vagy a .NET SDK használatával. Azonban hogy **is** mongodb-hez indexelők a portálon, végrehajtási előzményeinek figyelése.  

<a name="CreateDataSource"></a>
## <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása
Hozzon létre egy adatforrást, tegye a POST:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mydocdbdatasource",
        "type": "documentdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

A kérés törzse tartalmazza az adatforrás-definíciót, amely a következő mezőket kell tartalmaznia:

* **Név**: Válassza ki bármelyik, amelyek az adatbázis nevét.
* **Típus**: Meg kell `documentdb`.
* **hitelesítő adatok**:
  
  * **connectionString**: Kötelező. A következő formátumban adja meg a kapcsolati adatokat az Azure Cosmos DB-adatbázishoz: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>` MongoDB-gyűjtemény, vegye fel az **ApiKind = MongoDb** kapcsolati karakterláncot: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`
  Kerülje a végpont URL-címben portszámokat. Ha a port számát adja meg, az Azure Search nem tudja indexelése az Azure Cosmos DB-adatbázist.
* **tároló**:
  
  * **Név**: Kötelező. Adja meg az adatbázis-gyűjtemény indexelendő azonosítója.
  * **lekérdezés**: Választható. Megadhat egy lekérdezést egy tetszőleges JSON-dokumentumok egybesimítására indexelésére használhatja az Azure Search egybesimított sémába. A MongoDB-gyűjtemények lekérdezések nem támogatottak. 
* **dataChangeDetectionPolicy**: Ajánlott. Lásd: [módosított dokumentumok indexelése](#DataChangeDetectionPolicy) szakaszban.
* **dataDeletionDetectionPolicy**: Választható. Lásd: [törölt dokumentumok indexelése](#DataDeletionDetectionPolicy) szakaszban.

### <a name="using-queries-to-shape-indexed-data"></a>Az alakzat lekérdezésekkel indexelt adatok
Adja meg a beágyazott tulajdonságok vagy tömbök, projekt JSON-tulajdonságokkal simítják SQL-lekérdezést, és az adatok indexelése. 

> [!WARNING]
> Egyéni lekérdezések nem támogatottak a **MongoDB** gyűjtemények: `container.query` paramétert kell beállítani, NULL értékű vagy hiányzik. Ha egyéni lekérdezés használata van szüksége, vegye fel velünk a [User Voice](https://feedback.azure.com/forums/263029-azure-search).

A példában a dokumentum:

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

Az egybesimítás lekérdezés:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Kivetítési lekérdezés:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Tömb összeolvasztási lekérdezés:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts

<a name="CreateIndex"></a>
## <a name="step-2-create-an-index"></a>2. lépés: Index létrehozása
A cél Azure Search-index létrehozása, ha még nincs ilyen. Egy index használatával is létrehozhat a [Azure portal felhasználói felületén](search-create-index-portal.md), a [Index REST API létrehozása](/rest/api/searchservice/create-index) vagy [osztály Index](/dotnet/api/microsoft.azure.search.models.index).

Az alábbi példa egy azonosító és a Leírás mező indexet hoz létre:

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
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

Győződjön meg arról, hogy a célindex sémája kompatibilis sémáját, a forrás JSON-dokumentumok vagy az egyéni lekérdezés leképezése kimenetét.

> [!NOTE]
> Particionált gyűjteményeknél végezzen az alapértelmezett dokumentum kulcsot az Azure Cosmos DB `_rid` tulajdonság, amely az Azure Search automatikusan átnevezi `rid` mert mezőnevek undescore karaktere nem lehet elindítani. Emellett az Azure Cosmos DB `_rid` értékeket az Azure Search kulcsok érvénytelen karaktereket tartalmaz. Ebből kifolyólag a `_rid` értékei a Base64-kódolású.
> 
> MongoDB-gyűjtemény, az Azure Search automatikusan átnevezi a `_id` tulajdonságot `doc_id`.  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>JSON-adattípusok és az Azure Search-adattípusok közötti leképezések
| JSON-adatok típusa | Kompatibilis a célként megadott index mezőtípusok |
| --- | --- |
| Logikai |Edm.Boolean, Edm.String |
| Hasonló egész számok |Edm.Int32, Edm.Int64, Edm.String |
| Számok, tekintse meg például a nem fix pontok |Edm.Double, Edm.String |
| Karakterlánc |Edm.String |
| Ha például ["a", "b", "c"] egyszerű típusú tömbök |Collection(Edm.String) |
| Karakterláncok, dátumok hasonló |Edm.DateTimeOffset, Edm.String |
| A GeoJSON-objektumok, például {"type": "Pont", "koordináták": [hosszú, szél]} |Edm.GeographyPoint |
| Más JSON-objektumok |– |

<a name="CreateIndexer"></a>

## <a name="step-3-create-an-indexer"></a>3. lépés: Indexelő létrehozása

Az index és az adatforrás létrehozása után készen áll az indexelő létrehozása:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mydocdbindexer",
      "dataSourceName" : "mydocdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Az indexelő futása kétóránként (ütemezési időköz beállítása "PT2H"). Az indexelők futtatásához a 30 percenként, a "PT30M" időközt beállítani. A legrövidebb támogatott időköz 5 perc. Az ütemezés nem kötelező, ha nincs megadva, az indexelő futása csak egyszer, amikor létrejön. Azonban bármikor egy indexelő igény szerinti is futtathatja.   

Az indexelő API létrehozása a további részletekért tekintse meg [indexelő létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

<a id="RunIndexer"></a>
### <a name="running-indexer-on-demand"></a>Az indexelő igény szerinti futtatása
Mellett, rendszeres időközönként futó ütemezett, az indexelő is elindítható, igény szerint:

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2017-11-11
    api-key: [Search service admin key]

> [!NOTE]
> API futtatása sikeresen adja vissza, az indexelő meghívási be van ütemezve, de a tényleges feldolgozást aszinkron módon történik. 

Az indexelő állapota a portálon vagy az API-val első indexelő állapotát, következő ismertetünk, amelyek segítségével figyelheti. 

<a name="GetIndexerStatus"></a>
### <a name="getting-indexer-status"></a>Az indexelő állapotának beolvasása
Az indexelő állapotának és végrehajtási előzményeinek kérheti le:

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2017-11-11
    api-key: [Search service admin key]

A válasz teljes indexelő állapot, az utolsó (vagy a folyamatban lévő) az indexelő meghívása és az indexelő legutóbbi indítások előzményeit tartalmazza.

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

Végrehajtási előzmények akár a 50 utolsó befejezett végrehajtások, amely rendezi a rendszer fordított időrendben (így a legújabb végrehajtása a válaszban hamarabb elérik) tartalmazza.

<a name="DataChangeDetectionPolicy"></a>
## <a name="indexing-changed-documents"></a>Módosított dokumentumok indexelése
A-adatok a változásészlelési házirend célja, hogy hatékonyan a módosított adatokat cikkek azonosítására. Jelenleg az egyetlen támogatott házirend van-e a `High Water Mark` házirend használatával a `_ts` (timestamp) tulajdonsága a következő van megadva az Azure Cosmos DB által biztosított:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

Ez a szabályzat használatával erősen ajánlott az indexelő jó teljesítmény biztosítása érdekében. 

Ha egy egyéni lekérdezést használ, győződjön meg arról, hogy a `_ts` tulajdonság előre jelzett költségről a lekérdezés által.

<a name="IncrementalProgress"></a>
### <a name="incremental-progress-and-custom-queries"></a>Növekményes folyamata és egyéni lekérdezésekkel
Növekményes folyamata az indexelés során biztosítja, hogy indexelő végrehajtása megszakad az átmeneti meghibásodások vagy végrehajtási időkorlátot, ha az indexelő folytathatja a munkát, ahol abbahagyta következő futásakor, ahelyett, hogy a teljes gyűjteményt előzmények újraindexelése. Ez akkor különösen fontos, ha nagy gyűjteményeknek indexelés. 

Egyéni lekérdezés használata esetén a növekményes folyamata az engedélyezéséhez, hogy a lekérdezés rendezi az eredményeket a szerint biztosítása a `_ts` oszlop. Ez lehetővé teszi, hogy rendszeres időközönként ellenőrzőpontos, amely az Azure Search használatával biztosítja a növekményes folyamata az folytonosságát hibák esetén.   

Bizonyos esetekben, még akkor is, ha a lekérdezés tartalmaz egy `ORDER BY [collection alias]._ts` záradékot, az Azure Search előfordulhat, hogy nem következtet, hogy a lekérdezés alapján vannak rendezve a `_ts`. Azt is megadhatja, hogy az Azure Search, hogy az eredmények használatával vannak rendezve a `assumeOrderByHighWaterMarkColumn` konfigurációs tulajdonság. A mutatót ad meg, hozzon létre, vagy az indexelő a következő frissítése: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>
## <a name="indexing-deleted-documents"></a>Dokumentumok indexelése törölve
Sorok törlése a gyűjteményből, esetén általában szeretné azokat a sorokat törölni, valamint a search-index. Az egy adatok törlési szabályzat célja, hogy hatékonyan a törölt adatok cikkek azonosítására. Jelenleg az egyetlen támogatott házirend van-e a `Soft Delete` házirend (Törlés meg azt a jelzőt, valamilyen van jelölve), amely a következő van megadva:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Ha egy egyéni lekérdezést használ, győződjön meg róla, hogy a tulajdonság által hivatkozott `softDeleteColumnName` a lekérdezés által előre.

Az alábbi példa egy helyreállítható törlési házirendet hoz létre egy adatforrást:

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
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
Gratulálunk! Megtanulhatta, hogyan integrálható az Azure Cosmos DB az Azure Search egy indexelő.

* Azure Cosmos DB kapcsolatos további információkért tekintse meg a [Azure Cosmos DB-szolgáltatásoldal](https://azure.microsoft.com/services/cosmos-db/).
* Azure Search kapcsolatos további információkért tekintse meg a [keresési szolgáltatás oldalát](https://azure.microsoft.com/services/search/).
