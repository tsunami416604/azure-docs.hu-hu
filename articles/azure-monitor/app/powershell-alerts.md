---
title: A Powershell használata riasztások beállításához az Application Insights |} A Microsoft Docs
description: Automatizálhatja a konfigurálását, az Application Insights metrika változásaira vonatkozó e-mailek beolvasása.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 05d6a9e0-77a2-4a35-9052-a7768d23a196
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/31/2016
ms.author: mbullwin
ms.openlocfilehash: b94136f063f9d4793ce4c8a03c17454df920af26
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117554"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>A PowerShell használata riasztások beállításához az Application Insights szolgáltatásban
Automatizálhatja a konfigurációját [riasztások](../../azure-monitor/app/alerts.md) a [Application Insights](../../azure-monitor/app/app-insights-overview.md).

Emellett akkor is [állítsa be a webhookok automatizálható a riasztás](../../azure-monitor/platform/alerts-webhooks.md).

> [!NOTE]
> Ha azt szeretné, erőforrások és a riasztások létrehozásához egy időben, érdemes lehet [egy Azure Resource Manager-sablonnal](powershell.md).
>
>

## <a name="one-time-setup"></a>Egyszeri beállítás
Ha még nem használta a Powershellt az Azure-előfizetésében előtt:

Azure Powershell-modul telepítéséhez a számítógépen, ahol szeretné a szkriptek futtatására.

* Telepítés [Microsoft Webplatform-telepítővel (5-ös verzióját vagy újabb)](https://www.microsoft.com/web/downloads/platform.aspx).
* Telepítse a Microsoft Azure Powershell használatával

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz
Indítsa el az Azure Powershellt, és [csatlakozzon az előfizetéséhez](/powershell/azure/overview):

```PowerShell

    Add-AzureRmAccount
```


## <a name="get-alerts"></a>Riasztások beolvasása
    Get-AzureRmAlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Riasztás hozzáadása
    Add-AzureRmMetricAlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
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
E-mailt kérek a kiszolgáló válasza a HTTP-kérelmekre, több mint 5 perc elteltével átlagolt lassabb, mint 1 másodperc esetén. Az Application Insights-erőforrás neve IceCreamWebApp, és a Fabrikam az erőforráscsoportban. Az Azure-előfizetés tulajdonosa vagyok.

A GUID azonosító az előfizetés-azonosító (nem a kialakítási kulcsot az alkalmazás).

    Add-AzureRmMetricAlertRule -Name "slow responses" `
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
Az alkalmazástól, amelyben van [a trackmetric() függvény](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) jelentéséhez "salesPerHour." nevű metrika Küldjön egy e-mailt a munkatársainak, ha 100, alá csökken "salesPerHour" átlagolt 24 órán át.

    Add-AzureRmMetricAlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

A metrika használata által jelentett ugyanaz a szabály használható a [mérési paraméter](../../azure-monitor/app/api-custom-events-metrics.md#properties) TrackEvent vagy trackPageView például egy másik nyomkövetési hívás.

## <a name="metric-names"></a>Metrika neve
| Metrika neve | Képernyő nevét | Leírás |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Böngészőkivételek |A böngészőben fellépő nem kezelt kivételek száma. |
| `basicExceptionServer.count` |Kiszolgálókivételek |Az alkalmazás által fellépő nem kezelt kivételek száma |
| `clientPerformance.clientProcess.value` |Ügyfél feldolgozási ideje |Az utolsó bájtig eltelt egy dokumentum fogadása, mindaddig, amíg a DOM betöltése között eltelt idő. Aszinkron kérelmek feldolgozása még folyamatban lehet is. |
| `clientPerformance.networkConnection.value` |Hálózati kapcsolat ideje lapbetöltéskor |A böngészőben a hálózathoz való csatlakozáshoz szükséges idő. 0 is lehet, ha a gyorsítótárba. |
| `clientPerformance.receiveRequest.value` |Válasz fogadási ideje |Kérelem küldése a válasz fogadására indítása böngésző között eltelt idő. |
| `clientPerformance.sendRequest.value` |Kérelem küldési ideje |Kérelem elküldéséhez a böngésző által igénybe vett idő. |
| `clientPerformance.total.value` |Böngésző lapbetöltési ideje |A felhasználói kérelemtől a DOM, a stíluslapok, a szkriptek és a képek betöltéséig eltelt idő. |
| `performanceCounter.available_bytes.value` |Elérhető memória |A folyamatot, vagy a rendszer általi használatra azonnal elérhető fizikai memória. |
| `performanceCounter.io_data_bytes_per_sec.value` |Folyamat átviteli sebessége |Olvasási-írási műveletek fájlokon, hálózaton és eszközökön összesen (bájt/mp). |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |kivételek sebessége |Kivételek száma másodpercenként. |
| `performanceCounter.percentage_processor_time.value` |Folyamat CPU |Az alkalmazások a folyamatot a processzor-végrehajtási utasításokat által használt összes szál futtatására fordított időt százalékos. |
| `performanceCounter.percentage_processor_total.value` |Processzoridő |A processzor nem üresjárati szálak futtatásával töltött százalékos időhányada. |
| `performanceCounter.process_private_bytes.value` |Folyamat saját bájtjai |A kizárólag a megfigyelt alkalmazás folyamataihoz hozzárendelt memória mennyisége. |
| `performanceCounter.request_execution_time.value` |ASP.NET-kérelem végrehajtási ideje |A legutóbbi kérelem végrehajtási ideje. |
| `performanceCounter.requests_in_application_queue.value` |Végrehajtási üzenetsorban lévő ASP.NET-es kérelmek |Az alkalmazás kérelem-várólistájának hossza. |
| `performanceCounter.requests_per_sec.value` |ASP.NET-kérelmek gyakorisága |Az ASP.NET által az alkalmazásnak küldött összes kérelem száma másodpercenként. |
| `remoteDependencyFailed.durationMetric.count` |Függőségi hibák |A kiszolgálói alkalmazás által külső erőforrások indított sikertelen hívások száma. |
| `request.duration` |Kiszolgáló válaszideje |Egy HTTP-kérelem fogadása és a válasz küldésének befejezése között eltelt idő. |
| `request.rate` |Kérelmek száma |Az alkalmazás másodpercenként küldött összes kérelem száma. |
| `requestFailed.count` |Sikertelen kérelmek |HTTP-kérelmek száma, amelyek válaszkódot eredményezett > = 400 |
| `view.count` |Lapmegtekintések |Egy weblap kéréseinek száma. Szintetikus forgalom ki van szűrve. |
| {az egyéni metrika neve} |{A metrika neve} |A metrikaérték által jelentett [TrackMetric](../../azure-monitor/app/api-custom-events-metrics.md#trackmetric) vagy a [követési hívás mérések paraméterében](../../azure-monitor/app/api-custom-events-metrics.md#properties). |

A metrikák különféle telemetriai modulok által küldött:

| Metrika csoport | Adatgyűjtő modulja |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>megtekintés |[Böngésző JavaScript](../../azure-monitor/app/javascript.md) |
| performanceCounter |[Teljesítmény](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[Függőség](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |
| kérelem,<br/>requestFailed |[Kiszolgálói kérelem](../../azure-monitor/app/configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhookok
Is [automatizálható a riasztás](../../azure-monitor/platform/alerts-webhooks.md). Riasztást hoz létre az Azure egy webcímet tetszőleges fogja hívni.

## <a name="see-also"></a>Lásd még
* [Nakonfigurovat Application Insights-szkript](powershell-script-create-resource.md)
* [Az Application Insights és a webes teszt erőforrások létrehozása sablonból](powershell.md)
* [Az Application insights szolgáltatásba a Microsoft Azure Diagnostics kapcsolási automatizálása](powershell-azure-diagnostics.md)
* [Automatizálható a riasztás](../../azure-monitor/platform/alerts-webhooks.md)
