---
title: Reguláris kifejezés entitás típusa - LUIS
titleSuffix: Azure Cognitive Services
description: A reguláris kifejezés a legjobb a nyers utterance (kifejezés) szöveg. Figyelmen kívül hagyja az esetet, és figyelmen kívül hagyja a kulturális változatot.  A reguláris kifejezésegyeztetés a karakterszinten, nem pedig a token szinten történő helyesírás-ellenőrzés módosítása után kerül alkalmazásra.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: b9da76a80183f353a74d43e667bf6c9219eb6c05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74841217"
---
# <a name="regular-expression-entity"></a>Reguláriskifejezés-entitás

A reguláris kifejezés entitás kinyeri az entitást a megadott reguláris kifejezésminta alapján.

A reguláris kifejezés a legjobb a nyers utterance (kifejezés) szöveg. Figyelmen kívül hagyja az esetet, és figyelmen kívül hagyja a kulturális változatot.  A reguláris kifejezésegyeztetés a karakterszinten, nem pedig a token szinten történő helyesírás-ellenőrzés módosítása után kerül alkalmazásra. Ha a reguláris kifejezés túl összetett, például sok szögletes zárójelet használ, nem tudja hozzáadni a kifejezést a modellhez. A [.NET Regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) könyvtár részét használja, de nem az összeset.

**A gazdálkodó egység akkor illeszkedik, ha:**

* Az adatok konzisztensen vannak formázva minden olyan változattal, amely szintén konzisztens.
* A reguláris kifejezésnem igényel 2 szintnél több beágyazási szintet.

![Reguláriskifejezés-entitás](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Használati szempontok

A reguláris kifejezések megegyezhetnek a vártnál. Erre példa a numerikus szavak `one` `two`egyeztetése, például a és a. Erre példa a következő regex, `one` amely a számnak és a többi számnak felel meg:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Ez a regex kifejezés minden olyan szót is `phone`egyezik, amely ezekkel a számokkal végződik, például . Annak érdekében, hogy megoldja a problémákat, mint ez, győződjön meg róla, a regex mérkőzések figyelembe veszi szó határokat. A regex használni szó határok ebben a példában használják a következő regex:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Példa JSON

A `kb[0-9]{6}`használata esetén a , mint a reguláris kifejezés entitás definíciója, a következő JSON-válasz egy példa utterance kifejezés a visszaadott reguláris kifejezések entitások a lekérdezés:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpontra adott válasza](#tab/V2)

```JSON
"entities": [
  {
    "entity": "kb123456",
    "type": "KB number",
    "startIndex": 9,
    "endIndex": 16
  }
]
```


#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpontra adott válasza](#tab/V3)


Ez a JSON, ha `verbose=false` be van állítva a lekérdezési karakterláncban:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Ez a JSON, ha `verbose=true` be van állítva a lekérdezési karakterláncban:

```json
"entities": {
    "KB number": [
        "kb123456"
    ],
    "$instance": {
        "KB number": [
            {
                "type": "KB number",
                "text": "kb123456",
                "startIndex": 9,
                "length": 8,
                "modelTypeId": 8,
                "modelType": "Regex Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>További lépések

Ebben az [oktatóanyagban](tutorial-regex-entity.md)hozzon létre egy alkalmazást, amely következetesen formázott adatokat nyer ki egy utterance **(kifejezés)** kifejezés használatával.