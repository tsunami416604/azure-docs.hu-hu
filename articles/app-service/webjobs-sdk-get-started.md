---
title: A WebJobs SDK - Azure az első lépései
description: 'A WebJobs SDK eseményvezérelt háttérben történő feldolgozás bemutatása. Útmutató: Azure-szolgáltatások és harmadik féltől származó szolgáltatással lévő adatok eléréséhez.'
services: app-service\web, storage
documentationcenter: .net
author: tdykstra
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/27/2018
ms.author: tdykstra
ms.openlocfilehash: b1de898ec4f661c54b5227367ad416a5edec80ed
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="get-started-with-the-webjobs-sdk"></a>Ismerkedés a WebJobs SDK-val

Ez a cikk bemutatja, hogyan WebJobs SDK-projekt létrehozása, futtassa helyileg, és telepítheti az Azure.

Az utasítások alapján [Visual Studio 2017](https://www.visualstudio.com/vs/), de ugyanazokhoz a feladatokhoz, mint más eszközökkel is elvégezhető [Visual Studio Code](https://code.visualstudio.com/).

## <a name="what-is-the-webjobs-sdk"></a>Mi az a WebJobs SDK

Az Azure WebJobs SDK egy keretrendszer, amely leegyszerűsíti a programozás háttérben történő feldolgozás, aki hozzáfér az adatokat az Azure-szolgáltatásokhoz. Az SDK-t egy deklaratív szintaxisa válthat ki egy funkciót, például egy új üzenet a várólistában hozzá események funkciókat. Meghatározza, hogy hasonló deklaratív szintaxisán olvasását és írását, miután egy függvény lett elindítva. A rendszer az eseményindítók és kötések gondoskodik a társított el az Azure és a harmadik féltől származó szolgáltatással alacsony szintű kódolási feladatok nagy részét.

### <a name="functions-triggers-and-bindings"></a>Függvények, eseményindítók és kötések

A WebJobs SDK projekt határozza meg, egy vagy több *funkciók*. A függvény egy módszer, amelynek eseményindító attribútummal rendelkezik, a a metódus aláírásában. Eseményindítók függvény hívásakor feltételeket határoznak meg, és kötések adja meg, mi olvasását és írását. Például az eseményindító attribútum a következő függvény közli a futtatókörnyezetet, a függvény meghívásához, amikor egy várólista üzenet jelenik meg a `items` várólista. A `Blob` attribútum közli az üzenetsorban lévő üzenetet segítségével olvassa el a blob futásidejű a *elemet* tároló. A várólista üzenet tartalma &mdash; szerepel a `queueTrigger` paraméter &mdash; a blob neve.

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

Az útmutatások meghatározzák a vonatkozó 3.x (az előzetes verzió) eltérő Megjegyzés a WebJobs SDK verzió 2.x projekt létrehozása. A fő módosítása 3.x által bevezetett a .NET Core helyett a .NET-keretrendszer használatát.

### <a name="azure-functions"></a>Azure Functions

[Az Azure Functions](../azure-functions/functions-overview.md) a WebJobs SDK alapul, és egy olyan beállítás, ha már nincs szükség a WebJobs SDK közvetlenül használni. Az Azure Functions 1.x használja a WebJobs SDK 2.x. További információkért lásd: [Azure Functions és a WebJobs SDK összehasonlítása](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy rendelkezik [az Azure-fiók](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) és gyakorlattal [az Azure App Service apps](app-service-web-overview.md). Ebben a cikkben a lépések elvégzéséhez:

* [Telepítse a Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/) rendelkező a **Azure fejlesztési** munkaterhelés. Ha már telepítve a Visual Studio, de nem rendelkezik a munkaterhelés, vegye fel a munkaterhelés kiválasztásával **eszközök > Get eszköz és funkció**.
* [Az App Service-alkalmazás létrehozása](app-service-web-get-started-dotnet-framework.md). Ha már rendelkezik egy, a webjobs-feladat telepítheti, használhatja, amely egy új létrehozása helyett.

## <a name="create-a-project"></a>Projekt létrehozása

1. A Visual Studio válassza **fájl > Új projekt**.

2. Válassza ki **klasszikus Windows asztal > Konzolalkalmazásból (.NET-keretrendszer)**.

   Egy 3.x-projekt létrehozásához válassza **.NET Core > Konzolalkalmazás (.NET Core)**.

3. Nevet a projektnek *WebJobsSDKSample*, majd válassza ki **OK**.

   ![Új projekt párbeszédpanelje](./media/webjobs-sdk-get-started/new-project.png)

## <a name="add-webjobs-nuget-package"></a>Webjobs-feladatok NuGet-csomag hozzáadása

1. Telepítse a legújabb stabil 2.x verzióját a NuGet-csomag `Microsoft.Azure.WebJobs`. (A WebJobs SDK 3.x, válassza a legújabb 3.x.)
 
   Itt a **Csomagkezelő konzol** verzió 2.2.0 parancsot:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs -version 2.2.0
   ``` 

## <a name="create-the-jobhost"></a>A JobHost létrehozása

A `JobHost` célja a futtatási tárolóban függvények: elkezdi figyelni a eseményindítók és hívások funkciók. 

1. A *Program.cs*, vegye fel a `using` utasítást:

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

## <a name="enable-console-logging"></a>Konzol naplózásának engedélyezése

A WebJobs SDK projekt-naplózás több lehetőség áll rendelkezésre. Javasoljuk, hogy az egyik a [naplózási keretrendszer, az ASP.NET Core kifejlesztett](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/logging). Ezt a keretrendszert a jobb teljesítmény és nagyobb rugalmasságot és szűrés adathordozó kínál. 

Ebben a szakaszban beállította az új keretrendszert használó konzol naplózás.

1. Telepítse a legújabb stabil verzióját a következő NuGet-csomagok:

   * `Microsoft.Extensions.Logging` – A naplózási keretrendszer.
   * `Microsoft.Extensions.Logging.Console` -A konzol *szolgáltató*. A szolgáltató elküldi bejegyzéseit, amelyek egy adott célra, ebben az esetben a konzolt. 
 
   Az alábbiakban a **Csomagkezelő konzol** parancsok 2.0.1 verziójához:

   ```powershell
   Install-Package Microsoft.Extensions.Logging -version 2.0.1
   ``` 

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
   ``` 

2. A *Program.cs*, vegye fel a `using` utasítást:

   ```cs
   using Microsoft.Extensions.Logging;
   ```

3. Az a `Main` módszer, vegye fel a kód frissítése a `JobHostConfiguration` létrehozása előtt a `JobHost`:
 
   ```
   config.DashboardConnectionString = "";
   var loggerFactory = new LoggerFactory();
   config.LoggerFactory = loggerFactory
       .AddConsole();
   ```

   Ezt a kódot az alábbi módosításokat végzi:

   * Letiltja a [irányítópult naplózási](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs). Az irányítópult egy örökölt eszköz figyelését, és irányítópult naplózása nem ajánlott nagy átviteli termelési környezetben.
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

1. Hozzon létre *Functions.cs* a projektmappa, és cserélje ki a sablon kódot ezt a kódot:

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

   A `QueueTrigger` attribútum közli a futtatókörnyezetet, ez a függvény hívása új üzenet a az Azure Storage üzenetsorába nevű írásakor `queue`. Az üzenetsorban lévő üzenetet tartalmát metódus kódja végrehajtva a `message` paraméter. A metódus törzse hol dolgozza fel az eseményindítóra vonatkozó információt. Ebben a példában a kód csupán naplózza az üzenetet.

   A `message` paraméter nem rendelkezik karakterlánc. Is köthető egy JSON-objektum, egy bájttömböt vagy egy [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.queue.cloudqueuemessage) objektum. [Várólista eseményindító használati](../azure-functions/functions-bindings-storage-queue.md#trigger---usage). Mindegyik kötési típus (például az üzenetsorok, blobok vagy táblák) rendelkezik egy másik köthető paramétereinek típusa.

## <a name="create-a-storage-account"></a>Create a storage account

Az Azure Storage emulator helyileg futó összes, amelyet a WebJobs SDK szolgáltatás nem rendelkezik. Így ebben a szakaszban, az Azure Storage-fiók létrehozása és a projekt a használatára konfigurálja.

1. Nyissa meg **Server Explorer** , jelentkezzen be az Azure-bA. Kattintson a jobb gombbal a **Azure** csomópont, és válassza **kapcsolódás a Microsoft Azure-előfizetés**.

   ![Bejelentkezés az Azure-ba](./media/webjobs-sdk-get-started/sign-in.png)

1. A a **Azure** csomópontja **Server Explorer**, kattintson a jobb gombbal **tárolási**, majd válassza ki **létrehozása tárfiók**.

   ![Felhasználóifiók-menüjéből tároló létrehozása](./media/webjobs-sdk-get-started/create-storage-account-menu.png)

2. Az a **Storage-fiók létrehozása** párbeszédpanelen adja meg egy egyedi nevet a tárfiók.

3. Válassza ki ugyanazt **régió** , hogy létrehozta az App Service-alkalmazás, vagy a régió zárja be az Ön számára.

1. Kattintson a **Létrehozás** gombra.

   ![Storage-fiók létrehozása](./media/webjobs-sdk-get-started/create-storage-account.png)

1. Az a **tárolási** csomópontja **Server Explorer**, válassza ki az új tárfiókot. Az a **tulajdonságok** ablakban válassza ki a három pont (**...** ) jobb oldalán a **kapcsolati karakterlánc** érték mező.

   ![Kapcsolati karakterlánc három ponttal](./media/webjobs-sdk-get-started/conn-string-ellipsis.png)

2. Másolja a kapcsolati karakterláncot, majd mentse a valahol ezt az értéket, hogy másolja azt könnyen újra.

   ![Másolja a kapcsolati karakterlánc](./media/webjobs-sdk-get-started/copy-key.png)

## <a name="configure-storage-for-running-locally"></a>A helyi tároló konfigurálása

A WebJobs SDK a tárolási kapcsolati karakterlánc az alkalmazásbeállítások gyűjteményben keresi. Amikor helyileg futtat, ezt az értéket keresi a *App.config* fájl vagy a környezeti változók.

1. Adja hozzá az alábbi XML-a *App.config* fájl a Megnyitás után azonnal `<configuration>` címke.

   ```xml
   <connectionStrings>
     <add name="AzureWebJobsStorage" connectionString="{storage connection string}" />
   </connectionStrings>
   ```

2. Cserélje le *{tárolási kapcsolati karakterlánc}* korábban kimásolt kapcsolati karakterlánccal.

   Később újra fel szeretné használni a kapcsolati karakterláncot, amikor konfigurálja az App Service-alkalmazást az Azure-ban.

## <a name="test-locally"></a>Helyi tesztelése

Ebben a szakaszban most felépíti és a projekt helyi futtatása és indul el, a függvény egy üzenetsor-üzenetet hoz létre.

1. Nyomja le a Ctrl + F5 billentyűkombinációt a projekt való futtatásához.

   A konzolon látható, hogy a futtatókörnyezet található, a függvény, és arra vár, hogy elindítani az üzenetsor-üzeneteket.

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

   Megjelenik egy figyelmeztető üzenet a `ServicePointManager` beállítást. A tesztelési kell ennek a projekttel, figyelmen kívül hagyhatja a figyelmeztetést. A figyelmeztetéssel kapcsolatos további információkért lásd: [használata a WebJobs SDK](webjobs-sdk-how-to.md#jobhost-servicepointmanager-settings).

2. Zárja be a konzol ablakot.

1. A **Server Explorer**, bontsa ki a csomópontot az új tárfiók, és kattintson a jobb gombbal **várólisták**. 

2. Válassza ki **várólista létrehozása**. 

3. Adja meg *várólista* a várólista, és válassza a névként **OK**.

   ![Üzenetsor létrehozása](./media/webjobs-sdk-get-started/create-queue.png)

4. Kattintson jobb gombbal az új várólista csomópontra, majd válassza ki **nézet várólista**.

5. Válassza ki a **hozzáadása üzenet** ikonra.

   ![Üzenetsor létrehozása](./media/webjobs-sdk-get-started/create-queue-message.png)

6. Az a **hozzáadása üzenet** párbeszédpanelen adja meg *Hello World!* mint a **üzenet**, majd válassza ki **OK**.

   ![Üzenetsor létrehozása](./media/webjobs-sdk-get-started/hello-world-text.png)

7. Futtassa ismét a projektet.

   Az oka, hogy a `QueueTrigger` attribútumnak a `ProcessQueueMessage` funkció, a WeJobs SDK futásidejű figyeli az üzenetsor-üzeneteket indításakor. Nevű üzenetsor egy új üzenetsor-üzenetet talál *várólista* és a függvényt.

   Miatt [exponenciális leállítási lekérdezési várólista](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm), beletelhet, hogy a lehető leghosszabbak 2 percet, hogy a futtatókörnyezet találja az üzenetet, és a függvényt. A várakozási idő való futtatásával csökkenthető [fejlesztői mód](webjobs-sdk-how-to.md#jobhost-development-settings).

  A konzol kimeneti így néz ki:

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

8. Zárja be a konzol ablakot.

## <a name="add-application-insights-logging"></a>Az Application Insights naplózási hozzáadása

Futtatásakor a projektet az Azure-ban, a függvény végrehajtása nem tud figyelni a konzol kimeneti megtekintésével. A figyelési megoldást javasoljuk, hogy van [Application Insights](../application-insights/app-insights-overview.md). További információkért lásd: [figyelése az Azure Functions](../azure-functions/functions-monitoring.md).

Ebben a szakaszban hajtsa végre az Application Insights-naplózás előtt telepítse az Azure beállítása a következő feladatokat:

* Győződjön meg arról, hogy egy App Service-alkalmazást és egy Application insights szolgáltatással történő együttműködésre.
* Az Application Insights-példány és a korábban létrehozott tárfiókot használja az App Service alkalmazás konfigurálása.
* A projekt az Application insights naplózás beállítása.

### <a name="create-app-service-app-and-application-insights-instance"></a>App Service-alkalmazást és az Application Insights-példány létrehozása

1. Ha még nem rendelkezik egy App Service-alkalmazást, amely akkor használhatja, [hozzon létre egyet](app-service-web-get-started-dotnet-framework.md).

2. Ha még nem telepítette az Application Insights-erőforrást, melyekkel, [hozzon létre egyet](../application-insights/app-insights-create-new-resource.md). Állítsa be **alkalmazástípus** való **általános**, és hagyja ki a következő szakaszok **instrumentation kulcsának az átmásolása**.

3. Ha már rendelkezik egy Application Insights-erőforrást használ, kívánt [instrumentation kulcsának az átmásolása](../application-insights/app-insights-create-new-resource.md#copy-the-instrumentation-key).

### <a name="configure-app-settings"></a>Alkalmazásbeállítások konfigurálása 

1. A **Server Explorer**, bontsa ki a **App Service** csomópontjából **Azure**.

1. Bontsa ki az erőforráscsoportot, amely az App Service-alkalmazást, és kattintson a jobb gombbal az App Service-alkalmazást.

3. Válassza ki **beállítások megtekintéséhez**.

4. Az a **kapcsolati karakterláncok** mezőben adja meg a következő bejegyzés.

   |Name (Név)  |Kapcsolati karakterlánc  |Adatbázis típusa|
   |---------|---------|------|
   |AzureWebJobsStorage | {a tárolási kapcsolati karakterlánc, amely korábban kimásolt}|Egyéni|
   
6. Ha a **Alkalmazásbeállítások** mezőben nem rendelkezik az Application Insights instrumentation kulcsban, adjon a korábban kimásolt. (A rendszerállapot-kulcs már valószínűleg, attól függően, hogyan hozták létre az App Service-alkalmazást.)

   |Name (Név)  |Érték  |
   |---------|---------|
   |APPINSIGHTS_INSTRUMENTATIONKEY | {instrumentation kulcs} |

2. Cserélje le *{instrumentation kulcs}* az Application Insights-erőforrást, amelyen a instrumentation kulccsal.

2. Kattintson a **Mentés** gombra.

1. Adja hozzá az alábbi XML-a *App.config* fájl, a kapcsolati karakterláncok gyűjtemény után azonnal.

   ```xml
   <appSettings>
     <add key="APPINSIGHTS_INSTRUMENTATIONKEY" value="{instrumentation key}" />
   </appSettings>
   ```

2. Cserélje le *{instrumentation kulcs}* az Application Insights-erőforrást, amelyen a instrumentation kulccsal.

   Az ilyen adatok hozzáadása a *App.config* fájl lehetővé teszi, hogy az Application Insights-kapcsolat tesztelése, ha a projekt helyi futtatása. 

3. Mentse a módosításokat.

### <a name="add-application-insights-logging-provider"></a>Az Application Insights-naplózás szolgáltató felvétele

1. Telepítse a legújabb stabil 2.x verzióját a NuGet-csomagot az Application Insights naplózási szolgáltató: `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`. (A WebJobs SDK 3.x, válassza ki a csomag 3.x legfrissebb.)

   Itt a **Csomagkezelő konzol** verzió 2.2.0 parancsot:

   ```powershell
   Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
   ``` 

1. Telepítse a legújabb stabil 4.x verzióját a NuGet-csomagot a .NET-configuration Manager: `System.Configuration.ConfigurationManager`.

   Itt a **Csomagkezelő konzol** parancsot 4.4.1 verzió:

   ```powershell
   Install-Package System.Configuration.ConfigurationManager -version 4.4.1
   ``` 

2. Nyissa meg *Program.cs* , és adja hozzá a `using` a configuration manager utasítást:

   ```csharp
   using System.Configuration;
   ```

2. Cserélje le a kód a `Main` metódus a következő kóddal:

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

   Ezt a kódot az alábbi módosításokat végzi:

   * Az Application Insights naplózási szolgáltató alapértelmezett hozzáadja [szűrés](webjobs-sdk-how-to.md#log-filtering); minden információt és magasabb szintű naplók mostantól halad át a konzol és az Application Insights a helyi futtatás során. 
   * Hozzáadja a `LoggerFactory` objektum egy `using` blokk annak érdekében, hogy kimenet kiürített, amikor a gazdagép kilép. 

## <a name="test-application-insights-logging"></a>Teszt Application Insights naplózás

Ebben a szakaszban futtatja helyileg ismét ellenőrizni, hogy naplózási adatok most fog, valamint az Application Insights a konzol számára.

1. Használjon **Server Explorer** egy üzenetsor létrehozásához a megszokott módon tette [korábbi](#trigger-the-function), azzal a különbséggel adja meg *Hello App Insights!* az üzenet szövegként.

1. Futtassa a projektet.

   A WebJobs SDK feldolgozza az üzenetsorban lévő üzenetet, és a naplókat a konzolablakban meg.

1. Zárja be a konzol ablakot.

1. Nyissa meg a [Azure-portálon](https://portal.azure.com/), és nyissa meg az Application Insights-erőforrást.

2. Válassza ki **keresési**.

   ![Válassza ki a keresés](./media/webjobs-sdk-get-started/select-search.png)

1. Ha nem látja a *Hello App Insights!* üzenet, jelölje be **frissítése** rendszeresen néhány percig. (Naplók nem jelennek meg azonnal, mert az Application Insights-ügyfél a naplók kiürítése igénybe vesz igénybe feldolgozza.)

   ![Az Application Insights-naplók](./media/webjobs-sdk-get-started/logs-in-ai.png)

3. Zárja be a konzol ablakot.

## <a name="deploy-as-a-webjob"></a>Telepítsen egy webjobs-feladat

Ebben a szakaszban egy webjobs-feladat, hogy telepítse a projektet. Telepítheti azt egy App Service-alkalmazást, hogy [korábban létrehozott](#create-app-service-app-and-application-insights-instance). Tesztelheti a kódját, míg az Azure-ban, hogy lesz indít el egy függvény meghívása egy üzenetsor-üzenetet hoz létre.

1. A **Megoldáskezelőben**, kattintson jobb gombbal a projektre, majd válassza ki **Azure webjobs-feladat közzététel**.

1. Az a **adja hozzá a Azure webjobs-feladat** párbeszédablakban válassza **OK**.

   ![Azure webjobs-feladat felvétele](./media/webjobs-sdk-get-started/add-azure-webjob.png)

   A Visual Studio automatikusan telepíti a NuGet-csomagot a webjobs-feladat közzétételre.

1. Az a **profil** lépésében a **közzététel** varázslóban válassza **Microsoft Azure App Service**.

   ![Párbeszédpanelen közzététele](./media/webjobs-sdk-get-started/publish-dialog.png)

1. Az a **App Service** párbeszédablakban válassza **az erőforráscsoport > App Service-alkalmazás**, majd válassza ki **OK**.

   ![App Service párbeszédpanelen](./media/webjobs-sdk-get-started/app-service-dialog.png)

1. Az a **kapcsolat** a varázsló, jelölje be **közzététel**.

## <a name="trigger-the-function-in-azure"></a>A funkció az Azure-ban

1. Győződjön meg arról, hogy nem fut helyi (zárja be a konzol ablakot, ha folyamatban). Ellenkező esetben a helyi példány lehet bármely várólista-üzenetek hoz létre az első.

1. Használjon **Server Explorer** egy üzenetsor létrehozásához a megszokott módon tette [korábbi](#trigger-the-function), azzal a különbséggel adja meg *Hello Azure!*.

7. Frissítse a **várólista** a Visual Studio és az új üzenet lap eltűnt, mert a függvény az Azure App Service-ben futó feldolgozni azt.

   > [!TIP]
   > Ha a tesztelést az Azure-ban, [fejlesztői mód](webjobs-sdk-how-to.md#jobhost-development-settings) róla, hogy a várólista funkció azonnal, és elkerülheti a késedelmeket miatt a [exponenciális leállítási lekérdezési várólista](../azure-functions/functions-bindings-storage-queue.md#trigger---polling-algorithm).

### <a name="view-logs-in-application-insights"></a>Naplók megtekintése az Application Insightsban

1. Nyissa meg a [Azure-portálon](https://portal.azure.com/), és nyissa meg az Application Insights-erőforrást.

2. Válassza ki **keresési**.

1. Ha nem látja a *Hello Azure!* üzenet, jelölje be **frissítése** rendszeresen néhány percig.

   Megjelenik a naplókat, a függvény futó webjobs-feladat, beleértve a *Hello Azure!* az előző szakaszban leírt megadott szöveg.

## <a name="add-an-input-binding"></a>Egy bemeneti kötés hozzáadása

Bemeneti kötések leegyszerűsíti a kódot, amely adatokat olvas. Az ebben a példában az üzenetsorban lévő üzenetet egy blob neve lesz, és alkalmazhatja a blob nevének kell megkeresni és értelmezni az Azure Storage blob.

1. A *Functions.cs*, cserélje le a `ProcessQueueMessage` metódus a következő kóddal:

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   Ebben a kódban `queueTrigger` van egy [kötési kifejezés](../azure-functions/functions-triggers-bindings.md#binding-expressions-and-patterns), ami azt jelenti, hogy oldja fel egy másik értéket futásidőben.  Futásidőben az üzenetsorban lévő üzenetet tartalmát rendelkezik.

2. Adja hozzá a `using`:

   ```cs
   using System.IO;
   ```

3. A tárfiók a blob-tároló létrehozása

   a. A **Server Explorer**, bontsa ki a tárfiók a csomópontot, kattintson a jobb gombbal **Blobok**, majd válassza ki **Blob-tároló létrehozása**.

   b. Az a **Blob-tároló létrehozása** párbeszédpanelen adja meg *tároló* lehetőséget a tároló neve, majd kattintson **OK**.

4. Töltse fel a *Program.cs* a blob-tároló fájlt. (Ez a fájl szolgál példaként; akkor sikerült bármely szöveges fájl feltöltéséhez, és hozzon létre egy üzenetsor-üzenetet a fájl nevét.)

   a. A **Server Explorer**, kattintson duplán az imént létrehozott tároló csomópontjára.

   b. Az a **tároló** ablakban válassza ki a **feltöltése** gombra.

   ![A BLOB feltöltési gomb](./media/webjobs-sdk-get-started/blob-upload-button.png)

   c. Keresse meg és jelölje ki *Program.cs*, majd válassza ki **OK**.

5. Hozzon létre egy üzenetsor-üzenetet a várólistában, korábban létrehozott rendelkező *Program.cs* , az üzenet szövegét.

   ![Üzenetsor Program.cs](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

6. Futtassa a projektet.

   Az üzenetsorban lévő üzenetet elindítja a függvény, amely majd beolvassa a blob, és annak naplózza. A konzol kimeneti így néz ki:

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

Kimeneti kötések leegyszerűsíti a kódot, amely írja az adatokat. Ebben a példában az előzőre által a blobbal helyett annak méretét naplózás másolatát módosítja.

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

5. Hozzon létre egy másik várólistához üzenet *Program.cs* , az üzenet szövegét.

6. Futtassa a projektet.

   Az üzenetsor-üzenetet, a függvény, amely majd beolvassa a blob, annak naplózza, és létrehoz egy új blob váltja ki. A konzol kimeneti megegyezik, de ha nyissa meg a blob-tároló ablakot, és válassza ki **frissítése**, megjelenik egy új blob nevű *másolási-Program.cs.*

## <a name="next-steps"></a>További lépések

Ez az útmutató azt mutatja, hogyan hozzon létre, futtatása és WebJobs SDK projekt telepítése.

Minden, ami a WebJobs SDK projekt hiányzóra megjelenítéséhez utasításokat kellett projektet hozhat létre teljesen. Azonban a következő projekt létrehozásakor érdemes használni a **Azure webjobs-feladat** -sablon a **felhő** kategóriát. Ez a sablon létrehoz egy projektet a NuGet-csomagok és a már beállított mintakód. Ne feledje, hogy előfordulhat, hogy a mintakódot az új naplózási keretrendszer használatával módosíthatók.

További információkért lásd: [használata a WebJobs SDK](webjobs-sdk-how-to.md).
