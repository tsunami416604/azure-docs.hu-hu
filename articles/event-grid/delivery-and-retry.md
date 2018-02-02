---
title: "Az Azure Event rács kézbesítési, és próbálkozzon újra"
description: "Ismerteti, hogyan nyújt az Azure esemény rács a eseményeket, és hogyan kezeli az kézbesítetlen üzenetek."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: cdf6a4e999d55196e8f4eac5695163a7e5a933de
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="event-grid-message-delivery-and-retry"></a>Esemény rács üzenetkézbesítést, és próbálkozzon újra 

Ez a cikk ismerteti, miként kezeli az Azure esemény rács a kézbesítési nem vonatkozik, az eseményeket.

Esemény rács tartós kézbesítési biztosít. Minden üzenet legalább egyszer az egyes előfizetésekhez biztosítja. Az események küldhetők az egyes előfizetések regisztrált webhook azonnal. A webhook nem átvételét egy eseményt az első kézbesítési kísérlet 60 másodpercen belül, ha az esemény rács kézbesítési esemény újrapróbálkozik.

## <a name="message-delivery-status"></a>Üzenet kézbesítési állapota

Esemény rács események nyugtázta használja a HTTP válaszkódot. 

### <a name="success-codes"></a>Sikeres kódok

A következő HTTP-válaszkódot azt jelzi, hogy az esemény kézbesítése megtörtént sikeresen a webhook. Esemény rács úgy ítéli meg kézbesítési befejeződött.

- 200 OK
- 202 Accepted

### <a name="failure-codes"></a>Hibát kódok

A következő HTTP-válaszkódot azt jelzi, hogy az esemény kézbesítési tett kísérlet meghiúsult. Esemény rács megpróbálja újra elküldeni az esemény. 

- 400 Hibás kérés
- 401 nem engedélyezett
- 404 – Nem található
- 408 kérelmi időkorlátot.
- 414 URI túl hosszú
- 500 Internal Server Error
- 503 Service Unavailable
- 504-es számú átjáró időtúllépése

Bármely más válaszkód vagy hiányoznak a választ egy hibát jelez. Esemény rács kézbesítési próbálkozások. 

## <a name="retry-intervals"></a>Újrapróbálkozási időközök

Esemény rács az exponenciális leállítási újrapróbálkozási házirend esemény kézbesítési használ. A webhook nem válaszol vagy hibakódot ad vissza, ha az esemény rács újrapróbálja kézbesítése a következő ütemezés szerint:

1. 10 másodperc
2. 30 másodperc
3. 1 perc
4. 5 perc
5. 10 perc
6. 30 perc
7. 1 óra

Esemény rács intervallumokban újra egy kis véletlenszerű hozzáadja. Egy óra múlva esemény kézbesítési a rendszer ismét megkísérli óránként.

## <a name="retry-duration"></a>Ismételje meg az időtartama

Azure esemény rács összes eseményt, amely nem érkeznek meg 24 órán belül lejár.

## <a name="next-steps"></a>További lépések

* Esemény kézbesítések állapotának megtekintése: [figyelő esemény rács üzenetkézbesítést](monitor-event-delivery.md).
* Esemény rácshoz ismertetőért lásd: [esemény rács](overview.md).
* Ha gyorsan esemény rács segítségével, lásd: [Azure esemény rácshoz hozza létre és útvonal egyéni események](custom-event-quickstart.md).