---
title: Rendszerkép kategorizálása – Computer Vision
titleSuffix: Azure Cognitive Services
description: A Computer Vision API képkategorizálási funkciójával kapcsolatos fogalmak megismerése.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2b9b8da550d80b027da919ba0834e43e2c83d4b4
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945312"
---
# <a name="categorize-images-by-subject-matter"></a>Képek kategorizálása tárgy szerint

A címkék és a leírások mellett Computer Vision a rendszerképben észlelt besorolási alapú kategóriákat adja vissza. A címkéktől eltérően a kategóriák egy szülő/gyermek örökletes hierarchiában vannak rendszerezve, és kevesebb van belőlük (86, több ezer címkével szemben). Az összes kategória neve angol nyelven van. A kategorizálás saját maga vagy az újabb címkék modellje mellett is elvégezhető.

## <a name="the-86-category-concept"></a>A 86 kategorizáló fogalom

A számítógép-jövőkép a következő ábrán látható 86-kategóriák használatával széles körben kategorizálhatja a rendszerképet. Az elnevezések teljes, szöveges listáját a [Kategóriák elnevezései](category-taxonomy.md) oldalon találja meg.

![A besorolási kategória összes kategóriájának csoportosított listája](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Példák a képek kategorizálására

A következő JSON-válasz azt szemlélteti, hogy milyen Computer Vision ad vissza, amikor a példa képének kategorizálása a vizualizáció tulajdonságai alapján történik.

![Egy lakás-épület tetején található nő](./Images/woman_roof.png)

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

Az alábbi táblázat egy jellemző képkészletet és Computer Vision által visszaadott kategóriát mutat be minden egyes képhez.

| Image | Category |
|-------|----------|
| ![Négy személy jelent együtt családként](./Images/family_photo.png) | people_group |
| ![Egy kiskutya ült egy füves mezőben](./Images/cute_dog.png) | animal_dog |
| ![A napnyugtakor egy hegyi sziklán álló személy](./Images/mountain_vista.png) | outdoor_mountain |
| ![Egy halom kenyér szerepkörei egy táblán](./Images/bread.png) | food_bread |

## <a name="next-steps"></a>További lépések

Ismerje meg a [](concept-tagging-images.md) képek címkézésével és a [képek leírásával](concept-describing-images.md)kapcsolatos fogalmakat.
