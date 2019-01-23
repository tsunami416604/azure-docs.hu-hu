---
title: JSON-blobok indexelése az Azure Blob-indexelő az Azure Search – a teljes szöveges keresés
description: Feltérképezi az Azure JSON-blobok az Azure Search Blob indexelőjével szöveges tartalommal. Az indexelők automatizálni adatbetöltés a kijelölt adatforrásokhoz, például az Azure Blob storage.
ms.date: 12/21/2018
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: cafb48f28e38794ce0757d50a5d87432b237e17c
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54467162"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>JSON-blobok indexelése az Azure Search Blob indexelőjével
Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Search blob indexelőjével strukturálatlan tartalom használatával nyerhetők JSON-blobok, az Azure Blob storage-ban.

Használhatja a [portál](#json-indexer-portal), [REST API-k](#json-indexer-rest), vagy [.NET SDK-val](#json-indexer-dotnet) a JSON-tartalom indexelése. Az összes megközelítések közös egy Azure Storage-fiókban található blob-tárolóra található JSON-dokumentumok. A JSON-dokumentumok küld más-Azure platformról útmutatásért lásd: [adatok importálása az Azure Search](search-what-is-data-import.md).

Az Azure Blob storage-ban JSON-blobok jellemzően egyetlen JSON-dokumentumok és a egy JSON-tömböt. Az az Azure Search blob indexelőjével vagy konstrukció, attól függően, hogyan állíthatja tudja elemezni a **parsingMode** paraméter a kérésre.

> [!IMPORTANT]
> JSON blob-indexelés általánosan elérhető, de JsonArray elemzés nyilvános előzetes verzióban érhető el, és nem éles környezetekben használható. További információkért lásd: [REST api-version = 2017-11-11-Preview](search-api-2017-11-11-preview.md). 

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>A portál használata

A legegyszerűbb módszer a indexeli a JSON-dokumentumokat, hogy a varázsló használata a [az Azure portal](https://portal.azure.com/). A metaadatokat az Azure blob-tárolóban, elemzés a [ **adatimportálás** ](search-import-data-portal.md) varázslóval hozzon létre egy alapértelmezett indexet, leképezik a forrásmezőket cél index mezőire és betölteni az indexet egy művelettel. A mérete és összetettsége forrásadatok működési teljes szöveges keresési index lehet percek alatt.

### <a name="1---prepare-source-data"></a>1 – forrásadatok előkészítése

Azure storage-fiókkal, a Blob storage és a egy JSON-dokumentumokat tároló kell rendelkeznie. Ha ismeri ezeket a feladatokat bármelyikével, tekintse át az "Azure Blob service és a terhelés a Mintaadatok beállítása" előfeltétel a a [cognitive search-quickstart](cognitive-search-quickstart-blob.md#set-up-azure-blob-service-and-load-sample-data).

### <a name="2---start-import-data-wizard"></a>2 – adatok importálása varázsló indítása

Is [varázsló elindításához](search-import-data-portal.md) az Azure Search szolgáltatás lapján, vagy kattintson a parancssávon **Azure Search hozzáadása** a a **Blob service** szakasz a storage-fiók bal oldali navigációs ablaktáblán.

### <a name="3---set-the-data-source"></a>3 – az adatforrás beállításához

Az a **adatforrás** a forrás oldalon kell lennie **Azure Blob Storage**, az alábbi beállításokkal:

+ **Kinyerni kívánt adatok** kell *tartalom és metaadatok*. Ez a beállítás lehetővé teszi, hogy a varázsló következtetni és az importálás a mezők leképezése.
   
+ **Elemzési mód** értékre kell állítani *JSON* vagy *JSON-tömböt*. 

  *JSON* articulates minden egyes blob-dokumentumként egyetlen keresési, a keresési eredmények között egy független elemként jelenik meg. 

  *JSON-tömböt* van, a blobok mikroszolgáltatásokból álló, több olyan elemet, ahol azt szeretné, hogy minden egyes lehet különálló, független keresési dokumentum csuklós elemet. Ha a blobok összetettek, és ne adjon *JSON-tömböt* a teljes blob eseménystreamként egyetlen dokumentum.
   
+ **A tároló** meg kell adnia a tárfiók és tároló, vagy egy kapcsolati karakterláncot, amelyet a tárolóban. Kapcsolati karakterláncok Blob szolgáltatás portáloldalán kérheti le.

   ![BLOB adatforrás-definíciót](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 – kihagyja a varázslóban a "Hozzáadás cognitive search" lap

Kognitív képességek hozzáadása már nem szükséges JSON-dokumentum importáláshoz. Kivéve, ha egy adott kell [közé tartozik a Cognitive Services API-k és átalakításokat](cognitive-search-concept-intro.md) az indexelési folyamat kell ezt a lépést kihagyhatja.

A lépés kihagyásához kattintson a következő oldalon **célindex testreszabása**.

### <a name="5---set-index-attributes"></a>5 – set indexattribútumokat

Az a **Index** oldalon azt kell látnia, a mezők listája adattípus és a egy sorozatát jelölőnégyzetek az index attribútumainak beállítása. A varázsló egy alapértelmezett indexet, metaadatokat és a forrásadatok mintavétellel alapján hozhatja létre. 

Az alapértelmezett értékeket gyakran előállít egy közös üzemeltetése megoldást: Jelölje ki a következő mezőt: (karakterláncként cast) szolgál a kulcsot vagy a dokumentum-azonosító egyedi módon azonosítja az egyes dokumentumot, valamint a mezőket, amely jól használható teljes szöveges keresés és a egy eredményhalmazban lekérhető. Blobok a `content` mező kereshető tartalom a legjobb lehetőség.

Elfogadhatja az alapértelmezett beállításokat, vagy tekintse át a leírása [indexattribútumok](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) és [nyelvi elemzők](https://docs.microsoft.com/rest/api/searchservice/language-support) felülbírálás vagy kiegészítik a kezdeti értékekre. 

Tekintse át a választásait, néhány percet vesz igénybe. A varázsló futtatása után fizikai datové struktury hozhatók létre, és nem lehet ezen mezők szerkesztése nélkül elvetését, majd újra létre kellene hoznia minden objektumot.

   ![BLOB index definíciója](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 – az indexelő létrehozása

Teljes körűen megadva, a varázsló létrehoz három különböző objektumok a search szolgáltatás. Egy adatforrás-objektum és az index objektum az Azure Search szolgáltatás az elnevezett erőforrásként kerülnek mentésre. Az utolsó lépés egy indexelő objektumot hoz létre. Az indexelő elnevezési lehetővé teszi, hogy létezik, amely ütemezett, és az index és az adatforrás-objektum, ugyanabban a sorrendben varázsló létrehozott függetlenül kezelheti önálló erőforrásként.

Ha nem ismeri a indexelők esetében, egy *indexelő* egy erőforrás az Azure Search szolgáltatásban, amely feltérképezi a kereshető tartalmak egy külső adatforrást. A kimenetét a **adatimportálás** varázsló használata egy indexelőt, feltérképezi a JSON-adatforrást, kinyeri a kereshető tartalmak és importálja azt az Azure Search-indexbe.

   ![BLOB indexelő definíciója](media/search-howto-index-json/import-wizard-json-indexer.png)

Kattintson a **OK** futtassa a varázslót, és az összes objektum létrehozásához. Indexelő azonnal kezdődik.

Adatok importálása a portál oldalain követheti nyomon. Folyamatértesítéseinek jelzik, hogy az indexelő állapotának és hány dokumentumok feltöltésekor. Indexelő befejeződése után használhatja [keresési ablak](search-explorer.md) lekérdezheti az indexét.

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API-k használata

JSON-blobok indexelése hasonlít a rendszeres dokumentum kinyerése a közös Háromrészes munkafolyamat az Azure Search szolgáltatásban az összes indexelőre: hozzon létre egy adatforrást, hozzon létre egy index, indexelő létrehozása.

Indexelő kódalapú JSON, használhatja a REST API API-khoz, [indexelők](https://docs.microsoft.com/rest/api/searchservice/create-indexer), [adatforrások](https://docs.microsoft.com/rest/api/searchservice/create-data-source), és [indexek](https://docs.microsoft.com/rest/api/searchservice/create-index). Ellentétben a portál varázsló egy kódot megközelítés is rendelkeznie kell egy index helyben, készen áll a küldésekor, fogadja el a JSON-dokumentumok a **indexelő létrehozása** kérelmet.

Az Azure Blob storage-ban JSON-blobok jellemzően egyetlen JSON-dokumentumok és a egy JSON-tömböt. Az az Azure Search blob indexelőjével vagy konstrukció, attól függően, hogyan állíthatja tudja elemezni a **parsingMode** paraméter a kérésre.

| JSON-dokumentumok | parsingMode | Leírás | Rendelkezésre állás |
|--------------|-------------|--------------|--------------|
| Egy blob egy | `json` | Elemzi a JSON-blobok, a szöveg egy olyan adattömb. Egyes JSON-blobok az Azure Search egyetlen dokumentum válik. | Általánosan elérhető a [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) és [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) API-k. |
| Több blobonkénti | `jsonArray` | Elemzi a blobban, ahol a tömb egyes elemei lesz-e egy külön Azure Search-dokumentum egy JSON-tömböt.  | Általánosan elérhető a [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) és [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) API-k. |


### <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása

Az első lépés, hogy az indexelő által használt adatforrás kapcsolati adatait adja meg. Az adatforrás típusa, a megadott Itt `azureblob`, meghatározza, hogy mely adatok kinyerése viselkedések az indexelő kerül meghívásra. JSON blob-indexelés, az adatforrása definíciója megegyezik a JSON-dokumentumok és tömbök is. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>2. lépés: A cél keresési index létrehozása 

Indexelők az indexsémát van párosítva. Ha használja az API-t (a portál helyett), készítse elő az index előre, hogy a adhatja meg azt az indexelési művelet.

Az indexben kereshető tartalom az Azure Search tárolja. Index létrehozása, adjon meg egy sémát, amely megadja azokat a mezőket egy dokumentumot, attribútumokat és egyéb szerkezetek, amelyek a keresési funkció formázása. Ha létrehoz egy index, amelynek a forrása ugyanazokat a mezőneveket és adattípusok, az indexelő egyezni fog a forrás és cél mezőket, így nem kell explicit módon a a mezők leképezése munkáját.

A következő példa bemutatja egy [a Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) kérelmet. Az index fog rendelkezni a kereshető `content` tárolására blobok kinyert szöveget mező:   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="step-3-configure-and-run-the-indexer"></a>3. lépés: Konfigurálása és az indexelő futtatása

Mostanáig az adatforrás és az index parsingMode független lett. Azonban a 3. lépésben az indexelő konfigurációjának, az elérési út diverges attól függően, hogyan szeretné a JSON-blobját tartalom elemezni és struktúrája az Azure Search-index. Választási lehetőségek `json` vagy `jsonArray`:

+ Állítsa be **parsingMode** való `json` egyetlen dokumentumként minden egyes blob indexelendő.

+ Állítsa be **parsingMode** való `jsonArray` blobok JSON-tömbök áll, és szüksége lesz egy külön dokumentum az Azure Search szolgáltatásban a tömb egyes elemei. A dokumentum az egyetlen elemet a keresési eredmények között is felfoghatók. Ha azt szeretné, hogy a keresési eredmények között egy független elem megjelenjen a tömb egyes elemei, használja a `jsonArray` lehetőséget.

Az indexelő definíciója belül is használhat **mezőleképezéseivel** kiválasztása a forrás JSON-dokumentum melyik tulajdonságait használják a cél search-index feltöltéséhez. A JSON-tömbök Ha a tömb létezik egy alacsonyabb szintű tulajdonság jelzi, ha a tömb a blobon belüli kerül a dokumentum legfelső szintű beállítása.

> [!IMPORTANT]
> Ha használ `json` vagy `jsonArray` elemzési mód, az Azure Search feltételezi, hogy, hogy az adatforrás összes blobot tartalmaz-e a JSON. Ha ugyanazt az adatforrást támogatja a JSON-t, és nem JSON blobok vegyesen szeretne, ossza meg velünk az [UserVoice-webhelyünkön](https://feedback.azure.com/forums/263029-azure-search).


### <a name="how-to-parse-single-json-blobs"></a>Hogyan kell elemezni, egy JSON-blobok

Alapértelmezés szerint [Azure Search blob indexelőjével](search-howto-indexing-azure-blob-storage.md) egy olyan adattömb szöveg, JSON-blobok elemzi. Gyakran szeretné megőrizni a JSON-dokumentumok struktúráját. Tegyük fel például, a következő JSON-dokumentum rendelkezik az Azure Blob storage-ban:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

A blob indexelőjével elemzi a JSON-dokumentum, egy Azure Search-dokumentumban. Az indexelő index "szöveg", "datePublished" és "címkék" a forrás-index mezőire egyformán elnevezett és típusos cél elleni egyeztetése alapján tölti be.

Konfiguráció megtalálható egy indexelési művelet törzse. Ne felejtse el, hogy az adatforrás-objektum, korábban definiált, határozza meg az adatforrás típusa és kapcsolódási adatait. Ezenkívül a célindex is létezniük kell a szolgáltatás egy üres tárolóként. Ütemezés és a paraméterek megadása nem kötelező, de ha kihagyja őket, használatával azonnal, fut-e az indexelő `json` elemzési módot.

Egy teljes körűen kérés a következő lehet:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Feljegyzett, mező-leképezések nem szükségesek. Megadott index "szöveg", "datePublished, és a"címkék"mezőbe, a blob indexelő is kikövetkeztetni a megfelelő leképezés leképezése a kérelemben szereplő mező nélkül.

### <a name="how-to-parse-json-arrays"></a>Hogyan kell elemezni a JSON-tömbök

Másik megoldásként kérheti a JSON-tömb funkció. Ez a funkció akkor hasznos, ha a blobok tartalmazzák egy *JSON-objektumok tömbje*, és azt szeretné, hogy minden elem egy külön Azure Search-dokumentum lesz. Például adja meg a következő JSON-blobját, fel lehet tölteni az Azure Search-index három külön dokumentumot, az egyes "id" és "szöveg" mezőkkel.  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

A JSON-tömböt az indexelő definíciója az alábbi példához hasonlóan kell kinéznie. Figyelje meg, hogy a parsingMode paraméter adja meg a `jsonArray` elemzőt. A jobb oldali elemző megadása és a kellő adatok a bemeneti JSON-blobok indexelése csak két tömb-specifikus követelmények vonatkoznak.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Újra figyelje meg, hogy a mező-leképezések elhagyható. Feltéve, hogy az azonos nevű "id" és "szöveg" mezők index, a blob indexelőjével is kikövetkeztetni a megfelelő leképezés egy explicit mezőlista leképezés nélkül.

<a name="nested-json-arrays"></a>

### <a name="nested-json-arrays"></a>Beágyazott JSON-tömbök
Mi történik, ha szeretne indexelni a JSON-objektumok tömbje, de a tömb van beágyazva valahol a dokumentumot? Kiválaszthatja, melyik tulajdonság tartalmazza a tömb használatával a `documentRoot` konfigurációs tulajdonság. Például ha a blobokat a következőképpen:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Használja ezt a konfigurációt a tömbben szereplő indexelése a `level2` tulajdonság:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

### <a name="using-field-mappings-to-build-search-documents"></a>Mező-leképezések használatával hozhat létre a dokumentumok keresése

Ha a forrás- és mezőket metaadatszerű nem igazodik, leképezés mezőszakaszt definiálhat explicit mezők társítások a kérelem törzsében szereplő.

Jelenleg az Azure Search nem tud indexelni tetszőleges JSON-dokumentumokat közvetlenül, mert csak primitív adattípusokat, karakterlánc-tömbök és GeoJSON-pont támogatja. Használhatja azonban **mezőleképezéseivel** válassza ki a JSON-dokumentumok részei, és a "átemelés" azokat a keresési dokumentum legfelső szintű mezőkbe. A mező-leképezések alapjai kapcsolatos további információkért lásd: [az Azure Search indexelők Mezőleképezéseivel](search-indexer-field-mappings.md).

Nyissa meg ismét a példa JSON-dokumentum:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Tegyük fel, a következő mezőket a keresési index: `text` típusú `Edm.String`, `date` típusú `Edm.DateTimeOffset`, és `tags` típusú `Collection(Edm.String)`. Figyelje meg, hogy a "datePublished" között eltérés van a forrás és a `date` mezőt az indexben. Képezze le a JSON a kívánt formázásához, használja a következő mező-leképezések:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

A forrás mezőnevek a leképezések használatával vannak megadva a [JSON-mutató](https://tools.ietf.org/html/rfc6901) jelöléssel. Kezdődhet perjellel, tekintse meg a JSON-dokumentumok, a legfelső szintű, majd válassza ki a kívánt tulajdonságot (a beágyazási tetszőleges szintjén) előre perjellel elválasztva elérési úttal.

Is hivatkozni lehet az egyes tömbelemek rekordsémáját egy nulla alapú index használatával. Válasszon a fenti példában a "címkék" tömb első elemeként, például használja egy ehhez hasonló mezőleképezés:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Ha egy mező leképezés elérési útja egy forrás mező nevét, amely nem szerepel a JSON tulajdonságra hivatkozik, a rendszer kihagyta a leképezéseket hiba nélkül. Ez azért történik, így is támogatjuk a dokumentumok (Ez egy gyakori alkalmazási helyzet) eltérő sémával. Nem történik ellenőrzés, mert szüksége ügyeljen arra, hogy az a mező leképezésspecifikáció elgépelések elkerülése érdekében.
>
>

### <a name="rest-example-indexer-request-with-field-mappings"></a>REST-példa: Mezőleképezések az indexelő kérelem

Az alábbi példa egy teljes körűen megadott indexelő tartalmat, beleértve a mező-leképezések:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

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

A .NET SDK-t teljes mértékben paritásos REST API-val rendelkezik. Azt javasoljuk, hogy tekintse át az előző REST API-val szakaszban megismerheti a fogalmak, a munkafolyamat és a követelményeket. Ezután hivatkozni lehet a következő .NET API dokumentációja egy JSON-indexelő felügyelt programkódban implementálni.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

## <a name="see-also"></a>Lásd még

+ [Indexelők az Azure Search szolgáltatásban](search-indexer-overview.md)
+ [Az Azure Search szolgáltatással az Azure Blob Storage indexelése](search-howto-index-json-blobs.md)
+ [CSV-blobok indexelése az Azure Search blob indexelőjével](search-howto-index-csv-blobs.md)
+ [Oktatóanyag: Az Azure Blob storage-ból részben strukturált adatok keresése](search-semi-structured-data.md)
