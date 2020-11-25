---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: d74279c9c4d5d88e5837046e9484f5af7aad1442
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001170"
---
A `ApplicationInsights` beállítás lehetővé teszi, hogy hozzáadja az [Azure Application Insights](/azure/application-insights) telemetria támogatását a tárolóhoz. Application Insights a tároló részletes figyelését teszi lehetővé. Egyszerűen figyelheti a tárolót a rendelkezésre állás, a teljesítmény és a használat érdekében. A tárolóban lévő hibák felismerése és diagnosztizálása is gyorsan elvégezhető.

A következő táblázat a szakasz által támogatott konfigurációs beállításokat ismerteti `ApplicationInsights` .

|Kötelező| Név | Adattípus | Leírás |
|--|------|-----------|-------------|
|Nem| `InstrumentationKey` | Sztring | Annak a Application Insights-példánynak a rendszerállapot-kulcsa, amelybe a rendszer a tárolóhoz tartozó telemetria-adatmennyiséget elküldi. További információ: [Application Insights ASP.net Corehoz](../articles/azure-monitor/app/asp-net-core.md). <br><br>Példa:<br>`InstrumentationKey=123456789`|