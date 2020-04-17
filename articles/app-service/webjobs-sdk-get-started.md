---
title: A WebJobs SDK – első lépések
description: Bevezetés a WebJobs SDK eseményvezérelt háttérfeldolgozáshoz. Ismerje meg, hogyan érheti el az Azure-szolgáltatások ban és a külső szolgáltatásokban található adatokat.
author: ggailey777
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 4976be485a9b7609c6e8d23f6b897092217663fc
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535672"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Az Azure WebJobs SDK első lépései az eseményalapú háttérfeldolgozáshoz

Ez a cikk bemutatja, hogyan hozhat létre a Visual Studio 2019 segítségével egy Azure WebJobs SDK-projektet, hogyan futtathat helyileg, majd telepítheti azt az [Azure App Service szolgáltatásba.](overview.md) A WebJobs SDK 3.x verziója támogatja a .NET Core és a .NET Framework konzolalkalmazásokat is. Ha többet szeretne tudni a WebJobs SDK használatáról, olvassa [el az Azure WebJobs SDK használata eseményvezérelt háttérfeldolgozáshoz](webjobs-sdk-how-to.md)című témakört.

Ez a cikk bemutatja, hogyan telepítheti a WebJobs-ot .NET Core konzolalkalmazásként. A WebJobs . [WebJobs as .NET Framework console apps](webjobs-dotnet-deploy-vs.md#webjobs-as-net-framework-console-apps) Ha érdekli a WebJobs SDK 2.x-es verziója, amely csak a .NET Framework-et támogatja, olvassa el [a WebJobs fejlesztése és telepítése a Visual Studio – Azure App Service használatával című témakört.](webjobs-dotnet-deploy-vs.md)

## <a name="prerequisites"></a>Előfeltételek

* [Telepítse a Visual Studio 2019-et](/visualstudio/install/) az **Azure fejlesztési** munkaterhelésével. Ha már rendelkezik a Visual Studio szolgáltatással, de nem rendelkezik ezzel a munkaterheléssel, adja hozzá a munkaterhelést az **Eszközök > Eszközök beésési szolgáltatásai**lehetőséget választva.

* A WebJobs SDK-projekt Azure-ban való közzétételéhez [Azure-fiókkal](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) kell rendelkeznie.

## <a name="create-a-project"></a>Projekt létrehozása

1. A Visual Studióban válassza **az Új projekt létrehozása**lehetőséget.

2. Válassza a **Console App (.NET Core) lehetőséget.**

3. Nevezze el a projektet *WebJobsSDKSample néven,* majd válassza **a Létrehozás lehetőséget.**

   ![New Project (Új projekt) párbeszédpanel](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>WebJobs NuGet csomagok

1. Telepítse a [ `Microsoft.Azure.WebJobs.Extensions` NuGet csomag](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/)legújabb stabil 3.x `Microsoft.Azure.WebJobs`verzióját, amely tartalmazza a .

     Íme a **Csomagkezelő konzol** parancsa:

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version <3_X_VERSION>
     ```

    Ebben a parancsban cserélje le `<3_X_VERSION>` a csomag támogatott verziójára. 

## <a name="create-the-host"></a>A gazdagép létrehozása

Az állomás az eseményindítók és hívások függvényeket figyelő függvények futásidejű tárolója. A következő lépések hozzon [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost)létre egy állomás, amely megvalósítja , amely az általános állomás ASP.NET Core.

1. A *Program.cs*adja `using` hozzá a következő állításokat:

    ```cs
    using System.Threading.Tasks;
    using Microsoft.Extensions.Hosting;
    ```

1. Cserélje le az `Main` metódust az alábbi kódra:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

A core ASP.NET állomáskonfigurációk at a [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) példány hívási metódusai határozzák meg. További információt a .NET Generic Host című [témakörben talál.](/aspnet/core/fundamentals/host/generic-host) A `ConfigureWebJobs` bővítmény metódusa inicializálja a WebJobs állomást. A `ConfigureWebJobs`alkalmazásban inicializálhatja a WebJobs-bővítményeket, és beállíthatja a bővítmények tulajdonságait.  

## <a name="enable-console-logging"></a>Konzolnaplózás engedélyezése

Ebben a szakaszban olyan konzolnaplózást állíthat be, amely a [ASP.NET Core naplózási keretrendszert](/aspnet/core/fundamentals/logging)használja.

1. Telepítse a [ `Microsoft.Extensions.Logging.Console` NuGet csomag](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)legújabb stabil `Microsoft.Extensions.Logging`verzióját, amely tartalmazza a .

   Íme a **Csomagkezelő konzol** parancsa:

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version <3_X_VERSION>
   ```

1. A *Program.cs*adjon `using` hozzá egy nyilatkozatot:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

    Ebben a parancsban cserélje le `<3_X_VERSION>` a csomag támogatott 3.x verzióját.

1. Hívja [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) meg [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)a metódust . A [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) metódus hozzáadja a konzolnaplózást a konfigurációhoz.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    A `Main` módszer most így néz ki:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    A frissítés a következőket teszi:

    * Letiltja [az irányítópult naplózását.](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs) Az irányítópult egy örökölt figyelési eszköz, és az irányítópult naplózása nem ajánlott a nagy átviteli sebességű éles forgatókönyvek.
    * Hozzáadja a konzolszolgáltatót az alapértelmezett [szűréssel.](webjobs-sdk-how-to.md#log-filtering)

Most hozzáadhat egy függvényt, amelyet az Azure [Storage-várólistába](../azure-functions/functions-bindings-storage-queue.md)érkező üzenetek váltanak ki.

## <a name="install-the-storage-binding-extension"></a>A Storage-kötésbővítmény telepítése

A 3.x-es verziótól kezdve explicit módon telepítenie kell a WebJobs SDK által igényelt Storage kötésbővítményt. A korábbi verziókban a Storage kötések az SDK-ban szerepeltek.

1. Telepítse a [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet csomag legújabb stabil verzióját, 3.x-es verzió. 

    Íme a **Csomagkezelő konzol** parancsa:

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version <3_X_VERSION>
    ```
    
    Ebben a parancsban cserélje le `<3_X_VERSION>` a csomag támogatott verziójára. 

2. A `ConfigureWebJobs` bővítmény metódusban `AddAzureStorage` hívja meg [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) a metódust a példányin a Storage bővítmény inicializálásához. Ezen a ponton `ConfigureWebJobs` a módszer a következő példához hasonlóan néz ki:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Függvény létrehozása

1. Kattintson a jobb gombbal a projektre, válassza az Új elem **hozzáadása** > **parancsot...**, válassza **az Osztály parancsot,** nevezze el az új C# osztályfájlt *Functions.cs,* és válassza **a Hozzáadás parancsot.**

1. A Functions.cs cserélje le a létrehozott sablont a következő kódra:
    
    ```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Extensions.Logging;
    
    namespace WebJobsSDKSample
    {
        public class Functions
        {
            public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
            {
                logger.LogInformation(message);
            }
        }
    }
    ```

   Az `QueueTrigger` attribútum megmondja a futásidejűnek, hogy hívja meg ezt a `queue`függvényt, ha egy új üzenet van írva egy Azure Storage-várólistában, amelynek neve. A várólistaüzenet tartalma a `message` paraméterben lévő metóduskódhoz van megadva. A metódus törzse az, ahol feldolgozza az eseményindító adatait. Ebben a példában a kód csak naplózza az üzenetet.

   A `message` paraméternek nem kell karakterláncnak lennie. JSON-objektumhoz, bájttömbhöz vagy [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) objektumhoz is köthető. [Lásd: Várólista-eseményindító kulcsok használata](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). Minden kötéstípus (például várólisták, blobok vagy táblák) különböző paramétertípus-készlettel rendelkezik, amelyhez kötődhet.

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

A helyileg futó Azure Storage-emulátor nem rendelkezik a WebJobs SDK által igényelt összes szolgáltatással. Így ebben a szakaszban hozzon létre egy tárfiókot az Azure-ban, és konfigurálja a projektet, hogy azt használja. Ha már rendelkezik tárfiókkal, ugorjon le a 6.

1. Nyissa meg **a Kiszolgálókezelőt** a Visual Studióban, és jelentkezzen be az Azure-ba. Kattintson a jobb gombbal az **Azure-csomópontra,** majd válassza **a Csatlakozás a Microsoft Azure-előfizetéshez parancsot.**

   ![Bejelentkezés az Azure-ba](./media/webjobs-sdk-get-started/sign-in.png)

1. A **Kiszolgálókezelő** **Azure-csomópontja** alatt kattintson a jobb gombbal a **Storage**elemre, majd válassza **a Tárfiók létrehozása parancsot**.

   ![Tárfiók létrehozása menü](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. A **Tárfiók létrehozása** párbeszédpanelen adjon meg egy egyedi nevet a tárfióknak.

1. Válassza ki ugyanazt a **régiót,** amelyben létrehozta az App Service-alkalmazást, vagy egy önhöz közeli régiót.

1. Kattintson a **Létrehozás** gombra.

   ![Tárfiók létrehozása](./media/webjobs-sdk-get-started/create-storage-account.png)

1. A **Kiszolgálókezelő** **Tároló** csomópontja csoportban válassza ki az új tárfiókot. A **Tulajdonságok** ablakban jelölje ki a **kapcsolati karakterlánc** értékmezőjének jobb oldalán található három pontot (**...**).

   ![Kapcsolati karakterlánc három ponttal](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Másolja a kapcsolati karakterláncot, és mentse ezt az értéket olyan helyre, ahol újra könnyen másolhatja.

   ![Kapcsolati karakterlánc másolása](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>A tároló helyi futtatásának beállítása

A WebJobs SDK megkeresi a tárolási kapcsolat karakterláncát az Azure alkalmazásbeállításaiban. Ha helyileg futtatja, akkor ezt az értéket keresi a helyi konfigurációs fájlban vagy a környezeti változókban.

1. Kattintson a jobb gombbal a projektre, válassza **az** > Új elem hozzáadása**parancsot...**, válassza a **JavaScript JSON konfigurációs fájlt,** nevezze el az új *fájlt appsettings.json* fájlnak, és válassza **a Hozzáadás parancsot.** 

1. Az új fájlban `AzureWebJobsStorage` vegyen fel egy mezőt, ahogy az a következő példában is:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Cserélje le *a(z) {storage connection string}* karakterláncot a korábban másolt kapcsolati karakterláncra.

1. Jelölje ki az *appsettings.json* fájlt a Megoldáskezelőben, és a **Tulajdonságok** ablakban állítsa a **Másolás beállítást kimeneti könyvtárra** **másolásra, ha újabb.**

Később ugyanazt a kapcsolati karakterlánc-alkalmazásbeállítást fogja hozzáadni az alkalmazáshoz az Azure App Service-ben.

## <a name="test-locally"></a>Helyi tesztelés

Ebben a szakaszban a projektet helyileg hozza létre és futtatja, és a függvényt egy üzenetsor-üzenet létrehozásával indítja el.

1. A projekt futtatásához nyomja le a **Ctrl+F5** billentyűkombinációt.

   A konzol azt mutatja, hogy a futásidejű megtalálta a funkciót, és várja a várólista-üzenetek indítását. A v3.x állomás a következő kimenetet hozza létre:

   ```console
    info: Microsoft.Azure.WebJobs.Hosting.JobHostService[0]
          Starting JobHost
    info: Host.Startup[0]
          Found the following functions:
          WebJobsSDKSample.Functions.ProcessQueueMessage

    info: Host.Startup[0]
          Job host started
    Application started. Press Ctrl+C to shut down.
    Hosting environment: Development
    Content root path: C:\WebJobsSDKSample\WebJobsSDKSample\bin\Debug\netcoreapp2.1\
   ```

1. Zárja be a konzolablakot.

1. A Visual Studio **Kiszolgálókezelőjében** bontsa ki az új tárfiók csomópontját, majd kattintson a jobb gombbal **a Várólisták**elemre.

1. Válassza **a Várólista létrehozása**lehetőséget.

1. Írja be a *várólista* nevét a várólista neveként, majd kattintson az **OK gombra.**

   ![Várólista létrehozása](./media/webjobs-sdk-get-started/create-queue.png)

1. Kattintson a jobb gombbal az új várólista csomópontjára, majd válassza a **Várólista megtekintése parancsot.**

1. Kattintson az **Üzenet hozzáadása** ikonra.

   ![Várólista létrehozása](./media/webjobs-sdk-get-started/create-queue-message.png)

1. Az **Üzenet hozzáadása** párbeszédpanelen adja meg a *Hello World!* **üzenetszövegként,** majd válassza az **OK gombot.** Most egy üzenet van a várólistában.

   ![Várólista létrehozása](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Futtassa ismét a projektet.

   Mivel az `QueueTrigger` attribútumot használta `ProcessQueueMessage` a függvényben, a WeJobs SDK futásidejű figyeli a várólista-üzeneteket indításkor. Új várólista-üzenetet talál a *várólistában,* és meghívja a függvényt.

   A [várólista-lekérdezés exponenciális visszalépése](../azure-functions/functions-bindings-storage-queue-trigger.md#polling-algorithm)miatt akár 2 percig is eltarthat, amíg a futásidejű megtalálja az üzenetet, és meghívja a függvényt. Ez a várakozási idő [fejlesztési módban](webjobs-sdk-how-to.md#host-development-settings)való futással csökkenthető.

   A konzol kimenete így néz ki:

   ```console
    info: Function.ProcessQueueMessage[0]
          Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=2c319369-d381-43f3-aedf-ff538a4209b8)
    info: Function.ProcessQueueMessage[0]
          Trigger Details: MessageId: b00a86dc-298d-4cd2-811f-98ec39545539, DequeueCount: 1, InsertionTime: 1/18/2019 3:28:51 AM +00:00
    info: Function.ProcessQueueMessage.User[0]
          Hello World!
    info: Function.ProcessQueueMessage[0]
          Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=2c319369-d381-43f3-aedf-ff538a4209b8)
   ```

1. Zárja be a konzolablakot. 

1. Lépjen vissza a Várólista ablakba, és frissítse azt. Az üzenet eltűnt, mivel a helyileg futó függvény feldolgozta. 

## <a name="add-application-insights-logging"></a>Alkalmazáselemzési naplózás hozzáadása

Amikor a projekt fut az Azure-ban, nem figyelheti a függvény végrehajtását a konzol kimenetének megtekintésével. Az általunk ajánlott figyelési megoldás az [Application Insights.](../azure-monitor/app/app-insights-overview.md) További információ: [Monitor Azure Functions](../azure-functions/functions-monitoring.md).

Ebben a szakaszban a következő feladatokat az Application Insights-naplózás beállításához az Azure-ba való üzembe helyezés előtt végezze el:

* Győződjön meg arról, hogy rendelkezik egy App Service-alkalmazás és egy Application Insights-példány dolgozni.
* Konfigurálja az App Service-alkalmazást az Application Insights-példány és a korábban létrehozott tárfiók használatára.
* Állítsa be a projektet az Application Insights ba való naplózáshoz.

### <a name="create-app-service-app-and-application-insights-instance"></a>App Service-alkalmazás és Alkalmazásinsights-példány létrehozása

1. Ha még nem rendelkezik használható App Service-alkalmazással, [hozzon létre egyet.](app-service-web-get-started-dotnet-framework.md) Az alkalmazás létrehozásakor is létrehozhat egy csatlakoztatott Application Insights-erőforrást. Ha ezt teszi, `APPINSIGHTS_INSTRUMENTATIONKEY` a beállítás az alkalmazásban van beállítva.

1. Ha még nem rendelkezik olyan Application Insights-erőforrással, amelyet használhat, [hozzon létre egyet.](../azure-monitor/app/create-new-resource.md ) Állítsa **az Alkalmazás típusát** **Általános**beállításra , és hagyja ki **a instrumentation billentyű másolása**követendő szakaszokat .

1. Ha már rendelkezik egy használni kívánt Application Insights-erőforrással, [másolja a instrumentation kulcsot.](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key)

### <a name="configure-app-settings"></a>Alkalmazásbeállítások konfigurálása 

1. A Visual Studio **Kiszolgálókezelőjében** bontsa ki az **App Service-csomópontot** az **Azure**csoportban.

1. Bontsa ki azt az erőforráscsoportot, amelyben az App Service-alkalmazás található, majd kattintson a jobb gombbal az App Service-alkalmazásra.

1. Válassza **a Nézetbeállításai lehetőséget.**

1. A **Kapcsolati karakterláncok** mezőben adja hozzá a következő bejegyzést.

   |Name (Név)  |kapcsolati karakterlánc  |Adatbázis típusa|
   |---------|---------|------|
   |AzureWebJobsStorage | {a korábban másolt Tárolási kapcsolatkarakterlánc}|Egyéni|

1. Ha az **Alkalmazásbeállítások** mező nem rendelkezik Application Insights instrumentation kulccsal, adja hozzá a korábban másolt. (Előfordulhat, hogy a instrumentation kulcs már ott van, attól függően, hogy hogyan hozta létre az App Service-alkalmazást.)

   |Name (Név)  |Érték  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {instrumentation kulcs} |

1. Cserélje le *a(z) {instrumentation key}* kulcsot a használt Application Insights-erőforrás instrumentation kulcsára.

1. Kattintson a **Mentés** gombra.

1. Adja hozzá az Application Insights-kapcsolatot a projekthez, hogy helyileg futtathassa. Az *appsettings.json* fájlban `APPINSIGHTS_INSTRUMENTATIONKEY` adjon hozzá egy mezőt, ahogy az alábbi példában is:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Cserélje le *a(z) {instrumentation key}* kulcsot a használt Application Insights-erőforrás instrumentation kulcsára.

1. Mentse a módosításokat.

### <a name="add-application-insights-logging-provider"></a>Alkalmazáselemzési naplózási szolgáltató hozzáadása

Az Application [Insights](../azure-monitor/app/app-insights-overview.md) naplózásának előnyeinek kihasználásához frissítse a naplózási kódot az alábbi módon:

* Adjon hozzá egy Application Insights naplózási [szolgáltatót](webjobs-sdk-how-to.md#log-filtering)az alapértelmezett szűréssel. Helyi futtatásakor az összes információ és a magasabb szintű naplók a konzolra és az Application Insights-ba is írásra kerülnek.
* Helyezze a [LoggerFactory](./webjobs-sdk-how-to.md#logging-and-monitoring) `using` objektumot egy blokkba, hogy a naplókimenet kiürítése, amikor az állomás kilép.

1. Telepítse a [ `Microsoft.Azure.WebJobs.Logging.ApplicationInsights` NuGet csomag](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/)legújabb stabil 3.x verzióját.

   Íme a **Csomagkezelő konzol** parancsa:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <3_X_VERSION>
   ```
    Ebben a parancsban cserélje le `<3_X_VERSION>` a csomag támogatott verziójára.

1. Nyissa *meg Program.cs,* és `Main` cserélje le a metódusban lévő kódot a következő kódra:

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                    b.AddAzureStorage();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();

                    // If the key exists in settings, use it to enable Application Insights.
                    string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                    if (!string.IsNullOrEmpty(instrumentationKey))
                    {
                        b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    Ez hozzáadja az Application Insights-szolgáltatót a naplózáshoz az alkalmazásbeállításokhoz korábban hozzáadott kulcs használatával.

## <a name="test-application-insights-logging"></a>Az Alkalmazáselemzési adatok naplózása

Ebben a szakaszban futtassa újra helyileg, hogy ellenőrizze, hogy az adatok naplózása most az Application Insights és a konzolra.

1. A Visual Studio **Kiszolgálókezelőjével** hozzon létre egy várólista-üzenetet, mint [korábban,](#test-locally)kivéve a *Hello App Insights!* mint az üzenet szövegét.

1. Futtassa a projektet.

   A WebJobs SDK feldolgozza a várólista-üzenetet, és a naplók a konzolablakban jelennek meg.

1. Zárja be a konzolablakot.

1. Az [Azure Insights-erőforrás](https://portal.azure.com/) megtekintéséhez látogasson el az Azure Portalra. Keresse meg és válassza az **Application Insights**lehetőséget.

1. Válassza ki az Application Insights-példányt.

1. Válassza a **Keresés lehetőséget.**

   ![Keresés kiválasztása](./media/webjobs-sdk-get-started/select-search.png)

1. Ha nem látja a *Hello App Insights!* üzenetben válassza a **Frissítés** néhány percig. (A naplók nem jelennek meg azonnal, mert eltart egy ideig, amíg az Application Insights-ügyfél kiüríti a feldolgozott naplókat.)

   ![Naplók az Application Insightsban](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. Zárja be a konzolablakot.

## <a name="deploy-to-azure"></a><a name="deploy-as-a-webjob"></a>Üzembe helyezés az Azure-ban

A központi telepítés során hozzon létre egy alkalmazásszolgáltatás-példányt, amelyben a függvények futtatásához. Amikor közzétesz egy .NET Core konzolalkalmazást az Azure-beli App Service szolgáltatásban, az automatikusan webfeladatként jelenik meg. A közzétételről a [WebJobs fejlesztése és telepítése a Visual Studio használatával (Webhelyalkalmazások fejlesztése és telepítése) témakörben](webjobs-dotnet-deploy-vs.md)olvashat bővebben.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>A funkció aktiválása az Azure-ban

1. Győződjön meg arról, hogy nem helyileg fut (zárja be a konzolablakot, ha még nyitva van). Ellenkező esetben előfordulhat, hogy a helyi példány az első, amely feldolgozza a létrehozott várólista-üzeneteket.

1. A Visual Studio **Várólista lapján** adjon hozzá egy üzenetet a várólistához a korábban adott módon.

1. Frissítse a **Várólista** lapot, és az új üzenet eltűnik, mert az Azure-ban futó függvény feldolgozta.

   > [!TIP]
   > Amikor az Azure-ban teszteli, használja [a fejlesztési módot](webjobs-sdk-how-to.md#host-development-settings) annak biztosítására, hogy a várólista-eseményindító függvényt azonnal meghívja, és elkerülje a késleltetést a [várólista-lekérdezés exponenciális visszalépésmiatt.](../azure-functions/functions-bindings-storage-queue-trigger.md#polling-algorithm)

### <a name="view-logs-in-application-insights"></a>Naplók megtekintése az Application Insightsban

1. Nyissa meg az [Azure Portalon,](https://portal.azure.com/)és nyissa meg az Application Insights-erőforrást.

1. Válassza a **Keresés lehetőséget.**

1. Ha nem látja a *Hello Azure!* üzenetben válassza a **Frissítés** néhány percig.

   Megjelennek a webjobban futó függvény naplói, beleértve a *Hello Azure-t is!* az előző szakaszban beírt szöveget.

## <a name="add-an-input-binding"></a>Bemeneti kötés hozzáadása

A bemeneti kötések leegyszerűsítik az adatokat beolvasó kódot. Ebben a példában a várólista-üzenet lesz a blob nevét, és a blob nevét fogja használni, hogy megtalálja és olvassa el a blob az Azure Storage-ban.

1. A *Functions.cs*a `ProcessQueueMessage` módszert a következő kóddal helyettesítse:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   Ebben a `queueTrigger` kódban egy [kötési kifejezés,](../azure-functions/functions-bindings-expressions-patterns.md)ami azt jelenti, hogy futásidőben egy másik értékre oldódik fel.  Futásidőben a várólista-üzenet tartalma is benne van.

1. Adjon `using`hozzá egy :

   ```cs
   using System.IO;
   ```

1. Hozzon létre egy blob tárolót a tárfiókban.

   a. A Visual Studio **Kiszolgálókezelőjében** bontsa ki a tárfiók csomópontját, kattintson a jobb gombbal a **Blobs**elemre, és válassza **a Blob Container létrehozása parancsot.**

   b. A **Blob tároló létrehozása** párbeszédpanelen adja meg a *tárolót* a tároló neveként, majd kattintson az **OK**gombra.

1. Töltse fel a *Program.cs* fájlt a blob tárolóba. (Ez a fájl itt példaként használható; bármilyen szöveges fájlt feltölthet, és a fájl nevével rendelkező várólista-üzenetet hozhat létre.)

   a. A **Kiszolgálókezelőben**kattintson duplán a létrehozott tároló csomópontjára.

   b. A **Tároló ablakban** válassza a **Feltöltés** gombot.

   ![A Blob feltöltése gomb](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Keresse meg és *jelölje*Program.cs, majd kattintson az **OK gombra.**

1. Hozzon létre egy várólista-üzenetet a korábban létrehozott várólistában, *Program.cs* az üzenet szövegeként.

   ![Üzenetsor-üzenet Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Futtassa a projektet helyileg.

   A várólista-üzenet elindítja a függvényt, amely ezután beolvassa a blobot, és naplózza annak hosszát. A konzol kimenete így néz ki:

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```

## <a name="add-an-output-binding"></a>Kimeneti kötés hozzáadása

A kimeneti kötések egyszerűsítik az adatokat író kódot. Ez a példa módosítja az előzőt a blob egy példányának írásával a mérete naplózása helyett. A Blob storage-kötések szerepelnek a korábban telepített Azure Storage-bővítménycsomagban.

1. Cserélje le az `ProcessQueueMessage` metódust az alábbi kódra:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. Hozzon létre egy másik üzenetsor-üzenetet, *amelynek szövege* Program.cs.

1. Futtassa a projektet helyileg.

   A várólista-üzenet elindítja a függvényt, amely ezután beolvassa a blobot, naplózza annak hosszát, és létrehoz egy új blobot. A konzol kimenete ugyanaz, de amikor a blob tároló ablakába lép, és a Frissítés lehetőséget **választja,** megjelenik egy copy-Program.cs nevű új *blob.*

## <a name="republish-the-updates-to-azure"></a>A frissítések újbóli közzététele az Azure-ban

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

1. A **Közzététel** párbeszédpanelen győződjön meg arról, hogy az aktuális profil ki van jelölve, majd válassza a **Közzététel**lehetőséget. A közzététel eredményeit a **Kimenet** ablak részletezi.
 
1. Ellenőrizze a függvényt az Azure-ban, ha újra feltölt egy fájlt a blobtárolóba, és hozzáad egy üzenetet a várólistához, amely a feltöltött fájl neve. Láthatja, hogy az üzenet törlődik a várólistából, és a blob tárolóban létrehozott fájl másolata. 

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatja, hogyan hozhat létre, futtathat és telepíthet webjobs SDK 3.x projektet.

> [!div class="nextstepaction"]
> [További információ a WebJobs SDK-ról](webjobs-sdk-how-to.md)
