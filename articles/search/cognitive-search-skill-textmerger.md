---
title: Szöveg egyesítési cognitive search szakértelem – Azure Search
description: A mezők gyűjteménye szöveg egyesítése egy konszolidált mezőt. Cognitive szakértelem használja az Azure Search-felderítési bővítést folyamatban.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: ceda22394aab27f27740bb999b36e2cc46a6bd06
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57568888"
---
#    <a name="text-merge-cognitive-skill"></a>Szöveg egyesítési cognitive szakértelem

A **szöveg egyesítése** szakértelem összesíti a szöveget a mezők gyűjteménye egyetlen mezőbe. 

> [!NOTE]
> Szakértelem nincs kötve a Cognitive Services API-t, és nem terheli útmutatójához. Továbbra is ajánlott [Cognitive Services-erőforrás csatolása](cognitive-search-attach-cognitive-services.md), azonban felül a **ingyenes** erőforrás beállítás, amely korlátozza, hogy naponta napi végrehajtott információbeolvasás kis számú.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.MergeSkill

## <a name="skill-parameters"></a>Ismeretek paraméterek

A paraméterei a kis-és nagybetűket.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| insertPreTag  | Szerepelnie, előtt minden behelyettesítendő karakterlánc. Az alapértelmezett érték `" "`. Hagyja ki a helyet, állítsa az értékét `""`.  |
| insertPostTag | Karakterlánc minden Beszúrás után fogja tartalmazni. Az alapértelmezett érték `" "`. Hagyja ki a helyet, állítsa az értékét `""`.  |


##  <a name="sample-input"></a>Minta beviteli
JSON-dokumentumok biztosítása használható bemeneti szakértelem lehet:

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
Ez a példa bemutatja a korábbi bemenet, feltéve, hogy a kimenet a *insertPreTag* értékre van állítva `" "`, és *insertPostTag* értékre van állítva `""`. 

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

## <a name="extended-sample-skillset-definition"></a>A kiterjesztett minta indexmezők definíciója

Egy általános forgatókönyv a szöveg egyesítési be egy dokumentumot content mezőjének a képek (-OCR szakértelem, vagy a kép felirata szöveg) értéket képviselő szöveges alak egyesíteni. 

A következő példa indexmezők OCR szakértelem szövegeket nyerhet ki képekből a beágyazása a dokumentumba használ. Ezután létrehoz egy *merged_text* eredeti és az egyes rendszerképek OCRed szöveget tartalmazó mezőbe. Az optikai Karakterfelismerés szakértelem többet tudhat [Itt](https://docs.microsoft.com/en-us/azure/search/cognitive-search-skill-ocr).

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
A fenti példa feltételezi, hogy létezik-e a normalized-lemezképek mező. Normalized-lemezképek mező kapni, állítsa be a *imageAction* az indexelő meghatározását, hogy a konfigurációs *generateNormalizedImages* alább látható módon:

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

+ [Előre megadott képesség](cognitive-search-predefined-skills.md)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Az indexelő (REST) létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
