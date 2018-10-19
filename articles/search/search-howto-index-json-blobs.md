---
title: JSON-blobok indexelése az Azure Search blob indexelőjével
description: JSON-blobok indexelése az Azure Search blob indexelőjével
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.openlocfilehash: a4689093508c3287e60da9d4668393e71211fbdd
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49405702"
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>JSON-blobok indexelése az Azure Search blob indexelőjével
Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Search blob indexelőjével strukturálatlan tartalom használatával nyerhetők JSON-blobok, az Azure Blob storage-ban.

Az Azure Blob storage-ban JSON-blobok jellemzően egyetlen JSON-dokumentumok és a egy JSON-tömböt. Az az Azure Search blob indexelőjével vagy konstrukció, attól függően, hogyan állíthatja tudja elemezni a **parsingMode** paraméter a kérésre.

| JSON-dokumentumok | parsingMode | Leírás | Rendelkezésre állás |
|--------------|-------------|--------------|--------------|
| Egy blob egy | `json` | Elemzi a JSON-blobok, a szöveg egy olyan adattömb. Egyes JSON-blobok az Azure Search egyetlen dokumentum válik. | Általánosan elérhető a [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) és [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) API-k. |
| Több blobonkénti | `jsonArray` | Elemzi a blobban, ahol a tömb egyes elemei lesz-e egy külön Azure Search-dokumentum egy JSON-tömböt.  | Előzetes verzióban elérhető a [REST api-version =`2017-11-11-Preview` ](search-api-2017-11-11-preview.md) és [.NET SDK előzetes](https://aka.ms/search-sdk-preview). |

> [!Note]
> Előzetes API-k tesztelési és értékelési célokra szolgálnak, és nem éles környezetekben használható.
>

## <a name="setting-up-json-indexing"></a>JSON-indexelő beállítása
JSON-blobok indexelése a hasonló a normál dokumentumot kivonás a közös Háromrészes munkafolyamat az Azure Search szolgáltatásban az összes indexelőre.

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

### <a name="step-2-create-a-target-search-index"></a>2. lépés: Hozzon létre egy cél keresési indexhez 

Indexelők az indexsémát van párosítva. Ha használja az API-t (a portál helyett), készítse elő az index előre, hogy a adhatja meg azt az indexelési művelet. 

> [!Note]
> Az indexelők a portálon keresztül érhetők el a **importálás** mindenki számára elérhető indexelők esetében korlátozott számú műveletet. Gyakran előfordul az importálási munkafolyamattal gyakran hozhatnak létre a metaadatok a forrás alapján előzetes index. További információkért lásd: [adatok importálása az Azure Search szolgáltatásba a portálon](search-import-data-portal.md).

### <a name="step-3-configure-and-run-the-indexer"></a>3. lépés: Konfigurálja, és az indexelő futtatása

Mostanáig az adatforrás és az index parsingMode független lett. Azonban a 3. lépésben az indexelő konfigurációjának, az elérési út diverges attól függően, hogyan szeretné a JSON-blobját tartalom elemezni és struktúrája az Azure Search-index.

Az indexelő hívásakor tegye a következőket:

+ Állítsa be a **parsingMode** paramétert `json` (az index minden egyes blob egyetlen dokumentumként) vagy `jsonArray` (Ha a blobok tartalmazzák a JSON-tömbök és egy külön dokumentum kell tekinteni a tömb egyes elemei kell).

+ Ha szükséges, használjon **mezőleképezéseivel** kiválasztása a forrás JSON-dokumentum melyik tulajdonságait használják a cél search-index feltöltéséhez. A JSON-tömbök Ha a tömb már létezik egy alacsonyabb szintű tulajdonságként jelző, ahol a tömb a blobon belüli kerül a dokumentum legfelső szintű beállítása.

> [!IMPORTANT]
> Ha használ `json` vagy `jsonArray` elemzési mód, az Azure Search feltételezi, hogy, hogy az adatforrás összes blobot tartalmaz-e a JSON. Ha ugyanazt az adatforrást támogatja a JSON-t, és nem JSON blobok vegyesen szeretne, ossza meg velünk az [UserVoice-webhelyünkön](https://feedback.azure.com/forums/263029-azure-search).


## <a name="how-to-parse-single-json-blobs"></a>Hogyan kell elemezni, egy JSON-blobok

Alapértelmezés szerint [Azure Search blob indexelőjével](search-howto-indexing-azure-blob-storage.md) egy olyan adattömb szöveg, JSON-blobok elemzi. Gyakran szeretné megőrizni a JSON-dokumentumok struktúráját. Tegyük fel például, a következő JSON-dokumentum rendelkezik az Azure Blob storage-ban:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

### <a name="indexer-definition-for-single-json-blobs"></a>Az indexelő definíciója egy JSON-blobok

Az Azure Search blob indexelőjével az előző példához hasonló JSON-dokumentumok elemzése van egy Azure Search-dokumentumban. Az indexelő index összekapcsolja a "text", "datePublished" és "címkék" azonos névvel és típusos cél mező alapján a forrásból tölt be.

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

## <a name="how-to-parse-json-arrays-preview"></a>Hogyan kell elemezni a JSON-tömb (előzetes verzió)

Másik megoldásként kérheti a JSON-tömb az előzetes verziójú szolgáltatásra. Ez a funkció akkor hasznos, ha a blobok tartalmazzák egy *JSON-objektumok tömbje*, és azt szeretné, hogy minden elem egy külön Azure Search-dokumentum lesz. Például adja meg a következő JSON-blobját, fel lehet tölteni az Azure Search-index három külön dokumentumot, az egyes "id" és "szöveg" mezőkkel.  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

### <a name="indexer-definition-for-a-json-array"></a>Az indexelő definíciója egy JSON-tömb

Az indexelő kérelem egy JSON-tömb, használja az előzetes verziójú API és a `jsonArray` elemzőt. Ezek a JSON-blobok indexelése csak két tömb-specifikus követelmények vonatkoznak.

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Újra figyelje meg, hogy a mező-leképezések nem szükségesek. Adja meg az "id" és "szöveg" mező index, a blob indexelőjével is kikövetkeztetni a megfelelő leképezés a mezőlista leképezés nélkül.

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

## <a name="using-field-mappings-to-build-search-documents"></a>Mező-leképezések használatával hozhat létre a dokumentumok keresése

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

A forrás mezőnevek a leképezések használatával vannak megadva a [JSON-mutató](http://tools.ietf.org/html/rfc6901) jelöléssel. Kezdődhet perjellel, tekintse meg a JSON-dokumentumok, a legfelső szintű, majd válassza ki a kívánt tulajdonságot (a beágyazási tetszőleges szintjén) előre perjellel elválasztva elérési úttal.

Is hivatkozni lehet az egyes tömbelemek rekordsémáját egy nulla alapú index használatával. Válasszon a fenti példában a "címkék" tömb első elemeként, például használja egy ehhez hasonló mezőleképezés:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Ha egy mező leképezés elérési útja egy forrás mező nevét, amely nem szerepel a JSON tulajdonságra hivatkozik, a rendszer kihagyta a leképezéseket hiba nélkül. Ez azért történik, így is támogatjuk a dokumentumok (Ez egy gyakori alkalmazási helyzet) eltérő sémával. Nem történik ellenőrzés, mert szüksége ügyeljen arra, hogy az a mező leképezésspecifikáció elgépelések elkerülése érdekében.
>
>

## <a name="example-indexer-request-with-field-mappings"></a>Példa): Mezőleképezések az indexelő kérelem (

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


## <a name="help-us-make-azure-search-better"></a>Segítsen jobbá Azure Search
Ha a funkcióra vonatkozó javaslata vagy ötlete van javításai, fel velünk a kapcsolatot a a [UserVoice webhelyén](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="see-also"></a>Lásd még

+ [Indexelők az Azure Search szolgáltatásban](search-indexer-overview.md)
+ [Az Azure Search szolgáltatással az Azure Blob Storage indexelése](search-howto-index-json-blobs.md)
+ [CSV-blobok indexelése az Azure Search blob indexelőjével](search-howto-index-csv-blobs.md)
+ [Oktatóanyag: Az Azure Blob storage-ból részben strukturált adatok keresése ](search-semi-structured-data.md)
