---
title: "Azure Event rács üzenetkézbesítést figyelése"
description: "Az Azure Event rács üzenetek kézbesítési figyelését ismerteti."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/10/2018
ms.author: tomfitz
ms.openlocfilehash: 1552174589e91c370c3b85a9af7b5cc1106cbb90
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2018
---
# <a name="monitor-event-grid-message-delivery"></a>Üzenet kézbesítési esemény rács figyelése 

A cikkből megtudhatja, hogyan használhatja a portált esemény kézbesítések állapotát tekintheti meg.

Esemény rács tartós kézbesítési biztosít. Minden üzenet legalább egyszer az egyes előfizetésekhez biztosítja. Az események küldhetők az egyes előfizetések regisztrált webhook azonnal. A webhook nem átvételét egy eseményt az első kézbesítési kísérlet 60 másodpercen belül, ha az esemény rács kézbesítési esemény újrapróbálkozik.

Esemény kézbesítési és, az újrapróbálkozásokat információt [esemény rács üzenetkézbesítést, és próbálkozzon újra](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Kézbesítési metrikák

A portál eseményüzenetei portál állapotának metrikákat jeleníti meg.

A témaköröket, az adatok gyűjtése le van:

* **Sikeres közzététel**: esemény sikeres volt a témakörbe küldött és dolgozza fel, 2xx választ.
* **Nem sikerült közzétenni**: az esemény a témakörbe küldött, de hibakódú elutasították.
* **Nem egyező**: esemény sikeresen közzétette a témakört, de nem felel meg az esemény-előfizetést. Az eseményt eldobta.

Az előfizetések, az adatok gyűjtése le van:

* **Kézbesítési sikeres**: esemény sikeresen kézbesítve lenne az előfizetés végpontot, és 2xx választ kapott.
* **Kézbesítési sikertelen**: esemény előfizetés végpont elküldte, de 4xx vagy 5xx választ kapott.
* **Események lejárt**: esemény nem lett kézbesítve, és minden újrapróbálkozások küldtek. Az eseményt eldobta.
* **Események egyező**: a következő témakör esemény megfelelt az esemény-előfizetést.

## <a name="event-subscription-status"></a>Előfizetési eseményállapot

Egy Eseményelőfizetés metrikáját, keressen **esemény rács előfizetések** a szolgáltatásokat, és válassza ki azt.

![Keresse meg az esemény-előfizetések](./media/monitor-event-delivery/select-event-subscriptions.png)

Szűrés milyen típusú eseményt, az előfizetésben és helyen. Válassza ki **metrikák** az előfizetés megtekintéséhez.

![Esemény-előfizetések szűrése](./media/monitor-event-delivery/filter-events.png)

Tekintse meg az esemény-témakör és az előfizetés metrikákat.

![Nézet esemény metrikák](./media/monitor-event-delivery/subscription-metrics.png)

## <a name="custom-event-status"></a>Egyéni esemény állapota

Egy egyéni témakör közzétételekor megtekintheti a metrikák azt. Válassza ki a témakör tartalmazó erőforráscsoportot, és válassza ki a következő témakörben.

![Válassza ki az egyéni témakör](./media/monitor-event-delivery/select-custom-topic.png)

Tekintse meg az egyéni esemény-témakör metrikáit.

![Nézet esemény metrikák](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="next-steps"></a>További lépések

* Esemény kézbesítési és, az újrapróbálkozásokat információt [esemény rács üzenetkézbesítést, és próbálkozzon újra](delivery-and-retry.md).
* Esemény rácshoz ismertetőért lásd: [esemény rács](overview.md).
* Ha gyorsan esemény rács segítségével, lásd: [Azure esemény rácshoz hozza létre és útvonal egyéni események](custom-event-quickstart.md).
