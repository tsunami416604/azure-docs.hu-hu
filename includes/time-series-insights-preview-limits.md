---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 7bc6938523a6d66a2bc20b37c659568fc5ca494d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77123153"
---
### <a name="general-availability-and-preview-comparison"></a>Általános elérhetőség és előnézeti összehasonlítás

Az alábbi táblázat összefoglalja az Azure Time Series Insights általános rendelkezésre állása (GA) és az előzetes verziók közötti számos kulcsfontosságú különbséget.

| | FE | Előzetes verzió |
| --- | --- | ---|
| Első osztályú polgár | Eseményközpontú | Idősorozat-centrikus |
| Szemantikai érvelés | Alacsony szintű (referenciaadatok) | Magas szintű (modellek) |
| Adatkörnyezet-szinkronizálás | Nem eszközszint | Eszköz- és nem eszközszint |
| Számítási logikai tárolás | Nem | A típusváltozókban tárolt modell része |
| Tárolás és hozzáférés-vezérlés | Nem | Engedélyezve a modellen keresztül |
| Összesítések/mintavétel | Nem | Esemény súlyozása és idősúlyozott |
| Jelrekonstrukció | Nem | Interpoláció |
| Származtatott idősorok gyártása | Nem | Igen, egyesítések és illesztések |
| Nyelvi rugalmasság | Nem kompozható | Kompozható |
| Kifejezés nyelve | Predikátumkarakterlánc | Idősorozat-kifejezések (predikátumkarakterláncok, értékek, kifejezések és függvények) |

### <a name="property-limits"></a>Ingatlankorlátok

A Time Series Insights tulajdonságszámai 1000-re nőttek a ga-ban legfeljebb 800-as felső korlátról. A megadott eseménytulajdonságok megfelelő JSON-, CSV- és diagramoszlopokkal rendelkeznek, amelyeket a [Time Series Insights előzetes verziókezelőjében](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-quickstart)tekinthet meg.

| SKU | Maximális tulajdonságok |
| --- | --- |
| PAYG előzetes megtekintése | 1000 tulajdonság (oszlopok) |
| GA S1 | 600 tulajdonság (oszlopok) |
| GA S2 | 800 tulajdonság (oszlopok) |

### <a name="event-sources"></a>Eseményforrások

Példányonként legfeljebb két eseményforrás támogatott. 

* További információ az [eseményközpont-forrás hozzáadásáról.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)
* [Konfiguráljon egy IoT hubforrást.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)

Alapértelmezés szerint az előzetes verziójú környezetek környezetenként legfeljebb **1 megabájt (MB/s)** sebességű be- és [bájtot támogatnak.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-storage-ingress#ingress-scale-and-preview-limitations) Az ügyfelek szükség esetén **legfeljebb 16 MB/s** átviteli sebességre méretezhetik az előzetes verziójú környezeteiket. Partíciónként **0,5 MB/s**a korlát. 

### <a name="api-limits"></a>API-korlátok

A Time Series Insights előzetes verziójának REST API-korlátait a [REST API referenciadokumentációja](https://docs.microsoft.com/rest/api/time-series-insights/preview-query#limits)határozza meg.
