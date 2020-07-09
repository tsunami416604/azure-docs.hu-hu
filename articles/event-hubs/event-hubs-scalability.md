---
title: Skálázhatóság – Azure Event Hubs | Microsoft Docs
description: Ez a cikk az Azure-Event Hubs partíciók és átviteli egységek használatával történő skálázását ismerteti.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 6d1c7bc5a0206b634e5a85243f25b2c926762d7e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85315483"
---
# <a name="scaling-with-event-hubs"></a>Méretezés Event Hubs

Két tényező befolyásolja a skálázást Event Hubs.
*   Átviteli egységek
*   Partíciók

## <a name="throughput-units"></a>Átviteli egységek

A Event Hubs átviteli kapacitását az *átviteli egységek*vezérlik. Az átviteli egységek előre megvásárolt kapacitásegységek. Egyetlen átviteli sebesség A következőket teszi lehetővé:

* Bejövő forgalom: másodpercenként legfeljebb 1 MB vagy 1000 esemény másodpercenként (amelyik előbb bekövetkezik).
* Kimenő forgalom: másodpercenként legfeljebb 2 MB vagy 4096 esemény másodpercenként.

A megvásárolt átviteli egységek kapacitásán túli bemenő forgalmat a rendszer korlátozza, és [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) választ ad vissza. A kimenő forgalom nem eredményez korlátozási kivételeket, azonban a megvásárolt átviteli egységek kapacitására van korlátozva. Ha közzétételi sebességhez kapcsolódó kivételeket kap, vagy nagyobb kimenő forgalomra számított, ellenőrizze, hány átviteli egységet vásárolt a névtérhez. Az átviteli egységek kezelése a névterek **Méretezés** panelén történik az [Azure Portalon](https://portal.azure.com). Az átviteli egységeket programozott módon is kezelheti a [Event Hubs API](event-hubs-api-overview.md)-k használatával.

Az átviteli egységek előzetes megvásárlása és óránkénti számlázása. Miután megvásárolta, az átviteli egységek után legalább egy órányi díjat ki kell fizetni. Egy Event Hubs névtérhez legfeljebb 20 átviteli egység vásárolható meg, és az adott névtérben található összes Event hub esetében meg van osztva.

A Event Hubs **automatikus** feltöltési funkciója automatikusan méretezi az átviteli egységek számának növelésével, hogy megfeleljen a használati igényeknek. Az átviteli egységek növelése megakadályozza a szabályozást, amelyben:

- A bejövő adatforgalom aránya meghaladja a beállított átviteli egységeket.
- A kimenő adatforgalomra vonatkozó kérelmek aránya meghaladja a set átviteli egységeket.

A Event Hubs szolgáltatás növeli az átviteli sebességet, ha a terhelés a minimális küszöbértéknél nagyobb mértékben növekszik, anélkül, hogy a ServerBusy hibákkal kapcsolatos kérések sikertelenek lesznek. 

További információ az automatikus kiosztási szolgáltatásról: az [átviteli egységek automatikus skálázása](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partíciók
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Partíciókulcs

A [partíciós kulcs](event-hubs-programming-guide.md#partition-key) használatával a bejövő események adatait meghatározott partíciókhoz rendelheti az adatszervezet céljára. A partíciókulcs az eseményközpontnak átadott, a küldő által megadott érték. A feldolgozása egy statikus kivonatoló függvénnyel történik, amely létrehozza a partíció-hozzárendelést. Ha nem ad meg partíciókulcsot az események közzétételekor, a rendszer ciklikus időszeleteléses hozzárendelést használ.

Az esemény-közzétevő csak a partíciókulcsot ismeri, azt a partíciót nem, amelyre az esemény közzé lesz téve. A kulcs és a partíció szétválasztása révén a küldőnek nem szükséges behatóan ismernie az alárendelt feldolgozási folyamatokat. Az eszközszintű vagy egyedi felhasználói identitás remek partíciókulcs lehet, de más tulajdonságok, például a földrajzi hely alapján szintén lehetséges az események csoportosítása egyetlen partícióra.


## <a name="next-steps"></a>További lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

- [Átviteli egységek automatikus skálázása](event-hubs-auto-inflate.md)
- [Event Hubs szolgáltatás áttekintése](event-hubs-what-is-event-hubs.md)
