---
title: Arcfelismerés - Computer Vision
titleSuffix: Azure Cognitive Services
description: Ismerje meg a Computer Vision API arcfelismerő funkciójával kapcsolatos fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d10e4c98d3e2f00e7139063c8263e65ffd015d7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244701"
---
# <a name="face-detection-with-computer-vision"></a>Arcfelismerés a Computer Vision segítségével

Computer Vision képes felismerni az emberi arcok egy képen belül, és létrehoz a kor, nem, és téglalap minden észlelt arc. 

> [!NOTE]
> Ezt a funkciót az Azure [Face](/azure/cognitive-services/face/) szolgáltatás is kínálja. Lásd ezt az alternatívát a részletesebb arcelemzésért, beleértve az arcazonosítást és a pózészlelést. 

## <a name="face-detection-examples"></a>Példák arcfelismerésre

A következő példa bemutatja a Computer Vision által adott JSON-választ egyetlen emberi arcot tartalmazó képhez.

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

A következő példa bemutatja a JSON-választ adott egy több emberi arcokat tartalmazó kép.

![Vision Analyze családi fotó arc](./Images/family_photo_face.png)

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

Az arcfelismerési szolgáltatás része a [Kép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja meg. Szerepeljen `Faces` a **visualFeatures** lekérdezési paraméterben. Ezután, amikor megkapja a teljes JSON-választ, egyszerűen elemezje `"faces"` a szakasz tartalmának karakterláncát.

* [Rövid útmutató: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Rövid útmutató: Lemezkép elemzése (REST API)](./quickstarts/csharp-analyze.md)
