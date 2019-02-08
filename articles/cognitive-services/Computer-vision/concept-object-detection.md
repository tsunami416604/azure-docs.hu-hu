---
title: Objektum észlelése – Computer Vision
titleSuffix: Azure Cognitive Services
description: A Computer Vision API használatával objektumfelismerés kapcsolatos fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ee85e6bd171fc9415e5c7606d6e18a7a22fa6570
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55866916"
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

## <a name="limitations"></a>Korlátozások

Fontos megjegyzés: az észlelési szolgáltatás korlátozásai, így elkerülheti, vagy téves negatív (kihagyott objektumok) és a korlátozott részletes enyhítésén.
* Objektumok általában rendszer nem észleli, ha azok nagyon kicsi (kevesebb mint 5 %-a képen).
* Objektumok észlelése általában nem, ha azok szorosan együtt vannak elrendezve (például a lemezek stack).
* Objektumok nem különbözteti meg a márkáról vagy termék neve (szénsavas store tároló, például a különböző típusú). Azonban információkat szerezhet a márka lemezkép használatával a [saját arculat észlelési](concept-brand-detection.md) funkció.

## <a name="use-the-api"></a>Az API-val
Az észlelési szolgáltatás részét képezi a [kép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API-t. Ez az API REST-hívások vagy a natív SDK keresztül hívása. A teljes JSON-választ kap, ha egyszerűen tartalmát a karakterláncot elemezni a `"objects"` szakaszban.

* [Rövid útmutató: Kép (.NET SDK-t) elemzése](./quickstarts-sdk/csharp-analyze-sdk.md)
* [Rövid útmutató: Kép (REST API) elemzése](./quickstarts/csharp-analyze.md)