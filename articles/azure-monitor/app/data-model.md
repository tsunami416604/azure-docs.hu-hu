---
title: Az Azure Application Insights Telemetria adatmodellje | Microsoft Docs
description: Az Application Insights adatmodell áttekintése
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/14/2019
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 50109d7ba4688606a5a4f1b813d15d78636b7817
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311783"
---
# <a name="application-insights-telemetry-data-model"></a>Application Insights telemetria adatmodell

Az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) telemetria küld a webalkalmazásból a Azure Portalba, így elemezheti az alkalmazás teljesítményét és használatát. A telemetria modell úgy van szabványosítva, hogy platform-és nyelvtől független monitorozást lehessen létrehozni. 

A Application Insights által gyűjtött adatok ez a tipikus alkalmazás-végrehajtási minta:

![Application Insights alkalmazás modellje](./media/data-model/application-insights-data-model.png)

Az alkalmazás végrehajtásának figyeléséhez a következő típusú telemetria használják. A következő három típust általában automatikusan gyűjti a Application Insights SDK a webalkalmazási keretrendszerből:

* [**Kérelem**](data-model-request-telemetry.md) – az alkalmazás által fogadott kérelem naplózása. A Application Insights web SDK például automatikusan létrehoz egy kérelem telemetria-elemét minden olyan HTTP-kérelem esetében, amelyet a webalkalmazás fogad. 

    A **művelet** a kérelmeket feldolgozó végrehajtási szálakat dolgozza fel. Más típusú műveletek figyelésére is [kódot írhat](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) , például egy olyan webes feladat vagy függvény felébresztését, amely rendszeresen dolgozza fel az adatfeldolgozást.  Minden művelet rendelkezik AZONOSÍTÓval. Ez az azonosító a kérelem feldolgozásakor az alkalmazás által létrehozott összes telemetria [csoportosítására](../../azure-monitor/app/correlation.md) használható. Minden művelet sikeres vagy sikertelen, és időtartammal rendelkezik.
* [**Kivétel**](data-model-exception-telemetry.md) – a művelet általában egy olyan kivételt jelöl, amely egy műveletet eredményez.
* [**Függőség**](data-model-dependency-telemetry.md) – az alkalmazástól érkező hívást egy külső szolgáltatásra vagy tárhelyre, például egy REST API vagy SQL-példányra hívja. A ASP.NET-ben az SQL-függőségi hívások `System.Data` értékkel vannak meghatározva. A HTTP-végpontokra irányuló hívásokat a `System.Net` határozza meg. 

A Application Insights három további adattípust biztosít az egyéni telemetria:

* [Nyomkövetés](data-model-trace-telemetry.md) – közvetlenül vagy adapteren keresztül, a diagnosztikai naplózás megvalósításához olyan rendszerállapot-keretrendszer használatával, amely ismerős, például `Log4Net` vagy `System.Diagnostics`.
* [Esemény](data-model-event-telemetry.md) – jellemzően a szolgáltatással való felhasználói interakció rögzítésére használatos a használati minták elemzése érdekében.
* [Metrika](data-model-metric-telemetry.md) – az időszakos skaláris mérések jelentésére szolgál.

Minden telemetria-eleme meghatározhatja a [környezeti adatokat](data-model-context.md) , például az alkalmazás vagy a felhasználói munkamenet azonosítóját. A kontextus olyan szigorúan beírt mezők összessége, amelyek feloldják bizonyos helyzeteket. Ha az alkalmazás verziója megfelelően van inicializálva, a Application Insights az újbóli üzembe helyezéssel összefügg az alkalmazás viselkedésében rejlő új mintázatok észlelésével. A munkamenet-azonosító a leállás kiszámításához, vagy a probléma a felhasználókra gyakorolt hatásának kiszámításához használható. Bizonyos sikertelen függőségek, a hibák nyomon követése vagy a kritikus kivételek eltérő számának kiszámításával jól megértettük a hatását.

Application Insights telemetria modell meghatározza a telemetria azon műveletének a [módját, amelynek](../../azure-monitor/app/correlation.md) a része. Egy kérelem például létrehozhat egy SQL Database hívást és a rögzített diagnosztikai adatokat. Megadhatja azon telemetria-elemek korrelációs környezetét, amelyek visszakötik a kérelem telemetria.

## <a name="schema-improvements"></a>Sémák fejlesztése

A Application Insights adatmodell egyszerű és alapszintű, mégis hatékony módja az alkalmazás telemetria modellezésének. Arra törekszünk, hogy a modell egyszerű és karcsú legyen, hogy támogassa az alapvető forgatókönyveket, és lehetővé tegye a séma speciális használatra való kiterjesztését.

Az adatmodell vagy séma problémáinak és javaslatainak jelentéséhez használja a GitHub [ApplicationInsights-Home](https://github.com/Microsoft/ApplicationInsights-Home/issues) repositoryt.

## <a name="next-steps"></a>Következő lépések

- [Egyéni telemetria írása](../../azure-monitor/app/api-custom-events-metrics.md)
- Ismerje meg [, hogyan bővítheti és szűrheti a telemetria](../../azure-monitor/app/api-filtering-sampling.md).
- A [mintavétel](../../azure-monitor/app/sampling.md) használatával minimálisra csökkenthető az adatmodellen alapuló telemetria mennyisége.
- Tekintse meg Application Insights által támogatott [platformokat](../../azure-monitor/app/platforms.md) .
