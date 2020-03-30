---
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 11/09/2018
ms.author: mbullwin
ms.openlocfilehash: 91141e314bf276d9138157c8a9d85d5262ac5907
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67178877"
---
Egyéni mérések gyűjteménye. Ezzel a gyűjteménynel jelentheti a telemetriai elemhez társított, nevű mérést. A tipikus használati esetek a következők:
- a függőségi telemetriai tartalom mérete
- a Telemetriai kérelem által feldolgozott várólistaelemek száma
- az az idő, amelyet az ügyfél a varázsló lépésének befejezéséhez szükséges lépés végrehajtásához szükséges idő, eseménytelemetria.

Az Application Analytics alkalmazásban lekérdezheti az [egyéni méréseket:](https://analytics.applicationinsights.io/demo?q=H4sIAAAAAAAAA2WLOw6DMAyGd07hZoLeoRPqyMaGGAL8aiPhGCV2kKoeHsHK%2Bj1myyr8LoiaqfrT%2FkUCzRft4LMl8OUeL3LuLLIx%2BxR%2BIF8%2BtcoiNq2o78vgWuFthQaJ1AeGGxt6UlBwKxa1qQ6EpLhAfQAAAA%3D%3D&timespan=PT24H)

```
customEvents
| where customMeasurements != ""
| summarize avg(todouble(customMeasurements["Completion Time"]) * itemCount)
```

 > [!NOTE]
 > Az egyéni mérések a telemetriai elemhez vannak társítva, amelyhez tartoznak. Ezek a méréseket tartalmazó telemetriai elem mintavételezése tárgyát képezik. A más telemetriai típusoktól független értékkel rendelkező mérés nyomon követéséhez használja [a Metrika telemetriai adatokat.](../articles/azure-monitor/app/api-custom-events-metrics.md)

Maximális kulcshossz: 150
