---
title: Színséma észlelése - Computer Vision
titleSuffix: Azure Cognitive Services
description: A képek színsémájának a Computer Vision API használatával történő észlelésével kapcsolatos fogalmak.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: af0c39ed8211ac2041d143112437ad5d6b384259
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244732"
---
# <a name="detect-color-schemes-in-images"></a>Színsémák észlelése képeken

A Computer Vision elemzi a kép színeit, hogy három különböző tulajdonságot adjon meg: a domináns előtérszínt, a domináns háttérszínt és a kép egészének domináns színeit. A visszaadott színek a készlethez tartoznak: fekete, kék, barna, szürke, zöld, narancs, rózsaszín, lila, piros, kékeszöld, fehér és sárga. 

Computer Vision is kibontja a kiemelő szín, amely képviseli a legélénkebb szín a képen, kombinációja alapján domináns színek és telítettség. A kiemelő szín hexadecimális HTML színkódként lesz visszaadva. 

A Computer Vision logikai értéket is ad vissza, amely azt jelzi, hogy a kép fekete-fehér.

## <a name="color-scheme-detection-examples"></a>Példák színséma-észlelési példák

A következő példa a Computer Vision által visszaadott JSON-választ mutatja be a példakép színsémájának észlelésekor. Ebben az esetben a példakép nem fekete-fehér kép, de a domináns előtér és háttérszínek fekete színűek, és a kép egészének domináns színei fekete-fehérek.

![Szabadtéri hegy napnyugtakor, egy személy sziluettjével](./Images/mountain_vista.png)

```json
{
    "color": {
        "dominantColorForeground": "Black",
        "dominantColorBackground": "Black",
        "dominantColors": ["Black", "White"],
        "accentColor": "BB6D10",
        "isBwImg": false
    },
    "requestId": "0dc394bf-db50-4871-bdcc-13707d9405ea",
    "metadata": {
        "height": 202,
        "width": 300,
        "format": "Jpeg"
    }
}
```

### <a name="dominant-color-examples"></a>Példák domináns színre

Az alábbi táblázat az egyes mintaképek retonátorait, hátterét és képszíneit mutatja be.

| Kép | Domináns színek |
|-------|-----------------|
|![Fehér virág zöld háttérrel](./Images/flower.png)| Előtér: Fekete<br/>Háttér: Fehér<br/>Színek: fekete, fehér, zöld|
![Egy állomáson áthaladó vonat](./Images/train_station.png) | Előtér: Fekete<br/>Háttér: Fekete<br/>Színek: Fekete |

### <a name="accent-color-examples"></a>Példák a kiemelő színre

 Az alábbi táblázat a visszaadott kiemelőszínt mutatja, hexadecimális HTML színértékként, minden példaképhez.

| Kép | Kiegészítő szín |
|-------|--------------|
|![Egy személy, aki egy hegyi sziklán áll napnyugtakor](./Images/mountain_vista.png) | #BB6D10 |
|![Fehér virág zöld háttérrel](./Images/flower.png) | #C6A205 |
|![Egy állomáson áthaladó vonat](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Példák fekete & fehér észlelési

Az alábbi táblázat a Computer Vision fekete-fehér értékelését mutatja be a mintaképeken.

| Kép | Fekete & fehér? |
|-------|----------------|
|![Egy fekete-fehér képet épületek Manhattan](./Images/bw_buildings.png) | igaz |
|![Egy kék ház és az előkert](./Images/house_yard.png) | hamis |

## <a name="use-the-api"></a>Az API használata

A színséma észlelési szolgáltatás része a [Kép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja meg. Szerepeljen `Color` a **visualFeatures** lekérdezési paraméterben. Ezután, amikor megkapja a teljes JSON-választ, egyszerűen elemezje `"color"` a szakasz tartalmának karakterláncát.

* [Rövid útmutató: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Rövid útmutató: Lemezkép elemzése (REST API)](./quickstarts/csharp-analyze.md)
