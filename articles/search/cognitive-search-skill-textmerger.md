---
title: Szöveg egyesítési kognitív keresési szakértelem (Azure Search) |} Microsoft Docs
description: Szöveg mezőinek gyűjteményét egyesítése egy konszolidált mezőt. Egy Azure Search dúsító csővezeték kognitív szakértelem használja.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: ba779ebcbc791f9caa60948feeb38b88a23ef379
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640662"
---
#    <a name="text-merge-cognitive-skill"></a>Szöveg egyesítési kognitív szakértelem

A **szöveg egyesítése** szakértelem összesíti egy mező mezőinek gyűjteményét szöveg. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.TextMerger

## <a name="skill-parameters"></a>Szakértelem paraméterek

A paraméterei a kis-és nagybetűket.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| insertPreTag  | Minden való beszúrása előtt kell szerepelnie a karakterláncot. Az alapértelmezett érték `" "`. Hagyja ki ezt a helyet, adja meg az értéket `""`.  |
| insertPostTag | Karakterlánc után minden beszúrási fogja tartalmazni. Az alapértelmezett érték `" "`. Hagyja ki ezt a helyet, adja meg az értéket `""`.  |


##  <a name="sample-input"></a>A minta bemenet
Egy JSON-dokumentum használható bemeneti biztosítva szakértelem lehet:

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The brown fox jumps over the dog" ,
             "itemsToInsert": ["quick", "lazy"],
             "offsets": [3, 28],
           }
      }
    ]
}
```

##  <a name="sample-output"></a>Példa kimenet
Ez a példa bemutatja a korábbi bemeneti, feltéve, hogy a kimenet a *insertPreTag* értéke `" "`, és *insertPostTag* értéke `""`. 

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

## <a name="extended-sample-skillset-definition"></a>Kiterjesztett minta skillset meghatározása

Egy általános forgatókönyv szöveg egyesítési használatára vonatkozó a képek (egy OCR szakértelem, vagy a kép felirat szöveg) képviselő szöveges alakot egyesíteni a tartalom mezőbe egy dokumentum. 

A következő példa skillset szöveg kinyerése a dokumentum a beágyazott képek a OCR szakértelem használja. Ezután létrehoz egy *merged_text* mind az eredeti, és az egyes lemezképek OCRed szöveget tartalmazó mezőt. 

```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
        "name": "OCR skill",
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
      "@odata.type": "#Microsoft.Skills.Util.TextMerger",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " "
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
          "name": "mergedText", "targetname" : "merged_text"
        }
      ]
    }
  ]
}
```
A fenti példa feltételezi, hogy van-e a normalizált képek mező. Ahhoz, hogy normalizált képek mező, állítsa be a *imageAction* konfigurációját az indexelő-definíciót a *generateNormalizedImages* alább látható módon:

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
+ [Egy skillset definiálása](cognitive-search-defining-skillset.md)
+ [Az indexelő (REST) létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer)