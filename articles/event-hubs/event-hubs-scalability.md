---
title: Skálázhatóság – Azure Event Hubs | Microsoft Docs
description: Ez a cikk az Azure-Event Hubs partíciók és átviteli egységek használatával történő skálázását ismerteti.
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
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76309523"
---
# <a name="scaling-with-event-hubs"></a>Méretezés Event Hubs

Két tényező befolyásolja a skálázást Event Hubs.
*   Adatkapacitás-egységek
*   Partíciók

## <a name="throughput-units"></a>Adatkapacitás-egységek

Az Event Hubs átviteli kapacitásának szabályozása *átviteli egységek* révén történik. Az átviteli egységek előre megvásárolt kapacitásegységek. Egyetlen átviteli sebesség A következőket teszi lehetővé:

* Bejövő forgalom: másodpercenként legfeljebb 1 MB vagy 1000 esemény másodpercenként (amelyik előbb bekövetkezik).
* Kimenő forgalom: másodpercenként legfeljebb 2 MB vagy 4096 esemény másodpercenként.

A megvásárolt átviteli egységek kapacitásán túli bemenő forgalmat a rendszer korlátozza, és [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) választ ad vissza. A kimenő forgalom nem eredményez korlátozási kivételeket, azonban a megvásárolt átviteli egységek kapacitására van korlátozva. Ha közzétételi sebességhez kapcsolódó kivételeket kap, vagy nagyobb kimenő forgalomra számított, ellenőrizze, hány átviteli egységet vásárolt a névtérhez. Az átviteli egységeket a [Azure Portal](https://portal.azure.com)névterek **méretezési** paneljén kezelheti. Az átviteli egységeket programozott módon is kezelheti a [Event Hubs API](event-hubs-api-overview.md)-k használatával.

Az átviteli egységek előzetes megvásárlása és óránkénti számlázása. Miután megvásárolta, az átviteli egységek után legalább egy órányi díjat ki kell fizetni. Egy Event Hubs névtérhez legfeljebb 20 átviteli egység vásárolható meg, és az adott névtérben található összes Event hub esetében meg van osztva.

A Event Hubs **automatikus** feltöltési funkciója automatikusan méretezi az átviteli egységek számának növelésével, hogy megfeleljen a használati igényeknek. Az átviteli egységek növelése megakadályozza a szabályozást, amelyben:

- A bejövő adatforgalom aránya meghaladja a beállított átviteli egységeket.
- A kimenő adatforgalomra vonatkozó kérelmek aránya meghaladja a set átviteli egységeket.

A Event Hubs szolgáltatás növeli az átviteli sebességet, ha a terhelés a minimális küszöbértéknél nagyobb mértékben növekszik, anélkül, hogy a ServerBusy hibákkal kapcsolatos kérések sikertelenek lesznek. 

További információ az automatikus kiosztási szolgáltatásról: az [átviteli egységek automatikus skálázása](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partíciók
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Partíciókulcs

A [partíciókulccsal](event-hubs-programming-guide.md#partition-key) a beérkező eseményadatok képezhetők le adott partíciókra az adatok elrendezése céljából. A partíciókulcs az eseményközpontnak átadott, a küldő által megadott érték. A feldolgozása egy statikus kivonatoló függvénnyel történik, amely létrehozza a partíció-hozzárendelést. Ha nem ad meg partíciókulcsot az események közzétételekor, a rendszer ciklikus időszeleteléses hozzárendelést használ.

Az esemény-közzétevő csak a partíciókulcsot ismeri, azt a partíciót nem, amelyre az esemény közzé lesz téve. A kulcs és a partíció szétválasztása révén a küldőnek nem szükséges behatóan ismernie az alárendelt feldolgozási folyamatokat. Az eszközszintű vagy egyedi felhasználói identitás remek partíciókulcs lehet, de más tulajdonságok, például a földrajzi hely alapján szintén lehetséges az események csoportosítása egyetlen partícióra.


## <a name="next-steps"></a>Következő lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

- [Átviteli egységek automatikus skálázása](event-hubs-auto-inflate.md)
- [Event Hubs szolgáltatás áttekintése](event-hubs-what-is-event-hubs.md)
