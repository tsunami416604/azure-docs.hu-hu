---
title: JSON-Blobok indexelése az Azure Blob indexelő a teljes szöveges kereséshez – Azure Search
description: Azure JSON-Blobok bejárása a szöveges tartalomhoz a Azure Search blob indexelő használatával. Az indexelő automatizálja az adatfeldolgozást a kiválasztott adatforrásokhoz, például az Azure Blob Storage-hoz.
ms.date: 05/02/2019
author: HeidiSteen
manager: nitinme
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: d266f5edb85dd732cc39cfe98a64bee8019cdbd1
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656679"
---
# <a name="how-to-index-json-blobs-using-azure-search-blob-indexer"></a>JSON-Blobok indexelése Azure Search blob indexelő használatával
Ebből a cikkből megtudhatja, hogyan konfigurálhat egy Azure Search blob indexelő úgy, hogy kinyerje a JSON-dokumentumokból származó strukturált tartalmat az Azure Blob Storage-ban, és kereshetővé tegye Azure Search [](search-indexer-overview.md) Ez a munkafolyamat létrehoz egy Azure Search indexet, és betölti azt a JSON-blobokból kinyert meglévő szöveggel. 

A JSON-tartalom [](#json-indexer-portal)indexeléséhez használhatja a portált, a [REST API-kat](#json-indexer-rest)vagy a [.net SDK](#json-indexer-dotnet) -t is. Az összes megközelítés esetében gyakori, hogy a JSON-dokumentumok egy Azure Storage-fiókban található blob-tárolóban találhatók. A JSON-dokumentumok más nem Azure-platformokról való leküldésével [](search-what-is-data-import.md)kapcsolatos útmutatásért lásd: adatimportálás Azure Searchban.

Az Azure Blob Storage-ban található JSON-Blobok jellemzően egyetlen JSON-dokumentum vagy JSON-entitások gyűjteményei. A JSON-gyűjtemények esetében a blob jól formázott JSON-elemek tömbjét is tartalmazhatja. A Blobok több, sortöréssel elválasztott egyéni JSON-entitásból is állhatnak. A Azure Searchban található blob indexelő elemezheti az ilyen szerkezeteket, attól függően, hogy a **parsingMode** paraméter hogyan van megadva a kérelemben.

Az összes JSON-elemzési mód`json`( `jsonArray`, `jsonLines`,) már általánosan elérhető. 

> [!NOTE]
> Kövesse az indexelő konfigurációs javaslatait az [egy-a-többhöz indexelésben](search-howto-index-one-to-many-blobs.md) , hogy egyetlen Azure-blobból végezzen több keresési dokumentumot.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>A portál használata

A JSON-dokumentumok indexelésének legegyszerűbb módja a [Azure Portal](https://portal.azure.com/)varázsló használata. Az Azure Blob-tárolóban található metaadatok elemzésével az [**adatok importálása**](search-import-data-portal.md) varázsló létrehozhat egy alapértelmezett indexet, leképezheti a forrás mezőket a célként megadott index mezőibe, és betöltheti az indexet egyetlen művelettel. A forrásadatok méretétől és összetettségének függvényében percek alatt működőképes teljes szöveges keresési indexszel rendelkezhet.

Azt javasoljuk, hogy ugyanazt az Azure-előfizetést használja mind a Azure Search, mind az Azure Storage-hoz, lehetőleg ugyanabban a régióban.

### <a name="1---prepare-source-data"></a>1 – forrásadatok előkészítése

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/).

1. [Hozzon létre egy blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) -tárolót, amely az adatait tartalmazza. A nyilvános hozzáférési szint bármelyik érvényes értékére beállítható.

Az adatimportálás varázslóban szüksége lesz a Storage-fiók nevére, a tároló nevére és egy hozzáférési kulcsra az adatlekérdezéshez.

### <a name="2---start-import-data-wizard"></a>2 – az adatimportálás megkezdése varázsló

A Azure Search szolgáltatás áttekintés lapján elindíthatja [a varázslót](search-import-data-portal.md) a parancssorból, vagy a Storage-fiók bal oldali navigációs paneljének **Blob service** szakaszában a **Azure Search hozzáadása** lehetőségre kattintva.

   ![Adatimportálási parancs a portálon](./media/search-import-data-portal/import-data-cmd2.png "Az adatimportálás varázsló elindítása")

### <a name="3---set-the-data-source"></a>3 – az adatforrás beállítása

Az adatforrás lapon a forrásnak **Azure Blob Storagenak**kell lennie, a következő jellemzőkkel:

+ **A kinyerni kívánt adatoknak** *tartalomnak és metaadatoknak*kell lenniük. Ha ezt a lehetőséget választja, a varázsló lekövetkeztet egy index-sémát, és leképezi a mezőket az importáláshoz.
   
+ Az **elemzési módot** *JSON*-ra, JSON- *tömbre* vagy *JSON-sorra*kell beállítani. 

  A *JSON* minden blobot egyetlen keresési dokumentumként fogalmaz meg, amely független elemként jelenik meg a keresési eredmények között. 

  A *JSON-tömb* olyan blobokat tartalmaz, amelyek jól formázott JSON-adatmennyiséget tartalmaznak – a jól formázott JSON egy objektum tömbje, vagy egy olyan tulajdonság, amely objektumok tömbje, és azt szeretné, hogy az egyes elemek különálló, független keresési dokumentumként legyenek kialakítva. Ha a Blobok bonyolultak, és nem választja a *JSON* -tömböt, a teljes blob egyetlen dokumentumként lesz betöltve.

  A *JSON-sorok* a több JSON-entitásból álló, új vonallal elválasztott Blobok, ahol az egyes entitásokat önálló, független keresési dokumentumként kell összeállítani. Ha a Blobok bonyolultak, és nem választja a *JSON-vonalak* elemzési módját, akkor a teljes blob egyetlen dokumentumként lesz betöltve.
   
+ A **Storage** -tárolónak meg kell adnia a Storage-fiókot és-tárolót, vagy egy olyan kapcsolódási karakterláncot, amely a tárolóra van feloldva. A Blob service portál lapon lekérheti a kapcsolatok karakterláncait.

   ![BLOB-adatforrás definíciója](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 – a varázsló "kognitív keresés hozzáadása" lapjának kihagyása

A kognitív képességek hozzáadásához nem szükséges a JSON-dokumentumok importálása. Ha nincs kifejezetten szüksége az indexelési folyamat [Cognitive Services API-k és átalakítására](cognitive-search-concept-intro.md) , ugorja át ezt a lépést.

A lépés kihagyásához először ugorjon a következő lapra.

   ![A következő oldal gomb a kognitív kereséshez](media/search-get-started-portal/next-button-add-cog-search.png)

Ebből az oldalból kihagyhatja az index testreszabását.

   ![A kognitív képességek lépés kihagyása](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 – index attribútumainak beállítása

Az **index** lapon meg kell jelennie egy adattípusú mezők listájának, valamint egy sor jelölőnégyzet az index attribútumainak beállításához. A varázsló metaadatok alapján és a forrásadatok mintavételezésével hozhatja meg a mezők listáját. 

Az attribútumok tömeges kiválasztásához kattintson az attribútum oszlop tetején található jelölőnégyzetre. Válassza a beolvasható és **kereshető** lehetőséget minden olyan mező esetében, amelyet vissza kell adni egy ügyfélalkalmazás számára, és a teljes szöveges keresés feldolgozására is érvényes. Megfigyelheti, hogy az egész számok nem teljes szöveges vagy zavaros kereshetők (a számok szó szerint vannak kiértékelve, és gyakran hasznosak a szűrőkben).

További információkért tekintse át az [index attribútumainak](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) és a [nyelvi elemzők](https://docs.microsoft.com/rest/api/searchservice/language-support) leírását. 

Szánjon egy kis időt a kiválasztott elemek áttekintésére. A varázsló futtatása után a rendszer létrehozza a fizikai adatstruktúrákat, és nem tudja szerkeszteni ezeket a mezőket az összes objektum eldobása és újbóli létrehozása nélkül.

   ![BLOB-index definíciója](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 – indexelő létrehozása

Teljes mértékben meg van adva, a varázsló három különböző objektumot hoz létre a keresési szolgáltatásban. Az adatforrás-objektumok és az index objektumok elnevezett erőforrásokként lesznek mentve a Azure Search szolgáltatásban. Az utolsó lépés egy indexelő objektumot hoz létre. Az indexelő elnevezése lehetővé teszi, hogy önálló erőforrásként is használható legyen, amelyet az index és az adatforrás objektumtól függetlenül ütemezhet és kezelhet, amely ugyanabban a varázsló-sorozatban jön létre.

Ha nem ismeri az indexelő funkciót, az *Indexelő* egy olyan erőforrás, Azure Search, amely egy külső adatforrást mutat be a kereshető tartalomhoz. Az adatimportálás varázsló kimenete egy indexelő, amely feltérképezi a JSON-adatforrást, Kinyeri a kereshető tartalmat, és importálja azt egy Azure Search indexbe.

   ![BLOB indexelő definíciója](media/search-howto-index-json/import-wizard-json-indexer.png)

A varázsló futtatásához és az összes objektum létrehozásához kattintson **az OK** gombra. Az indexelés azonnal megkezdődik.

Az adatimportálást a portál oldalain lehet figyelni. Az állapotjelző értesítések az indexelési állapotot és a feltöltött dokumentumok számát jelzik. 

Az indexelés befejezésekor a [Search Explorer](search-explorer.md) használatával kérdezheti le az indexet.

> [!NOTE]
> Ha nem látja a várt adattípust, előfordulhat, hogy további attribútumokat kell beállítania további mezőknél. Törölje az imént létrehozott indexet és indexelő, és ismételje meg a varázslót, és módosítsa az index attribútumainak kiválasztását az 5. lépésben. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API-k használata

A JSON-Blobok indexeléséhez használhatja a REST APIt, amely a következő három részből álló munkafolyamatot követi az összes indexelő esetében Azure Search: hozzon létre egy adatforrást, hozzon létre egy indexet, hozzon létre egy indexet. A blob Storage-ból származó adatok kinyerése akkor történik meg, amikor beküldi az index-létrehozási kérelmet. A kérés befejezését követően lekérdezhető index jelenik meg. 

A szakasz végén található [Rest-mintakód](#rest-example) áttekintésével megtudhatja, hogyan hozhat létre mindhárom objektumot. Ez a szakasz a JSON- [elemzési üzemmódok](#parsing-modes), az [egyes Blobok](#parsing-single-blobs), a [JSON-tömbök](#parsing-arrays)és a [beágyazott tömbök](#nested-json-arrays)részleteit is tartalmazza.

A kód alapú JSON-indexeléshez a [Poster](search-get-started-postman.md) és a REST API használatával hozza létre ezeket az objektumokat:

+ [index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [adatforrás](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

A műveletek sorrendje megköveteli, hogy az objektumokat ebben a sorrendben hozza létre és hívja meg. A portál munkafolyamatával ellentétben a kód megközelítéséhez egy elérhető indexre van szükség, amely elfogadja az indexelő kéréssel elküldhető JSON-dokumentumokat.

Az Azure Blob Storage-ban található JSON-Blobok jellemzően egyetlen JSON-dokumentum vagy egy JSON "tömb". A Azure Searchban található blob-indexelő elemezheti az építkezést, attól függően, hogy miként állítja be a **parsingMode** paramétert a kérelemre.

| JSON-dokumentum | parsingMode | Leírás | Rendelkezésre állás |
|--------------|-------------|--------------|--------------|
| Egy blob | `json` | A JSON-blobokat egyetlen darab szövegként elemzi. Minden JSON-blob egyetlen Azure Search dokumentum lesz. | Általánosan elérhető a [Rest](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API-ban és a [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK-ban is. |
| Több blobon | `jsonArray` | Egy JSON-tömböt elemez a blobban, ahol a tömb minden eleme külön Azure Search dokumentum lesz.  | Általánosan elérhető a [Rest](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API-ban és a [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK-ban is. |
| Több blobon | `jsonLines` | Egy olyan blobot elemez, amely több JSON-entitást (egy "tömböt") tartalmaz egy sortöréssel elválasztva, ahol minden entitás külön Azure Search dokumentum lesz. | Általánosan elérhető a [Rest](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API-ban és a [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK-ban is. |

### <a name="1---assemble-inputs-for-the-request"></a>1 – bemenetek összegyűjtése a kérelemhez

Minden kérelem esetében meg kell adnia a szolgáltatás nevét és a rendszergazdai kulcsot a Azure Search (a POST fejlécben), valamint a blob Storage-hoz tartozó Storage-fiók nevét és kulcsát. A Poster [](search-get-started-postman.md) használatával http-kéréseket küldhet a Azure Searchnak.

Másolja a következő négy értéket a Jegyzettömbbe, hogy beillessze őket egy kérelembe:

+ Azure Search szolgáltatás neve
+ Azure Search rendszergazdai kulcs
+ Azure Storage-fiók neve
+ Azure Storage-fiók kulcsa

Ezeket az értékeket a portálon találja:

1. A Azure Search portáljának oldalain másolja a keresési szolgáltatás URL-címét az Áttekintés lapról.

2. A bal oldali navigációs ablaktáblán kattintson a **kulcsok** elemre, majd másolja az elsődleges vagy a másodlagos kulcsot (ezek egyenértékűek).

3. Váltson a Storage-fiókjához tartozó portál oldalaira. A bal oldali navigációs ablaktábla **Beállítások**területén kattintson a **hozzáférési kulcsok**elemre. Ezen a lapon a fiók neve és kulcsa is szerepel. Másolja a Storage-fiók nevét és az egyik kulcsot a Jegyzettömbbe.

### <a name="2---create-a-data-source"></a>2 – adatforrás létrehozása

Ez a lépés az indexelő által használt adatforrás-kapcsolatok adatait tartalmazza. Az adatforrás egy megnevezett objektum Azure Searchban, amely megőrzi a kapcsolatok adatait. Az adatforrás típusa, `azureblob`amely meghatározza, hogy az indexelő milyen adatkiemelési viselkedéseket hív meg. 

Adjon meg érvényes értékeket a szolgáltatásnév, a rendszergazdai kulcs, a Storage-fiók és a fiók kulcsa helyőrzők helyett.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 – cél keresési index létrehozása 

Az indexelő egy index sémával vannak párosítva. Ha az API-t (nem pedig a portált) használja, előre készítse el az indexet, hogy az indexelő műveletnél megadható legyen.

Az index a Azure Search kereshető tartalmait tárolja. Index létrehozásához adjon meg egy sémát, amely meghatározza a keresési élményt formáló dokumentum, attribútum és egyéb szerkezetek mezőit. Ha olyan indexet hoz létre, amelynek neve és adattípusa megegyezik a forrással, az indexelő megegyezik a forrás és a cél mezővel, így a munka megtakarításával a mezők explicit módon képezhetők le.

Az alábbi példa egy [create index](https://docs.microsoft.com/rest/api/searchservice/create-index) -kérést mutat be. Az index egy kereshető `content` mezővel fog rendelkezni, amely a blobokból kinyert szöveget tárolja:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 – az indexelő konfigurálása és futtatása

Az indexekhez és az adatforrásokhoz hasonlóan az indexelő is egy elnevezett objektum, amelyet Ön a Azure Search szolgáltatásban hoz létre és használ fel. Az indexelő létrehozásához egy teljesen meghatározott kérelem a következőképpen jelenhet meg:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Az indexelő konfigurációja a kérelem törzsében található. Egy adatforrást és egy olyan üres célértéket igényel, amely már létezik a Azure Searchban. 

Az ütemterv és a paraméterek megadása nem kötelező. Ha kihagyja őket, az indexelő azonnal fut az elemzési `json` mód használatával.

Az adott indexelő nem tartalmaz mező-hozzárendelést. Az indexelő definícióján belül kihagyhatja a **mezők hozzárendeléseit** , ha a forrás JSON-dokumentum tulajdonságai egyeznek a cél keresési index mezőivel. 


### <a name="rest-example"></a>REST-példa

Ez a szakasz az objektumok létrehozásához használt összes kérelem emlékeztetője. Az összetevőkről a jelen cikk előző részeiben talál további információt.

### <a name="data-source-request"></a>Adatforrás-kérelem

Minden indexelő olyan adatforrás-objektumot igényel, amely kapcsolódási adatokat biztosít a meglévő adatokhoz. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Indexelési kérelem

Az összes indexelő megköveteli az adatfogadásra szolgáló cél indexet. A kérelem törzse meghatározza az index sémáját, amely a mezőkből áll, és a kívánt viselkedést támogatja egy kereshető indexben. Az indexnek üresnek kell lennie az indexelő futtatásakor. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Indexelő kérelme

Ez a kérelem egy teljesen meghatározott indexelő jelenít meg. Ide tartoznak a mező-hozzárendelések, amelyek az előző példákban kimaradtak. Ne felejtse el, hogy az "Schedule", a "parameters" és a "fieldMappings" nem kötelező, feltéve, hogy elérhető az alapértelmezett érték. Az "ütemterv" kihagyása azt eredményezi, hogy az indexelő azonnal fut. A "parsingMode" érték kihagyása esetén az index a "JSON" alapértelmezett értéket használja.

Az indexelő létrehozása Azure Search eseményindítók adatimportálásakor. Azonnal fut, és azt követően egy ütemezett időpontban, ha már biztosított egyet.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>A .NET SDK használata

A .NET SDK-val teljes paritású a REST API. Javasoljuk, hogy tekintse át az előző REST API szakaszt a fogalmak, a munkafolyamatok és a követelmények megismeréséhez. A következő .NET API-referenciák dokumentációjában a JSON-indexelő implementálása felügyelt kódban végezhető el.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Elemzési módok

A JSON-Blobok több űrlapot is tartalmazhatnak. A JSON-indexelő **parsingMode** paramétere határozza meg, hogyan történik a JSON-blob tartalmának elemzése és strukturálása egy Azure Search indexben:

| parsingMode | Leírás |
|-------------|-------------|
| `json`  | Az egyes Blobok indexelése egyetlen dokumentumként. Ez az alapértelmezett érték. |
| `jsonArray` | Akkor válassza ezt a módot, ha a Blobok JSON-tömbökből állnak, és a tömb minden eleméhez külön dokumentumra lesz szükség a Azure Searchban. |
|`jsonLines` | Akkor válassza ezt a módot, ha a Blobok több JSON-entitásból állnak, amelyek új vonallal vannak elválasztva, és minden entitásnak külön dokumentumnak kell lennie Azure Searchban. |

A dokumentumok egyetlen elemként is megtekinthetők a keresési eredményekben. Ha azt szeretné, hogy a tömb minden eleme egy független elemként jelenjen meg a keresési eredmények között, akkor használja `jsonArray` a `jsonLines` vagy a lehetőséget a megfelelő módon.

Az indexelő definíciójában igény szerint [mező](search-indexer-field-mappings.md) -hozzárendeléseket is használhat, hogy kiválassza a forrás JSON-dokumentumának a cél keresési indexének feltöltéséhez használt tulajdonságokat. Az `jsonArray` elemzési mód esetében, ha a tömb alacsonyabb szintű tulajdonságként létezik, beállíthat egy dokumentum gyökerét, amely azt jelzi, hogy a tömb hová kerül a blobon belül.

> [!IMPORTANT]
> A `json` `jsonArray` vagy az`jsonLines` elemzési mód használatakor Azure Search feltételezi, hogy az adatforrásban lévő összes blob JSON-t tartalmaz. Ha a JSON-és a nem JSON-Blobok kombinációját is támogatnia kell ugyanabban az adatforrásban, tudassa velünk a [UserVoice webhelyen](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Egyszeri JSON-Blobok elemzése

Alapértelmezés szerint a [Azure Search blob indexelő](search-howto-indexing-azure-blob-storage.md) a JSON-blobokat egyetlen darab szövegként elemzi. Gyakran szeretné megőrizni a JSON-dokumentumok szerkezetét. Tegyük fel például, hogy rendelkezik a következő JSON-dokumentummal az Azure Blob Storage-ban:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

A blob indexelő a JSON-dokumentumot egyetlen Azure Search dokumentumba elemzi. Az indexelő betölti az indexet a "text", "datePublished" és "Tags" kifejezéssel a forrástól az azonos névvel ellátott és beírt cél index mezőkkel.

Ahogy említettük, a mező-hozzárendelések nem szükségesek. A "text", "datePublished" és "Tags" mezőkkel rendelkező indexek esetében a blob indexelő a kérelemben található mező-hozzárendelés nélkül következtetheti ki a megfelelő leképezést.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>JSON-tömbök elemzése

Azt is megteheti, hogy a JSON Array kapcsolót használja. Ez a beállítás akkor hasznos, ha a Blobok *jól FORMÁZOTT JSON-objektumokat*tartalmazó tömböt tartalmaznak, és azt szeretné, hogy minden elem külön Azure Search dokumentum legyen. Például a következő JSON-blob esetében a Azure Search indexet három külön dokumentummal töltheti fel, amelyek mindegyike "id" és "text" mezőket tartalmaz.  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

JSON-tömb esetén az indexelő definíciójának az alábbi példához hasonlóan kell kinéznie. Figyelje meg, hogy a parsingMode paraméter `jsonArray` határozza meg az elemzőt. A megfelelő elemző megadása és a megfelelő adatok bevitele a JSON-Blobok indexelésének egyetlen két tömb-specifikus követelménye.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Figyelje meg, hogy a mező-hozzárendelések nem hagyhatók el. Az "id" és "text" mezőket tartalmazó indexek feltételezik, hogy a blob-indexelő a megfelelő leképezést explicit mező-hozzárendelési lista nélkül következteti ki.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Beágyazott tömbök elemzése
A beágyazott elemeket tartalmazó `documentRoot` JSON-tömbök esetében megadható, hogy többszintű struktúrát jelezzen. Ha például a Blobok így néznek ki:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Ezzel a konfigurációval indexelheti a `level2` tulajdonságban található tömböt:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>Blobok elemzése a sortörésekkel elválasztva

Ha a blob egy sortöréssel elválasztott több JSON-entitást tartalmaz, és azt szeretné, hogy minden elem külön Azure Search dokumentum legyen, választhat a JSON-sorok lehetőség közül. Ha például a következő blob (három különböző JSON-entitás) van megadva, feltöltheti Azure Search indexét három külön dokumentummal, amelyek mindegyike "id" és "text" mezőket tartalmaz.

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

A JSON-sorok esetében az indexelő definíciójának az alábbi példához hasonlóan kell kinéznie. Figyelje meg, hogy a parsingMode paraméter `jsonLines` határozza meg az elemzőt. 

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Azt is figyelje meg, hogy a mező-hozzárendelések kihagyhatók, `jsonArray` hasonlóan az elemzési módhoz.

## <a name="add-field-mappings"></a>Mező-hozzárendelések hozzáadása

Ha a forrás-és a célként megadott mezők nem tökéletesen illeszkednek, meghatározhatja a mezők leképezése szakaszt a kérelem törzsében explicit mező – mező típusú társításokhoz.

Jelenleg Azure Search nem tud közvetlenül indexelni tetszőleges JSON-dokumentumokat, mert csak primitív adattípusokat, karakterlánc-tömböket és GeoJSON-pontokat támogat. A **mező** -hozzárendelések segítségével azonban kiválaszthatja a JSON-dokumentum részeit, és "emelheti" azokat a keresési dokumentum legfelső szintű mezőibe. A mező-hozzárendelésekkel kapcsolatos alapvető tudnivalókat lásd: [Azure Search indexek mezőinek leképezése](search-indexer-field-mappings.md).

Tekintse át a példában szereplő JSON-dokumentumot:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Tegyük fel, hogy a keresési index a `text` következő mezőkkel `date` rendelkezik: `Edm.DateTimeOffset` `Edm.String`típus, `tags` típusú és `Collection(Edm.String)`típusú. Figyelje meg a "datePublished" közötti eltérést az index forrásában és `date` mezőjében. A JSON a kívánt alakzattá való leképezéséhez használja a következő mező-hozzárendeléseket:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

A leképezések mezőinek neve a [JSON-mutató](https://tools.ietf.org/html/rfc6901) jelölésének használatával van megadva. A továbbítási perjelet a JSON-dokumentum gyökerére való hivatkozással kezdheti meg, majd kiválaszthatja a kívánt tulajdonságot (tetszőleges szintű beágyazás esetén) a perjelek közötti kétirányú útvonal használatával.

Az egyes tömb elemeit nulla alapú index használatával is megtekintheti. Ha például a fenti példában szereplő "címkék" tömb első elemét szeretné kiválasztani, használja a következőhöz hasonló mezőt:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Ha egy mező-hozzárendelési útvonalban található forrás mező neve olyan tulajdonságra hivatkozik, amely nem szerepel a JSON-ban, akkor a leképezés hiba nélkül kimarad. Ez azért van így, hogy a dokumentumok más sémával is használhatók legyenek (amely gyakori használati eset). Mivel nincs érvényesítés, ügyelnie kell arra, hogy elkerülje az elírásokat a mező-hozzárendelési specifikációban.
>
>

## <a name="see-also"></a>Lásd még

+ [Indexelő a Azure Search](search-indexer-overview.md)
+ [Az Azure Blob Storage indexelése Azure Search](search-howto-index-json-blobs.md)
+ [CSV-Blobok indexelése Azure Search blob indexelő](search-howto-index-csv-blobs.md)
+ [Oktatóanyag: Részben strukturált adatok keresése az Azure Blob Storage-ból](search-semi-structured-data.md)
