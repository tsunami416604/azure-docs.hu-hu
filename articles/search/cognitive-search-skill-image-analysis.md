---
title: Kép elemzés kognitív keresési szakértelem (Azure Search) |} Microsoft Docs
description: Bontsa ki a ImageAnalysis kognitív szakértelem használatával egy Azure Search dúsító folyamat kép elemzése révén szemantikai szöveg.
services: search
manager: pablocas
author: luiscabrer
documentationcenter: ''
ms.assetid: ''
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 271f3231078b8842b040509ccf0406ed2415e5ec
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
#   <a name="image-analysis-cognitive-skill"></a>Kép elemzés kognitív szakértelem

A **kép elemzés** szakértelem széles választéka vizuális funkciókat a lemezkép tartalom alapján bontja ki. Például hozzon létre egy felirat kép, címkék létrehozása, vagy celebrities és jellegzetes hely azonosítása.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>Szakértelem paraméterek

A paraméterei a kis-és nagybetűket.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| defaultLanguageCode   |  Egy karakterlánc, amely a nyelvi való visszatéréshez. A szolgáltatás felismerési eredmények a megadott nyelven adja vissza. Ha ez a paraméter nincs megadva, akkor az alapértelmezett érték: "hu". <br/><br/>Támogatott nyelvek a következők: <br/>*en* -angol (alapértelmezett) <br/> *zh* -egyszerűsített kínai|
|visualFeatures |   Karakterlánctömbnek jelző vizuális szolgáltatást való visszatéréshez. Érvényes visual szolgáltatás típusok a következők:  <ul><li> *kategóriák* -kép tartalmához egy meghatározott kognitív szolgáltatások besorolás szerint kategorizálja [dokumentáció](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).</li><li> *címkék* -címkét a kép és a lemezkép tartalommal kapcsolatos szavak részletes listáját.</li><li>*Leírás* -tartalom teljes angol mondatot kép ismerteti.</li><li>*Lapok* -észleli, ha a lapok találhatók. Ha van ilyen, állít elő koordinátáit, nem és életkor.</li><li> *ImageType* -azt észleli, ha a lemezkép clipart vagy sor rajz.</li><li>   *Szín* -meghatározza az ékezet színét, meghatározó színét, és azt a képfájl fekete & fehér.</li><li>*Felnőtt* -azt észleli, ha a kép pornográf jellegű (ábrázol meztelenség vagy egy nemét act). Ivarilag kétértelmű tartalmat is észleli.</li></ul> Vizuális funkciókat nevei-és nagybetűk.|
| részletek   | Mely tartományspecifikus vissza adatokat karakterláncokból álló tömb. Érvényes visual szolgáltatás típusok a következők: <ul><li>*Celebrities* -celebrities azonosítja, ha a kép észlelte.</li><li>*Jellegzetes hely* -jellegzetes hely azonosítja, ha a kép észlelte.</li></ul>
 |

## <a name="skill-inputs"></a>Szakértelem bemenetek

| Bemeneti név      | Leírás                                          |
|---------------|------------------------------------------------------|
| Kép         | Összetett típus. Az Azure Blob indexelő által előállított jelenleg csak akkor működik a "/ dokumentum/normalized_images" mező, amikor ```imageAction``` értéke ```generateNormalizedImages```. Tekintse meg a [minta](#sample-output) további információt.|



##  <a name="sample-definition"></a>A minta meghatározása

```json
{
    "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
    "visualFeatures": [
        "Tags",
        "Faces",
        "Categories",
        "Adult",
        "Description",
        "ImageType",
        "Color"
    ],
    "defaultLanguageCode": "en",
    "inputs": [
        {
            "name": "image",
            "source": "/document/normalized_images/*"
        }
    ],
    "outputs": [
        {
            "name": "categories",
            "targetName": "myCategories"
        },
        {
            "name": "tags",
            "targetName": "myTags"
        },
        {
            "name": "description",
            "targetName": "myDescription"
        },
        {
            "name": "faces",
            "targetName": "myFaces"
        },
        {
            "name": "imageType",
            "targetName": "myImageType"
        },
        {
            "name": "color",
            "targetName": "myColor"
        },
        {
            "name": "adult",
            "targetName": "myAdultCategory"
        }
    ]
}
```

##  <a name="sample-input"></a>A minta bemenet

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "url": "https://storagesample.blob.core.windows.net/sample-container/image.jpg"
            }
        }
    ]
}
```


##  <a name="sample-output"></a>Példa kimenet

```json
{
    "values": [
      {
        "recordId": "1",
            "data": {
                "categories": [
           {
                        "name": "abstract_",
                        "score": 0.00390625
                    },
                    {
                "name": "people_",
                        "score": 0.83984375,
                "detail": {
                            "celebrities": [
                                {
                                    "name": "Satya Nadella",
                                    "faceRectangle": {
                                        "left": 597,
                                        "top": 162,
                                        "width": 248,
                                        "height": 248
                                    },
                                    "confidence": 0.999028444
                                }
                            ],
                            "landmarks": [
                                {
                                    "name": "Forbidden City",
                                    "confidence": 0.9978346
                                }
                            ]
                        }
                    }
                ],
                "adult": {
                    "isAdultContent": false,
                    "isRacyContent": false,
                    "adultScore": 0.0934349000453949,
                    "racyScore": 0.068613491952419281
                },
                "tags": [
                    {
                        "name": "person",
                        "confidence": 0.98979085683822632
                    },
                    {
                        "name": "man",
                        "confidence": 0.94493889808654785
                    },
                    {
                        "name": "outdoor",
                        "confidence": 0.938492476940155
                    },
                    {
                        "name": "window",
                        "confidence": 0.89513939619064331
                    }
                ],
                "description": {
                    "tags": [
                        "person",
                        "man",
                        "outdoor",
                        "window",
                        "glasses"
                    ],
                    "captions": [
                        {
                            "text": "Satya Nadella sitting on a bench",
                            "confidence": 0.48293603002174407
                        }
                    ]
                },
                "requestId": "0dbec5ad-a3d3-4f7e-96b4-dfd57efe967d",
                "metadata": {
                    "width": 1500,
                    "height": 1000,
                    "format": "Jpeg"
                },
                "faces": [
                    {
                        "age": 44,
                        "gender": "Male",
                    "faceRectangle": {
                            "left": 593,
                            "top": 160,
                            "width": 250,
                            "height": 250
                        }
                    }
                ],
                "color": {
                    "dominantColorForeground": "Brown",
                    "dominantColorBackground": "Brown",
                    "dominantColors": [
                        "Brown",
                        "Black"
                    ],
                    "accentColor": "873B59",
                    "isBWImg": false
                    },
                "imageType": {
                    "clipArtType": 0,
                    "lineDrawingType": 0
                }
           }
      }
    ]
}
```


## <a name="error-cases"></a>Hibák esetén
Hiba a következő esetekben nem elemek ki kell olvasni.

| Hibakód | Leírás |
|------------|-------------|
| NotSupportedLanguage | A megadott nyelv nem támogatott. |
| InvalidImageUrl | Kép URL-címe a program rosszul formázott vagy nem érhető el.|
| InvalidImageFormat | Bemeneti adata nem egy érvényes lemezkép. |
| InvalidImageSize | Bemeneti kép mérete túl nagy. |
| NotSupportedVisualFeature  | A szolgáltatás megadott típus érvénytelen. |
| NotSupportedImage | Nem támogatott kép, például gyermekpornográfia. |
| InvalidDetails | Tartományspecifikus modell nem támogatott. |

## <a name="see-also"></a>Lásd még

+ [Előre definiált képességek](cognitive-search-predefined-skills.md)
+ [Egy skillset definiálása](cognitive-search-defining-skillset.md)
+ [Az indexelő (REST) létrehozása](ref-create-indexer.md)
