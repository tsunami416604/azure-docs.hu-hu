---
title: WebJobs - Azure funkcionál tartós futtatása
description: Megtudhatja, hogyan code, és a webjobs-feladatok futtatása a WebJobs SDK használatával tartós funkciók konfigurálását.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 3fc84d1492d2855ffa3bb5538226da049a928339
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Hogyan futtathat tartós funkciókkal, mint a webjobs-feladatok

[Az Azure Functions](functions-overview.md) és a [tartós funkciók](durable-functions-overview.md) bővítmény épül a [WebJobs SDK](../app-service/web-sites-create-web-jobs.md). A `JobHost` a WebJobs SDK a futtatókörnyezet, az Azure Functions van. Ha meg kell határozni `JobHost` viselkedését az Azure Functions nem lehetséges módokon, létrehozhatja és tartós funkciók futtassa saját kezűleg a WebJobs SDK használatával. Ezt követően futtathatja a tartós funkciók az Azure webjobs-feladat vagy bárhol egy konzol alkalmazás fut-e.

A titkosításblokkoló tartós funkciók minta érhető el a WebJobs SDK-verzió: Töltse le, vagy klónozza a [tartós funkciók tárház](https://github.com/azure/azure-functions-durable-extension/) , és keresse meg a *minták\\webjobssdk\\láncolás* mappa.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy ismeri a WebJobs SDK, a C# osztály könyvtár fejlesztés az Azure Functions és tartós funkciók alapjait. Ha ezek a témakörök a bevezetést van szüksége, tekintse meg a következőket:

* [Ismerkedés a WebJobs SDK-val](../app-service/webjobs-sdk-get-started.md)
* [Az első függvény létrehozása a Visual Studio használatával](functions-create-your-first-function-visual-studio.md)
* [Tartós funkciók](durable-functions-sequence.md)

Ebben a cikkben a lépések elvégzéséhez:

* [A Visual Studio 2017 15.6 vagy újabb verzió telepítése](https://docs.microsoft.com/visualstudio/install/) rendelkező a **Azure fejlesztési** munkaterhelés.

  Ha már telepítve a Visual Studio, de nem rendelkezik a munkaterhelés, vegye fel a munkaterhelés kiválasztásával **eszközök > Get eszköz és funkció**. 

  (Használhatja [Visual Studio Code](https://code.visualstudio.com/) helyette, de néhány utasításokat a Visual Studio.)

* Telepítheti és futtathatja [Azure Storage Emulator](../storage/common/storage-use-emulator.md) 5.2-es vagy újabb verziója. Alternatív frissítése érdekében szükség a *App.config* fájl egy Azure Storage kapcsolati karakterlánccal.

## <a name="webjobs-sdk-versions"></a>WebJobs SDK-verzió

Ez a cikk ismerteti, hogyan fejleszthet a WebJobs SDK 2.x project (az Azure Functions verzió egyenértékű 1.x). Verziójával kapcsolatos információkért 3.x, lásd: [WebJobs SDK 3.x](#webjobs-sdk-3x) című cikkben. 

## <a name="create-console-app"></a>Konzolalkalmazás létrehozása

A WebJobs SDK projekt csak egy olyan konzol app projektet a megfelelő NuGet-csomagok telepítve.

A Visual Studio **új projekt** párbeszédablakban válassza **klasszikus Windows asztal > Konzolalkalmazás (.NET-keretrendszer)**. A következő projektfájlban a `TargetFrameworkVersion` kell `v4.6.1`.

A Visual Studio szintén tartalmazza a webjobs-feladat projektsablon, amellyel kiválasztásával **felhő > Azure webjobs-feladat (.NET-keretrendszer)**. Ez a sablon telepítése csomagok száma, amelyek némelyike nem szükség lehet.

## <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

A WebJobs SDK, core kötések, a naplózási keretrendszer és a tartós feladatkiterjesztés NuGet-csomagok van szüksége. Az alábbiakban **Csomagkezelő konzol** azokat a csomagokat, a legújabb stabil verziószáma, ez a cikk írásának napjától parancsokat:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.4.0
```

Naplózási szolgáltatók is kell. A következő parancsokat az Application Insights-szolgáltató telepítéséhez és a `ConfigurationManager`. A `ConfigurationManager` lehetővé teszi, hogy az Application Insights instrumentation kulcs beszerzése Alkalmazásbeállítások.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

A következő parancsot a konzol szolgáltatót telepíti:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost kód

A tartós funkciók a kiterjesztést használni, hívja meg a `UseDurableTask` a a `JobHostConfiguration` objektum a `Main` módszert:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

A megadott tulajdonságok listáját a `DurableTaskExtension` objektumazonosító, lásd: [host.json](functions-host-json.md#durabletask).

A `Main` módszer is a hely a naplózás szolgáltatók beállítása. A következő példa a konzol és az Application Insights-szolgáltatókat konfigurál.

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

A WebJobs SDK-függvények képest írni az Azure Functions szolgáltatáshoz írt kódot néhány különbségek vannak.

A WebJobs SDK nem támogatja a következő Azure Functions-szolgáltatásairól:

* [Függvénynév attribútum](#functionname-attribute)
* [HTTP-eseményindítóval](#http-trigger)
* [Tartós funkciók HTTP felügyeleti API](#http-management-api)

### <a name="functionname-attribute"></a>Függvénynév attribútum

A WebJobs SDK projektben a metódus nevét, egy függvény a függvény nevét. A `FunctionName` attribútum csak az Azure Functions használják.

### <a name="http-trigger"></a>HTTP eseményindító

A WebJobs SDK nem rendelkezik egy HTTP-eseményindítóval. A minta projekt vezénylési ügyfél egy időzítő indítófeltételt használó:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>HTTP-kezelési API

Nincs HTTP-eseményindítóval van, mert a WebJobs SDK nincs [HTTP felügyeleti API](durable-functions-http-api.md).

A WebJobs SDK projekt hívása helyett HTTP-kérelmek küldése a vezénylési ügyfél objektum módszerek. A három feladatokat hajthat végre a HTTP Management API felelnek meg a következőkkel:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Az orchestration ügyfél függvény a minta projekt elindítja az orchestrator függvény, és majd hiányzóra változik, amely behívja hurkot `GetStatusAsync` 2 másodpercenként:

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

Ez a szakasz áttekintést futtatásáról a [mintaprojektet](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining). Ismerteti az futtassa helyileg a WebJobs SDK-projektet, és telepítse azt egy Azure webjobs-feladat részletes útmutatás: [Ismerkedés a WebJobs SDK-val](../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Helyi futtatás

1. Győződjön meg arról, hogy fut-e a Storage emulator (lásd: [Előfeltételek](#prerequisites)).

1. Ha meg szeretné tekinteni az Application Insights-naplók, amikor helyileg futtatta:

  a. Hozzon létre egy Application Insights-erőforrást, alkalmazás típusa **általános**.

  b. A rendszerállapot-kulcsot a Mentés a *App.config* fájlt.

1. Futtassa a projektet.

### <a name="run-in-azure"></a>Futtassa az Azure-ban

1. Hozzon létre egy webalkalmazást és a storage-fiók.

1. A web app alkalmazásban mentése a tárolási kapcsolat karakterláncát a AzureWebJobsStorage nevű Alkalmazásbeállítás.

1. Hozzon létre egy Application Insights-erőforrást, alkalmazás típusa **általános**.

1. Mentse a instrumentation kulcs APPINSIGHTS_INSTRUMENTATIONKEY nevű Alkalmazásbeállítás.

1. Telepítése webjobs-feladat.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

A fő módosítása 3.x által bevezetett a .NET Core helyett a .NET-keretrendszer használatát. 3.x-projekt létrehozása utasításokat megegyeznek, kivéve a következőket:

1. Hozzon létre egy .NET Core konzolalkalmazást. A Visual Studio **új projekt** párbeszédablakban válassza **.NET Core > Konzolalkalmazás (.NET Core)**. A projektfájl azt jelenti, hogy `TargetFramework` van `netcoreapp2.0`.

1. Válassza ki az előzetes verziót a következő csomagok 3.x:

  * `Microsoft.Azure.WebJobs.Extensions`
  * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

2. Változás `Main` metódus kód segítségével kérheti le a tárolási kapcsolati karakterlánc és az Application Insights instrumentation kulcsot egy *appsettings.json* fájl, a .NET Core konfigurációs keretrendszer használatával.  Például:

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

A WebJobs SDK kapcsolatos további információkért lásd: [használata a WebJobs SDK](../app-service/webjobs-sdk-how-to.md).

