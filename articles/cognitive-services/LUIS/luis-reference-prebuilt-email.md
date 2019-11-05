---
title: LUIS előre elkészített entitások e-mail-referenciája
titleSuffix: Azure Cognitive Services
description: Ez a cikk a Language Understanding (LUIS) e-mailes előkészített entitás-információit tartalmazza.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 6f262752a50b58eae8ffbea81b8e7fc4d8c65b98
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464979"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>E-mail-előkészített entitás egy LUIS-alkalmazáshoz
Az e-mailek kinyerése magában foglalja a teljes e-mail-címet. Mivel ez az entitás már be van tanítva, nem kell felvennie például az hosszúságú kimondott szöveg tartalmazó e-mailt. Az e-mail-entitás csak `en-us` kulturális környezetekben támogatott. 

## <a name="resolution-for-prebuilt-email"></a>Előre elkészített e-mailek feloldása

A lekérdezés a következő entitás-objektumokat adja vissza:

`please send the information to patti@contoso.com`

#### <a name="v3-responsetabv3"></a>[V3 válasz](#tab/V3)

A következő JSON a `verbose` paraméterrel van beállítva `false`:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-responsetabv3-verbose"></a>[V3 részletes válasz](#tab/V3-verbose)

A következő JSON a `verbose` paraméterrel van beállítva `true`:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ],
    "$instance": {
        "email": [
            {
                "type": "builtin.email",
                "text": "patti@contoso.com",
                "startIndex": 31,
                "length": 17,
                "modelTypeId": 2,
                "modelType": "Prebuilt Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```
#### <a name="v2-responsetabv2"></a>[V2 válasz](#tab/V2)

A következő példa a **beépített. e-mail-** entitás feloldását mutatja be.

```json
"entities": [
    {
        "entity": "patti@contoso.com",
        "type": "builtin.email",
        "startIndex": 31,
        "endIndex": 55,
        "resolution": {
        "value": "patti@contoso.com"
        }
    }
]
```
* * * 

## <a name="next-steps"></a>További lépések

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

A [szám](luis-reference-prebuilt-number.md), a [sorszám](luis-reference-prebuilt-ordinal.md)és a [százalék](luis-reference-prebuilt-percentage.md)megismerése. 
