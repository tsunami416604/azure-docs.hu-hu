---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 48080bb4d1e24f7f98d3dfe1fd63b65ba46df35e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87289913"
---
### <a name="property-limits"></a>Tulajdonságok korlátai

Azure Time Series Insights a 1 000 a Gen1-ben maximális 800-os korláttal emelkedett. A megadott esemény tulajdonságai a megfelelő JSON-, CSV-és diagram-oszlopokkal rendelkeznek, amelyeket megtekintheti a [Azure Time Series Insights Gen2 Explorerben](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| Termékváltozat | Maximális tulajdonságok |
| --- | --- |
| Gen2 (L1) | 1 000 tulajdonságok (oszlopok) |
| Gen1 (S1) | 600 tulajdonságok (oszlopok) |
| Gen1 (S2) | 800 tulajdonságok (oszlopok) |

### <a name="event-sources"></a>Eseményforrások

A példányok száma legfeljebb két eseményforrás esetén támogatott.

* Ismerje meg, hogyan [adhat hozzá egy Event hub-forrást](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* [IoT hub-forrás](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)konfigurálása.

Alapértelmezés szerint a Gen2-környezetek **felhasználónként 1 megabájt/másodperc (MB/s)** [sebességű bejövő forgalmat támogatnak](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingress-throughput-limits) . Ha szükséges, az ügyfelek akár **16 MB/s** adatátviteli sebességet is igénybe vehetnek. A partíciós korlát **0,5 MB/s**.

### <a name="api-limits"></a>API-korlátok

A [REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1)dokumentációjában a Azure Time Series Insights Gen2 REST API korlátai vannak megadva.
