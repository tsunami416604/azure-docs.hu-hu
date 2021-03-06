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
ms.openlocfilehash: e7b5dfe9380612d56e591c3f619dfcb8582f8dee
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95554257"
---
### <a name="property-limits"></a>Tulajdonságok korlátai

Azure Time Series Insights a tulajdonságok korlátai a meleg tárolás érdekében 1 000-ra növekedtek, és nem vonatkoznak a hűtőházi tárolásra vonatkozó tulajdonságértékek. A megadott esemény tulajdonságai a megfelelő JSON-, CSV-és diagram-oszlopokkal rendelkeznek, amelyeket megtekintheti a [Azure Time Series Insights Gen2 Explorerben](../articles/time-series-insights/quickstart-explore-tsi.md).

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

A [REST API](/rest/api/time-series-insights/preview#limits-1)dokumentációjában a Azure Time Series Insights Gen2 REST API korlátai vannak megadva.