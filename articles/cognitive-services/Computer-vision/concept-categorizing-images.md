---
title: Képek – Computer Vision kategorizálása
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogy a lemezkép kategorizálási funkciója a Computer Vision API kapcsolatos fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 40873b13407066920caec5c04751b65c01d7e209
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53579498"
---
# <a name="image-categorization-with-computer-vision"></a>Kép kategorizálási a Computer Vision

Címkézés és a leírások mellett a Computer Vision a korábbi verziókban meghatározott besorolás-alapú kategóriák adja vissza. Ezek a kategóriák öröklődő szülő/gyermek hierarchián alapuló elnevezésekkel vannak besorolva. Minden kategórianév angol nyelvű. Önállóan vagy az új modellek címkézést is használt.

## <a name="the-86-category-concept"></a>A 86 kategorizáló fogalom

Az alábbi ábrán látható 86 fogalmak listája alapján, kép kategorizálhatók és a széles körű-specifikus. Az elnevezések teljes, szöveges listáját a [Kategóriák elnevezései](category-taxonomy.md) oldalon találja meg.

![a kategória besorolás minden kategória csoportosított listája](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Kép kategorizálási példák

A következő JSON-választ mutatja be, milyen számítógépes Látástechnológiai ad vissza, ha a alapuló a vizuális jellemzőket példaképen kategorizálásához.

![Egy nő a tető egy apartman épület](./Images/woman_roof.png)

```json
{
    "categories": [
        {
            "name": "people_",
            "score": 0.81640625
        }
    ],
    "requestId": "bae7f76a-1cc7-4479-8d29-48a694974705",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

Az alábbi táblázatban egy jellemző kép beállítása és az egyes rendszerképek a Computer Vision által visszaadott kategória mutatja be.

| Kép | Kategória |
|-------|----------|
| ![Négy személyt együttesen, egy csomagcsalád feltételéről](./Images/family_photo.png) | people_group |
| ![Egy grassy mező úgy ételadagot](./Images/cute_dog.png) | animal_dog |
| ![Egy személy állandó az egy Hegyi rock napnyugta:](./Images/mountain_vista.png) | outdoor_mountain |
| ![Egy táblán kenyér szerepkörök tárházát](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>További lépések

Fogalmak ismertetése [rendszerképek címkézése](concept-tagging-images.md) és [lemezképek leíró](concept-describing-images.md).