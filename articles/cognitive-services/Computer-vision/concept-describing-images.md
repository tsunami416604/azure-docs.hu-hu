---
title: Képleírások – Computer Vision
titleSuffix: Azure Cognitive Services
description: A Computer Vision API rendszerkép-leírási funkciójával kapcsolatos fogalmak.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "80244715"
---
# <a name="describe-images-with-human-readable-language"></a>Képek leírása emberi olvasásra alkalmas nyelvvel

Computer Vision elemezheti a rendszerképet, és létrehozhat egy olyan, emberi olvasásra alkalmas mondatot, amely leírja annak tartalmát. Az algoritmus a különböző vizualizációs funkciók alapján több leírást ad vissza, és mindegyik Leírás megbízhatósági pontszámot kap. A végső kimenet a legmagasabbtól a legalacsonyabb megbízhatóságig rendezett leírások listája.

## <a name="image-description-example"></a>Példa a képek leírására

A következő JSON-válasz azt szemlélteti, hogy milyen Computer Vision ad vissza, amikor leírja a képet a vizualizáció funkciói alapján.

![A Manhattanben található épületek fekete-fehér képe](./Images/bw_buildings.png)

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

A rendszerkép leírása funkció a [képelemzési](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API részét képezi. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja. Belefoglalás `Description` a **visualFeatures** lekérdezési paraméterbe. Ezután, amikor megkapja a teljes JSON-választ, egyszerűen elemezze a szakasz tartalmának karakterláncát `"description"` .

* [Gyors útmutató: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Gyors útmutató: rendszerkép elemzése (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan [címkézheti a képeket](concept-tagging-images.md) és [kategorizálhatja a képeket](concept-categorizing-images.md).
