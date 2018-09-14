---
title: Ismerkedés az Azure WebJobs SDK-val
description: Bevezetés a WebJobs SDK-val eseményvezérelt háttérbeli feldolgozásra. Megtudhatja, hogyan érhetik el az Azure-szolgáltatások és a külső szolgáltatások adatait.
services: app-service\web, storage
documentationcenter: .net
author: ggailey777
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: glenga
ms.openlocfilehash: e2f9bee2adcebfd47ca088a1bbe76b870e285044
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45580435"
---
# <a name="get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Az eseményvezérelt háttérben történő feldolgozás az Azure WebJobs SDK használatának első lépései

Ez a cikk bemutatja, hogyan hozzon létre egy Azure WebJobs SDK-projektet, helyileg futtatja az alkalmazást, és üzembe helyezése az Azure App Service-ben.

Az utasítások alapján [Visual Studio 2017](https://www.visualstudio.com/vs/), de ugyanazok a feladatok elvégezhetők a más eszközök, például [Visual Studio Code](https://code.visualstudio.com/).

## <a name="what-is-the-azure-webjobs-sdk"></a>Mi az Azure WebJobs SDK?

Az Azure WebJobs SDK egy keretrendszer, amely megkönnyíti a kódírás háttérben történő feldolgozás, amely az Azure-szolgáltatások adatokhoz fér hozzá a rendszer. Az SDK-t egy deklaratív szintaxis használata mellett a eseményeket indíthat el egy függvényt, például egy új üzenetet egy üzenetsorba hozzáadott funkciókat. Hasonló deklaratív szintaxis azt szabályozza, miután a függvény aktiválódott-e az adatok írásakor vagy olvasásakor. A rendszer az eseményindítók és kötések az Azure és a külső szolgáltatások hozzáféréséhez kapcsolódó alacsony szintű kódolási feladatokat a legtöbb gondoskodik.

### <a name="functions-triggers-and-bindings"></a>Függvények, eseményindítók és kötések

A WebJobs SDK projekt határozza meg, egy vagy több *funkciók*. Egy függvény olyan módszer, amely a podpis metody az eseményindító attribútummal rendelkezik. Eseményindítók adja meg a közönségét egy függvény feltételeit, és kötések adja meg, milyen olvasását és írását. Például a következő függvényt az eseményindító attribútuma arra utasítja a függvény meghívásához, amikor egy üzenetsor üzenet jelenik meg a futtatókörnyezet a `items` várólista. A `Blob` attribútum arra utasítja a futtatókörnyezet, az üzenetsorban található üzenet használatával olvassa el a blobok a *workitems* tároló. Az üzenetsorban található üzenet tartalmának &mdash; megadott a `queueTrigger` paraméter &mdash; a blob neve.

```cs
public static void Run(
    [QueueTrigger("items")] string myQueueItem,
    [Blob("workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    TraceWriter log)
{
    log.Info($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

### <a name="versions-2x-and-3x"></a>Verziók 2.x és 3.x

Az útmutatások a WebJobs SDK verziója 2.x-projekt létrehozása. A WebJobs SDK legújabb verziójának 3.x, de jelenleg előzetes verzióban és ez a cikk még nem rendelkezik verzióra vonatkozó utasításokat. A fő módosítás bevezetett verzió 3.x a .NET Core helyett a .NET-keretrendszer használatát.

### <a name="azure-functions"></a>Azure Functions

[Az Azure Functions](../azure-functions/functions-overview.md) a WebJobs SDK alapul, és a egy olyan beállítás, ha már nincs szükség a WebJobs SDK-t közvetlenül használni. Az Azure Functions 1.x használja a WebJobs SDK 2.x. További információkért lásd: [összehasonlítása az Azure Functions és WebJobs SDK](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy [az Azure-fiók](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) és tapasztalatát a [az Azure App Service apps](app-service-web-overview.md). A jelen cikkben ismertetett lépések végrehajtásához:

* [Visual Studio 2017 telepítése](https://docs.microsoft.com/visualstudio/install/) együtt a **Azure-fejlesztési** számítási feladatot. Ha már rendelkezik a Visual Studióval, de nem rendelkezik az adott számítási feladat, vegye fel a számítási feladatok kiválasztásával **eszközök > első eszközeivel és szolgáltatásaival**.
* [Hozzon létre egy App Service-alkalmazás](app-service-web-get-started-dotnet-framework.md). Ha már rendelkezik ilyennel, amely központilag telepíthető a webjobs-feladat, használhatja, amely egy új létrehozása helyett.

## <a name="create-a-project"></a>Projekt létrehozása

1. A Visual Studióban válassza ki a **fájl > Új projekt**.

1. Válassza ki **Windows klasszikus Asztalialkalmazás > Console App (.NET Framework)**.

1. Adja a projektnek *WebJobsSDKSample*, majd válassza ki **OK**.

   ![New Project (Új projekt) párbeszédpanel](./media/webjobs-sdk-get-started/new-project.png)

## <a name="add-webjobs-nuget-package"></a>Webjobs-feladatok NuGet-csomag hozzáadása

1. Telepítse a legújabb stabil 2.x verzióját a NuGet csomag `Microsoft.Azure.WebJobs`.
 
   Íme a **Package Manager Console** 2.2.0-ás verzió parancsot:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs -version 2.2.0
   ``` 

## <a name="create-the-jobhost"></a>A JobHost létrehozása

A `JobHost` objektum a Functions runtime tároló: figyeli az eseményindítók és a hívások függvény. 

1. A *Program.cs*, adjon hozzá egy `using` utasítást:

   ```cs
   using Microsoft.Azure.WebJobs;
   ```

1. Cserélje le az `Main` metódust az alábbi kódra:

   ```cs
   static void Main()
   {
       var config = new JobHostConfiguration();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

## <a name="enable-console-logging"></a>Konzol naplózás engedélyezése

Többféle módon is naplózása a WebJobs SDK-projektben. Javasoljuk, hogy az egyik a [naplózási keretrendszer ASP.NET Core kifejlesztett](https://docs.microsoft.com/aspnet/core/fundamentals/logging). Ezt a keretrendszert jobb teljesítményt és nagyobb rugalmasságot és szűrés adathordozó érhetők el. 

Ebben a szakaszban beállíthatja az új keretrendszert használó konzol naplózás.

1. Telepítse a következő NuGet-csomagok legújabb stabil verziója:

   * `Microsoft.Extensions.Logging` – A naplózási keretrendszer.
   * `Microsoft.Extensions.Logging.Console` – A konzol *szolgáltató*. Egy szolgáltató elküldi naplók egy adott célra, ebben az esetben a konzolon. 
 
   Az alábbiakban a **Package Manager Console** verzió 2.0.1 parancsokat:

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.0.1
   ``` 

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
   ``` 

1. A *Program.cs*, adjon hozzá egy `using` utasítást:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

1. Az a `Main` metódust, adja hozzá a kódot, hogy frissítse a `JobHostConfiguration` létrehozása előtt a `JobHost`:
 
   ```
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   ```

   Ez a kód az alábbi módosításokat végzi:

   * Letiltja a [irányítópult naplózási](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Az irányítópult egy örökölt eszköz figyelése, és irányítópult-naplózás nem ajánlott a nagy átviteli sebességű termelési forgatókönyvekhez.
   * Hozzáadja a konzol szolgáltató alapértelmezett [szűrés](webjobs-sdk-how-to.md#log-filtering). 

   A `Main` metódus most néz ki:

   ```
   var config = new JobHostConfiguration();
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   var host = new JobHost(config);
   host.RunAndBlock();
   ```
   
## <a name="create-a-function"></a>Függvény létrehozása

1. Hozzon létre *Functions.cs* a projektmappába, és cserélje le a sablonban lévő kód ezzel a kóddal:

   ```cs
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Host;
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

Az Azure Storage emulator helyileg futó nem rendelkezik az a Funkciók, amelyek a WebJobs SDK-nak kell. Tehát ebben a szakaszban, hozzon létre egy tárfiókot az Azure-ban, és konfigurálja a használatára.

1. Nyissa meg **Server Explorer** , és jelentkezzen be az Azure-bA. Kattintson a jobb gombbal a **Azure** csomópontot, és válassza ki **kapcsolódás a Microsoft Azure-előfizetéshez**.

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

## <a name="configure-storage-for-running-locally"></a>Helyileg futó tároló konfigurálása

A WebJobs SDK keres a tároló kapcsolati karakterláncát az alkalmazás beállításainak gyűjteményben. Amikor helyileg futtat, ezt az értéket keresi a *App.config* fájlban vagy környezeti változót.

1. Adja hozzá a következő XML formátumú, hogy a *App.config* fájl a Megnyitás után azonnal `<configuration>` címke.

   ```xml
   <connectionStrings>
     <add name="AzureWebJobsStorage" connectionString="{storage connection string}" />
   </connectionStrings>
   ```

1. Cserélje le *{tárolási kapcsolati karakterlánccal}* , korábban kimásolt kapcsolati karakterlánccal.

   Később újra fel szeretné használni a kapcsolati karakterláncot, amikor konfigurálja az App Service-alkalmazás az Azure-ban.

## <a name="test-locally"></a>Helyi tesztelése

Ebben a szakaszban Ön hozhat létre, és a projekt helyi futtatása, majd aktiválja a függvényt hoz létre üzenetsori üzenet.

1. Nyomja le a Ctrl + F5 billentyűkombinációt a projekt futtatása.

   A konzolon látható, hogy a futtatókörnyezet található a függvényt, és üzenetsorbeli üzenetek indításához, vár.

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   ```

   Megjelenik egy figyelmeztető üzenet egy `ServicePointManager` beállítás. A tesztelési foglalják a projekttel, figyelmen kívül hagyhatja a figyelmeztetést. A figyelmeztetéssel kapcsolatos további információkért lásd: [használata a WebJobs SDK](webjobs-sdk-how-to.md#jobhost-servicepointmanager-settings).

1. A konzolablak bezárásához.

1. A **Server Explorer**, bontsa ki a csomópontot az új tárfiókhoz, és kattintson a jobb gombbal **üzenetsorok**. 

1. Válassza ki **üzenetsor létrehozása**. 

1. Adja meg *várólista* a várólistát, majd válassza ki a neveként **OK**.

   ![Üzenetsor létrehozása](./media/webjobs-sdk-get-started/create-queue.png)

1. Kattintson a jobb gombbal az új várólista a csomópontot, és válassza **nézet várólista**.

1. Válassza ki a **üzenet hozzáadása** ikonra.

   ![Üzenetsor létrehozása](./media/webjobs-sdk-get-started/create-queue-message.png)

1. Az a **üzenet hozzáadása** párbeszédpanelen adja meg *Hello World!* mint a **üzenet**, majd válassza ki **OK**.

   ![Üzenetsor létrehozása](./media/webjobs-sdk-get-started/hello-world-text.png)

1. Futtassa ismét a projektet.

   Mivel a `QueueTrigger` az attribútum a `ProcessQueueMessage` funkciót, az WeJobs SDK modul figyeli a üzenetsorbeli üzenetek indításkor. Megjeleníti egy új üzenetsor nevű üzenetsor *várólista* meghívja a függvényt.

   Miatt [exponenciális visszatartással lekérdezési várólista](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), is igénybe vehet, amíg, mint 2 percet, hogy a futtatókörnyezet találja az üzenetet, és a függvény meghívása. A várakozási idő-ban történő futtatásával csökkenthető [fejlesztői mód](webjobs-sdk-how-to.md#jobhost-development-settings).

  A konzol kimenete a következőhöz hasonló:

   ```console
   Found the following functions:
   WebJobsSDKSample.Functions.ProcessQueueMessage
   info: Host.Startup[0]
         Found the following functions:
         WebJobsSDKSample.Functions.ProcessQueueMessage
   Job host started
   info: Host.Startup[0]
         Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   info: Function[0]
         Hello World!
   info: Host.Results[0]
         Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=ebcb275d-0d7c-4293-a1af-93e0804b9e49)
   ```

1. A konzolablak bezárásához.

## <a name="add-application-insights-logging"></a>Adja hozzá az Application Insights-naplózás

A projekt az Azure-ban fut, amikor a függvény végrehajtása nem tud figyelni a konzolkimenet megtekintésével. A figyelési megoldást, azt javasoljuk, hogy a rendszer [Application Insights](../application-insights/app-insights-overview.md). További információkért lásd: [figyelése az Azure Functions](../azure-functions/functions-monitoring.md).

Ebben a szakaszban a következő feladatokat állíthat be az Application Insights-naplózás az Azure-ban üzembe helyezése előtt hajtsa végre:

* Győződjön meg arról, hogy az App Service-alkalmazások és a egy Application Insights-példány használata.
* Konfigurálja az Application Insights-példány és a korábban létrehozott tárfiók használata az App Service-alkalmazás.
* Állítsa be a naplózást, és az Application Insights projektet.

### <a name="create-app-service-app-and-application-insights-instance"></a>App Service-alkalmazás és az Application Insights-példány létrehozása

1. Ha még nem rendelkezik App Service-alkalmazások, amelyet használhat, [hozzon létre egyet](app-service-web-get-started-dotnet-framework.md).

1. Ha még nem rendelkezik egy Application Insights-erőforrást, amelyet használhat, [hozzon létre egyet](../application-insights/app-insights-create-new-resource.md). Állítsa be **alkalmazástípus** való **általános**, majd ugorjon a következő szakaszok **a kialakítási kulcs másolása**.

1. Ha már rendelkezik egy Application Insights-erőforrást, amelyet szeretne használni, [a kialakítási kulcs másolása](../application-insights/app-insights-create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Alkalmazásbeállítások konfigurálása 

1. A **Server Explorer**, bontsa ki a **App Service-ben** csomópont alatt **Azure**.

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

1. Adja hozzá a következő XML formátumú, hogy a *App.config* fájlt, a kapcsolati karakterláncok gyűjtemény után azonnal.

   ```xml
   <appSettings>
     <add key="APPINSIGHTS_INSTRUMENTATIONKEY" value="{instrumentation key}" />
   </appSettings>
   ```

1. Cserélje le *{kialakítási kulcs}* a rendszerállapotkulcsot az Application Insights-erőforrás, amely használja az.

   Ezen adatok hozzáadása a *App.config* fájl lehetővé teszi, hogy az Application Insights-kapcsolat tesztelése, ha a projekt helyi futtatása. 

1. Mentse a módosításokat.

### <a name="add-application-insights-logging-provider"></a>Az Application Insights naplózási szolgáltató hozzáadása

1. Telepítse a NuGet-csomag legújabb stabil 2.x verzióját az Application Insights-naplózás szolgáltató: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`.

   Íme a **Package Manager Console** 2.2.0-ás verzió parancsot:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
   ``` 

1. Telepítse a NuGet-csomag legújabb stabil 4.x verzióját a .NET-configuration Manager: `System.Configuration.ConfigurationManager`.

   Íme a **Package Manager Console** 4.4.1-es parancsot:

   ```powershell
   Install-Package System.Configuration.ConfigurationManager -version 4.4.1
   ``` 

1. Nyissa meg *Program.cs* , és adja hozzá a `using` a configuration Manager utasítást:

   ```csharp
   using System.Configuration;
   ```

1. Cserélje le a kódot a `Main` módszer a következő kóddal:

   ```csharp
   using (var loggerFactory = new LoggerFactory())
   {
       var config = new JobHostConfiguration();
       var instrumentationKey =
           ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];
       config.DashboardConnectionString = "";
       config.LoggerFactory = loggerFactory
           .AddApplicationInsights(instrumentationKey, null)
           .AddConsole();
       var host = new JobHost(config);
       host.RunAndBlock();
   }
   ```

   Ez a kód az alábbi módosításokat végzi:

   * Hozzáadja az Application Insights-naplózás szolgáltató alapértelmezett [szűrés](webjobs-sdk-how-to.md#log-filtering); összes információt és magasabb szintű naplók mostantól halad át a konzol és az Application Insights ha helyileg futtatja. 
   * -Be illeszti be a `LoggerFactory` az objektum egy `using` letiltása, győződjön meg arról, hogy napló kimenetét kiürül, amikor a gazdagép kilép. 

## <a name="test-application-insights-logging"></a>Teszt Application Insights-naplózás

Ebben a szakaszban, futtassa helyileg újra, győződjön meg arról, hogy naplózási adatok most intézni fog az Application Insights, valamint a konzol esetleges szabályozási hiányosságok elhárítását.

1. Használat **Server Explorer** üzenetsori üzenetek létrehozásához azonos módon tette [korábbi](#trigger-the-function), azzal a különbséggel adja meg *Hello App Insights!* az üzenet szövege.

1. Futtassa a projektet.

   A WebJobs SDK feldolgozza az üzenetsorban található üzenet, és a naplók a konzolablakban láthatja.

1. A konzolablak bezárásához.

1. Nyissa meg a [az Azure portal](https://portal.azure.com/), és nyissa meg az Application Insights-erőforrást.

1. Válassza ki **keresési**.

   ![Válassza ki a keresés](./media/webjobs-sdk-get-started/select-search.png)

1. Ha nem látja a *Hello App Insights!* üzenet válassza **frissítése** rendszeres időközönként néhány percig. (Naplók nem jelennek meg azonnal, mivel az Application Insights-ügyfél a naplók kiürítése egy ideig tart feldolgozza.)

   ![Az Application Insights-naplók](./media/webjobs-sdk-get-started/logs-in-ai.png)

1. A konzolablak bezárásához.

## <a name="deploy-as-a-webjob"></a>A Webjobs üzembe helyezése

Ez a szakasz a Webjobs-feladatként, telepítse a projektet. Az App Service-alkalmazások üzembe helyezés során [korábban létrehozott](#create-app-service-app-and-application-insights-instance). Tesztelheti a kódját az Azure-beli futtatása közben, hogy egy függvény meghívási fog aktiválása hoz létre üzenetsori üzenet.

1. A **Megoldáskezelőben**, és kattintson a jobb gombbal a projektre, majd válassza ki **Publish as Azure WebJob**.

1. Az a **adja hozzá a az Azure webjobs-feladat** párbeszédablakban válassza **OK**.

   ![Az Azure webjobs-feladat felvétele](./media/webjobs-sdk-get-started/add-azure-webjob.png)

   A Visual Studio automatikusan telepíti a NuGet-csomagot a webjobs-feladat közzétételre.

1. Az a **profil** lépésében a **közzététel** varázslóban válassza **Microsoft Azure App Service**.

   ![Közzététel párbeszédpanel](./media/webjobs-sdk-get-started/publish-dialog.png)

1. Az a **App Service-ben** párbeszédablakban válassza **az erőforráscsoport > az App Service-alkalmazást**, majd válassza ki **OK**.

   ![App Service párbeszédpanelen](./media/webjobs-sdk-get-started/app-service-dialog.png)

1. Az a **kapcsolat** . lépés a varázslóban válassza a **közzététel**.

## <a name="trigger-the-function-in-azure"></a>Aktiválja a függvényt az Azure-ban

1. Ellenőrizze, hogy Ön nem helyi futtatás (Ha még mindig nyitva, a a konzolablak bezárásához). Ellenkező esetben a helyi példány lehet az első olyan hoz létre üzenetsor-üzeneteket feldolgoznia.

1. Használat **Server Explorer** üzenetsori üzenetek létrehozásához azonos módon tette [korábbi](#trigger-the-function), azzal a különbséggel adja meg *Hello Azure!*.

1. Frissítse a **várólista** a Visual Studio és az új üzenet lap eltűnt, mert az Azure App Service-ben futó függvény azt feldolgozott.

   > [!TIP]
   > Ha az Azure-ban tesztelt, [fejlesztői mód](webjobs-sdk-how-to.md#jobhost-development-settings) róla, hogy egy üzenetsor eseményindító függvény azonnal, és elkerülheti az késleltetések miatt az [exponenciális visszatartással lekérdezési várólista](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

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

   Ebben a kódban `queueTrigger` van egy [kifejezés kötés](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns), ami azt jelenti, hogy egy másik értéket a futtatáskor mutat.  Futásidőben az üzenetsorban található üzenet tartalmának rendelkezik.

1. Adjon hozzá egy `using`:

   ```cs
   using System.IO;
   ```

1. Hozzon létre egy blobtárolót a tárfiókjában.

   a. A **Server Explorer**, bontsa ki a tárfiók a csomópontot, kattintson a jobb gombbal **Blobok**, majd válassza ki **Blobtároló létrehozása**.

   b. Az a **Blobtároló létrehozása** párbeszédpanelen adja meg *tároló* , a tároló nevét, és kattintson **OK**.

1. Töltse fel a *Program.cs* fájlt a blobtárolóba. (Ez a fájl szolgál példaként; képes bármilyen szöveges fájl feltöltése és a fájlnévvel rendelkező üzenetsori üzenet létrehozására.)

   a. A **Server Explorer**, kattintson duplán a csomópontra az imént létrehozott tárolóba.

   b. Az a **tároló** ablakban válassza ki a **feltöltése** gombra.

   ![BLOB feltöltése gomb](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Keresse meg és jelölje ki *Program.cs*, majd válassza ki **OK**.

1. Az üzenetsorban, korábban létrehozott üzenetsori üzenetek létrehozásához a *Program.cs* , az üzenet szövegét.

   ![Üzenetsorban található üzenet Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. Futtassa a projektet.

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

Kimeneti kötések adatokat író kód egyszerűsítése érdekében. Ebben a példában az előzőre, ha a blob mérete naplózás helyett másolatát módosítja.

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

1. Futtassa a projektet.

   Az üzenetsorban található üzenet aktiválja a függvényt, amely majd beolvassa a blob, naplózza a hossza, és létrehoz egy új blob. A konzol kimenete megegyezik, de ha a blob-tároló ablakban nyissa meg és jelölje ki **frissítése**, megjelenik egy új blob nevű *másolási-Program.cs.*

## <a name="next-steps"></a>További lépések

Ez az útmutató azt mutatja, létrehozása, futtatása és a WebJobs SDK-projekt üzembe helyezése.

Mindent, ami egy WebJobs SDK projektbe megjeleníthető utasításokat volna, hozzon létre egy projektet a teljesen új. Azonban a következő projekt létrehozásakor érdemes használni a **Azure webjobs-feladat** -sablon a **felhőalapú** kategória. Ez a sablon létrehoz egy projektet a NuGet-csomagok és a már beállított mintakódot. Vegye figyelembe, hogy a mintakódot is meg kell változtatni új naplózási keretrendszer használatával.

További információkért lásd: [használata a WebJobs SDK](webjobs-sdk-how-to.md).
