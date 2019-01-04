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
ms.date: 11/30/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 371fa639b2edc300e44cc495393e89c9fce9c4bf
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53580858"
---
# <a name="generating-smart-cropped-thumbnails-with-computer-vision"></a>A Computer Vision intelligens csonkolva miniatűrök létrehozása

A miniatűr kép csökkentett méretű reprezentációját. Miniatűr képeket és egyéb adatokat képviselő gazdaságosabb, elrendezés mobilbarát lehetővé szolgálnak. A Computer Vision API használatával intelligens levágás, a kép átméretezése együtt egy adott rendszerképhez intuitív miniatűrök létrehozása.

A Computer Vision, miniatűrkép-generálás algoritmus a következőképpen működik:
1. A lemezképből eltávolíthatja a zavaró elemeket, és azonosíthatja a _érdeklődési körét_&mdash;a terület a fő objektumok jelenik meg a kép.
1. Az azonosított alapján a kép levágása _érdeklődési körét_.
1. A cél miniatűr dimenziók igazítás oldalarányának módosítása.

## <a name="area-of-interest"></a>Érdeklődési körét

Ha feltölt egy képet, a Computer Vision API elemzi azt határozza meg a *érdeklődési körét*. Ebben a régióban használatával, majd határozza meg a kép levágása. A vágási művelet azonban mindig azonos lesz a kívánt oldalarány Ha meg van adva.

Is beszerezheti a nyers határolókeret koordinátái a azonos *érdeklődési körét* meghívásával a **areaOfInterest** API helyette. Ezután használhatja ezeket az adatokat az eredeti lemezkép módosítása azonban kívánja.

## <a name="examples"></a>Példák

A létrehozott miniatűr széles körben függően változhat a megadott magasság, szélesség és intelligens levágás, a következő képen látható módon.

![Miniatűrök](./Images/thumbnail-demo.png)

Az alábbi táblázat a példában képek a Computer Vision által generált tipikus miniatűrök mutatja be. A miniatűrök a célként megadott magasság generált, és 50 képpontos, intelligens vágása szélessége engedélyezve van.

| Kép | Miniatűr |
|-------|-----------|
|![Egy személy állandó az egy Hegyi rock napnyugta:](./Images/mountain_vista.png) | ![Öltözet hegyi miniatűrje](./Images/mountain_vista_thumbnail.png) |
|![Egy zöld hátterű fehér flower](./Images/flower.png) | ![Vizuális elemzése Flower miniatűrje](./Images/flower_thumbnail.png) |
|![Egy nő a tető egy apartman épület](./Images/woman_roof.png) | ![Nőről tető miniatűrje](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>További lépések

Ismerje meg [rendszerképek címkézése](concept-tagging-images.md) és [lemezképek kategorizálásához](concept-categorizing-images.md).