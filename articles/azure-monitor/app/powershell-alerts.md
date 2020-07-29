---
title: Riasztások beállítása a Application Insightsban a PowerShell használatával | Microsoft Docs
description: Application Insights konfigurációjának automatizálása a metrikák változásairól szóló e-mailek lekéréséhez.
ms.topic: conceptual
ms.date: 07/23/2016
ms.openlocfilehash: 74d477b6660c0f7ec2ee32b34169bb85886936e5
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322465"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>A PowerShell használata riasztások beállításához az Application Insights szolgáltatásban

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Application Insightsban](./app-insights-overview.md)automatizálhatja a [riasztások](../platform/alerts-log.md) konfigurációját.

Emellett beállíthatja, [hogy a webhookok a riasztásra adott válaszokat automatizálják](../platform/alerts-webhooks.md).

> [!NOTE]
> Ha egy időben szeretné létrehozni az erőforrásokat és a riasztásokat, érdemes lehet [egy Azure Resource Manager sablont használni](powershell.md).

## <a name="one-time-setup"></a>Egyszeri telepítés
Ha még nem használta a PowerShellt az Azure-előfizetéséhez, mielőtt:

Telepítse az Azure PowerShell-modult arra a gépre, amelyen futtatni szeretné a parancsfájlokat.

* Telepítse a [Microsoft webplatform-telepítőt (V5 vagy újabb)](https://www.microsoft.com/web/downloads/platform.aspx).
* A Microsoft Azure PowerShell telepítéséhez használhatja

## <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz
Indítsa el Azure PowerShell és [kapcsolódjon az előfizetéséhez](/powershell/azure/):

```azurepowershell
Add-AzAccount
```


## <a name="get-alerts"></a>Riasztások beolvasása

```azurepowershell
Get-AzAlertRule -ResourceGroup "Fabrikam" `
  [-Name "My rule"] `
  [-DetailedOutput]
```

## <a name="add-alert"></a>Riasztás hozzáadása

```azurepowershell
Add-AzMetricAlertRule -Name "{ALERT NAME}" `
  -Description "{TEXT}" `
  -ResourceGroup "{GROUP NAME}" `
  -TargetResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
  -MetricName "{METRIC NAME}" `
  -Operator GreaterThan `
  -Threshold {NUMBER}  `
  -WindowSize {HH:MM:SS} `
  [-SendEmailToServiceOwners] `
  [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM"] `
  -Location "East US" // must be East US at present `
  -RuleType Metric
```


## <a name="example-1"></a>1\. példa
Küldjön e-mailt, ha a kiszolgáló HTTP-kérésekre adott válasza 5 percen belül átlagosan 1 másodpercnél lassabb. A Application Insights erőforrás neve IceCreamWebApp, és a fabrikam erőforráscsoport. Az Azure-előfizetés tulajdonosa vagyok.

A GUID az előfizetés azonosítója (nem az alkalmazás Instrumentation kulcsa).

```azurepowershell
Add-AzMetricAlertRule -Name "slow responses" `
  -ResourceGroup "Fabrikam" `
  -TargetResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
  -MetricName "request.duration" `
  -Operator GreaterThan `
  -Threshold 1 `
  -WindowSize 00:05:00 `
  -SendEmailToServiceOwners `
  -Location "East US" `
  -RuleType Metric
```

## <a name="example-2"></a>2\. példa
Olyan alkalmazással rendelkezem, amelyben a [TrackMetric ()](./api-custom-events-metrics.md#trackmetric) kifejezést használjuk a "salesPerHour" nevű metrika jelentéséhez. E-mail küldése a munkatársainak, ha a "salesPerHour" a 100 alá esik, és 24 óra átlagot vesz igénybe.

```azurepowershell
Add-AzMetricAlertRule -Name "poor sales" `
  -Description "slow sales alert" `
  -ResourceGroup "Fabrikam" `
  -TargetResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
  -MetricName "salesPerHour" `
  -Operator LessThan `
  -Threshold 100 `
  -WindowSize 24:00:00 `
  -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
  -Location "East US" `
  -RuleType Metric
```

Ugyanez a szabály használható egy másik nyomkövetési hívás (például TrackEvent vagy trackPageView) [mérési paramétere](./api-custom-events-metrics.md#properties) használatával jelentett metrika esetében.

## <a name="metric-names"></a>Metrikák nevei
| Metrika neve | Képernyő neve | Leírás |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Böngészőkivételek |A böngészőben fellépő nem kezelt kivételek száma. |
| `basicExceptionServer.count` |Kiszolgálói kivételek |Az alkalmazás által eldobott kezeletlen kivételek száma |
| `clientPerformance.clientProcess.value` |Ügyfél feldolgozási ideje |A dokumentum utolsó bájtjának betöltése közötti idő, amíg a DOM be nem töltődik. Lehetséges, hogy az aszinkron kérelmek feldolgozása még folyamatban van. |
| `clientPerformance.networkConnection.value` |Oldal betöltési hálózati kapcsolati ideje |A böngészőnek a hálózathoz való kapcsolódáshoz szükséges ideje. Gyorsítótárazva lehet 0. |
| `clientPerformance.receiveRequest.value` |Válaszidő fogadása |A böngésző küldési kérelme közötti idő a kapott válasz megkezdéséhez. |
| `clientPerformance.sendRequest.value` |Kérelem küldési ideje |A böngésző által a kérelem elküldéséhez szükséges idő. |
| `clientPerformance.total.value` |Böngésző oldalának betöltési ideje |A felhasználói kérések ideje, amíg a DOM, a stíluslapok, a parancsfájlok és a lemezképek be nem töltődik. |
| `performanceCounter.available_bytes.value` |Igénybe vehető memória |A fizikai memória azonnal elérhető egy folyamat vagy a rendszer általi használatra. |
| `performanceCounter.io_data_bytes_per_sec.value` |Folyamat i/o-sebessége |A fájlok, a hálózat és az eszközök számára másodpercenként olvasott és írt bájtok másodpercenkénti száma. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |kivételek aránya |Kivételek száma másodpercenként. |
| `performanceCounter.percentage_processor_time.value` |Processzor feldolgozása |A processzor által az alkalmazások folyamatának végrehajtásához használt összes folyamati szál eltelt idejének százalékos aránya. |
| `performanceCounter.percentage_processor_total.value` |Processzoridő |Az az időtartam, ameddig a processzor a nem üresjárati szálakban költ. |
| `performanceCounter.process_private_bytes.value` |Saját bájtok feldolgozása |A memória kizárólag a figyelt alkalmazás folyamataihoz van rendelve. |
| `performanceCounter.request_execution_time.value` |ASP.NET kérelem végrehajtási ideje |A legutóbbi kérelem végrehajtási ideje. |
| `performanceCounter.requests_in_application_queue.value` |ASP.NET kérelmek a végrehajtási várólistában |Az alkalmazás-kérelmek várólistájának hossza |
| `performanceCounter.requests_per_sec.value` |ASP.NET kérelmek gyakorisága |Az alkalmazásra irányuló kérelmek másodpercenkénti aránya a ASP.NET. |
| `remoteDependencyFailed.durationMetric.count` |Függőségi hibák |A kiszolgálói alkalmazás által külső erőforrások felé indított sikertelen hívások száma. |
| `request.duration` |Kiszolgáló válaszideje |HTTP-kérelem fogadása és a válasz küldésének befejezése közötti idő. |
| `request.rate` |Kérelmek gyakorisága |Az alkalmazásra irányuló kérelmek másodpercenkénti száma. |
| `requestFailed.count` |Sikertelen kérelmek |A >= 400 hibakódot eredményező HTTP-kérelmek száma |
| `view.count` |Lapok nézetei |Weblapokhoz tartozó ügyfél-felhasználói kérelmek száma. A szintetikus forgalom ki van szűrve. |
| {az egyéni metrika neve} |{A metrika neve} |A [TrackMetric](./api-custom-events-metrics.md#trackmetric) által jelentett metrikai érték, vagy [egy nyomkövetési hívás mérési paraméterében](./api-custom-events-metrics.md#properties). |

A metrikákat különböző telemetria-modulok küldik:

| Metrikai csoport | Gyűjtő modul |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>megtekintése |[Böngésző JavaScript](./javascript.md) |
| performanceCounter |[Teljesítmény](./configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[Függőség](./configuration-with-applicationinsights-config.md) |
| kérelem<br/>requestFailed |[Kiszolgálói kérelem](./configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhookok
[A riasztásra adott válasz automatizálható](../platform/alerts-webhooks.md). Az Azure meghívja a választott webcímet, ha riasztást vált ki.

## <a name="see-also"></a>További információ
* [Application Insights konfigurálására szolgáló parancsfájl](./create-new-resource.md#creating-a-resource-automatically)
* [Application Insights-és webes tesztelési erőforrások létrehozása sablonokból](powershell.md)
* [Összekapcsolási Microsoft Azure Diagnostics automatizálása Application Insights](powershell-azure-diagnostics.md)
* [a riasztásra adott válasz automatizálása](../platform/alerts-webhooks.md)

