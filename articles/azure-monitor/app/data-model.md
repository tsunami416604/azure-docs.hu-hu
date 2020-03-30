---
title: Azure Application Insights telemetriai adatmodell | Microsoft dokumentumok
description: Az Application Insights-adatmodell áttekintése
services: application-insights
documentationcenter: .net
manager: carmonm
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/14/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 94013a12e1cf48a8007fce2547c200d82a657b71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671834"
---
# <a name="application-insights-telemetry-data-model"></a>Az Application Insights telemetriai adatmodellje

[Az Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) telemetriát küld a webalkalmazásból az Azure Portalra, így elemezheti az alkalmazás teljesítményét és használatát. A telemetriai modell szabványosított, így platform- és nyelvfüggetlen figyelést hozhat létre. 

Az Application Insights által gyűjtött adatok ezt a tipikus alkalmazás-végrehajtási mintát modellezik:

![Application Insights alkalmazásmodell](./media/data-model/application-insights-data-model.png)

A következő típusú telemetriai adatok az alkalmazás végrehajtásának figyelésére szolgálnak. A következő három típust általában automatikusan gyűjti az Application Insights SDK a webalkalmazás-keretrendszerből:

* [**Kérelem**](data-model-request-telemetry.md) – Az alkalmazás által fogadott kérelem naplózásához létrehozva. Például az Application Insights webes SDK automatikusan létrehoz egy kérelem telemetriai elem minden HTTP-kérelmet, amely a webalkalmazás kap. 

    A **művelet** a végrehajtás azon szálai, amelyek feldolgozják a kérelmet. Más típusú műveletek, például az adatokat rendszeresen felíró függvény "ébresztése" figyeléséhez [is írhat kódot.](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest)  Minden műveletnek van egy azonosítója. Ez az azonosító, amely az alkalmazás a kérelem feldolgozása során létrehozott összes telemetriai adatok [csoportosítására](../../azure-monitor/app/correlation.md) használható. Minden művelet sikeres vagy sikertelen, és időtartama.
* [**Kivétel**](data-model-exception-telemetry.md) – általában egy olyan kivételt jelöl, amely egy művelet sikertelensét okozza.
* [**Függőség**](data-model-dependency-telemetry.md) – Az alkalmazásból egy külső szolgáltatáshoz vagy tárolóhoz, például REST API-hoz vagy SQL-hez irányuló hívást jelöli. A ASP.NET az SQL-be irányuló `System.Data`függőségi hívásokat a. A HTTP-végpontok hívásait a `System.Net`határozza meg. 

Az Application Insights három további adattípust biztosít az egyéni telemetriai adatokhoz:

* [Trace](data-model-trace-telemetry.md) - használt akár közvetlenül, vagy egy adapteren keresztül végrehajtására diagnosztikai naplózás `Log4Net` segítségével `System.Diagnostics`műszerkeretrendszer, amely ismerős az Ön számára, például vagy .
* [Esemény](data-model-event-telemetry.md) – általában a szolgáltatással való felhasználói interakció rögzítésére, a használati minták elemzésére szolgál.
* [Metrika](data-model-metric-telemetry.md) – időszakos skaláris mérések jelentéséhez használható.

Minden telemetriai elem meghatározhatja a [környezeti információkat,](data-model-context.md) például az alkalmazás verzióját vagy a felhasználói munkamenet-azonosítót. A környezet olyan erősen beírt mezők készlete, amelyek feloldanak bizonyos forgatókönyveket. Ha az alkalmazás verziója megfelelően inicializálva van, az Application Insights észleli az új minták at az alkalmazás viselkedése korrelál az újratelepítés. A munkamenet-azonosító segítségével kiszámíthatja a kimaradást vagy a felhasználókra gyakorolt problémát. Bizonyos sikertelen függőségek, hibakövetés vagy kritikus kivétel esetén a munkamenet-azonosítóértékek eltérő számának kiszámítása jó hatással jár.

Az Application Insights telemetriai modell határozza meg a módját, hogy [korrelálja](../../azure-monitor/app/correlation.md) a telemetriai adatokat a művelet, amelynek része. Például egy kérés sql-adatbázis-hívásokat és rögzített diagnosztikai adatokat hozhat. Beállíthatja a korrelációs környezetben azokat a telemetriai elemeket, amelyek összekötik azt a kérelem telemetriai adatok.

## <a name="schema-improvements"></a>Séma fejlesztései

Az Application Insights-adatmodell egy egyszerű és egyszerű, mégis hatékony módja az alkalmazás telemetriai adatainak modellezésére. Arra törekszünk, hogy a modell egyszerű és karcsú maradjon, hogy támogassa az alapvető forgatókönyveket, és lehetővé tegye a séma kiterjesztését a speciális használatra.

Adatmodell vagy séma problémák és javaslatok jelentéséhez használja a GitHub [ApplicationInsights-Home](https://github.com/Microsoft/ApplicationInsights-Home/issues) tárház.

## <a name="next-steps"></a>További lépések

- [Egyéni telemetriai adatok írása](../../azure-monitor/app/api-custom-events-metrics.md)
- További információ a [telemetria meghosszabbításáról és szűréséről.](../../azure-monitor/app/api-filtering-sampling.md)
- A [mintavételezés](../../azure-monitor/app/sampling.md) segítségével minimalizálhatja az adatmodell en alapuló telemetriai adatok mennyiségét.
- Tekintse meg az Application Insights által támogatott [platformokat.](../../azure-monitor/app/platforms.md)
