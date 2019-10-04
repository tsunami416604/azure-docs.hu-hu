---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 046c5c5e32c71364e1bf54551989e19eec4937e4
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67712431"
---
A `ApplicationInsights` beállítás lehetővé teszi, hogy [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetriai támogatása a tárolóba. Az Application Insights az a tároló részletes figyelését teszi lehetővé. Könnyedén figyelheti a tárolója elérhetőségéről, teljesítményéről és kihasználtságáról. Emellett egyszerűen azonosíthatja és diagnosztizálhatja a hibákat a tárolóban.

A következő táblázat ismerteti a támogatott konfigurációs beállításait a `ApplicationInsights` szakaszban.

|Szükséges| Name (Név) | Adattípus | Leírás |
|--|------|-----------|-------------|
|Nem| `InstrumentationKey` | Karakterlánc | A rendszerállapotkulcsot az Application Insights-példány melyik telemetriai adatokat a tároló küldi. További információkért lásd: [Application Insights az ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Példa:<br>`InstrumentationKey=123456789`|

