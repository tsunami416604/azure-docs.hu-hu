---
title: Aktív tanulási javaslatok - QnA Maker
description: Az aktív tanulási javaslatok lehetővé teszik a tudásbázis minőségének javítását azáltal, hogy a felhasználó által beküldött anyagokon alapuló alternatív kérdéseket javasolanak a kérdés-válasz párra.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: edbe06b12fbb97473b28ccca968fd3e7d8366152
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804216"
---
# <a name="active-learning-suggestions"></a>Aktív tanulási javaslatok

_Az Aktív tanulási javaslatok_ funkció lehetővé teszi a tudásbázis minőségének javítását azáltal, hogy a felhasználó által beküldött anyagok on-the-beküldésealapján alternatív kérdéseket javasol a kérdés-válasz párra. Ezeket a javaslatokat áttekintheti, vagy hozzáadja őket a meglévő kérdésekhez, vagy elutasítja azokat.

A tudásbázis nem változik automatikusan. Ahhoz, hogy bármilyen változás érvénybe lépjen, el kell fogadnia a javaslatokat. Ezek a javaslatok kérdéseket vetnek fel, de nem módosítják és nem távolítják el a meglévő ket.

## <a name="what-is-active-learning"></a>Mi az aktív tanulás?

A QnA Maker új kérdésváltozatokat tanul implicit és explicit visszajelzéssel.

* [Implicit visszajelzés](#how-qna-makers-implicit-feedback-works) – A ranker megérti, ha egy felhasználó kérdés több választ pontszámok, amelyek nagyon közel vannak, és úgy véli, ez a visszajelzés. Nem kell semmit sem tenned, hogy ez megtörténjen.
* [Explicit visszajelzés](#how-you-give-explicit-feedback-with-the-train-api) – Ha több választ ad vissza a tudásbázisból, az ügyfélalkalmazás megkérdezi a felhasználót, hogy melyik kérdés a helyes kérdés. A felhasználó explicit visszajelzést küld a QnA Maker a [Train API.](../How-to/improve-knowledge-base.md#train-api)

Mindkét módszer biztosítja a ranker hasonló lekérdezések, amelyek fürtözött.

## <a name="how-active-learning-works"></a>Hogyan működik az aktív tanulás?

Az aktív tanulás a QnA Maker által visszaadott néhány legjobb válasz pontszáma alapján aktiválódik. Ha a lekérdezésnek megfelelő QnA-párok közötti pontszámkülönbségek egy kis tartományon belül vannak, akkor a lekérdezés lehetséges javaslatnak minősül (alternatív kérdésként) a lehetséges QnA párok mindegyikére vonatkozóan. Miután elfogadta ddikta fel a javasolt kérdést egy adott QnA párhoz, a többi pár esetében elutasítjuk. Meg kell emlékezni, hogy mentse és a vonat, elfogadása után javaslatokat.

Az aktív tanulás a lehető legjobb javaslatokat adja azokban az esetekben, amikor a végpontok ésszerű mennyiséget és különböző használati lekérdezéseket kapnak. Ha 5 vagy több hasonló lekérdezés van csoportosítva, 30 percenként a QnA Maker azt javasolja, hogy a felhasználó-alapú kérdéseket a tudásbázis tervezője fogadja el vagy utasítsa el. Az összes javaslat a hasonlóság alapján csoportosítva van csoportosítva, és az alternatív kérdésekre vonatkozó leggyakoribb javaslatok a végfelhasználók által leadott lekérdezések gyakorisága alapján jelennek meg.

Amint a QnA Maker portálon kérdéseket javasolnak, át kell tekintenie és el kell fogadnia vagy el kell utasítania ezeket a javaslatokat. Nincs API a javaslatok kezelésére.

## <a name="turn-on-active-learning"></a>Az aktív tanulás bekapcsolása

Alapértelmezés szerint az aktív tanulás ki van **kapcsolva.**
Aktív tanulás használata:
* Be kell [kapcsolnia az aktív tanulást,](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions) hogy a QnA Maker alternatív kérdéseket gyűjtsön a tudásbázisához.
* A javasolt alternatív kérdések megtekintéséhez használja a Szerkesztés lap [Nézet beállításait.](../How-To/improve-knowledge-base.md#view-suggested-questions)

## <a name="how-qna-makers-implicit-feedback-works"></a>A QnA Maker implicit visszajelzései működése

A QnA Maker implicit visszajelzése egy algoritmust használ a pontszám közelségének meghatározására, majd aktív tanulási javaslatokat tesz. A közelség meghatározására algoritmus nem egyszerű számítás. A következő példában található tartományok nem javítandóak, hanem útmutatóként kell használni őket, hogy megértsük az algoritmus hatását.

Ha egy kérdés pontszáma nagyon magabiztos, például 80%, az aktív tanuláshoz figyelembe vett pontszámok tartománya széles, körülbelül 10% -on belül. Mivel a megbízhatósági pontszám csökken, mint például 40%, a pontszámok tartománya is csökken, körülbelül 4% -on belül.

A következő JSON-válasz a QnA Maker generateAnswer lekérdezésében az A, B és C pontszámok közel vannak, és javaslatoknak minősülnek.

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA Maker nem fogja tudni, melyik válasz a legjobb válasz. Használja a QnA Maker portál javaslatlistáját, hogy válassza ki a legjobb választ, és újra betanítása.


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>Hogyan adhat kifejezett visszajelzést a Train API-val?

A QnA Maker-nek kifejezett visszajelzésre van szüksége arról, hogy melyik válasz volt a legjobb válasz. A legjobb válasz meghatározása csak Önön múlik, és a következőket tartalmazhatja:

* Felhasználói visszajelzés, kiválasztja az egyik választ.
* Üzleti logika, például egy elfogadható pontszámtartomány meghatározása.
* A felhasználói visszajelzések és az üzleti logika kombinációja.

A [Betanítási API-val](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train) küldje el a helyes választ a QnA Makernek, miután a felhasználó kiválasztotta.

## <a name="next-step"></a>Következő lépés

> [!div class="nextstepaction"]
> [A tudásbázis lekérdezése](query-knowledge-base.md)