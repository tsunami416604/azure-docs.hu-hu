---
title: Kognitív keresési képességek szövegének egyesítése – Azure Search
description: Szöveg egyesítése mezők egy összevont mezőjébe Használja ezt a kognitív képességet egy Azure Search alkoholtartalom-növelési folyamat során.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: 1e88fcc13d97d92cf9b35616ecb7d71c2d24db1f
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265264"
---
#    <a name="text-merge-cognitive-skill"></a>Szöveg egyesítése – kognitív képességek

A **szöveges egyesítési** képesség a mezők gyűjteményében lévő szöveget egyetlen mezőben összesíti. 

> [!NOTE]
> Ez a képesség nem kötődik Cognitive Services API-hoz, és nem kell fizetnie a használatért. Továbbra is [csatlakoztatnia kell egy Cognitive Services-erőforrást](cognitive-search-attach-cognitive-services.md), hogy felülírja az **ingyenes** erőforrás-beállítást, amely naponta csak kis mennyiségű napi dúsítást korlátozza.

## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Text. MergeSkill

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterek megkülönböztetik a kis-és nagybetűket.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| insertPreTag  | Minden Beszúrás előtt szerepeltetni kívánt karakterlánc. Az alapértelmezett érték `" "`. A szóköz kihagyása érdekében állítsa a értéket `""`a következőre:.  |
| insertPostTag | Az összes beszúrás után szerepeltetni kívánt karakterlánc. Az alapértelmezett érték `" "`. A szóköz kihagyása érdekében állítsa a értéket `""`a következőre:.  |


##  <a name="sample-input"></a>Minta bemenet
Az ehhez a képességhez használható, felhasználható bemenetet biztosító JSON-dokumentum a következő lehet:

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "text": "The brown fox jumps over the dog",
        "itemsToInsert": ["quick", "lazy"],
        "offsets": [3, 28],
      }
    }
  ]
}
```

##  <a name="sample-output"></a>Példa kimenet
Ez a példa az előző bemenet kimenetét mutatja be, feltéve, hogy a *insertPreTag* be `" "`van állítva, és a *insertPostTag* értékre van állítva `""`. 

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "mergedText": "The quick brown fox jumps over the lazy dog"
      }
    }
  ]
}
```

## <a name="extended-sample-skillset-definition"></a>Kiterjesztett minta készségkészlet-definíciója

A szöveges egyesítés használatának gyakori forgatókönyve, hogy egyesítse a képek szöveges megjelenítését (OCR-képességből származó szöveg vagy kép felirata) egy dokumentum tartalom mezőjébe. 

A következő példában a készségkészlet az OCR-képességet használja a dokumentumba ágyazott képek szövegének kinyeréséhez. Ezután létrehoz egy *merged_text* mezőt, amely az eredeti és a OCRed szöveget is tartalmazza az egyes képekből. Az OCR-képességről [itt](https://docs.microsoft.com/azure/search/cognitive-search-skill-ocr)olvashat bővebben.

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset" 
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
A fenti példa azt feltételezi, hogy a normalizált lemezképek mező létezik. A normalizált lemezképek mező beszerzéséhez állítsa a *imageAction* -konfigurációt az indexelő definíciójában a *generateNormalizedImages* értékre, ahogy az alábbi ábrán látható:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters":{
    "configuration":{
        "dataToExtract":"contentAndMetadata",
        "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Lásd még

+ [Előre definiált képességek](cognitive-search-predefined-skills.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Indexelő létrehozása (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
