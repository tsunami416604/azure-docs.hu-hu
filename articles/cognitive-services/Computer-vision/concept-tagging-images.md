---
title: Tartalom címkék – Computer Vision
titleSuffix: Azure Cognitive Services
description: A Computer Vision API lemezképek címkézési funkciójával kapcsolatos fogalmak megismerése.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ad01751f7f7c573352c8fd15cc0f8cc2ebbbe700
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945111"
---
# <a name="applying-content-tags-to-images"></a>Tartalomcímkék alkalmazása képekre

A Computer Vision több ezer felismerhető objektum, élő lény, díszlet és művelet alapján ad vissza címkéket. Amennyiben a címkék félreérthetőek vagy nem közismertek, az API válasza „tippeket” tartalmaz a címke adott környezetben való értelmezésének megkönnyítése érdekében. A címkékhez nincs besorolási rendszer és öröklési hierarchia. A tartalomcímkék gyűjteménye képezi a kép ember által olvasható nyelven, teljes mondatokban megformált „leírásának” alapját. Fontos tudni, hogy a képleírásokhoz jelenleg csak az angol nyelv támogatott.

Egy rendszerkép feltöltését vagy egy képurl-cím megadását követően Computer Vision algoritmusok kimeneti címkéket a képen azonosított objektumok, élő lények és műveletek alapján. A címkézés nem korlátozódik a kép fő témájára, például az előtérben szereplő személyre, hanem magában foglalja a környezetet (beltér vagy kültér), bútorokat, eszközöket, növényeket, állatokat, kiegészítőket, készülékeket stb.

## <a name="image-tagging-example"></a>Példa a képek címkézésére

A következő JSON-válasz azt szemlélteti, hogy milyen Computer Vision ad vissza, amikor az ábrán látható vizualizációs funkciókat címkézi.

![Egy kék ház és az első udvar](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>További lépések

A [képek kategorizálásával](concept-categorizing-images.md) és a [képek leírásával](concept-describing-images.md)kapcsolatos fogalmak megismerése.
