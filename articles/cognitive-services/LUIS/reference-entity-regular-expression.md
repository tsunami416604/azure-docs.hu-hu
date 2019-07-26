---
title: Reguláris kifejezés típusú entitás típusa
titleSuffix: Language Understanding - Azure Cognitive Services
description: A reguláris kifejezés a legjobb a nyers szövegekhez. Figyelmen kívül hagyja az esetet, és figyelmen kívül hagyja a kulturális változat.  Reguláris kifejezések egyeztetésének szinten karaktert, nem a token szint helyesírás-ellenőrzésének átalakítás után alkalmazza.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: dfffea26ccd8dfcecea2c6a2e07234f3ab27f72b
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480230"
---
# <a name="regular-expression-entity"></a>Reguláriskifejezés-entitás 

A reguláris kifejezés entitás kinyeri az entitást az Ön által megadott reguláris kifejezési minta alapján.

A reguláris kifejezés a legjobb a nyers szövegekhez. Figyelmen kívül hagyja az esetet, és figyelmen kívül hagyja a kulturális változat.  Reguláris kifejezések egyeztetésének szinten karaktert, nem a token szint helyesírás-ellenőrzésének átalakítás után alkalmazza. Ha a reguláris kifejezés túl összetett, például sok szögletes zárójelet használ, nem tudja felvenni a kifejezést a modellbe. A nem az összes [.net regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expressions) függvénytár részét használja. 

**Az entitás jól illeszkedik, ha:**

* Az adat konzisztens módon formázható minden olyan változattal, amely szintén konzisztens.
* A reguláris kifejezéshez nem szükséges több, mint 2 beágyazási szint. 

![Reguláriskifejezés-entitás](./media/luis-concept-entities/regex-entity.png)

## <a name="usage-considerations"></a>Használati megfontolások

A reguláris kifejezések nem egyeznek meg a vártnál. Ilyen `one` például a és `two`a numerikus szó egyeztetése. Ilyen például a következő regex, amely megfelel a számnak `one` a többi számmal együtt:

```javascript
(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*
``` 

Ez a regex kifejezés minden olyan szót tartalmaz, amely ezekkel a számokkal `phone`végződik, például:. A probléma megoldása érdekében győződjön meg arról, hogy a regex-egyezések figyelembe veszik a Word határait. A jelen példában a Word-határok használatára szolgáló regex a következő regexben van használatban:

```javascript
\b(plus )?(zero|one|two|three|four|five|six|seven|eight|nine)(\s+(zero|one|two|three|four|five|six|seven|eight|nine))*\b
```

### <a name="example-json"></a>Példa JSON-ban

Ha a `kb[0-9]{6}`használatakor a reguláris kifejezés entitásának definícióját használja, a következő JSON-válasz egy példa az eredményül kapott reguláris kifejezés entitásokra `When was kb123456 published?`a lekérdezéshez:

```JSON
{
  "query": "when was kb123456 published?",
  "topScoringIntent": {
    "intent": "FindKBArticle",
    "score": 0.933641255
  },
  "intents": [
    {
      "intent": "FindKBArticle",
      "score": 0.933641255
    },
    {
      "intent": "None",
      "score": 0.04397359
    }
  ],
  "entities": [
    {
      "entity": "kb123456",
      "type": "KB number",
      "startIndex": 9,
      "endIndex": 16
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

Ebben az [oktatóanyagban](luis-quickstart-intents-regex-entity.md)hozzon létre egy alkalmazást a konzisztens formátumú adatok kinyeréséhez a **reguláris kifejezés** entitás használatával.