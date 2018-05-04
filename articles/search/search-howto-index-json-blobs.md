---
title: Az Azure Search-indexelőt, blob JSON-blobok indexelő
description: Az Azure Search-indexelőt, blob JSON-blobok indexelő
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: eugenesh
ms.openlocfilehash: 64d16182ce1992ec312ad1620d9d5cf11e0ddea8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Az Azure Search-indexelőt, blob JSON-blobok indexelő
Ez a cikk bemutatja, hogyan konfigurálhatja egy Azure Search blob indexelőt strukturált tartalom kibontása a JSON-blobok az Azure Blob Storage tárolóban.

Az Azure Blob storage JSON-blobok jellemzően vagy egy JSON-dokumentumot, vagy egy JSON-tömb. A blob indexelő az Azure Search vagy konstrukció, attól függően, hogyan tudja értelmezni a **parsingMode** paraméter a kérésre.

| JSON-dokumentum | parsingMode | Leírás | Rendelkezésre állás |
|--------------|-------------|--------------|--------------|
| Egy blob egy | `json` | JSON-blobok elemez, egyetlen adattömb szöveg. Minden egyes JSON-blob lesz egy Azure Search dokumentumot. | Mindkét általánosan elérhető [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) és [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) API-k. |
| Egy blob több | `jsonArray` | A BLOB, ahol a tömb egyes elemei lesz-e egy külön Azure Search dokumentum egy JSON-tömb elemzi.  | A képen a [REST api-version =`2016-09-01-Preview` ](search-api-2016-09-01-preview.md) és [.NET SDK előzetes](https://aka.ms/search-sdk-preview). |

> [!Note]
> Előnézeti API-k tesztelésében és értékelésében készült, és nem használható üzemi környezetben.
>

## <a name="setting-up-json-indexing"></a>JSON indexelő beállítása
JSON-blobok indexelő hasonlít az összes indexelő az Azure Search közös Háromrészes munkafolyamat rendszeres dokumentum kibontásával.

### <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása

Az első lépés arra, hogy az indexelő által használt adatforrás kapcsolati adatait. A megadott típus adatforrás-ként `azureblob`, meghatározza, hogy mely adatok kinyerése viselkedések az indexelő hívják. JSON-BLOB indexelő az adatforrás megadása definition megegyezik a JSON-dokumentumokat és a tömbök. 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="step-2-create-a-target-search-index"></a>2. lépés: A cél search-index létrehozása 

Indexelő van párosítva a sémát indexeli. Ha az API-t (ahelyett, hogy a portál) használ, készítsen index előre, hogy a adhatja meg azt az indexelési művelet. 

> [!Note]
> Indexelő ki vannak téve a portálon keresztül a **importálási** általánosan elérhető indexelő korlátozott számú műveletet. Az importálási munkafolyamattal gyakran, gyakran állíthat össze egy előzetes index a a forráshelyen metaadatok alapján. További információkért lásd: [adatok importálása az Azure Search a portálon](search-import-data-portal.md).

### <a name="step-3-configure-and-run-the-indexer"></a>3. lépés: Konfigurálja, és az indexelő futtatása

Az adatforrás és az index már eddig parsingMode független. Azonban a 3. lépésben az indexelő konfigurációs elérési diverges attól függően, hogy hogyan kívánja a JSON-blob tartalom értelmezése és az Azure Search-index strukturált.

Az indexelő meghívásakor tegye a következőket:

+ Állítsa be a **parsingMode** paramétert `json` (az index minden egyes blob egyetlen dokumentumként) vagy `jsonArray` (Ha a blobok JSON-tömbök tartalmazhat, és a különálló dokumentum kezelendő tömb egyes elemei kell).

+ Ezenkívül szükség esetén **hozzárendelések mezőben** kiválaszthatja, mely a JSON-dokumentum tulajdonságainak a cél search-index feltöltésére használatos olyan. A JSON-tömbök hogy létezik-e a tömb alsó szintű tulajdonságainál jelző, ahol a tömb a blob belül helyezkedik el, a dokumentum legfelső szintű beállítása.

> [!IMPORTANT]
> Amikor `json` vagy `jsonArray` mód elemzése, Azure Search azt feltételezi, hogy, hogy az adatforrás összes BLOB tartalmazza JSON. Ha támogatja a JSON és a nem JSON blobok vegyesen ugyanarra az adatforrásra van szüksége, ossza meg velünk a [a UserVoice webhelyén](https://feedback.azure.com/forums/263029-azure-search).


## <a name="how-to-parse-single-json-blobs"></a>Hogyan kell elemezni egy JSON-blobok

Alapértelmezés szerint [blob Azure Search-indexelőt](search-howto-indexing-azure-blob-storage.md) JSON-blobok elemez, egyetlen adattömb szöveg. Gyakran meg szeretné őrizni a JSON-dokumentumok struktúráját. Tegyük fel például, a következő JSON-dokumentum rendelkezik az Azure Blob storage:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

### <a name="indexer-definition-for-single-json-blobs"></a>Egyetlen JSON-blobok indexelő definíciója

Az Azure Search blob indexelő használ, az előző példához hasonló JSON-dokumentum elemzi egy Azure Search-dokumentumban. Az indexelő index "text", "datePublished" és "címkék" azonos nevű és típusos célmező alapján forrásból egyeztetésével tölti be.

Az indexelési művelet törzsében konfigurációs valósul meg. A visszaírási, hogy az adatforrás-objektum, korábban definiált, határozza meg az adatforrás típusa és a kapcsolat adatait. Emellett a cél index is létezniük kell a szolgáltatás egy üres tárolóként. Ütemezés és a paraméterek nem kötelező, de kihagyásakor őket az indexelő futása azonnal, használatával `json` elemzési módot.

Egy teljesen megadott kérelem a következő lehet:

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

Amint azt a mező leképezések esetén nincs szükség. Megadott index a "text", "datePublished, és a"címkék"mezők, a blob indexelő is következtethető ki a megfelelő hozzárendelési leképezés szerepel a kérelemben mező nélkül.

## <a name="how-to-parse-json-arrays-preview"></a>Hogyan elemzése JSON-tömbök (előzetes verzió)

Azt is megteheti hogy kérheti a JSON-tömb előzetes funkció. Ez a funkció akkor hasznos, ha a blobok tartalmaz egy *JSON-objektumok tömbje*, és azt szeretné, hogy minden elem egy külön Azure Search dokumentum válik. Például a következő JSON-blob megadott, töltheti fel az Azure Search-index három különálló dokumentumok, az "id" és "text" mező.  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

### <a name="indexer-definition-for-a-json-array"></a>A JSON-tömb indexelő meghatározása

A JSON-tömb, az indexelő kérelem használja az előzetes verziót API és a `jsonArray` elemző. Ezek a csak két tömb vonatkozó követelményei JSON-blobok indexelő.

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Figyelje meg újra, mező-leképezések esetén nincs szükség. Egy index ugyanezzel az "id" és "text" mező megadott, a blob indexelő is következtethető ki, a helyes leképezés egy mezőlista leképezés nélkül.

### <a name="nested-json-arrays"></a>Beágyazott JSON-tömbök
Mi történik, ha szeretné JSON-objektumok tömbje, de a tömb index van beágyazva valahol a dokumentumot? Kiválaszthatja, melyik tulajdonsága tartalmazza a tömb használatával a `documentRoot` konfigurációs tulajdonság. Ha például a blobok néznek ki:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Ez a konfiguráció segítségével index a tömb található a `level2` tulajdonság:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="using-field-mappings-to-build-search-documents"></a>Dokumentumok keresése összeállításához mező hozzárendelések használata

Forrás és cél mező nem tökéletesen igazított, határozhat meg egy mezőszakaszt leképezési explicit mezők társítások a kérés törzsében.

Jelenleg Azure Search nem tud indexelni tetszőleges JSON-dokumentumokat közvetlenül, mert csak egyszerű adattípusok, karakterlánc tömbök és GeoJSON-pont támogatja. Használhat azonban **hozzárendelések mezőben** válassza ki a JSON-dokumentum részeit, és "növekedési" azokat a keresési dokumentum felső szintű mezőkbe. Mező hozzárendelések alapokkal kapcsolatos további tudnivalókért lásd: [mezőben az Azure Search indexelők hozzárendelések](search-indexer-field-mappings.md).

A példa JSON-dokumentum újralátogatás:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Tegyük fel, a search-index a következő mezőket: `text` típusú `Edm.String`, `date` típusú `Edm.DateTimeOffset`, és `tags` típusú `Collection(Edm.String)`. A forrás "datePublished" között eltérés megfigyelheti és `date` mező mellett az index. A JSON megfeleltetni kívánt alakzattá, használja a következő mező leképezéseket:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

A forrás mezőnevek a leképezésben megadott használatával a [JSON mutató](http://tools.ietf.org/html/rfc6901) jelöléssel. Tekintse meg a JSON-dokumentum gyökerébe perjellel kezdődik, majd válassza ki a kívánt tulajdonságot (szinten tetszőleges beágyazási) előre törtvonallal elválasztott elérési úttal.

Is hivatkozni lehet az egyes tömbelemek a nulla alapú indexét. Válassza ki a fenti példában a "címkék" tömb első eleme, például használja ehhez hasonló mező leképezéseket:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Ha a forrás Mezőnév mező leképezési elérési utak olyan tulajdonságon, amely nem létezik a JSON hivatkozik, a rendszer kihagyja a leképezéseket, hiba nélkül. Ez történik, hogy egy másik séma (Ez egy gyakori használati eset) dokumentumok is támogatott. Mivel nem lehet érvényesíteni, kell, hogy az a mező leképezésspecifikáció gépelési elkerülése érdekében.
>
>

## <a name="example-indexer-request-with-field-mappings"></a>Példa: Az indexelő kérelem mező hozzárendelések

A következő példa a teljes mértékben megadott indexelő hasznos adatok között, beleértve mező hozzárendelések:

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


## <a name="help-us-make-azure-search-better"></a>Segítsen az Azure Search továbbfejlesztésében
Ha a szolgáltatás-kérelmek vagy ötleteket javításai, kapcsolatba velünk a a [UserVoice webhelyén](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="see-also"></a>Lásd még

+ [Az Azure Search indexelő](search-indexer-overview.md)
+ [Az Azure Search Azure Blob Storage indexelése](search-howto-index-json-blobs.md)
+ [Az Azure Search-indexelőt, blob CSV blobok indexelő](search-howto-index-csv-blobs.md)
+ [Oktatóanyag: Az Azure Blob storage félig strukturált adatok keresése ](search-semi-structured-data.md)