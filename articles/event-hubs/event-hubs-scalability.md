---
title: Skálázhatóság – az Azure Event Hubs |} A Microsoft Docs
description: Információt nyújt az Azure Event Hubs méretezhető.
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
ms.openlocfilehash: c46b333f2cc304cc12ddf78670b60940c7bc0db3
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827683"
---
# <a name="scaling-with-event-hubs"></a>Az Event Hubs méretezése

Nincsenek két tényező, amely befolyásolhatja az Event Hubs méretezés.
*   Átviteli egységek
*   Partíciók

## <a name="throughput-units"></a>Átviteli egységek

Az Event Hubs átviteli kapacitásának szabályozása *átviteli egységek* révén történik. Az átviteli egységek előre megvásárolt kapacitásegységek. Egy átviteli teszi lehetővé:

* Bemenő forgalom: Második vagy 1000 esemény (amelyik előbb bekövetkezik) másodpercenként legfeljebb 1 MB.
* Kimenő forgalom: Akár 2 MB / másodpercben a második vagy 4096 esemény.

A megvásárolt átviteli egységek kapacitásán túli bemenő forgalmat a rendszer korlátozza, és [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) választ ad vissza. A kimenő forgalom nem eredményez korlátozási kivételeket, azonban a megvásárolt átviteli egységek kapacitására van korlátozva. Ha közzétételi sebességhez kapcsolódó kivételeket kap, vagy nagyobb kimenő forgalomra számított, ellenőrizze, hány átviteli egységet vásárolt a névtérhez. Az átviteli egységek segítségével kezelheti a **méretezési** névtereket paneljén a [az Azure portal](https://portal.azure.com). Átviteli egységek használatával programozott módon is kezelhetők a [Event Hubs API-k](event-hubs-api-overview.md).

Átviteli egységek előre megvásárolt és díjszabása óradíjalapú. Miután megvásárolta, az átviteli egységek után legalább egy órányi díjat ki kell fizetni. Legfeljebb 20 átviteli egység vásárolható meg az Event Hubs-névtér és a névtér összes event hubs vannak megosztva.

A **automatikus feltöltésről** az Event hubs szolgáltatás automatikusan felskálázással növelje átviteli egységek számát, a használattal kapcsolatos igények alapján. Átviteli egységek növelése megakadályozza, hogy a szabályozási forgatókönyvek, ahol:

- Bejövő forgalom díjait meghaladják a beállított kapacitásegységek.
- Adatok kimenő kérelemarányok haladhatja meg a set-átviteli egységek.

Az Event Hubs szolgáltatás növeli az átviteli sebességet, ha a terhelés növekszik a minimális küszöbérték ServerBusy hiba miatt sikertelenül működő kérések nélkül. 

További információ az automatikus feltöltési funkció, lásd: [átviteli egységek automatikus skálázása](event-hubs-auto-inflate.md).

## <a name="partitions"></a>Partíciók
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]

### <a name="partition-key"></a>Partíciókulcs

A [partíciókulccsal](event-hubs-programming-guide.md#partition-key) a beérkező eseményadatok képezhetők le adott partíciókra az adatok elrendezése céljából. A partíciókulcs az eseményközpontnak átadott, a küldő által megadott érték. A feldolgozása egy statikus kivonatoló függvénnyel történik, amely létrehozza a partíció-hozzárendelést. Ha nem ad meg partíciókulcsot az események közzétételekor, a rendszer ciklikus időszeleteléses hozzárendelést használ.

Az esemény-közzétevő csak a partíciókulcsot ismeri, azt a partíciót nem, amelyre az esemény közzé lesz téve. A kulcs és a partíció szétválasztása révén a küldőnek nem szükséges behatóan ismernie az alárendelt feldolgozási folyamatokat. Az eszközszintű vagy egyedi felhasználói identitás remek partíciókulcs lehet, de más tulajdonságok, például a földrajzi hely alapján szintén lehetséges az események csoportosítása egyetlen partícióra.


## <a name="next-steps"></a>További lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

- [Átviteli egységek automatikus skálázása](event-hubs-auto-inflate.md)
- [Event Hubs szolgáltatás áttekintése](event-hubs-what-is-event-hubs.md)
