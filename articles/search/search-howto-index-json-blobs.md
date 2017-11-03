---
title: "Az Azure Search-indexelőt, blob JSON-blobok indexelő"
description: "Az Azure Search-indexelőt, blob JSON-blobok indexelő"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 57e32e51-9286-46da-9d59-31884650ba99
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 09/07/2017
ms.author: eugenesh
ms.openlocfilehash: bf4d3a517e1308a142d21cffff64f3c6e104eb62
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="indexing-json-blobs-with-azure-search-blob-indexer"></a>Az Azure Search-indexelőt, blob JSON-blobok indexelő
Ez a cikk bemutatja, hogyan konfigurálhatja az Azure Search blob indexelő strukturált tartalom kibontása a blobok JSON tartalmazó.

## <a name="scenarios"></a>Forgatókönyvek
Alapértelmezés szerint [blob Azure Search-indexelőt](search-howto-indexing-azure-blob-storage.md) JSON-blobok elemez, egyetlen adattömb szöveg. Gyakran meg szeretné őrizni a JSON-dokumentumok struktúráját. Ha például adott a JSON-dokumentum

    {
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Előfordulhat, hogy szeretne elemezni a "text", "datePublished" és "címkék" mezőket egy Azure Search dokumentumba.

Azt is megteheti, ha a blobok tartalmaz egy **JSON-objektumok tömbje**, érdemes lehet egy külön Azure Search dokumentum válik a tömb egyes elemei. Ha például adott egy blobot a a JSON:  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

az Azure Search-index három különálló dokumentumok, az "id" és "text" mező feltöltheti azt.

> [!IMPORTANT]
> A funkció elemzése JSON-tömb jelenleg előzetes verzió. Csak a REST API verziójával érhető el **2016 09-01. dátumú előnézeti**. Ne feledje, tekintse meg az API-k tesztelésében és értékelésében készült, és nem használható üzemi környezetben.
>
>

## <a name="setting-up-json-indexing"></a>JSON indexelő beállítása
JSON-blobok indexelő hasonlít a rendszeres dokumentum kibontásával. Először hozza létre a datasource, pontosan a szokásos módon: 

    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

Ezután hozzon létre a cél search-index, ha még nem rendelkezik. 

Végül hozzon létre egy indexelőt, és állítsa be a `parsingMode` paramétert `json` (az index minden egyes blob egyetlen dokumentumként) vagy `jsonArray` (Ha a blobok JSON-tömbök tartalmazhat, és meg kell különálló dokumentum kezelendő tömb egyes elemei):

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

Szükség esetén használja **hozzárendelések mezőben** a forrás JSON-dokumentum feltöltéséhez használt a cél search-index a következő szakaszban látható tulajdonságainak kiválasztásához.

> [!IMPORTANT]
> Amikor `json` vagy `jsonArray` mód elemzése, Azure Search azt feltételezi, hogy, hogy az adatforrás összes BLOB tartalmazza JSON. Ha támogatja a JSON és a nem JSON blobok vegyesen ugyanarra az adatforrásra van szüksége, ossza meg velünk a [a UserVoice webhelyén](https://feedback.azure.com/forums/263029-azure-search).
>
>

## <a name="using-field-mappings-to-build-search-documents"></a>Dokumentumok keresése összeállításához mező hozzárendelések használata
Jelenleg Azure Search nem tud indexelni tetszőleges JSON-dokumentumokat közvetlenül, mert csak egyszerű adattípusok, karakterlánc tömbök és GeoJSON-pont támogatja. Használhat azonban **hozzárendelések mezőben** válassza ki a JSON-dokumentum részeit, és "növekedési" azokat a keresési dokumentum felső szintű mezőkbe. Mező hozzárendelések alapokkal kapcsolatos további tudnivalókért lásd: [Azure keresési indexelő mező hozzárendelések hidat képeznek az adatforrások és a keresési indexek közötti különbségekről](search-indexer-field-mappings.md).

A példa JSON-dokumentum érkező vissza:

    {
        "article" : {
             "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Tegyük fel, rendelkezik egy keresési indexszel, a következő mezőket: `text` típusú `Edm.String`, `date` típusú `Edm.DateTimeOffset`, és `tags` típusú `Collection(Edm.String)`. A JSON megfeleltetni kívánt alakzattá, használja a következő mező leképezéseket:

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

A JSON-dokumentumokat csak egyszerű legfelső szintű tulajdonságot tároljon, ha egyáltalán nem esetleg mező leképezéseket. Például ha a JSON így néz ki, a legfelső szintű tulajdonságok "text", "datePublished" és "címkék" közvetlenül felel meg a megfelelő mezőket a keresési index:

    {
       "text" : "A hopefully useful article explaining how to parse JSON blobs",
       "datePublished" : "2016-04-13"
       "tags" : [ "search", "storage", "howto" ]    
     }

Íme egy mező leképezések teljes indexelő adattartalom:

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
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

## <a name="indexing-nested-json-arrays"></a>Beágyazott JSON-tömbök indexelése
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

## <a name="help-us-make-azure-search-better"></a>Segítsen az Azure Search továbbfejlesztésében
Ha a szolgáltatás-kérelmek vagy ötleteket javításai, kapcsolatba velünk a a [UserVoice webhelyén](https://feedback.azure.com/forums/263029-azure-search/).
