---
title: Figyelő Azure Event Grid üzenet kézbesítése
description: Útmutató Azure Event Grid üzenetek kézbesítésének figyeléséhez.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: spelluru
ms.openlocfilehash: b1035046cc3c3b6cd7bde895e2e779d1c966abe0
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74170011"
---
# <a name="monitor-event-grid-message-delivery"></a>Figyelő Event Grid üzenet kézbesítése 

Ez a cikk azt ismerteti, hogyan használható a portál az események kézbesítési állapotának megtekintéséhez.

Event Grid tartós kézbesítést biztosít. Minden egyes előfizetéshez legalább egyszer kézbesít minden üzenetet. Az eseményeket azonnal elküldi az egyes előfizetések regisztrált webhookjának. Ha egy webhook nem igazolja az esemény kézhezvételét az első kézbesítési kísérlet 60 másodpercén belül, Event Grid újrapróbálkozik az esemény kézbesítésével.

Eseménykézbesítés és újrapróbálkozás [Event Grid az üzenetek kézbesítését, és ismételje meg](delivery-and-retry.md).

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

* Eseménykézbesítés és újrapróbálkozás [Event Grid az üzenetek kézbesítését, és ismételje meg](delivery-and-retry.md).
* Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
* Tekintse meg a gyors kezdéshez Event Grid használatával [az Azure Event Griddel egyéni események létrehozása és útvonal](custom-event-quickstart.md).
