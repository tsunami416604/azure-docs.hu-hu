---
title: Méretezhetőség – Azure Event Hubs | Microsoft dokumentumok
description: Ez a cikk az Azure Event Hubs partíciók és átviteli egységek használatával történő méretezésével kapcsolatos információkat tartalmaz.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 06/18/2019
ms.author: shvija
ms.openlocfilehash: 2b36faef8c39a8e9b02a056576ae7f5a77b1f6bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280949"
---
# <a name="scaling-with-event-hubs"></a>Méretezés eseményközpontokkal

Két tényező befolyásolja a méretezést az Event Hubs segítségével.
*   Átviteli egységek
*   Partíciók

## <a name="throughput-units"></a>Átviteli egységek

Az Event Hubs átviteli kapacitását *átviteli egységek*szabályozzák. Az átviteli egységek előre megvásárolt kapacitásegységek. Egyetlen átviteli parancs lehetővé teszi:

* Be- és be- és 1000 esemény másodpercenként (amelyik előbb következik be).
* Kimenő forgalom: Másodpercenként legfeljebb 2 MB vagy 4096 esemény másodpercenként.

A megvásárolt átviteli egységek kapacitásán túli bemenő forgalmat a rendszer korlátozza, és [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) választ ad vissza. A kimenő forgalom nem eredményez korlátozási kivételeket, azonban a megvásárolt átviteli egységek kapacitására van korlátozva. Ha közzétételi sebességhez kapcsolódó kivételeket kap, vagy nagyobb kimenő forgalomra számított, ellenőrizze, hány átviteli egységet vásárolt a névtérhez. Az átviteli egységek kezelése a névterek **Méretezés** panelén történik az [Azure Portalon](https://portal.azure.com). Az átviteli egységeket programozott módon is kezelheti az [Event Hubs API-k](event-hubs-api-overview.md)használatával.

Az átviteli egységek előre megvannak vásárolva, és óránként kerülnek számlázásra. Miután megvásárolta, az átviteli egységek után legalább egy órányi díjat ki kell fizetni. Egy Event Hubs névtérhez legfeljebb 20 átviteli egység vásárolható meg, és azok az adott névtér összes eseményközpontjában meg oszthatók.

Az Event Hubs **automatikus felfújási** funkciója automatikusan felskálázódik az átviteli egységek számának növelésével, a használati igények nek megfelelően. Az átviteli egységek növelése megakadályozza a sávszélesség-szabályozási forgatókönyveket, amelyekben:

- Az adatbe- és be- és adatátviteli sebességek meghaladják a beállított átviteli egységeket.
- Az adatforgalom kérési sebessége meghaladja a beállított átviteli egységeket.

Az Event Hubs szolgáltatás növeli az átviteli terhelést, ha a terhelés meghaladja a minimális küszöbértéket, anélkül, hogy a ServerBusy hibákkal kapcsolatos kérelmek sikertelenek lennenek. 

Az automatikus felfújás funkcióról az [Átviteli egységek automatikus méretezése](event-hubs-auto-inflate.md)című témakörben talál további információt.

## <a name="partitions"></a>Partíciók
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Partíciókulcs

A [partíciókulcs](event-hubs-programming-guide.md#partition-key) segítségével a bejövő eseményadatokat adott partíciókra képezheti le az adatszervezés céljából. A partíciókulcs az eseményközpontnak átadott, a küldő által megadott érték. A feldolgozása egy statikus kivonatoló függvénnyel történik, amely létrehozza a partíció-hozzárendelést. Ha nem ad meg partíciókulcsot az események közzétételekor, a rendszer ciklikus időszeleteléses hozzárendelést használ.

Az esemény-közzétevő csak a partíciókulcsot ismeri, azt a partíciót nem, amelyre az esemény közzé lesz téve. A kulcs és a partíció szétválasztása révén a küldőnek nem szükséges behatóan ismernie az alárendelt feldolgozási folyamatokat. Az eszközszintű vagy egyedi felhasználói identitás remek partíciókulcs lehet, de más tulajdonságok, például a földrajzi hely alapján szintén lehetséges az események csoportosítása egyetlen partícióra.


## <a name="next-steps"></a>További lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

- [Átviteli egységek automatikus skálázása](event-hubs-auto-inflate.md)
- [Az Eseményközpontok szolgáltatás áttekintése](event-hubs-what-is-event-hubs.md)
