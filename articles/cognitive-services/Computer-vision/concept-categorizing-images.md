---
title: Képek – Computer Vision kategorizálása
titleSuffix: Azure Cognitive Services
description: Képek a Computer Vision API használatával kategorizálásához kapcsolatos fogalmakat.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: pafarley
ms.openlocfilehash: 7062d98d40c15f4e9e873038fc12fc1b104c996d
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333479"
---
# <a name="categorizing-images"></a>Képek kategorizálása

Címkézés és a leírások mellett a Computer Vision a korábbi verziókban meghatározott besorolás-alapú kategóriák adja vissza. Ezek a kategóriák öröklődő szülő/gyermek hierarchián alapuló elnevezésekkel vannak besorolva. Minden kategórianév angol nyelvű. Önállóan vagy az új modellek címkézést is használt.

## <a name="the-86-category-concept"></a>A 86 kategorizáló fogalom

Az alábbi ábrán látható 86 fogalmak listája alapján, kép kategorizálhatók és a széles körű-specifikus. Az elnevezések teljes, szöveges listáját a [Kategóriák elnevezései](category-taxonomy.md) oldalon találja meg.

![a kategória besorolás minden kategória csoportosított listája](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Kép kategorizálási példák

A következő JSON-választ mutatja be, milyen számítógépes Látástechnológiai ad vissza, ha a alapuló a vizuális jellemzőket példaképen kategorizálásához.

![Nő a tetőn](./Images/woman_roof.png)

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
| ![Családi fénykép](./Images/family_photo.png) | people_group |
| ![Aranyos kutya](./Images/cute_dog.png) | animal_dog |
| ![Hegyvidéki tájkép](./Images/mountain_vista.png) | outdoor_mountain |
| ![Elemzési célra készült, kenyeret ábrázoló kép](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>További lépések

Fogalmak ismertetése [rendszerképek címkézése](concept-tagging-images.md) és [lemezképek leíró](concept-describing-images.md).