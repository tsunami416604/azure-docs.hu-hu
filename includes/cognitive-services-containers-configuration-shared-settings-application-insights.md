---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 046c5c5e32c71364e1bf54551989e19eec4937e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67712431"
---
A `ApplicationInsights` beállítás lehetővé teszi, hogy [az Azure Application Insights](https://docs.microsoft.com/azure/application-insights) telemetriai támogatást a tárolóba. Az Application Insights részletesen figyeli a tárolót. Könnyedén figyelheti a tároló rendelkezésre állását, teljesítményét és használatát. Gyorsan azonosíthatja és diagnosztizálhatja a tárolóban lévő hibákat is.

Az alábbi táblázat a `ApplicationInsights` szakaszban támogatott konfigurációs beállításokat ismerteti.

|Kötelező| Név | Adattípus | Leírás |
|--|------|-----------|-------------|
|Nem| `InstrumentationKey` | Sztring | Az Application Insights-példány instrumentációs kulcsa, amelyhez a tároló telemetriai adatait küldi. További információ: [Application Insights for ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Példa:<br>`InstrumentationKey=123456789`|

