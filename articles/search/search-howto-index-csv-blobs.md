---
title: CSV-Blobok indexelése az Azure Cognitive Search blob indexelő szolgáltatásával
titleSuffix: Azure Cognitive Search
description: CSV-Blobok bejárása az Azure Blob Storage-ban teljes szöveges kereséshez Azure Cognitive Search index használatával. Az indexelő automatizálja az adatfeldolgozást a kiválasztott adatforrásokhoz, például az Azure Blob Storage-hoz.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 18d0eb704deba80bf83b5cae0a598f47181700f7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793778"
---
# <a name="how-to-index-csv-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>CSV-Blobok indexelése blob indexelő használatával az Azure-ban Cognitive Search 

> [!Note]
> a delimitedText-elemzési mód előzetes verzióban érhető el, és nem éles használatra készült. A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs .NET SDK-támogatás.
>

Alapértelmezés szerint az [Azure Cognitive Search blob indexelő](search-howto-indexing-azure-blob-storage.md) egy különálló szövegként elemzi a tagolt szöveges blobokat. A CSV-fájlokat tartalmazó Blobokkal azonban gyakran a blob minden sorát külön dokumentumként kell kezelni. Például a következő tagolt szöveg esetében érdemes lehet két dokumentumra elemezni, melyek mindegyike "id", "datePublished" és "Tags" mezőket tartalmaz: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Ebből a cikkből megtudhatja, hogyan elemezheti a CSV-blobokat egy Azure Cognitive Search blob indexerby az `delimitedText` elemzési mód beállításával. 

> [!NOTE]
> Kövesse az indexelő konfigurációs javaslatait az [egy-a-többhöz indexelésben](search-howto-index-one-to-many-blobs.md) , hogy egyetlen Azure-blobból végezzen több keresési dokumentumot.

## <a name="setting-up-csv-indexing"></a>CSV-indexelés beállítása
CSV-Blobok indexeléséhez hozzon létre vagy frissítsen egy indexelő definíciót a [create Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer) -kérelem `delimitedText` elemzési módjával:

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders` azt jelzi, hogy az egyes Blobok első (nem üres) sora fejléceket tartalmaz.
Ha a Blobok nem tartalmaznak kezdeti fejlécet, a fejléceket az indexelő konfigurációjában kell megadni: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

A elválasztó karakter testreszabható a `delimitedTextDelimiter` konfigurációs beállítás használatával. Példa:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Jelenleg csak az UTF-8 kódolás támogatott. Ha más kódolások támogatására van szüksége, szavazzon rá a [UserVoice](https://feedback.azure.com/forums/263029-azure-search)-on.

> [!IMPORTANT]
> Ha a tagolt szöveges elemzési módot használja, az Azure Cognitive Search feltételezi, hogy az adatforrásban lévő összes blob CSV-fájl lesz. Ha a CSV-és a nem CSV-Blobok együttes használatát is meg kell adni ugyanabban az adatforrásban, akkor szavazzon rá a [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Példák kérése
Mindez együttesen a teljes hasznos adatokat mutatja. 

DataSource 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexelő

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-cognitive-search-better"></a>Segítsen nekünk, hogy jobban megtegyük az Azure Cognitive Search
Ha a funkciókra vonatkozó kérések vagy ötletek vannak, adja meg a [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

