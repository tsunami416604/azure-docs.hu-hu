---
title: CSV-blobok indexelése az Azure Search Blob indexelőjével – Azure Search
description: Feltérképezi az Azure Blob storage használata az Azure Search-index a teljes szöveges keresés a CSV-blobok. Az indexelők automatizálni adatbetöltés a kijelölt adatforrásokhoz, például az Azure Blob storage.
ms.date: 05/02/2019
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: e7d959e77d27fb04b18f402e4056d4dea1607039
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522893"
---
# <a name="indexing-csv-blobs-with-azure-search-blob-indexer"></a>CSV-blobok indexelése az Azure Search blob indexelőjével

> [!Note]
> elemzési mód delimitedText előzetes állapotban van, nem éles használatra szánt. A [REST API verzióját 2019-05-06-Preview](search-api-preview.md) ezt a szolgáltatást biztosít. Rendszer jelenleg nem .NET SDK-t támogatja.
>

Alapértelmezés szerint [Azure Search blob indexelőjével](search-howto-indexing-azure-blob-storage.md) elemzi tagolt szöveges blobok, egy olyan adattömb szöveg. Azonban a blobok CSV-adatokat tartalmazó, gyakran szeretné kezelni az egyes sorok a blob, különálló dokumentumként. Például adja meg a következő tagolt szöveg, előfordulhat, hogy szeretné elemezni a két dokumentumot, az egyes tartalmazó, "id", "datePublished" és "címkék" mezőket: 

    id, datePublished, tags
    1, 2016-01-12, "azure-search,azure,cloud" 
    2, 2016-07-07, "cloud,mobile" 

Ebben a cikkben, megtudhatja, hogyan CSV-blobok elemezni, egy Azure Search blob indexerby beállítása a `delimitedText` elemzési mód. 

> [!NOTE]
> Kövesse az indexelő konfigurációs a [egy-a-többhöz indexelő](search-howto-index-one-to-many-blobs.md) több keresési dokumentumot egy Azure-blobból a kimeneti.

## <a name="setting-up-csv-indexing"></a>Fürt megosztott kötetei szolgáltatás indexelő beállítása
CSV-blobok indexelése, létrehozni vagy frissíteni az indexelő meghatározását, és a `delimitedText` elemzési mód a egy [indexelő létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer) kérelem:

    {
      "name" : "my-csv-indexer",
      ... other indexer properties
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "firstLineContainsHeaders" : true } }
    }

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

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional, my-folder>" }
    }   

Az indexelő:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06-Preview
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

