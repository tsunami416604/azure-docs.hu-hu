---
title: Azure Event rács üzenetkézbesítést figyelése
description: Az Azure Event rács üzenetek kézbesítési figyelését ismerteti.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: tomfitz
ms.openlocfilehash: 625f3e228bb28c85e68fb592914fb2191baf3e4e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626989"
---
# <a name="monitor-event-grid-message-delivery"></a>Üzenet kézbesítési esemény rács figyelése 

A cikkből megtudhatja, hogyan használhatja a portált esemény kézbesítések állapotát tekintheti meg.

Esemény rács tartós kézbesítési biztosít. Minden üzenet legalább egyszer az egyes előfizetésekhez biztosítja. Az események küldhetők az egyes előfizetések regisztrált webhook azonnal. A webhook nem nyugtázta egy eseményt az első kézbesítési kísérlet 60 másodpercen belül, ha az esemény rács kézbesítési esemény újrapróbálkozik.

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

Az esemény-előfizetésre metrikák megtekintéséhez vagy kereshet az előfizetési típust, vagy egy adott erőforrás előfizetések.

Előfizetés Eseménytípus szerint megkereséséhez válassza **minden szolgáltatás**.

![Válassza ki az összes szolgáltatás](./media/monitor-event-delivery/all-services.png)

Keresse meg **esemény rács** válassza **esemény rács előfizetések** az elérhető lehetőségek közül.

![Keresse meg az esemény-előfizetések](./media/monitor-event-delivery/search-and-select.png)

Szűrés milyen típusú eseményt, az előfizetésben és helyen. Válassza ki **metrikák** az előfizetés megtekintéséhez.

![Esemény-előfizetések szűrése](./media/monitor-event-delivery/filter-events.png)

Tekintse meg az esemény-témakör és az előfizetés metrikákat.

![Nézet esemény metrikák](./media/monitor-event-delivery/subscription-metrics.png)

A metrikák egy adott erőforrás megkereséséhez válassza ki az adott erőforrás. Ezt követően válassza **események**.

![Válassza ki az erőforrás eseményei](./media/monitor-event-delivery/select-events.png)

Megjelenik a metrikák elő az adott erőforráshoz.

## <a name="custom-event-status"></a>Egyéni esemény állapota

Ha közzétett egy egyéni témakör, megtekintheti a metrikák azt. Válassza ki az erőforráscsoportot a témakörhöz, és válassza ki a következő témakörben.

![Válassza ki az egyéni témakör](./media/monitor-event-delivery/select-custom-topic.png)

Tekintse meg az egyéni esemény-témakör metrikáit.

![Nézet esemény metrikák](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="next-steps"></a>További lépések

* Esemény kézbesítési és, az újrapróbálkozásokat információt [esemény rács üzenetkézbesítést, és próbálkozzon újra](delivery-and-retry.md).
* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Ha gyorsan esemény rács segítségével, lásd: [Azure esemény rácshoz hozza létre és útvonal egyéni események](custom-event-quickstart.md).
