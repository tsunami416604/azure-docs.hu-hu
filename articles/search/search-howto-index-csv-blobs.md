---
title: Keresés CSV-Blobok között
titleSuffix: Azure Cognitive Search
description: CSV-fájl kinyerése és importálása az Azure Blob Storage-ból a delimitedText-elemzési mód használatával.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bf600890bfed570e712a159005b8ef5267298cc0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76122321"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>CSV-Blobok indexelése delimitedText-elemzési mód és blob-indexek használatával az Azure-ban Cognitive Search

Alapértelmezés szerint az [Azure Cognitive Search blob indexelő](search-howto-indexing-azure-blob-storage.md) egy különálló szövegként elemzi a tagolt szöveges blobokat. A CSV-fájlokat tartalmazó Blobokkal azonban gyakran a blob minden sorát külön dokumentumként kell kezelni. Például a következő tagolt szöveg esetében érdemes lehet két dokumentumra elemezni, melyek mindegyike "id", "datePublished" és "Tags" mezőket tartalmaz: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Ebből a cikkből megtudhatja, hogyan elemezheti a CSV-blobokat egy Azure Cognitive Search blob indexelő `delimitedText` használatával az elemzési mód beállításával. 

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

A elválasztó karaktert a `delimitedTextDelimiter` konfigurációs beállítás használatával szabhatja testre. Például:

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

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexelő

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
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

