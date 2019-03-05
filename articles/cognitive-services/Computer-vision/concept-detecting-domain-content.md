---
title: Tartomány-specifikus tartalommal – a Computer Vision észlelése
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan adjon meg egy képet kategorizálási tartományt kép részletesebb információt ad vissza.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 92859667e1dc53b9c6ca9e46a2db1c6dc335ae37
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339011"
---
# <a name="detect-domain-specific-content"></a>Tartomány-specifikus tartalom észlelése

Emellett a címkézési és a magas szintű kategorizálási, Computer Vision is támogatja a további speciális adatokon, amely rendelkezik betanítva modellek használatával tartomány-specifikus elemzés.

A tartományspecifikus modellek használata két módja van: magukat (hatókörön belüli elemzés), akár a kategorizálási funkció továbbfejlesztése.

### <a name="scoped-analysis"></a>Hatókörön belüli elemzés

Csak a kiválasztott tartomány-specifikus modellt használó meghívásával kép elemezheti a [modellek /\<modell\>/elemzése](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) API-t.

Az alábbiakban a minta által visszaadott JSON-választ a **modellek, hírességek és elemzése** API a megadott lemezkép:

![Satya Nadella helyzet](./images/satya.jpeg)

```json
{
  "result": {
    "celebrities": [{
      "faceRectangle": {
        "top": 391,
        "left": 318,
        "width": 184,
        "height": 184
      },
      "name": "Satya Nadella",
      "confidence": 0.99999856948852539
    }]
  },
  "requestId": "8217262a-1a90-4498-a242-68376a4b956b",
  "metadata": {
    "width": 800,
    "height": 1200,
    "format": "Jpeg"
  }
}
```

### <a name="enhanced-categorization-analysis"></a>Továbbfejlesztett kategorizálási elemzése

Tartomány-specifikus modellek használatával általános képelemzés kiegészítésére. Részeként ehhez [magas szintű kategorizálási](concept-categorizing-images.md) adja meg a tartomány-specifikus modelleket a *részletek* paraméterében a [elemzés](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API-hívás.

Ebben az esetben a 86 kategóriaelnevezési osztályozó először neve. Ha észlelt kategóriákra rendelkezik a megfelelő tartomány-specifikus modellek, a lemezkép átadott is a modell és az eredmények kerülnek.

A következő JSON-válasz bemutatja, hogyan tartomány-specifikus elemzését is meg lehet adni, mint a `detail` egy szélesebb körű kategorizálási elemzési csomópontja.

```json
"categories":[
  {
    "name":"abstract_",
    "score":0.00390625
  },
  {
    "name":"people_",
    "score":0.83984375,
    "detail":{
      "celebrities":[
        {
          "name":"Satya Nadella",
          "faceRectangle":{
            "left":597,
            "top":162,
            "width":248,
            "height":248
          },
          "confidence":0.999028444
        }
      ],
      "landmarks":[
        {
          "name":"Forbidden City",
          "confidence":0.9978346
        }
      ]
    }
  }
]
```

## <a name="list-the-domain-specific-models"></a>A tartományspecifikus modellek listáját

Számítógépes Látástechnológia jelenleg a következő tartomány-specifikus modelleket támogatja:

| Name (Név) | Leírás |
|------|-------------|
| hírességek | Hírességek felismerése, támogatja a lemezképek besorolni a `people_` kategória |
| Arcrész | Tereptárgyak felismerése, támogatja a lemezképek besorolni a `outdoor_` vagy `building_` kategóriák |

Hívása a [modellek](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) API ad vissza, ez az információ, amelyhez alkalmazhat az egyes kategóriák együtt:

```json
{
  "models":[
    {
      "name":"celebrities",
      "categories":[
        "people_",
        "人_",
        "pessoas_",
        "gente_"
      ]
    },
    {
      "name":"landmarks",
      "categories":[
        "outdoor_",
        "户外_",
        "屋外_",
        "aoarlivre_",
        "alairelibre_",
        "building_",
        "建筑_",
        "建物_",
        "edifício_"
      ]
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

Tudnivalók a fogalmak [lemezképek kategorizálásához](concept-categorizing-images.md).
