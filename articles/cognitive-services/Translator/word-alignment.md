---
title: Szóigazítás - Fordító szöveg API
titleSuffix: Azure Cognitive Services
description: Az igazítási információk fogadásához használja a Fordítás metódust, és adja meg a választható includeAlignment paramétert.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: dd4ff1e39c062910f4627973c801dc3c51f345e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73837227"
---
# <a name="how-to-receive-word-alignment-information"></a>A szóigazítási információk fogadása

## <a name="receiving-word-alignment-information"></a>Szóigazítási információk fogadása
Az igazítási információk fogadásához használja a Fordítás metódust, és adja meg a választható includeAlignment paramétert.

## <a name="alignment-information-format"></a>Igazítási információs formátum
Az igazítás a forrás minden szavához a következő formátum karakterláncértékeként jelenik meg. Az egyes szavak adatait szóköz választja el egymástól, beleértve a nem szóköznel elválasztott nyelveket (parancsfájlokat is), például a kínainyelvet:

[[SourceTextStartIndex]:[Forrásszövegszövegezőindex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Példa igazítási karakterláncra: "0:0-7:10 1:2-11:20 3:4-0:3:4-4:6 5:5-21:21".

Más szóval a kettőspont elválasztja a kezdő és záró indexet, a kötőjel választja el a nyelveket, a szóköz pedig elválasztja a szavakat. Az egyik szó a másik nyelv ben a nullához, egy vagy több szóhoz igazodhat, és az igazított szavak nem lehetnek összefüggőek. Ha nem áll rendelkezésre igazítási információ, az Igazítás elem üres lesz. A metódus ebben az esetben nem ad vissza hibát.

## <a name="restrictions"></a>Korlátozások
A program ezen a ponton csak a nyelvpárok egy részhalmazához adja vissza az igazítást:
* az angoltól bármely más nyelvig;
* bármely más nyelvről angolra, kivéve a kínai egyszerűsített, a hagyományos kínait és a lettet angolra
* japánról koreaira vagy koreairól japánra Nem kap igazítási információt, ha a mondat konzervfordítás. A konzervfordításra példa a "Ez egy teszt", "Szeretlek", és más magas frekvenciájú mondatok.

## <a name="example"></a>Példa

Példa JSON

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj": "0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
