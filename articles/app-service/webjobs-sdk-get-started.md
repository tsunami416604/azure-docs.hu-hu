---
title: A WebJobs SDK - Azure használatának első lépései
description: Bevezetés a WebJobs SDK-val eseményvezérelt háttérbeli feldolgozásra. Megtudhatja, hogyan érhetik el az Azure-szolgáltatások és a külső szolgáltatások adatait.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: jeconnoc
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: b824c99a015cfa2c1d1c75e2a1257eff482e8dd6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58087958"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Az eseményvezérelt háttérben történő feldolgozás az Azure WebJobs SDK használatának első lépései

Ez a cikk bemutatja, hogyan használható a Visual Studio 2017 Azure WebJobs SDK-projekt létrehozása, helyileg futtatja az alkalmazást, és hogyan telepítheti azt [Azure App Service](overview.md). A projektet hoz létre egy .NET Core-konzolalkalmazást, amely verzióját használja, a WebJobs SDK 3.x. Ha érdekli a verzió 2.x, amely a .NET-keretrendszert használja, lásd: [fejlesztés és üzembe helyezése a Visual Studio használatával – az Azure App Service WebJobs](webjobs-dotnet-deploy-vs.md).

Használata a WebJobs SDK-val kapcsolatos további információkért lásd: [az Azure WebJobs SDK használata az eseményvezérelt háttérben történő feldolgozás](webjobs-sdk-how-to.md).

## <a name="prerequisites"></a>Előfeltételek

* [Visual Studio 2017 telepítése](/visualstudio/install/) együtt a **Azure-fejlesztési** számítási feladatot. Ha már rendelkezik a Visual Studióval, de nem rendelkezik az adott számítási feladat, vegye fel a számítási feladatok kiválasztásával **eszközök > első eszközeivel és szolgáltatásaival**.

* Rendelkeznie kell [az Azure-fiók](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) a WebJobs SDK projekt közzététele az Azure-bA.

## <a name="create-a-project"></a>Projekt létrehozása

1. A Visual Studióban válassza ki a **File > New > Project**.

2. Válassza ki **.NET Core > Console App (.NET Core)**.

3. Adja a projektnek *WebJobsSDKSample*, majd válassza ki **OK**.

   ![New Project (Új projekt) párbeszédpanel](./media/webjobs-sdk-get-started/new-project.png)

## <a name="webjobs-nuget-packages"></a>Webjobs-feladatok NuGet-csomagok

1. Telepítse a legújabb stabil 3.x verzióit a következő NuGet-csomagok:

   * `Microsoft.Azure.WebJobs`
   * `Microsoft.Azure.WebJobs.Extensions`

     Íme a **Package Manager Console** verzió 3.0.4 parancsokat:

     ```powershell
     Install-Package Microsoft.Azure.WebJobs -version 3.0.4
     Install-Package Microsoft.Azure.WebJobs.Extensions -version 3.0.1
     ```

## <a name="create-the-host"></a>A gazdagép létrehozása

A gazdagép az a futásidejű tároló, függvények, figyeli a functions eseményindítók és a hívások. Az alábbi lépéseket, hozzon létre egy gazdaszolgáltatást, amely megvalósítja [ `IHost` ](/dotnet/api/microsoft.extensions.hosting.ihost), amely az, hogy a általános gazdagép, az ASP.NET Core.

1. A *Program.cs*, adjon hozzá egy `using` utasítást:

    ```cs
    using Microsoft.Extensions.Hosting;
    ```

1. Cserélje le az `Main` metódust az alábbi kódra:

    ```cs
    static void Main(string[] args)
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

ASP.NET Core, a gazdagép-konfigurációk vannak beállítva, a metódusok meghívásával a [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) példány. További információkért lásd: [.NET általános gazdagép](/aspnet/core/fundamentals/host/generic-host). A `ConfigureWebJobs` metódust inicializálja a WebJobs-gazdagép. A `ConfigureWebJobs`, adott WebJobs-bővítmények inicializálása, és ezek a bővítmények tulajdonságainak beállítása.  

## <a name="enable-console-logging"></a>Konzol naplózás engedélyezése

Ebben a szakaszban állítsa be a konzol naplózás, amely használja a [ASP.NET Core naplózási keretrendszer](/aspnet/core/fundamentals/logging).

1. Telepítse a következő NuGet-csomagok legújabb stabil verziója:

   * `Microsoft.Extensions.Logging` – A naplózási keretrendszer.
   * `Microsoft.Extensions.Logging.Console` – A konzol szolgáltató, amely naplókat küld a konzolon.

   Az alábbiakban a **Package Manager Console** 2.2.0-ás verzió parancsokat:

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.2.0
   ```

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.2.0
   ```

1. A *Program.cs*, adjon hozzá egy `using` utasítást:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. Hívja a [ `ConfigureLogging` ](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) metódust [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder). A [ `AddConsole` ](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) metódus konzol naplózási hozzáadja a konfigurációt.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    A `Main` metódus most néz ki:

    ```cs
    static void Main(string[] args)
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
            host.Run();
        }
    }
    ```

    Ez a frissítés a következőket teszi:

    * Letiltja a [irányítópult naplózási](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Az irányítópult egy örökölt eszköz figyelése, és irányítópult-naplózás nem ajánlott a nagy átviteli sebességű termelési forgatókönyvekhez.
    * Hozzáadja a konzol szolgáltató alapértelmezett [szűrés](webjobs-sdk-how-to.md#log-filtering).

Most már hozzáadhat érkező üzenetek által aktivált függvény egy [Azure Storage-üzenetsor](../azure-functions/functions-bindings-storage-queue.md).

## <a name="install-the-storage-binding-extension"></a>A Storage-kötésbővítmény telepítése

Verziótól kezdve 3.x, explicit módon telepíteni kell a tárolási kötési bővítmény, a WebJobs SDK által igényelt. A korábbi verziókban a Storage-kötések az SDK-t is tartalmazza.

1. Telepítse a legújabb stabil verzióját, a [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet-csomag verziója 3.x. 

    Íme a **Package Manager Console** verzió 3.0.3 parancsot:

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version 3.0.3
    ```

2. Az a `ConfigureWebJobs` metódust, a hívás a `AddAzureStorage` metódust a [ `HostBuilder` ](/dotnet/api/microsoft.extensions.hosting.hostbuilder) példány inicializálni a Storage-bővítményt. Ezen a ponton a `ConfigureWebJobs` módszer a következő példához hasonlóan néz ki:

    ```cs
    builder.ConfigureWebJobs(b =>
                    {
                        b.AddAzureStorageCoreServices();
                        b.AddAzureStorage();
                    });
    ```

## <a name="create-a-function"></a>Függvény létrehozása

1. Kattintson a jobb gombbal a projektre, válassza ki **Hozzáadás** > **új elem...** , válassza a **osztály**, nevezze el az új C# osztályban fájlba *Functions.cs*, és válassza ki **Hozzáadás**.

1. Cserélje le a létrejött sablon Functions.cs, a következő kóddal:

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

   A `QueueTrigger` attribútum arra utasítja a modul egy Azure Storage-üzenetsor nevű új üzenet íródik a függvény hívására `queue`. Az üzenetsorban található üzenet tartalmának metódus kódja vannak megadva a `message` paraméter. A metódus törzsét, ahol dolgozza fel az eseményindítóra vonatkozó információt. Ebben a példában a kód csak naplózza az üzenetet.

   A `message` paraméter nem rendelkezik karakterláncot. Akkor is kötést hozhasson létre egy JSON-objektumot, egy bájttömböt vagy egy [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) objektum. [Üzenetsor eseményindító használati](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Kell kötni a paraméter-típusok különböző minden kötési típus (például az üzenetsorok, blobok és táblázatok) rendelkezik.

## <a name="create-a-storage-account"></a>Tárfiók létrehozása

Az Azure Storage emulator helyileg futó nem rendelkezik az a Funkciók, amelyek a WebJobs SDK-nak kell. Tehát ebben a szakaszban, hozzon létre egy tárfiókot az Azure-ban, és konfigurálja a használatára. Ha már rendelkezik egy storage-fiókot, csak a 6. lépés kihagyása.

1. Nyissa meg **Server Explorer** a Visual studio és a jelentkezzen be az Azure-bA. Kattintson a jobb gombbal a **Azure** csomópontot, és válassza ki **kapcsolódás a Microsoft Azure-előfizetéshez**.

   ![Bejelentkezés az Azure-ba](./media/webjobs-sdk-get-started/sign-in.png)

1. Alatt a **Azure** csomópontja **Server Explorer**, kattintson a jobb gombbal **tárolási**, majd válassza ki **hozzon létre tárfiókot**.

   ![Hozzon létre a Tárfiók menüjében](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

1. Az a **Create Storage Account** párbeszédpanelen adja meg a tárfiók egyedi nevét.

1. Válassza ki ugyanazt **régió** , hogy az App Service-alkalmazáshoz a létrehozott, vagy egy régióban, akkor zárja be.

1. Kattintson a **Létrehozás** gombra.

   ![Storage-fiók létrehozása](./media/webjobs-sdk-get-started/create-storage-account.png)

1. Alatt a **tárolási** csomópontja **Server Explorer**, válassza ki az új tárfiókot. Az a **tulajdonságok** ablakban kattintson a három pontra (**...** ) jobb oldalán a **kapcsolati karakterlánc** érték mező.

   ![Kapcsolati karakterlánc három pont](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

1. Másolja a kapcsolati karakterláncot, és mentse a valahol ezt az értéket, hogy másolja azt könnyen újra.

   ![Kapcsolati karakterlánc másolása](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-to-run-locally"></a>Futtassa helyileg a tárolás konfigurálása

A WebJobs SDK-t keresi a tárolási kapcsolat karakterláncát az alkalmazásbeállításokban, az Azure-ban. Amikor helyileg futtat, ezt az értéket a helyi konfigurációs fájlban vagy környezeti változók keres.

1. Kattintson a jobb gombbal a projektre, válassza ki **Hozzáadás** > **új elem...** , válassza a **JavaScript JSON-konfigurációs fájl**, nevezze el az új fájl *appsettings.json* , majd válasszon **Hozzáadás**. 

1. Az új fájlban adja hozzá a `AzureWebJobsStorage` mezőt, a következő példához hasonlóan:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. Cserélje le *{tárolási kapcsolati karakterlánccal}* , korábban kimásolt kapcsolati karakterlánccal.

1. Válassza ki a *appsettings.json* fájlt a Megoldáskezelőben, majd a a **tulajdonságok** ablak **Copy to Output Directory** való **másolás, ha újabb**.

Később az alkalmazás Azure App Service-ben a kapcsolati karakterlánc azonos Alkalmazásbeállítás fogja hozzáadni.

## <a name="test-locally"></a>Helyi tesztelése

Ebben a szakaszban Ön hozhat létre, és a projekt helyi futtatása, majd aktiválja a függvényt hoz létre üzenetsori üzenet.

1. Nyomja meg **Ctrl + F5** , futtassa a projektet.

   A konzolon látható, hogy a futtatókörnyezet található a függvényt, és üzenetsorbeli üzenetek indításához, vár. A következő kimenet jön létre a v3.x gazdagép:

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

1. A konzolablak bezárásához.

1. A **Server Explorer** a Visual Studióban bontsa ki a csomópontot az új tárfiókhoz, és kattintson a jobb gombbal **üzenetsorok**.

1. Válassza ki **üzenetsor létrehozása**.

1. Adja meg *várólista* a várólistát, majd válassza ki a neveként **OK**.

   ![Üzenetsor létrehozása](./media/webjobs-sdk-get-started/create-queue.png)

1. Kattintson a jobb gombbal az új várólista a csomópontot, és válassza **nézet várólista**.

1. Válassza ki a **üzenet hozzáadása** ikonra.

   ![Üzenetsor létrehozása](./media/webjobs-sdk-get-started/create-queue-message.png)

1. Az a **üzenet hozzáadása** párbeszédpanelen adja meg *Hello World!* mint a **üzenet**, majd válassza ki **OK**. Nincs most egy üzenetet az üzenetsorba.

   ![Üzenetsor létrehozása](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Futtassa ismét a projektet.

   Mivel a `QueueTrigger` az attribútum a `ProcessQueueMessage` funkciót, az WeJobs SDK modul figyeli a üzenetsorbeli üzenetek indításkor. Megjeleníti egy új üzenetsor nevű üzenetsor *várólista* meghívja a függvényt.

   Miatt [exponenciális visszatartással lekérdezési várólista](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), is igénybe vehet, amíg, mint 2 percet, hogy a futtatókörnyezet találja az üzenetet, és a függvény meghívása. A várakozási idő-ban történő futtatásával csökkenthető [fejlesztői mód](webjobs-sdk-how-to.md#host-development-settings).

   A konzol kimenete a következőhöz hasonló:

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

1. A konzolablak bezárásához. 

1. Lépjen vissza a várólista-ablakot, és frissítse azt. Az üzenet azért eltűnt, mert a helyileg futó függvény dolgozott. 

## <a name="add-application-insights-logging"></a>Adja hozzá az Application Insights-naplózás

A projekt az Azure-ban fut, amikor a függvény végrehajtása nem tud figyelni a konzolkimenet megtekintésével. A figyelési megoldást, azt javasoljuk, hogy a rendszer [Application Insights](../azure-monitor/app/app-insights-overview.md). További információkért lásd: [figyelése az Azure Functions](../azure-functions/functions-monitoring.md).

Ebben a szakaszban a következő feladatokat állíthat be az Application Insights-naplózás az Azure-ban üzembe helyezése előtt hajtsa végre:

* Győződjön meg arról, hogy az App Service-alkalmazások és a egy Application Insights-példány használata.
* Konfigurálja az Application Insights-példány és a korábban létrehozott tárfiók használata az App Service-alkalmazás.
* Állítsa be a naplózást, és az Application Insights projektet.

### <a name="create-app-service-app-and-application-insights-instance"></a>App Service-alkalmazás és az Application Insights-példány létrehozása

1. Ha még nem rendelkezik App Service-alkalmazások, amelyet használhat, [hozzon létre egyet](app-service-web-get-started-dotnet-framework.md). Az alkalmazás létrehozásakor is létrehozhat egy csatlakoztatott Application Insights-erőforrást. Ha így tesz, a `APPINSIGHTS_INSTRUMENTATIONKEY` az alkalmazásban van beállítva.

1. Ha még nem rendelkezik egy Application Insights-erőforrást, amelyet használhat, [hozzon létre egyet](../azure-monitor/app/create-new-resource.md ). Állítsa be **alkalmazástípus** való **általános**, majd ugorjon a következő szakaszok **a kialakítási kulcs másolása**.

1. Ha már rendelkezik egy Application Insights-erőforrást, amelyet szeretne használni, [a kialakítási kulcs másolása](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Alkalmazásbeállítások konfigurálása 

1. A **Server Explorer** a Visual Studióban bontsa ki a **App Service-ben** csomópont alatt **Azure**.

1. Bontsa ki az erőforráscsoportot, amely az App Service-alkalmazást, és kattintson a jobb gombbal az App Service-alkalmazást.

1. Válassza ki **beállításainak megtekintéséhez**.

1. Az a **kapcsolati karakterláncok** adjon hozzá a következő bejegyzés.

   |Name (Név)  |kapcsolati karakterlánc  |Adatbázis típusa|
   |---------|---------|------|
   |AzureWebJobsStorage | {a tárolási kapcsolati karakterlánc, amely korábban vágólapra másolt}|Egyéni|

1. Ha a **Alkalmazásbeállítások** mezőben nem rendelkezik egy Application Insights-kialakítási kulcs, amelyik korábban vágólapra másolt hozzáadása. (A kialakítási kulcsot már valószínűleg, attól függően, hogy létrehozta az App Service-alkalmazás.)

   |Name (Név)  |Érték  |
   |---------|---------|
   |ÁLLÍTANI AZ APPINSIGHTS_INSTRUMENTATIONKEY | {kialakítási kulcs} |

1. Cserélje le *{kialakítási kulcs}* a rendszerállapotkulcsot az Application Insights-erőforrás, amely használja az.

1. Kattintson a **Mentés** gombra.

1. Az Application Insights-kapcsolat hozzáadása a projekthez, hogy helyileg is futtathatja. Az a *appsettings.json* fájlt, adjon hozzá egy `APPINSIGHTS_INSTRUMENTATIONKEY` mezőhöz viszi, az alábbi példában látható módon:

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}",
        "APPINSIGHTS_INSTRUMENTATIONKEY": "{instrumentation key}"
    }
    ```

    Cserélje le *{kialakítási kulcs}* a rendszerállapotkulcsot az Application Insights-erőforrás, amely használja az.

1. Mentse a módosításokat.

### <a name="add-application-insights-logging-provider"></a>Az Application Insights naplózási szolgáltató hozzáadása

Kihasználásához [Application Insights](../azure-monitor/app/app-insights-overview.md) naplózás, frissítheti a naplózási kódot, tegye a következőket:

* -Alapértelmezett naplózás az Application Insights-szolgáltató hozzáadása [szűrés](webjobs-sdk-how-to.md#log-filtering); összes információt és a magasabb szintű naplók kerül a konzol és az Application Insights, ha helyileg futtatja.
* Helyezze a `LoggerFactory` az objektum egy `using` letiltása, győződjön meg arról, hogy napló kimenetét kiürül, amikor a gazdagép kilép.

1. Telepítse a legújabb stabil 3.x verziót, a NuGet-csomag az Application Insights-naplózás szolgáltató: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`.

   Íme a **Package Manager Console** verzió 3.0.2-es parancsot:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version 3.0.2
   ```

1. Nyissa meg *Program.cs* , és cserélje le a kódot a `Main` módszer a következő kóddal:

    ```cs
    static void Main(string[] args)
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
                        b.AddApplicationInsights(o => o.InstrumentationKey = instrumentationKey);
                    }
                });
        var host = builder.Build();
        using (host)
        {
            host.Run();
        }
    }
    ```

    Az Application Insights-szolgáltató hozzáadása a naplózást, a korábban hozzáadott az alkalmazásbeállítások kulcs használatával.

## <a name="test-application-insights-logging"></a>Teszt Application Insights-naplózás

Ebben a szakaszban, futtassa helyileg újra, győződjön meg arról, hogy naplózási adatok most intézni fog az Application Insights, valamint a konzol esetleges szabályozási hiányosságok elhárítását.

1. Használat **Server Explorer** a Visual Studióban hozzon létre üzenetsori üzenet, ugyanúgy tette [korábbi](#trigger-the-function-in-azure), azzal a különbséggel adja meg *Hello App Insights!* az üzenet szövege.

1. Futtassa a projektet.

   A WebJobs SDK feldolgozza az üzenetsorban található üzenet, és a naplók a konzolablakban láthatja.

1. A konzolablak bezárásához.

1. Nyissa meg a [az Azure portal](https://portal.azure.com/), és nyissa meg az Application Insights-erőforrást.

1. Válassza ki **keresési**.

   ![Válassza ki a keresés](./media/webjobs-sdk-get-started/select-search.png)

1. Ha nem látja a *Hello App Insights!* üzenet válassza **frissítése** rendszeres időközönként néhány percig. (Naplók nem jelennek meg azonnal, mivel az Application Insights-ügyfél a naplók kiürítése egy ideig tart feldolgozza.)

   ![Az Application Insights-naplók](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. A konzolablak bezárásához.

## <a name="deploy-as-a-webjob"></a>Helyezze üzembe az Azure

A telepítés során használandó futtathatja a függvényeit alkalmazás szolgáltatáspéldány létrehozása. Amikor közzétesz egy .NET Core-konzolalkalmazást az Azure App Service-ben, azt automatikusan lekéri futtatása a webjobs-feladat. Közzététel kapcsolatos további információkért lásd: [fejlesztés és a WebJobs üzembe helyezése Visual studióval](webjobs-dotnet-deploy-vs.md).

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

## <a name="trigger-the-function-in-azure"></a>Aktiválja a függvényt az Azure-ban

1. Ellenőrizze, hogy Ön nem helyi futtatás (Ha még mindig nyitva, a a konzolablak bezárásához). Ellenkező esetben a helyi példány lehet az első olyan hoz létre üzenetsor-üzeneteket feldolgoznia.

1. Az a **várólista** lapon, a Visual Studióban, mint korábban adjon meg egy üzenetet az üzenetsorba.

1. Frissítse a **várólista** oldalon, és az új üzenet eltűnik, mert az Azure-ban futó függvény dolgozott.

   > [!TIP]
   > Ha az Azure-ban tesztelt, [fejlesztői mód](webjobs-sdk-how-to.md#host-development-settings) róla, hogy egy üzenetsor eseményindító függvény azonnal, és elkerülheti az késleltetések miatt az [exponenciális visszatartással lekérdezési várólista](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Naplók megtekintése az Application insights szolgáltatásban

1. Nyissa meg a [az Azure portal](https://portal.azure.com/), és nyissa meg az Application Insights-erőforrást.

1. Válassza ki **keresési**.

1. Ha nem látja a *Hello Azure!* üzenet válassza **frissítése** rendszeres időközönként néhány percig.

   Láthatja, hogy a webjobs-feladat, a függvény naplóinak többek között a *Hello Azure!* az előző szakaszban megadott szöveg.

## <a name="add-an-input-binding"></a>Bemeneti kötés hozzáadása

A bemeneti kötések egyszerűbbé teszik a kódot, amely adatokat olvas be. Ebben a példában az üzenetsorban található üzenet lesz a blob nevét, és a blob nevét fogja használni a keresése, és az Azure Storage-blob olvasása.

1. A *Functions.cs*, cserélje le a `ProcessQueueMessage` módszer a következő kóddal:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   Ebben a kódban `queueTrigger` van egy [kifejezés kötés](../azure-functions/functions-bindings-expressions-patterns.md), ami azt jelenti, hogy egy másik értéket a futtatáskor mutat.  Futásidőben az üzenetsorban található üzenet tartalmának rendelkezik.

1. Adjon hozzá egy `using`:

   ```cs
   using System.IO;
   ```

1. Hozzon létre egy blobtárolót a tárfiókjában.

   a. A **Server Explorer** a Visual Studióban bontsa ki a tárfiók csomópontot, kattintson a jobb gombbal **Blobok**, majd válassza ki **Blobtároló létrehozása**.

   b. Az a **Blobtároló létrehozása** párbeszédpanelen adja meg *tároló* , a tároló nevét, és kattintson **OK**.

1. Töltse fel a *Program.cs* fájlt a blobtárolóba. (Ez a fájl szolgál példaként; képes bármilyen szöveges fájl feltöltése és a fájlnévvel rendelkező üzenetsori üzenet létrehozására.)

   a. A **Server Explorer**, kattintson duplán a csomópontra a létrehozott tárolóba.

   b. Az a **tároló** ablakban válassza ki a **feltöltése** gombra.

   ![BLOB feltöltése gomb](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Keresse meg és jelölje ki *Program.cs*, majd válassza ki **OK**.

1. Az üzenetsorban, korábban létrehozott üzenetsori üzenetek létrehozásához a *Program.cs* , az üzenet szövegét.

   ![Üzenetsorban található üzenet Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. A projekt helyi futtatása.

   Az üzenetsorban található üzenet aktiválja a függvényt, amely majd beolvassa a blobot, és a hossza naplózza. A konzol kimenete a következőhöz hasonló:

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

Kimeneti kötések adatokat író kód egyszerűsítése érdekében. Ebben a példában az előzőre, ha a blob mérete naplózás helyett másolatát módosítja. BLOB storage-kötések az Azure Storage-kiterjesztési csomag, amely korábban telepítette szerepelnek.

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

1. Hozzon létre egy másik rendelkező üzenetsori üzenet *Program.cs* , az üzenet szövegét.

1. A projekt helyi futtatása.

   Az üzenetsorban található üzenet aktiválja a függvényt, amely majd beolvassa a blob, naplózza a hossza, és létrehoz egy új blob. A konzol kimenete megegyezik, de ha a blob-tároló ablakban nyissa meg és jelölje ki **frissítése**, megjelenik egy új blob nevű *másolási-Program.cs.*

## <a name="republish-the-updates-to-azure"></a>Tegye közzé újra a frissítések az Azure-bA

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

1. Az a **közzététel** párbeszédpanelen győződjön meg arról, hogy a jelenlegi profilja van kiválasztva, és válassza a **közzététel**. A közzététel eredményeit a részletes leírást talál a **kimeneti** ablak.
 
1. Ellenőrizze a függvény az Azure-ban újra egy fájl feltöltése a blob-tárolóba, és felvesz egy üzenetet az üzenetsorba, a feltöltött fájl neve. Az üzenetsor és a egy példányát a létrehozott blob-tárolóban fájlt törlődnek üzenetet látja. 

## <a name="next-steps"></a>További lépések

Ez a cikk bemutatta létrehozása, futtatása és a WebJobs SDK-val 3.x projekt telepítése.

> [!div class="nextstepaction"]
> [További információ a WebJobs SDK-val](webjobs-sdk-how-to.md)
