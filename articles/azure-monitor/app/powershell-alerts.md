---
title: A PowerShell használatával riasztásokat állíthat be az Application Insightsban | Microsoft dokumentumok
description: Automatizálhatja az Application Insights konfigurációját, hogy e-maileket kapjon a metrikamódosításokkal kapcsolatban.
ms.topic: conceptual
ms.date: 10/31/2016
ms.openlocfilehash: c19cb43d08b44b55c786e750e64a83e6f0c67381
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669845"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>A PowerShell használata riasztások beállításához az Application Insights szolgáltatásban

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Automatizálhatja a [riasztások konfigurációját](../../azure-monitor/app/alerts.md) az [Application Insights](../../azure-monitor/app/app-insights-overview.md)ban.

Ezenkívül beállíthatja a [webhookokat, hogy automatizálhassa a riasztásra adott válaszát.](../../azure-monitor/platform/alerts-webhooks.md)

> [!NOTE]
> Ha egyszerre szeretne erőforrásokat és riasztásokat létrehozni, fontolja meg [egy Azure Resource Manager-sablon használatát.](powershell.md)

## <a name="one-time-setup"></a>Egyszeri beállítás
Ha korábban még nem használta a PowerShellt az Azure-előfizetésével:

Telepítse az Azure Powershell-modult arra a számítógépre, ahol futtatni szeretné a parancsfájlokat.

* Telepítse a [Microsoft Web Platform Installer (v5 vagy újabb)](https://www.microsoft.com/web/downloads/platform.aspx)programot.
* A Microsoft Azure Powershell telepítéséhez használja

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz
Indítsa el az Azure PowerShellt, és [csatlakozzon az előfizetéséhez:](/powershell/azure/overview)

```powershell

    Add-AzAccount
```


## <a name="get-alerts"></a>Értesítések beszerezni
    Get-AzAlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Riasztás hozzáadása
    Add-AzMetricAlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US" // must be East US at present
     -RuleType Metric



## <a name="example-1"></a>1. példa
E-mail nekem, ha a szerver válasza http-kérések, átlagosan több mint 5 perc, lassabb, mint 1 másodperc. Az Application Insights-erőforrás neve IceCreamWebApp, és a Fabrikam erőforráscsoportban található. Én vagyok az Azure-előfizetés tulajdonosa.

A GUID az előfizetésazonosító (nem az alkalmazás instrumentation kulcsa).

    Add-AzMetricAlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## <a name="example-2"></a>2. példa
Van egy alkalmazásom, amelyben [a TrackMetric()](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) segítségével jelentek egy "salesPerHour" nevű metrikát. Küldjön egy e-mailt a kollégáimnak, ha a "salesPerHour" 100 alá esik, átlagosan több mint 24 óra.

    Add-AzMetricAlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

Ugyanez a szabály használható a jelentett metrikához egy másik követési hívás, például a TrackEvent vagy a trackPageView [mérési paraméterének](../../azure-monitor/app/api-custom-events-metrics.md#properties) használatával.

## <a name="metric-names"></a>Metrikák nevei
| Metrika neve | Képernyő neve | Leírás |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Böngészőkivételek |A böngészőben felnem fogott kivételek száma. |
| `basicExceptionServer.count` |Kiszolgálói kivételek |Az alkalmazás által okozott nem kezelt kivételek száma |
| `clientPerformance.clientProcess.value` |Ügyfél feldolgozási ideje |A dokumentum utolsó bájtjának fogadása és a DOM betöltése között töltött idő. Előfordulhat, hogy az Async-kérelmek feldolgozása még folyamatban van. |
| `clientPerformance.networkConnection.value` |Oldalbetöltési hálózat csatlakozási ideje |A böngésző által a hálózathoz való csatlakozáshoz szükséges idő. 0 lehet, ha gyorsítótárazva van. |
| `clientPerformance.receiveRequest.value` |Válaszidő fogadása |A böngésző és a válasz fogadásának megkezdése között töltött idő. |
| `clientPerformance.sendRequest.value` |Kérés ideje elküldése |A böngésző által a kérelem küldéséhez szükséges idő. |
| `clientPerformance.total.value` |A böngészőoldal betöltési ideje |A felhasználói kérelemtől a DOM-ig, a stíluslapoktól, a szkriptektől és a képektől kezdve betöltődnek. |
| `performanceCounter.available_bytes.value` |Igénybe vehető memória |A fizikai memória azonnal elérhető egy folyamathoz vagy a rendszer használatához. |
| `performanceCounter.io_data_bytes_per_sec.value` |Io-ráta feldolgozása |Fájlokba, hálózatba és eszközökbe másodpercenként imitálta és írt bájtok száma. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |kivételarány |Kivételek másodpercenként. |
| `performanceCounter.percentage_processor_time.value` |Folyamat PROCESSZOR |A processzor által az alkalmazásfolyamathoz használt összes folyamatszál eltelt idejének százalékos aránya. |
| `performanceCounter.percentage_processor_total.value` |Processzor idő |A processzor által nem tétlen szálakban töltött idő hány százaléka. |
| `performanceCounter.process_private_bytes.value` |Magánbájtok feldolgozása |A memória kizárólag a figyelt alkalmazás folyamataihoz van rendelve. |
| `performanceCounter.request_execution_time.value` |ASP.NET kérelem végrehajtási ideje |A legutóbbi kérelem végrehajtási ideje. |
| `performanceCounter.requests_in_application_queue.value` |ASP.NET kérelmek végrehajtási várólistában |Az alkalmazáskérelem-várólista hossza. |
| `performanceCounter.requests_per_sec.value` |ASP.NET kérelem aránya |Az alkalmazáshoz érkező összes kérelem másodpercenkénti aránya ASP.NET.Rate of all requests to the application per second from ASP.NET. |
| `remoteDependencyFailed.durationMetric.count` |Függőségi hibák |A kiszolgálóalkalmazás által a külső erőforrásokhoz kezdeményezett sikertelen hívások száma. |
| `request.duration` |Szerverválaszidő |A HTTP-kérelem fogadása és a válasz elküldésének befejezése között töltött idő. |
| `request.rate` |Kérelem aránya |Az alkalmazáshoz érkező összes kérelem másodpercenkénti aránya. |
| `requestFailed.count` |Sikertelen kérelmek |A válaszkódot eredményező HTTP-kérelmek száma >= 400 |
| `view.count` |Oldalnézetek |A weboldalhoz benyújtott ügyfélfelhasználói kérelmek száma. A szintetikus forgalom kivan szűrve. |
| {az egyéni metrika neve} |{A metrika neve} |A [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) által jelentett metrikus érték vagy a [követési hívás mérési paraméterében.](../../azure-monitor/app/api-custom-events-metrics.md#properties) |

A metrikákat különböző telemetriai modulok küldik:

| Metrikus csoport | Gyűjtő modul |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>Nézd |[Böngésző JavaScript](../../azure-monitor/app/javascript.md) |
| performanceCounter |[Teljesítmény](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[Függőség](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| Kérés<br/>kérésFailed |[Kiszolgálói kérelem](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhookok
[Automatizálhatja a riasztásra adott válaszát.](../../azure-monitor/platform/alerts-webhooks.md) Az Azure egy ön által választott webcímet hív meg, amikor riasztás tetszést ad.

## <a name="see-also"></a>Lásd még
* [Parancsfájl az Application Insights konfigurálásához](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically)
* [Alkalmazáselemzési és webes teszterőforrások létrehozása sablonokból](powershell.md)
* [A Microsoft Azure diagnosztika és az Application Insights összekapcsolásának automatizálása](powershell-azure-diagnostics.md)
* [A riasztásra adott válasz automatizálása](../../azure-monitor/platform/alerts-webhooks.md)
