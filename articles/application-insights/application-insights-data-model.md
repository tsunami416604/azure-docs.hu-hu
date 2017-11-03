---
title: Az Azure Application Insights Telemetria adatmodell |} Microsoft Docs
description: "Application Insights az modell áttekintése"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/25/2017
ms.author: mbullwin
ms.openlocfilehash: b14eea46e773a4b92ba20cd3121cd258f86307c9
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="application-insights-telemetry-data-model"></a>Application Insights telemetria adatmodell

[Az Azure Application Insights](app-insights-overview.md) telemetriai küldi, hogy a teljesítmény- és az alkalmazás használati elemezheti a webalkalmazás az Azure-portálon. A telemetriai adatok modell szabványosított, hogy lehet létrehozni a platform és nyelvfüggetlen figyelését. 

Az Application Insights által gyűjtött adatokat a tipikus végrehajtási alkalmazásminta modellek:

![Application Insights Alkalmazásmodellt.](./media/application-insights-data-model/application-insights-data-model.png)

A következő típusú telemetriai figyeléséhez a az alkalmazás végrehajtása során használt. A következő három típusa általában automatikusan begyűjti az Application Insights SDK által a webes alkalmazás-keretrendszer:

* [**Kérelem** ](application-insights-data-model-request-telemetry.md) – generált való bejelentkezéshez az alkalmazás által fogadott kérelmet. Például az Application Insights webes SDK automatikusan hoz létre, amely megkapja a webes alkalmazás egyes HTTP-kérelem a kérelem telemetriai elem. 

    Egy **művelet** szálainak állapotát, amely feldolgozza a kérést végrehajtó van. Emellett [írhat kódot](app-insights-api-custom-events-metrics.md#trackrequest) más típusú műveletet, figyeléséhez, mint például a "ébresztésére" egy a web feladat, vagy nem működik, amely rendszeres időközönként adatokat dolgozza fel.  Egyes műveletek rendelkezik azonosítóval. Ezt az Azonosítót, amely a következőkre használható [csoport](application-insights-correlation.md) összes telemetriai adat jelentkezik, míg az alkalmazás a kérelem feldolgozása. Egyes műveletek vagy sikeres vagy sikertelen lesz, és idő időtartama.
* [**Kivétel** ](application-insights-data-model-exception-telemetry.md) -általában jelenti. a művelet sikertelen okozó kivételt.
* [**A függőségi** ](application-insights-data-model-dependency-telemetry.md) -hívás jelenti az alkalmazásból. egy külső szolgáltatás vagy a tárhelyen, többek között a REST API-t vagy az SQL. Az ASP.NET, függőségi hívásokat SQL határozzák `System.Data`. HTTP-végpontokról hívások által meghatározott `System.Net`. 

Az Application Insights három további adattípusok egyéni telemetriai adatokat ismerteti:

* [Nyomkövetési](application-insights-data-model-trace-telemetry.md) - használt vagy közvetlenül, vagy egy diagnosztikai naplózás végrehajtásához adapteren keresztül egy instrumentation keretrendszer, amely ismerős lehet, mint például használatával `Log4Net` vagy `System.Diagnostics`.
* [Esemény](application-insights-data-model-event-telemetry.md) - általánosan használt rögzítheti a felhasználói beavatkozás a szolgáltatásba, a elemezheti a használati szokásokat.
* [A metrika](application-insights-data-model-metric-telemetry.md) - jelentés rendszeres skaláris mértékek használt.

Minden telemetriai elem határozhatja meg a [kontextusadatok](application-insights-data-model-context.md) például alkalmazás verziója vagy a felhasználói munkamenet-azonosító. A környezetben a mezők halmaza alapján szigorú típusmeghatározású, amely bizonyos forgatókönyvekben feloldja. Alkalmazásverzió megfelelően van inicializálva, az Application Insights képes észlelni új minták szorosan összefügg az újratelepítés alkalmazás viselkedésében. Munkamenet-azonosító a szolgáltatáskimaradás vagy egy problémát okozzon a felhasználóknak kiszámításához használható. Függőség munkamenet-azonosító értékek eltérők száma az egyes kiszámítása nem sikerült, hiba nyomkövetési vagy kritikus kivétel hatással beható ismerete biztosítja.

Application Insights telemetria modell módját határozzák meg [összefüggéseket](application-insights-correlation.md) telemetria bekapcsolásával a művelet, amely egy részét képezi. Például egy kérelem egy SQL-adatbázis hívás, és diagnosztikai adatait rögzíti. Beállíthatja a korrelációs környezetben összekötését azt vissza az a – kéréstelemetria telemetriai elemeket.

## <a name="schema-improvements"></a>Séma fejlesztései

Application Insights adatmodell használata a következő modellre: a telemetriai egyszerű és egyszerű, mégis hatékony módja. A modell egyszerű és alapvető forgatókönyvek támogatása céljából slim és speciális használatra a séma kibővítése lehetővé igyekeztünk.

Adatok modell és séma problémák és javaslatokat használja a Githubon jelentésének [ApplicationInsights-Home](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema) tárházba.

## <a name="next-steps"></a>Következő lépések

- [Egyéni telemetriai adatok írása](app-insights-api-custom-events-metrics.md)
- Megtudhatja, hogyan [kiterjesztése és a telemetriai adatok szűrése](app-insights-api-filtering-sampling.md).
- Használjon [mintavételi](app-insights-sampling.md) minimalizálása érdekében az adatmodellen alapuló telemetriai adatok mennyiségét.
- Tekintse meg [platformok](app-insights-platforms.md) Application Insights által támogatott.
