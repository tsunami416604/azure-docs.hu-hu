---
title: "Az Azure Search Cosmos DB adatforrás indexelő |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan hozzon létre egy Azure Search-indexelőt Cosmos DB adatforrásként."
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
ms.date: 08/10/2017
ms.author: eugenesh
robot: noindex
ms.openlocfilehash: c7c883f683c744415a1b600cea45c1882939e021
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="connecting-cosmos-db-with-azure-search-using-indexers"></a>Az Azure Search használatával az indexelők Cosmos DB csatlakozás

Ha szeretné végrehajtani egy remek keresési élményt biztosít a Cosmos DB adatok, az Azure Search-indexelőt segítségével szerez adatokat is használhatja az Azure Search-index. Ez a cikk azt mutatja be Azure Cosmos DB integrálása az Azure Search anélkül, hogy az indexelő infrastruktúra karbantartása kód írása.

A Cosmos DB indexelő beállításához rendelkeznie kell egy [Azure Search szolgáltatás](search-create-service-portal.md), és hozzon létre egy index, adatforrás, és végül az indexelő. Ezek az objektumok használatával hozhat létre a [portal](search-import-data-portal.md), [.NET SDK](/dotnet/api/microsoft.azure.search), vagy [REST API](/rest/api/searchservice/) az összes nem-.NET nyelvet. 

Ha úgy dönt, a portálhoz, a [adatok importálása varázsló](search-import-data-portal.md) végigvezeti Önt a létrehozását ezeket az erőforrásokat.

> [!NOTE]
> Azure Cosmos-adatbázis a DocumentDB következő generációja van. Bár a termék neve módosul, szintaxisa azonos az előzővel. Továbbra is meg `documentdb` indexelő cikkben megfelelően. 

> [!TIP]
> Indítja el a **adatimportálás** varázsló egyszerűbbé teheti az adott adatforrás indexelő Cosmos DB irányítópultról. A kezdéshez lépjen a bal oldali navigációs menüben a **Gyűjtemények** > **Azure Search hozzáadása** elemre.

<a name="Concepts"></a>
## <a name="azure-search-indexer-concepts"></a>Az Azure keresési indexelő fogalmak
Az Azure Search támogatja a létrehozását és kezelését az adatok adatforrásokat (beleértve a Cosmos DB) és adatforrások üzemeltető indexelők.

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

* **név**: válassza ki a Cosmos DB adatbázis képviselő bármely nevét.
* **típus**: kell `documentdb`.
* **hitelesítő adatok**:
  
  * **connectionString**: kötelező. A következő formátumban adja meg a kapcsolati adatokat az Azure Cosmos DB adatbázishoz:`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`
* **tároló**:
  
  * **név**: kötelező. Adja meg az azonosítót, a Cosmos DB gyűjtemény indexelése.
  * **lekérdezés**: nem kötelező. Megadhat egy lekérdezést egy tetszőleges JSON-dokumentumok egybesimítására Azure Search indexelheti strukturálatlan sémába.
* **dataChangeDetectionPolicy**: ajánlott. Lásd: [módosított dokumentumokat indexelő](#DataChangeDetectionPolicy) szakasz.
* **dataDeletionDetectionPolicy**: nem kötelező. Lásd: [törölt dokumentumok indexelő](#DataDeletionDetectionPolicy) szakasz.

### <a name="using-queries-to-shape-indexed-data"></a>Lekérdezések alakzathoz indexelt adatokat
Adjon meg egy Cosmos DB lekérdezést egybesimítására beágyazott tulajdonságok vagy a tömbök, a projekt JSON-tulajdonságok és az adatok indexelése szűrő. 

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
> A particionált gyűjtemények, az alapértelmezett dokumentum kulcs Cosmos DB `_rid` tulajdonság, amely lekérdezi átnevezésre `rid` az Azure Search. Emellett Cosmos DB tartozó `_rid` érték az Azure Search kulcsok érvénytelen karaktereket tartalmaz. Emiatt a `_rid` értékei a Base64-kódolású.
> 
> 

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>JSON az adattípusokat és az Azure Search adattípusok közötti leképezést
| JSON-ADATTÍPUS | KOMPATIBILIS INDEX MEZŐ CÉLTÍPUSOK |
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
A szabályzat adatok módosítása célja megváltozott adatelemek hatékonyan azonosításához. Az egyetlen támogatott házirend jelenleg a `High Water Mark` házirend használatával a `_ts` (időbélyegzője) tulajdonság a megadott Cosmos DB, ami az alábbiak szerint van meghatározva:

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
Gratulálunk! Azure Cosmos DB integrálása az Azure Search használja az indexelő Cosmos DB megtanulhatta.

* Hogyan Azure Cosmos DB kapcsolatos további tudnivalókért lásd: a [Azure Cosmos DB oldalát](https://azure.microsoft.com/services/cosmos-db/).
* Hogyan Azure Search kapcsolatos további tudnivalókért lásd: a [keresési szolgáltatás lapján](https://azure.microsoft.com/services/search/).
