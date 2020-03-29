---
title: Objektumészlelés - Computer Vision
titleSuffix: Azure Cognitive Services
description: Ismerje meg a Computer Vision API objektumészlelési funkciójával kapcsolatos fogalmakat – használat és korlátok.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3957e15a09bd7e7ecd814d169451af3241108b64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131672"
---
# <a name="detect-common-objects-in-images"></a>Képek gyakori objektumainak észlelése

Az objektumészlelés hasonló a [címkézéshez,](concept-tagging-images.md)de az API a határolókeret koordinátáit adja vissza (képpontban) minden egyes talált objektumhoz. Ha például egy kép egy kutyát, macskát és személyt tartalmaz, a Észlelés művelet felsorolja ezeket az objektumokat a képen lévő koordinátákkal együtt. Ezzel a funkcióval feldolgozhatja a kép objektumai közötti kapcsolatokat. Azt is meghatározhatja, hogy egy képen több példány is szerepel-e ugyanabból a címkéből.

A Detect API a képen azonosított objektumok vagy élőlények alapján alkalmazza a címkéket. Jelenleg nincs hivatalos kapcsolat a címkézési rendszerszám és az objektumészlelési rendszerszám között. Fogalmi szinten a Detect API csak objektumokat és élőlényeket talál, míg a címke API tartalmazhat olyan környezetfüggő kifejezéseket is, mint a "beltéri", amely nem honosítható határolókeretekkel.

## <a name="object-detection-example"></a>Példa objektumészlelésre

A következő JSON-válasz bemutatja, hogy a Computer Vision milyen értéket ad vissza a példaképobjektumainak észlelésekor.

![Egy nő, aki Microsoft Surface-eszközt használ a konyhában](./Images/windows-kitchen.jpg)

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

## <a name="limitations"></a>Korlátozások

Fontos megjegyezni az objektumészlelés korlátait, hogy elkerülhesse vagy mérsékelhesse a hamis negatív (kihagyott objektumok) és a korlátozott részletesség hatásait.

* Az objektumokat általában nem észleli a rendszer, ha kicsik (a kép kevesebb mint 5%-a).
* Az objektumokat általában nem észleli a rendszer, ha szorosan egymáshoz vannak rendezve (például egy halom lemez).
* Az objektumokat nem különböztetik meg márka- vagy terméknevek (például különböző típusú üdítők egy raktárpolcon). A [márkaészlelési](concept-brand-detection.md) funkcióval azonban márkaadatokat kaphat egy képből.

## <a name="use-the-api"></a>Az API használata

Az objektumészlelési szolgáltatás a [Kép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API része. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja meg. Szerepeljen `Objects` a **visualFeatures** lekérdezési paraméterben. Ezután, amikor megkapja a teljes JSON-választ, egyszerűen elemezje `"objects"` a szakasz tartalmának karakterláncát.

* [Rövid útmutató: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Rövid útmutató: Lemezkép elemzése (REST API)](./quickstarts/csharp-analyze.md)