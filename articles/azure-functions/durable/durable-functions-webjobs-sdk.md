---
title: A tartós függvények webjobs-ként való futtatása - Azure
description: Megtudhatja, hogy miként kódolhatja és konfigurálhatja a Tartós függvényeket a WebJobs-ban a WebJobs SDK használatával történő futtatásra.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: d8dd0c86fbc520d0bd3ef6034891bd9871774b4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232744"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>A tartós függvények webjobs-ként való futtatása

Alapértelmezés szerint a durable functions az Azure Functions futásidejű gazdagépvezények üzemeltetéséhez. Előfordulhatazonban azonban, hogy bizonyos esetekben több szabályszükséges az eseményeket figyelő kód felett. Ez a cikk bemutatja, hogyan valósíthatja meg a vezénylési a WebJobs SDK használatával. A Funkciók és a WebJobs alkalmazások részletesebb összehasonlítását a Funkciók és a [WebJobs összehasonlítása című témakörben talál.](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs)

[Az Azure Functions](../functions-overview.md) és a [Durable Functions](durable-functions-overview.md) bővítmény a [WebJobs SDK-ra](../../app-service/webjobs-sdk-how-to.md)épül. A webjobs SDK feladatgazdagépe az Azure Functions futásideje. Ha az Azure Functions ben nem lehetséges módon kell szabályoznia a viselkedést, a WebJobs SDK saját kezű használatával fejlesztheti és futtathatja a tartós függvényeket.

A WebJobs SDK 3.x verziójában az állomás `IHost`a , a 2.x-es verzióban pedig az `JobHost` objektumot használja.

A láncoló tartós függvények minta elérhető a WebJobs SDK 2.x verzió: töltse le vagy klónozza a [Durable Functions tárház](https://github.com/azure/azure-functions-durable-extension/), és megy a *minták\\webjobssdk\\láncolatú* mappába.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy ismeri a WebJobs SDK alapjait, az Azure Functions C# osztálykönyvtárának fejlesztését és a tartós funkciókat. Ha ezekben a témakörökben szeretne bemutatni, olvassa el az alábbi forrásokat:

* [A WebJobs SDK – első lépések](../../app-service/webjobs-sdk-get-started.md)
* [Az első függvény létrehozása a Visual Studio használatával](../functions-create-your-first-function-visual-studio.md)
* [Tartós függvények](durable-functions-sequence.md)

A cikkben ismertetett lépések végrehajtásához tegye a következőket:

* [Telepítse a Visual Studio 2019-et](https://docs.microsoft.com/visualstudio/install/) az **Azure fejlesztési** munkaterhelésével.

  Ha már rendelkezik a Visual Studio szolgáltatással, de nem rendelkezik ezzel a munkaterheléssel, adja hozzá a munkaterhelést az **Eszközök** > **be- és szolgáltatások**lehetőséget választva.

  (A [Visual Studio-kód](https://code.visualstudio.com/) is használható, de néhány utasítás a Visual Studio-ra vonatkozik.)

* Telepítse és futtassa [az Azure Storage Emulátor](../../storage/common/storage-use-emulator.md) 5.2-es vagy újabb verzióját. Egy másik lehetőség az *App.config* fájl frissítése egy Azure Storage-kapcsolati karakterlánccal.

## <a name="webjobs-sdk-versions"></a>WebJobs SDK-verziók

Ez a cikk bemutatja, hogyan fejleszthet webjobs SDK 2.x projektet (ami egyenértékű az Azure Functions 1.x-es verziójával). A 3.x-es verzióról a cikk második részében a [WebJobs SDK 3.x](#webjobs-sdk-3x) című részben olvashat.

## <a name="create-a-console-app"></a>Konzolalkalmazás létrehozása

A Tartós függvények WebJobs-ként való futtatásához először létre kell hoznia egy konzolalkalmazást. A WebJobs SDK-projekt csak egy konzolalkalmazás-projekt, amelyen telepítve vannak a megfelelő NuGet-csomagok.

A Visual Studio **Új projekt** párbeszédpanelén válassza a Windows **Classic asztali** > **konzol alkalmazás (.NET Framework)** lehetőséget. A projektfájlban `TargetFrameworkVersion` a `v4.6.1`programnak a legyen .

A Visual Studio rendelkezik egy WebJob projektsablonnal is, amelyet a **Cloud** > **Azure WebJob (.NET Framework) (.NET Framework)** kiválasztásával használhat. Ez a sablon számos csomagot telepít, amelyek közül néhányra nincs szükség.

## <a name="install-nuget-packages"></a>NuGet-csomagok telepítése

NuGet csomagokra van szüksége a WebJobs SDK-hoz, az alapvető kötésekhez, a naplózási keretrendszerhez és a Tartós feladat bővítményhez. Itt vannak **a Package Manager Console** parancsok ezekhez a csomagokhoz, a legújabb stabil verziószámokkal a cikk írásának dátumátkövetően:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

Naplózási szolgáltatókra is szükség van. A következő parancsok telepítik az `ConfigurationManager`Azure Application Insights-szolgáltatót és a. A `ConfigurationManager` lehetővé teszi, hogy az Application Insights instrumentation kulcs az alkalmazás beállításait.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

A következő parancs telepíti a konzolszolgáltatót:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost kód

A konzolalkalmazás létrehozása és a szükséges NuGet csomagok telepítése után készen áll a Durable Functions használatára. Ezt a JobHost kód használatával teszi.

A Tartós funkciók bővítmény `UseDurableTask` használatához `JobHostConfiguration` hívja meg `Main` a metódusban lévő objektumot:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

Az objektumban beállítható tulajdonságok listáját `DurableTaskExtension` a [host.json](../functions-host-json.md#durabletask).

A `Main` módszer a naplózási szolgáltatók beállításának helye is. A következő példa konfigurálja a konzol és az Application Insights-szolgáltatók.

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

Tartós funkciók a WebJobs környezetben némileg eltér a tartós függvények az Azure Functions környezetében. Fontos, hogy tisztában legyenek a különbségeket, ahogy írja a kódot.

A WebJobs SDK nem támogatja a következő Azure Functions funkciókat:

* [FunctionName attribútum](#functionname-attribute)
* [HTTP-eseményindító](#http-trigger)
* [Tartós függvények HTTP felügyeleti API](#http-management-api)

### <a name="functionname-attribute"></a>FunctionName attribútum

A WebJobs SDK-projektekben a függvény metódusneve a függvény neve. Az `FunctionName` attribútum csak az Azure Functions.

### <a name="http-trigger"></a>HTTP eseményindító

A WebJobs SDK nem rendelkezik HTTP-eseményindítóval. A mintaprojekt vezénylési ügyfélegy időzítő eseményindító:

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

Mivel nem rendelkezik HTTP-eseményindítóval, a WebJobs SDK nem rendelkezik [HTTP-felügyeleti API-val.](durable-functions-http-api.md)

A WebJobs SDK-projektekben a vezénylési ügyfélobjektum metódusait hívhatja meg, nem pedig HTTP-kérelmek küldésével. A http-felügyeleti API-val elvégzett három feladatnak a következő módszerek felelnek meg:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

A vezénylési ügyfél függvény a mintaprojektelindítja az `GetStatusAsync` orchestrator függvényt, majd egy 2 másodpercenként hívó ciklusba kerül:

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

A tartós függvények webfeladatként való futtatásra vannak beállítva, és most már tisztában van azzal, hogy ez miben fog különbözni a tartós függvények önálló Azure-funkciókként való futtatásától. Ezen a ponton, látva, hogy a munka egy minta hasznos lehet.

Ez a szakasz áttekintést nyújt a [mintaprojekt](https://github.com/Azure/azure-functions-durable-extension/tree/master/samples/webjobssdk/chaining)futtatásáról. A WebJobs SDK-projektek helyi futtatását és üzembe helyezését ismerteti az [Első lépések a WebJobs SDK-val](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob)című témakörben talál.

### <a name="run-locally"></a>Helyi futtatás

1. Győződjön meg arról, hogy a storage emulátor fut [(lásd: Előfeltételek](#prerequisites)).

1. Ha a projekt helyi futtatásakor szeretné látni a naplókat az Application Insightsban:

    a. Hozzon létre egy Application Insights-erőforrást, és használja az **Általános** alkalmazástípust.

    b. Mentse a műszerezési kulcsot az *App.config* fájlba.

1. Futtassa a projektet.

### <a name="run-in-azure"></a>Futtatás az Azure-ban

1. Hozzon létre egy webalkalmazást és egy tárfiókot.

1. A webalkalmazásban mentse a tárolási kapcsolat karakterláncát `AzureWebJobsStorage`egy alkalmazás nevű alkalmazásbeállításba.

1. Hozzon létre egy Application Insights-erőforrást, és használja az **Általános** alkalmazástípust.

1. Mentse a műszerezési kulcsot `APPINSIGHTS_INSTRUMENTATIONKEY`egy nevű alkalmazásbeállításba.

1. Üzembe helyezése webfeladatként.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3.x

Ez a cikk bemutatja, hogyan fejleszthet WebJobs SDK 2.x projektet. Ha [WebJobs SDK 3.x](../../app-service/webjobs-sdk-get-started.md) projektet fejleszt, ez a szakasz segít megérteni a különbségeket.

A fő változás a .NET Core használata a .NET Framework helyett. WebJobs SDK 3.x projekt létrehozásához az utasítások ugyanazok, a következő kivételekkel:

1. Hozzon létre egy .NET Core konzolalkalmazást. A Visual Studio **Új projekt** párbeszédpanelén válassza a **.NET Core** > **Console App (.NET Core)** lehetőséget. A projektfájl a `TargetFramework` `netcoreapp2.x`.

1. Válassza ki a következő csomagok WebJobs SDK 3.x kiadási verzióját:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Állítsa be a tárolási kapcsolati karakterláncot és az Application Insights instrumentation kulcs egy *appsettings.json* fájlban, a . Például:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Ehhez `Main` módosítsa a metóduskódot. Például:

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

Ha többet szeretne megtudni a WebJobs SDK-ról, olvassa [el a WebJobs SDK használata](../../app-service/webjobs-sdk-how-to.md)című témakört.
