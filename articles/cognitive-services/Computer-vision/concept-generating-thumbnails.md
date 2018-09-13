---
title: Miniatűrök létrehozása
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Computer Vision segítségével az Azure Cognitive Servicesben lemezképek miniatűrök létrehozása a kapcsolódó fogalmak.
services: cognitive-services
author: deken
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 92de0c0a428c9010188131a768074234241ed604
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44725408"
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