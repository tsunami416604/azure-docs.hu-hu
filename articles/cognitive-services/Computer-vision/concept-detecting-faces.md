---
title: Arcok észlelése
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Computer Vision segítségével az Azure Cognitive Servicesben arcok észlelése kapcsolatos fogalmakat.
services: cognitive-services
author: deken
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: abf0dc0ea80685130f6566ae962d2bed124e0924
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44725407"
---
# <a name="detecting-faces"></a>Arcok észlelése

Számítógépes Látástechnológia a képen látható emberi arcok észlelése, és az életkor, nemek és minden egyes észlelt face négyszöge generál. A Computer Vision a [Face](/azure/cognitive-services/face/) funkcióinak csak egy részét tartalmazza. A Face szolgáltatást részletesebb elemzések elvégzésére is használhatja, például az arc vagy a testtartás azonosítására.  

## <a name="face-detection-examples"></a>Arcok észlelése példák

Az első példa bemutatja egy egyetlen emberi arcok tartalmazó rendszerképek Computer Vision által visszaadott JSON-választ.

![Vizuális Nőről tető Face elemzése](./Images/woman_roof_face.png)

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