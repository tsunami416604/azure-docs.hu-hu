---
title: Színsémák – Computer Vision észlelése
titleSuffix: Azure Cognitive Services
description: A Computer Vision API használatával képeken színsémáját észlelése kapcsolatos fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 0793f572e043248af409e65cca4fd854f1371900
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55880878"
---
# <a name="detect-color-schemes-in-images"></a>Észlelheti a színsémákat képek

Számítógépes Látástechnológia színek kigyűjti a képen. Ezután elemzi a színeket három kontextusban: az domináns előtérszín, a domináns háttérszín és a teljes lemezkép domináns színek. 12 domináns kiemelési színcsoportba rendezi vannak csoportosítva. Ezek a színek a következők: fekete, kék, barna, szürke, zöld, narancsszín, rózsaszín, lila, vörös, pávakék, fehér és sárga. Számítógépes Látástechnológia elemzi a kinyert kép egy Kiemelőszín domináns színek és színtelítettség együttes a nézők a legtöbb élénk szín a rendszerkép képviselő vissza színeket. Egy kép színösszeállításától függően az egyszerű fekete és fehér vagy más színek hexadecimális színkódokként lehetnek visszaadva. Számítógépes Látástechnológia is adja vissza egy logikai érték, amely azt jelzi, hogy egy kép fekete-fehér.

## <a name="color-scheme-detection-examples"></a>Szín séma észlelési példák

Az alábbi példában látható a JSON-választ adott vissza, Computer Vision által a példaképen színsémájának észlelése. Ebben az esetben a példaképen nem egy fekete-fehér-rendszerképet, de az előtérbeli és háttérbeli domináns színek feketék, és a domináns színek egészére a kép fekete-fehér.

![Hegyvidéki tájkép](./Images/mountain_vista.png)

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

### <a name="dominant-color-examples"></a>Színig példák

A következő táblázat ismerteti a domináns előtér, háttér és lemezkép színeket az egyes példa-lemezképek, amelyet a Computer Vision.

| Kép | Domináns színek |
|-------|-----------------|
|![Egy zöld hátterű fehér flower](./Images/flower.png)| Előtér: Fekete<br/>Háttér: Fehér<br/>Színek: Fekete, a fehér, a zöld|
![Állomás keresztül futtató vonaton](./Images/train_station.png) | Előtér: Fekete<br/>Háttér: Fekete<br/>Színek: Fekete |

### <a name="accent-color-examples"></a>Gombszöveg színét példák

 A következő táblázat ismerteti a Kiemelés színe hexadecimális értékként HTML szín, az egyes példa lemezképek Computer Vision által visszaadott.

| Kép | Kiegészítő szín |
|-------|--------------|
|![Egy személy állandó az egy Hegyi rock napnyugta:](./Images/mountain_vista.png) | #BB6D10 |
|![Egy zöld hátterű fehér flower](./Images/flower.png) | #C6A205 |
|![Állomás keresztül futtató vonaton](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Fekete-fehér észlelési példák

Az alábbi táblázat azt jelzi, hogy valamennyi Példa kép fekete-fehér, amelyet a Computer Vision.

| Kép | Fekete-fehér? |
|-------|----------------|
|![Épületek Manhattan a kép fekete-fehér](./Images/bw_buildings.png) | true |
|![Egy kék ház és az első yard](./Images/house_yard.png) | false |

## <a name="next-steps"></a>További lépések

Tudnivalók a fogalmak [képtípussal észlelése](concept-detecting-image-types.md).
