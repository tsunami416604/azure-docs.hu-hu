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
ms.openlocfilehash: 477349f1addf71a30e8ecb179266d8eac5510887
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80244750"
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

| Kép | Kategória |
|-------|----------|
| ![Négy személy jelent együtt családként](./Images/family_photo.png) | people_group |
| ![Egy kiskutya ült egy füves mezőben](./Images/cute_dog.png) | animal_dog |
| ![A napnyugtakor egy hegyi sziklán álló személy](./Images/mountain_vista.png) | outdoor_mountain |
| ![Egy halom kenyér szerepkörei egy táblán](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>Az API használata

A kategorizálási funkció a [rendszerkép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API részét képezi. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja. Belefoglalás `Categories` a **visualFeatures** lekérdezési paraméterbe. Ezután, amikor megkapja a teljes JSON-választ, egyszerűen elemezze a `"categories"` szakasz tartalmának karakterláncát.

* [Gyors útmutató: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Gyors útmutató: rendszerkép elemzése (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>További lépések

Ismerje meg a [képek címkézésével](concept-tagging-images.md) és a [képek leírásával](concept-describing-images.md)kapcsolatos fogalmakat.
