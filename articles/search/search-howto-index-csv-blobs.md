---
title: CSV-blobok indexelése az Azure Search blob indexelőjével |} A Microsoft Docs
description: Ismerje meg, az Azure Search szolgáltatással a CSV-blobok indexelése
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.openlocfilehash: b1f97b5e9542e32096bb060bce40e7b9620d0f49
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406075"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>CSV-blobok indexelése az Azure Search blob indexelőjével
Alapértelmezés szerint [Azure Search blob indexelőjével](search-howto-indexing-azure-blob-storage.md) elemzi tagolt szöveges blobok, egy olyan adattömb szöveg. Azonban a blobok CSV-adatokat tartalmazó, gyakran szeretné kezelni az egyes sorok a blob, különálló dokumentumként. Például adja meg a következő tagolt szöveg, előfordulhat, hogy szeretné elemezni a két dokumentumot, az egyes tartalmazó, "id", "datePublished" és "címkék" mezőket: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Ebből a cikkből megismerheti, hogyan elemezni a CSV-blobok, az az Azure Search blob indexelőjével lesz. 

> [!IMPORTANT]
> Ez a funkció jelenleg nyilvános előzetes verzióban érhető el, és nem éles környezetekben használható. További információkért lásd: [REST api-version = 2017-11-11-Preview](search-api-2017-11-11-preview.md). 
> 

## <a name="setting-up-csv-indexing"></a>Fürt megosztott kötetei szolgáltatás indexelő beállítása
CSV-blobok indexelése, létrehozni vagy frissíteni az indexelő meghatározását, és a `delimitedText` elemzési mód:  

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

Az indexelő API létrehozása a további részletekért tekintse meg [indexelő létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

`firstLineContainsHeaders` azt jelzi, hogy az első sort (nem üres) minden egyes blob fejléceket tartalmazza-e.
Blobok nem tartalmaznak egy kezdeti fejlécsort, ha a fejlécek az indexelő konfigurációjának kell megadni: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

Az elválasztó karakter használatával testre szabhatja a `delimitedTextDelimiter` konfigurációs beállítás. Példa:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Jelenleg csak az UTF-8 kódolást használata támogatott. Ha támogatásra van szüksége más kódolás, szavazzon rá a [UserVoice](https://feedback.azure.com/forums/263029-azure-search).

> [!IMPORTANT]
> A tagolt szöveges elemzési mód használata esetén az Azure Search azt feltételezi, hogy az adatforrás összes BLOB lesz-e a fürt megosztott kötetei szolgáltatás. Ha ugyanazt az adatforrást a fürt megosztott kötetei szolgáltatás és a-CSV blobok kiszolgálnia van szüksége, kérjük szavazzon rá a [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Példák
Ez az összes üzembe együtt, az alábbiakban a teljes hasznos példákat. 

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

## <a name="help-us-make-azure-search-better"></a>Segítsen jobbá Azure Search
Ha funkcióra vonatkozó javaslata vagy ötlete van javításai, adja meg a bemenetet a [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

