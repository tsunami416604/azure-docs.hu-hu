---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 2317e0b8bfe01f94989412db7c0c4560b2ca728f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124108"
---
A kimondott példaszövegek fájlja, az **utterances.json**, egy adott formátumot követ. 

A `text` mező a kimondott példaszövegeket tartalmazza. Az `intentName` mezőnek a LUIS-alkalmazásban található létező szándék nevének kell megfelelnie. Az `entityLabels` mező kötelező. Ha nem szeretne entitásokat megcímkézni, adjon meg egy üres tömböt.

Ha az entityLabels tömb nem üres, a `startCharIndex` és az `endCharIndex` indexnek az `entityName` mezőben hivatkozott entitást kell jelölnie. Az index nullaalapú, így a fenti példában a 6 a Seattle szó „S” betűjére hivatkozik, nem pedig a nagy S előtt található szóközre. Ha a címkét egy a szövegben található szóközön kezdi el vagy fejezi be, a kimondott szöveg hozzáadását célzó API-hívás sikertelen lesz.

```JSON
[
  {
    "text": "go to Seattle today",
    "intentName": "BookFlight",
    "entityLabels": [
      {
        "entityName": "Location::LocationTo",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "purple dogs are difficult to work with",
    "intentName": "BookFlight",
    "entityLabels": []
  }
]
```
