---
title: Tartományspecifikus tartalom - Computer Vision
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogy miként adhat meg képkategorizálási tartományt a képekkel kapcsolatos részletesebb információk visszaadásához.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8d6dc91ae7bb0f6d7a24064749d9295558a7d39c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68946339"
---
# <a name="detect-domain-specific-content"></a>Tartományspecifikus tartalom észlelése

A címkézés és a magas szintű kategorizálás mellett a Computer Vision további tartományspecifikus elemzést is támogat a speciális adatokra betanított modellek használatával.

A tartományspecifikus modelleket kétféleképpen használhatja: önmagukban (hatókörrel végzett elemzés) vagy a kategorizálási szolgáltatás továbbfejlesztéseként.

### <a name="scoped-analysis"></a>Hatókör-elemzés

A modell csak a kiválasztott tartományspecifikus modell használatával elemezheti a [Modellek/\<\>modell /Analyze](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) API hívásával.

A következő egy minta JSON választ adott vissza a **modellek / hírességek / API elemzése** API az adott kép:

![Satya Nadella állva, mosolyogva](./images/satya.jpeg)

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

### <a name="enhanced-categorization-analysis"></a>Továbbfejlesztett kategorizálási elemzés

Tartományspecifikus modellek et is használhat az általános képelemzés kiegészítésére. Ezt a magas [szintű kategorizálás](concept-categorizing-images.md) részeként végezze el, ha tartományspecifikus modelleket ad meg az [Api-hívás](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) *elemzési* paraméterében.

Ebben az esetben először a 86 kategóriás taxonómiai osztályozót nevezzük. Ha az észlelt kategóriák bármelyike rendelkezik egy megfelelő tartomány-specifikus modellel, a rendszerkép is áthalad a modellen, és az eredmények hozzáadódnak.

A következő JSON-válasz bemutatja, hogyan tartományspecifikus elemzés szerepelhet `detail` csomópontként egy szélesebb kategorizálási elemzésben.

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

## <a name="list-the-domain-specific-models"></a>A tartományspecifikus modellek felsorolása

A Computer Vision jelenleg a következő tartományspecifikus modelleket támogatja:

| Név | Leírás |
|------|-------------|
| Hírességek | Híresség elismerés, támogatott képek `people_` besorolása a kategóriában |
| Tereptárgyak | Landmark felismerés, támogatott képek `outdoor_` minősített `building_` vagy kategóriák |

A [Models](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) API hívása visszaadja ezt az információt azokkal a kategóriákkal együtt, amelyekre az egyes modellek alkalmazhatók:

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

Ismerje meg a [képek kategorizálásának](concept-categorizing-images.md)fogalmait.
