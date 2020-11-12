---
title: fájlbefoglalás
description: fájlbefoglalás
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 5ec1f71d13d5d2fb3af86c1c63d5e61ce13d2c82
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94553239"
---
### <a name="property-limits"></a>Tulajdonságok korlátai

Azure Time Series Insights a tulajdonságok korlátai a meleg tárolás érdekében 1 000-ra növekedtek, és nem vonatkoznak a hűtőházi tárolásra vonatkozó tulajdonságértékek. A megadott esemény tulajdonságai a megfelelő JSON-, CSV-és diagram-oszlopokkal rendelkeznek, amelyeket megtekintheti a [Azure Time Series Insights Gen2 Explorerben](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart).

| Termékváltozat | Maximális tulajdonságok |
| --- | --- |
| Gen2 (L1) | 1 000 tulajdonságok (oszlopok) a meleg tároláshoz és korlátlan a hideg tároláshoz|
| Gen1 (S1) | 600 tulajdonságok (oszlopok) |
| Gen1 (S2) | 800 tulajdonságok (oszlopok) |

### <a name="streaming-ingestion"></a>Streamek feldolgozása

* Környezetekben legfeljebb két [eseményforrás](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md) lehet.

* Az Event sources szolgáltatással kapcsolatos ajánlott eljárások és általános útmutatás [itt](../articles/time-series-insights/concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) található

* Alapértelmezés szerint a Azure Time Series Insights Gen2 **legfeljebb 1 megabájt/másodperc (Mbps)** sebességgel képes befogadni a bejövő adatot Azure Time Series Insights Gen2-környezetben. A [hub-partíciók esetében](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md#hub-partitions-and-per-partition-limits)további korlátozások vannak érvényben. A támogatási jegynek a Azure Portalon keresztüli elküldésével legfeljebb 8 MBps sebesség adható meg. További információért olvassa el a streaming betöltési [átviteli sebességének korlátozásait](../articles/time-series-insights/concepts-streaming-ingress-throughput-limits.md).

### <a name="api-limits"></a>API-korlátok

A [REST API](https://docs.microsoft.com/rest/api/time-series-insights/preview#limits-1)dokumentációjában a Azure Time Series Insights Gen2 REST API korlátai vannak megadva.
