---
title: Azure Cosmos DB adatforrás indexelése – Azure Search
description: Azure Cosmos DB adatforrások bejárása és az adat beolvasása a Azure Search teljes szöveges kereshető indexében. Az indexelő automatizálja az adatfeldolgozást a kiválasztott adatforrásokhoz, például a Azure Cosmos DBhoz.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 802a4e9c6191d33051eb075543691845595bc9c3
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "69656694"
---
# <a name="how-to-index-cosmos-db-using-an-azure-search-indexer"></a>Cosmos DB indexelése Azure Search indexelő használatával


> [!Note]
> A MongoDB API támogatása előzetes verzióban érhető el, és nem éles használatra készült. A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nem érhető el portál vagy .NET SDK-támogatás.
>
> Az SQL API általánosan elérhető.

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy Azure Cosmos db [Indexelő](search-indexer-overview.md) a tartalom kinyeréséhez és a Azure Search kereshetővé tételéhez. Ez a munkafolyamat létrehoz egy Azure Search indexet, és betölti azt a Azure Cosmos DBból kinyert meglévő szöveggel. 

Mivel a terminológia zavaró lehet, érdemes megjegyezni, hogy [Azure Cosmos db indexelés](https://docs.microsoft.com/azure/cosmos-db/index-overview) és [Azure Search indexelés](search-what-is-an-index.md) különböző művelet, amely egyedi az egyes szolgáltatásokhoz. Azure Search indexelésének megkezdése előtt a Azure Cosmos DB-adatbázisnak már léteznie kell, és tartalmaznia kell az adatait.

A Cosmos-tartalmak indexeléséhez használhatja a [portált](#cosmos-indexer-portal), a REST API-kat vagy a .net SDK-t is. A Cosmos DB indexelő Azure Search képes az alábbi protokollokon keresztül elért [Azure Cosmos-elemek](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) feltérképezésére:

* [SQL API](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference) 
* [MongoDB API (előzetes verzió)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)

> [!Note]
> A felhasználó hangja meglévő elemeket tartalmaz a további API-támogatáshoz. Szavazást készíthet a Cosmos API-k számára, amelyeket a Azure Search: [Table API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab), [Graph API](https://feedback.azure.com/forums/263029-azure-search/suggestions/13285011-add-graph-databases-to-your-data-sources-eg-neo4) [Apache Cassandra API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32857525-indexer-crawler-for-apache-cassandra-api-in-azu).
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>A portál használata

Az Azure Cosmos-elemek indexelésének legegyszerűbb módja a [Azure Portal](https://portal.azure.com/)varázsló használata. Ha mintavételezési adatokat és metaadatokat olvas be a tárolón, az [**adatok importálása**](search-import-data-portal.md) varázsló Azure Search létrehozhat egy alapértelmezett indexet, leképezheti a forrás mezőket a célként megadott index mezőire, és betöltheti az indexet egyetlen művelettel. A forrásadatok méretétől és összetettségének függvényében percek alatt működőképes teljes szöveges keresési indexszel rendelkezhet.

Azt javasoljuk, hogy ugyanazt az Azure-előfizetést használja Azure Search és Azure Cosmos DB esetében is, lehetőleg ugyanabban a régióban.

### <a name="1---prepare-source-data"></a>1 – forrásadatok előkészítése

Rendelkeznie kell egy Cosmos-fiókkal, egy Azure Cosmos-adatbázissal, amely az SQL API vagy a MongoDB API-ra van leképezve, valamint egy JSON-dokumentum tárolója. 

Győződjön meg arról, hogy a Cosmos DB adatbázisa tartalmaz adatait. Az [adatok importálása varázsló](search-import-data-portal.md) beolvassa a metaadatokat, és elvégzi az adatok mintavételezését az index sémájának következtetéséhez, de az adatokat a Cosmos DBból is betölti. Ha az adatok hiányoznak, a varázsló ezzel a hibával leáll: "hiba történt az index sémájának észlelése az adatforrásból: nem sikerült létrehozni a prototípus-indexet, mert a (z)" emptycollection "adatforrás nem adott vissza értéket".

### <a name="2---start-import-data-wizard"></a>2 – az adatimportálás megkezdése varázsló

A varázsló elindításához a Azure Search szolgáltatás lapján, vagy a Storage-fiók bal oldali navigációs paneljének **Beállítások** szakaszában a **Azure Search hozzáadása** elemre kattintva is [elindíthatja a varázslót](search-import-data-portal.md) .

   ![Adatimportálási parancs a portálon](./media/search-import-data-portal/import-data-cmd2.png "Az adatimportálás varázsló elindítása")

### <a name="3---set-the-data-source"></a>3 – az adatforrás beállítása

> [!NOTE] 
> A **MongoDB** -adatforrások jelenleg nem hozhatók létre és nem szerkeszthetők Azure Portal vagy a .net SDK használatával. A portálon **azonban nyomon** követheti a MongoDB indexek végrehajtási előzményeit.

**Az adatforrás lapon a** forrásnak **Cosmos DBnak**kell lennie, a következő jellemzőkkel:

+ A **név** az adatforrás-objektum neve. A létrehozás után kiválaszthatja más számítási feladatokhoz is.

+ **Cosmos db fióknak** a Cosmos db elsődleges vagy másodlagos, `AccountEndpoint` és `AccountKey` értékűnek kell lennie. A fiók meghatározza, hogy az adatgyűjtés SQL API-ként vagy Mongo DB API-ként történik-e

+ Az **adatbázis** egy meglévő adatbázis a fiókból. 

+ A **gyűjtemény** a dokumentumok tárolója. A dokumentumoknak léteznie kell ahhoz, hogy az importálás sikeres legyen. 

+ A **lekérdezés** üres is lehet, ha az összes dokumentumot szeretné használni, ellenkező esetben olyan lekérdezést is beírhat, amely kiválasztja a dokumentum részhalmazát. 

   ![Cosmos DB adatforrás-definíció](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB adatforrás-definíció")

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 – a varázsló "kognitív keresés hozzáadása" lapjának kihagyása

A kognitív képességek hozzáadása nem szükséges a dokumentumok importálásához. Ha nincs kifejezetten szüksége az indexelési folyamat [Cognitive Services API-k és átalakítására](cognitive-search-concept-intro.md) , ugorja át ezt a lépést.

A lépés kihagyásához először ugorjon a következő lapra.

   ![A következő oldal gomb a kognitív kereséshez](media/search-get-started-portal/next-button-add-cog-search.png)

Ebből az oldalból kihagyhatja az index testreszabását.

   ![A kognitív képességek lépés kihagyása](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 – index attribútumainak beállítása

Az **index** lapon meg kell jelennie egy adattípusú mezők listájának, valamint egy sor jelölőnégyzet az index attribútumainak beállításához. A varázsló metaadatok alapján és a forrásadatok mintavételezésével hozhatja meg a mezők listáját. 

Az attribútumok tömeges kiválasztásához kattintson az attribútum oszlop tetején található jelölőnégyzetre. Válassza a **beolvasható** és **kereshető** lehetőséget minden olyan mező esetében, amelyet vissza kell adni egy ügyfélalkalmazás számára, és a teljes szöveges keresés feldolgozására is érvényes. Megfigyelheti, hogy az egész számok nem teljes szöveges vagy zavaros kereshetők (a számok szó szerint vannak kiértékelve, és gyakran hasznosak a szűrőkben).

További információkért tekintse át az [index attribútumainak](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) és a [nyelvi elemzők](https://docs.microsoft.com/rest/api/searchservice/language-support) leírását. 

Szánjon egy kis időt a kiválasztott elemek áttekintésére. A varázsló futtatása után a rendszer létrehozza a fizikai adatstruktúrákat, és nem tudja szerkeszteni ezeket a mezőket az összes objektum eldobása és újbóli létrehozása nélkül.

   ![Cosmos DB index definíciója](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Cosmos DB index definíciója")

### <a name="6---create-indexer"></a>6 – indexelő létrehozása

Teljes mértékben meg van adva, a varázsló három különböző objektumot hoz létre a keresési szolgáltatásban. Az adatforrás-objektumok és az index objektumok elnevezett erőforrásokként lesznek mentve a Azure Search szolgáltatásban. Az utolsó lépés egy indexelő objektumot hoz létre. Az indexelő elnevezése lehetővé teszi, hogy önálló erőforrásként is használható legyen, amelyet az index és az adatforrás objektumtól függetlenül ütemezhet és kezelhet, amely ugyanabban a varázsló-sorozatban jön létre.

Ha nem ismeri az indexelő funkciót, az *Indexelő* egy olyan erőforrás, Azure Search, amely egy külső adatforrást mutat be a kereshető tartalomhoz. Az **adatimportálás** varázsló kimenete egy indexelő, amely feltérképezi a Cosmos DB adatforrást, Kinyeri a kereshető tartalmat, és importálja azt egy Azure Search indexbe.

Az alábbi képernyőfelvételen az alapértelmezett indexelő konfiguráció látható. Ha egyszer szeretné futtatni az indexelő, váltson **egyszerre** . Kattintson a **Submit (elküldés** ) gombra a varázsló futtatásához és az összes objektum létrehozásához. Az indexelés azonnal megkezdődik.

   ![Cosmos DB indexelő definíciója](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB indexelő definíciója")

Az adatimportálást a portál oldalain lehet figyelni. Az állapotjelző értesítések az indexelési állapotot és a feltöltött dokumentumok számát jelzik. 

Az indexelés befejezésekor a [Search Explorer](search-explorer.md) használatával kérdezheti le az indexet.

> [!NOTE]
> Ha nem látja a várt adattípust, előfordulhat, hogy további attribútumokat kell beállítania további mezőknél. Törölje az imént létrehozott indexet és indexelő, és ismételje meg a varázslót, és módosítsa az index attribútumainak kiválasztását az 5. lépésben. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API-k használata

A REST API használatával indexelheti a Azure Cosmos DB-adatforrást, amely a következő három részből álló munkafolyamatot követve az összes indexelő Azure Search: adatforrás létrehozása, index létrehozása, indexelő létrehozása. A Cosmos-tárolóból kinyert adatok akkor következnek be, amikor elküldi az index-létrehozási kérelmet. A kérés befejezését követően lekérdezhető index jelenik meg. 

Ha kiértékeli a MongoDB, az adatforrás létrehozásához a REST `api-version=2019-05-06-Preview` kell használnia.

A Cosmos DB-fiókban megadhatja, hogy a gyűjtemény automatikusan indexelje-e az összes dokumentumot. Alapértelmezés szerint az összes dokumentum automatikusan indexelve van, de ki is kapcsolhatja az automatikus indexelést. Ha az indexelés ki van kapcsolva, a dokumentumok csak az önhivatkozások vagy a dokumentumok AZONOSÍTÓjának használatával érhetők el. A Azure Search megköveteli Cosmos DB Automatikus indexelés bekapcsolását a gyűjteményben, amelyet Azure Search fog indexelni. 

> [!WARNING]
> Azure Cosmos DB a DocumentDB következő generációja. Az **2017-11-11** -es API-verzióval korábban a `documentdb` szintaxist használhatja. Ez azt jelentette, hogy az adatforrás típusát `cosmosdb` vagy `documentdb`ként is megadhatja. Az API **2019-05-06** -es verziójától kezdve a Azure Search API-k és a portál csak a jelen cikkben leírtaknak megfelelően támogatja a `cosmosdb` szintaxist. Ez azt jelenti, hogy az adatforrás típusának `cosmosdb` kell lennie, ha egy Cosmos DB-végponthoz szeretne csatlakozni.

### <a name="1---assemble-inputs-for-the-request"></a>1 – bemenetek összegyűjtése a kérelemhez

Minden kérelem esetében meg kell adnia a szolgáltatás nevét és a rendszergazdai kulcsot a Azure Search (a POST fejlécben), valamint a blob Storage-hoz tartozó Storage-fiók nevét és kulcsát. A [Poster](search-get-started-postman.md) használatával http-kéréseket küldhet a Azure Searchnak.

Másolja a következő négy értéket a Jegyzettömbbe, hogy beillessze őket egy kérelembe:

+ Azure Search szolgáltatás neve
+ Azure Search rendszergazdai kulcs
+ Cosmos DB a kapcsolatok karakterlánca

Ezeket az értékeket a portálon találja:

1. A Azure Search portáljának oldalain másolja a keresési szolgáltatás URL-címét az Áttekintés lapról.

2. A bal oldali navigációs ablaktáblán kattintson a **kulcsok** elemre, majd másolja az elsődleges vagy a másodlagos kulcsot (ezek egyenértékűek).

3. Váltson a Cosmos Storage-fiók portál oldalaira. A bal oldali navigációs ablaktábla **Beállítások**területén kattintson a **kulcsok**elemre. Ez az oldal egy URI-t, két kapcsolatok karakterláncot és két kulcsot tartalmaz. Másolja az egyik kapcsolódási karakterláncot a Jegyzettömbbe.

### <a name="2---create-a-data-source"></a>2 – adatforrás létrehozása

Az **adatforrások** az index, a hitelesítő adatok és az adatok változásainak azonosítására szolgáló szabályzatokat határozzák meg (például módosított vagy törölt dokumentumok a gyűjteményen belül). Az adatforrás független erőforrásként van definiálva, így több indexelő is használható.

Adatforrás létrehozásához hozzon létre egy POST-kérést:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCollection", "query": null },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        }
    }

A kérelem törzse tartalmazza az adatforrás definícióját, amelynek tartalmaznia kell a következő mezőket:

| Mező   | Leírás |
|---------|-------------|
| **név** | Kötelező. Válasszon egy tetszőleges nevet az adatforrás-objektum megjelenítéséhez. |
|**type**| Kötelező. @No__t_0nak kell lennie. |
|**hitelesítő adatok** | Kötelező. Cosmos DB-kapcsolatok karakterláncának kell lennie.<br/>SQL-gyűjtemények esetén a következő formátumú kapcsolatok karakterláncai: `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/>A MongoDB-gyűjtemények esetében adja hozzá a **ApiKind = MongoDB** karakterláncot a kapcsolódási sztringhez:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/>Kerülje a portok számát a végpont URL-címében. Ha a portszámot is tartalmazza, Azure Search nem fogja tudni indexelni a Azure Cosmos DB-adatbázist.|
| **tároló** | A következő elemeket tartalmazza: <br/>**név**: kötelező. Az indexelni kívánt adatbázis-gyűjtemény AZONOSÍTÓjának meghatározása.<br/>**lekérdezés**: nem kötelező. Megadhat egy lekérdezést, amely egy tetszőleges JSON-dokumentumot lelapul egy olyan egyszerű sémába, amely Azure Search indexelhető.<br/>A MongoDB-gyűjtemények esetében a lekérdezések nem támogatottak. |
| **dataChangeDetectionPolicy** | Ajánlott. Lásd: [módosított dokumentumok indexelése](#DataChangeDetectionPolicy) szakasz.|
|**dataDeletionDetectionPolicy** | Választható. Lásd: [törölt dokumentumok indexelése](#DataDeletionDetectionPolicy) szakasz.|

### <a name="using-queries-to-shape-indexed-data"></a>Indexelt adatformátumok használata lekérdezések használatával
Megadhat egy SQL-lekérdezést a beágyazott tulajdonságok vagy tömbök, a Project JSON-tulajdonságok és az indexelni kívánt adatszűréshez. 

> [!WARNING]
> Az egyéni lekérdezések nem támogatottak a **MongoDB** -gyűjtemények esetében: `container.query` paramétert NULL értékűre vagy elhagyott értékre kell beállítani. Ha egyéni lekérdezést kell használnia, kérjük, tudassa velünk a [felhasználói hangon](https://feedback.azure.com/forums/263029-azure-search).

Példa dokumentumra:

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }

Lekérdezés szűrése:

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

Lekérdezés összeolvasztása:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Leképezési lekérdezés:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Tömb-összeolvasztási lekérdezés:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3 – cél keresési index létrehozása 

Ha még nem rendelkezik ilyennel, [hozzon létre egy cél Azure Search indexet](/rest/api/searchservice/create-index) . Az alábbi példa egy azonosítót és egy leírás mezőt tartalmazó indexet hoz létre:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
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

Győződjön meg arról, hogy a célként megadott index sémája kompatibilis a forrás JSON-dokumentumok sémájával vagy az egyéni lekérdezési leképezés kimenetével.

> [!NOTE]
> A particionált gyűjtemények esetében az alapértelmezett dokumentum kulcsa Azure Cosmos DB `_rid` tulajdonsága, amely Azure Search automatikusan átnevezi `rid`, mert a mezőnevek nem kezdődhetnek undescore karakterrel. Emellett Azure Cosmos DB `_rid` értékek olyan karaktereket tartalmaznak, amelyek Azure Search kulcsokban érvénytelenek. Emiatt a `_rid` értékek Base64 kódolású.
> 
> A MongoDB-gyűjtemények esetében a Azure Search automatikusan átnevezi a `_id` tulajdonságot a következőre: `doc_id`.  

### <a name="mapping-between-json-data-types-and-azure-search-data-types"></a>A JSON-adattípusok és az Azure Search adattípusok közötti megfeleltetés
| JSON-adattípus | Kompatibilis cél index típusú mezők |
| --- | --- |
| bool |EDM. Boolean, EDM. String |
| Egész számokhoz hasonló számok |EDM. Int32, EDM. Int64, EDM. String |
| A lebegő pontokhoz hasonló számok |EDM. Double, EDM. String |
| Sztring |Edm.String |
| Egyszerű típusok tömbje, például ["a", "b", "c"] |Collection(Edm.String) |
| A dátumokhoz hasonló karakterláncok |EDM. DateTimeOffset, EDM. String |
| GeoJSON objektumok, például {"type": "pont", "koordináták": [Long, Lat]} |Edm.GeographyPoint |
| Egyéb JSON-objektumok |– |

### <a name="4---configure-and-run-the-indexer"></a>4 – az indexelő konfigurálása és futtatása

Miután létrehozta az indexet és az adatforrást, készen áll az indexelő létrehozására:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Ez az indexelő két óránként fut (az ütemezett időköz értéke "PT2H"). Az indexelő 30 percenkénti futtatásához állítsa az intervallumot "PT30M" értékre. A legrövidebb támogatott időköz 5 perc. Az ütemterv nem kötelező – ha nincs megadva, az indexelő csak egyszer fut a létrehozáskor. Az indexelő igény szerinti futtatása azonban bármikor elvégezhető.   

Az indexelő API létrehozásával kapcsolatos további információkért tekintse meg az [Indexelő létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer)című leírást.

Az indexelő-ütemtervek definiálásával kapcsolatos további információkért lásd: [az indexelő ütemezett Azure Search](search-howto-schedule-indexers.md).

## <a name="use-net"></a>A .NET használata

Az általánosan elérhető .NET SDK teljes paritással rendelkezik az általánosan elérhető REST API. Javasoljuk, hogy tekintse át az előző REST API szakaszt a fogalmak, a munkafolyamatok és a követelmények megismeréséhez. A következő .NET API-referenciák dokumentációjában a JSON-indexelő implementálása felügyelt kódban végezhető el.

+ [Microsoft. Azure. Search. models. DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [Microsoft. Azure. Search. models. datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [Microsoft. Azure. Search. models. index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [Microsoft. Azure. Search. models. indexelő](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Módosított dokumentumok indexelése

Az adatváltozás-észlelési szabályzat célja, hogy hatékonyan azonosítsa a módosított adatelemeket. Jelenleg az egyetlen támogatott szabályzat a `High Water Mark` szabályzat, amely a Azure Cosmos DB által megadott `_ts` (timestamp) tulajdonságot használja, amely a következőképpen van megadva:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

A szabályzat használata kifejezetten ajánlott a megfelelő indexelő teljesítmény biztosításához. 

Ha egyéni lekérdezést használ, győződjön meg arról, hogy a lekérdezés a `_ts` tulajdonságot használja.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Növekményes előrehaladás és egyéni lekérdezések

Az indexelés során fellépő növekményes előrehaladás biztosítja, hogy ha az indexelő végrehajtását átmeneti hibák vagy végrehajtási időkorlát miatt megszakítja, az indexelő elvégezheti, hogy a legközelebb Mikor fusson, és ne kelljen újraindexelni a teljes gyűjteményt. Ez különösen fontos a nagyméretű gyűjtemények indexelése során. 

Ha egyéni lekérdezés használatakor szeretné engedélyezni a növekményes előrehaladást, győződjön meg arról, hogy a lekérdezés az `_ts` oszlop alapján rendeli az eredményeket. Ez lehetővé teszi, hogy a Azure Search a meghibásodások jelenlétében növekményes előrehaladást biztosítson az időszakos ellenőrzés során.   

Bizonyos esetekben, még akkor is, ha a lekérdezés `ORDER BY [collection alias]._ts` záradékot tartalmaz, Azure Search előfordulhat, hogy nem következtet rá, hogy a lekérdezés a `_ts` szerint van rendezve. Megadhatja Azure Search, hogy az eredmények a `assumeOrderByHighWaterMarkColumn` konfigurációs tulajdonság használatával legyenek rendezve. A célzás megadásához az alábbi módon hozza létre vagy frissítse az indexelő: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Törölt dokumentumok indexelése

Ha a sorok törlődnek a gyűjteményből, általában törölni kívánja ezeket a sorokat a keresési indexből is. Az adattörlési észlelési szabályzat célja, hogy hatékonyan azonosítsa a törölt adatelemeket. Jelenleg az egyetlen támogatott házirend a `Soft Delete` házirend (a törlés egy bizonyos rendezési jelzővel van megjelölve), amely a következőképpen van megadva:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Ha egyéni lekérdezést használ, győződjön meg arról, hogy az `softDeleteColumnName` által hivatkozott tulajdonságot a lekérdezés tervezi.

Az alábbi példa egy olyan adatforrást hoz létre, amely egy törlési szabályzattal rendelkezik:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
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

Gratulálunk! Megtanulta, hogyan integrálhatja a Azure Cosmos DBt a Azure Search indexelő használatával.

* Ha többet szeretne megtudni a Azure Cosmos DBről, tekintse meg a [Azure Cosmos db szolgáltatás lapot](https://azure.microsoft.com/services/cosmos-db/).
* Ha többet szeretne megtudni a Azure Searchről, tekintse meg a [keresési szolgáltatás lapot](https://azure.microsoft.com/services/search/).
