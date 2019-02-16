---
title: Ismertető a rendszerképek – Computer Vision
titleSuffix: Azure Cognitive Services
description: A Computer Vision API, a lemezkép leírása szolgáltatáshoz kapcsolódó fogalmak.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 91618b211fdd869daf74491b175d6359ffa3f30c
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312191"
---
# <a name="describe-images-with-human-readable-language"></a>Képek az emberek számára olvasható nyelv leírása

Számítógépes Látástechnológia kép elemezheti és hozzon létre egy emberek számára olvasható mondatot, amely leírja annak tartalmát. Az algoritmus ténylegesen retruns több leírások alapján különböző vizuális jellemzőinek és a leírás egy magabiztossági pontszámot kap. A végső kimenetet az leírások legmagasabbtól a legalacsonyabb megbízhatósági rendezett listája.

## <a name="image-description-example"></a>Kép leírását bemutató példa

A következő JSON-választ mutatja be, milyen számítógépes Látástechnológiai ad vissza, ha a alapuló a vizuális jellemzőket példaképen leíró.

![Épületek Manhattan a kép fekete-fehér](./Images/bw_buildings.png)

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

Fogalmak ismertetése [rendszerképek címkézése](concept-tagging-images.md) és [lemezképek kategorizálásához](concept-categorizing-images.md).
