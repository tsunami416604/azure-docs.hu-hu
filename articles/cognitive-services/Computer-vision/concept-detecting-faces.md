---
title: Arcok – Computer Vision észlelése
titleSuffix: Azure Cognitive Services
description: A face észlelési funkciója a Computer Vision API kapcsolatos fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: c4ff513a337c9bba6d75aa0fbca4540faaac1035
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55160662"
---
# <a name="face-detection-with-computer-vision"></a>Arcfelismerés a Computer Vision

Számítógépes Látástechnológia a képen látható emberi arcok észlelése, és az életkor, nemek és minden egyes észlelt face négyszöge generál. A Computer Vision a [Face](/azure/cognitive-services/face/) funkcióinak csak egy részét tartalmazza. A Face szolgáltatást részletesebb elemzések elvégzésére is használhatja, például az arc vagy a testtartás azonosítására.  

## <a name="face-detection-examples"></a>Arcok észlelése példák

Az első példa bemutatja egy egyetlen emberi arcok tartalmazó rendszerképek Computer Vision által visszaadott JSON-választ.

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

A második példa bemutatja a JSON-választ adott vissza képet tartalmazó több emberi arcok azonosítására.

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

Tudnivalók a fogalmak [tartomány-specifikus tartalom észlelése](concept-detecting-domain-content.md).
