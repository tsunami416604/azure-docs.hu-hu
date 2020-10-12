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
ms.openlocfilehash: 887f6d9163f800ecbe8a5ab9a0715fd171f1d3d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "80244511"
---
# <a name="applying-content-tags-to-images"></a>Tartalomcímkék alkalmazása képekre

A Computer Vision több ezer felismerhető objektum, élő lény, díszlet és művelet alapján ad vissza címkéket. Amennyiben a címkék félreérthetőek vagy nem közismertek, az API válasza „tippeket” tartalmaz a címke adott környezetben való értelmezésének megkönnyítése érdekében. A címkék nincsenek rendszerezve, és nincs öröklési hierarchiájuk. A tartalomcímkék gyűjteménye képezi a kép ember által olvasható nyelven, teljes mondatokban megformált „leírásának” alapját. Fontos tudni, hogy a képleírásokhoz jelenleg csak az angol nyelv támogatott.

Egy kép feltöltése vagy URL-címének megadása után Computer Vision-algoritmusok adnak meg címkéket a képeken felismert tárgyak, élőlények és tevékenységek alapján. A címkézés nem korlátozódik a kép fő témájára, például az előtérben szereplő személyre, hanem magában foglalja a környezetet (beltér vagy kültér), bútorokat, eszközöket, növényeket, állatokat, kiegészítőket, készülékeket stb.

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

## <a name="use-the-api"></a>Az API használata

A címkézési funkció a [rendszerkép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API részét képezi. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja. Belefoglalás `Tags` a **visualFeatures** lekérdezési paraméterbe. Ezután, amikor megkapja a teljes JSON-választ, egyszerűen elemezze a szakasz tartalmának karakterláncát `"tags"` .

* [Gyors útmutató: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Gyors útmutató: rendszerkép elemzése (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>További lépések

Ismerkedjen meg a [képek kategorizálása](concept-categorizing-images.md) és a [képek leírásával](concept-describing-images.md)kapcsolatos fogalmakkal.
