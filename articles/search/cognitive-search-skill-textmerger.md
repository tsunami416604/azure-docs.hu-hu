---
title: Szövegegyesítés kognitív készség
titleSuffix: Azure Cognitive Search
description: Mezők gyűjteményéből származó szöveg egyesítése egyetlen összevont mezőbe. Ezt a kognitív képességeket az Azure Cognitive Search AI-bővítési folyamatában használhatja.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 98ea416305f080850d85498f74693eb2d45b0944
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162344"
---
#   <a name="text-merge-cognitive-skill"></a>Szövegegyesítés kognitív készség

A **Szövegegyesítés** szakértelem egyetlen mezőbe egyesíti a szöveget. 

> [!NOTE]
> Ez a szakértelem nem kötődik a Cognitive Services API-t, és nem kell fizetnie a használata. Továbbra is [csatolja a Cognitive Services-erőforrás,](cognitive-search-attach-cognitive-services.md)azonban az **ingyenes** erőforrás beállítás, amely korlátozza, hogy egy kis számú napi dúsítások naponta.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| beszúrás: PreTag  | Minden beszúrás előtt szerepelnie kell a karakterláncnak. Az alapértelmezett érték `" "`. A szóköz kihagyásához `""`állítsa az értéket a értékre.  |
| insertPostTag | Minden beszúrás után bekell vonni a karakterláncot. Az alapértelmezett érték `" "`. A szóköz kihagyásához `""`állítsa az értéket a értékre.  |


##  <a name="sample-input"></a>Mintabevitel
A JSON-dokumentum, amely használható bemenetet biztosít ehhez a szakértelemhez, a következő lehet:

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "text": "The brown fox jumps over the dog",
        "itemsToInsert": ["quick", "lazy"],
        "offsets": [3, 28]
      }
    }
  ]
}
```

##  <a name="sample-output"></a>Példa kimenet
Ez a példa az előző bemenet kimenetét mutatja, feltételezve, hogy a *insertPreTag* beállítása `" "`a , és a *InsertPostTag* beállítása `""`a. 

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

## <a name="extended-sample-skillset-definition"></a>Kiterjesztett minta-képességek definíciója

A Szövegegyesítés gyakori forgatókönyve a képek szöveges ábrázolása (ocr-szakértelemből származó szöveg vagy kép felirata) egyesítése a dokumentum tartalommezőjébe. 

A következő példa skillset használja az OCR szakértelem kinyerése szöveget a dokumentumba ágyazott képek. Ezután létrehoz egy *merged_text* mezőt, amely az eredeti és az OCRed szöveget is tartalmazza az egyes képekről. Az OCR [készségről](https://docs.microsoft.com/azure/search/cognitive-search-skill-ocr)itt olvashat bővebben.

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
A fenti példa feltételezi, hogy létezik normalizált képmező. Normalizált képek mező bekerüléséhez állítsa be az *imageAction* konfigurációt az indexelő definíciójában a *NormalizedImages generálására* az alábbi módon:

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

+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
+ [Indexelő létrehozása (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
