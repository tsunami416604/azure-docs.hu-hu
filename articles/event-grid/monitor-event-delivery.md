---
title: Figyelő Azure Event Grid üzenet kézbesítése
description: Ez a cikk azt ismerteti, hogyan használható a Azure Portal az Azure Event Grid üzenetek kézbesítési állapotának megtekintéséhez.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 16587feaca65aa21836d9be1c44e00faa0f4f8d8
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722135"
---
# <a name="monitor-event-grid-message-delivery"></a>Figyelő Event Grid üzenet kézbesítése 

Ez a cikk azt ismerteti, hogyan használható a portál az események kézbesítési állapotának megtekintéséhez.

Event Grid tartós kézbesítést biztosít. Minden egyes előfizetéshez legalább egyszer kézbesít minden üzenetet. Az eseményeket azonnal elküldi az egyes előfizetések regisztrált webhookjának. Ha egy webhook nem igazolja az esemény kézhezvételét az első kézbesítési kísérlet 60 másodpercén belül, Event Grid újrapróbálkozik az esemény kézbesítésével.

További információ az események kézbesítéséről és újrapróbálkozásáról, [Event Grid az üzenetek kézbesítéséről, és próbálkozzon újra](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Kézbesítési metrikák

A portál megjeleníti az események kézbesítésének állapotára vonatkozó metrikákat.

Témakörök esetén a metrikák a következők:

* **Sikeres közzététel**: az esemény sikeresen elküldött a témakörbe, és 2xx-válaszsal lett feldolgozva.
* A **Közzététel sikertelen**: az esemény a témakörbe lett küldve, de hibakódtal elutasította.
* Nem **egyező**: az esemény sikeresen közzé lett téve a témakörben, de nem felel meg az esemény-előfizetésnek. Az esemény el lett dobva.

Az előfizetések esetében a metrikák a következők:

* **Sikeres kézbesítés**: az esemény sikeresen kézbesítve lett az előfizetés végpontjának, és 2xx választ kapott.
* A **kézbesítés sikertelen volt**: az előfizetés végpontjának küldött esemény, de 4xx vagy 5xx-válasz érkezett.
* **Lejárt események**: az esemény nem lett kézbesítve, és az összes újrapróbálkozási kísérlet elküldése megtörtént. Az esemény el lett dobva.
* **Egyeztetett események**: az esemény-előfizetés megfelelt a témakörben szereplő eseménynek.

## <a name="event-subscription-status"></a>Esemény-előfizetés állapota

Ha szeretné megtekinteni az esemény-előfizetés mérőszámait, kereshet az előfizetés típusa vagy egy adott erőforrás előfizetése alapján.

Ha az esemény-előfizetés típusa szerint szeretne keresni, válassza a **minden szolgáltatás**lehetőséget.

![Válassza ki az összes szolgáltatás](./media/monitor-event-delivery/all-services.png)

Keressen az **Event Grid** kifejezésre, és válassza ki **Event Grid előfizetéseket** az elérhető lehetőségek közül.

![Esemény-előfizetések keresése](./media/monitor-event-delivery/search-and-select.png)

Szűrés az esemény típusa, az előfizetés és a hely alapján. Válassza ki a megtekinteni kívánt előfizetéshez tartozó **metrikákat** .

![Esemény-előfizetések szűrése](./media/monitor-event-delivery/filter-events.png)

Tekintse meg az esemény témakörének és előfizetésének mérőszámait.

![Esemény metrikáinak megtekintése](./media/monitor-event-delivery/subscription-metrics.png)

Egy adott erőforrás metrikáinak megkereséséhez válassza ki az erőforrást. Ezután válassza az **események**lehetőséget.

![Erőforrások eseményeinek kiválasztása](./media/monitor-event-delivery/select-events.png)

Megjelenik az adott erőforráshoz tartozó előfizetések metrikái.

## <a name="custom-event-status"></a>Egyéni esemény állapota

Ha közzétett egy egyéni témakört, megtekintheti a metrikákat. Válassza ki a témakörhöz tartozó erőforráscsoportot, és válassza ki a témakört.

![Egyéni témakör kiválasztása](./media/monitor-event-delivery/select-custom-topic.png)

Tekintse meg az egyéni esemény témakör mérőszámait.

![Esemény metrikáinak megtekintése](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="set-alerts"></a>Riasztások beállítása

Megadhatja a riasztásokat a témakörben és a tartományi szintű mérőszámokban az egyéni témakörökhöz és az esemény-tartományokhoz. Az Áttekintés panelen válassza a bal oldali erőforrás menü **riasztások** elemét a riasztási szabályok megtekintéséhez, kezeléséhez és létrehozásához. [További információ a Azure Monitor riasztásokról](../azure-monitor/platform/alerts-overview.md)

![Esemény metrikáinak megtekintése](./media/monitor-event-delivery/select-alerts.png)

## <a name="next-steps"></a>Következő lépések

* További információ az események kézbesítéséről és újrapróbálkozásáról, [Event Grid az üzenetek kézbesítéséről, és próbálkozzon újra](delivery-and-retry.md).
* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Az Event Grid használatának gyors megkezdéséhez tekintse meg [az egyéni események létrehozása és irányítása Azure Event Grid](custom-event-quickstart.md)használatával című témakört.
