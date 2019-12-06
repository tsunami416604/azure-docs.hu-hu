---
title: Reguláris kifejezés típusú entitás – LUIS
titleSuffix: Azure Cognitive Services
description: A reguláris kifejezés a legjobb a nyers szövegekhez. Figyelmen kívül hagyja az esetet, és figyelmen kívül hagyja a kulturális változatot.  A reguláris kifejezések egyeztetése a karakter szintjén, nem pedig a jogkivonat szintjén történik.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: b9da76a80183f353a74d43e667bf6c9219eb6c05
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74841217"
---
# <a name="regular-expression-entity"></a>Reguláriskifejezés-entitás

A reguláris kifejezés entitás kinyeri az entitást az Ön által megadott reguláris kifejezési minta alapján.

A reguláris kifejezés a legjobb a nyers szövegekhez. Figyelmen kívül hagyja az esetet, és figyelmen kívül hagyja a kulturális változatot.  A reguláris kifejezések egyeztetése a karakter szintjén, nem pedig a jogkivonat szintjén történik. Ha a reguláris kifejezés túl összetett, például sok szögletes zárójelet használ, nem tudja felvenni a kifejezést a modellbe. A nem az összes [.net regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) függvénytár részét használja.

**Az entitás jól illeszkedik, ha:**

* Az adat konzisztens módon formázható minden olyan változattal, amely szintén konzisztens.
* A reguláris kifejezéshez nem szükséges több, mint 2 beágyazási szint.

![Reguláriskifejezés-entitás](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Használati megfontolások

A reguláris kifejezések nem egyeznek meg a vártnál. Ilyen például a numerikus szó, például a `one` és az `two`. Ilyen például a következő regex, amely megfelel a számnak `one` más számokkal együtt:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Ez a regex kifejezés olyan szavakat is tartalmaz, amelyek ezekkel a számokkal végződik, például `phone`. A probléma megoldása érdekében győződjön meg arról, hogy a regex-egyezések figyelembe veszik a Word határait. A jelen példában a Word-határok használatára szolgáló regex a következő regexben van használatban:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Példa JSON-ra

Ha `kb[0-9]{6}`használ, mint a reguláris kifejezés entitásának definíciója, a következő JSON-válasz egy példa a lekérdezésben szereplő reguláris kifejezéssel rendelkező entitásokra:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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


#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 előrejelzési végpont válasza](#tab/V3)


Ez a JSON, ha `verbose=false` van beállítva a lekérdezési karakterláncban:

```json
"entities": {
    "KB number": [
        "kb123456"
    ]
}
```

Ez a JSON, ha `verbose=true` van beállítva a lekérdezési karakterláncban:

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

## <a name="next-steps"></a>Következő lépések

Ebben az [oktatóanyagban](tutorial-regex-entity.md)hozzon létre egy alkalmazást a konzisztens formátumú adatok kinyeréséhez a **reguláris kifejezés** entitás használatával.