---
title: Az Azure Event rács kézbesítési, és próbálkozzon újra
description: Ismerteti, hogyan nyújt az Azure esemény rács a eseményeket, és hogyan kezeli az kézbesítetlen üzenetek.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 8eb6717369b48289bd31dcd1972ce275bc550c77
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2018
---
# <a name="event-grid-message-delivery-and-retry"></a>Esemény rács üzenetkézbesítést, és próbálkozzon újra 

Ez a cikk ismerteti, miként kezeli az Azure esemény rács a kézbesítési nem vonatkozik, az eseményeket.

Esemény rács tartós kézbesítési biztosít. Minden üzenet legalább egyszer az egyes előfizetésekhez biztosítja. Az események küldhetők az egyes előfizetések regisztrált webhook azonnal. A webhook nem átvételét egy eseményt az első kézbesítési kísérlet 60 másodpercen belül, ha az esemény rács kézbesítési esemény újrapróbálkozik. 

Jelenleg esemény rács küld minden esemény külön-külön előfizetők. Az előfizető kap egy tömböt egy adott eseményhez.

## <a name="message-delivery-status"></a>Üzenet kézbesítési állapota

Esemény rács események nyugtázta használja a HTTP válaszkódot. 

### <a name="success-codes"></a>Sikeres kódok

A következő HTTP-válaszkódot azt jelzi, hogy az esemény kézbesítése megtörtént sikeresen a webhook. Esemény rács úgy ítéli meg kézbesítési befejeződött.

- 200 OK
- 202 elfogadott

### <a name="failure-codes"></a>Hibát kódok

A következő HTTP-válaszkódot azt jelzi, hogy az esemény kézbesítési tett kísérlet meghiúsult. 

- 400 Hibás kérés
- 401 nem engedélyezett
- 404 – Nem található
- 408 kérelmi időkorlátot.
- 414 URI túl hosszú
- 500 Belső kiszolgálóhiba
- 503 A szolgáltatás nem érhető el
- 504 Időtúllépés az átjárón

Esemény rács kap, amely jelzi, hogy a végpont nem érhető el hiba, ha megkísérli ismét elküldeni a eseményt. 

## <a name="retry-intervals-and-duration"></a>Ismételje meg a időközöket és időtartama

Esemény rács az exponenciális leállítási újrapróbálkozási házirend esemény kézbesítési használ. A webhook nem válaszol vagy hibakódot ad vissza, ha az esemény rács újrapróbálja kézbesítése a következő ütemezés szerint:

1. 10 másodperc
2. 30 másodperc
3. 1 perc
4. 5 perc
5. 10 perc
6. 30 perc
7. 1 óra

Esemény rács intervallumokban újra egy kis véletlenszerű hozzáadja. Egy óra múlva esemény kézbesítési a rendszer ismét megkísérli óránként.

Alapértelmezés szerint esemény rács összes eseményt, amely nem érkeznek meg 24 órán belül lejár.

## <a name="next-steps"></a>További lépések

* Esemény kézbesítések állapotának megtekintése: [figyelő esemény rács üzenetkézbesítést](monitor-event-delivery.md).
* Esemény rácshoz ismertetőért lásd: [esemény rács](overview.md).
* Ha gyorsan esemény rács segítségével, lásd: [Azure esemény rácshoz hozza létre és útvonal egyéni események](custom-event-quickstart.md).