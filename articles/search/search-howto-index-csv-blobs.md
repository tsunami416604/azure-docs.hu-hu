---
title: "Az Azure Search-indexelőt, blob CSV blobok indexelő |} Microsoft Docs"
description: Megtudhatja, hogyan CSV BLOB az Azure Search index
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: ed3c9cff-1946-4af2-a05a-5e0b3d61eb25
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 12/28/2017
ms.author: eugenesh
ms.openlocfilehash: 40b7f1f4f75d389a64329e7d8fd3c7feb79d5e55
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>Az Azure Search-indexelőt, blob CSV blobok indexelő
Alapértelmezés szerint [blob Azure Search-indexelőt](search-howto-indexing-azure-blob-storage.md) elemez szöveg blobok szöveg egyetlen adattömb jelölik. Azonban a CSV-adatokat tartalmazó BLOB, gyakran szeretné kezelni a blob, különálló dokumentumként soronként. Például adja a következő tagolt szöveget: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

érdemes elemezni a 2-dokumentumok esetében az egyes "id", "datePublished" és "címkék" mezőket tartalmazó.

Ebben a cikkben megismerheti, hogyan elemzése az Azure Search blob indexelő CSV blobok lesz. 

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg csak előzetes verzióban érhető el. Csak a REST API verziójával érhető el **2015-02-28-előzetes**. Adjon ne feledje, az előzetes API-k tesztelésében és értékelésében készült, és nem használható üzemi környezetben. 
> 
> 

## <a name="setting-up-csv-indexing"></a>Fürt megosztott kötetei szolgáltatás indexelő beállítása
A CSV-blobok index, definíció létrehozása vagy módosítása egy indexelő rendelkező a `delimitedText` elemzése mód:  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Hozzon létre indexelő API további részletekért tekintse meg [létrehozása indexelő](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

`firstLineContainsHeaders`azt jelzi, hogy az első sort (kötelező) minden egyes blob tartalmaz-e a fejléceket.
Blobok nem tartalmaznak egy kezdeti fejlécsort, ha a fejlécek az indexelő konfigurációban kell megadni: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Az elválasztó karakter használatával testre szabhatja a `delimitedTextDelimiter` konfigurációs beállítás. Példa:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Jelenleg csak az UTF-8 kódolást használata támogatott. Ha segítségre más kódolás, tudassa velünk, a [a UserVoice webhelyén](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> A tagolt szövegfájl elemzése mód használata esetén Azure Search azt feltételezi, hogy az adatforrás összes BLOB lesz-e a fürt megosztott kötetei szolgáltatás. Ha támogatja a megosztott Fürtkötet, valamint nem CSV-blobok vegyesen ugyanarra az adatforrásra van szüksége, tudassa velünk, a [a UserVoice webhelyén](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Példák
Ez minden üzembe együtt, az alábbiakban a teljes adattartalmat példák. 

Adatforrás: 

    POST https://[service name].search.windows.net/datasources?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Az indexelő:

    POST https://[service name].search.windows.net/indexers?api-version=2015-02-28-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-search-better"></a>Segítsen az Azure Search továbbfejlesztésében
Ha a szolgáltatás-kérelmek vagy ötleteket javításai, lépjen kapcsolatba velünk a a [UserVoice webhelyén](https://feedback.azure.com/forums/263029-azure-search/).

