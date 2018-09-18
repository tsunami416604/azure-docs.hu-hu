---
title: Képtípus – Computer Vision észlelése
titleSuffix: Azure Cognitive Services
description: A Computer Vision API használatával képtípussal észlelése kapcsolatos fogalmakat.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 1a76106caed514f56e897203fba5215b7e93d4ff
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984826"
---
# <a name="detecting-image-types"></a>Képek típusának észlelése

Számítógépes Látástechnológia elemezheti kép tartalomtípusa jelzi, hogy-e a kép ClipArt, a méretezési csoport vagy vonalrajz valószínűsége minősítése.

## <a name="detecting-clip-art"></a>ClipArt észlelése

Számítógépes Látástechnológia kép elemzi, és értékeli annak lehetőségét, hogy a kép ClipArt a méretezési csoport 0 – 3, a folyamatban, az alábbi táblázatban leírtak szerint.

| Érték | Jelentés |
|-------|---------|
| 0 | A nem ClipArt |
| 1 | Nem egyértelmű |
| 2 | Normál ClipArt |
| 3 | Jó ClipArt |

### <a name="clip-art-detection-examples"></a>Grafikus képek észlelési példák

A következő JSON-válaszok mutatja be, milyen számítógépes Látástechnológiai ad vissza, ha a példában képek ClipArt folyamatban a valószínűsége minősítése.

![Vizuális sajtok iránti Szenvedélyének ClipArt elemzése](./Images/cheese_clipart.png)

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

![Vizuális ház Yard elemzése](./Images/house_yard.png)

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

![Vizuális Lion rajz elemzése](./Images/lion_drawing.png)

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

![Vizuális Flower elemzése](./Images/flower.png)

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