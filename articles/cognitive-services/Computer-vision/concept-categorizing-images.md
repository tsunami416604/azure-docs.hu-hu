---
title: Képek kategorizálása
titleSuffix: Computer Vision - Cognitive Services - Azure
description: A Computer Vision segítségével az Azure Cognitive Servicesben lemezképek kategorizálásához kapcsolódó fogalom.
services: cognitive-services
author: deken
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: 9c15392e7f7df346553f925c5bf255ee900d25fe
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44725394"
---
# <a name="categorizing-images"></a>Képek kategorizálása

Címkézés és a leírások mellett a Computer Vision a korábbi verziókban meghatározott besorolás-alapú kategóriák adja vissza. Ezekben a kategóriákban egy szülő-gyermek örökletes hierarchiákkal rendelkező besorolás szerint vannak rendszerezve. Az összes kategória angolul jelennek meg. Önállóan vagy az új modellek címkézést is használt.

## <a name="the-86-category-concept"></a>A kategória 86 fogalma

Az alábbi ábrán látható 86 fogalmak listája alapján, kép kategorizálhatók és a széles körű-specifikus. A teljes szöveges formátumú besorolás, lásd: [Kategóriaelnevezési](category-taxonomy.md).

![Kategóriák elemzése](./Images/analyze_categories.png)

## <a name="image-categorization-examples"></a>Kép kategorizálási példák

A következő JSON-választ mutatja be, milyen számítógépes Látástechnológiai ad vissza, ha a alapuló a vizuális jellemzőket példaképen kategorizálásához.

![Nőről tető](./Images/woman_roof.png)

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
| ![Cuki kutya](./Images/cute_dog.png) | animal_dog |
| ![Öltözet hegyi](./Images/mountain_vista.png) | outdoor_mountain |
| ![Vizuális Food kenyér elemzése](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>További lépések

Fogalmak ismertetése [rendszerképek címkézése](concept-tagging-images.md) és [lemezképek leíró](concept-describing-images.md).