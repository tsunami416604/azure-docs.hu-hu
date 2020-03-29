---
title: Márkafelismerés - Computer Vision
titleSuffix: Azure Cognitive Services
description: Ez a cikk az objektumészlelés speciális módját ismerteti; márka- és/vagy embléma-észlelést a Computer Vision API segítségével.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 50e4fe1e2573c8566bbdf5697bb81b025a00935c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131738"
---
# <a name="detect-popular-brands-in-images"></a>Népszerű márkák észlelése képeken

A márkaészlelés az [objektumészlelés](concept-object-detection.md) egy speciális módja, amely több ezer globális embléma adatbázisát használja a képeken vagy videókon lévő kereskedelmi márkák azonosítására. Ezzel a funkcióval például felfedezheti, hogy mely márkák a legnépszerűbbek a közösségi médiában, vagy a legelterjedtebbek a médiatermékek elhelyezésében.

A Computer Vision szolgáltatás észleli, hogy vannak-e márkalogók egy adott képen; ha igen, akkor a márkanevet, a megbízhatósági pontszámot és az embléma körüli határolókeret koordinátáit adja vissza.

A beépített logó adatbázis kiterjed népszerű márkák fogyasztói elektronika, ruházat, és így tovább. Ha úgy találja, hogy a keresett márkát a Computer Vision szolgáltatás nem észleli, akkor jobban kiszolgálhatja a saját emblémadetektor létrehozását és képzését a [Custom Vision](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) szolgáltatás használatával.

## <a name="brand-detection-example"></a>Példa márkaészlelésre

A következő JSON-válaszok bemutatják, hogy a Computer Vision milyen értéket ad vissza a példaképekben lévő márkák észlelésekor.

![Piros ing, rajta Microsoft címkével és logóval](./Images/red-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":20,
         "y":97,
         "w":62,
         "h":52
      }
   }
]
```

Bizonyos esetekben a márkadetektor két külön logóként veszi fel mind a logóképet, mind a stilizált márkanevet.

![Szürke pulóver Microsoft címkével és logóval](./Images/gray-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":106,
         "w":55,
         "h":46
      }
   },
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":86,
         "w":202,
         "h":63
      }
   }
]
```

## <a name="use-the-api"></a>Az API használata

A márkaészlelési funkció a [Kép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API része. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja meg. Szerepeljen `Brands` a **visualFeatures** lekérdezési paraméterben. Ezután, amikor megkapja a teljes JSON-választ, egyszerűen elemezje `"brands"` a szakasz tartalmának karakterláncát.

* [Rövid útmutató: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Rövid útmutató: Lemezkép elemzése (REST API)](./quickstarts/csharp-analyze.md)
