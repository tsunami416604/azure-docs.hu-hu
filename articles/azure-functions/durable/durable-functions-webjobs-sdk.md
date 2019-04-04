---
title: Durable Functions futtatása a WebJobs - Azure-t
description: Ismerje meg, hogyan code, és konfigurálja a Durable Functions a WebJobs SDK-val a webjobs-feladatok futtatásához.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: df12639aaafaf3df7ae2b755d635d4fba83d846e
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905092"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Webjobs-feladatok, Durable Functions futtatása

Durable Functions alapértelmezés szerint az Azure Functions futtatókörnyezettel, a gazdagép vezénylések használja. Előfordulhatnak azonban bizonyos forgatókönyvek, ahol Ön kell több szabályozhatja a kódot, amely az eseményeket figyeli. Ez a cikk bemutatja, hogyan megvalósításához a vezénylés, a WebJobs SDK-val. Functions és webjobs-feladatok részletes összehasonlítását lásd: [hasonlítsa össze a Functions és WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Az Azure Functions](../functions-overview.md) és a [Durable Functions](durable-functions-overview.md) bővítmény épül a [WebJobs SDK-val](../../app-service/webjobs-sdk-how-to.md). A feladat gazdagép, a WebJobs SDK-ban a modul az Azure Functions. Ha az Azure Functions nem lehetséges módokon viselkedés vezérlése van szüksége, fejlesztheti és Durable Functions futtassa saját kezűleg a WebJobs SDK használatával.

A verzió 3.x, a WebJobs SDK-val, az állomás megvalósítását `IHost`, és verzió 2.x verzióját használja a `JobHost` objektum.

Egy WebJobs SDK 2.x verziójában érhető el a titkosításblokkoló Durable Functions-minta: Töltse le vagy klónozza a [Durable Functions-tárház](https://github.com/azure/azure-functions-durable-extension/), és nyissa meg a *minták\\webjobssdk\\láncolás* mappát.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy ismeri a WebJobs SDK-C# osztály könyvtárfejlesztőjével az Azure Functions és a tartós függvények alapjait. Ha ezek a témakörök bevezetést van szüksége, tekintse meg az alábbi forrásanyagokat:

* [A WebJobs SDK használatának első lépései](../../app-service/webjobs-sdk-get-started.md)
* [Az első függvény létrehozása a Visual Studio használatával](../functions-create-your-first-function-visual-studio.md)
* [Tartós függvények](durable-functions-sequence.md)

A jelen cikkben ismertetett lépések végrehajtásához:

* [Telepítse a Visual Studio 2017 15.6 vagy újabb verzió](https://docs.microsoft.com/visualstudio/install/) együtt a **Azure-fejlesztési** számítási feladatok.

  Ha már rendelkezik a Visual Studióval, de nem rendelkezik az adott számítási feladatot, adja hozzá a számítási feladatok kiválasztásával **eszközök** > **első eszközeivel és szolgáltatásaival**.

  (Használható [Visual Studio Code](https://code.visualstudio.com/) ehelyett azonban az itt található utasításokat a Visual Studio jellemző.)

* Telepítheti és futtathatja [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) 5.2-es vagy újabb verziója. Frissítés helyett a *App.config* fájlt egy Azure Storage kapcsolati karakterlánccal.

## <a name="webjobs-sdk-versions"></a>A WebJobs SDK-verziókra

Ez a cikk bemutatja, hogyan hozhat létre a WebJobs SDK 2.x project (Azure Functions verzióra egyenértékű 1.x). Verzióval kapcsolatos információ 3.x, lásd: [WebJobs SDK-val 3.x](#webjobs-sdk-3x) a cikk későbbi részében.

## <a name="create-a-console-app"></a>Konzolalkalmazás létrehozása

Durable Functions, webjobs-feladatok futtatásához, először létre kell hoznia egy konzolalkalmazást. A WebJobs SDK projekt érhető el csak console app telepített megfelelő NuGet-csomagokkal.

A Visual Studio **új projekt** párbeszédpanelen jelölje ki **Windows klasszikus Asztalialkalmazás** > **Console App (.NET Framework)**. A következő projektfájlban a `TargetFrameworkVersion` lehet `v4.6.1`.

A Visual Studio is rendelkezik a webjobs-feladat projektsablon, amellyel kiválasztásával **felhőalapú** > **Azure WebJob (.NET Framework)**. Ez a sablon telepíti a csomagok száma, amelyek némelyike nem feltétlenül szükségesek.

## <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

NuGet-csomagok a WebJobs SDK-val, core kötések, a naplózási keretrendszer és a tartós feladat bővítmény van szükség. Az alábbiakban **Package Manager Console** parancsok a azokat a csomagokat, a legújabb stabil verziója számokkal, ez a cikk írásának dátumon:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Naplózás szolgáltató is szükséges. Az alábbi parancsokat az Azure Application Insights-szolgáltató telepítésének és a `ConfigurationManager`. A `ConfigurationManager` az Application Insights-kialakítási kulcs vezeti be az alkalmazásbeállításokat.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

A következő parancsot a konzol szolgáltatót telepíti:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost kódot

A Konzolalkalmazás létrehozása és a NuGet-csomagok telepítése van szüksége, készen áll a Durable Functions használatát. Ehhez JobHost kód használatával.

Durable Functions bővítményt használja, hívja meg `UseDurableTask` a a `JobHostConfiguration` objektumának a `Main` módszer:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Beállíthatja a tulajdonságok listája a `DurableTaskExtension` objektumazonosító, lásd: [host.json](../functions-host-json.md#durabletask).

A `Main` mód is a hely a naplózás szolgáltatók beállítása. Az alábbi példa konfigurálja a konzol és az Application Insights-szolgáltatók.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Functions

Durable Functions a webjobs-feladatok kontextusában Durable Functions némileg eltér az Azure Functions kontextusában. Fontos tisztában lenni a különbségek a kód írása.

A WebJobs SDK nem támogatja a következő Azure Functions-szolgáltatásairól:

* [Függvénynév attribútum](#functionname-attribute)
* [HTTP eseményindító](#http-trigger)
* [Durable Functions – HTTP-felügyeleti API](#http-management-api)

### <a name="functionname-attribute"></a>Függvénynév attribútum

A WebJobs SDK-projektben egy függvény metódusnév a függvény nevét. A `FunctionName` attribútuma csak az Azure Functions szolgál.

### <a name="http-trigger"></a>HTTP eseményindító

A WebJobs SDK-val nem rendelkezik egy HTTP-eseményindítóval. A mintaprojekt vezénylési ügyfél egy időzítő indítófeltételt használja:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>HTTP-felügyeleti API

Mivel nincs HTTP-eseményindítóval rendelkezik, a WebJobs SDK-val nem rendelkezik [HTTP-felügyeleti API](durable-functions-http-api.md).

A WebJobs SDK-projektben a HTTP-kérelmek küldésével meghívhat módszerek a vezénylési ügyfél objektum helyett. A három feladatokat is végezhet a HTTP-felügyeleti API felel meg a következő módszerek:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

A mintaprojekt a vezénylési ügyfél függvényt az orchestrator függvény elindul, és majd hiányzóra változik, amely meghívja ezt hurkot `GetStatusAsync` másodpercenként 2:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>Minta futtatása

Durable Functions beállítása Webjobs-feladatként futtatására van, és most már rendelkezik egy hogyan ez különbözni fog Durable Functions futtató önálló Azure Functions ismerete. Ezen a ponton jelent meg, hogy működik a minta akkor lehet hasznos.

Ez a szakasz futtatása áttekintést nyújt a [mintaprojektet](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Ismertetjük a WebJobs SDK-projekt helyi futtatása és üzembe helyezése az Azure WebJob részletes útmutatás: [a WebJobs SDK használatának első lépései](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Helyi futtatás

1. Győződjön meg arról, hogy fut-e a Storage emulator (lásd: [Előfeltételek](#prerequisites)).

1. Ha meg szeretné tekinteni az Application Insights-naplók, ha a projekt helyi futtatása:

    a. Hozzon létre egy Application Insights-erőforrást, és használja a **általános** alkalmazás típusát.

    b. A kialakítási kulcsot az mentse a *App.config* fájlt.

1. Futtassa a projektet.

### <a name="run-in-azure"></a>Azure-ban

1. Hozzon létre egy webalkalmazást és a egy tárfiókot.

1. A web app alkalmazásban mentse a tárolási kapcsolati karakterlánc egy alkalmazásban nevű beállításhoz `AzureWebJobsStorage`.

1. Hozzon létre egy Application Insights-erőforrást, és használja a **általános** alkalmazás típusát.

1. A kialakítási kulcs mentéséhez egy alkalmazásban nevű beállításával `APPINSIGHTS_INSTRUMENTATIONKEY`.

1. Helyezze üzembe a Webjobs-feladatként.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

Ez a cikk bemutatja, hogyan hozhat létre a WebJobs SDK 2.x projekt. Ha az Ön egy [WebJobs SDK-val 3.x](../../app-service/webjobs-sdk-get-started.md) projekt, ez a szakasz segít megérteni a különbségeket.

A fő változást jelent meg a .NET-keretrendszer helyett a .NET Core használata. A WebJobs SDK-val 3.x-projekt létrehozásához a ugyanezek az utasítások érvényesek, a következő kivételektől eltekintve:

1. Hozzon létre egy .NET Core-konzolalkalmazást. A Visual Studio **új projekt** párbeszédpanelen jelölje ki **.NET Core** > **Console App (.NET Core)**. Soubor projektu azt jelenti, hogy `TargetFramework` van `netcoreapp2.x`.

1. Válassza ki a végleges verziót a WebJobs SDK-t a következő csomagok 3.x:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Állítsa be a tárfiók kapcsolati karakterláncának és az Application Insights-kialakítási kulcsot az egy *appsettings.json* fájlt, a .NET Core konfigurációs keretrendszer használatával. Például:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Módosítsa a `Main` ehhez mód kódot. Például:

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>További lépések

A WebJobs SDK-val kapcsolatos további információkért lásd: [használata a WebJobs SDK](../../app-service/webjobs-sdk-how-to.md).
