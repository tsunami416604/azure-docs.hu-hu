---
title: Ismertető a rendszerképek – Computer Vision
titleSuffix: Azure Cognitive Services
description: A Computer Vision API, a lemezkép leírása szolgáltatáshoz kapcsolódó fogalmak.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 933c4e4a6731a492a5aa461de7c782af422fb6fb
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55162701"
---
# <a name="describe-images-with-human-readable-language"></a>Képek az emberek számára olvasható nyelv leírása

Számítógépes Látástechnológiai algoritmus elemezheti a tartalmat a képet. Ez az elemzés képezi a kép ember által olvasható, teljes mondatokban megfogalmazott „leírásának” alapját. A leírás összegzi mindazt, ami a képen látható. Számítógépes Látástechnológiai algoritmus alapuló a vizuális jellemzőket azonosítja a kép különböző leírások létrehozásához. Leírás ki lesz értékelve, és a egy megbízhatósági pontszám jönnek létre. A megbízhatósági pontszámok listája csökkenő sorrendbe rendezve érkezik vissza.

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
