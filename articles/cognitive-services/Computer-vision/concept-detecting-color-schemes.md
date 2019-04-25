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
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ff7af2204f9e05a1ba4ef800c63c3ad462242350
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60368569"
---
# <a name="detect-color-schemes-in-images"></a>Észlelheti a színsémákat képek

Computer Vision elemzi a képen három különböző attribútumainak megadásához a színek: a domináns előtérszín, a domináns háttérszín és domináns színek a lemezkép teljes készletét. A készlethez tartozó színek visszaadott: fekete, kék, brown, szürke, zöld, orange, rózsaszínre, lila, vörös, pávakék, fehér és sárga. 

Számítógépes Látástechnológia is az a képen domináns színek és színtelítettség kombinációja alapján a legtöbb élénk szín jelöli Kiemelőszín adja eredményül. A gombszöveg színét, egy HTML-színt hexadecimális kódot adja vissza. 

Számítógépes Látástechnológia is egy logikai érték, amely a kép fekete-fehér e adja vissza.

## <a name="color-scheme-detection-examples"></a>Szín séma észlelési példák

Az alábbi példában látható a JSON-választ adott vissza, Computer Vision által a példaképen színsémájának észlelése. Ebben az esetben a példaképen nem egy fekete-fehér-rendszerképet, de a domináns előtérbeli és a háttérszínek feketék, és a domináns színek egészére a kép fekete-fehér.

![Öltözet hegyi napnyugta, és a egy személy körvonalszerűen:](./Images/mountain_vista.png)

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

Az alábbi táblázat a visszaadott előtér, háttér és az egyes minta lemezképek lemezkép színeket.

| Image | Domináns színek |
|-------|-----------------|
|![Egy zöld hátterű fehér flower](./Images/flower.png)| Előtér: Fekete<br/>Háttér: Fehér<br/>Színek: Fekete, a fehér, a zöld|
![Állomás keresztül futtató vonaton](./Images/train_station.png) | Előtér: Fekete<br/>Háttér: Fekete<br/>Színek: Fekete |

### <a name="accent-color-examples"></a>Gombszöveg színét példák

 Az alábbi táblázat a visszaadott Kiemelés színe hexadecimális értékként HTML szín, az egyes példa lemezképek.

| Image | Kiegészítő szín |
|-------|--------------|
|![Egy személy állandó az egy Hegyi rock napnyugta:](./Images/mountain_vista.png) | #BB6D10 |
|![Egy zöld hátterű fehér flower](./Images/flower.png) | #C6A205 |
|![Állomás keresztül futtató vonaton](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Fekete-fehér észlelési példák

Az alábbi táblázat számítógépes Látástechnológiai fekete-fehér kiértékelése a minta képeket.

| Image | Fekete-fehér? |
|-------|----------------|
|![Épületek Manhattan a kép fekete-fehér](./Images/bw_buildings.png) | true |
|![Egy kék ház és az első yard](./Images/house_yard.png) | false |

## <a name="next-steps"></a>További lépések

Tudnivalók a fogalmak [képtípussal észlelése](concept-detecting-image-types.md).
