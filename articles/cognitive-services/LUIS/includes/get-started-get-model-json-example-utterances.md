---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 10/19/2019
ms.author: diberry
ms.openlocfilehash: ba400beb13d0bd4b29eee0c60617ec7ad128dd77
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503737"
---
A példa hosszúságú kimondott szöveg egy adott formátumot követ. 

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
