---
title: Profil ASP.NET Core Azure Linux webalkalmazások Application Insights Profiler | Microsoft dokumentumok
description: Fogalmi áttekintés és részletes oktatóanyag az Application Insights Profiler használatáról.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 02/23/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 9c98cd5d3d4d76f9455e4c036aa32a4ead20cfff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671715"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Profil ASP.NET Core Azure Linux webalkalmazások az Application Insights Profiler segítségével

Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

Az Application Insights használatakor megtudhatja, hogy mennyi időt tölt az élő webalkalmazás egyes [metódusai.](../../azure-monitor/app/app-insights-overview.md) Az Application Insights Profiler már elérhető ASP.NET Core webalkalmazásokhoz, amelyek Linux ban vannak üzemeltetve az Azure App Service szolgáltatásban. Ez az útmutató lépésenként ismerteti, hogyan lehet a Profiler-nyomkövetések gyűjtése ASP.NET Core Linux webalkalmazások.

A forgatókönyv befejezése után az alkalmazás összegyűjtheti a Profiler-nyomkövetéseket, például a képen látható nyomkövetéseket. Ebben a példában a Profiler-nyomkövetés azt jelzi, hogy egy adott webes kérelem lassú a várakozással töltött idő miatt. A *forró elérési utat* a kódot, amely lassítja az alkalmazást, egy láng ikon jelöli. A **HomeController** szakasz **Névjegy** metódusa lelassítja a webalkalmazást, mert a metódus a **Thread.Sleep** függvényt hívja meg.

![Profilozó-nyomkövetések](./media/profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Előfeltételek
A következő utasítások minden Windows, Linux és Mac fejlesztői környezetre vonatkoznak:

* Telepítse a [.NET Core SDK 2.1.2 vagy újabb rendszert.](https://dotnet.microsoft.com/download/archives)
* Telepítse a Git-et az Első lépések - A Git telepítése című témakör [utasításait követve.](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)

## <a name="set-up-the-project-locally"></a>A projekt helyi beállítása

1. Nyisson meg egy parancssori ablakot a számítógépen. Az alábbi utasítások minden Windows, Linux és Mac fejlesztői környezetben működnek.

1. Hozzon létre egy ASP.NET Core MVC webalkalmazás:

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

1. Módosítsa a munkakönyvtárat a projekt gyökérmappájára.

1. Adja hozzá a NuGet csomagot a Profiler-nyomkövetések összegyűjtéséhez:

    ```shell
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

1. Az Application Insights engedélyezése Program.cs:

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .UseApplicationInsights() // Add this line of code to Enable Application Insights
            .UseStartup<Startup>();
    ```
    
1. A Profiler engedélyezése Startup.cs:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddServiceProfiler(); // Add this line of code to Enable Profiler
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

1. Adjon hozzá egy kódsort a **HomeController.cs** szakaszban, hogy véletlenszerűen késleltesse néhány másodpercet:

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

1. A módosítások mentése és véglegesítése a helyi tárházban:

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>A Linux webalkalmazás létrehozása a projekt üzemeltetéséhez

1. Hozza létre a webalkalmazás-környezetet a Linuxos App Service használatával:

    ![A Linux webalkalmazás létrehozása](./media/profiler-aspnetcore-linux/create-linux-appservice.png)

2. Hozza létre a központi telepítési hitelesítő adatokat:

    > [!NOTE]
    > Rögzítse jelszavát, amelyet később használni szeretne a webalkalmazás telepítésekor.

    ![A központi telepítési hitelesítő adatok létrehozása](./media/profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Adja meg a telepítési beállításokat. Hozzon létre egy helyi Git-tárházat a webalkalmazásban az Azure Portalon található utasításokat követve. A Git-tárház automatikusan létrejön.

    ![A Git-tárház beállítása](./media/profiler-aspnetcore-linux/setup-git-repo.png)

További telepítési lehetőségeket [ebben a cikkben](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type)talál.

## <a name="deploy-your-project"></a>A projekt üzembe helyezése

1. A Parancssor ablakban keresse meg a projekt gyökérmappáját. Git-távoli tárház hozzáadása az App Service tárházára való rámutatáshoz:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Használja a telepítési hitelesítő adatok létrehozásához használt **felhasználónevet.**
    * Használja a webalkalmazás linuxos App Service használatával történő létrehozásához használt **alkalmazásnevet.**

2. A projekt üzembe helyezése az Azure-ba történő módosítások lenyomásával:

    ```
    git push azure master
    ```

A következő példához hasonló kimenetnek kell lennie:

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

## <a name="add-application-insights-to-monitor-your-web-apps"></a>Az Application Insights hozzáadása a webalkalmazások figyeléséhez

1. [Hozzon létre egy Application Insights-erőforrást.](./../../azure-monitor/app/create-new-resource.md )

2. Másolja az Application Insights-erőforrás **iKey-értékét,** és állítsa be a következő beállításokat a webalkalmazásokban:

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ```

    Az alkalmazás beállítások módosításakor a webhely automatikusan újraindul. Az új beállítások alkalmazása után a Profiler azonnal két percig fut. A Profiler ezután óránként két percig fut.

3. Generáljon némi forgalmat a webhelyére. Generálhat forgalmat a webhely néhány alkalommal **i.**

4. Várjon 2–5 percet, amíg az események összesítettaz Application Insights.

5. Tallózással keresse meg az Application Insights **teljesítménypanelje** az Azure Portalon. A Profiler-nyomkövetéseket az ablaktábla jobb alsó sarkában tekintheti meg.

    ![Profilozó-nyomkövetések megtekintése](./media/profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>Ismert problémák

### <a name="profile-now-button-doesnt-work-for-linux-profiler"></a>A Profil most gomb nem működik a Linux Profiler esetében
Az App Insights profilozó Linux-verziója még nem támogatja az igény szerinti profilkészítést a profil gomb használatával.


## <a name="next-steps"></a>További lépések
Ha az Azure App Service által üzemeltetett egyéni tárolókat használ, kövesse a Service Profiler engedélyezése egy [tárolóba rendezett ASP.NET Core alkalmazás](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) utasításokat az Application Insights Profiler engedélyezéséhez.

Jelentse na az Application Insights GitHub-tárházának a következőHöz: [ApplicationInsights-Profiler-AspNetCore: Problémák](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
