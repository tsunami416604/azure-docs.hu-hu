---
title: A miniatűrök – Computer Vision generálása
titleSuffix: Azure Cognitive Services
description: Képek a Computer Vision API használatával miniatűrök létrehozása a kapcsolódó fogalmak.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 9cb82b40d1fbec513b0219f26d1959fbd7f64570
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49343962"
---
# <a name="generating-thumbnails"></a>Miniatűrök létrehozása

A miniatűr a teljes méretű kép kis reprezentációját. Változatos eszközök, például telefonokon, táblagépeken és számítógépeken hozzon létre egy másik felhasználói felület (UX) elrendezések és a miniatűr méretek van szükség. Intelligens levágás, a Computer Vision funkció segítségével megoldani a problémát.

Kép feltöltése után a Computer Vision egy jó minőségű miniatűrt hoz létre, és ezután elemzi a tárgyakat azonosíthatja a *a lényeges terület* (ROI). Azt is igény szerint vágja körül, a képet a megtérülési RÁTÁRA követelményeinek. A létrehozott miniatűr, amely eltér az eredeti kép, hogy megfeleljen az igényeinek oldalarányának méretarány használatával jelenítheti meg.

A miniatűr algoritmus a következőképpen történik:

1. Zavaró elemet távolít el a lemezképet, és a fő objektum, a tartományban, azonosítja.
2. Körülvágja a képet a lényeges azonosított régió alapján.
3. Az arányt, a cél miniatűr dimenziók megfelelően változik.

A létrehozott miniatűr széles körben függően változhat a megadott magasság, szélesség és intelligens levágás, a következő képen látható módon.

![Miniatűrök](./Images/thumbnail-demo.png)

## <a name="thumbnail-generation-examples"></a>A miniatűrkészítés példák

Az alábbi táblázat a példában képek a Computer Vision által generált tipikus miniatűrök mutatja be. A miniatűrök a célként megadott magasság generált, és 50 képpontos, intelligens vágása szélessége engedélyezve van.

| Kép | Miniatűr |
|-------|-----------|
|![Öltözet hegyi](./Images/mountain_vista.png) | ![Öltözet hegyi miniatűrje](./Images/mountain_vista_thumbnail.png) |
|![Vizuális Flower elemzése](./Images/flower.png) | ![Vizuális elemzése Flower miniatűrje](./Images/flower_thumbnail.png) |
|![Nőről tető](./Images/woman_roof.png) | ![Nőről tető miniatűrje](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>További lépések

Fogalmak ismertetése [rendszerképek címkézése](concept-tagging-images.md) és [lemezképek kategorizálásához](concept-categorizing-images.md).