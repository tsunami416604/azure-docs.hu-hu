---
title: Optikai Karakterfelismerés cognitive search szakértelem (Azure Search) |} A Microsoft Docs
description: Szöveg kinyerése képfájlok Azure Search-felderítési bővítést folyamatban.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 234651ad3672982e4de9617561a926712697945a
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2018
ms.locfileid: "45734033"
---
# <a name="ocr-cognitive-skill"></a>Optikai Karakterfelismerés cognitive szakértelem

A **OCR** szakértelem szöveges állomány kinyerésére képfájlok. Támogatott formátumok a következők:

+ . JPEG
+ . JPG
+ . PNG
+ . BMP
+ . GIF

> [!NOTE]
> A kognitív keresés nyilvános előzetes verzióban érhető el. Képességcsoport végrehajtási, és a lemezkép kinyerése és a normalizálási jelenleg rendelkezésre állnak az ingyenes. Később az ezen funkciók díjszabásáról jelentjük be. 

## <a name="skill-parameters"></a>Ismeretek paraméterek

A paraméterei a kis-és nagybetűket.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| detectOrientation | Lehetővé teszi, hogy a kép tájolásának autodetection. <br/> Érvényes értékek: true / false (hamis).|
|defaultLanguageCode | <p>  A bemeneti szöveg nyelvkódja. A támogatott nyelvek közé tartoznak a következők: <br/> zh-Hans (ChineseSimplified) <br/> zh-Hant (ChineseTraditional) <br/>cs (Cseh) <br/>da (dán) <br/>NL (holland) <br/>en (angol nyelven) <br/>Fi (finn)  <br/>FR (francia) <br/>  Németország (németországi) <br/>el (görög) <br/> hu (magyar) <br/> azt (olasz) <br/>  japán (japán) <br/> ko (koreai) <br/> NB (norvég) <br/>   pl (lengyel) <br/> csendes-óceáni idő (portugál) <br/>  a kérelemegység (orosz) <br/>  a gen (spanyol) <br/>  SV (svéd) <br/>  tr (török) <br/> ar (arab) <br/> ro (Románia) <br/> az SR-Cyrl (SerbianCyrillic) <br/> az SR-Latn (SerbianLatin) <br/>  SK (szlovák). <br/>  UNK (ismeretlen) <br/><br/> Ha a nyelvi kód nincs megadva vagy null értékű, a nyelv az hálózati kártya automatikus észlelése. </p> |
| textExtractionAlgorithm | "nyomtatott" vagy "kézírásos". A "kézírásos" szöveg felismerése OCR algoritmus jelenleg előzetes verzióban érhető el, és csak angol nyelven. |

## <a name="skill-inputs"></a>Ismeretek bemenetek

| Bemeneti név      | Leírás                                          |
|---------------|------------------------------------------------------|
| image         | Komplex típus. Az Azure Blob indexelőjével által előállított jelenleg csak akkor működik a "/ dokumentum/normalized_images" mezőt, amikor ```imageAction``` értékre van állítva ```generateNormalizedImages```. Tekintse meg a [minta](#sample-output) további információt.|


## <a name="skill-outputs"></a>Ismeretek kimenetek
| Kimeneti név     | Leírás                   |
|---------------|-------------------------------|
| szöveg          | A kép kinyert egyszerű szöveget.   |
| layoutText    | Komplex típus, amely leírja a kinyert szöveg, valamint a helyet, ahol a szöveg található.|


## <a name="sample-definition"></a>Minta-definíció

```json
{
    "skills": [
      {
        "description": "Extracts text (plain and structured) from image.",
        "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
        "context": "/document/normalized_images/*",
        "defaultLanguageCode": null,
        "detectOrientation": true,
        "inputs": [
          {
            "name": "image",
            "source": "/document/normalized_images/*"
          }
        ],
        "outputs": [
          {
            "name": "text",
            "targetName": "myText"
          },
          {
            "name": "layoutText",
            "targetName": "myLayoutText"
          }
        ]
      }
    ]
 }
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>Szöveg- és layoutText kimeneti példa

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Példa: A dokumentum tartalma a beágyazott képek kinyert szöveget egyesítése.

Egy közös Szövegegyesítő funkcióban lehetővé teszi a képek (-OCR szakértelem, vagy a kép felirata szöveg) értéket képviselő szöveges alak egyesítése egy dokumentumot content mezőjének be. 

A következő példa indexmezők létrehoz egy *merged_text* a dokumentum a beágyazott szöveges tartalmát a dokumentumot, valamint a lemezképek mindegyike OCRed szöveget tartalmazó mezőbe. 

#### <a name="request-body-syntax"></a>Kéréstörzs szintaxisa
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
          "name": "mergedText", "targetname" : "merged_text"
        }
      ]
    }
  ]
}
```
A fenti indexmezők példa feltételezi, hogy, hogy létezik-e a normalized-lemezképek mező. Ebben a mezőben létrehozásához állítsa be a *imageAction* az indexelő meghatározását, hogy a konfigurációs *generateNormalizedImages* alább látható módon:

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
+ [TextMerger szakértelem](cognitive-search-skill-textmerger.md)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Az indexelő (REST) létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
