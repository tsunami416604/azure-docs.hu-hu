---
title: Az ASP.NET Core Azure Linux webalkalmazások Application Insights Profilkészítő profil |} Microsoft Docs
description: Fogalmi áttekintés és részletes oktatóanyagainkat Application Insights Profiler használatával.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: mbullwin
ms.openlocfilehash: 5596c4efeba14e9d2bfdadd7ce92bb6b2c9fcbf0
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Application Insights Profilkészítő profil ASP.NET Core Azure Linux webalkalmazások

Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

Annak megállapítása, mennyi időt töltött az egyik módszer az élő webes alkalmazás használatakor [Application Insights](app-insights-overview.md). Application Insights Profilkészítő már az ASP.NET Core webes alkalmazásokhoz, amelyek Linux Azure App Service érhető el. Ez az útmutató részletes útmutatás a szolgáltatásprofil-elemzői adat hogyan gyűjthetők össze az ASP.NET Core Linux web Apps.

Ez a forgatókönyv befejezése után az alkalmazás össze tudják gyűjteni a szolgáltatásprofil-elemzői adat például a nyomkövetési adatokat, az ábrán látható. Ebben a példában a Profilkészítő nyomkövetési azt jelzi, hogy egy adott webes kérelem lassú várakozási idő miatt. A *gyakran használt adatok elérési útja* van lelassulnak, ami az alkalmazás kódban van megjelölve lángmegelőzési ikon jelzi. A **kapcsolatos** metódust a **HomeController** szakaszban van lelassulnak, ami a webalkalmazás, mert a metódust hívja a **Thread.Sleep** függvény.

![Szolgáltatásprofil-elemzői adat](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Előfeltételek
Az alábbi utasítások alapján alkalmazható azonban minden Windows, Linux és Mac-fejlesztési környezetben:

* Telepítse a [.NET Core SDK 2.1.2 vagy újabb](https://www.microsoft.com/net/download/windows/build).
* A következő utasításokat követve telepítse a Git [első lépések – telepíti a Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>Állítsa be a projekt helyi küldése

1. Nyisson meg egy parancssori ablakot, a számítógépre. Az alábbi utasítások működnek az összes Windows, Linux és Mac-fejlesztési környezetet.

2. Az ASP.NET Core MVC webalkalmazás létrehozásához:

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

3. A munkakönyvtár megváltoztatása a projekt gyökérmappájába.

4. Adja hozzá a NuGet-csomagot kell gyűjteni a szolgáltatásprofil-elemzői adat:

    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

5. Adja hozzá a kódsort a **HomeController.cs** szakasz véletlenszerű késleltetés néhány másodpercen belül:

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

6. Mentés és a módosítások végrehajtásához a helyi tárházba:

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>A projekt üzemeltetésére Linux-webalkalmazás létrehozása

1. A webalkalmazás környezete létrehozása Linux App Service segítségével:

    ![A Linux-webalkalmazás létrehozása](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. Az üzembe helyezési hitelesítő adatok létrehozása:

    > [!NOTE]
    > Jegyezze fel a jelszót később a webes alkalmazás központi telepítésekor.

    ![Az üzembe helyezési hitelesítő adatok létrehozása](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Válassza ki a központi telepítés beállításait. A web app alkalmazásban egy helyi Git-tárház beállítása az Azure portálon útmutatásai alapján. Automatikusan létrejön egy Git-tárházat.

    ![A Git-tárház beállítása](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

Több központi telepítési beállítások, lásd: [Ez a cikk](https://docs.microsoft.com/azure/app-service/containers/choose-deployment-type).

## <a name="deploy-your-project"></a>A projekt telepítése

1. A parancssori ablakot keresse meg a projekt gyökérmappájába. Egy távoli Git-tárházat, mutasson az App Service a tárház hozzáadása:

    ```
    git remote add azure https://<username>@<app_name>.scm.azurewebsites.net:443/<app_name>.git
    ```

    * Használja a **felhasználónév** , hogy az üzembe helyezési hitelesítő adatok létrehozásához használt.
    * Használja a **alkalmazásnév** , hogy használja-e a webalkalmazás létrehozása Linux App Service segítségével.

2. Telepítse a projektet a módosításokat az Azure-ba való küldésével:

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

## <a name="add-application-insights-to-monitor-your-web-apps"></a>A webalkalmazások figyeléséhez az Application Insights hozzáadása

1. [Az Application Insights-erőforrás létrehozása](./app-insights-create-new-resource.md).

2. Másolás a **iKey** az Application Insights-erőforrás értékének és a webalkalmazások a következő beállításokat:

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![Az Alkalmazásbeállítások konfigurálása](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    Ha módosítja az alkalmazás-beállításokat, a hely automatikusan újraindul. Miután az új beállításokat alkalmazzák, a Profilkészítő azonnal két percig fut. A Profilkészítő majd két percig óránként fut.

3. A webhely néhány adatforgalmat generálnak. A hely frissítésével is létrehozhat a forgalom **kapcsolatos** lap néhány alkalommal.

4. Várjon 2-5 percet, hogy az események összesített az Application Insights részére.

5. Keresse meg az Application Insights **teljesítmény** ablaktáblán az Azure portálon. A szolgáltatásprofil-elemzői adat, ha a panel alsó sarokban tekintheti meg.

    ![Megtekintése a szolgáltatásprofil-elemzői adat](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>Ismert problémák

### <a name="the-enable-action-in-the-profiler-configuration-pane-doesnt-work"></a>A Profilkészítő konfigurációs ablaktáblán az engedélyezés műveletre nem működik

> [!NOTE]
> Ha az alkalmazás az App Service segítségével Linux működteti, nem kell a Profilkészítő engedélyezze újra a **teljesítmény** ablaktábláján az Application Insights portáljáról. A NuGet-csomagot a projekt tartalmazza, és állítsa be az Application Insights **iKey** a webalkalmazás-beállítások engedélyezése a Profilkészítő értéket.

Ha követi a engedélyezése munkafolyamata [Application Insights Profilkészítő for Windows](./app-insights-profiler.md) válassza ki **engedélyezése** a a **konfigurálása Profilkészítő** ablaktáblán, hibaüzenetet kap. Az engedélyezés műveletre próbálja telepíteni a Windows-verzió a Profilkészítő ügynök a Linux-környezetben.

Dolgozunk a probléma megoldása.

![Ne engedélyezze újra a Profilkészítőt a teljesítmény panelen](./media/app-insights-profiler-aspnetcore-linux/issue-enable-profiler.png)


## <a name="next-steps"></a>További lépések
Ha Azure App Service által üzemeltetett egyéni tárolók használatához kövesse az utasításokat a [ szolgáltatásprofil-elemzőben engedélyezze az ASP.NET Core indexelése alkalmazások](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) Application Insights Profilkészítő engedélyezéséhez.

A problémák vagy az Application Insights GitHub-tárházban javasol: [ApplicationInsights-Profiler-AspNetCore: problémák](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
