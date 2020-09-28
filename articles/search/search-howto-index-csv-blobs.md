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
ms.date: 07/11/2020
ms.openlocfilehash: f9c01e8e31e78c277a7a3ec1e5d8d0c32b58f8bc
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403653"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>CSV-Blobok indexelése delimitedText-elemzési mód és blob-indexek használatával az Azure-ban Cognitive Search

Alapértelmezés szerint az [Azure Cognitive Search blob indexelő](search-howto-indexing-azure-blob-storage.md) egy különálló szövegként elemzi a tagolt szöveges blobokat. A CSV-fájlokat tartalmazó Blobokkal azonban gyakran a blob minden sorát külön dokumentumként kell kezelni. Például a következő tagolt szöveg esetében érdemes lehet két dokumentumra elemezni, melyek mindegyike "id", "datePublished" és "Tags" mezőket tartalmaz: 

```text
id, datePublished, tags
1, 2016-01-12, "azure-search,azure,cloud"
2, 2016-07-07, "cloud,mobile"
```

Ebből a cikkből megtudhatja, hogyan elemezheti a CSV-blobokat egy Azure Cognitive Search blob indexelő használatával az `delimitedText` elemzési mód beállításával. 

> [!NOTE]
> Kövesse az indexelő konfigurációs javaslatait az [egy-a-többhöz indexelésben](search-howto-index-one-to-many-blobs.md) , hogy egyetlen Azure-blobból végezzen több keresési dokumentumot.

## <a name="setting-up-csv-indexing"></a>CSV-indexelés beállítása
CSV-Blobok indexeléséhez hozzon létre vagy frissítsen egy indexelő definíciót a `delimitedText` [create Indexer](/rest/api/searchservice/create-indexer) -kérelem elemzési módjával:

```http
    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }
```

`firstLineContainsHeaders` azt jelzi, hogy az egyes Blobok első (nem üres) sora fejléceket tartalmaz.
Ha a Blobok nem tartalmaznak kezdeti fejlécet, a fejléceket az indexelő konfigurációjában kell megadni: 

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 
```

A elválasztó karaktert a konfigurációs beállítás használatával szabhatja testre `delimitedTextDelimiter` . Például:

```http
"parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }
```

> [!NOTE]
> Jelenleg csak az UTF-8 kódolás támogatott. Ha más kódolások támogatására van szüksége, szavazzon rá a [UserVoice](https://feedback.azure.com/forums/263029-azure-search)-on.

> [!IMPORTANT]
> Ha a tagolt szöveges elemzési módot használja, az Azure Cognitive Search feltételezi, hogy az adatforrásban lévő összes blob CSV-fájl lesz. Ha a CSV-és a nem CSV-Blobok együttes használatát is meg kell adni ugyanabban az adatforrásban, akkor szavazzon rá a [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Példák kérése
Mindez együttesen a teljes hasznos adatokat mutatja. 

DataSource 

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   
```

Indexelő

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }
```

## <a name="help-us-make-azure-cognitive-search-better"></a>Segítsen nekünk, hogy jobban megtegyük az Azure Cognitive Search
Ha a funkciókra vonatkozó kérések vagy ötletek vannak, adja meg a [UserVoice](https://feedback.azure.com/forums/263029-azure-search/). Ha segítségre van szüksége a meglévő szolgáltatás használatához, tegye fel a kérdését [stack Overflowra](https://stackoverflow.microsoft.com/questions/tagged/18870).