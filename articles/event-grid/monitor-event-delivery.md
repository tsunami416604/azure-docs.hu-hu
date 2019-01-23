---
title: Azure Event Grid üzenetkézbesítése figyelése
description: Az Azure Event Grid üzenetek kézbesítésének felügyeletének módját ismerteti.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: spelluru
ms.openlocfilehash: fdd18b833794c25cb90188ba8bc418d4785492ba
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54464808"
---
# <a name="monitor-event-grid-message-delivery"></a>Event Grid üzenetkézbesítése figyelése 

Ez a cikk ismerteti a portál használatával esemény kézbesítések állapotának megtekintéséhez.

Event Grid biztosít a tartós kézbesítési. Minden üzenet legalább egyszer minden egyes előfizetés esetén kínál. Az események küldhetők a regisztrált webhook-előfizetések azonnal. Ha a webhook nem igazolhatom egy eseményt az első kézbesítési kísérlet 60 másodpercen belül, az Event Grid kézbesítési esemény újrapróbálkozik.

Eseménykézbesítés és újrapróbálkozás [Event Grid az üzenetek kézbesítését, és ismételje meg](delivery-and-retry.md).

## <a name="delivery-metrics"></a>Kézbesítési metrikák

A portál megjeleníti az eseményt üzenetek kézbesítése állapotára vonatkozó metrikákat.

A témaköröket a metrikák a következők:

* **Sikeres közzététel**: Sikeresen eseményt a témakörbe küldött, és feldolgozása és a egy 2xx választ.
* **Nem sikerült a közzététel**: Eseményt a témakörbe küldött, de egy hibakódot elutasították.
* **Páratlan**: Esemény sikeresen közzétette a témakörre, de nem felel meg egy esemény-előfizetést. Az eseményt eldobta.

Az előfizetések a metrikák a következők:

* **Sikeres kézbesítés**: Esemény sikeresen kézbesíteni az előfizetési végpont, és a 2xx válasz érkezett.
* **Sikertelen kézbesítés**: Esemény az előfizetéshez tartozó végpontra küldött, de a 4xx vagy 5xx válasz érkezett.
* **Lejárt események**: Esemény nem lett kézbesítve, és az összes újrapróbálkozás le lett elküldve. Az eseményt eldobta.
* **Egyeztetett események**: A témakör az esemény megfelelést váltott ki. az esemény-előfizetés.

## <a name="event-subscription-status"></a>Esemény-előfizetés állapota

Egy esemény-előfizetés metrikáinak megtekintéséhez, vagy kereshet előfizetéssel rendelkezik, vagy egy adott erőforráshoz tartozó előfizetések.

Az esemény-előfizetés típusa alapján keres, válassza ki a **minden szolgáltatás**.

![Válassza ki az összes szolgáltatás](./media/monitor-event-delivery/all-services.png)

Keresse meg **event grid** válassza **Event Grid-előfizetések** az elérhető lehetőségek közül.

![Esemény-előfizetések keresése](./media/monitor-event-delivery/search-and-select.png)

Szűrés adott esemény, az előfizetésben és helyen. Válassza ki **metrikák** az előfizetés megtekintéséhez.

![Esemény-előfizetések szűrése](./media/monitor-event-delivery/filter-events.png)

Az esemény témakör és az előfizetés a metrikákat tekinthet meg.

![Esemény-metrikák megtekintése](./media/monitor-event-delivery/subscription-metrics.png)

A metrikák található adott erőforrásra vonatkozóan, válassza ki az adott erőforráshoz. Ezután válassza ki **események**.

![Egy erőforráscsoport eseményeire kiválasztása](./media/monitor-event-delivery/select-events.png)

Láthatja, az előfizetéseket az adott erőforrás metrikáit.

## <a name="custom-event-status"></a>Egyéni esemény állapota

Ha egy egyéni témakör tettük közzé, megtekintheti a metrikákat. Válassza ki a témakör az erőforráscsoportot, és jelölje ki a témakört.

![Egyéni témakör kiválasztása](./media/monitor-event-delivery/select-custom-topic.png)

Az egyéni esemény témakör a metrikákat tekinthet meg.

![Esemény-metrikák megtekintése](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="next-steps"></a>További lépések

* Eseménykézbesítés és újrapróbálkozás [Event Grid az üzenetek kézbesítését, és ismételje meg](delivery-and-retry.md).
* Az Event Grid megismeréséhez tekintse meg [az Event Grid bevezetőjét](overview.md).
* Tekintse meg a gyors kezdéshez Event Grid használatával [az Azure Event Griddel egyéni események létrehozása és útvonal](custom-event-quickstart.md).
