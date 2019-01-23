---
title: Az Azure Event Grid teljesítés és újrapróbálkozás
description: Azure Event Grid piacról eseményeket, és hogyan kezeli az kézbesítetlen üzenetek ismerteti.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/01/2019
ms.author: spelluru
ms.openlocfilehash: b69215a76b332db9b994827705d6bbc3b48af5c8
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54465513"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid az üzenetek kézbesítését, és próbálkozzon újra

Ez a cikk bemutatja, hogyan kezeli az Azure Event Grid a eseményeket, amikor a kézbesítési arra nem vonatkozik.

Event Grid biztosít a tartós kézbesítési. Minden üzenet legalább egyszer minden egyes előfizetés esetén kínál. Az események küldhetők a regisztrált végpont-előfizetések azonnal. A végpont nem igazolhatom egy eseményt, ha az Event Grid kézbesítési esemény újrapróbálkozik.

Jelenleg Event Grid küld minden esemény külön-külön előfizetők számára. Az előfizető egy tömböt egyetlen eseményt kap.

## <a name="retry-schedule-and-duration"></a>Ismételje meg az ütemezés és időtartama

Event Grid egy exponenciális leállítási újrapróbálkozási házirend eseménykézbesítés használ. A végpont nem válaszol, vagy esetén hibakódot ad vissza, ha az Event Grid újrapróbálkozik kézbesítési a következő ütemezés szerint:

1. 10 másodperc
2. 30 másodperc
3. 1 perc
4. 5 perc
5. 10 perc
6. 30 perc
7. 1 óra

Event Grid ad hozzá egy kis véletlenszerű újrapróbálkozási lépéseket. Egy óra elteltével eseménykézbesítés rendszer óránként egyszer.

Alapértelmezés szerint az Event Grid összes eseményt, amely nem biztosított 24 órán belül lejár. Is [testre szabhatja az újrapróbálkozási szabályzat](manage-event-delivery.md) egy esemény-előfizetés létrehozásakor. Biztosítanak a maximális számát (alapértelmezés: 30) a kézbesítési kísérletek és az esemény élő idő (1440 perc az alapértelmezett érték).

## <a name="dead-letter-events"></a>Kézbesítetlen üzenetek esemény

Event Grid nem lehet kézbesíteni az egy eseményt, amikor azokat küldeni tudná, a kézbesítetlen esemény egy tárfiókba. Ez a folyamat az úgynevezett kézbesítetlen levelek kezelése. Alapértelmezés szerint a nem az Event Grid kapcsolja be a kézbesítetlen levelek kezelése. Az engedélyezéshez, egy storage-fiókot, amely tárolja a kézbesítetlen események az esemény-előfizetés létrehozásakor adjon meg. Ezt a tárfiókot, a szállítások megoldásához események lekéri.

Event Grid egy eseményt, amikor megpróbálta összes az újrapróbálkozási kísérletek a kézbesíthetetlen levelek helyre küldi. Event Grid egy 400 (hibás kérés) vagy a 413 (kérelem túl nagy) válaszkódot kap, ha elküldi a megfelelő az esemény azonnal a kézbesíthetetlen levelek végpontot. Ezek válaszkódot adja meg kézbesítési esemény soha nem fog sikerülni.

Öt perces késleltetés van, hogy egy eseményt, és ha biztosítását a kézbesíthetetlen levelek helyre a legutóbbi kísérlet között. Ez a késleltetés csökkentése érdekében a Blob storage művelet célja. A kézbesíthetetlen levelek hely 4 órán keresztül nem érhető el, ha az eseményt a rendszer eldobja.

A kézbesíthetetlen levelek helyének beállítása, mielőtt egy tároló tárfiók kell rendelkeznie. A végpont megadása a tároló az esemény-előfizetés létrehozásakor. A végpont formátuma van: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Érdemes egy eseményt a kézbesítetlen levelek helyre küldött értesítést küldünk. Event Grid használatára kézbesítetlen válaszadására [esemény-előfizetés létrehozása](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) a kézbesíthetetlen levelek a blob Storage. Minden alkalommal, amikor a kézbesíthetetlen levelek blob storage-kézbesítetlen eseményt kap, az Event Grid értesíti a kezelő. A kezelő műveletekhez tartozó kézbesítetlen események összeegyezteti szeretné válaszol.

Kézbesítetlen levelek hely beállítása egy példa: [kapcsolat megszakadásának betűs, és ismételje meg a házirendek](manage-event-delivery.md).

## <a name="message-delivery-status"></a>Üzenet kézbesítési állapota

Event Grid igazolhatom az események HTTP-válaszkódot használ. 

### <a name="success-codes"></a>Sikeres kód

A következő HTTP-válaszkódot adja meg, hogy az esemény rendelkezik lett sikeresen kézbesítve, a webhook. Event Grid úgy ítéli meg kézbesítési befejeződött.

- 200 OK
- 202-es elfogadva

### <a name="failure-codes"></a>Sikertelen kód

A következő HTTP-válaszkódot adja meg, hogy az esemény kézbesítési kísérlet meghiúsult.

- 400 Hibás kérés
- 401-es nem engedélyezett
- 404 – Nem található
- 408 kérés időtúllépése
- 413 kérelem az entitás túl nagy
- 414 URI túl hosszú
- 429 túl sok kérelem
- 500 Belső kiszolgálóhiba
- 503 A szolgáltatás nem érhető el
- 504 Időtúllépés az átjárón

## <a name="next-steps"></a>További lépések

* Esemény kézbesítések állapotának megtekintése: [figyelő Event Grid üzenetkézbesítése](monitor-event-delivery.md).
* Esemény a Kézbesítési beállítások testreszabásához tekintse meg a [kapcsolat megszakadásának betűs, és ismételje meg a házirendek](manage-event-delivery.md).