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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280949"
---
# <a name="scaling-with-event-hubs"></a>Méretezés Event Hubs

Két tényező befolyásolja a skálázást Event Hubs.
*   Átviteli egységek
*   Partíciók

## <a name="throughput-units"></a>Átviteli egységek

Az Event Hubs átviteli kapacitásának szabályozása *átviteli egységek* révén történik. Az átviteli egységek előre megvásárolt kapacitásegységek. Egyetlen átviteli sebesség A következőket teszi lehetővé:

* Bemenő forgalom: Legfeljebb 1 MB, második vagy 1000 esemény / másodperc (amelyik előbb bekövetkezik).
* Kimenő forgalom: Legfeljebb 2 MB, második vagy 4096 esemény / másodperc.

A megvásárolt átviteli egységek kapacitásán túli bemenő forgalmat a rendszer korlátozza, és [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) választ ad vissza. A kimenő forgalom nem eredményez korlátozási kivételeket, azonban a megvásárolt átviteli egységek kapacitására van korlátozva. Ha közzétételi sebességhez kapcsolódó kivételeket kap, vagy nagyobb kimenő forgalomra számított, ellenőrizze, hány átviteli egységet vásárolt a névtérhez. Az átviteli egységeket a [Azure Portal](https://portal.azure.com)névterek **méretezési** paneljén kezelheti. Az átviteli egységeket programozott módon is kezelheti a [Event Hubs API](event-hubs-api-overview.md)-k használatával.

Átviteli egységek előre megvásárolt és díjszabása óradíjalapú. Miután megvásárolta, az átviteli egységek után legalább egy órányi díjat ki kell fizetni. Legfeljebb 20 átviteli egység vásárolható meg az Event Hubs-névtér és a névtér összes event hubs vannak megosztva.

A Event Hubs **automatikus** feltöltési funkciója automatikusan méretezi az átviteli egységek számának növelésével, hogy megfeleljen a használati igényeknek. Átviteli egységek növelése megakadályozza, hogy a szabályozási forgatókönyvek, ahol:

- Bejövő forgalom díjait meghaladják a beállított kapacitásegységek.
- Adatok kimenő kérelemarányok haladhatja meg a set-átviteli egységek.

Az Event Hubs szolgáltatás növeli az átviteli sebességet, ha a terhelés növekszik a minimális küszöbérték ServerBusy hiba miatt sikertelenül működő kérések nélkül. 

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
