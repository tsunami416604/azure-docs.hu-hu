---
title: Színsémák – Computer Vision észlelése
titleSuffix: Azure Cognitive Services
description: A Computer Vision API használatával képeken színsémáját észlelése kapcsolatos fogalmakat.
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: c01433ed3ef1279bf3db9bdcf76e57aad2208895
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45983597"
---
# <a name="detecting-color-schemes"></a>Színsémák észlelése

Számítógépes Látástechnológia színek kigyűjti a képen. Ezután elemzi a színeket három kontextusban: az domináns előtérszín, a domináns háttérszín és a teljes lemezkép domináns színek. 12 domináns kiemelési színcsoportba rendezi vannak csoportosítva. Ezen válogatja ki feketék, kék, brown, szürke, zöld, orange, rózsaszínre, lila, vörös, pávakék, fehér és sárga. Számítógépes Látástechnológia elemzi a kinyert kép egy Kiemelőszín domináns színek és színtelítettség együttes a nézők a legtöbb élénk szín a rendszerkép képviselő vissza színeket. Attól függően, a képet a színeket visszaadott a színt hexadecimális kódokkal egyszerű fekete-fehér vagy színcsoportba rendezi. Számítógépes Látástechnológia is adja vissza egy logikai érték, amely azt jelzi, hogy egy kép fekete-fehér.

## <a name="color-scheme-detection-examples"></a>Szín séma észlelési példák

Az alábbi példában látható a JSON-választ adott vissza, Computer Vision által a példaképen színsémájának észlelése. Ebben az esetben a példaképen nem egy fekete-fehér-rendszerképet, de az előtérbeli és háttérbeli domináns színek feketék, és a domináns színek egészére a kép fekete-fehér.

![Öltözet hegyi](./Images/mountain_vista.png)

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
|![Vizuális Flower elemzése](./Images/flower.png)| Előtér: fekete<br/>Háttér: fehér<br/>Szín: Fekete, a fehér, a zöld|
![Vizuális elemzése Train állomás](./Images/train_station.png) | Előtér: fekete<br/>Háttér: fekete<br/>Színek: fekete |

### <a name="accent-color-examples"></a>Gombszöveg színét példák

 A következő táblázat ismerteti a Kiemelés színe hexadecimális értékként HTML szín, az egyes példa lemezképek Computer Vision által visszaadott.

| Kép | Kiemelőszín |
|-------|--------------|
|![Öltözet hegyi](./Images/mountain_vista.png) | #BB6D10 |
|![Vizuális Flower elemzése](./Images/flower.png) | #C6A205 |
|![Vizuális elemzése Train állomás](./Images/train_station.png) | #474A84 |

### <a name="black--white-detection-examples"></a>Fekete-fehér észlelési példák

Az alábbi táblázat azt jelzi, hogy valamennyi Példa kép fekete-fehér, amelyet a Computer Vision.

| Kép | Fekete-fehér? |
|-------|----------------|
|![Vizuális elemzése létrehozása](./Images/bw_buildings.png) | true |
|![Vizuális ház Yard elemzése](./Images/house_yard.png) | false |

## <a name="next-steps"></a>További lépések

Tudnivalók a fogalmak [képtípussal észlelése](concept-detecting-image-types.md).