---
title: Saját arculat észlelése – Computer Vision
titleSuffix: Azure Cognitive Services
description: A Computer Vision API használatával márka/emblémájának észlelési kapcsolatos fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/26/2019
ms.author: pafarley
ms.openlocfilehash: 7bfda764c418258a7bc555697530214240a5f0d3
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734319"
---
# <a name="brand-detection"></a>Márka észlelése

Márka észlelés a speciális mód [észlelési objektum](concept-object-detection.md) , amely a globális emblémák százezer adatbázis vagy képeken szereplő kereskedelmi márkái azonosítására használ. Használhatja a szolgáltatás, például felderítheti, mely márkái: a közösségi oldalakon legnépszerűbb vagy a media termékelhelyezésről legelterjedtebb.

A Computer Vision szolgáltatás észleli, hogy vannak-e márka emblémát a megadott lemezkép; Ha igen, a márkanév, egy megbízhatósági pontszám és az embléma körüli határolókeret koordinátái ad vissza.

A beépített embléma adatbázis consumer electronics, ruházat és más népszerű márkát ismerteti. Ha azt tapasztalja, hogy a keresett márkájú nem észleli a Computer Vision service, akkor előfordulhat, hogy jobb szolgáltatáshoz jutnak a létrehozása és betanítása saját embléma detector használatával használatával a [Custom Vision](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) szolgáltatás.

## <a name="brand-detection-example"></a>Példa a márka észlelése

A következő JSON-válaszok bemutatják, milyen számítógépes Látástechnológiai ad vissza, ha a példában képek márkái észlelése.

![A szürke sweatshirt Microsoft címke, valamint egy rajta embléma](./Images/gray-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.706,
         "rectangle":{
            "x":470,
            "y":862,
            "w":338,
            "h":327
         }
      }
   ],
   "requestId":"5fda6b40-3f60-4584-bf23-911a0042aa13",
   "metadata":{
      "width":2286,
      "height":1715,
      "format":"Jpeg"
   }
}
```
Bizonyos esetekben a márka detector használatával kiesik a emblémakép, mind a stilizált márkanév, két külön emblémát.

![A red shirt Microsoft címke, valamint egy rajta embléma](./Images/red-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.657,
         "rectangle":{
            "x":436,
            "y":473,
            "w":568,
            "h":267
         }
      },
      {
         "name":"Microsoft",
         "confidence":0.85,
         "rectangle":{
            "x":101,
            "y":561,
            "w":273,
            "h":263
         }
      }
   ],
   "requestId":"10dcd2d6-0cf6-4a5e-9733-dc2e4b08ac8d",
   "metadata":{
      "width":1286,
      "height":1715,
      "format":"Jpeg"
   }
}
```

## <a name="use-the-api"></a>Az API-val
A márka észlelési szolgáltatás részét képezi a [kép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API-t. Ez az API REST-hívások vagy a natív SDK keresztül hívása. A teljes JSON-választ kap, ha egyszerűen tartalmát a karakterláncot elemezni a `"brands"` szakaszban.

* [Rövid útmutató: Kép (.NET SDK-t) elemzése](./quickstarts-sdk/csharp-analyze-sdk.md)
* [Rövid útmutató: Kép (REST API) elemzése](./quickstarts/csharp-analyze.md)
