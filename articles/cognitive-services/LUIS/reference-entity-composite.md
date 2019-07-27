---
title: Összetett entitás típusa – LUIS
titleSuffix: Azure Cognitive Services
description: Az összetett entitások más entitásokból állnak, mint például az előre összeépített entitások, az egyszerű, a reguláris kifejezések és a listázási entitások. A különálló entitások teljes entitás űrlap.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: ea258275cf954bc6e06da03324c2ae93de0e7fde
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563242"
---
# <a name="composite-entity"></a>Összetett entitást 

Az összetett entitások más entitásokból állnak, mint például az előre összeépített entitások, az egyszerű, a reguláris kifejezések és a listázási entitások. A különálló entitások teljes entitás űrlap. 

**Ez az entitás jó illeszkedést biztosít az adattartalomhoz:**

* Kapcsolódnak egymáshoz. 
* Kapcsolódik egymáshoz a kimondott szövegkörnyezetben.
* Többféle típusú entitás használja.
* Az ügyfélalkalmazás adategységként kell csoportosítani és feldolgoznia.
* Különböző felhasználói hosszúságú kimondott szöveg kell rendelkeznie, amelyek gépi tanulást igényelnek.

![Összetett entitást](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Példa JSON-ban

Vegye fontolóra az előre elkészített `number` és `Location::ToLocation` a következő kifejezéssel rendelkező összetett entitást:

`book 2 tickets to paris`

Figyelje meg, hogy `2`, száma, és `paris`, a ToLocation van közöttük, amelyek nem részei a bármelyikével szavakat. A zöld, szerepel a címkézett utterance (kifejezés) az aláhúzás a [LUIS](luis-reference-regions.md) -webhely azt jelzi, hogy egy összetett entitást.

![Összetett entitást](./media/luis-concept-data-extraction/composite-entity.png)

A visszaadott összetett entitások egy `compositeEntities` is ad a tömb és az összetett lévő entitások a `entities` tömb:

```JSON

"entities": [
    {
    "entity": "2 tickets to cairo",
    "type": "ticketInfo",
    "startIndex": 0,
    "endIndex": 17,
    "score": 0.67200166
    },
    {
    "entity": "2",
    "type": "builtin.number",
    "startIndex": 0,
    "endIndex": 0,
    "resolution": {
        "subtype": "integer",
        "value": "2"
    }
    },
    {
    "entity": "cairo",
    "type": "builtin.geographyV2",
    "startIndex": 13,
    "endIndex": 17
    }
],
"compositeEntities": [
    {
    "parentType": "ticketInfo",
    "value": "2 tickets to cairo",
    "children": [
        {
        "type": "builtin.geographyV2",
        "value": "cairo"
        },
        {
        "type": "builtin.number",
        "value": "2"
        }
    ]
    }
]
```    

|Objektum|Entitás neve|Érték|
|--|--|--|
|Előre összeállított entitások - szám|"builtin.number"|"2"|
|Előre elkészített entitás – GeographyV2|"Location::ToLocation"|"Párizs"|

## <a name="next-steps"></a>További lépések

Ebben az [oktatóanyagban](luis-tutorial-composite-entity.md)egy **összetett entitást** ad hozzá, amellyel a különböző típusú kinyert adatmennyiségeket egyetlen tartalmazó entitásba csomagolhatja. A kötegelés az adatokat, az ügyfélalkalmazás is könnyen kinyerheti az különböző adattípusok kapcsolódó adatokat.
