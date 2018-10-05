---
title: Kép elemzése a kognitív keresés szakértelem (Azure Search) |} A Microsoft Docs
description: Képelemzés ImageAnalysis cognitive szakértelem használata az Azure Search-felderítési bővítést folyamatban keresztül szemantikai szöveg kinyerése.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: luisca
ms.openlocfilehash: 6c59a7744e4630a04793b0c0e4d790a2e9cbe0f1
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48803443"
---
#   <a name="image-analysis-cognitive-skill"></a>Kép elemzése cognitive szakértelem

A **képelemzés** szakértelem kinyeri a sokféle kép tartalma alapján vizuális jellemzőket. Például hozzon létre egy felirat kép, címkék létrehozása, vagy hírességek és tereptárgyak felismerése azonosításához.

> [!NOTE]
> A kognitív keresés nyilvános előzetes verzióban érhető el. Képességcsoport végrehajtási, és a lemezkép kinyerése és a normalizálási jelenleg rendelkezésre állnak az ingyenes. Később az ezen funkciók díjszabásáról jelentjük be. 

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Vision.ImageAnalysisSkill 

## <a name="skill-parameters"></a>Ismeretek paraméterek

A paraméterei a kis-és nagybetűket.

| Paraméter neve     | Leírás |
|--------------------|-------------|
| defaultLanguageCode   |  A visszaadandó nyelv jelző karakterlánc. A szolgáltatás megadott nyelvre a felismerési eredményeket adja vissza. Ha ez a paraméter nincs megadva, alapértelmezett értéke "en". <br/><br/>Támogatott nyelvek a következők: <br/>*en* – angol (alapértelmezett) <br/> *zh* – egyszerűsített kínai|
|visualFeatures |   Jelző vizuális szolgáltatást való visszatéréshez karakterláncok tömbje. Érvényes visual funkció típusok a következők:  <ul><li> *kategóriák* -kép tartalmához a besorolás, a Cognitive Services meghatározott szerint kategorizálja [dokumentáció](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).</li><li> *a címkék* -megjelöli a lemezképet a szavakat a képen tartalommal kapcsolatos részletes listáját.</li><li>*Leírás* – ismerteti a tartalmat a teljes angol nyelvű mondatot kép.</li><li>*Arcok* -észleli, hogy megtalálhatók-e a arcokat. Ha jelen van, akkor állít elő, koordináták, nem és életkor.</li><li> *ImageType* -azt észleli, ha a kép ClipArt típusú vagy vonalrajz.</li><li>   *Szín* – a gombszöveg színét, színig, határozza meg, és hogy egy kép fekete-fehér.</li><li>*Felnőtt* -azt észleli, ha a kép pornográf jellegű (ábrázolja meztelenség vagy egy nemek act). Nyíltan kétértelmű tartalmat is észlelhető.</li></ul> Vizuális jellemzőket nevei és nagybetűk.|
| részletek   | Egy karakterlánctömb, jelezve, hogy mely tartomány-specifikus részletek való visszatéréshez. Érvényes visual funkció típusok a következők: <ul><li>*Hírességek* – hírességek azonosítja a kép észlelésekor.</li><li>*Arcrész* -arcrész azonosítja a kép észlelésekor.</li></ul>
 |

## <a name="skill-inputs"></a>Ismeretek bemenetek

| Bemeneti név      | Leírás                                          |
|---------------|------------------------------------------------------|
| image         | Komplex típus. Az Azure Blob indexelőjével által előállított jelenleg csak akkor működik a "/ dokumentum/normalized_images" mezőt, amikor ```imageAction``` értékre van állítva ```generateNormalizedImages```. Tekintse meg a [minta](#sample-output) további információt.|



##  <a name="sample-definition"></a>Minta-definíció
```json
{
    "@odata.type": "#Microsoft.Skills.Vision.ImageAnalysisSkill",
    "context": "/document/normalized_images/*",
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

##  <a name="sample-input"></a>Minta beviteli

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
                                    "faceBoundingBox": {
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
                    "faceBoundingBox": {
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
                    "isBwImg": false
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
Hiba történt a következő esetekben nincsenek elemek ki kell olvasni.

| Hibakód | Leírás |
|------------|-------------|
| NotSupportedLanguage | A megadott nyelv nem támogatott. |
| InvalidImageUrl | Kép URL-címe, a program rosszul formázott vagy nem érhető el.|
| InvalidImageFormat | A bemeneti adatok nem egy érvényes rendszerképet. |
| InvalidImageSize | Bemeneti kép mérete túl nagy. |
| NotSupportedVisualFeature  | A szolgáltatás megadott típus nem érvényes. |
| NotSupportedImage | Nem támogatott kép, például gyermekpornográfia. |
| InvalidDetails | Tartomány-specifikus modell nem támogatott. |

## <a name="see-also"></a>Lásd még

+ [Előre megadott képesség](cognitive-search-predefined-skills.md)
+ [Hogyan képességcsoport megadása](cognitive-search-defining-skillset.md)
+ [Az indexelő (REST) létrehozása](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
