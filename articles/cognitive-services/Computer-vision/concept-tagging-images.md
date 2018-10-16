---
title: Címkézés rendszerképek – Computer Vision
titleSuffix: Azure Cognitive Services
description: Címkézés képek a Computer Vision API használatával kapcsolatos fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 0025cdcfaa64a262a5ca54ab4db5a84f6a5768ba
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338913"
---
# <a name="tagging-images"></a>Képek címkézése

Számítógépes Látástechnológia alapján ad vissza címkéket több mint 2000 felismerhető objektumok, tárgy, táj és műveleteket. Ha a címkék olyan nem egyértelmű vagy nem közismert, az API-válasz biztosít "tippek" ismert beállítását a környezetben a címke jelentésének értelmezéséhez. Címkék nem egy besorolás szerint vannak rendezve, és nincs öröklési hierarchiát létezik. Tartalom címkék gyűjteménye a lemezkép teljes mondatokban formázott emberi olvasható nyelveként megjelenik a "leírás" alapját képezi. Vegye figyelembe, hogy ezen a ponton angol Képleírás az egyetlen támogatott nyelv.

Kép feltöltése vagy a kép URL-címének megadása után a Látástechnológiai algoritmus kimeneti címkék az objektumok, a tárgy és a műveletek azonosítja a kép alapján. Címkézés nem korlátozódik a fő tulajdonos, például az előtérben, a személy, de is tartalmaz, a beállítás (belső vagy külső) bútordarab, eszközök, üzemek, például állatokat, Kellékek, kütyük stb.

## <a name="image-tagging-example"></a>Kép címkézési példa

A következő JSON-választ mutatja be, milyen számítógépes Látástechnológiai ad vissza, ha vizuális jellemzőket észlelhető a példaképen a címkézést.

![House_Yard](./Images/house_yard.png).

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

Fogalmak ismertetése [lemezképek kategorizálásához](concept-categorizing-images.md) és [lemezképek leíró](concept-describing-images.md).