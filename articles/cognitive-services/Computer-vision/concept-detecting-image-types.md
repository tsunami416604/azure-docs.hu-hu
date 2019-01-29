---
title: Képtípus – Computer Vision észlelése
titleSuffix: Azure Cognitive Services
description: A kép típusú észlelési funkciója a Computer Vision API kapcsolatos fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: da7cce0cd69823f254cd6b666dc468f155b9e097
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55156275"
---
# <a name="detecting-image-types-with-computer-vision"></a>A Computer Vision képtípussal észlelése

Számítógépes Látástechnológia elemezheti kép tartalomtípusa jelzi, hogy-e a kép ClipArt, a méretezési csoport vagy vonalrajz valószínűsége minősítése.

## <a name="detecting-clip-art"></a>ClipArt észlelése

Számítógépes Látástechnológia kép elemzi, és értékeli annak lehetőségét, hogy a kép ClipArt a méretezési csoport 0 – 3, a folyamatban, az alábbi táblázatban leírtak szerint.

| Value | Jelentés |
|-------|---------|
| 0 | Nem ClipArt |
| 1 | Nem egyértelmű |
| 2 | Normál ClipArt |
| 3 | Jó ClipArt |

### <a name="clip-art-detection-examples"></a>Grafikus képek észlelési példák

A következő JSON-válaszok mutatja be, milyen számítógépes Látástechnológiai ad vissza, ha a példában képek ClipArt folyamatban a valószínűsége minősítése.

![A szelet sajtok egy videoklip lejátszása art-kép](./Images/cheese_clipart.png)

```json
{
    "imageType": {
        "clipArtType": 3,
        "lineDrawingType": 0
    },
    "requestId": "88c48d8c-80f3-449f-878f-6947f3b35a27",
    "metadata": {
        "height": 225,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Egy kék ház és az első yard](./Images/house_yard.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "a9c8490a-2740-4e04-923b-e8f4830d0e47",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="detecting-line-drawings"></a>Sor rajzok észlelése

Számítógépes Látástechnológia kép elemzi, és jelzi, hogy-e a lemezkép Vonalrajz egy logikai értéket ad vissza.

### <a name="line-drawing-detection-examples"></a>Vonalrajz észlelési példák

A következő JSON-válaszok mutatja be, milyen számítógépes Látástechnológiai ad vissza, ha jelzi, hogy-e a példában képek sor rajzok.

![Egy olyan lion Vonalrajz képe](./Images/lion_drawing.png)

```json
{
    "imageType": {
        "clipArtType": 2,
        "lineDrawingType": 1
    },
    "requestId": "6442dc22-476a-41c4-aa3d-9ceb15172f01",
    "metadata": {
        "height": 268,
        "width": 300,
        "format": "Jpeg"
    }
}
```

![Egy zöld hátterű fehér flower](./Images/flower.png)

```json
{
    "imageType": {
        "clipArtType": 0,
        "lineDrawingType": 0
    },
    "requestId": "98437d65-1b05-4ab7-b439-7098b5dfdcbf",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>További lépések

Fogalmak ismertetése [rendszerképek címkézése](concept-tagging-images.md) és [lemezképek kategorizálásához](concept-categorizing-images.md).
