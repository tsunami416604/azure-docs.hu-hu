---
title: Egyéni rendelkezésre állási tesztek létrehozása és futtatása az Azure Functions használatával
description: Ez a dokumentum ismerteti, hogyan hozhat létre egy Azure-függvény TrackAvailability() amely rendszeresen fut a TimerTrigger függvényben megadott konfigurációnak megfelelően. A teszt eredményeit a rendszer elküldi az Application Insights-erőforrásnak, ahol lekérdezheti és riasztást kaphat a rendelkezésre állási eredmények adatairól. A testreszabott tesztek lehetővé teszik, hogy a portál felhasználói felületén a lehetségesnél összetettebb rendelkezésre állási teszteket írjon, figyeljen egy alkalmazást az Azure virtuális hálózatán belül, módosítsa a végpont címét, vagy hozzon létre egy rendelkezésre állási tesztet, ha az nem érhető el a régiójában.
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 11/22/2019
ms.openlocfilehash: 476d66c51c10a5fcfb3cb0319c47b3338d28812c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665799"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Egyéni rendelkezésre állási tesztek létrehozása és futtatása az Azure Functions használatával

Ez a cikk ismerteti, hogyan hozhat létre egy Azure-függvény TrackAvailability() amely rendszeresen fut a timertrigger függvényben megadott konfiguráció nak megfelelően a saját üzleti logikával. A teszt eredményeit a rendszer elküldi az Application Insights-erőforrásnak, ahol lekérdezheti és riasztást kaphat a rendelkezésre állási eredmények adatairól. Ez lehetővé teszi, hogy személyre szabott teszteket hozzon létre, hasonlóan ahhoz, amit a [portálon a rendelkezésre állásfigyelésen](../../azure-monitor/app/monitor-web-app-availability.md) keresztül tehet. A testreszabott tesztek lehetővé teszik, hogy a portál felhasználói felületén a lehetségesnél összetettebb rendelkezésre állási teszteket írjon, figyeljen egy alkalmazást az Azure virtuális hálózatán belül, módosítsa a végpont címét, vagy hozzon létre egy rendelkezésre állási tesztet, még akkor is, ha ez a funkció nem érhető el az Ön régiójában.

> [!NOTE]
> Ebben a példában kizárólag a TrackAvailability() API-hívás azure-függvényen belüli működésének mechanikáját mutatja be. Nem, hogyan kell írni az alapul szolgáló HTTP-teszt kód/üzleti logika, amely szükséges lenne, hogy kapcsolja be ezt egy teljesen működőképes rendelkezésre állási teszt. Alapértelmezés szerint, ha végigvezeti ezt a példát, akkor létre fog hozni egy rendelkezésre állási tesztet, amely mindig létrehoz egy hibát.

## <a name="create-timer-triggered-function"></a>Időzítő aktivált függvény létrehozása

- Ha rendelkezik egy Application Insights-erőforrással:
    - Alapértelmezés szerint az Azure Functions létrehoz egy Application Insights-erőforrást, de ha a már létrehozott erőforrások egyikét szeretné használni, meg kell adnia azt a létrehozás során.
    - Kövesse az Azure [Functions-erőforrás és időzítő aktivált függvény (leállítás](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) a karbantartás előtt) az alábbi lehetőségekkel kapcsolatos utasításokat.
        -  Válassza a **Figyelés** lapot a felső sarokmellett.

            ![ Hozzon létre egy Azure Functions alkalmazást saját App Insights-erőforrással](media/availability-azure-functions/create-function-app.png)

        - Jelölje be az Application Insights legördülő lista, és írja be vagy válassza ki az erőforrás nevét.

            ![Meglévő Application Insights-erőforrás kiválasztása](media/availability-azure-functions/app-insights-resource.png)

        - Válassza **a Véleményezés + létrehozás lehetőséget**
- Ha még nem hozott létre egy Application Insights-erőforrást az időzítő által aktivált függvényhez:
    - Alapértelmezés szerint az Azure Functions-alkalmazás létrehozásakor létrehoz egy Application Insights-erőforrást.
    - Kövesse az [Utasításokat,](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) hogyan hozhat létre egy Azure Functions erőforrás és időzítő aktivált függvény (stop tisztítás előtt).

## <a name="sample-code"></a>Mintakód

Másolja az alábbi kódot a run.csx fájlba (ez felülírja a már meglévő kódot). Ehhez lépjen be az Azure Functions alkalmazásba, és válassza ki az időzítő eseményindító funkciót a bal oldalon.

>[!div class="mx-imgBorder"]
>![Az Azure-függvény run.csx szolgáltatása az Azure Portalon](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Az végpontcímhez a következőt kell használnia: `EndpointAddress= https://dc.services.visualstudio.com/v2/track`. Kivéve, ha az erőforrás található egy régióban, például az Azure Government vagy az Azure China ebben az esetben olvassa el ezt a cikket [az alapértelmezett végpontok felülbírálása,](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) és válassza ki a megfelelő telemetriai csatorna végpont a régióban.

```C#
#load "runAvailabilityTest.csx"
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");
 
//[CONFIGURATION_REQUIRED]
// If your resource is in a region like Azure Government or Azure China, change the endpoint address accordingly.
// Visit https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification for more details.
private const string EndpointAddress = "https://dc.services.visualstudio.com/v2/track";
 
private static readonly TelemetryConfiguration telemetryConfiguration = new TelemetryConfiguration(instrumentationKey, new InMemoryChannel { EndpointAddress = EndpointAddress });
private static readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
 
public async static Task Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");
 
    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }
 
    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";
 
    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");
 
    log.LogInformation($"Executing availability test run for {testName} at: {DateTime.Now}");
    string operationId = Guid.NewGuid().ToString("N");
 
    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = testName,
        RunLocation = location,
        Success = false
    };
 
    var stopwatch = new Stopwatch();
    stopwatch.Start();
 
    try
    {
        await RunAvailbiltyTestAsync(log);
        availability.Success = true;
    }
    catch (Exception ex)
    {
        availability.Message = ex.Message;
 
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", testName);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        telemetryClient.TrackException(exceptionTelemetry);
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;
 
        telemetryClient.TrackAvailability(availability);
        // call flush to ensure telemetry is sent
        telemetryClient.Flush();
    }
}

```

A nézetfájlok jobb oldalán válassza a **Hozzáadás**lehetőséget. Hívja meg az új **function.proj** fájlt a következő konfigurációval.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.8.2" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![A jobb oldali válassza ki, add. A function.proj fájl elnevezése](media/availability-azure-functions/addfile.png)

A nézetfájlok jobb oldalán válassza a **Hozzáadás**lehetőséget. Hívja meg az új fájlt **runAvailabilityTest.csx** a következő konfigurációval.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>Elérhetőség ellenőrzése

Győződjön meg arról, hogy minden működik, tekintse meg a grafikont az Application Insights-erőforrás Rendelkezésre álláslapján.

> [!NOTE]
> Ha végre a saját üzleti logika runAvailabilityTest.csx akkor látni fogja a sikeres eredményeket, mint a screenshotok alatt, ha nem, akkor látni fogja, sikertelen eredményeket.

>[!div class="mx-imgBorder"]
>![Rendelkezésre állás lap sikeres eredményekkel](media/availability-azure-functions/availtab.png)

Amikor beállítja a tesztet az Azure Functions használatával, észre fogja venni, hogy ellentétben a **Teszt hozzáadása** a Rendelkezésre állás lapon, a teszt neve nem jelenik meg, és nem lesz képes együttműködni vele. Az eredmények láthatóvá válnak, de a portálon keresztül rendelkezésre állási teszt létrehozásakor a részletes nézet helyett egy összegző nézetet kap.

A végpontok között tranzakció részleteinek megtekintéséhez válassza **a Sikeres** vagy **sikertelen** lehetőséget a részletezés alatt, majd válasszon egy mintát. A végpontok között a tranzakció részleteit is megkaphatja, ha kiválaszt egy adatpontot a grafikonon.

>[!div class="mx-imgBorder"]
>![Mintarendelkezésre állási teszt kiválasztása](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![Végpontok között lebonyolított tranzakciók részletei](media/availability-azure-functions/end-to-end.png)

Ha mindent úgy futtatott, ahogy van (üzleti logika hozzáadása nélkül), akkor látni fogja, hogy a teszt sikertelen volt.

## <a name="query-in-logs-analytics"></a>Lekérdezés a naplókban (Analytics)

A Naplók(elemzés) segítségével megtekintheti a rendelkezésre állási eredményeket, a függőségeket és egyebeket. Ha többet szeretne megtudni a Naplókról, látogasson el [a Lekérdezés áttekintése című témakörbe.](../../azure-monitor/log-query/log-query-overview.md)

>[!div class="mx-imgBorder"]
>![Elérhetőségi eredmények](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Függőségek](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>További lépések

- [Alkalmazástérkép](../../azure-monitor/app/app-map.md)
- [Tranzakció diagnosztikája](../../azure-monitor/app/transaction-diagnostics.md)
