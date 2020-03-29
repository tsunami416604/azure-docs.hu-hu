---
title: Képkategorizálás - Computer Vision
titleSuffix: Azure Cognitive Services
description: Ismerje meg a Computer Vision API képkategorizálási funkciójával kapcsolatos fogalmakat.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80244750"
---
# <a name="categorize-images-by-subject-matter"></a>Képek kategorizálása tárgy szerint

A címkék és a leírás mellett a Computer Vision a képen észlelt rendszeralapú kategóriákat is visszaadja. A címkékkel ellentétben a kategóriák szülő/gyermek örökletes hierarchiába vannak rendezve, és kevesebb van belőlük (86, szemben a címkék ezreivel). Minden kategórianév angol nyelvű. Kategorizálás lehet tenni önmagában vagy az újabb címkék modell mellett.

## <a name="the-86-category-concept"></a>A 86 kategorizáló fogalom

A számítógépes látás széles körben vagy konkrétan kategorizálhatja a képet az alábbi ábrán szereplő 86 kategóriából álló lista használatával. Az elnevezések teljes, szöveges listáját a [Kategóriák elnevezései](category-taxonomy.md) oldalon találja meg.

![A kategóriataxonómia összes kategóriájának csoportosított listája](./Images/analyze_categories-v2.png)

## <a name="image-categorization-examples"></a>Példák képkategorizálási példák

A következő JSON-válasz bemutatja, hogy a Computer Vision milyen értéket ad vissza a példakép vizuális jellemzői alapján történő kategorizálásakor.

![Egy nő a tetőn egy lakóház](./Images/woman_roof.png)

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

Az alábbi táblázat egy tipikus képkészletet és a Computer Vision által az egyes képekhez visszaadott kategóriát mutatja be.

| Kép | Kategória |
|-------|----------|
| ![Négy ember pózolt együtt, mint egy család](./Images/family_photo.png) | people_group |
| ![Egy kiskutya ül egy füves területen](./Images/cute_dog.png) | animal_dog |
| ![Egy személy, aki egy hegyi sziklán áll napnyugtakor](./Images/mountain_vista.png) | outdoor_mountain |
| ![Egy halom kenyér szerepek egy asztalon](./Images/bread.png) | food_bread |

## <a name="use-the-api"></a>Az API használata

A kategorizálási szolgáltatás része a [Kép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API. Ezt az API-t natív SDK-n vagy REST-hívásokon keresztül hívhatja meg. Szerepeljen `Categories` a **visualFeatures** lekérdezési paraméterben. Ezután, amikor megkapja a teljes JSON-választ, egyszerűen elemezje `"categories"` a szakasz tartalmának karakterláncát.

* [Rövid útmutató: Computer Vision .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Rövid útmutató: Lemezkép elemzése (REST API)](./quickstarts/csharp-analyze.md)

## <a name="next-steps"></a>További lépések

Ismerje meg a [képek címkézésének](concept-tagging-images.md) és [leírásának](concept-describing-images.md)kapcsolódó fogalmait.
