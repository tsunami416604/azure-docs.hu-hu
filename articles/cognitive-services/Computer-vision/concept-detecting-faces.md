---
title: Arcok – Computer Vision észlelése
titleSuffix: Azure Cognitive Services
description: Ismerje meg, a face észlelési funkciója a Computer Vision API kapcsolatos fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 699192aba87bb009d7dbddddcc9579883bb71db9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60368135"
---
# <a name="face-detection-with-computer-vision"></a>Arcfelismerés a Computer Vision

Számítógépes Látástechnológiai is lemezképen belüli emberi arcok észlelése és létrehozása az életkor, nemek és minden egyes észlelt face négyszöge. 

> [!NOTE]
> Ez a szolgáltatás emellett az Azure által nyújtott [Face](/azure/cognitive-services/face/) szolgáltatás. Lásd: Ez a megoldás részletes elemzés, beleértve az arcfelismerés arc és észlelési jelentenek. 

## <a name="face-detection-examples"></a>Arcok észlelése példák

A következő példa bemutatja egy egyetlen emberi arcok tartalmazó rendszerképek Computer Vision által visszaadott JSON-választ.

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

A következő példa bemutatja a JSON-választ adott vissza képet tartalmazó több emberi arcok azonosítására.

![Vizuális családi fénykép Face elemzése](./Images/family_photo_face.png)

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

## <a name="next-steps"></a>További lépések

Tekintse meg a [kép elemzése](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) referenciadokumentációt további információ a face észlelési szolgáltatás használata.
