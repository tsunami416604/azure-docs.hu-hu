---
title: Márka észlelése – Computer Vision
titleSuffix: Azure Cognitive Services
description: Ez a cikk az objektumok észlelésének speciális módját tárgyalja; a márka és/vagy embléma észlelése a Computer Vision API használatával.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 117beca1284f28c75c1ac772425423f732b8a236
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73718636"
---
# <a name="detect-popular-brands-in-images"></a>Népszerű márkák felismerése képeken

A márka észlelése az [objektumok észlelésének](concept-object-detection.md) speciális módja, amely több ezer globális emblémát tartalmazó adatbázist használ a képekben vagy videókban található kereskedelmi márkák azonosítására. Ezt a funkciót használhatja például arra, hogy felderítse, mely márkák a legnépszerűbbek a közösségi médiában, vagy a média termékeinek legelterjedtebb eleme.

A Computer Vision szolgáltatás észleli, hogy van-e olyan márka emblémája egy adott képben; Ha igen, a rendszer visszaadja a márka nevét, a megbízhatósági pontszámot és az embléma körüli határoló mező koordinátáit.

A beépített embléma-adatbázis a népszerű márkákat tartalmazza a fogyasztói elektronika, a ruházati szolgáltatások és egyebek terén. Ha azt tapasztalja, hogy a keresett márkát nem észleli a Computer Vision szolgáltatás, akkor lehet, hogy jobban kiszolgálhatja a saját emblémás detektor létrehozását és betanítását a [Custom Vision](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) szolgáltatás használatával.

## <a name="brand-detection-example"></a>Példa a márka észlelésére

A következő JSON-válaszok azt szemléltetik, hogy milyen Computer Vision ad vissza, amikor a képeken a márkákat észleli.

![Egy piros póló egy Microsoft-címkével és-emblémával](./Images/red-shirt-logo.jpg)

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

Bizonyos esetekben a Brand detektor az embléma képét és a stilizált márkanevet is két külön emblémaként fogja felvenni.

![Egy szürke pulóver egy Microsoft-címkével és-emblémával](./Images/gray-shirt-logo.jpg)

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

A márka észlelése funkció a [rendszerkép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API részét képezi. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja. `Brands` belefoglalása a **visualFeatures** lekérdezési paraméterbe. Ezután a teljes JSON-válasz beszerzése után egyszerűen elemezze az `"brands"` szakasz tartalmának karakterláncát.

* [Gyors útmutató: Computer Vision .NET SDK](./quickstarts-sdk/csharp-sdk.md)
* [Gyors útmutató: rendszerkép elemzése (REST API)](./quickstarts/csharp-analyze.md)
