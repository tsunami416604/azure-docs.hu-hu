---
title: OCR kognitív keresési képesség – Azure Search
description: A képfájlokból származó szöveg kinyerése az optikai karakterfelismerés (OCR) használatával Azure Search alkoholtartalom-növelési folyamatban.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: d2b3cdac72d47afb804763c521aae0b1d5936567
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70186353"
---
# <a name="ocr-cognitive-skill"></a>OCR – kognitív képesség

Az optikai karakterfelismerési (OCR) képesség felismeri a nyomtatott és a kézírásos szövegeket a képfájlokban. Ez a képesség a Cognitive Services [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) által biztosított gépi tanulási modelleket használja. Az **OCR** -képesség a következő funkciókra mutat:

+ Az ["OCR"](../cognitive-services/computer-vision/concept-recognizing-text.md#ocr-optical-character-recognition-api) API az angoltól eltérő nyelveken használatos. 
+ Angol nyelven az új ["READ"](../cognitive-services/computer-vision/concept-recognizing-text.md#read-api) API használatos.

Az **OCR** -képesség kigyűjti a szöveget a képfájlokból. A támogatott formátumok a következők:

+ . JPEG
+ . JPG
+ .PNG
+ .BMP
+ . GIF
+ . TIFF

> [!NOTE]
> Ha a hatókört a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti, akkor [a számlázható Cognitive Services erőforrást](cognitive-search-attach-cognitive-services.md)kell csatolnia. Az API-k Cognitive Services-ben való meghívásakor felmerülő díjak, valamint a képek kinyerése a dokumentum repedési szakaszának részeként Azure Search. A dokumentumokból való szöveg kinyerése díjmentes.
>
> A beépített készségek elvégzése a meglévő Cognitive Services utólagos elszámolású [díjszabás szerint](https://azure.microsoft.com/pricing/details/cognitive-services/)történik. A rendszerkép kibontásának díjszabását a [Azure Search díjszabási oldalán](https://go.microsoft.com/fwlink/?linkid=2042400)találja.


## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterek megkülönböztetik a kis-és nagybetűket.

| Paraméternév     | Leírás |
|--------------------|-------------|
| detectOrientation | Engedélyezi a képek tájolásának automatikus észlelését. <br/> Érvényes értékek: TRUE/FALSE.|
|defaultLanguageCode | <p>  A bemeneti szöveg nyelvi kódja A támogatott nyelvek közé tartoznak a következők: <br/> zh-Hans (ChineseSimplified) <br/> zh-Hant (ChineseTraditional) <br/>cs (Cseh) <br/>da (dán) <br/>nl (holland) <br/>en (angol) <br/>Fi (finn)  <br/>fr (francia) <br/>  Németország (német) <br/>el (görög) <br/> hu (Magyar) <br/> It (olasz) <br/>  ja (Japán) <br/> Ko (koreai) <br/> NB (norvég) <br/>   pl (lengyel) <br/> PT (portugál) <br/>  ru (orosz) <br/>  es (spanyol) <br/>  SV (svéd) <br/>  TR (török) <br/> ar (arab) <br/> ro (román) <br/> sr-Cyrl (SerbianCyrillic) <br/> SR-Latn (SerbianLatin) <br/>  SK (szlovák). <br/>  unk (ismeretlen) <br/><br/> Ha a Nyelvkód nincs meghatározva vagy NULL értékű, a nyelv angolra lesz állítva. Ha a nyelv explicit módon "unk" értékre van állítva, a rendszer automatikusan észleli a nyelvet. </p> |
|lineEnding | Az egyes észlelt sorok között használandó érték. Lehetséges értékek: "Space", "CarriageReturn", "soremelés".  Az alapértelmezett érték a "space" |

Korábban egy "textExtractionAlgorithm" nevű paraméter szerepel annak megadásához, hogy a képességnek ki kell-e bontania a "nyomtatott" vagy "kézzel írott" szöveget.  Ez a paraméter elavult, és már nem szükséges, mivel a legújabb olvasási API-algoritmus képes mindkét típusú szöveg kinyerésére egyszerre.  Ha a szaktudás definíciója már tartalmazza ezt a paramétert, akkor nem kell eltávolítania, de a továbbiakban nem lesz használatban, és mindkét típusú szöveg kibontásra kerül, függetlenül attól, hogy mire van beállítva.

## <a name="skill-inputs"></a>Szaktudás bemenetei

| Bemenet neve      | Leírás                                          |
|---------------|------------------------------------------------------|
| image         | Összetett típus. A jelenleg csak az Azure Blob indexelő által létrehozott "/Document/normalized_images" mezővel működik, ```imageAction``` ha a értéke nem ```none```a (z) értékre van állítva. További információért tekintse meg a [mintát](#sample-output) .|


## <a name="skill-outputs"></a>Szaktudás kimenetei
| Kimenet neve     | Leírás                   |
|---------------|-------------------------------|
| text          | A képből kinyert egyszerű szöveg.   |
| layoutText    | Összetett típus, amely leírja a kinyert szöveget, valamint azt a helyet, ahol a szöveg található.|


## <a name="sample-definition"></a>Minta definíciója

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

## <a name="sample-text-and-layouttext-output"></a>Minta szöveg-és layoutText kimenete

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

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Minta: A beágyazott képekből kinyert szöveg egyesítése a dokumentum tartalmával.

A szöveges egyesítés gyakori használati esete, hogy egyesíthető a képek szöveges ábrázolása (OCR-képességből származó szöveg vagy kép felirata) egy dokumentum tartalmi mezőjébe.

A következő példa készségkészlet létrehoz egy *merged_text* mezőt. Ez a mező a dokumentum szöveges tartalmát, valamint a dokumentumban beágyazott képek OCRed szövegét tartalmazza.

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
A fenti készségkészlet példa azt feltételezi, hogy a normalizált lemezképek mező létezik. A mező létrehozásához állítsa a *imageAction* -konfigurációt az indexelő definíciójában a *generateNormalizedImages* értékre, ahogy az alábbi ábrán látható:

```json
{
  //...rest of your indexer definition goes here ...
  "parameters": {
    "configuration": {
      "dataToExtract":"contentAndMetadata",
      "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Lásd még
+ [Előre definiált képességek](cognitive-search-predefined-skills.md)
+ [TextMerger-szakértelem](cognitive-search-skill-textmerger.md)
+ [Készségkészlet definiálása](cognitive-search-defining-skillset.md)
+ [Indexelő létrehozása (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
