---
title: Keresés csv-blobok között
titleSuffix: Azure Cognitive Search
description: CsV-t kinyerhet és importálhat az Azure Blob storage-ból a dekaktálási mód használatával.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bf600890bfed570e712a159005b8ef5267298cc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122321"
---
# <a name="how-to-index-csv-blobs-using-delimitedtext-parsing-mode-and-blob-indexers-in-azure-cognitive-search"></a>CSV-blobok indexelése a dekaktálási móddal és a Blob-indexelőkkel az Azure Cognitive Search szolgáltatásban

Alapértelmezés szerint [az Azure Cognitive Search blob indexelő](search-howto-indexing-azure-blob-storage.md) elemzi a tagolt szöveges blobokat egyetlen szövegtömbként. Azonban a blobok tartalmazó CSV-adatok, gyakran szeretné kezelni a blob minden egyes sora külön dokumentumként. A következő tagolt szöveg et tekintve például két dokumentumra érdemes elemezni, amelyek mindegyike "id", "datePublished" és "tags" mezőket tartalmaz: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Ebben a cikkben megtudhatja, hogyan elemezheti a CSV-blobok egy Azure `delimitedText` Cognitive Search blob indexelő az elemzési mód beállításával. 

> [!NOTE]
> Kövesse az indexelő konfigurációs [javaslatokat az egy-a-többhöz indexelés](search-howto-index-one-to-many-blobs.md) több keresési dokumentumok egy Azure blobkimeneti.

## <a name="setting-up-csv-indexing"></a>CsV indexelés beállítása
CsV-blobok indexeléséhez hozzon létre `delimitedText` vagy frissítsen egy indexelő-definíciót az elemzési móddal egy [Indexelő létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer) kérésen:

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

`firstLineContainsHeaders`azt jelzi, hogy az egyes blobok első (nem üres) sora fejléceket tartalmaz.
Ha a blobok nem tartalmaznak kezdeti fejlécsort, a fejléceket meg kell adni az indexelő konfigurációjában: 

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } } 

A határoló karaktert a `delimitedTextDelimiter` konfigurációs beállítással szabhatja testre. Példa:

    "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextDelimiter" : "|" } }

> [!NOTE]
> Jelenleg csak az UTF-8 kódolás támogatott. Ha más kódolások támogatására van szüksége, szavazzon rá a [UserVoice](https://feedback.azure.com/forums/263029-azure-search)-

> [!IMPORTANT]
> A tagolt szövegelemzési mód használatakor az Azure Cognitive Search feltételezi, hogy az adatforrás összes blobja CSV lesz. Ha ugyanabban az adatforrásban a CSV és a nem CSV-blobok keverékét kell támogatnia, kérjük, szavazzon rá a [UserVoice](https://feedback.azure.com/forums/263029-azure-search).
> 
> 

## <a name="request-examples"></a>Példák kérése
Összerakva ezt az egészet, itt vannak a teljes hasznos teher példák. 

Datasource: 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Indexelő:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-csv-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }

## <a name="help-us-make-azure-cognitive-search-better"></a>Segítsen nekünk az Azure Cognitive Search jobbá tenni
Ha javítási funkciókérései vagy ötletei vannak, adja meg a [uservoice-ra](https://feedback.azure.com/forums/263029-azure-search/)vonatkozó véleményét.

