---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 046c5c5e32c71364e1bf54551989e19eec4937e4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "67712431"
---
A `ApplicationInsights` beállítás lehetővé teszi, hogy hozzáadja az [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetria támogatását a tárolóhoz. Application Insights a tároló részletes figyelését teszi lehetővé. Egyszerűen figyelheti a tárolót a rendelkezésre állás, a teljesítmény és a használat érdekében. A tárolóban lévő hibák felismerése és diagnosztizálása is gyorsan elvégezhető.

A következő táblázat a szakasz által támogatott konfigurációs beállításokat ismerteti `ApplicationInsights` .

|Kötelező| Name (Név) | Adattípus | Leírás |
|--|------|-----------|-------------|
|Nem| `InstrumentationKey` | Sztring | Annak a Application Insights-példánynak a rendszerállapot-kulcsa, amelybe a rendszer a tárolóhoz tartozó telemetria-adatmennyiséget elküldi. További információ: [Application Insights ASP.net Corehoz](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Példa:<br>`InstrumentationKey=123456789`|

