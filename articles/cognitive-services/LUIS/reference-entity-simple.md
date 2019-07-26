---
title: Egyszerű entitás típusa
titleSuffix: Language Understanding - Azure Cognitive Services
description: Az egyszerű entitások olyan általános entitások, amelyek egyetlen fogalmat ismertetnek, és a gép által megtanult környezetből származnak. Mivel az egyszerű entitások általában nevek, például a cégnevek, a terméknév vagy más kategóriájú nevek, akkor adjon meg egy kifejezési listát, ha egyszerű entitást használ a használt nevek jelzésének növelésére.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: 3f03b33f685bb5c7c9ba8f2267b8556c5dadade4
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68480219"
---
# <a name="simple-entity"></a>Egyszerű entitás 

Az egyszerű entitások olyan általános entitások, amelyek egyetlen fogalmat ismertetnek, és a gép által megtanult környezetből származnak. Mivel az egyszerű entitások általában nevek, például a cégnevek, a terméknév vagy más kategóriájú nevek, akkor adjon meg egy [kifejezési listát](luis-concept-feature.md) , ha egyszerű entitást használ a használt nevek jelzésének növelésére. 

**Az entitás jól illeszkedik, ha:**

* Az adat nem konzisztens formátumú, de ugyanazt a dolgot jelzi. 

![egyszerű entitás](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Példa JSON-ban

`Bob Jones wants 3 meatball pho`

Az előző utterance (kifejezés), a `Bob Jones` van jelölve egy egyszerű `Customer` entitás.

A végpont által visszaadott szerepel az entitás nevét, a felderített szöveget az utterance (kifejezés), a helyét a felderített szöveg és a pontszám:

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|Objektum|Entitás neve|Érték|
|--|--|--|
|Egyszerű entitás|`Customer`|`bob jones`|

## <a name="next-steps"></a>További lépések

Ebben az [oktatóanyagban](luis-quickstart-primary-and-secondary-data.md)az **egyszerű entitás**használatával kinyerheti a feldolgozói feladatokból származó, a vállalat által megtanult adatok nevét. A kinyerési pontosság növeléséhez adjon hozzá egy [kifejezést](luis-concept-feature.md) az egyszerű entitáshoz tartozó kifejezések listájához.