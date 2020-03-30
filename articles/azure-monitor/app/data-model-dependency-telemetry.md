---
title: Azure Monitor Application Insights függőségi adatmodell
description: Application Insights-adatmodell a függőségi telemetriai adatokhoz
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: ba0d848904d1ba885dc53e2941953d8dfb4864cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671919"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Függőségi telemetria: Application Insights-adatmodell

Függőségi telemetriai adatok (az [Application Insights)](../../azure-monitor/app/app-insights-overview.md)a figyelt összetevő egy távoli összetevővel, például SQL-sel vagy HTTP-végpontdal való interakcióját jelöli.

## <a name="name"></a>Név

A függőségi hívással kezdeményezett parancs neve. Alacsony számossági érték. Ilyenek például a tárolt eljárásnév és URL-elérési út sablon.

## <a name="id"></a>ID (Azonosító)

Egy függőségi híváspéldány azonosítója. A függőségi hívásnak megfelelő kérelem telemetriai elemével való korrelációhoz használható. További információ: [Korrelációs](../../azure-monitor/app/correlation.md) oldal.

## <a name="data"></a>Adatok

A függőségi hívás által kezdeményezett parancs. Ilyenek például az SQL utasítás és a HTTP URL-cím az összes lekérdezési paraméterrel.

## <a name="type"></a>Típus

Függőségtípus neve. Alacsony számossági érték a függőségek logikai csoportosításához és más mezők, például a commandName és a resultCode értelmezéséhez. Ilyenek például az SQL, az Azure-tábla és a HTTP.

## <a name="target"></a>Cél

Függőségi hívás célhelye. Ilyenpéldául a kiszolgáló neve, az állomáscíme. További információ: [Korrelációs](../../azure-monitor/app/correlation.md) oldal.

## <a name="duration"></a>Időtartam

A kérelem időtartama `DD.HH:MM:SS.MMMMMM`formátumban: . . Kevesebb, mint `1000` napok.

## <a name="result-code"></a>Eredménykód

Függőségi hívás eredménykódja. Ilyenek például az SQL hibakód és a HTTP-állapotkód.

## <a name="success"></a>Sikeres

A sikeres vagy sikertelen hívás jelzése.

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mérések

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>További lépések

- Függőségkövetés beállítása a [.NET](../../azure-monitor/app/asp-net-dependencies.md)rendszerhez.
- Függőségkövetés beállítása [Java](../../azure-monitor/app/java-agent.md)beállításhoz.
- [Egyéni függőségi telemetriai adatok írása](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Tekintse meg az Application Insights-típusok és adatmodell [adatmodelljét.](data-model.md)
- Tekintse meg az Application Insights által támogatott [platformokat.](../../azure-monitor/app/platforms.md)
