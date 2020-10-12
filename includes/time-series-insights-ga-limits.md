---
title: fájlbefoglalás
description: fájlbefoglalás
services: digital-twins
ms.service: digital-twins
ms.topic: include
ms.date: 07/09/2020
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.custom: include file
ms.openlocfilehash: 1aff5780aa5f4cf21d862a5cee3b140179dac03c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88703142"
---
Az alábbiakban a Azure Time Series Insights Gen1 található főbb korlátokat összegzi.

### <a name="sku-ingress-rates-and-capacities"></a>SKU-ba beáramló díjak és kapacitások

Az S1 és az S2 SKU beáramlási aránya és kapacitása rugalmasságot biztosít az új Azure Time Series Insights környezet konfigurálásakor. Az SKU kapacitása azt jelzi, hogy a napi beáramlási arány a tárolt események vagy bájtok száma alapján történik, attól függően, hogy melyik az első. Vegye figyelembe, hogy a bejövő forgalom *percenként*mérhető, és a **szabályozás** a jogkivonat-gyűjtő algoritmus használatával történik. A bejövő forgalom 1 KB-os blokkokban van mérve. Például egy 0,8 KB-os tényleges eseményt egyetlen eseményként kell mérni, a 2,6 KB-os esemény pedig három eseménynek számít.

| S1 SKU kapacitása | Bejövő forgalom aránya | Maximális tárolókapacitás
| --- | --- | --- |
| 1 | 1 GB (1 000 000 esemény) naponta | 30 GB (30 000 000 esemény) havonta |
| 10 | 10 GB (10 000 000 esemény) naponta | 300 GB (300 000 000 esemény)/hó |

| S2 SKU kapacitás | Bejövő forgalom aránya | Maximális tárolókapacitás
| --- | --- | --- |
| 1 | 10 GB (10 000 000 esemény) naponta | 300 GB (300 000 000 esemény)/hó |
| 10 | 100 GB (100 000 000 esemény)/nap | 3 TB (3 000 000 000 esemény) havonta |

> [!NOTE]
> A kapacitások lineárisan méretezhetők, ezért a 2. kapacitású S1 SKU 2 GB-ot (2 000 000), napi beléptetési arányt és 60 GB-ot (60 000 000 eseményt) támogat.

Az S2 SKU-környezetek havonta több eseményt támogatnak, és lényegesen magasabb beáramlási kapacitással rendelkeznek.

| SKU  | Események száma havonta  | Események száma percenként | Esemény mérete percenként  |
|---------|---------|---------|---------|---------|
| S1     |   30 000 000   |  720    |  720 KB   |
 |S2     |   300 000 000   | 7 200   | 7 200 KB  |

### <a name="property-limits"></a>Tulajdonságok korlátai

A Gen1 tulajdonság korlátai a kiválasztott SKU-környezettől függenek. A megadott esemény tulajdonságai a megfelelő JSON-, CSV-és diagram-oszlopokkal rendelkeznek, amelyek megtekinthetők a [Azure Time Series Insights Explorerben](https://docs.microsoft.com/azure/time-series-insights/time-series-quickstart).

| SKU | Maximális tulajdonságok |
| --- | --- |
| S1 | 600 tulajdonságok (oszlopok) |
| S2 | 800 tulajdonságok (oszlopok) |

### <a name="event-sources"></a>Eseményforrások

A példányok száma legfeljebb két eseményforrás esetén támogatott.

* Ismerje meg, hogyan [adhat hozzá egy Event hub-forrást](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-eventhub).
* [IoT hub-forrás](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)konfigurálása.

### <a name="api-limits"></a>API-korlátok

A [REST API](https://docs.microsoft.com/rest/api/time-series-insights/dataaccess(preview)/query/getavailability)dokumentációjában a Azure Time Series Insights Gen1 REST API korlátai vannak megadva.