---
title: Keresés az Azure Cosmos DB-adatai között
titleSuffix: Azure Cognitive Search
description: Adatok importálása az Azure Cosmos DB-ből egy kereshető indexbe az Azure Cognitive Search-ben. Az indexelők automatizálják az adatok betöltését a kiválasztott adatforrások, például az Azure Cosmos DB számára.
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: d1723b6c5d56554fbff576f6a07e37455845bda4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283003"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>Cosmos DB-adatok indexelése indexelővel az Azure Cognitive Searchben 

> [!IMPORTANT] 
> Sql API általában elérhető.
> A MongoDB API, a Gremlin API és a Cassandra API támogatása jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verzió funkció szolgáltatásszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Az [űrlap](https://aka.ms/azure-cognitive-search/indexer-preview)kitöltésével hozzáférést kérhet az előnézetekhez. A [REST API 2019-05-06-preview verziója](search-api-preview.md) előnézeti funkciókat biztosít. Jelenleg korlátozott a portál támogatása, és nincs .NET SDK-támogatás.

> [!WARNING]
> Csak a Cosmos DB-gyűjtemények [egy indexelési szabályzat](https://docs.microsoft.com/azure/cosmos-db/index-policy) [beállítása Konzisztens](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode) támogatja az Azure Cognitive Search. A Lusta indexelési házirenddel rendelkező gyűjtemények indexelése nem ajánlott, és hiányzó adatokat eredményezhetnek. Az indexelésletiltott gyűjtemények nem támogatottak.

Ez a cikk bemutatja, hogyan konfigurálhat egy Azure Cosmos DB [indexelő](search-indexer-overview.md) t a tartalom kinyerésére, és az Azure Cognitive Search kereshetővé. Ez a munkafolyamat létrehoz egy Azure Cognitive Search indexet, és betölti azt az Azure Cosmos DB-ből kinyert meglévő szöveggel. 

Mivel a terminológia zavaró lehet, érdemes megjegyezni, hogy az [Azure Cosmos DB indexelése](https://docs.microsoft.com/azure/cosmos-db/index-overview) és [az Azure Cognitive Search indexelése](search-what-is-an-index.md) különböző műveletek, egyedi az egyes szolgáltatások. Az Azure Cognitive Search indexelésének megkezdése előtt az Azure Cosmos DB-adatbázisnak már léteznie kell, és adatokat kell tartalmaznia.

Az Azure Cognitive Search Cosmos DB indexelője feltérképezheti a különböző protokollokon keresztül elérhető [Azure Cosmos DB-elemeket.](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items) 

+ Az [sql API](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference), amely általánosan elérhető, [használhatja](#cosmos-indexer-portal)a portál , [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations), vagy [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet) az adatforrás és az indexelő létrehozásához.

+ A [MongoDB API (előzetes verzió)](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)használatával vagy a [portálon,](#cosmos-indexer-portal) vagy a [REST API 2019-05-06-Preview-verzió](search-api-preview.md) az adatforrás és az indexelő létrehozásához.

+ Cassandra [API (előzetes verzió)](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction) és [Gremlin API (előzetes verzió)](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)esetén csak a [REST API 2019-05-06-preview verziójával](search-api-preview.md) hozhatja létre az adatforrást és az indexelőt.


> [!Note]
> Szavazhat a User Voice for the [Table API-ra,](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) ha szeretné, hogy az Azure Cognitive Search támogatja.
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>A portál használata

> [!Note]
> A portál jelenleg támogatja az SQL API-t és a MongoDB API-t (előzetes verzió).

Az Azure Cosmos DB-elemek indexelésének legegyszerűbb módja egy varázsló használata az [Azure Portalon.](https://portal.azure.com/) Az adatok mintavételezése és a metaadatok olvasása a tárolón, az [**Adatok importálása**](search-import-data-portal.md) varázsló az Azure Cognitive Search hozhat létre egy alapértelmezett index, a forrásmezők a cél index mezőket, és töltse be az indexet egyetlen műveletben. A forrásadatok méretétől és összetettségétől függően percek alatt teljes szöveges keresési indexet kaphat.

Azt javasoljuk, hogy használja ugyanazt a régiót vagy helyet az Azure Cognitive Search és az Azure Cosmos DB alacsonyabb késés és a sávszélesség díjak elkerülése érdekében.

### <a name="1---prepare-source-data"></a>1 - Forrásadatok előkészítése

Rendelkeznie kell egy Cosmos DB-fiókkal, egy Azure Cosmos DB-adatbázissal az SQL API-hoz, a MongoDB API-hoz (előzetes verzió) vagy a Gremlin API-hoz (előzetes verzió) és az adatbázis tartalmához.

Győződjön meg arról, hogy a Cosmos DB adatbázis adatokat tartalmaz. Az [Adatok importálása varázsló](search-import-data-portal.md) beolvassa a metaadatokat, és adatmintavételt hajt végre egy indexséma kikövetkeztetéséhez, de a Cosmos DB-ből is betölti az adatokat. Ha az adatok hiányoznak, a varázsló ezzel a hibával leáll: "Hiba az adatforrásból származó indexséma észlelésekor: Nem lehetett prototípus indexet készíteni, mert az "üres forrás" adatforrás nem adott vissza adatokat".

### <a name="2---start-import-data-wizard"></a>2 – Adatok importálása varázsló indítása

A varázslót az Azure Cognitive Search szolgáltatáslapján található parancssávról [indíthatja el,](search-import-data-portal.md) vagy ha cosmos DB SQL API-hoz csatlakozik, kattintson az **Azure Cognitive Search hozzáadása** elemre a Cosmos DB-fiók bal oldali navigációs **ablakának Beállítások** szakaszában.

   ![Adatok importálása parancs a portálon](./media/search-import-data-portal/import-data-cmd2.png "Az Adatok importálása varázsló indítása")

### <a name="3---set-the-data-source"></a>3 - Az adatforrás beállítása

Az **adatforráslapon** a forrásnak **Cosmos DB-nak**kell lennie, a következő specifikációkkal:

+ **A név** az adatforrás-objektum neve. Miután létrehozta, kiválaszthatja azt más számítási feladatokhoz.

+ **Cosmos DB-fiók** kell lennie az elsődleges vagy másodlagos `AccountEndpoint` kapcsolati `AccountKey`karakterlánc ot Cosmos DB, egy és egy . MongoDB-gyűjtemények esetén adja hozzá **az ApiKind=MongoDb-t** a kapcsolati karakterlánc végéhez, és pontosvesszővel válassza el a kapcsolati karakterlánctól. A Gremlin API és a Cassandra API esetében használja a [REST API utasításait.](#cosmosdb-indexer-rest)

+ **Az adatbázis** egy meglévő adatbázis a fiókból. 

+ **A gyűjtemény** dokumentumok tárolója. A dokumentumoknak létezniük kell ahhoz, hogy az importálás sikeres legyen. 

+ **A lekérdezés** üres lehet, ha az összes dokumentumot szeretné, ellenkező esetben olyan lekérdezést adhat meg, amely kijelöl egy dokumentum részhalmazát. **A lekérdezés** csak az SQL API-hoz érhető el.

   ![Cosmos DB adatforrás-definíciója](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB adatforrás-definíciója")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - A varázsló "Tartalom gazdagítása" oldalának kihagyása

Kognitív képességek hozzáadása (vagy gazdagítás) hozzáadása nem importálási követelmény. Ha csak egy adott szükség van [a ai-bővítés hozzáadása](cognitive-search-concept-intro.md) az indexelési folyamathoz, akkor hagyja ki ezt a lépést.

A lépés kihagyásához kattintson az oldal alján található kék gombokra a "Tovább" és a "Kihagyás" területen.

### <a name="5---set-index-attributes"></a>5 - Indexattribútumok beállítása

A **Tárgymutató** lapon meg kell jelennie az adattípusú mezők listájának és az indexattribútumok beállításához tartozó jelölőnégyzetek sorának. A varázsló metaadatok alapján és a forrásadatok mintavételezésével létrehozhat mezőket. 

Az attribútumoszlop tetején található jelölőnégyzetre kattintva tömegesen kiválaszthatja az attribútumokat. Válassza **a Beolvasható** és **a Kereshető** lehetőséget minden olyan mezőhöz, amelyet vissza kell küldeni egy ügyfélalkalmazásba, és teljes szöveges keresési feldolgozásnak kell alávetni. Észre fogja venni, hogy az egész számok nem teljes szövegvagy fuzzy kereshető (számok kiértékelése szó szerint, és gyakran hasznos a szűrők).

További információkért tekintse át az [indexattribútumok](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) és [a nyelvi elemzők](https://docs.microsoft.com/rest/api/searchservice/language-support) leírását. 

Szánjon egy kis időbe, hogy áttekintse a beállításokat. A varázsló futtatása után fizikai adatstruktúrák jönnek létre, és ezeket a mezőket nem szerkesztheti az összes objektum eldobása és újbóli létrehozása nélkül.

   ![Cosmos DB index definíciója](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Cosmos DB index definíciója")

### <a name="6---create-indexer"></a>6 - Indexelő létrehozása

Teljesen megadva, a varázsló három különböző objektumot hoz létre a keresési szolgáltatásban. Az adatforrás-objektum és az indexobjektum elnevezett erőforrásként kerül mentésre az Azure Cognitive Search szolgáltatásban. Az utolsó lépés egy indexelő objektumot hoz létre. Az indexelő elnevezése lehetővé teszi, hogy önálló erőforrásként létezzen, amelyet az ugyanabban a varázslósorozatban létrehozott index- és adatforrásobjektumtól függetlenül ütemezhet és kezelhet.

Ha nem ismeri az indexelők, *indexelő* egy erőforrás az Azure Cognitive Search, amely feltérképezi a kereshető tartalom külső adatforrása. Az Adatok **importálása** varázsló kimenete egy indexelő, amely feltérképezi a Cosmos DB adatforrását, kinyeri a kereshető tartalmat, és importálja azt egy indexbe az Azure Cognitive Search-en.

A következő képernyőképen látható az alapértelmezett indexelő konfiguráció. Az **indexelő** egyszeri futtatásához. Kattintson **a Küldés** gombra a varázsló futtatásához és az összes objektum létrehozásához. Az indexelés azonnal megkezdődik.

   ![Cosmos DB indexelő definíciója](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB indexelő definíciója")

A portállapokon figyelheti az adatimportálást. A végrehajtási értesítések jelzik az indexelési állapotot és a feltöltött dokumentumok nagy számára. 

Ha az indexelés befejeződött, a [Kereséskezelővel](search-explorer.md) lekérdezheti az indexet.

> [!NOTE]
> Ha nem látja a várt adatokat, előfordulhat, hogy több mezőhöz további attribútumokat kell beállítania. Törölje az imént létrehozott indexet és indexelőt, majd lépjen újra a varázslóba, és módosítsa az 5. 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API-k használata

A REST API segítségével indexelheti az Azure Cosmos DB-adatokat, az Azure Cognitive Search összes indexelőjének közös háromrészes munkafolyamata it: hozzon létre egy adatforrást, hozzon létre egy indexet, hozzon létre egy indexelőt. A Cosmos DB-ből történő adatkinyerés az Indexelő létrehozása kérés elküldésekor történik. Miután ez a kérés befejeződött, lesz egy lekérdezhető index. 

> [!NOTE]
> A Cosmos DB Gremlin API vagy a Cosmos DB Cassandra API-ból származó adatok indexeléséhez először hozzáférést kell kérnie a kapuzott előnézetekhez [az űrlap](https://aka.ms/azure-cognitive-search/indexer-preview)kitöltésével. A kérelem feldolgozása után utasításokat kap a [REST API 2019-05-06-Preview verziójának](search-api-preview.md) az adatforrás létrehozásához való használatához.

A cikk korábbi részeként megemlíti, hogy az [Azure Cosmos DB indexelése](https://docs.microsoft.com/azure/cosmos-db/index-overview) és [az Azure Cognitive Search indexelési](search-what-is-an-index.md) indexelés különböző műveletek. A Cosmos DB indexelés, alapértelmezés szerint az összes dokumentum automatikusan indexelt, kivéve a Cassandra API-t. Ha kikapcsolja az automatikus indexelést, a dokumentumok csak az önhivatkozásokon vagy a dokumentumazonosító használatával történő lekérdezéseken keresztül érhetők el. Az Azure Cognitive Search indexelése megköveteli, hogy a Cosmos DB automatikus indexelése be legyen kapcsolva az Azure Cognitive Search által indexelt gyűjteményben. Amikor feliratkozik a Cosmos DB Cassandra API indexelő előzetes verziójára, útmutatást kap a Cosmos DB indexelés beállításához.

> [!WARNING]
> Az Azure Cosmos DB a DocumentDB következő generációja. Korábban a **2017-11-11** API-verzióval használhatta a `documentdb` szintaxist. Ez azt jelentette, hogy az `cosmosdb` adatforrás `documentdb`típusát a vagy . A **2019-05-06-os API-verzióval** kezdve az Azure `cosmosdb` Cognitive Search API-k és a Portál is csak a jelen cikkben meghatározott szintaxist támogatja. Ez azt jelenti, hogy `cosmosdb` az adatforrás-típusnak kell, ha egy Cosmos DB-végponthoz szeretne csatlakozni.

### <a name="1---assemble-inputs-for-the-request"></a>1 - A kérelem bemenetének összeállítása

Minden kérelemhez meg kell adnia a szolgáltatás nevét és a rendszergazdai kulcsot az Azure Cognitive Search (a POST fejlécben), valamint a tárfiók nevét és kulcsblob storage. [A Postman](search-get-started-postman.md) segítségével HTTP-kérelmeket küldhet az Azure Cognitive Search szolgáltatásba.

Másolja a vágólapra a következő négy értéket, hogy beilleszthesse őket egy kérésbe:

+ Az Azure Cognitive Search szolgáltatás neve
+ Azure Cognitive Search felügyeleti kulcs
+ Cosmos DB kapcsolati karakterlánc

Ezeket az értékeket a portálon találja:

1. Az Azure Cognitive Search portállapjain másolja a keresési szolgáltatás URL-címét az Áttekintés lapról.

2. A bal oldali navigációs ablakban kattintson a **Kulcsok** elemre, majd másolja az elsődleges vagy másodlagos kulcsot (egyenértékűek).

3. Váltson át a Cosmos-tárfiók portállapjaira. A bal oldali navigációs ablak **Beállítások**területén kattintson a **Billentyűk gombra.** Ez a lap uri-t, két kapcsolati karakterláncot és két kulcskészletet tartalmaz. Másolja az egyik csatlakozási karakterláncot a Jegyzettömbbe.

### <a name="2---create-a-data-source"></a>2 - Adatforrás létrehozása

Az **adatforrás** határozza meg az indexelésre, hitelesítő adatokra és az adatok változásainak azonosítására szolgáló házirendeket (például a gyűjteményen belüli módosított vagy törölt dokumentumokat). Az adatforrás önálló erőforrásként van definiálva, így több indexelő is használhatja.

Adatforrás létrehozásához fogalmazzon meg egy POST-kérelmet:

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

A kérelem törzse tartalmazza az adatforrás-definíciót, amelynek a következő mezőket kell tartalmaznia:

| Mező   | Leírás |
|---------|-------------|
| **név** | Kötelező. Az adatforrás-objektumot jelképező nevet válassza. |
|**Típus**| Kötelező. Kell `cosmosdb`. |
|**Megbízólevél** | Kötelező. A Cosmos DB kapcsolati karakterláncának kell lennie.<br/>AZ SQL-gyűjtemények esetén a kapcsolati karakterláncok formátuma a következő:`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/>MongoDB-gyűjtemények esetén adja hozzá **az ApiKind=MongoDb-t** a kapcsolati karakterlánchoz:<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>Gremlin grafikonok és Cassandra táblák, iratkozzon fel a [kapuzott indexelő előnézeti](https://aka.ms/azure-cognitive-search/indexer-preview) hozzáférést kap az előnézet és a hitelesítő adatok formázásával kapcsolatos információkat.<br/><br/>Kerülje a végpont url-címében lévő portszámokat. Ha megadja a port számát, az Azure Cognitive Search nem lesz képes indexálni az Azure Cosmos DB-adatbázist.|
| **tároló** | A következő elemeket tartalmazza: <br/>**név**: Kötelező. Adja meg az indexelendő adatbázisgyűjtemény azonosítóját.<br/>**query**: Nem kötelező. Megadhat egy lekérdezést egy tetszőleges JSON-dokumentum összeolvasztásához egy olyan sík sémába, amelyet az Azure Cognitive Search indexelhet.<br/>A MongoDB API, A Gremlin API és a Cassandra API esetében a lekérdezések nem támogatottak. |
| **dataChangeDetectionPolicy** | Ajánlott. Lásd: [Módosított dokumentumok indexelése](#DataChangeDetectionPolicy) szakasz.|
|**dataDeletionDetectionPolicy** | Választható. Lásd: [Törölt dokumentumok indexelése](#DataDeletionDetectionPolicy) szakasz.|

### <a name="using-queries-to-shape-indexed-data"></a>Lekérdezések használata indexelt adatok formálásához
Sql-lekérdezést adhat meg a beágyazott tulajdonságok vagy tömbök összeolvasztására, a JSON-tulajdonságok kivetítésére és az indexelendő adatok szűrésére. 

> [!WARNING]
> Az egyéni lekérdezések nem támogatottak a **MongoDB API,** **a Gremlin API**és a **Cassandra API**esetében: `container.query` a paramétert null értékre kell állítani, vagy ki kell hagyni. Ha egyéni lekérdezést kell használnia, kérjük, tudassa velünk a [User Voice](https://feedback.azure.com/forums/263029-azure-search).

Példa dokumentum:

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

Összeolvasztási lekérdezés:

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
Vetítési lekérdezés:

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


Tömbösszeolvasztási lekérdezés:

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3 - Célkeresési index létrehozása 

[Hozzon létre egy cél Azure Cognitive Search index,](/rest/api/searchservice/create-index) ha még nem rendelkezik. A következő példa létrehoz egy indexet azonosítóval és leírásmezővel:

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

Győződjön meg arról, hogy a célindex sémája kompatibilis a forrás JSON-dokumentumok sémájával vagy az egyéni lekérdezés-vetület kimenetével.

> [!NOTE]
> Particionált gyűjtemények esetén az alapértelmezett dokumentumkulcs `_rid` az Azure Cosmos DB tulajdonsága, amelyre az Azure Cognitive Search automatikusan átnevez, `rid` mert a mezőnevek nem kezdődhetnek el aláhúzáskarakterrel. Az Azure Cosmos `_rid` DB-értékei érvénytelen karaktereket tartalmaznak az Azure Cognitive Search-kulcsokban. Ezért az `_rid` értékek Base64 kódolásúak.
> 
> MongoDB-gyűjtemények esetén az Azure Cognitive `_id` Search `id`automatikusan átnevezi a tulajdonságot a rendszerre.  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>JSON-adattípusok és az Azure Cognitive Search-adattípusok közötti leképezés
| JSON-adattípus | Kompatibilis célindexmezőtípusok |
| --- | --- |
| Logikai |Edm.Boolean, Edm.String |
| Egész számoknak tűnő számok |Edm.Int32, Edm.Int64, Edm.String |
| Lebegőpontoknak tűnő számok |Edm.Double, Edm.String |
| Sztring |Edm.String |
| Primitív típusok tömbjei, például ["a", "b", "c"] |Collection(Edm.String) |
| Dátumoknak tűnő karakterláncok |Edm.DateTimeOffset, Edm.String |
| GeoJSON objektumok, például { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| Egyéb JSON-objektumok |N/A |

### <a name="4---configure-and-run-the-indexer"></a>4 - Az indexelő konfigurálása és futtatása

Az index és az adatforrás létrehozása után készen áll az indexelő létrehozására:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

Ez az indexelő kétóránként fut (az ütemezési időköz "PT2H" értékre van állítva). Ha 30 percenként szeretne indexelőt futtatni, állítsa az intervallumot "PT30M" értékre. A legrövidebb támogatott időköz 5 perc. Az ütemezés nem kötelező – ha nincs megadva, az indexelő csak egyszer fut, amikor létrehozták. Az indexelőt azonban bármikor futtathatja igény szerint.   

Az Indexelő létrehozása API-val kapcsolatos további részletekért olvassa el az [Indexelő létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer)című.

Az indexelő-ütemezések meghatározásáról az [Azure Cognitive Search indexelőinek ütemezése](search-howto-schedule-indexers.md)című témakörben olvashat bővebben.

## <a name="use-net"></a>A .NET használata

Az általánosan elérhető .NET SDK teljes paritást az általánosan elérhető REST API-val. Azt javasoljuk, hogy tekintse át az előző REST API szakaszt a fogalmak, a munkafolyamat és a követelmények megismeréséhez. Ezután a következő .NET API referenciadokumentációban egy JSON indexelő felügyelt kódban való implementálódásához hivatkozhat.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>Módosított dokumentumok indexelése

Az adatváltozás-észlelési házirend célja a módosított adatelemek hatékony azonosítása. Jelenleg az egyetlen támogatott [`HighWaterMarkChangeDetectionPolicy`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.highwatermarkchangedetectionpolicy) szabályzat `_ts` az Azure Cosmos DB által biztosított (időbélyeg) tulajdonság használata, amely a következőképpen van megadva:

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

A szabályzat használata erősen ajánlott a jó indexelő teljesítmény biztosítása érdekében. 

Ha egyéni lekérdezést használ, győződjön `_ts` meg arról, hogy a tulajdonságot a lekérdezés vetíti ki.

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>Növekményes folyamat és egyéni lekérdezések

Az indexelés során a növekményes folyamat biztosítja, hogy ha az indexelő végrehajtását átmeneti hibák vagy végrehajtási időkorlát szakítja meg, az indexelő a következő futtatáskor folytathatja, ahelyett, hogy a teljes gyűjteményt újra indexelnie kellene a semmiből. Ez különösen fontos nagy gyűjtemények indexelésekén. 

Egyéni lekérdezés használata esetén a növekményes folyamat `_ts` engedélyezéséhez győződjön meg arról, hogy a lekérdezés az oszlop szerint rendezi az eredményeket. Ez lehetővé teszi az Azure Cognitive Search által a hibák jelenlétében való növekményes előrehaladás biztosításához használt rendszeres ellenőrzés-rámutatást.   

Bizonyos esetekben, még ha `ORDER BY [collection alias]._ts` a lekérdezés tartalmaz egy záradékot, az Azure `_ts`Cognitive Search nem következtet, hogy a lekérdezés a. Megállapíthatja az Azure Cognitive Search, `assumeOrderByHighWaterMarkColumn` hogy az eredmények a konfigurációs tulajdonság használatával vannak rendezve. A tipp megadásához hozza létre vagy frissítse az indexelőt az alábbiak szerint: 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>Törölt dokumentumok indexelése

Amikor a sorokat törli a gyűjteményből, általában ezeket a sorokat is törölni szeretné a keresési indexből. Az adattörlés-észlelési házirend célja a törölt adatelemek hatékony azonosítása. Jelenleg az egyetlen támogatott `Soft Delete` házirend a házirend (a törlés valamilyen jelzővel van megjelölve), amely a következőképpen van megadva:

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

Ha egyéni lekérdezést használunk, győződjön meg `softDeleteColumnName` arról, hogy a lekérdezés kivetíti a hivatkozott tulajdonságot.

A következő példa lágy törlési házirenddel rendelkező adatforrást hoz létre:

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

## <a name="next-steps"></a><a name="NextSteps"></a>További lépések

Gratulálunk! Megtanulta, hogyan integrálhatja az Azure Cosmos DB-t az Azure Cognitive Search alkalmazásával egy indexelő használatával.

* Ha többet szeretne megtudni az Azure Cosmos DB-ről, tekintse meg az [Azure Cosmos DB szolgáltatáslapját.](https://azure.microsoft.com/services/cosmos-db/)
* Ha többet szeretne megtudni az Azure Cognitive Search szolgáltatásról, olvassa el a [Keresési szolgáltatás lapot.](https://azure.microsoft.com/services/search/)
