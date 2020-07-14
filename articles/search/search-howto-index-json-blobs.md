---
title: Keresés JSON-blobokon
titleSuffix: Azure Cognitive Search
description: Azure JSON-Blobok bejárása a szöveges tartalomhoz az Azure Cognitive Search blob indexelő használatával. Az indexelő automatizálja az adatfeldolgozást a kiválasztott adatforrásokhoz, például az Azure Blob Storage-hoz.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 3e1efb1f93910f311ad5df898152d71158003244
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86146844"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>JSON-Blobok indexelése blob indexelő használatával az Azure-ban Cognitive Search

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy Azure Cognitive Search blob- [indexet](search-indexer-overview.md) az Azure Blob Storage-ban lévő JSON-dokumentumokból származó strukturált tartalom kinyeréséhez, és kereshetővé teheti az Azure-ban Cognitive Search Ez a munkafolyamat létrehoz egy Azure Cognitive Search indexet, és betölti azt a JSON-blobokból kinyert meglévő szöveggel. 

A JSON-tartalom indexeléséhez használhatja a [portált](#json-indexer-portal), a [REST API-kat](#json-indexer-rest)vagy a [.net SDK](#json-indexer-dotnet) -t is. Az összes megközelítés esetében gyakori, hogy a JSON-dokumentumok egy Azure Storage-fiókban található blob-tárolóban találhatók. A JSON-dokumentumok más nem Azure-platformokról történő leküldésével kapcsolatos útmutatásért lásd: [adatimportálás az azure Cognitive Searchban](search-what-is-data-import.md).

Az Azure Blob Storage-ban található JSON-Blobok általában egyetlen JSON-dokumentum (elemzési mód `json` ) vagy JSON-entitások gyűjteményei. Gyűjtemények esetében a blob jól formázott JSON-elemek **tömbjét** tartalmazhatja (az elemzési mód `jsonArray` ). A Blobok több, sortöréssel elválasztott JSON-entitásból is állhatnak (elemzési mód: `jsonLines` ). A kérelemben szereplő **parsingMode** paraméter határozza meg a kimeneti struktúrákat.

> [!NOTE]
> A több keresési dokumentum egyetlen blobból való indexelésével kapcsolatos további információkért lásd: [egy a többhöz indexelés](search-howto-index-one-to-many-blobs.md).

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>A portál használata

A JSON-dokumentumok indexelésének legegyszerűbb módja a [Azure Portal](https://portal.azure.com/)varázsló használata. Az Azure Blob-tárolóban található metaadatok elemzésével az [**adatok importálása**](search-import-data-portal.md) varázsló létrehozhat egy alapértelmezett indexet, leképezheti a forrás mezőket a célként megadott index mezőibe, és betöltheti az indexet egyetlen művelettel. A forrásadatok méretétől és összetettségének függvényében percek alatt működőképes teljes szöveges keresési indexszel rendelkezhet.

Azt javasoljuk, hogy az Azure Cognitive Search és az Azure Storage-hoz ugyanazt a régiót vagy helyet használja az alacsonyabb késéshez és a sávszélességgel kapcsolatos költségek elkerülése érdekében.

### <a name="1---prepare-source-data"></a>1 – forrásadatok előkészítése

[Jelentkezzen be a Azure Portalba](https://portal.azure.com/) , és [hozzon létre egy BLOB-tárolót](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) , amely az adatait tartalmazza. A nyilvános hozzáférési szint bármelyik érvényes értékére beállítható.

Az **adatimportálás** varázslóban szüksége lesz a Storage-fiók nevére, a tároló nevére és egy hozzáférési kulcsra az adatlekérdezéshez.

### <a name="2---start-import-data-wizard"></a>2 – az adatimportálás megkezdése varázsló

A keresési szolgáltatás áttekintés lapján [elindíthatja a varázslót](search-import-data-portal.md) a parancssáv használatával.

   ![Adatimportálási parancs a portálon](./media/search-import-data-portal/import-data-cmd2.png "Az adatimportálás varázsló elindítása")

### <a name="3---set-the-data-source"></a>3 – az adatforrás beállítása

**Az adatforrás lapon a** forrásnak **Azure Blob Storagenak**kell lennie, a következő jellemzőkkel:

+ **A kinyerni kívánt adatoknak** *tartalomnak és metaadatoknak*kell lenniük. Ha ezt a lehetőséget választja, a varázsló lekövetkeztet egy index-sémát, és leképezi a mezőket az importáláshoz.
   
+ Az **elemzési módot** *JSON*-ra, JSON- *tömbre* vagy *JSON-sorra*kell beállítani. 

  A *JSON* minden blobot egyetlen keresési dokumentumként fogalmaz meg, amely független elemként jelenik meg a keresési eredmények között. 

  A *JSON-tömb* olyan blobokat tartalmaz, amelyek jól formázott JSON-adatmennyiséget tartalmaznak – a jól formázott JSON egy objektum tömbje, vagy egy olyan tulajdonság, amely objektumok tömbje, és azt szeretné, hogy az egyes elemek különálló, független keresési dokumentumként legyenek kialakítva. Ha a Blobok bonyolultak, és nem választja a *JSON-tömböt* , a teljes blob egyetlen dokumentumként lesz betöltve.

  A *JSON-sorok* a több JSON-entitásból álló, új vonallal elválasztott Blobok, ahol az egyes entitásokat önálló, független keresési dokumentumként kell összeállítani. Ha a Blobok bonyolultak, és nem választja a *JSON-vonalak* elemzési módját, akkor a teljes blob egyetlen dokumentumként lesz betöltve.
   
+ A **Storage-tárolónak** meg kell adnia a Storage-fiókot és-tárolót, vagy egy olyan kapcsolódási karakterláncot, amely a tárolóra van feloldva. A Blob service portál lapon lekérheti a kapcsolatok karakterláncait.

   ![BLOB-adatforrás definíciója](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 – a "tartalom gazdagítása" lap kihagyása a varázslóban

A kognitív képességek (vagy a dúsítás) hozzáadása nem szükséges importálási követelmény. Ha nem rendelkezik konkrét [mesterséges intelligencia-bővítéssel](cognitive-search-concept-intro.md) az indexelési folyamathoz, hagyja ki ezt a lépést.

A lépés kihagyásához kattintson a lap alján található kék gombra a "Next" és a "Skip" (kihagyás) elemre.

### <a name="5---set-index-attributes"></a>5 – index attribútumainak beállítása

Az **index** lapon meg kell jelennie egy adattípusú mezők listájának, valamint egy sor jelölőnégyzet az index attribútumainak beállításához. A varázsló metaadatok alapján és a forrásadatok mintavételezésével hozhatja meg a mezők listáját. 

Az attribútumok tömeges kiválasztásához kattintson az attribútum oszlop tetején található jelölőnégyzetre. Válassza a **beolvasható** és **kereshető** lehetőséget minden olyan mező esetében, amelyet vissza kell adni egy ügyfélalkalmazás számára, és a teljes szöveges keresés feldolgozására is érvényes. Megfigyelheti, hogy az egész számok nem teljes szöveges vagy zavaros kereshetők (a számok szó szerint vannak kiértékelve, és gyakran hasznosak a szűrőkben).

További információkért tekintse át az [index attribútumainak](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) és a [nyelvi elemzők](https://docs.microsoft.com/rest/api/searchservice/language-support) leírását. 

Szánjon egy kis időt a kiválasztott elemek áttekintésére. A varázsló futtatása után a rendszer létrehozza a fizikai adatstruktúrákat, és nem tudja szerkeszteni ezeket a mezőket az összes objektum eldobása és újbóli létrehozása nélkül.

   ![BLOB-index definíciója](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 – indexelő létrehozása

Teljes mértékben meg van adva, a varázsló három különböző objektumot hoz létre a keresési szolgáltatásban. Az adatforrás-objektumok és az index objektumok elnevezett erőforrásokként lesznek mentve az Azure Cognitive Search szolgáltatásban. Az utolsó lépés egy indexelő objektumot hoz létre. Az indexelő elnevezése lehetővé teszi, hogy önálló erőforrásként is használható legyen, amelyet az index és az adatforrás objektumtól függetlenül ütemezhet és kezelhet, amely ugyanabban a varázsló-sorozatban jön létre.

Ha nem ismeri az indexelő funkciót, az *Indexelő* egy olyan erőforrás az Azure Cognitive Searchban, amely egy külső adatforrást mutat be a kereshető tartalomhoz. Az **adatimportálás** varázsló kimenete egy indexelő, amely feltérképezi a JSON-adatforrást, Kinyeri a kereshető tartalmat, és importálja azokat egy Azure-Cognitive Search indexbe.

   ![BLOB indexelő definíciója](media/search-howto-index-json/import-wizard-json-indexer.png)

A varázsló futtatásához és az összes objektum létrehozásához kattintson **az OK** gombra. Az indexelés azonnal megkezdődik.

Az adatimportálást a portál oldalain lehet figyelni. Az állapotjelző értesítések az indexelési állapotot és a feltöltött dokumentumok számát jelzik. 

Az indexelés befejezésekor a [Search Explorer](search-explorer.md) használatával kérdezheti le az indexet.

> [!NOTE]
> Ha nem látja a várt adattípust, előfordulhat, hogy további attribútumokat kell beállítania további mezőknél. Törölje az imént létrehozott indexet és indexelő, és ismételje meg a varázslót, és módosítsa az index attribútumainak kiválasztását az 5. lépésben. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>REST API-k használata

A REST API használatával indexelheti a JSON-blobokat, az Cognitive Search Azure-ban található összes indexelő esetében az alábbi háromrészes munkafolyamatot követve: hozzon létre egy adatforrást, hozzon létre egy indexet, hozzon létre egy indexelő. A blob Storage-ból származó adatok kinyerése akkor történik meg, amikor beküldi az index-létrehozási kérelmet. A kérés befejezését követően lekérdezhető index jelenik meg. 

A szakasz végén található [Rest-mintakód](#rest-example) áttekintésével megtudhatja, hogyan hozhat létre mindhárom objektumot. Ez a szakasz a JSON- [elemzési üzemmódok](#parsing-modes), az [egyes Blobok](#parsing-single-blobs), a [JSON-tömbök](#parsing-arrays)és a [beágyazott tömbök](#nested-json-arrays)részleteit is tartalmazza.

A kód alapú JSON-indexeléshez a [Poster](search-get-started-postman.md) és a REST API használatával hozza létre ezeket az objektumokat:

+ [index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [adatforrás](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [Indexelő](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

A műveletek sorrendje megköveteli, hogy az objektumokat ebben a sorrendben hozza létre és hívja meg. A portál munkafolyamatával ellentétben a kód megközelítéséhez egy elérhető indexre van szükség, amely elfogadja az **Indexelő** kéréssel elküldhető JSON-dokumentumokat.

Az Azure Blob Storage-ban található JSON-Blobok jellemzően egyetlen JSON-dokumentum vagy egy JSON "tömb". Az Azure Cognitive Searchban található blob indexelő képes elemezni az építkezést, attól függően, hogy miként állítja be a **parsingMode** paramétert a kérelemre.

| JSON-dokumentum | parsingMode | Leírás | Rendelkezésre állás |
|--------------|-------------|--------------|--------------|
| Egy blob | `json` | A JSON-blobokat egyetlen darab szövegként elemzi. Minden JSON-blob egyetlen Azure Cognitive Search-dokumentum lesz. | Általánosan elérhető a [Rest](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API-ban és a [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK-ban is. |
| Több blobon | `jsonArray` | Egy JSON-tömböt elemez a blobban, ahol a tömb minden eleme külön Azure Cognitive Search dokumentum lesz.  | Általánosan elérhető a [Rest](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API-ban és a [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK-ban is. |
| Több blobon | `jsonLines` | Egy olyan blobot elemez, amely több JSON-entitást (egy "tömböt") tartalmaz egy sortöréssel elválasztva, ahol minden entitás külön Azure Cognitive Search dokumentum lesz. | Általánosan elérhető a [Rest](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API-ban és a [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK-ban is. |

### <a name="1---assemble-inputs-for-the-request"></a>1 – bemenetek összegyűjtése a kérelemhez

Minden kérelem esetében meg kell adnia a szolgáltatás nevét és a rendszergazdai kulcsot az Azure Cognitive Search (a POST fejlécben), valamint a blob Storage-hoz tartozó Storage-fiók nevét és kulcsát. A [Poster](search-get-started-postman.md) használatával http-kéréseket küldhet az Azure Cognitive Searchnak.

Másolja a következő négy értéket a Jegyzettömbbe, hogy beillessze őket egy kérelembe:

+ Azure Cognitive Search szolgáltatás neve
+ Azure Cognitive Search rendszergazdai kulcs
+ Azure Storage-tárfiók neve
+ Azure Storage-fiók kulcsa

Ezeket az értékeket a portálon találja:

1. Az Azure Cognitive Search portál oldalain másolja a keresési szolgáltatás URL-címét az Áttekintés lapról.

2. A bal oldali navigációs ablaktáblán kattintson a **kulcsok** elemre, majd másolja az elsődleges vagy a másodlagos kulcsot (ezek egyenértékűek).

3. Váltson a Storage-fiókjához tartozó portál oldalaira. A bal oldali navigációs ablaktábla **Beállítások**területén kattintson a **hozzáférési kulcsok**elemre. Ezen a lapon a fiók neve és kulcsa is szerepel. Másolja a Storage-fiók nevét és az egyik kulcsot a Jegyzettömbbe.

### <a name="2---create-a-data-source"></a>2 – adatforrás létrehozása

Ez a lépés az indexelő által használt adatforrás-kapcsolatok adatait tartalmazza. Az adatforrás egy megnevezett objektum az Azure Cognitive Searchban, amely megőrzi a kapcsolatok adatait. Az adatforrás típusa, `azureblob` amely meghatározza, hogy az indexelő milyen adatkiemelési viselkedéseket hív meg. 

Adjon meg érvényes értékeket a szolgáltatásnév, a rendszergazdai kulcs, a Storage-fiók és a fiók kulcsa helyőrzők helyett.

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   
```

### <a name="3---create-a-target-search-index"></a>3 – cél keresési index létrehozása 

Az indexelő egy index sémával vannak párosítva. Ha az API-t (nem pedig a portált) használja, előre készítse el az indexet, hogy az indexelő műveletnél megadható legyen.

Az index a kereshető tartalmat tárolja az Azure Cognitive Searchban. Index létrehozásához adjon meg egy sémát, amely meghatározza a keresési élményt formáló dokumentum, attribútum és egyéb szerkezetek mezőit. Ha olyan indexet hoz létre, amelynek neve és adattípusa megegyezik a forrással, az indexelő megegyezik a forrás és a cél mezővel, így a munka megtakarításával a mezők explicit módon képezhetők le.

Az alábbi példa egy [create index](https://docs.microsoft.com/rest/api/searchservice/create-index) -kérést mutat be. Az index egy kereshető mezővel fog rendelkezni `content` , amely a blobokból kinyert szöveget tárolja:   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```


### <a name="4---configure-and-run-the-indexer"></a>4 – az indexelő konfigurálása és futtatása

Az indexekhez és az adatforrásokhoz hasonlóan az indexelő is egy elnevezett objektum, amelyet Ön egy Azure Cognitive Search szolgáltatásban hoz létre és használ fel. Az indexelő létrehozásához egy teljesen meghatározott kérelem a következőképpen jelenhet meg:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }
```

Az indexelő konfigurációja a kérelem törzsében található. Szükség van egy adatforrásra és egy üres, az Azure Cognitive Searchban már meglévő indexre. 

Az ütemterv és a paraméterek megadása nem kötelező. Ha kihagyja őket, az indexelő azonnal fut az `json` elemzési mód használatával.

Az adott indexelő nem tartalmaz mező-hozzárendelést. Az indexelő definícióján belül kihagyhatja a **mezők hozzárendeléseit** , ha a forrás JSON-dokumentum tulajdonságai egyeznek a cél keresési index mezőivel. 


### <a name="rest-example"></a>REST-példa

Ez a szakasz az objektumok létrehozásához használt összes kérelem emlékeztetője. Az összetevőkről a jelen cikk előző részeiben talál további információt.

### <a name="data-source-request"></a>Adatforrás-kérelem

Minden indexelő olyan adatforrás-objektumot igényel, amely kapcsolódási adatokat biztosít a meglévő adatokhoz. 

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  
```

### <a name="index-request"></a>Indexelési kérelem

Az összes indexelő megköveteli az adatfogadásra szolgáló cél indexet. A kérelem törzse meghatározza az index sémáját, amely a mezőkből áll, és a kívánt viselkedést támogatja egy kereshető indexben. Az indexnek üresnek kell lennie az indexelő futtatásakor. 

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

### <a name="indexer-request"></a>Indexelő kérelme

Ez a kérelem egy teljesen meghatározott indexelő jelenít meg. Ide tartoznak a mező-hozzárendelések, amelyek az előző példákban kimaradtak. Ne felejtse el, hogy az "Schedule", a "parameters" és a "fieldMappings" nem kötelező, feltéve, hogy elérhető az alapértelmezett érték. Az "ütemterv" kihagyása azt eredményezi, hogy az indexelő azonnal fut. A "parsingMode" érték kihagyása esetén az index a "JSON" alapértelmezett értéket használja.

Az indexelő létrehozása az Azure Cognitive Search eseményindítók adatimportálási szolgáltatásával. Azonnal fut, és azt követően egy ütemezett időpontban, ha már biztosított egyet.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

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
```

<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>A .NET SDK használata

A .NET SDK-val teljes paritású a REST API. Javasoljuk, hogy tekintse át az előző REST API szakaszt a fogalmak, a munkafolyamatok és a követelmények megismeréséhez. A következő .NET API-referenciák dokumentációjában a JSON-indexelő implementálása felügyelt kódban végezhető el.

+ [Microsoft. Azure. Search. models. DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [Microsoft. Azure. Search. models. datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [Microsoft. Azure. Search. models. index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [Microsoft. Azure. Search. models. indexelő](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Elemzési módok

A JSON-Blobok több űrlapot is tartalmazhatnak. A JSON-indexelő **parsingMode** paramétere határozza meg, hogyan történik a JSON-blob tartalmának elemzése és strukturálása egy Azure Cognitive Search indexben:

| parsingMode | Leírás |
|-------------|-------------|
| `json`  | Az egyes Blobok indexelése egyetlen dokumentumként. Ez az alapértelmezett beállítás. |
| `jsonArray` | Akkor válassza ezt a módot, ha a Blobok JSON-tömbökből állnak, és a tömb minden eleméhez külön dokumentumra lesz szükség az Azure Cognitive Searchban. |
|`jsonLines` | Akkor válassza ezt a módot, ha a Blobok több JSON-entitásból állnak, amelyek új vonallal vannak elválasztva, és minden entitásnak külön dokumentumnak kell lennie az Azure Cognitive Searchban. |

A dokumentumok egyetlen elemként is megtekinthetők a keresési eredményekben. Ha azt szeretné, hogy a tömb minden eleme egy független elemként jelenjen meg a keresési eredmények között, akkor használja a `jsonArray` vagy a lehetőséget a megfelelő módon `jsonLines` .

Az indexelő definíciójában igény szerint [mező-hozzárendeléseket](search-indexer-field-mappings.md) is használhat, hogy kiválassza a forrás JSON-dokumentumának a cél keresési indexének feltöltéséhez használt tulajdonságokat. Az `jsonArray` elemzési mód esetében, ha a tömb alacsonyabb szintű tulajdonságként létezik, beállíthat egy dokumentum gyökerét, amely azt jelzi, hogy a tömb hová kerül a blobon belül.

> [!IMPORTANT]
> Az `json` `jsonArray` `jsonLines` Azure Cognitive Search azt feltételezi, hogy az adatforrásban lévő összes blob JSON-t tartalmaz, vagy az elemzési módot használja. Ha a JSON-és a nem JSON-Blobok kombinációját is támogatnia kell ugyanabban az adatforrásban, tudassa velünk a [UserVoice webhelyen](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Egyszeri JSON-Blobok elemzése

Alapértelmezés szerint az [Azure Cognitive Search blob Indexer](search-howto-indexing-azure-blob-storage.md) egyetlen darab szövegként elemzi a JSON-blobokat. Gyakran szeretné megőrizni a JSON-dokumentumok szerkezetét. Tegyük fel például, hogy rendelkezik a következő JSON-dokumentummal az Azure Blob Storage-ban:

```http
    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }
```

A blob indexelő a JSON-dokumentumot egyetlen Azure Cognitive Search-dokumentumba elemzi. Az indexelő betölti az indexet a "text", "datePublished" és "Tags" kifejezéssel a forrástól az azonos névvel ellátott és beírt cél index mezőkkel.

Ahogy említettük, a mező-hozzárendelések nem szükségesek. A "text", "datePublished" és "Tags" mezőkkel rendelkező indexek esetében a blob indexelő a kérelemben található mező-hozzárendelés nélkül következtetheti ki a megfelelő leképezést.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>JSON-tömbök elemzése

Azt is megteheti, hogy a JSON Array kapcsolót használja. Ez a beállítás akkor hasznos, ha a Blobok *jól FORMÁZOTT JSON-objektumokat tartalmazó tömböt*tartalmaznak, és azt szeretné, hogy mindegyik elem külön Azure Cognitive Search dokumentum legyen. Például a következő JSON-blob esetében az Azure Cognitive Search indexét három külön dokumentummal töltheti fel, amelyek mindegyike "id" és "text" mezőket tartalmaz.  

```text
    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]
```

JSON-tömb esetén az indexelő definíciójának az alábbi példához hasonlóan kell kinéznie. Figyelje meg, hogy a parsingMode paraméter határozza meg az `jsonArray` elemzőt. A megfelelő elemző megadása és a megfelelő adatok bevitele a JSON-Blobok indexelésének egyetlen két tömb-specifikus követelménye.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
```

Figyelje meg, hogy a mező-hozzárendelések nem hagyhatók el. Az "id" és "text" mezőket tartalmazó indexek feltételezik, hogy a blob-indexelő a megfelelő leképezést explicit mező-hozzárendelési lista nélkül következteti ki.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Beágyazott tömbök elemzése
A beágyazott elemeket tartalmazó JSON-tömbök esetében megadható, `documentRoot` hogy többszintű struktúrát jelezzen. Ha például a Blobok így néznek ki:

```http
    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }
```

Ezzel a konfigurációval indexelheti a tulajdonságban található tömböt `level2` :

```http
    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }
```

## <a name="parse-blobs-separated-by-newlines"></a>Blobok elemzése a sortörésekkel elválasztva

Ha a blob egy sortöréssel elválasztott több JSON-entitást tartalmaz, és azt szeretné, hogy az egyes elemek különálló Azure Cognitive Search-dokumentum legyenek, választhat a JSON-sorok lehetőség közül. Ha például a következő blob (három különböző JSON-entitás) van megadva, az Azure Cognitive Search indexét három külön dokumentummal töltheti fel, amelyek mindegyike "id" és "text" mezőket tartalmaz.

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

A JSON-sorok esetében az indexelő definíciójának az alábbi példához hasonlóan kell kinéznie. Figyelje meg, hogy a parsingMode paraméter határozza meg az `jsonLines` elemzőt. 

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }
```

Azt is figyelje meg, hogy a mező-hozzárendelések kihagyhatók, hasonlóan az `jsonArray` elemzési módhoz.

## <a name="add-field-mappings"></a>Mező-hozzárendelések hozzáadása

Ha a forrás-és a célként megadott mezők nem tökéletesen illeszkednek, meghatározhatja a mezők leképezése szakaszt a kérelem törzsében explicit mező – mező típusú társításokhoz.

Az Azure Cognitive Search jelenleg nem tud közvetlenül indexelni tetszőleges JSON-dokumentumokat, mert csak primitív adattípusokat, karakterlánc-tömböket és GeoJSON-pontokat támogat. A **mező-hozzárendelések** segítségével azonban kiválaszthatja a JSON-dokumentum részeit, és "emelheti" azokat a keresési dokumentum legfelső szintű mezőibe. A mező-hozzárendelésekkel kapcsolatos alapvető tudnivalókat lásd: [mezők hozzárendelése az Azure Cognitive Search indexelő](search-indexer-field-mappings.md)szolgáltatáshoz.

Tekintse át a példában szereplő JSON-dokumentumot:

```http
    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }
```

Tegyük fel, hogy a keresési index a következő mezőkkel rendelkezik: típus, típusú `text` `Edm.String` és típusú `date` `Edm.DateTimeOffset` `tags` `Collection(Edm.String)` . Figyelje meg a "datePublished" közötti eltérést az index forrásában és `date` mezőjében. A JSON a kívánt alakzattá való leképezéséhez használja a következő mező-hozzárendeléseket:

```http
    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]
```

A leképezések mezőinek neve a [JSON-mutató](https://tools.ietf.org/html/rfc6901) jelölésének használatával van megadva. A továbbítási perjelet a JSON-dokumentum gyökerére való hivatkozással kezdheti meg, majd kiválaszthatja a kívánt tulajdonságot (tetszőleges szintű beágyazás esetén) a perjelek közötti kétirányú útvonal használatával.

Az egyes tömb elemeit nulla alapú index használatával is megtekintheti. Ha például a fenti példában szereplő "címkék" tömb első elemét szeretné kiválasztani, használja a következőhöz hasonló mezőt:

```http
    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> Ha egy mező-hozzárendelési útvonalban található forrás mező neve olyan tulajdonságra hivatkozik, amely nem szerepel a JSON-ban, akkor a leképezés hiba nélkül kimarad. Ez azért van így, hogy a dokumentumok más sémával is használhatók legyenek (amely gyakori használati eset). Mivel nincs érvényesítés, ügyelnie kell arra, hogy elkerülje az elírásokat a mező-hozzárendelési specifikációban.
>
>

## <a name="see-also"></a>Lásd még

+ [Indexelők az Azure Cognitive Searchben](search-indexer-overview.md)
+ [Azure-Blob Storage indexelése az Azure Cognitive Search](search-howto-index-json-blobs.md)
+ [CSV-Blobok indexelése az Azure Cognitive Search blob indexelő szolgáltatásával](search-howto-index-csv-blobs.md)
+ [Oktatóanyag: részben strukturált adatok keresése az Azure Blob Storage-ból](search-semi-structured-data.md)
