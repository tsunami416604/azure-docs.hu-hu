---
title: Egyéni rendelkezésre állási tesztek létrehozása és futtatása Azure Functions használatával
description: Ez a dokumentum azt ismerteti, hogyan hozhat létre egy Azure-függvényt a TrackAvailability () használatával, amely rendszeres időközönként a TimerTrigger függvényben megadott konfigurációnak megfelelően fog futni. A teszt eredményét a rendszer elküldi a Application Insights-erőforrásnak, ahol a rendelkezésre állási eredmények adataira vonatkozó lekérdezéssel és riasztással kapcsolatos értesítéseket fog kapni. A testreszabott tesztek lehetővé teszik az összetettebb rendelkezésre állási tesztek megírását, mint amennyi a portál felhasználói felületén lehetséges, figyelheti az alkalmazást az Azure-VNET belül, módosíthatja a végponti címeket, vagy létrehozhat egy rendelkezésre állási tesztet, ha az nem érhető el a régióban.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: morgangrobin
ms.author: mogrobin
ms.date: 10/11/2019
ms.openlocfilehash: 900228e1f9bdf9d367fa37b9ec90a6148faec656
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72880251"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Egyéni rendelkezésre állási tesztek létrehozása és futtatása Azure Functions használatával

Ez a cikk bemutatja, hogyan hozhat létre egy Azure-függvényt a TrackAvailability () alkalmazással, amely rendszeres időközönként a TimerTrigger függvényben megadott konfiguráció szerint fog futni. A teszt eredményét a rendszer elküldi a Application Insights-erőforrásnak, ahol a rendelkezésre állási eredmények adataira vonatkozó lekérdezéssel és riasztással kapcsolatos értesítéseket fog kapni. Ez lehetővé teszi, hogy a portálon a [rendelkezésre állási monitorozáshoz](../../azure-monitor/app/monitor-web-app-availability.md) hasonló testreszabott teszteket hozzon létre. A testreszabott tesztek lehetővé teszik összetettebb rendelkezésre állási tesztek megírását, mint amennyi a portál felhasználói felületén lehetséges, figyelheti az alkalmazást az Azure-VNET, módosíthatja a végponti címeket, vagy létrehozhat egy rendelkezésre állási tesztet, még akkor is, ha ez a funkció nem érhető el a régióban.


## <a name="create-timer-triggered-function"></a>Időzítő által aktivált függvény létrehozása

- Ha Application Insights erőforrással rendelkezik:
    - Alapértelmezés szerint a Azure Functions egy Application Insights-erőforrást hoz létre, de ha a már létrehozott erőforrások valamelyikét szeretné használni, akkor a létrehozás során meg kell adnia.
    - Kövesse az [Azure functions erőforrás és időzítő által aktivált függvény létrehozásához](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) szükséges lépéseket (leállítás a tisztítás előtt) a következő lehetőségekkel.
        -  A **Létrehozás**előtt kattintson a Application Insights szakaszra.

            ![ Hozzon létre egy Azure Functions alkalmazást a saját alkalmazás-keresési erőforrással](media/availability-azure-functions/create-function-app.png)

        - Kattintson a **meglévő erőforrás kiválasztása** elemre, és írja be az erőforrás nevét. **Alkalmazás** kiválasztása

            ![Meglévő Application Insights erőforrás kiválasztása](media/availability-azure-functions/app-insights-resource.png)

        - Kattintson a **Létrehozás** elemre.
- Ha még nem rendelkezik olyan Application Insights erőforrással, amelyet az időzítő által aktivált függvényhez még nem hozott létre:
    - Alapértelmezés szerint a Azure Functions-alkalmazás létrehozásakor létrejön egy Application Insights erőforrás.
    - Kövesse az [Azure functions erőforrás és időzítő által aktivált függvény létrehozásával](https://docs.microsoft.com/azure/azure-functions/functions-create-scheduled-function) kapcsolatos utasításokat (leállítás a tisztítás előtt).

## <a name="sample-code"></a>Mintakód

Másolja az alábbi kódot a Run. CSX fájlba (Ezzel felülírja a már meglévő kódot). Ehhez lépjen a Azure Functions alkalmazásba, és válassza ki a bal oldali időzítő trigger funkciót.

![Az Azure-függvény Run. CSX Azure Portal](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> A használni kívánt végpont címe: `EndpointAddress= https://dc.services.visualstudio.com/v2/track`. Kivéve, ha az erőforrás olyan régióban található, mint Azure Government vagy az Azure China, ahol ebben az esetben az [alapértelmezett végpontok felülbírálásával](https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification) és a régió megfelelő telemetria-csatornájának végpontjának megadásával foglalkozó cikkben tájékozódhat.

```C#
using System;
using System.Diagnostics;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.Extensions.Logging;

// [CONFIGURATION_REQUIRED] configure test timeout accordingly for which your request should run
private static readonly HttpClient HttpClient = new HttpClient { Timeout = TimeSpan.FromSeconds(30) };

// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
//DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string InstrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");

// [CONFIGURATION_REQUIRED] Configure EndpointAddress
private static readonly TelemetryConfiguration TelemetryConfiguration = new TelemetryConfiguration(InstrumentationKey, new ServerTelemetryChannel() { EndpointAddress = "<EndpointAddress>" });
private static readonly TelemetryClient TelemetryClient = new TelemetryClient(TelemetryConfiguration);

[FunctionName("Function")]
public static async void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
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

    // [CONFIGURATION_REQUIRED] configure {uri} and {contentMatch} accordingly for your web app. {uri} is the website that you are testing the availability of, make sure to include http:// ot https:// in your url. If {contentMatch} is present on the page, the test will succeed, otherwise it will fail.  
    await AvailabilityTestRun(
        name: testName,
        location: location,
        uri: "<http://example.com>",
        contentMatch: "<Enter a short string of text that is present  in the body of the page your are testing>",
        log: log
    );
}

private static async Task AvailabilityTestRun(string name, string location, string uri, string contentMatch, ILogger log)
{
    log.LogInformation($"Executing availability test run for {name} at: {DateTime.Now}");

    string operationId = Guid.NewGuid().ToString("N");

    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = name,
        RunLocation = location,
        Success = false
    };

    var stopwatch = new Stopwatch();
    stopwatch.Start();
    bool isMonitoringFailure = false;

    try
    {
        using (var httpResponse = await HttpClient.GetAsync(uri))
        {
            // add test results to availability telemetry property
            availability.Properties.Add("HttpResponseStatusCode", Convert.ToInt32(httpResponse.StatusCode).ToString());

            // check if response content contains specific text
            string content = httpResponse.Content != null ? await httpResponse.Content.ReadAsStringAsync() : "";
            if (httpResponse.IsSuccessStatusCode && content.Contains(contentMatch))
            {
                availability.Success = true;
                availability.Message = $"Test succeeded with response: {httpResponse.StatusCode}";
                log.LogTrace($"[Verbose]: {availability.Message}");
            }
            else if (!httpResponse.IsSuccessStatusCode)
            {
                availability.Message = $"Test failed with response: {httpResponse.StatusCode}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
            else
            {
                availability.Message = $"Test content does not contain: {contentMatch}";
                log.LogWarning($"[Warning]: {availability.Message}");
            }
        }
    }
    catch (TaskCanceledException e)
    {
        availability.Message = $"Test timed out: {e.Message}";
        log.LogWarning($"[Warning]: {availability.Message}");
    }
    catch (Exception ex)
    {
        // track exception when unable to determine the state of web app
        isMonitoringFailure = true;
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", name);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        exceptionTelemetry.Properties.Add("TestUri", uri);
        TelemetryClient.TrackException(exceptionTelemetry);
        log.LogError($"[Error]: {ex.Message}");

        // optional - throw to fail the function
        //throw;
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;

        // do not make an assumption as to the state of the web app when monitoring failures occur
        if (!isMonitoringFailure)
        {
            TelemetryClient.TrackAvailability(availability);
            log.LogInformation($"Availability telemetry for {name} is sent.");
        }

        // call flush to ensure telemetries are sent
        TelemetryClient.Flush();
    }
}
```

A fájlok megtekintése lehetőségnél kattintson a **Hozzáadás**gombra. Hívja meg az új file **function. Proj** fájlt a következő konfigurációval.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
    </ItemGroup>
</Project>

```

![A jobb gombbal válassza a Hozzáadás lehetőséget. Nevezze el a file function. Proj fájlt.](media/availability-azure-functions/addfile.png)

## <a name="check-availability"></a>Rendelkezésre állás keresése

Az összes működésének ellenőrzéséhez tekintse meg a diagramot a Application Insights erőforrás rendelkezésre állás lapján.

![Rendelkezésre állás lap sikeres eredményekkel](media/availability-azure-functions/availtab.png)

Ha a tesztet a Azure Functions használatával állítja be, akkor **a teszt nem** jelenik meg a rendelkezésre állás lapon, és nem fog tudni kommunikálni a tesztek nevével. Az eredmények vizualizációval jelennek meg, de a részletes nézet helyett a portálon keresztüli rendelkezésre állási teszt létrehozásakor is megjelenik egy összegző nézet.

A végpontok közötti tranzakció részleteinek megtekintéséhez válassza a **sikeres** vagy **sikertelen** művelet elemet a részletezés szakaszban, majd válasszon ki egy mintát. A végpontok közötti tranzakció részleteit úgy is elérheti, ha kijelöl egy adatpontot a diagramon.

![Minta rendelkezésre állási teszt kiválasztása](media/availability-azure-functions/sample.png)

![Végpontok közötti tranzakció részletei](media/availability-azure-functions/end-to-end.png)

## <a name="query-in-logs-analytics"></a>Lekérdezés a naplókban (Analitika)

A naplók (Analitika) használatával megtekintheti a rendelkezésre állási eredményeket, a függőségeket és egyebeket. A naplókról további információt a [log Query áttekintése című](../../azure-monitor/log-query/log-query-overview.md)témakörben találhat.

![Rendelkezésre állási eredmények](media/availability-azure-functions/availabilityresults.png)

![Függőségek](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Következő lépések

- [Alkalmazástérkép](../../azure-monitor/app/app-map.md)
- [Tranzakció-diagnosztika](../../azure-monitor/app/transaction-diagnostics.md)
