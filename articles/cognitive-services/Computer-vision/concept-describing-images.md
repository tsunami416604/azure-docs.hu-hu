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
ms.openlocfilehash: dcf61c642a9f8ad7aa68d72736ce8fdb0e9a6e3c
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945266"
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

## <a name="next-steps"></a>További lépések

A [képek címkézésével](concept-tagging-images.md) és a [képek kategorizálásával](concept-categorizing-images.md)kapcsolatos fogalmak megismerése.
