---
title: Az Azure Application Insights Telemetria adatmodellt |} A Microsoft Docs
description: Az Application Insights data model áttekintése
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: da59fef660db15a646fcf1077e6f649a0de678d5
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47092232"
---
# <a name="application-insights-telemetry-data-model"></a>Application Insights telemetria adatmodell

[Az Azure Application Insights](app-insights-overview.md) telemetriai adatokat küld a webalkalmazásból az Azure Portalra, így elemezheti a teljesítményét és használatát az alkalmazás. A telemetriai adatok modell szabványosított, hogy lehet létrehozni a platform és a figyelés nyelvtől független legyen. 

Az Application Insights által gyűjtött adatokat a jellemző végrehajtási alkalmazásminta modellek:

![Application Insights-alkalmazásmodell](./media/application-insights-data-model/application-insights-data-model.png)

A következő típusú telemetriát az alkalmazás végrehajtását figyelésére használható. A következő három típusú általában automatikusan gyűjti össze az Application Insights SDK-t, a webalkalmazás-keretrendszer:

* [**Kérelem** ](application-insights-data-model-request-telemetry.md) – az alkalmazás által fogadott kérelem bejelentkezni generált. Például az Application Insights webes SDK automatikusan előállít egy kérelemben szereplő telemetriai elem minden HTTP-kérelem, amely megkapja a webalkalmazás. 

    Egy **művelet** végrehajtási, hogy a kérelmet feldolgozó szál van. Emellett [kód írása](app-insights-api-custom-events-metrics.md#trackrequest) más típusú műveletet, figyeléséhez, mint például egy "felébresztéséhez" egy webes feladat, vagy nem működik, amely rendszeres időközönként feldolgozza az adatokat.  Minden művelet-azonosítóval rendelkezik. Ezt az Azonosítót, amely az alábbiakra használhatók [csoport](application-insights-correlation.md) összes telemetriai adat, míg az alkalmazás a kérés feldolgozása folyamatban van. Minden művelet vagy sikeres vagy sikertelen lesz, és idő időtartama.
* [**Kivétel** ](application-insights-data-model-exception-telemetry.md) – általában a egy művelet meghiúsulását okozó kivételt jelenti.
* [**Függőségi** ](application-insights-data-model-dependency-telemetry.md) -hívást az alkalmazásból egy külső szolgáltatás vagy a REST API-t vagy az SQL, például jelöli. Az ASP.NET, a következőre irányuló függőséghívás SQL által meghatározott `System.Data`. HTTP-végpontokra irányuló hívásokat által meghatározott `System.Net`. 

Az Application Insights további adattípusok egyéni telemetriát biztosít:

* [Nyomkövetési](application-insights-data-model-trace-telemetry.md) – vagy közvetlenül használni, vagy keresztül az adapter megvalósításához a diagnosztikai naplózás használatával egy rendszerállapot-keretrendszer, amely ismerős lehet, mint például `Log4Net` vagy `System.Diagnostics`.
* [Esemény](application-insights-data-model-event-telemetry.md) – felhasználói interakció a szolgáltatáshoz, a használati minták elemzése rögzítéséhez használt.
* [A metrika](application-insights-data-model-metric-telemetry.md) – az időszakos skaláris mérések jelentést.

Minden telemetriai elem adhatja meg a [környezeti információkat](application-insights-data-model-context.md) például alkalmazás verziója vagy a felhasználói munkamenet-azonosító. Környezet a típusos mezők halmaza, amelyek bizonyos forgatókönyvek feloldja. Alkalmazásverzió megfelelően van inicializálva, az Application Insights képes észlelni a új mintákat korrelált újbóli üzembe helyezés az alkalmazás működésében. A munkamenet-azonosító a szolgáltatáskimaradás elhárítása után vagy egy problémát okozzon a felhasználóknak kiszámításához használható. Függőségi munkamenet-azonosító értékek eltérők száma az egyes kiszámítása nem sikerült, hiba történt a nyomkövetési vagy kritikus kivétel kínál jól ismerik a hatást.

Application Insights telemetria modell módját határozzák meg [összekapcsolását](application-insights-correlation.md) telemetriai adatokat a műveletet, amely egy részét képezi. Például egy kérelem egy SQL Database-hívásokat indíthatna és rögzített diagnosztikai adatok. Vissza a kérelmek telemetriai adatai rendelését telemetriai elemeket korrelációs kontextusa állíthatja be.

## <a name="schema-improvements"></a>Séma fejlesztései

Application Insights adatmodell módja egy egyszerű és egyszerű, mégis hatékony modellezésére a telemetriai. Igyekszünk tartani a modell, egyszerű és slim alapvető alkalmazási helyzetet támogatásához, és lehetővé teszi a speciális használható a séma kiterjesztését.

Data modell és séma problémák és javaslatok használata a GitHub jelentéséhez [ApplicationInsights – kezdőlap](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema) tárház.

## <a name="next-steps"></a>További lépések

- [Egyéni telemetriát írhat](app-insights-api-custom-events-metrics.md)
- Ismerje meg, hogyan [bővítése és szűrőtelemetria](app-insights-api-filtering-sampling.md).
- Használat [mintavételi](app-insights-sampling.md) mennyiségű telemetriai adat modellen alapuló minimalizálása érdekében.
- Tekintse meg [platformok](app-insights-platforms.md) Application Insights által támogatott.
