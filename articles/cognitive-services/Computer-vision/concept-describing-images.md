---
title: Képleírások - Computer Vision
titleSuffix: Azure Cognitive Services
description: A Computer Vision API képleíró funkciójával kapcsolatos fogalmak.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7bf95a2b49608ef1f031a3b443db92b42cdae624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244715"
---
# <a name="describe-images-with-human-readable-language"></a>Képek leírása ember által olvasható nyelvvel

Computer Vision képes elemezni egy képet, és létrehoz egy ember által olvasható mondat, amely leírja annak tartalmát. Az algoritmus valójában több leírást ad vissza különböző vizuális funkciók alapján, és minden leírás megbízhatósági pontszámot kap. A végső kimenet a legmagasabbtól a legalacsonyabb megbízhatóságig rendezett leírások listája.

## <a name="image-description-example"></a>Példa a kép leírására

A következő JSON-válasz bemutatja, hogy a Computer Vision mit ad vissza a példakép vizuális jellemzői alapján történő leírásakor.

![Egy fekete-fehér képet épületek Manhattan](./Images/bw_buildings.png)

```json
{
    "description": {
        "tags": ["outdoor", "building", "photo", "city", "white", "black", "large", "sitting", "old", "water", "skyscraper", "many", "boat", "river", "group", "street", "people", "field", "tall", "bird", "standing"],
        "captions": [
            {
                "text": "a black and white photo of a city",
                "confidence": 0.95301952483304808
            },
            {
                "text": "a black and white photo of a large city",
                "confidence": 0.94085190563213816
            },
            {
                "text": "a large white building in a city",
                "confidence": 0.93108362931954824
            }
        ]
    },
    "requestId": "b20bfc83-fb25-4b8d-a3f8-b2a1f084b159",
    "metadata": {
        "height": 300,
        "width": 239,
        "format": "Jpeg"
    }
}
```

## <a name="use-the-api"></a>Az API használata

A képleíró szolgáltatás a [Kép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API része. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja meg. Szerepeljen `Description` a **visualFeatures** lekérdezési paraméterben. Ezután, amikor megkapja a teljes JSON-választ, egyszerűen elemezje `"description"` a szakasz tartalmának karakterláncát.

* [Rövid útmutató: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Rövid útmutató: Lemezkép elemzése (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>További lépések

Ismerje meg a [képek címkézésének](concept-tagging-images.md) és [kategorizálásának](concept-categorizing-images.md)kapcsolódó fogalmait.
