---
title: Application Insights függőségi adatmodell Azure Monitor
description: Application Insights adatmodell a függőségi telemetria
ms.topic: conceptual
ms.date: 04/17/2017
ms.reviewer: sergkanz
ms.openlocfilehash: ba0d848904d1ba885dc53e2941953d8dfb4864cf
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77671919"
---
# <a name="dependency-telemetry-application-insights-data-model"></a>Függőségi telemetria: Application Insights adatmodell

A függőségi telemetria ( [Application Insights](../../azure-monitor/app/app-insights-overview.md)) a figyelt összetevő és egy távoli összetevő, például az SQL vagy egy http-végpont interakcióját jelöli.

## <a name="name"></a>Name

A függőségi hívással kezdeményezett parancs neve. Alacsony kardinális érték. Ilyenek például a tárolt eljárás neve és az URL-cím elérési útja sablon.

## <a name="id"></a>ID

A függőségi hívás példányának azonosítója. A függőségi híváshoz tartozó telemetria-elemmel való korrelációhoz használatos. További információ: [korrelációs](../../azure-monitor/app/correlation.md) oldal.

## <a name="data"></a>Adatok

A függőségi hívás által kezdeményezett parancs. Ilyenek például az SQL-utasítások és a HTTP URL-címek az összes lekérdezési paraméterrel.

## <a name="type"></a>Típus

Függőségi típus neve A függőségek logikai csoportosításának és más mezők, például a commandName és a resultCode értelmezésének alacsony kardinális értéke. Ilyenek például az SQL, az Azure Table és a HTTP.

## <a name="target"></a>Cél

A függőségi hívás célhelye. Ilyenek például a kiszolgáló neve, a gazdagép címe. További információ: [korrelációs](../../azure-monitor/app/correlation.md) oldal.

## <a name="duration"></a>Időtartam

Kérelem időtartama (formátum `DD.HH:MM:SS.MMMMMM` ):. Rövidebbnek kell lennie, mint `1000` nap.

## <a name="result-code"></a>Eredménykód

A függőségi hívás eredményének kódja. Ilyenek például az SQL-hibakódok és a HTTP-állapotkód.

## <a name="success"></a>Sikeres

A sikeres vagy sikertelen hívás megjelölése.

## <a name="custom-properties"></a>Egyéni tulajdonságok

[!INCLUDE [application-insights-data-model-properties](../../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Egyéni mérések

[!INCLUDE [application-insights-data-model-measurements](../../../includes/application-insights-data-model-measurements.md)]


## <a name="next-steps"></a>További lépések

- Függőségi követés beállítása a [.net](../../azure-monitor/app/asp-net-dependencies.md)-hez.
- A függőség nyomon követésének beállítása a [Javához](../../azure-monitor/app/java-agent.md).
- [Egyéni függőségi telemetria írása](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Lásd: [adatmodell](data-model.md) Application Insights típusokhoz és adatmodellekhez.
- Tekintse meg Application Insights által támogatott [platformokat](../../azure-monitor/app/platforms.md) .
