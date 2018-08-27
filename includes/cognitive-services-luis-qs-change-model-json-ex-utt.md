---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 419f15901b665b43b850922f77bd32d7aac8d3a2
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/24/2018
ms.locfileid: "42920467"
---
Beszédmódok példafájl **utterances.json**, egy adott formátumot követi. 

A `text` mező tartalmazza a példa utterance (kifejezés) szövegét. A `intentName` mezőben meg kell felelnie a LUIS alkalmazás egy meglévő leképezés neve. Az `entityLabels` mező kötelező. Ha nem szeretné a entitások címke, adja meg egy üres tömb.

A entityLabels tömb nem üres, ha a `startCharIndex` és `endCharIndex` kell megjelölni az entitást, amelyekre a `entityName` mező. Az index a nulláról induló, ami azt jelenti, az első példában 6 előtt a nagybetűs S. budapesti és egy szóközt nem, az "S" hivatkozik. Ha a megkezdéséhez, vagy a címke adható meg a szövegben a záró, az API-hívást a beszédmódok hozzáadása sikertelen lesz.

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