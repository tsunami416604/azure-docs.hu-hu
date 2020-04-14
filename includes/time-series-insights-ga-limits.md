---
title: fájl belefoglalása
description: fájl belefoglalása
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.custom: include file
ms.openlocfilehash: 11469d992e0f5669cd3fc1e3864627dd0b8ae23d
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263343"
---
A következő összegzi a kulcskorlátokat az általános elérhetőségben.

### <a name="sku-ingress-rates-and-capacities"></a>A Termékváltozat be- és kapacitásai

Az S1 és S2 termékváltozatok díjai és kapacitásai rugalmasságot biztosítanak egy új Time Series Insights-környezet konfigurálásakor. A termékváltozat-kapacitás a napi be- és bájtok száma alapján jelzi a napi be- és bájtsebességet, amelyik előbb következik be. Vegye figyelembe, hogy a be- ésézés *percenként*imperget, és a **szabályozás** a token gyűjtő algoritmus használatával történik. A be- és be- és átakat 1 KB-os blokkokban mérik. Például egy 0,8 KB-os tényleges esemény egy eseményként, egy 2,6 KB-os esemény pedig három eseménynek számít.

| S1 termékváltozat kapacitása | Be- és visszakamatemelési arány | Maximális tárolókapacitás
| --- | --- | --- |
| 1 | 1 GB (1 millió esemény) naponta | 30 GB (30 millió esemény) havonta |
| 10 | 10 GB (10 millió esemény) naponta | 300 GB (300 millió esemény) havonta |

| S2 termékváltozat kapacitása | Be- és visszakamatemelési arány | Maximális tárolókapacitás
| --- | --- | --- |
| 1 | 10 GB (10 millió esemény) naponta | 300 GB (300 millió esemény) havonta |
| 10 | 100 GB (100 millió esemény) naponta | 3 TB (3 milliárd esemény) havonta |

> [!NOTE]
> A kapacitások lineárisan skáláznak, így a 2 kapacitású S1 termékváltozat támogatja a napi 2 GB (2 millió) eseményt és a 60 GB-os (60 millió esemény) havonta.

S2 Termékváltozat-környezetek támogatása lényegesen több esemény havonta, és lényegesen nagyobb be- és kapacitással rendelkeznek.

| SKU  | Események száma havonta  | Események száma percenként | Esemény mérete percenként  |
|---------|---------|---------|---------|---------|
| S1     |   30 millió   |  720    |  720 KB   |
 |S2     |   300 millió   | 7,200   | 7200 KB  |

### <a name="property-limits"></a>Ingatlankorlátok

GA tulajdonságkorlátok függ a termékváltozat-környezet, amely a kiválasztott. A megadott eseménytulajdonságok megfelelő JSON-, CSV- és diagramoszlopokkal rendelkeznek, amelyek a [Time Series Insights Explorer](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart)programban tekinthetők meg.

| SKU | Maximális tulajdonságok |
| --- | --- |
| S1 | 600 tulajdonság (oszlopok) |
| S2 | 800 tulajdonság (oszlopok) |

### <a name="event-sources"></a>Eseményforrások

Példányonként legfeljebb két eseményforrás támogatott. 

* További információ az [eseményközpont-forrás hozzáadásáról.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub)
* [Konfiguráljon egy IoT hubforrást.](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)

### <a name="api-limits"></a>API-korlátok

A Time Series Insights általános elérhetőségéhez tartozó REST API-korlátozásokat a [REST API referenciadokumentációja](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#limits)határozza meg.