---
title: Word-igazítás információkat a Microsoft Translator szöveg API-val |} Microsoft Docs
description: A Microsoft Translator szöveg API word igazítási adatok fogadására.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 9d8284db61235284ec0d5a1594c34687c89560e9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347690"
---
# <a name="how-to-receive-word-alignment-information"></a>Szeretne kapni a word igazítás információk

## <a name="receiving-word-alignment-information"></a>Word igazítás információk fogadása
Igazítás információk fogadását, a fordítás módszert használja, és adja meg a választható includeAlignment értékét.

## <a name="alignment-information-format"></a>Igazítás információ formátuma
Igazítás adja vissza a rendszer a következő formátumban, az összes szó a forrás-karakterlánc. Minden szó információi van szóközzel elválasztva, beleértve a nem szóközökkel elválasztott nyelvek (parancsfájlok) például a kínai:

[[SourceTextStartIndex]: [SourceTextEndIndex] – [TgtTextStartIndex]: [TgtTextEndIndex]] *

Példa igazítás karakterlánc: "0:0-7:10 1:2 – 11:20 3:4 – 0:3, 3:4 – 4:6 5:5 – 21:21".

Ez azt jelenti a kettőspont elválasztja start end index, a kötőjel elválasztja a nyelveket, és szóköz a szavakat. Egy word előfordulhat, hogy megfelel-e nulla, egy vagy több szavak más nyelven, és lehet, hogy a igazított szavak nem összefüggő. Nincs igazítás információ nem érhető el, a igazítás elem üres lesz. A metódus visszaadja ebben az esetben nem történt hiba.

## <a name="restrictions"></a>Korlátozások
Igazítás csak akkor kapunk a nyelvi párok egy része számára ezen a ponton:
* az olyan egyéb nyelvek; angol
* angol, egyszerűsített kínai, hagyományos kínai, és lett angol kivételével bármely más nyelven történő
* Japán, koreai történő, illetve a japán, koreai nem kap igazítás információkat a mondatok előre összeállított fordítás esetén. Példa egy előre összeállított fordítás: "Ez egy tesztművelet", "Gyönyörű,", és egyéb nagy sűrűségű mondatokat.

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
