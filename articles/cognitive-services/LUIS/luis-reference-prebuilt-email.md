---
title: Luis előre összeállított entitások e-mail hivatkozás
titleSuffix: Azure Cognitive Services
description: Ez a cikk az előre összeállított entitásadatokat tartalmazza a nyelvi ismeretek (LUIS) című témakörben.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: f7aa2a341a82d30f7022af49e626a3cd358854b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273483"
---
# <a name="email-prebuilt-entity-for-a-luis-app"></a>Előre összeállított e-mail entitás egy LUIS-alkalmazáshoz
E-mail kinyerése tartalmazza a teljes e-mail címet egy utterance (kifejezés). Mivel ez az entitás már be van tanítva, nem kell hozzáadnia az e-maileket tartalmazó példautterances az alkalmazás leképezések. Az e-mail `en-us` entitás csak a kulturális környezetben támogatott.

## <a name="resolution-for-prebuilt-email"></a>Felbontás előre összeállított e-mailekhez

A következő entitásobjektumok at adják vissza a lekérdezéshez:

`please send the information to patti@contoso.com`

#### <a name="v3-response"></a>[V3 válasz](#tab/V3)

A következő JSON `verbose` paraméter a `false`következő:

```json
"entities": {
    "email": [
        "patti@contoso.com"
    ]
}
```
#### <a name="v3-verbose-response"></a>[V3 részletes válasz](#tab/V3-verbose)

A következő JSON `verbose` paraméter a `true`következő:

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
#### <a name="v2-response"></a>[V2 válasz](#tab/V2)

A következő példa a **builtin.email** entitás felbontását mutatja be.

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

További információ a [V3 előrejelzési végpontjáról.](luis-migration-api-v3.md)

További információ a [számról](luis-reference-prebuilt-number.md), [a számról](luis-reference-prebuilt-ordinal.md)és a [százalékos értékről](luis-reference-prebuilt-percentage.md).
