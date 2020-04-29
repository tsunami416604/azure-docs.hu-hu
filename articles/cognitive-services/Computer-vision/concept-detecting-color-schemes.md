---
title: Színséma észlelése – Computer Vision
titleSuffix: Azure Cognitive Services
description: A rendszerképek színsémájának észlelésével kapcsolatos fogalmak a Computer Vision API használatával.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80244732"
---
# <a name="detect-color-schemes-in-images"></a>Képek színsémáinak észlelése

Computer Vision elemzi a képen látható színeket, hogy három különböző attribútumot adjon meg: a domináns előtéri színt, a domináns háttérszínt, valamint a kép domináns színeit egészként. A visszaadott színek a készlethez tartoznak: fekete, kék, barna, szürke, zöld, narancssárga, rózsaszín, lila, piros, kékeszöld, fehér és sárga. 

A Computer Vision egy ékezetes színt is Kinyer, amely a képen a legélénkebb színt jelöli, a domináns színek és a telítettség kombinációja alapján. A rendszer a kiejtés színét hexadecimális HTML színkóddal adja vissza. 

A Computer Vision egy logikai értéket ad vissza, amely azt jelzi, hogy a képek fekete-fehérben vannak-e.

## <a name="color-scheme-detection-examples"></a>Példák a színséma észlelésére

Az alábbi példa a Computer Vision által visszaadott JSON-választ mutatja be, amikor észleli a képsémát a képen. Ebben az esetben a kép nem fekete-fehér kép, de a domináns előtér-és háttérszínek fekete színűek, a kép domináns színei pedig fekete és fehérek.

![Kültéri hegyi naplemente, egy személy sziluettje](./Images/mountain_vista.png)

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

### <a name="dominant-color-examples"></a>Domináns szín példák

A következő táblázat az egyes mintaképek előtérbeli, háttérszínét és képének színét mutatja be.

| Kép | Domináns színek |
|-------|-----------------|
|![Fehér virág Zöld háttérrel](./Images/flower.png)| Előtér: fekete<br/>Háttér: fehér<br/>Színek: fekete, fehér, zöld|
![Állomáson futó vonat](./Images/train_station.png) | Előtér: fekete<br/>Háttér: fekete<br/>Színek: fekete |

### <a name="accent-color-examples"></a>Kiejtési színek példái

 A következő táblázat a visszaadott hangsúlyozási színt jeleníti meg hexadecimális HTML színértékként az egyes képképeknél.

| Kép | Kiegészítő szín |
|-------|--------------|
|![A napnyugtakor egy hegyi sziklán álló személy](./Images/mountain_vista.png) | #BB6D10 |
|![Fehér virág Zöld háttérrel](./Images/flower.png) | #C6A205 |
|![Állomáson futó vonat](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Fekete & White észlelési példák

A következő táblázat Computer Vision fekete-fehér kiértékelését mutatja a minta lemezképekben.

| Kép | Fekete & fehér? |
|-------|----------------|
|![A Manhattanben található épületek fekete-fehér képe](./Images/bw_buildings.png) | igaz |
|![Egy kék ház és az első udvar](./Images/house_yard.png) | hamis |

## <a name="use-the-api"></a>Az API használata

A színséma-észlelési funkció a [rendszerkép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API részét képezi. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja. Belefoglalás `Color` a **visualFeatures** lekérdezési paraméterbe. Ezután, amikor megkapja a teljes JSON-választ, egyszerűen elemezze a `"color"` szakasz tartalmának karakterláncát.

* [Gyors útmutató: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Gyors útmutató: rendszerkép elemzése (REST API)](./quickstarts/csharp-analyze.md)
