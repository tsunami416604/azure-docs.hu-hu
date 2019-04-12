---
title: Profiljának ASP.NET Core Azure Linux-webalkalmazások Application Insights Profiler |} A Microsoft Docs
description: Fogalmi áttekintése és Application Insights Profiler használatának részletes oktatóanyaga.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.reviewer: mbullwin
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: cweining
ms.openlocfilehash: 35789cc1e516fb24d5e985e12b44fe3cd01b795d
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494748"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Az ASP.NET Core Azure Linux-webalkalmazások Application Insights Profiler-profil

Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

Ismerje meg, hogy mennyi időt vesz igénybe az egyes módszerek az élő webalkalmazásának használatakor [Application Insights](../../azure-monitor/app/app-insights-overview.md). Application Insights Profiler már elérhető az Azure App Service Linux rendszeren futó ASP.NET Core web Apps. Ez az útmutató részletes útmutatás a Profiler nyomkövetések hogyan gyűjthetők össze az ASP.NET Core Linuxos webalkalmazásokat.

Ez az útmutató befejeztével az alkalmazás például a képen látható a nyomkövetés Profiler-nyomkövetések képes összegyűjteni. Ebben a példában a Profiler nyomkövetést azt jelzi, hogy egy bizonyos webes kérés lassú várakozással töltött idő miatt. A *gyakori elérésű útvonal* egy személy ikon jelöli, amelyet a rendszer lassítása az alkalmazást. A **kapcsolatos** metódus az a **HomeController** szakaszban van a webalkalmazás lassítása, mert a metódust hívja a **Thread.Sleep** függvény.

![Profiler hívásláncai](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Előfeltételek
Összes Windows, Linux és Mac fejlesztési környezetre vonatkozik az alábbi utasításokat:

* Telepítse a [.NET Core SDK 2.1.2 vagy újabb](https://dotnet.microsoft.com/download/archives).
* A Git telepítése a webhelyen található utasításokat követve [első lépések – Git telepítése](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>A projekt helyi beállítása

1. Nyissa meg a parancssor ablakát a gépen. Az alábbi utasításokat az összes Windows, Linux és Mac fejlesztési környezetek esetében működik.

1. Az ASP.NET Core MVC webalkalmazás létrehozásához:

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

1. A munkakönyvtárban módosítsa a projekt gyökérkönyvtárába.

1. Adja hozzá a NuGet-csomagot a Profiler-nyomkövetések gyűjtése:

    ```shell
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

1. Engedélyezze az Application Insights a program.cs fájlban:

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseApplicationInsights() // Add this line of code to Enable Application Insights
            .UseStartup<Startup>();
    ```
    
1. A Startup.cs fájlban Profiler engedélyezése:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. Adjon hozzá egy sort a kód a **HomeController.cs** véletlenszerűen késleltetheti néhány másodpercig szakaszban:

    ```csharp
        using System.Threading;
        ...

        public IActionResult About()
            {
                Random r = new Random();
                int delay = r.Next(5000, 10000);
                Thread.Sleep(delay);
                return View();
            }
    ```

1. Mentse és véglegesítse a módosításokat a helyi tárházban:

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>A Linux webalkalmazás üzemeltetéséhez a projekt létrehozása

1. A web app-környezet létrehozása a linuxon futó App Service használatával:

    ![A Linux-webalkalmazás létrehozása](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. Az üzembe helyezési hitelesítő adatok létrehozása:

    > [!NOTE]
    > Jegyezze fel a jelszót a webes alkalmazás üzembe helyezésekor későbbi használat céljából.

    ![Az üzembe helyezési hitelesítő adatok létrehozása](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Adja meg a központi telepítési beállításokat. Állítsa be a webalkalmazás helyi Git-tárházat az utasításokat követve az Azure Portalon. Git-tárház automatikusan létrejön.

    ![A Git-tárház beállítása](./media/profiler-aspnetcore-linux/setup-git-repo.png)

A további telepítési lehetőségekről [Ez a cikk](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type).

## <a name="deploy-your-project"></a>A projekt üzembe helyezése

1. A parancssori ablakot keresse meg a projekt gyökérkönyvtárába. Adjon hozzá egy távoli Git-tárház, mutasson a tárházba az App Service-ben:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Használja a **felhasználónév** az üzembe helyezési hitelesítő adatok létrehozásához használt.
    * Használja a **alkalmazásnév** , hogy használja-e a webes alkalmazás létrehozása Linuxon futó App Service használatával.

2. A projekt telepítése révén a módosításokat az Azure-bA:

    ```
    git push azure master
    ```

Az alábbi példához hasonló kimenetnek kell megjelennie:

    ```
    Counting objects: 9, done.
    Delta compression using up to 8 threads.
    Compressing objects: 100% (8/8), done.
    Writing objects: 100% (9/9), 1.78 KiB | 911.00 KiB/s, done.
    Total 9 (delta 3), reused 0 (delta 0)
    remote: Updating branch 'master'.
    remote: Updating submodules.
    remote: Preparing deployment for commit id 'd7369a99d7'.
    remote: Generating deployment script.
    remote: Running deployment command...
    remote: Handling ASP.NET Core Web Application deployment.
    remote: ......
    remote:   Restoring packages for /home/site/repository/EventPipeExampleLinux.csproj...
    remote: .
    remote:   Installing Newtonsoft.Json 10.0.3.
    remote:   Installing Microsoft.ApplicationInsights.Profiler.Core 1.1.0-LKG
    …

    ```

## <a name="add-application-insights-to-monitor-your-web-apps"></a>A webalkalmazások monitorozása az Application Insights hozzáadása

1. [Hozzon létre egy Application Insights-erőforrást](./../../azure-monitor/app/create-new-resource.md ).

2. Másolás a **Rendszerállapotkulcsot** Application Insights-erőforrás értékének, és a web apps állítson be a következő beállításokat:

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ```

    Ha a beállítások módosítják, a hely automatikusan újraindul. Miután az új beállításokat alkalmazzák, a Profiler azonnal két percig fut. A Profiler majd két percig óránként fut.

3. Hozzon létre a webhely forgalom egy része. A hely frissítése forgalmat is létrehozhat **kapcsolatos** oldal néhány alkalommal.

4. Várjon az események 2 – 5 percet, amíg az Application Insightsba összesített.

5. Tallózással keresse meg az Application Insights **teljesítmény** panel az Azure Portalon. A Profiler nyomkövetések aljának jobb oldalán a panelen tekintheti meg.

    ![Profiler-nyomkövetések megtekintéséhez](./media/profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>Ismert problémák

### <a name="profile-now-button-doesnt-work-for-linux-profiler"></a>Profil gombra a Linux Profiler nem működik.
A Linux-verzióját az App Insights profiler még nem támogatja az igény szerinti profilkészítés a profilt használó most gombra.


## <a name="next-steps"></a>További lépések
Ha egyéni Azure App Service által üzemeltetett tárolók használja, kövesse a [ Service Profiler engedélyezése a tárolóalapú ASP.NET Core alkalmazás](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) Application Insights Profiler engedélyezése.

Jelentheti a hibákat és javaslatokat az Application Insights GitHub-adattárhoz: [ApplicationInsights-Profiler-AspNetCore: Problémák](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
