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
ms.openlocfilehash: 3eb20013a6b3afaddce10f2e4652add0edf22a9a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276781"
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

Partíciók tegye lehetővé, hogy nagy számú az alsóbb feldolgozási. A particionált felhasználói modell, amely az Event Hubs kínál partíciókat, mert meg is horizontális felskálázás az esemény egyidejű feldolgozása közben. Az Event Hub legfeljebb 32 partícióval rendelkezhet.

Azt javasoljuk, hogy optimális méretezhetőség 1:1 átviteli egységek és partíciók kiegyenlítése. Egy partíció rendelkezik egy garantált bejövő és kimenő, legfeljebb egy átviteli egységgel rendelkezhet. Előfordulhat, hogy a partíció nagyobb átviteli sebességet érhet el, amíg a teljesítmény nem garantált. Ezért erősen ajánlott, hogy az eseményközpontokban található partíciók számának nagyobbnak vagy azzal egyenlőnek átviteli egységek számát kell-e.

Adja meg a teljes átviteli sebesség a kellene tervezi, hogy a szükséges átviteli egységek számát és a partíciók minimális száma, de a hány partíciók kell, hogy? Válassza ki az alsóbb rétegbeli párhuzamosság használatával kíván elérni, valamint a jövőbeli átviteli igényei alapján a partíciók számát. Nem jár költségekkel rendelkezik egy eseményközpontban partíciók számát.

Részletes információk az Event Hubs díjszabásáról: [Event Hubs-díjszabás](https://azure.microsoft.com/pricing/details/event-hubs/).

## <a name="next-steps"></a>További lépések
Az alábbi webhelyeken további információt talál az Event Hubsról:

- [Átviteli egységek automatikus skálázása](event-hubs-auto-inflate.md)
- [Event Hubs szolgáltatás áttekintése](event-hubs-what-is-event-hubs.md)
