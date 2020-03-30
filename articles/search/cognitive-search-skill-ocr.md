---
title: OCR kognitív készség
titleSuffix: Azure Cognitive Search
description: Szöveg kinyerése képfájlokból optikai karakterfelismerés (OCR) használatával az Azure Cognitive Search dúsítási folyamatában.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bdb510113a8d65ac04b54e77158f46d03cccd9de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72791930"
---
# <a name="ocr-cognitive-skill"></a>OCR kognitív készség

Az **Optikai karakterfelismerés (OCR)** szakértelem felismeri a képfájlokban nyomtatott és kézzel írt szöveget. Ez a szakértelem a Cognitive Services [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) által biztosított gépi tanulási modelleket használja. Az **OCR** szakértelem a következő funkciókra van leképezve:

+ Az ["OCR"](../cognitive-services/computer-vision/concept-recognizing-text.md#ocr-optical-character-recognition-api) API az angoltól eltérő nyelvekhez használatos. 
+ Az angol nyelv esetében az új ["Olvasás"](../cognitive-services/computer-vision/concept-recognizing-text.md#read-api) API-t használja a használat.

Az **OCR** szakértelem szöveget bont ki a képfájlokból. A támogatott fájlformátumok a következők:

+ . Jpeg
+ . Jpg
+ . Png
+ . Bmp
+ . Gif
+ . Tiff

> [!NOTE]
> A hogy a feldolgozás gyakoriságának növelésével, további dokumentumok hozzáadásával vagy további AI-algoritmusok hozzáadásával bővíti a hatókört, [egy számlázható Cognitive Services-erőforrást kell csatolnia.](cognitive-search-attach-cognitive-services.md) A díjak akkor keletkeznek, amikor API-kat hívnak a Cognitive Servicesben, és az Azure Cognitive Search dokumentumfeltörési szakaszának részeként képkinyerést végeznek. A dokumentumokból történő szövegkinyerésért nem kell díjat fizetni.
>
> A beépített képességek végrehajtása a meglévő [Cognitive Services díja int.](https://azure.microsoft.com/pricing/details/cognitive-services/) A képkinyerésdíj szabása az [Azure Cognitive Search díjszabási lapján található.](https://go.microsoft.com/fwlink/?linkid=2042400)


## <a name="skill-parameters"></a>Szakértelem paraméterei

A paraméterekben különbözőnek számítanak a kis- és a nagybetűk.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| észlelésirány | Lehetővé teszi a képtájolás automatikus észlelését. <br/> Érvényes értékek: true / false.|
|defaultLanguageCode | <p>  A bemeneti szöveg nyelvkódja. A támogatott nyelvek közé tartoznak a következők: <br/> zh-Hans (kínaiegyszerűsített) <br/> zh-Hant (hagyományos kínai) <br/>cs (cseh) <br/>da (dán) <br/>nl (holland) <br/>hu (Angol) <br/>fi (finn)  <br/>fr (francia) <br/>  de (német) <br/>el (görög) <br/> hu (magyar) <br/> ez (olasz) <br/>  ja (japán) <br/> ko (koreai) <br/> nb (norvég) <br/>   pl (lengyel) <br/> pt (portugál) <br/>  ru (orosz) <br/>  es (spanyol) <br/>  sv (svéd) <br/>  tr (török) <br/> ar (arab) <br/> ro (román) <br/> sr-Cyrl (szerbcirkuli) <br/> sr-Latn (szerblatin) <br/>  (szlovák). <br/>  unk (ismeretlen) <br/><br/> Ha a nyelvkód meg határozatlan vagy null, a nyelv angolra lesz állítva. Ha a nyelv explicit módon "unk" lesz, a nyelv automatikusan észlelhető lesz. </p> |
|lineEnding (sorvége) | Az egyes észlelt sorok között használandó érték. Lehetséges értékek: 'Space','CarriageReturn','LineFeed'.  Az alapértelmezett érték a "Space" |

Korábban volt egy "textExtractAlgorithm" nevű paraméter, amely azt adta meg, hogy a szakértelemnek ki kell-e vonnia a "nyomtatott" vagy a "kézzel írt" szöveget.  Ez a paraméter elavult, és már nem szükséges, mivel a legújabb Olvasási API algoritmus képes mindkét típusú szöveg egyszerre történő kibontására.  Ha a szakértelem-definíció már tartalmazza ezt a paramétert, akkor nem kell eltávolítania, de a továbbiakban nem lesz használva, és mindkét típusú szöveg kibontása a jövőben történik, függetlenül attól, hogy mire van beállítva.

## <a name="skill-inputs"></a>Szakértelem bemenetei

| Bemenet neve      | Leírás                                          |
|---------------|------------------------------------------------------|
| image         | Összetett típus. Jelenleg csak akkor működik, "/document/normalized_images" mező, amelyet az Azure Blob indexelő, ha ```imageAction``` van beállítva, hogy ```none```a. További információt a [mintában](#sample-output) talál.|


## <a name="skill-outputs"></a>Szakértelem kimenetei
| Kimenet neve     | Leírás                   |
|---------------|-------------------------------|
| szöveg          | Egyszerű szöveg a képből kibontva.   |
| layoutText    | A kibontott szöveget és a szöveg helyét leíró összetett típus.|


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

## <a name="sample-text-and-layouttext-output"></a>Mintaszöveg és elrendezésSzöveg kimenet

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

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Minta: Beágyazott képekből kinyert szöveg egyesítése a dokumentum tartalmával.

A Szövegegyesülés gyakori használati esete a képek szöveges ábrázolásának (ocr-szakértelemből származó szöveg vagy kép felirata) egyesítése a dokumentum tartalommezőjébe.

A következő példa skillset létrehoz egy *merged_text* mezőben. Ez a mező a dokumentum szöveges tartalmát és a dokumentumba ágyazott képek mindegyikének OCRed szövegét tartalmazza.

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
A fenti skillset példa feltételezi, hogy egy normalizált-images mező létezik. A mező létrehozásához állítsa be az *imageAction* konfigurációt az indexelő definíciójában a *NormalizedImages generálására* az alábbiak szerint:

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
+ [Beépített képességek](cognitive-search-predefined-skills.md)
+ [TextMerger szakértelem](cognitive-search-skill-textmerger.md)
+ [Hogyan definiálni a skillset](cognitive-search-defining-skillset.md)
+ [Indexelő létrehozása (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
