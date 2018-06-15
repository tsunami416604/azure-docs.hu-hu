---
title: Az Azure Search-indexelőt, blob CSV blobok indexelő |} Microsoft Docs
description: Megtudhatja, hogyan CSV BLOB az Azure Search index
author: chaosrealm
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 12/28/2017
ms.author: eugenesh
ms.openlocfilehash: bf65ab7858ba792418e325e7a025ee1bd88bbb27
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34363036"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Az Azure Search-indexelőt, blob CSV blobok indexelő
Alapértelmezés szerint [blob Azure Search-indexelőt](search-howto-indexing-azure-blob-storage.md) elemez szöveg blobok szöveg egyetlen adattömb jelölik. Azonban a CSV-adatokat tartalmazó BLOB, gyakran szeretné kezelni a blob, különálló dokumentumként soronként. Például a következő tagolt szöveges megadott, érdemes elemzéséhez be két dokumentumot, minden egyes "id", "datePublished" és "címkék" mezőket tartalmazó: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Ebből a cikkből megismerheti, hogyan elemzése az Azure Search blob indexelő CSV blobok lesz. 

> [!IMPORTANT]
> Ez a funkció jelenleg nyilvános előzetes verziójában, és nem használható üzemi környezetben. További információkért lásd: [REST api-version = 2017-11-11-Előnézet](search-api-2017-11-11-preview.md). 
> 

## <a name="setting-up-csv-indexing"></a>Fürt megosztott kötetei szolgáltatás indexelő beállítása
A CSV-blobok index, definíció létrehozása vagy módosítása egy indexelő rendelkező a `delimitedText` elemzése mód:  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Hozzon létre indexelő API további részletekért tekintse meg [létrehozása indexelő](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

`firstLineContainsHeaders` azt jelzi, hogy az első sort (kötelező) minden egyes blob tartalmaz-e a fejléceket.
Blobok nem tartalmaznak egy kezdeti fejlécsort, ha a fejlécek az indexelő konfigurációban kell megadni: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Az elválasztó karakter használatával testre szabhatja a `delimitedTextDelimiter` konfigurációs beállítás. Példa:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Jelenleg csak az UTF-8 kódolást használata támogatott. Ha segítségre más kódolás, szavazhatnak az [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> A tagolt szövegfájl elemzése mód használata esetén Azure Search azt feltételezi, hogy az adatforrás összes BLOB lesz-e a fürt megosztott kötetei szolgáltatás. Ha támogatja a megosztott Fürtkötet, valamint nem CSV-blobok vegyesen ugyanarra az adatforrásra van szüksége, adjon szavazhatnak az [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Példák
Ez minden üzembe együtt, az alábbiakban a teljes adattartalmat példák. 

Adatforrás: 

    POST https://[service name].search.windows.net/datasources?api-version=2017-11-11-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Az indexelő:

    POST https://[service name].search.windows.net/indexers?api-version=2017-11-11-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Segítsen az Azure Search továbbfejlesztésében
Ha a szolgáltatás-kérelmek vagy ötleteket javításai, információk megadása a [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

