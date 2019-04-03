---
title: Egy Azure Cosmos DB adatforrás - az Azure Search index
description: Feltérképezi az Azure Cosmos DB az adatforrást, és kiolvasni az adatokat az Azure Search a kereshető teljes szöveges index. Az indexelők automatizálni adatbetöltés a kijelölt adatforrásokhoz, például az Azure Cosmos DB.
ms.date: 02/28/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 019945c48342238a1caa7611bdff6d06fd1e2bd9
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883394"
---
# <a name="how-to-index-cosmos-db-using-an-azure-search-indexer"></a>Az Azure Search-indexelők használatával a Cosmos DB indexelése

Ez a cikk bemutatja, hogyan konfigurálhatja egy Azure Cosmos DB [indexelő](search-indexer-overview.md) bontsa ki a tartalmat, és lehetővé teszi az Azure Search kereshető. Ezt a munkafolyamatot hoz létre az Azure Search-index, és betölti azt az Azure Cosmos DB-ből kinyert szöveget. 

Terminológiai zavaró lehet, mert fontos megjegyezni, hogy [Azure Cosmos DB-indexelő](https://docs.microsoft.com/azure/cosmos-db/index-overview) és [Azure Search-indexelő](search-what-is-an-index.md) különböző műveletek, mindegyik szolgáltatáshoz egyedi. Mielőtt elkezdené az Azure Search indexelést, az Azure Cosmos DB-adatbázis már léteznie kell, és tartalmaz adatokat.

Használhatja a [portál](#cosmos-indexer-portal), REST API-k, vagy a .NET SDK Cosmos tartalmának. A Cosmos DB-indexelő az Azure Search szolgáltatás be tud járni [Azure Cosmos-elemek](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) ezeket a protokollokat keresztül érhetők el:

* [SQL API](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference) 
* [MongoDB API-val](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction) (Azure Search támogatását az API-t a nyilvános előzetes verzióban érhető el)  

> [!Note]
> User Voice további API-támogatás a meglévő elemeket tartalmaz. A Cosmos szeretné tekintse meg az Azure Search támogatott API-k szavazat is leadott: [Table API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab), [Graph API](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4), [Apache Cassandra API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu).
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>A portál használata

Az Azure Cosmos-elemek az indexelés a legegyszerűbb módszer, hogy a varázsló használata a [az Azure portal](https://portal.azure.com/). Adatok mintavételezésének és a tároló metaadatait a [ **adatimportálás** ](search-import-data-portal.md) varázsló az Azure Search szolgáltatásban hozzon létre egy alapértelmezett indexet, leképezik a forrásmezőket cél index mezőire és az index egyetlen betöltése a művelet. A mérete és összetettsége forrásadatok működési teljes szöveges keresési index lehet percek alatt.

Az Azure Search és az Azure Cosmos DB, lehetőség szerint ugyanabban a régióban található Azure-előfizetéshez használatát javasoljuk.

### <a name="1---prepare-source-data"></a>1 – forrásadatok előkészítése

Rendelkeznie kell egy Cosmos-fiók egy Azure Cosmos database az SQL API vagy a MongoDB API-t, és a egy JSON-dokumentumokat tároló leképezve. 

Ellenőrizze, hogy a Cosmos DB-adatbázissal adatokat tartalmaz. A [adatok importálása varázsló](search-import-data-portal.md) beolvassa a metaadatokat és hajtja végre az indexsémát, de kikövetkeztetnünk az adat-mintavételezés is terhelések adatok Cosmos DB-ből. Hiányzik az adatokat, ha a varázsló a hibával leáll "hiba észlelését indexsémát az adatforrás: Nem építhető fel prototípusindex mert "emptycollection" adatforrás nem adott vissza adatokat".

### <a name="2---start-import-data-wizard"></a>2 – adatok importálása varázsló indítása

Is [varázsló elindításához](search-import-data-portal.md) az Azure Search szolgáltatás lapján, vagy kattintson a parancssávon **Azure Search hozzáadása** a a **beállítások** szakasz a tárfiók bal navigációs ablak.

   ![Adatok importálása parancs portálon](./media/search-import-data-portal/import-data-cmd2.png "indítsa el az adatok importálása varázsló")

### <a name="3---set-the-data-source"></a>3 – az adatforrás beállításához

> [!NOTE] 
> Jelenleg nem hozható létre, vagy szerkesztheti **MongoDB** adatforrások az Azure portal vagy a .NET SDK használatával. Azonban hogy **is** mongodb-hez indexelők a portálon, végrehajtási előzményeinek figyelése.

Az a **adatforrás** a forrás oldalon kell lennie **Cosmos DB**, az alábbi beállításokkal:

+ **Név** az adatforrás-objektum neve. Létrehozása után választhat más számítási feladatokhoz.

+ **Cosmos DB-fiók** a Cosmos DB, az elsődleges vagy másodlagos kapcsolati karakterláncra kell lennie egy `AccountEndpoint` és a egy `AccountKey`. A fiók meghatározza, hogy e adatok leadott-e az SQL API-t vagy a Mongo DB API-hoz

+ **Adatbázis** egy meglévő adatbázis-fiókból. 

+ **Gyűjtemény** dokumentumok tárolója. Ahhoz, hogy az importálás sikerüljön léteznie kell a dokumentumokat. 

+ **Lekérdezés** maradhat üresen, ha azt szeretné, hogy minden dokumentum, ellenkező esetben megadhatja egy lekérdezést, amely kiválaszt egy dokumentum részére. 

   ![A cosmos DB adatforrás-definíció](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB adatforrás-definíció")

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 – kihagyja a varázslóban a "Hozzáadás cognitive search" lap

Kognitív képességek hozzáadása már nem szükséges a dokumentum importáláshoz. Kivéve, ha egy adott kell [közé tartozik a Cognitive Services API-k és átalakításokat](cognitive-search-concept-intro.md) az indexelési folyamat kell ezt a lépést kihagyhatja.

A lépés kihagyásához először a következő oldalra lépéshez.

   ![Cognitive Search következő oldal gomb](media/search-get-started-portal/next-button-add-cog-search.png)

A lapról, áttérhet index testreszabása.

   ![A kognitív képességek lépés kihagyása](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 – set indexattribútumokat

Az a **Index** oldalon azt kell látnia, a mezők listája adattípus és a egy sorozatát jelölőnégyzetek az index attribútumainak beállítása. A varázsló a mezők listáját, metaadatokat és a forrásadatok mintavétellel alapján hozhatja létre. 

Akkor is tömeges-válasszon attribútumok egy attribútum oszlop tetején a jelölőnégyzetére kattintva. Válasszon **lekérhető** és **kereshető** minden mezőhöz, amely egy ügyfélalkalmazás, és teljes szöveges keresés feldolgozást mindegyikre vissza kell adni. Láthatja, hogy egész számok nem állnak-e teljes szöveges vagy intelligens kereshető (számok pontosan értékeli és gyakran hasznosak a szűrők).

Tekintse át a leírása [indexattribútumok](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) és [nyelvi elemzők](https://docs.microsoft.com/rest/api/searchservice/language-support) további információt. 

Tekintse át a választásait, néhány percet vesz igénybe. A varázsló futtatása után fizikai datové struktury hozhatók létre, és nem lehet ezen mezők szerkesztése nélkül elvetését, majd újra létre kellene hoznia minden objektumot.

   ![A cosmos DB indexelése definíció](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Cosmos DB index definíciója")

### <a name="6---create-indexer"></a>6 – az indexelő létrehozása

Teljes körűen megadva, a varázsló létrehoz három különböző objektumok a search szolgáltatás. Egy adatforrás-objektum és az index objektum az Azure Search szolgáltatás az elnevezett erőforrásként kerülnek mentésre. Az utolsó lépés egy indexelő objektumot hoz létre. Az indexelő elnevezési lehetővé teszi, hogy létezik, amely ütemezett, és az index és az adatforrás-objektum, ugyanabban a sorrendben varázsló létrehozott függetlenül kezelheti önálló erőforrásként.

Ha nem ismeri a indexelők esetében, egy *indexelő* egy erőforrás az Azure Search szolgáltatásban, amely feltérképezi a kereshető tartalmak egy külső adatforrást. A kimenetét a **adatimportálás** varázsló használata egy indexelőt, feltérképezi a Cosmos DB-adatforrást, kinyeri a kereshető tartalmak és importálja azt az Azure Search-indexbe.

Az alábbi képernyőfelvételen az alapértelmezett az indexelő konfigurációjának. Átválthat az **egyszer** Ha futtassa az indexelőt egyszer szeretne. Kattintson a **küldés** futtassa a varázslót, és az összes objektum létrehozásához. Indexelő azonnal kezdődik.

   ![A cosmos DB-indexelő definíciója](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB-indexelő definíciója")

Adatok importálása a portál oldalain követheti nyomon. Folyamatértesítéseinek jelzik, hogy az indexelő állapotának és hány dokumentumok feltöltésekor. 

Indexelő befejeződése után használhatja [keresési ablak](search-explorer.md) lekérdezheti az indexét.

> [!NOTE]
> Ha nem látja a várt adatokat, szükség lehet további attribútumok beállítása több mező alapján. Törölje az index és indexelő az imént létrehozott, és. lépés: a varázsló lépéseit, módosítja az index attribútumainak az 5. lépésben kiválasztott beállításokat. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API-k használata

A REST API-t indexet az Azure Cosmos DB-adatai, az Azure Search szolgáltatásban az összes indexelőre közös Háromrészes munkafolyamat következő használható: hozzon létre egy adatforrást, hozzon létre egy index, indexelő létrehozása. Adatok kinyerése Cosmos-storage-ból az indexelő létrehozása kérés benyújtása következik be. A kérelem teljesítése után a lekérdezhető index. 

Ha kipróbálja a mongodb-hez, az adatforrás létrehozása a REST API-t kell használnia.

A Cosmos DB-fiók választhatja ki kívánja-e a gyűjtemény összes dokumentum automatikusan indexelése. Alapértelmezés szerint minden dokumentum automatikusan indexelt, de a felhasználók bármikor kikapcsolhatják az automatikus indexeléshez. Ha az indexelés ki van kapcsolva, dokumentumok csak keresztül elérhető azok önmagukra mutató hivatkozások, vagy a lekérdezések használatával, hogy a dokumentum azonosítója. Az Azure Search van szükség a Cosmos DB automatikus indexelést, a gyűjtemény, amely indexelését az Azure Search által működnie kell. 

> [!NOTE]
> Azure Cosmos DB a DocumentDB következő generációja. Bár a termék neve megváltozott, a `documentdb` szintaxis az Azure Search indexelők még létezik-e a visszamenőleges kompatibilitás az Azure Search API-k és a portál oldalainak. Az indexelők konfigurálásakor ügyeljen arra, hogy adja meg a `documentdb` szintaxis ebben a cikkben szereplő utasítások szerint.


### <a name="1---assemble-inputs-for-the-request"></a>1 – állítsa össze a kérés bemenetek

Az egyes kérések meg kell adnia a szolgáltatás nevét és adminisztrációs kulcsot az Azure Search (a POST-fejléc), és a tárfiók nevét és kulcsát a blob Storage. Használhat [Postman](search-fiddler.md) HTTP-kéréseket küldhet az Azure Search.

Másolja az alábbi négy értéket a Jegyzettömbbe, így beillesztheti őket egy kérelem:

+ Az Azure Search-szolgáltatásnév
+ Az Azure Search adminisztrátori kulcs
+ A cosmos DB kapcsolati karakterlánc

Ezeket az értékeket a portálon találja meg:

1. Az Azure Search portál lapjain másolja a search szolgáltatás URL-címet az Áttekintés oldal.

2. Kattintson a bal oldali navigációs ablaktáblában **kulcsok** , majd másolja vagy az elsődleges vagy másodlagos kulcsot (azok egyenértékű).

3. Váltson a portál oldalain Cosmos tárfiókot. A bal oldali navigációs ablaktáblán a **beállítások**, kattintson a **kulcsok**. Ez az oldal tartalmaz egy URI-t, kapcsolati karakterláncok, két csoportját, és két különböző kulcsok. Másolja a kapcsolati karakterláncok közül a Jegyzettömbbe.

### <a name="2---create-a-data-source"></a>2 – egy adatforrás létrehozása

A **adatforrás** megadja az index, hitelesítő adatok és az adatok (például a gyűjtemény belül módosított vagy törölt dokumentumok) módosítása azonosítására szolgáló házirendek adatait. Az adatforrás egy független erőforrásként van definiálva, hogy több indexelők használható.

Hozzon létre egy adatforrást, hogy állítson össze egy POST-kérelem:

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

| Mező   | Leírás |
|---------|-------------|
| **név** | Kötelező. Válassza ki az egyik nevére, és az adatforrás-objektum képviseli. |
|**type**| Kötelező. Meg kell `documentdb`. |
|**hitelesítő adatok** | Kötelező. Cosmos DB kapcsolati karakterláncnak kell lennie.<br/>SQL-gyűjteményeket, a kapcsolati karakterláncok vannak, a következő formátumban: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/>MongoDB-gyűjtemény, vegye fel az **ApiKind = MongoDb** kapcsolati karakterláncot:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/>Kerülje a végpont URL-címben portszámokat. Ha a port számát adja meg, az Azure Search nem tudja indexelése az Azure Cosmos DB-adatbázist.|
| **tároló** | A következő elemeket tartalmazza: <br/>**Név**: Kötelező. Adja meg az adatbázis-gyűjtemény azonosítója indexelése.<br/>**lekérdezés**: Választható. Megadhat egy lekérdezést egy tetszőleges JSON-dokumentumok egybesimítására indexelésére használhatja az Azure Search egybesimított sémába.<br/>A MongoDB-gyűjtemények lekérdezések nem támogatottak. |
| **dataChangeDetectionPolicy** | Ajánlott. Lásd: [módosított dokumentumok indexelése](#DataChangeDetectionPolicy) szakaszban.|
|**dataDeletionDetectionPolicy** | Választható. Lásd: [törölt dokumentumok indexelése](#DataDeletionDetectionPolicy) szakaszban.|

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


### <a name="3---create-a-target-search-index"></a>3 – a cél keresési index létrehozása 

[A cél Azure Search-index létrehozása](/rest/api/searchservice/create-index) Ha még nincs ilyen. Az alábbi példa egy azonosító és a Leírás mező indexet hoz létre:

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
| Bool |Edm.Boolean, Edm.String |
| Hasonló egész számok |Edm.Int32, Edm.Int64, Edm.String |
| Számok, tekintse meg például a nem fix pontok |Edm.Double, Edm.String |
| String |Edm.String |
| Ha például ["a", "b", "c"] egyszerű típusú tömbök |Collection(Edm.String) |
| Karakterláncok, dátumok hasonló |Edm.DateTimeOffset, Edm.String |
| A GeoJSON-objektumok, például {"type": "Pont", "koordináták": [hosszú, szél]} |Edm.GeographyPoint |
| Más JSON-objektumok |– |

### <a name="4---configure-and-run-the-indexer"></a>4 – konfigurálása és az indexelő futtatása

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

## <a name="use-net"></a>A .NET használata

A .NET SDK-t teljes mértékben paritásos REST API-val rendelkezik. Azt javasoljuk, hogy tekintse át az előző REST API-val szakaszban megismerheti a fogalmak, a munkafolyamat és a követelményeket. Ezután hivatkozni lehet a következő .NET API dokumentációja egy JSON-indexelő felügyelt programkódban implementálni.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

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

## <a name="watch-this-video"></a>Videó megtekintése

Ez némileg régebbi 7 perces videó az Azure Cosmos DB Programigazgatója Andrew Liu Azure Search-index hozzáadása egy Azure Cosmos DB-tárolók mutatja be. A portál oldalain látható a videó elavult, de az adatokat továbbra is megfelelő.

>[!VIDEO https://www.youtube.com/embed/OyoYu1Wzk4w]

## <a name="NextSteps"></a>Következő lépések

Gratulálunk! Megtanulhatta, hogyan integrálható az Azure Cosmos DB az Azure Search egy indexelő.

* Azure Cosmos DB kapcsolatos további információkért tekintse meg a [Azure Cosmos DB-szolgáltatásoldal](https://azure.microsoft.com/services/cosmos-db/).
* Azure Search kapcsolatos további információkért tekintse meg a [keresési szolgáltatás oldalát](https://azure.microsoft.com/services/search/).
