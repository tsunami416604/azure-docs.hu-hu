---
title: CSV-Blobok indexelése Azure Search blob indexelő-Azure Search
description: CSV-Blobok bejárása az Azure Blob Storage-ban a teljes szöveges kereséshez Azure Search index használatával. Az indexelő automatizálja az adatfeldolgozást a kiválasztott adatforrásokhoz, például az Azure Blob Storage-hoz.
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: b135fd1a0758567a7b504996bf442a913741fe59
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656761"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>CSV-Blobok indexelése Azure Search blob indexelő

> [!Note]
> a delimitedText-elemzési mód előzetes verzióban érhető el, és nem éles használatra készült. A [REST API 2019-05-06-es verziójának előzetes verziója](search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs .NET SDK-támogatás.
>

Alapértelmezés szerint a [Azure Search blob indexelő](search-howto-indexing-azure-blob-storage.md) a tagolt szöveges blobokat egyetlen darab szövegként elemzi. A CSV-fájlokat tartalmazó Blobokkal azonban gyakran a blob minden sorát külön dokumentumként kell kezelni. Például a következő tagolt szöveg esetében érdemes lehet két dokumentumra elemezni, melyek mindegyike "id", "datePublished" és "Tags" mezőket tartalmaz: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Ebből a cikkből megtudhatja, hogyan elemezheti a CSV-blobokat egy Azure Search blob `delimitedText` indexerby az elemzési mód beállításával. 

> [!NOTE]
> Kövesse az indexelő konfigurációs javaslatait az [egy-a-többhöz indexelésben](search-howto-index-one-to-many-blobs.md) , hogy egyetlen Azure-blobból végezzen több keresési dokumentumot.

## <a name="setting-up-csv-indexing"></a>CSV-indexelés beállítása
CSV-Blobok indexeléséhez hozzon létre vagy frissítsen egy indexelő definíciót a `delimitedText` [create Indexer](https://docs.microsoft.com/rest/api/searchservice/create-indexer) -kérelem elemzési módjával:

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders`azt jelzi, hogy az egyes Blobok első (nem üres) sora fejléceket tartalmaz.
Ha a Blobok nem tartalmaznak kezdeti fejlécet, a fejléceket az indexelő konfigurációjában kell megadni: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

A elválasztó karaktert a `delimitedTextDelimiter` konfigurációs beállítás használatával szabhatja testre. Példa:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Jelenleg csak az UTF-8 kódolás támogatott. Ha más kódolások támogatására van szüksége, szavazzon rá a [UserVoice](https://feedback.azure.com/forums/263029-azure-search)-on.

> [!IMPORTANT]
> Ha a tagolt szöveges elemzési módot használja, Azure Search feltételezi, hogy az adatforrásban lévő összes blob CSV-fájl lesz. Ha a CSV-és a nem CSV-Blobok együttes használatát is meg kell adni ugyanabban az adatforrásban, akkor szavazzon rá a [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
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

## <a name="help-us-make-azure-search-better"></a>Segítsen nekünk, hogy jobban Azure Search
Ha a funkciókra vonatkozó kérések vagy ötletek vannak, adja meg a [UserVoice](https://feedback.azure.com/forums/263029-azure-search/).

