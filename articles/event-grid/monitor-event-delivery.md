---
title: Az Azure Event Grid üzenetkézbesítésének figyelése
description: Ez a cikk ismerteti, hogyan használhatja az Azure Portalon az Azure Event Grid-üzenetek kézbesítésének állapotának megtekintéséhez.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 16587feaca65aa21836d9be1c44e00faa0f4f8d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722135"
---
# <a name="monitor-event-grid-message-delivery"></a>Eseményrácsüzenet kézbesítésének figyelése 

Ez a cikk azt ismerteti, hogyan használhatja a portált az eseményszállítások állapotának megtekintéséhez.

Az Event Grid tartós kézbesítést biztosít. Minden egyes üzenetet legalább egyszer kézbesít minden egyes előfizetéshez. Az események et a rendszer azonnal elküldi az egyes előfizetések regisztrált webhookjába. Ha egy webhook nem nyugtázza egy esemény kézhezvételét az első kézbesítési kísérlettől számított 60 másodpercen belül, az Event Grid újrapróbálkozik az esemény kézbesítésével.

Az eseménykézbesítésről és az újrapróbálkozásokról az [Event Grid üzenetkézbesítésével és újrapróbálkozásával](delivery-and-retry.md)kapcsolatos információkért kattintson.

## <a name="delivery-metrics"></a>Kézbesítési mutatók

A portál megjeleníti az eseményüzenetek kézbesítésének állapotát mutató mutatókat.

A témakörök esetében a mérőszámok a következők:

* **A közzététel sikeres :** A témakörnek sikeresen elküldött esemény, amelyet 2xx válaszsal dolgoznak fel.
* **A közzététel sikertelen :** A témakörnek küldött, de hibakóddal elutasított esemény.
* **Páratlan**: A témakörben sikeresen közzétett esemény, de nem illeszkedik esemény-előfizetéshez. Az eseményt ejtették.

Az előfizetések esetében a mérőszámok a következők:

* **Kézbesítés sikerült:** Az esemény sikeresen kézbesítve az előfizetés végpontjára, és 2xx választ kapott.
* **Kézbesítés sikertelen:** Az előfizetés végpontjára küldött esemény, de 4xx vagy 5xx választ kapott.
* **Lejárt események**: Az esemény nem lett kézbesítve, és az összes újrapróbálkozási kísérlet elküldésre került. Az eseményt ejtették.
* **Egyező események:** A témakörben lévő eseményt az esemény-előfizetés párosította.

## <a name="event-subscription-status"></a>Esemény-előfizetés állapota

Egy esemény-előfizetés metrikák megtekintéséhez kereshet előfizetéstípusa vagy előfizetések egy adott erőforrás.

Az esemény-előfizetés típusa szerint való kereséshez válassza **a Minden szolgáltatás**lehetőséget.

![Az összes szolgáltatás kijelölése](./media/monitor-event-delivery/all-services.png)

Keressen **eseményrácsot,** és válassza az **Eseményrács-előfizetések** lehetőséget a rendelkezésre álló lehetőségek közül.

![Esemény-előfizetések keresése](./media/monitor-event-delivery/search-and-select.png)

Szűrés az esemény típusa, az előfizetés és a hely szerint. Válassza **ki a metrikák** az előfizetés megtekintéséhez.

![Esemény-előfizetések szűrése](./media/monitor-event-delivery/filter-events.png)

Tekintse meg az eseménytémakör és az előfizetés metrikáit.

![Eseménymérőszámok megtekintése](./media/monitor-event-delivery/subscription-metrics.png)

Egy adott erőforrás metrikák megkereséséhez válassza ki az adott erőforrást. Ezután válassza az **Események**lehetőséget.

![Események kijelölése egy erőforráshoz](./media/monitor-event-delivery/select-events.png)

Az adott erőforrás előfizetései metrikák at láthatja.

## <a name="custom-event-status"></a>Egyéni esemény állapota

Ha közzétett egy egyéni témakört, megtekintheti a mérőszámokat. Jelölje ki a témakör erőforráscsoportját, és jelölje ki a témakört.

![Egyéni témakör kiválasztása](./media/monitor-event-delivery/select-custom-topic.png)

Tekintse meg az egyéni eseménytémakör metrikáit.

![Eseménymérőszámok megtekintése](./media/monitor-event-delivery/custom-topic-metrics.png)

## <a name="set-alerts"></a>Riasztások beállítása

Riasztásokat állíthat be a témakörhöz és a tartományszintű metrikákhoz az egyéni témakörökhöz és az eseménytartományokhoz. Az áttekintő panelen válassza a riasztások a bal oldali erőforrás menüben a **figyelmeztetési** szabályok megtekintése, kezelése és létrehozása érdekében. [További információ az Azure Monitor-riasztásokról](../azure-monitor/platform/alerts-overview.md)

![Eseménymérőszámok megtekintése](./media/monitor-event-delivery/select-alerts.png)

## <a name="next-steps"></a>További lépések

* Az eseménykézbesítésről és az újrapróbálkozásokról az [Event Grid üzenetkézbesítésével és újrapróbálkozásával](delivery-and-retry.md)kapcsolatos információkért kattintson.
* Az Event Grid ismertetése: [Az Event Grid bemutatása](overview.md).
* Az Event Grid használatának gyors megkezdéséhez olvassa el az [Egyéni események létrehozása és irányítása az Azure Event Griddel című témakört.](custom-event-quickstart.md)
