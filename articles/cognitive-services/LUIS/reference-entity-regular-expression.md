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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
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

A reguláris kifejezések nem egyeznek meg a vártnál. Ilyen például a `one` és `two`a numerikus szó egyeztetése. Ilyen például a következő regex, amely megfelel a számnak `one` a többi számmal együtt:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
```

Ez a regex kifejezés minden olyan szót tartalmaz, amely ezekkel a számokkal `phone`végződik, például:. A probléma megoldása érdekében győződjön meg arról, hogy a regex-egyezések figyelembe veszik a Word határait. A jelen példában a Word-határok használatára szolgáló regex a következő regexben van használatban:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Példa JSON-ra

Ha a `kb[0-9]{6}`használatakor a reguláris kifejezés entitásának definícióját használja, a következő JSON-válasz egy példa az eredményül kapott reguláris kifejezés entitásokra a lekérdezéshez:

`When was kb123456 published?`:

#### <a name="v2-prediction-endpoint-response"></a>[V2 előrejelzési végpont válasza](#tab/V2)

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


#### <a name="v3-prediction-endpoint-response"></a>[V3 előrejelzési végpont válasza](#tab/V3)


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

Ebben az [oktatóanyagban](tutorial-regex-entity.md)hozzon létre egy alkalmazást a konzisztens formátumú adatok kinyeréséhez a **reguláris kifejezés** entitás használatával.