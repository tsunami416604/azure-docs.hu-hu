---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 3986b77cfad167134bf8ada1e3cbb73ad64dd3ca
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572230"
---
Egyéni mérések gyűjteménye. Használja a gyűjtemény a telemetriai adatok elemhez társított mérési jelentést. A tipikus használati esetek a következők:
- Függőségi Telemetria hasznos adat mérete
- Telemetriai kérelem által feldolgozott várólista elemek száma.
- adott ügyfél varázsló lépés Befejezés esemény Telemetriát a lépés befejezéséhez szükséges időt.

Lekérdezheti [egyéni mérések](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H) az Application Analytics szolgáltatásban:

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Egyéni mérések társítva a szereplő telemetriai elem tartoznak. A fenti mérések tartalmazó szereplő telemetriai elem azok mintavételi vonatkoznak. Egy mérték, amely független a többi telemetriatípusok értéke nyomon követéséhez használja [metrika telemetriai](../articles/application-insights/app-insights-api-custom-events-metrics.md).

Maximális kulcshossz: 150
