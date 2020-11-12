---
title: Arcfelismerés – Computer Vision
titleSuffix: Azure Cognitive Services
description: A Computer Vision API Arcfelismerés szolgáltatásával kapcsolatos fogalmak megismerése.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 089c59c041110209e55cc3fb10b1c5adf54bd13e
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94538274"
---
# <a name="face-detection-with-computer-vision"></a>Arcfelismerés Computer Vision

A Computer Vision képes észlelni az emberi arcokat egy képen belül, és az összes észlelt arc esetében az életkor, a nemre és a téglalapot is létrehozza. 

> [!NOTE]
> Ezt a funkciót az Azure [Face](/azure/cognitive-services/face/) szolgáltatás is felkínálja. További részletekért tekintse meg ezt az alternatívát, többek között az arcfelismerés és az észlelést. 

## <a name="face-detection-examples"></a>Arcfelismerés – példák

Az alábbi példa azt mutatja be, Computer Vision által visszaadott JSON-választ egy emberi arcot tartalmazó képhez.

![Tetőn álló nő arcának vizuális elemzése](./Images/woman_roof_face.png)

```json
{
    "faces": [
        {
            "age": 23,
            "gender": "Female",
            "faceRectangle": {
                "top": 45,
                "left": 194,
                "width": 44,
                "height": 44
            }
        }
    ],
    "requestId": "8439ba87-de65-441b-a0f1-c85913157ecd",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Png"
    }
}
```

A következő példa azt mutatja be, hogyan tért vissza a JSON-válasz egy több emberi arcot tartalmazó képhez.

![Kép elemzése a családi fényképpel](./Images/family_photo_face.png)

```json
{
    "faces": [
        {
            "age": 11,
            "gender": "Male",
            "faceRectangle": {
                "top": 62,
                "left": 22,
                "width": 45,
                "height": 45
            }
        },
        {
            "age": 11,
            "gender": "Female",
            "faceRectangle": {
                "top": 127,
                "left": 240,
                "width": 42,
                "height": 42
            }
        },
        {
            "age": 37,
            "gender": "Female",
            "faceRectangle": {
                "top": 55,
                "left": 200,
                "width": 41,
                "height": 41
            }
        },
        {
            "age": 41,
            "gender": "Male",
            "faceRectangle": {
                "top": 45,
                "left": 103,
                "width": 39,
                "height": 39
            }
        }
    ],
    "requestId": "3a383cbe-1a05-4104-9ce7-1b5cf352b239",
    "metadata": {
        "height": 230,
        "width": 300,
        "format": "Png"
    }
}
```

## <a name="use-the-api"></a>Az API használata

Az Arcfelismerés funkció a [rendszerkép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) API részét képezi. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja. Belefoglalás `Faces` a **visualFeatures** lekérdezési paraméterbe. Ezután, amikor megkapja a teljes JSON-választ, egyszerűen elemezze a szakasz tartalmának karakterláncát `"faces"` .

* [Gyors útmutató: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Gyors útmutató: rendszerkép elemzése (REST API)](./quickstarts/csharp-analyze.md)
