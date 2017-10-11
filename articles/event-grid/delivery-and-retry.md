---
title: "Az Azure Event rács kézbesítési, és próbálkozzon újra"
description: "Ismerteti, hogyan nyújt az Azure esemény rács a eseményeket, és hogyan kezeli az kézbesítetlen üzenetek."
services: event-grid
author: djrosanova
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/11/2017
ms.author: darosa
ms.openlocfilehash: e0f8afdfd84ea3c0c061459c27da285f6ae8957e
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="event-grid-message-delivery-and-retry"></a>Esemény rács üzenetkézbesítést, és próbálkozzon újra 

Ez a cikk ismerteti, miként kezeli az Azure esemény rács a kézbesítési nem vonatkozik, az eseményeket.

Esemény rács tartós kézbesítési biztosít. Minden üzenet legalább egyszer az egyes előfizetésekhez biztosítja. Az események küldhetők az egyes előfizetések regisztrált webhook azonnal. A webhook nem átvételét egy eseményt az első kézbesítési kísérlet 60 másodpercen belül, ha az esemény rács kézbesítési esemény újrapróbálkozik.

## <a name="message-delivery-status"></a>Üzenet kézbesítési állapota

Esemény rács események nyugtázta használja a HTTP válaszkódot. 

### <a name="success-codes"></a>Sikeres kódok

A következő HTTP-válaszkódot azt jelzi, hogy az esemény kézbesítése megtörtént sikeresen a webhook. Esemény rács úgy ítéli meg kézbesítési befejeződött.

- 200 OK
- 202 elfogadott

### <a name="failure-codes"></a>Hibát kódok

A következő HTTP-válaszkódot azt jelzi, hogy az esemény kézbesítési tett kísérlet meghiúsult. Esemény rács megpróbálja újra elküldeni az esemény. 

- 400 Hibás kérés
- 401 nem engedélyezett
- 404 – Nem található
- 408 kérelmi időkorlátot.
- 414 URI túl hosszú
- 500 belső kiszolgálóhiba
- 503-as szolgáltatás nem érhető el
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

Esemény rács intervallumokban újra egy kis véletlenszerű hozzáadja.

## <a name="retry-duration"></a>Ismételje meg az időtartama

Az előzetes Azure esemény rács összes eseményt, amely nem érkeznek meg két órán belül lejár. Általános rendelkezésre állását, mielőtt most 24 órára növekszik. 

## <a name="next-steps"></a>Következő lépések

* Esemény rácshoz ismertetőért lásd: [esemény rács](overview.md).
* Ha gyorsan esemény rács segítségével, lásd: [Azure esemény rácshoz hozza létre és útvonal egyéni események](custom-event-quickstart.md).