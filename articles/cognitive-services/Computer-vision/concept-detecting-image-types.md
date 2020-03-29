---
title: Képtípus-észlelés - Computer Vision
titleSuffix: Azure Cognitive Services
description: A Computer Vision API képtípus-észlelési funkciójával kapcsolatos fogalmak.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 4e6c2db5333962d7ae43534998ffc1c48b0dba45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244562"
---
# <a name="detecting-image-types-with-computer-vision"></a>Képtípusok észlelése a Computer Vision segítségével

A [Kép elemzése](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API-val a Computer Vision elemezheti a képek tartalomtípusát, jelezve, hogy egy kép ClipArt-elem vagy vonalrajz.

## <a name="detecting-clip-art"></a>ClipArt-elemek észlelése

A Computer Vision elemzi a képet, és 0-tól 3-ig terjedő skálán értékeli annak valószínűségét, hogy a kép ClipArt-elem, az alábbi táblázatban leírtak szerint.

| Érték | Jelentés |
|-------|---------|
| 0 | Nem ClipArt |
| 1 | Kétértelmű |
| 2 | Normál clip-art elemek |
| 3 | Jó clip-art |

### <a name="clip-art-detection-examples"></a>Példák ClipArt-felismerésre

A következő JSON-válaszok azt mutatják be, hogy a Computer Vision milyen értéket ad vissza, amikor a példaképek clipart-elemnek adják ki a valószínűségét.

![Egy szelet sajt ClipArt-képe](./Images/cheese_clipart.png)

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

![Egy kék ház és az előkert](./Images/house_yard.png)

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

## <a name="detecting-line-drawings"></a>Vonalrajzok észlelése

A Computer Vision elemzi a képet, és logikai értéket ad vissza, amely jelzi, hogy a kép vonalrajz-e.

### <a name="line-drawing-detection-examples"></a>Példák vonalrajz-észlelési példák

A következő JSON-válaszok azt mutatják be, hogy mit ad vissza a Computer Vision, amikor jelzi, hogy a példaképek vonalrajzok-e.

![Egy oroszlán vonalrajzi képe](./Images/lion_drawing.png)

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

![Fehér virág zöld háttérrel](./Images/flower.png)

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

## <a name="use-the-api"></a>Az API használata

A képtípus-észlelési szolgáltatás a [Kép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API része. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja meg. Szerepeljen `ImageType` a **visualFeatures** lekérdezési paraméterben. Ezután, amikor megkapja a teljes JSON-választ, egyszerűen elemezje `"imageType"` a szakasz tartalmának karakterláncát.

* [Rövid útmutató: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Rövid útmutató: Lemezkép elemzése (REST API)](./quickstarts/csharp-analyze.md)
