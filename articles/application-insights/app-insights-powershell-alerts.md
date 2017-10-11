---
title: "Riasztások beállítása az Application Insights a Powershell használatával |} Microsoft Docs"
description: "Az Application Insights metrika változásaira vonatkozó e-mailek eléréséhez automatizálásához."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 05d6a9e0-77a2-4a35-9052-a7768d23a196
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: bwren
ms.openlocfilehash: 64675c51abf80daa3a55220f910aa8fdee1042ca
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>A PowerShell használata riasztások beállításához az Application Insights szolgáltatásban
Automatizálható a konfigurációja [riasztások](app-insights-alerts.md) a [Application Insights](app-insights-overview.md).

Emellett képes [webhookok automatizálhatja a válasz riasztás beállítása](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

> [!NOTE]
> Ha azt szeretné, erőforrások és a riasztások létrehozásához egy időben, érdemes lehet [Azure Resource Manager-sablonnal](app-insights-powershell.md).
>
>

## <a name="one-time-setup"></a>Egyszeri beállítása
Ha még nem használta PowerShell Azure-előfizetéséhez előtt:

Az Azure Powershell modul telepítése a számítógépen, ahová a parancsfájlok futtatása.

* Telepítés [Microsoft Webplatform-telepítővel (v5 vagy magasabb)](http://www.microsoft.com/web/downloads/platform.aspx).
* Telepítse a Microsoft Azure Powershell használatával

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz
Indítsa el az Azure PowerShell és [csatlakozás az előfizetéshez](/powershell/azure/overview):

```PowerShell

    Add-AzureAccount
```


## <a name="get-alerts"></a>Riasztásokat kaphat
    Get-AzureAlertRmRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Riasztások hozzáadása
    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
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
E-mailt kérek a HTTP-kérelmekre, átlagosan több mint 5 perc, a kiszolgáló válaszát lassabb, mint 1 másodperc esetén is. Az Application Insights-erőforrás neve IceCreamWebApp, és a Fabrikam az erőforráscsoporthoz tartozik. Az Azure-előfizetés tulajdonosa vagyok.

A GUID-azonosító az előfizetés-azonosító (nem az alkalmazás instrumentation kulcsa).

    Add-AlertRule -Name "slow responses" `
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
Egy alkalmazás, amelyben használni kell [trackmetric() függvény](app-insights-api-custom-events-metrics.md#trackmetric) "salesPerHour." nevű metrika bejelenteni Küldjön egy e-mailt, a munkatársak számára, ha "salesPerHour" 100, alá süllyed átlagosan 24 órán át.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

A metrika a jelentett ugyanaz a szabály használható az [mérési paraméter](app-insights-api-custom-events-metrics.md#properties) TrackEvent vagy trackPageView például egy másik nyomkövetési hívás.

## <a name="metric-names"></a>Metrika neve
| Metrika neve | Képernyő neve | Leírás |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Böngészőkivételek |A böngészőben fellépő nem kezelt kivételek száma. |
| `basicExceptionServer.count` |Kivételek |Az alkalmazás által nem kezelt kivételek száma |
| `clientPerformance.clientProcess.value` |Ügyfél feldolgozási ideje |Egy dokumentum utolsó bájtját fogadását, amíg a DOM betöltése között eltelt idő. Aszinkron kérelmek feldolgozása még folyamatban lehet is. |
| `clientPerformance.networkConnection.value` |Lapbetöltési hálózati csatlakozás ideje |A böngészőben a hálózathoz való kapcsolódáshoz szükséges idő. 0 akkor lehet, ha a gyorsítótárba. |
| `clientPerformance.receiveRequest.value` |A fogadó válaszidő |A böngésző küldött kérelmek válaszideje kiindulási között eltelt idő. |
| `clientPerformance.sendRequest.value` |A küldési kérelem ideje |A kérés küldése böngésző szükséges időt. |
| `clientPerformance.total.value` |Böngésző lapbetöltési ideje |Felhasználói kérelemtől a DOM, a stíluslapok, parancsfájlok és a képeket idő töltődnek be. |
| `performanceCounter.available_bytes.value` |Rendelkezésre álló memória |A folyamat vagy a rendszer általi használatra azonnal elérhető fizikai memória. |
| `performanceCounter.io_data_bytes_per_sec.value` |Feldolgozási IO sebessége |Összes bájt / mp olvashatók és írhatók a fájlokon, hálózaton és eszközökön. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |Kivétel arány |Kivételek száma másodpercenként. |
| `performanceCounter.percentage_processor_time.value` |CPU folyamat |Az alkalmazások folyamat végrehajtása utasításokat a processzor által használt összes Folyamatszál eltelt idő hány százalékát. |
| `performanceCounter.percentage_processor_total.value` |Processzoridő |A processzor nem üresjárati szálak futtatásával töltött idő százalékos aránya |
| `performanceCounter.process_private_bytes.value` |Folyamat saját bájtok |Kizárólag a megfigyelt alkalmazás rendelt memória. |
| `performanceCounter.request_execution_time.value` |ASP.NET-kérelem végrehajtási ideje |A legutóbbi kérelem végrehajtási ideje. |
| `performanceCounter.requests_in_application_queue.value` |ASP.NET-kérelem végrehajtásának várólista |Az alkalmazás kérelem-várólistájának hossza. |
| `performanceCounter.requests_per_sec.value` |ASP.NET-kérelmek gyakorisága |Az ASP.NET az alkalmazás másodpercenkénti összes kérelmek száma. |
| `remoteDependencyFailed.durationMetric.count` |Függőségi hiba |A kiszolgáló alkalmazás által külső erőforrások felé indított sikertelen hívások száma. |
| `request.duration` |Kiszolgáló válaszideje |Egy HTTP-kérelem fogadása és a válasz küldésének befejezése között eltelt idő. |
| `request.rate` |Kérelmek gyakorisága |Az alkalmazás másodpercenkénti összes kérelmek arányát. |
| `requestFailed.count` |Sikertelen kérelmek |HTTP-kérelmek száma válaszkódot eredményező > = 400 |
| `view.count` |Lapmegtekintések |A weblap ügyfélfelhasználói kéréseinek száma. Szintetikus forgalom ki van szűrve. |
| {az egyéni metrika neve} |{A metrika neve} |A metrika értékét által jelentett [TrackMetric](app-insights-api-custom-events-metrics.md#trackmetric) vagy a [mérések paraméter követési hívásának](app-insights-api-custom-events-metrics.md#properties). |

A metrikák különféle telemetriai modulok által küldött:

| Metrika csoport | Adatgyűjtő modulja |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>megtekintés |[Böngésző JavaScript](app-insights-javascript.md) |
| performanceCounter |[Teljesítmény](app-insights-configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[Függőség](app-insights-configuration-with-applicationinsights-config.md) |
| kérelem,<br/>requestFailed |[Kiszolgálói kérelem](app-insights-configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>webhook
Is [automatizálhatja a válasz riasztás](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Riasztást hoz létre Azure egy webcímet az Ön által választott fog hívni.

## <a name="see-also"></a>Lásd még:
* [Konfigurálhatja az Application Insights-parancsprogramot](app-insights-powershell-script-create-resource.md)
* [Az Application Insights és webes teszt erőforrások létrehozása sablonból](app-insights-powershell.md)
* [Az Application Insights a Microsoft Azure Diagnostics kapcsoló automatizálásához](app-insights-powershell-azure-diagnostics.md)
* [A válasz riasztás automatizálásához](../monitoring-and-diagnostics/insights-webhooks-alerts.md)
