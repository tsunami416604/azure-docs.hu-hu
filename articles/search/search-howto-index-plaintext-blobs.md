---
title: Keresés az egyszerű szöveges Blobok között
titleSuffix: Azure Cognitive Search
description: A keresési indexelő beállítása egyszerű szöveg kinyerésére az Azure-blobokból a teljes szöveges kereséshez az Azure Cognitive Searchban.
manager: nitinme
author: mgottein
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 2b01b9a3782d25660462d0cc2bf3aec87baf0023
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403784"
---
# <a name="how-to-index-plain-text-blobs-in-azure-cognitive-search"></a>Egyszerű szöveges Blobok indexelése az Azure-ban Cognitive Search

Ha blob- [Indexelő](search-howto-indexing-azure-blob-storage.md) használatával Kinyeri a kereshető szöveget a teljes szöveges kereséshez, különböző elemzési módokat hívhat meg, hogy jobb indexelési eredményekhez lehessen jutni. Alapértelmezés szerint az indexelő a tagolt szöveges blobokat egyetlen darab szövegként elemzi. Ha azonban az összes blob egyszerű szöveget tartalmaz ugyanabban a kódolásban, akkor a **szöveges elemzési mód**használatával jelentősen javíthatja az indexelési teljesítményt.

## <a name="set-up-plain-text-indexing"></a>Egyszerű szöveges indexelés beállítása

Az egyszerű szöveges Blobok indexeléséhez hozzon létre vagy frissítsen egy indexelő definíciót a `parsingMode` Configuration tulajdonsággal a `text` [create Indexer](/rest/api/searchservice/create-indexer) -kérelemben:

```http
    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }
```

Alapértelmezés szerint a `UTF-8` rendszer a kódolást feltételezi. Másik kódolás megadásához használja a következő `encoding` konfigurációs tulajdonságot: 

```http
    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }
```

## <a name="request-example"></a>Példa kérésre

Az elemzési módok az indexelő definíciójában vannak megadva.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-plaintext-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "parameters" : { "configuration" : { "parsingMode" : "delimitedText", "delimitedTextHeaders" : "id,datePublished,tags" } }
    }
```

## <a name="help-us-make-azure-cognitive-search-better"></a>Segítsen nekünk, hogy jobban megtegyük az Azure Cognitive Search
Ha a funkciókra vonatkozó kérések vagy ötletek vannak, adja meg a [UserVoice](https://feedback.azure.com/forums/263029-azure-search/). Ha segítségre van szüksége a meglévő szolgáltatás használatához, tegye fel a kérdését [stack Overflowra](https://stackoverflow.microsoft.com/questions/tagged/18870).

## <a name="see-also"></a>Lásd még

* [Indexelők az Azure Cognitive Searchben](search-indexer-overview.md)
* [BLOB-indexelő konfigurálása](search-howto-indexing-azure-blob-storage.md)
* [A blob indexelésének áttekintése](search-blob-storage-integration.md)