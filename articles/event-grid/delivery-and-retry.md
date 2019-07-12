---
title: Az Azure Event Grid teljesítés és újrapróbálkozás
description: Azure Event Grid piacról eseményeket, és hogyan kezeli az kézbesítetlen üzenetek ismerteti.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: spelluru
ms.openlocfilehash: 0945b06f78ac34500f0b16a4a419cff12d1a4734
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812924"
---
# <a name="event-grid-message-delivery-and-retry"></a>Event Grid az üzenetek kézbesítését, és próbálkozzon újra

Ez a cikk bemutatja, hogyan kezeli az Azure Event Grid a eseményeket, amikor a kézbesítési arra nem vonatkozik.

Event Grid biztosít a tartós kézbesítési. Minden üzenet legalább egyszer minden egyes előfizetés esetén kínál. Az események küldhetők a regisztrált végpont-előfizetések azonnal. A végpont nem igazolhatom egy eseményt, ha az Event Grid kézbesítési esemény újrapróbálkozik.

Jelenleg Event Grid küld minden esemény külön-külön előfizetők számára. Az előfizető egy tömböt egyetlen eseményt kap.

## <a name="retry-schedule-and-duration"></a>Ismételje meg az ütemezés és időtartama

Event Grid egy üzenet kézbesítése után választ 30 másodpercet vár. 30 másodperc Ha a végpont nem válaszolt, az üzenetet a rendszer sorba állítja próbálkozzon újra. Event Grid egy exponenciális leállítási újrapróbálkozási házirend eseménykézbesítés használ. Event Grid újrapróbálkozik kézbesítési az elérhető legjobb lehetőség alapján a következő ütemezés szerint:

- 10 másodperc
- 30 másodperc
- 1 perc
- 5 perc
- 10 perc
- 30 perc
- 1 óra
- Akár 24 órát a óránként

Ha a végpont 3 percen belül válaszol, Event Grid megkísérli az esemény eltávolítása az újrapróbálkozási várólista az elérhető legjobb lehetőség alapján, de ismétlődő továbbra is lehetséges, hogy kapott.

Event Grid egy kis véletlenszerű ad hozzá minden újrapróbálkozási lépést, és kulcsmodulonként hagyhat bizonyos újrapróbálkozásokat, ha a végpont konzisztens módon sérült, le hosszabb ideig, vagy úgy tűnik, hogy kihasznált.

Determinisztikus viselkedését, állítsa az élő esemény időpontját és a maximális kézbesítési kísérletek a [előfizetés újrapróbálkozási szabályzatok](manage-event-delivery.md).

Alapértelmezés szerint az Event Grid összes eseményt, amely nem biztosított 24 órán belül lejár. Is [testre szabhatja az újrapróbálkozási szabályzat](manage-event-delivery.md) egy esemény-előfizetés létrehozásakor. Biztosítanak a maximális számát (alapértelmezés: 30) a kézbesítési kísérletek és az esemény élő idő (1440 perc az alapértelmezett érték).

## <a name="delayed-delivery"></a>Késleltetett kézbesítés

A végpont során kézbesítési hiba lép fel, mert Event Grid megkezdik a szállítási és az eseményeket, hogy a végpont újrapróbálkozási késleltetés. Például, ha meghibásodik egy végpontnak közzétett első tíz események, Event Grid feltételezi, hogy a végpont problémák, és minden későbbi próbálkozások késleltetni fogja *és az új* egy kis ideig – bizonyos esetekben akár néhány órát kézbesítések .

Késleltetett kézbesítési működési célja, hogy nem megfelelő állapotú végpontok, valamint az Event Grid rendszer védelmét. Visszatartás, és nem megfelelő állapotú végpontok szállítási késedelem nélkül Event Grid újrapróbálkozási szabályzat és a kötet képességeket is könnyen túlterhelheti a rendszer.

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

Event Grid tekinti **csak** a következő HTTP-válaszkódot, a sikeres kézbesítések. Minden más kódok sikertelen kézbesítések minősülnek, és megpróbálja állapota vagy deadlettered szükség szerint. Sikeres állapotkódot fogadásakor Event Grid tekinti kézbesítési befejeződött.

- 200 OK
- 201 Created
- 202-es elfogadva
- 203 nem mérvadó információ
- 204 Nincs tartalom

### <a name="failure-codes"></a>Sikertelen kód

A fenti halmazon (200-204) minden más kód tekinti a hibákat, és újból próbálkozunk. Néhány-e őket az alábbi kötött adott újrapróbálkozási szabályzatok, minden más hajtsa végre a szabványos exponenciális visszatartási modell. Fontos figyelembe kell venni, hogy Event Grid-architektúra nagymértékben párhuzamos jellege miatt az újrapróbálkozási viselkedés nem determinisztikus. 

| Állapotkód | Újrapróbálkozási viselkedés |
| ------------|----------------|
| 400 Hibás kérés | Akár 5 percet újrapróbálkozás (kézbesítetlen Ha azonnal kézbesítetlen beállítása) |
| 401-es nem engedélyezett | 5 perc múlva próbálkozzon újra, vagy több |
| 403 Tiltott | 5 perc múlva próbálkozzon újra, vagy több |
| 404 – Nem található | 5 perc múlva próbálkozzon újra, vagy több |
| 408 Kérés időtúllépése | 2 perc múlva próbálkozzon újra, vagy több |
| 413 kérelem az entitás túl nagy | Újrapróbálkozás 10 másodperc vagy annál (kézbesítetlen Ha azonnal kézbesítetlen beállítása) |
| 503 A szolgáltatás nem érhető el | 30 másodperc múlva újra, vagy több |
| Minden más | 10 másodperc után próbálja meg újból, vagy több |


## <a name="next-steps"></a>További lépések

* Esemény kézbesítések állapotának megtekintése: [figyelő Event Grid üzenetkézbesítése](monitor-event-delivery.md).
* Esemény a Kézbesítési beállítások testreszabásához tekintse meg a [kapcsolat megszakadásának betűs, és ismételje meg a házirendek](manage-event-delivery.md).
