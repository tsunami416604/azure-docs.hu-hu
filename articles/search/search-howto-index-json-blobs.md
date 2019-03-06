---
title: JSON-blobok indexelése az Azure Blob-indexelő az Azure Search – a teljes szöveges keresés
description: Feltérképezi az Azure JSON-blobok az Azure Search Blob indexelőjével szöveges tartalommal. Az indexelők automatizálni adatbetöltés a kijelölt adatforrásokhoz, például az Azure Blob storage.
ms.date: 02/28/2019
author: HeidiSteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: d70ad65f5bbc4424b4224cf601d903ad7ec10691
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405113"
---
# <a name="how-to-index-json-blobs-using-azure-search-blob-indexer"></a>Az Azure Search Blob indexelőjével JSON-blobok indexelése
Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Search blob [indexelő](search-indexer-overview.md) nyerje strukturált JSON-dokumentumokat az Azure Blob storage-ban, és lehetővé teszi az Azure Search kereshető. Ezt a munkafolyamatot hoz létre az Azure Search-index, és betölti azt a meglévő, a JSON-blobok kinyert szöveget. 

Használhatja a [portál](#json-indexer-portal), [REST API-k](#json-indexer-rest), vagy [.NET SDK-val](#json-indexer-dotnet) a JSON-tartalom indexelése. Az összes megközelítések közös, hogy a JSON-dokumentumok találhatók-e az Azure Storage-fiókban lévő blobtárolóba. A JSON-dokumentumok küld más-Azure platformról útmutatásért lásd: [adatok importálása az Azure Search](search-what-is-data-import.md).

Az Azure Blob storage-ban JSON-blobok jellemzően egy JSON-dokumentumok vagy a JSON-entitások gyűjteményét. A JSON-gyűjteményeket, a blob rendelkezhet egy **tömb** megfelelően formázott JSON-elemek. Blobok is álló egy sortöréssel elválasztva több egyedi JSON entitásokra. Az az Azure Search blob indexelőjével értelmezni tudja az ilyen konstrukció, attól függően, hogyan állíthatja a **parsingMode** paraméter a kérésre.

> [!IMPORTANT]
> `json` és `jsonArray` elemzési módhoz általánosan elérhetők, de `jsonLines` elemzési mód nyilvános előzetes verzióban érhető el, és nem éles környezetekben használható. További információkért lásd: [REST api-version = 2017-11-11-Preview](search-api-2017-11-11-preview.md). 

> [!NOTE]
> Kövesse az indexelő konfigurációs a [egy-a-többhöz indexelő](search-howto-index-one-to-many-blobs.md) több keresési dokumentumot egy Azure-blobból a kimeneti.

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>A portál használata

A legegyszerűbb módszer a indexeli a JSON-dokumentumokat, hogy a varázsló használata a [az Azure portal](https://portal.azure.com/). A metaadatokat az Azure blob-tárolóban, elemzés a [ **adatimportálás** ](search-import-data-portal.md) varázslóval hozzon létre egy alapértelmezett indexet, leképezik a forrásmezőket cél index mezőire és betölteni az indexet egy művelettel. A mérete és összetettsége forrásadatok működési teljes szöveges keresési index lehet percek alatt.

Azure Search és az Azure storage, lehetőség szerint ugyanabban a régióban található Azure-előfizetéshez használatát javasoljuk.

### <a name="1---prepare-source-data"></a>1 – forrásadatok előkészítése

Azure storage-fiókkal, a Blob storage és a egy JSON-dokumentumokat tároló kell rendelkeznie. Ha ismeri ezeket a követelményeket bármelyikével, tekintse át "Azure Blob service és a terhelés a Mintaadatok beállítása" az a [cognitive search-quickstart](cognitive-search-quickstart-blob.md#set-up-azure-blob-service-and-load-sample-data).

> [!Important]
> A tárolóban, ügyeljen arra, hogy **nyilvános hozzáférés szintje** "Tároló (névtelen olvasási hozzáférés tárolók és blobok)" értékre van állítva. Az Azure storage és az Azure Search egy előfizetésen belül, és ha lehetséges, ugyanabban a régióban kell lennie. 

### <a name="2---start-import-data-wizard"></a>2 – adatok importálása varázsló indítása

Is [varázsló elindításához](search-import-data-portal.md) az Azure Search szolgáltatás lapján, vagy kattintson a parancssávon **Azure Search hozzáadása** a a **Blob service** szakasz a storage-fiók bal oldali navigációs ablaktáblán.

   ![Adatok importálása parancs portálon](./media/search-import-data-portal/import-data-cmd2.png "indítsa el az adatok importálása varázsló")

### <a name="3---set-the-data-source"></a>3 – az adatforrás beállításához

Az a **adatforrás** a forrás oldalon kell lennie **Azure Blob Storage**, az alábbi beállításokkal:

+ **Kinyerni kívánt adatok** kell *tartalom és metaadatok*. Ez a beállítás lehetővé teszi, hogy a varázsló következtetni és az importálás a mezők leképezése.
   
+ **Elemzési mód** értékre kell állítani *JSON*, *JSON-tömböt* vagy *JSON sorok*. 

  *JSON* articulates minden egyes blob-dokumentumként egyetlen keresési, a keresési eredmények között egy független elemként jelenik meg. 

  *JSON-tömböt* , amelyek tartalmazzák a megfelelően formázott JSON-adatok – a megfelelően formázott JSON-blobok objektumok egy tömbjét felel meg, vagy rendelkezik egy olyan tulajdonsággal, amely objektumok tömbje, és azt szeretné, hogy minden egyes lehet különálló, független keresési dokumentum csuklós elemet. Ha a blobok összetettek, és ne adjon *JSON-tömböt* a teljes blob eseménystreamként egyetlen dokumentum.

  *JSON-sorok* van, a blobok mikroszolgáltatásokból álló, egy új sor, elválasztva több JSON entitás minden entitás független keresési különálló dokumentumként kell csuklós a kívánt helyre. Ha a blobok összetettek, és ne adjon *JSON sorok* elemzési módot, majd a teljes blob eseménystreamként egyetlen dokumentum.
   
+ **A tároló** meg kell adnia a tárfiók és tároló, vagy egy kapcsolati karakterláncot, amelyet a tárolóban. Kapcsolati karakterláncok Blob szolgáltatás portáloldalán kérheti le.

   ![BLOB adatforrás-definíciót](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-add-cognitive-search-page-in-the-wizard"></a>4 – kihagyja a varázslóban a "Hozzáadás cognitive search" lap

Kognitív képességek hozzáadása már nem szükséges JSON-dokumentum importáláshoz. Kivéve, ha egy adott kell [közé tartozik a Cognitive Services API-k és átalakításokat](cognitive-search-concept-intro.md) az indexelési folyamat kell ezt a lépést kihagyhatja.

A lépés kihagyásához először a következő oldalra lépéshez.

   ![Cognitive Search következő oldal gomb](media/search-get-started-portal/next-button-add-cog-search.png)

A lapról, áttérhet index testreszabása.

   ![A kognitív képességek lépés kihagyása](media/search-get-started-portal/skip-cog-skill-step.png)

### <a name="5---set-index-attributes"></a>5 – set indexattribútumokat

Az a **Index** oldalon azt kell látnia, a mezők listája adattípus és a egy sorozatát jelölőnégyzetek az index attribútumainak beállítása. A varázsló a mezők listáját, metaadatokat és a forrásadatok mintavétellel alapján hozhatja létre. 

Akkor is tömeges-válasszon attribútumok egy attribútum oszlop tetején a jelölőnégyzetére kattintva. Válasszon **lekérhető** és **kereshető** minden mezőhöz, amely egy ügyfélalkalmazás, és teljes szöveges keresés feldolgozást mindegyikre vissza kell adni. Láthatja, hogy egész számok nem állnak-e teljes szöveges vagy intelligens kereshető (számok pontosan értékeli és gyakran hasznosak a szűrők).

Tekintse át a leírása [indexattribútumok](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) és [nyelvi elemzők](https://docs.microsoft.com/rest/api/searchservice/language-support) további információt. 

Tekintse át a választásait, néhány percet vesz igénybe. A varázsló futtatása után fizikai datové struktury hozhatók létre, és nem lehet ezen mezők szerkesztése nélkül elvetését, majd újra létre kellene hoznia minden objektumot.

   ![BLOB index definíciója](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 – az indexelő létrehozása

Teljes körűen megadva, a varázsló létrehoz három különböző objektumok a search szolgáltatás. Egy adatforrás-objektum és az index objektum az Azure Search szolgáltatás az elnevezett erőforrásként kerülnek mentésre. Az utolsó lépés egy indexelő objektumot hoz létre. Az indexelő elnevezési lehetővé teszi, hogy létezik, amely ütemezett, és az index és az adatforrás-objektum, ugyanabban a sorrendben varázsló létrehozott függetlenül kezelheti önálló erőforrásként.

Ha nem ismeri a indexelők esetében, egy *indexelő* egy erőforrás az Azure Search szolgáltatásban, amely feltérképezi a kereshető tartalmak egy külső adatforrást. A kimenetét a **adatimportálás** varázsló használata egy indexelőt, feltérképezi a JSON-adatforrást, kinyeri a kereshető tartalmak és importálja azt az Azure Search-indexbe.

   ![BLOB indexelő definíciója](media/search-howto-index-json/import-wizard-json-indexer.png)

Kattintson a **OK** futtassa a varázslót, és az összes objektum létrehozásához. Indexelő azonnal kezdődik.

Adatok importálása a portál oldalain követheti nyomon. Folyamatértesítéseinek jelzik, hogy az indexelő állapotának és hány dokumentumok feltöltésekor. 

Indexelő befejeződése után használhatja [keresési ablak](search-explorer.md) lekérdezheti az indexét.

> [!NOTE]
> Ha nem látja a várt adatokat, szükség lehet további attribútumok beállítása több mező alapján. Törölje az index és indexelő az imént létrehozott, és. lépés: a varázsló lépéseit, módosítja az index attribútumainak az 5. lépésben kiválasztott beállításokat. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API-k használata

A REST API-t használhatja az Azure Search szolgáltatásban az összes indexelőre közös Háromrészes munkafolyamat következő JSON-blobok indexelése: hozzon létre egy adatforrást, hozzon létre egy index, indexelő létrehozása. Adatok kinyerése a blob storage-ból az indexelő létrehozása kérés benyújtása következik be. A kérelem teljesítése után a lekérdezhető index. 

Áttekintheti [REST kódpéldákat](#rest-example) végén található ez a szakasz bemutatja, hogyan három összes objektum létrehozásához. Kapcsolatos is tartalmaz részleteket ebben a szakaszban [JSON-elemzési módok](#parsing-modes), [blobokat egyetlen](#parsing-single-blobs), [JSON-tömbök](#parsing-arrays), és [tömbök beágyazott](#nested-json-arrays).

Indexelő kódalapú JSON, használja a [Postman](search-fiddler.md) és a REST API-t ezen objektumok létrehozása:

+ [index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [Adatforrás](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

Műveleti sorrend kell létrehozni, és hívja az objektumok ebben a sorrendben. Ellentétben a portal munkafolyamata a kód megközelítést igényel fogadja el a JSON-dokumentumok küldött keresztül érhető el index a **indexelő létrehozása** kérelmet.

Az Azure Blob storage-ban JSON-blobok jellemzően egy JSON-dokumentumok és a egy JSON "array". Az az Azure Search blob indexelőjével vagy konstrukció, attól függően, hogyan állíthatja tudja elemezni a **parsingMode** paraméter a kérésre.

| JSON-dokumentumok | parsingMode | Leírás | Rendelkezésre állás |
|--------------|-------------|--------------|--------------|
| Egy blob egy | `json` | Elemzi a JSON-blobok, a szöveg egy olyan adattömb. Egyes JSON-blobok az Azure Search egyetlen dokumentum válik. | Általánosan elérhető a [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API és [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK-t. |
| Több blobonkénti | `jsonArray` | Elemzi a blobban, ahol a tömb egyes elemei lesz-e egy külön Azure Search-dokumentum egy JSON-tömböt.  | Mindkét előzetes verzióban elérhető [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API és [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK-t. |
| Több blobonkénti | `jsonLines` | Egy blobot, amely tartalmaz egy új sor, ahol a minden entitás egy külön Azure Search-dokumentum lesz elválasztva több JSON entitás ("tömb") elemzi. | Mindkét előzetes verzióban elérhető [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API és [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK-t. |

### <a name="1---assemble-inputs-for-the-request"></a>1 – állítsa össze a kérés bemenetek

Az egyes kérések meg kell adnia a szolgáltatás nevét és adminisztrációs kulcsot az Azure Search (a POST-fejléc), és a tárfiók nevét és kulcsát a blob Storage. Használhat [Postman](search-fiddler.md) HTTP-kéréseket küldhet az Azure Search.

Másolja az alábbi négy értéket a Jegyzettömbbe, így beillesztheti őket egy kérelem:

+ Az Azure Search-szolgáltatásnév
+ Az Azure Search adminisztrátori kulcs
+ Az Azure storage-fiók neve
+ Az Azure storage-fiókkulcs

Ezeket az értékeket a portálon találja meg:

1. Az Azure Search portál lapjain másolja a search szolgáltatás URL-címet az Áttekintés oldal.

2. Kattintson a bal oldali navigációs ablaktáblában **kulcsok** , majd másolja vagy az elsődleges vagy másodlagos kulcsot (azok egyenértékű).

3. Váltson a tárfiók a portál oldalain. A bal oldali navigációs ablaktáblán a **beállítások**, kattintson a **Tárelérési kulcsok**. Ez az oldal nyújt a fióknevet és a kulcsot. A tárfiók nevét és az egyik kulcsot másolja a Jegyzettömbbe.

### <a name="2---create-a-data-source"></a>2 – egy adatforrás létrehozása

Ez a lépés biztosítja az indexelő által használt adatforrás kapcsolati adatait. Az adatforrás egy elnevezett objektumot az Azure Search használ, amely a kapcsolati információkat. Az adatforrás típusa, `azureblob`, meghatározza, hogy mely adatok kinyerése viselkedések az indexelő kerül meghívásra. 

Helyettesítse be a szolgáltatás neve, az adminisztrációs kulcsot, a storage-fiók érvényes értékei, és a kulcs helyőrzőket fiók.

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 – a cél keresési index létrehozása 

Indexelők az indexsémát van párosítva. Ha használja az API-t (a portál helyett), készítse elő az index előre, hogy a adhatja meg azt az indexelési művelet.

Az indexben kereshető tartalom az Azure Search tárolja. Index létrehozása, adjon meg egy sémát, amely megadja azokat a mezőket egy dokumentumot, attribútumokat és egyéb szerkezetek, amelyek a keresési funkció formázása. Ha létrehoz egy index, amelynek a forrása ugyanazokat a mezőneveket és adattípusok, az indexelő egyezni fog a forrás és cél mezőket, így nem kell explicit módon a a mezők leképezése munkáját.

A következő példa bemutatja egy [a Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) kérelmet. Az index fog rendelkezni a kereshető `content` tárolására blobok kinyert szöveget mező:   

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 – konfigurálása és az indexelő futtatása

Mivel az index és a egy adatainak forrás-, és az indexelő is egy elnevezett objektum, amely létrehoz és újra felhasználhatja az Azure Search szolgáltatás. Indexelő létrehozása teljes körűen megadott kérelem a következő lehet:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Az indexelő konfigurációjának a kérelem törzsében szerepel. Egy adatforrás és a egy üres célindex, amely már létezik az Azure Search van szükség. 

Ütemezés és a paraméterek megadása nem kötelező. Ha kihagyja őket, használatával azonnal, fut-e az indexelő `json` elemzési módot.

Nem tartalmazza az adott indexelő [mezőleképezéseivel](#field-mappings). Az indexelő meghatározásán hagyhatja **mezőleképezéseivel** Ha tulajdonságai között a forrás JSON-dokumentum felel meg a cél keresési indexhez mezőit. 


### <a name="rest-example"></a>Példa REST

Ez a szakasz egy röviden összefoglaljuk az objektumok létrehozására használt összes kérelem. Összetevő részei, lásd: Ez a cikk korábbi szakaszaiban.

### <a name="data-source-request"></a>Forrás kérelem

Minden indexelő esetében van szükség egy adatforrás-objektum, amely a meglévő adatok való kapcsolódáshoz szükséges adatokat biztosít. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Index kérelem

Minden indexelő esetében van szükség egy célindex, amely megkapja az adatokat. A kérelem törzsében mezők, kereshető indexet a kívánt viselkedést támogatja a teljesítménykapacitást álló az indexséma határozza meg. Ez az index üresnek kell lennie az indexelő futtatásakor. 

    POST https://[service name].search.windows.net/indexes?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key for Azure Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Az indexelő kérelem

A kérelem egy teljes körűen megadott indexelő jeleníti meg. Ez magában foglalja [mezőleképezéseivel](#field-mappings), amely az előző példák lettek hagyva. Idézze, hogy "ütemezés", "parameters" és "fieldMappings" választható mindaddig, amíg nincs egy elérhető alapértelmezett. Az indexelő futtatása azonnal felsorolhatja az "ütemezés" okoz. Az index az "json" alapértelmezett "parsingMode" felsorolhatja okoz.

Adatok importálása az indexelő létrehozása az Azure Search aktivál. Ez azonnal, és ezt követően ütemezés szerint futtatott Ha megadott egy.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
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

A .NET SDK-t teljes mértékben paritásos REST API-val rendelkezik. Azt javasoljuk, hogy tekintse át az előző REST API-val szakaszban megismerheti a fogalmak, a munkafolyamat és a követelményeket. Ezután hivatkozni lehet a következő .NET API dokumentációja egy JSON-indexelő felügyelt programkódban implementálni.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Elemzési módhoz

JSON-blobok feltételezheti, hogy több űrlap. A **parsingMode** paramétert a JSON-indexelő határozza meg, hogyan elemzi és struktúrája az Azure Search-index JSON blob tartalma:

| parsingMode | Leírás |
|-------------|-------------|
| `json`  | Az index minden egyes blob egyetlen dokumentumként. Ez az alapértelmezett érték. |
| `jsonArray` | Válassza ezt a módot, ha a blobok JSON-tömbök áll, és szüksége lesz egy külön dokumentum az Azure Search szolgáltatásban a tömb egyes elemei. |
|`jsonLines` | Válassza ezt a módot, ha a blobok JSON több entitás, Sortöréssel elválasztott áll, és minden entitás egy külön dokumentum az Azure Search lesz szüksége. |

A dokumentum az egyetlen elemet a keresési eredmények között is felfoghatók. Ha azt szeretné, hogy a keresési eredmények között egy független elem megjelenjen a tömb egyes elemei, használja a `jsonArray` vagy `jsonLines` lehetőséget igény szerint.

Az indexelő definíciója belül is használhat [mezőleképezéseivel](search-indexer-field-mappings.md) kiválasztása a forrás JSON-dokumentum melyik tulajdonságait használják a cél search-index feltöltéséhez. A `jsonArray` mód, elemzés, ha a tömb már létezik egy alacsonyabb szintű tulajdonságként, beállíthat jelző, ahol a tömb a blobon belüli kerül a dokumentum legfelső szintű.

> [!IMPORTANT]
> Ha használ `json`, `jsonArray` vagy `jsonLines` elemzési mód, az Azure Search feltételezi, hogy, hogy az adatforrás összes blobot tartalmaz-e a JSON. Ha ugyanazt az adatforrást támogatja a JSON-t, és nem JSON blobok vegyesen szeretne, ossza meg velünk az [UserVoice-webhelyünkön](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Egyetlen JSON-blobok elemzése

Alapértelmezés szerint [Azure Search blob indexelőjével](search-howto-indexing-azure-blob-storage.md) egy olyan adattömb szöveg, JSON-blobok elemzi. Gyakran szeretné megőrizni a JSON-dokumentumok struktúráját. Tegyük fel például, a következő JSON-dokumentum rendelkezik az Azure Blob storage-ban:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

A blob indexelőjével elemzi a JSON-dokumentum, egy Azure Search-dokumentumban. Az indexelő index "szöveg", "datePublished" és "címkék" a forrás-index mezőire egyformán elnevezett és típusos cél elleni egyeztetése alapján tölti be.

Feljegyzett, mező-leképezések nem szükségesek. Megadott index "szöveg", "datePublished, és a"címkék"mezőbe, a blob indexelő is kikövetkeztetni a megfelelő leképezés leképezése a kérelemben szereplő mező nélkül.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>JSON-tömbök elemzése

A JSON-tömb lehetőséget is használhatja. Ez a beállítás akkor hasznos, ha a blobok tartalmazzák egy *megfelelően formázott JSON-objektumok tömbje*, és azt szeretné, hogy minden elem egy külön Azure Search-dokumentum lesz. Például adja meg a következő JSON-blobját, fel lehet tölteni az Azure Search-index három külön dokumentumot, az egyes "id" és "szöveg" mezőkkel.  

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

## <a name="parse-nested-arrays"></a>Beágyazott tömbök elemzése
A JSON Tárolótömböket rendelkező beágyazott elemek, megadhat egy `documentRoot` többszintű struktúrához jelzi. Például ha a blobokat a következőképpen:

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

## <a name="parse-blobs-separated-by-newlines"></a>Blobok sortörésekből álló szóközöket tartalmazza elválasztva elemzése

Ha a blob egy sortöréssel elválasztva több JSON entitásokat tartalmaz, és azt szeretné, hogy minden elem lesz egy külön Azure Search-dokumentum, kérheti a JSON-sorok lehetőséget. Például adott az a következő blob (ha vannak három különböző JSON-entitások), az Azure Search-index három külön dokumentumot, az "id" és "szöveg" mezők mindegyike fel lehet tölteni.

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

A JSON-sorok az indexelő definíciója az alábbi példához hasonlóan kell kinéznie. Figyelje meg, hogy a parsingMode paraméter adja meg a `jsonLines` elemzőt. 

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Újra, figyelje meg, mező-leképezések, hogy nincs megadva, hasonlóan ahhoz, hogy a `jsonArray` elemzési mód.

## <a name="add-field-mappings"></a>Mező-leképezések hozzáadása

Ha a forrás- és mezőket metaadatszerű nem igazodik, leképezés mezőszakaszt definiálhat explicit mezők társítások a kérelem törzsében szereplő.

Jelenleg az Azure Search nem tud indexelni tetszőleges JSON-dokumentumokat közvetlenül, mert csak a primitív adattípusokat, a karakterlánc-tömbök és a GeoJSON-pont támogatja. Használhatja azonban **mezőleképezéseivel** válassza ki a JSON-dokumentumok részei, és a "átemelés" azokat a keresési dokumentum legfelső szintű mezőkbe. A mező-leképezések alapjai kapcsolatos további információkért lásd: [az Azure Search indexelők Mezőleképezéseivel](search-indexer-field-mappings.md).

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

## <a name="see-also"></a>Lásd még

+ [Indexelők az Azure Search szolgáltatásban](search-indexer-overview.md)
+ [Az Azure Search szolgáltatással az Azure Blob Storage indexelése](search-howto-index-json-blobs.md)
+ [CSV-blobok indexelése az Azure Search blob indexelőjével](search-howto-index-csv-blobs.md)
+ [Oktatóanyag: Az Azure Blob storage-ból részben strukturált adatok keresése](search-semi-structured-data.md)
