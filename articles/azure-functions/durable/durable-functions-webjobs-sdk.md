---
title: Tartós függvények futtatása a WebJobs - Azure-t
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
ms.openlocfilehash: 18c35070707408f43fd0e5dfc5e3330ef62a914d
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53343156"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Tartós függvények futtatása a webjobs-feladatok

[Az Azure Functions](../functions-overview.md) és a [Durable Functions](durable-functions-overview.md) bővítmény épül a [WebJobs SDK-val](../../app-service/web-sites-create-web-jobs.md). A `JobHost` van a modul az Azure Functions a WebJobs SDK-val. Ha meg kell határozni `JobHost` viselkedését az Azure Functions nem lehetséges módokon, fejlesztéséhez és tartós függvények futtatása a WebJobs SDK-val saját maga által. A tartós függvények ezután futtathat egy Azure webjobs-feladat vagy bárhol a konzol alkalmazás fut-e.

A WebJobs SDK-verzió érhető el a titkosításblokkoló Durable Functions-minta: Töltse le vagy klónozza a [Durable Functions-tárház](https://github.com/azure/azure-functions-durable-extension/) , és keresse meg a *minták\\webjobssdk\\láncolás* mappát.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy ismeri a WebJobs SDK-C# osztály könyvtárfejlesztőjével az Azure Functions és a tartós függvények alapjait. Ha ezek a témakörök bevezetést van szüksége, tekintse meg az alábbi forrásanyagokat:

* [A WebJobs SDK használatának első lépései](../../app-service/webjobs-sdk-get-started.md)
* [Az első függvény létrehozása a Visual Studio használatával](../functions-create-your-first-function-visual-studio.md)
* [Tartós függvények](durable-functions-sequence.md)

A jelen cikkben ismertetett lépések végrehajtásához:

* [Telepítse a Visual Studio 2017 15.6 vagy újabb verzió](https://docs.microsoft.com/visualstudio/install/) együtt a **Azure-fejlesztési** számítási feladatok.

  Ha már rendelkezik a Visual Studióval, de nem rendelkezik az adott számítási feladat, vegye fel a számítási feladatok kiválasztásával **eszközök > első eszközeivel és szolgáltatásaival**.

  (Használható [Visual Studio Code](https://code.visualstudio.com/) ehelyett azonban az itt található utasításokat a Visual Studio jellemző.)

* Telepítheti és futtathatja [Azure Storage Emulator](../../storage/common/storage-use-emulator.md) 5.2-es vagy újabb verziója. Frissítés helyett a *App.config* fájlt egy Azure Storage kapcsolati karakterlánccal.

## <a name="webjobs-sdk-versions"></a>A WebJobs SDK-verziókra

Ez a cikk bemutatja, hogyan hozhat létre a WebJobs SDK 2.x project (Azure Functions verzióra egyenértékű 1.x). Verzióval kapcsolatos információ 3.x, lásd: [WebJobs SDK-val 3.x](#webjobs-sdk-3x) a cikk későbbi részében.

## <a name="create-console-app"></a>Konzolalkalmazás létrehozása

A WebJobs SDK projekt érhető el csak console app telepített megfelelő NuGet-csomagokkal.

A Visual Studio **új projekt** párbeszédablakban válassza **Windows klasszikus Asztalialkalmazás > Console App (.NET Framework)**. A következő projektfájlban a `TargetFrameworkVersion` lehet `v4.6.1`.

A Visual Studio is rendelkezik a webjobs-feladat projektsablon, amellyel kiválasztásával **felhő > Azure WebJob (.NET Framework)**. Ez a sablon telepíti a csomagok száma, amelyek némelyike nem feltétlenül szükségesek.

## <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

NuGet-csomagok a WebJobs SDK-val, core kötések, a naplózási keretrendszer és a tartós feladat bővítmény van szükség. Az alábbiakban **Package Manager Console** azokat a csomagokat, ez a cikk írásának dátumon legújabb stabil verziója számokkal parancsokat:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Naplózás szolgáltató is szükséges. Az alábbi parancsokat az Application Insights-szolgáltató telepítésének és a `ConfigurationManager`. A `ConfigurationManager` az Application Insights-kialakítási kulcs vezeti be az alkalmazásbeállításokat.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

A következő parancsot a konzol szolgáltatót telepíti:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost kódot

Durable Functions bővítményt használja, hívja meg `UseDurableTask` a a `JobHostConfiguration` objektumának a `Main` módszer:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Beállíthatja a tulajdonságok listája a `DurableTaskExtension` objektumazonosító, lásd: [host.json](../functions-host-json.md#durabletask).

A `Main` mód is a hely a naplózás szolgáltatók beállítása. Az alábbi példa a konzol és az Application Insights-szolgáltatók konfigurálja.

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

A WebJobs SDK-függvények képest írni az Azure Functions szolgáltatáshoz írt néhány különbségek vannak.

A WebJobs SDK nem támogatja a következő Azure Functions-szolgáltatásairól:

* [Függvénynév attribútum](#functionname-attribute)
* [HTTP-eseményindító](#http-trigger)
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

A WebJobs SDK-projektben módszerek segítségével meghívhatja a vezénylési ügyfél objektum helyett HTTP-kérelmek küldéséhez. A három feladatokat is végezhet a HTTP-felügyeleti API felel meg a következő módszerek:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

A vezénylési ügyfél függvényt a mintaprojekt az orchestrator függvény elindul, és majd hiányzóra változik, amely meghívja ezt hurkot `GetStatusAsync` másodpercenként 2:

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

Ez a szakasz futtatása áttekintést nyújt a [mintaprojektet](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Ismertetjük a WebJobs SDK-projekt helyi futtatása és üzembe helyezése az Azure WebJob részletes útmutatás: [a WebJobs SDK használatának első lépései](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Helyi futtatás

1. Győződjön meg arról, hogy fut-e a Storage emulator (lásd: [Előfeltételek](#prerequisites)).

1. Ha meg szeretné tekinteni az Application Insights-naplók, ha helyileg futtatja:

    a. Hozzon létre egy Application Insights-erőforrást, alkalmazástípus **általános**.

    b. A kialakítási kulcsot az mentse a *App.config* fájlt.

1. Futtassa a projektet.

### <a name="run-in-azure"></a>Azure-ban

1. Hozzon létre egy webalkalmazást és a egy tárfiókot.

1. A web app alkalmazásban mentse a tárolási kapcsolat karakterláncát a AzureWebJobsStorage nevű Alkalmazásbeállítás.

1. Hozzon létre egy Application Insights-erőforrást, alkalmazástípus **általános**.

1. Mentse a kialakítási kulcsot állítani az APPINSIGHTS_INSTRUMENTATIONKEY nevű Alkalmazásbeállítás.

1. Helyezze üzembe a Webjobs-feladatként.

## <a name="webjobs-sdk-3x"></a>A WebJobs SDK 3.x

A fő változás 3.x bevezetett, a .NET-keretrendszer helyett a .NET Core használata. 3.x projekt létrehozásához a ugyanezek az utasítások érvényesek, kivéve a következőket:

1. Hozzon létre egy .NET Core-konzolalkalmazást. A Visual Studio **új projekt** párbeszédablakban válassza **.NET Core > Console App (.NET Core)**. Soubor projektu azt jelenti, hogy `TargetFramework` van `netcoreapp2.0`.

1. Válassza ki az előzetes verziót, a következő csomagok 3.x:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Változás `Main` metódus kód segítségével kérheti le a tárolási kapcsolati karakterlánc és az Application Insights-kialakítási kulcsot a egy *appsettings.json* fájlt, a .NET Core keretrendszerrel.  Például:

   ```cs
   static void Main(string[] args)
   {
       var builder = new ConfigurationBuilder()
           .SetBasePath(Directory.GetCurrentDirectory())
           .AddJsonFile("appsettings.json");

       var appSettingsConfig = builder.Build();

       using (var loggerFactory = new LoggerFactory())
       {
           var config = new JobHostConfiguration();

           config.DashboardConnectionString = "";
           config.StorageConnectionString =
               appSettingsConfig.GetConnectionString("AzureWebJobsStorage");
           var instrumentationKey =
               appSettingsConfig["APPINSIGHTS_INSTRUMENTATIONKEY"];

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

## <a name="next-steps"></a>További lépések

A WebJobs SDK-val kapcsolatos további információkért lásd: [használata a WebJobs SDK](../../app-service/webjobs-sdk-how-to.md).