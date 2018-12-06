---
title: Objektum észlelése – Computer Vision
titleSuffix: Azure Cognitive Services
description: A Computer Vision API használatával objektumfelismerés kapcsolatos fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: pafarley
ms.openlocfilehash: 7f0f86e783edb55bc3193df073c92c9523a90176
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976033"
---
# <a name="object-detection"></a>Objektumészlelés

Objektumfelismerés hasonlít a [címkézés](concept-tagging-images.md), azonban az API-t adja vissza a határolókeret koordinátái (képpontban) minden egyes objektum található. Például képet tartalmaz egy kutya, cat és személy, ha a hibakeresés művelet felsorolja azokat az objektumokat a a koordináták a képen. Ez a funkció használatával feldolgozni a képet az objektumok közötti kapcsolatok. Azt is lehetővé teszi meghatározhatja, hogy vannak-e a képet ugyanazt a címkét több példányát.

A észlelése API objektumok vagy élő dolgot azonosítani a képen alapuló címkék vonatkozik. Vegye figyelembe, hogy ezen a ponton, a besorolás, címkézés használt és a besorolás, használja az objektum észlelésére között nincs hivatalos kapcsolat. Elméleti szinten a észlelése API csak megkeresi objektumok és élő dolog, amíg a címke API is használható például a "belső" környezeti feltételek, amelyeket nem a határoló be a honosított.

## <a name="object-detection-example"></a>Objektum észlelési példa

A következő JSON-választ mutatja be, milyen számítógépes Látástechnológiai ad vissza, ha az a példában a képen objektumok észlelése.

![Egy nő egy konyha a Microsoft Surface-eszközök használata](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="next-steps"></a>További lépések

Fogalmak ismertetése [lemezképek kategorizálásához](concept-categorizing-images.md) és [lemezképek leíró](concept-describing-images.md).