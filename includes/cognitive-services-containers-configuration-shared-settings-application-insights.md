---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 2f5b03dd0170da9a9869183d7a412688509525ef
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179387"
---
A `ApplicationInsights` beállítás lehetővé teszi, hogy [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetriai támogatása a tárolóba. Az Application Insights az a tároló részletes figyelését teszi lehetővé. Könnyedén figyelheti a tárolója elérhetőségéről, teljesítményéről és kihasználtságáról. Emellett egyszerűen azonosíthatja és diagnosztizálhatja a hibákat a tárolóban.

A következő táblázat ismerteti a támogatott konfigurációs beállításait a `ApplicationInsights` szakaszban.

|Szükséges| Name (Név) | Adattípus | Leírás |
|--|------|-----------|-------------|
|Nem| `InstrumentationKey` | Karakterlánc | A rendszerállapotkulcsot az Application Insights-példány melyik telemetriai adatokat a tároló küldi. További információkért lásd: [Application Insights az ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Példa:<br>`InstrumentationKey=123456789`|

