---
title: Optikai Karakterfelismerés cognitive search szakértelem – Azure Search
description: Szöveg kinyerése képfájlok optikai karakterfelismerés (OCR) segítségével az Azure Search-felderítési bővítést folyamatban.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 6fa316452b5b9f56c33de00b96c2cf57c40edfcc
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56429655"
---
# <a name="ocr-cognitive-skill"></a>Optikai Karakterfelismerés cognitive szakértelem

Optikai karakterfelismerés (OCR) szakértelem felismeri a képfájlok nyomtatott és kézzel írt szöveg. Ezen a képzettségi használja a gépi tanulási modellek által biztosított [számítógépes Látástechnológiai](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) a Cognitive Services. A **OCR** szakértelem képez le a következő funkciókat:

+ Ha textExtractionAlgorithm van állítva a "kézírásos", a ["RecognizeText"](../cognitive-services/computer-vision/quickstarts-sdk/csharp-hand-text-sdk.md) funkció használható.
+ Ha textExtractionAlgorithm van állítva a "nyomtatott", a ["OCR"](../cognitive-services/computer-vision/concept-extracting-text-ocr.md) funkció angoltól eltérő nyelvű szolgál. Az angol nyelvű tájékoztatáshoz az új ["Szöveg felismerése"](../cognitive-services/computer-vision/concept-recognizing-text.md) funkcióinak, nyomtatott szöveg szolgál.

A **OCR** szakértelem szöveges állomány kinyerésére képfájlok. Támogatott formátumok a következők:

+ .JPEG
+ . JPG
+ .PNG
+ .BMP
+ .GIF

> [!NOTE]
> 2018. December 21., kezdési is [Cognitive Services-erőforrás csatolása](cognitive-search-attach-cognitive-services.md) és a egy Azure Search-képességek alkalmazási lehetőségét. Ez lehetővé teszi indexmezők végrehajtási díjszabási elindításához. Ezen a napon azt is már díjszabási kép kinyerése a dokumentumfeltörést fázis részeként. A dokumentumok szövegkinyerés továbbra is ingyenesen kínáljuk.
>
> [Beépített kognitív szakértelem](cognitive-search-predefined-skills.md) végrehajtás díja a [használatalapú-as-, a Cognitive Services nyissa meg az árat](https://azure.microsoft.com/pricing/details/cognitive-services), azonos értékelje, ha végrehajtotta a feladat közvetlenül. Kép kinyerése nem egy Azure Search költségekkel, jelenleg az előzetes verzió áron érhető el. További információkért lásd: a [díjszabását ismertető oldalt az Azure Search](https://go.microsoft.com/fwlink/?linkid=2042400) vagy [számlázás módját works](search-sku-tier.md#how-billing-works).

## <a name="skill-parameters"></a>Ismeretek paraméterek

A paraméterei a kis-és nagybetűket.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| detectOrientation | Lehetővé teszi, hogy a kép tájolásának autodetection. <br/> Érvényes értékek: true / false (hamis).|
|defaultLanguageCode | <p>  A bemeneti szöveg nyelvkódja. A támogatott nyelvek közé tartoznak a következők: <br/> zh-Hans (ChineseSimplified) <br/> zh-Hant (ChineseTraditional) <br/>cs (Cseh) <br/>da (dán) <br/>NL (holland) <br/>en (angol nyelven) <br/>Fi (finn)  <br/>FR (francia) <br/>  Németország (németországi) <br/>el (görög) <br/> hu (magyar) <br/> azt (olasz) <br/>  japán (japán) <br/> ko (koreai) <br/> NB (norvég) <br/>   pl (lengyel) <br/> csendes-óceáni idő (portugál) <br/>  a kérelemegység (orosz) <br/>  a gen (spanyol) <br/>  sv (Swedish) <br/>  tr (török) <br/> ar (arab) <br/> ro (Románia) <br/> sr-Cyrl (SerbianCyrillic) <br/> sr-Latn (SerbianLatin) <br/>  SK (szlovák). <br/>  UNK (ismeretlen) <br/><br/> Ha a nyelvi kód nincs megadva vagy null értékű, a nyelv az angol nyelvű lesz beállítva. Ha a nyelv explicit módon "unk" értékre van állítva, a nyelv nem automatikusan észleli. </p> |
| textExtractionAlgorithm | "nyomtatott" vagy "kézírásos". A "kézírásos" szöveg felismerése OCR algoritmus jelenleg előzetes verzióban érhető el, és csak angol nyelven. |

## <a name="skill-inputs"></a>Ismeretek bemenetek

| Bemeneti név      | Leírás                                          |
|---------------|------------------------------------------------------|
| image         | Komplex típus. Az Azure Blob indexelőjével által előállított jelenleg csak akkor működik a "/ dokumentum/normalized_images" mezőt, amikor ```imageAction``` értékre van állítva egy eltérő ```none```. Tekintse meg a [minta](#sample-output) további információt.|


## <a name="skill-outputs"></a>Ismeretek kimenetek
| Kimeneti név     | Leírás                   |
|---------------|-------------------------------|
| szöveg          | A kép kinyert egyszerű szöveget.   |
| layoutText    | Komplex típus, amely ismerteti a kinyert szöveg és a hely, ahol a szöveg található.|


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

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Minta: Az egyesítés a dokumentum tartalma a beágyazott képek kinyert szöveget.

Egy közös Szövegegyesítő funkcióban lehetővé teszi a képek (-OCR szakértelem, vagy a kép felirata szöveg) értéket képviselő szöveges alak egyesítése egy dokumentumot content mezőjének be. 

A következő példa indexmezők létrehoz egy *merged_text* mező. Ez a mező szöveges tartalmát a dokumentumhoz, és a dokumentum a beágyazott képek mindegyike OCRed szöveget tartalmazza. 

#### <a name="request-body-syntax"></a>Kéréstörzs szintaxisa
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
