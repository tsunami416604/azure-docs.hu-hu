---
title: Profiljának ASP.NET Core Azure Linux-webalkalmazások Application Insights Profiler |} A Microsoft Docs
description: Fogalmi áttekintése és Application Insights Profiler használatának részletes oktatóanyaga.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: mbullwin
ms.openlocfilehash: 6fcc11b0120c9d19cfc1482100ac68d04c9d625d
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645275"
---
# <a name="profile-aspnet-core-azure-linux-web-apps-with-application-insights-profiler"></a>Az ASP.NET Core Azure Linux-webalkalmazások Application Insights Profiler-profil

Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.

Ismerje meg, hogy mennyi időt vesz igénybe az egyes módszerek az élő webalkalmazásának használatakor [Application Insights](app-insights-overview.md). Application Insights Profiler már elérhető az Azure App Service Linux rendszeren futó ASP.NET Core web Apps. Ez az útmutató részletes útmutatás a Profiler nyomkövetések hogyan gyűjthetők össze az ASP.NET Core Linuxos webalkalmazásokat.

Ez az útmutató befejeztével az alkalmazás például a képen látható a nyomkövetés Profiler-nyomkövetések képes összegyűjteni. Ebben a példában a Profiler nyomkövetést azt jelzi, hogy egy bizonyos webes kérés lassú várakozással töltött idő miatt. A *gyakori elérésű útvonal* egy személy ikon jelöli, amelyet a rendszer lassítása az alkalmazást. A **kapcsolatos** metódus az a **HomeController** szakaszban van a webalkalmazás lassítása, mert a metódust hívja a **Thread.Sleep** függvény.

![Profiler hívásláncai](./media/app-insights-profiler-aspnetcore-linux/profiler-traces.png)

## <a name="prerequisites"></a>Előfeltételek
Összes Windows, Linux és Mac fejlesztési környezetre vonatkozik az alábbi utasításokat:

* Telepítse a [.NET Core SDK 2.1.2 vagy újabb](https://www.microsoft.com/net/download/windows/build).
* A Git telepítése a webhelyen található utasításokat követve [első lépések – Git telepítése](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

## <a name="set-up-the-project-locally"></a>A projekt helyi beállítása

1. Nyissa meg a parancssor ablakát a gépen. Az alábbi utasításokat az összes Windows, Linux és Mac fejlesztési környezetek esetében működik.

2. Az ASP.NET Core MVC webalkalmazás létrehozásához:

    ```
    dotnet new mvc -n LinuxProfilerTest
    ```

3. A munkakönyvtárban módosítsa a projekt gyökérkönyvtárába.

4. Adja hozzá a NuGet-csomagot a Profiler-nyomkövetések gyűjtése:

    ```
    dotnet add package Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

5. Adjon hozzá egy sort a kód a **HomeController.cs** véletlenszerűen késleltetheti néhány másodpercig szakaszban:

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

6. Mentse és véglegesítse a módosításokat a helyi tárházban:

    ```
        git init
        git add .
        git commit -m "first commit"
    ```

## <a name="create-the-linux-web-app-to-host-your-project"></a>A Linux webalkalmazás üzemeltetéséhez a projekt létrehozása

1. A web app-környezet létrehozása a linuxon futó App Service használatával:

    ![A Linux-webalkalmazás létrehozása](./media/app-insights-profiler-aspnetcore-linux/create-linux-appservice.png)

2. Az üzembe helyezési hitelesítő adatok létrehozása:

    > [!NOTE]
    > Jegyezze fel a jelszót a webes alkalmazás üzembe helyezésekor későbbi használat céljából.

    ![Az üzembe helyezési hitelesítő adatok létrehozása](./media/app-insights-profiler-aspnetcore-linux/create-deployment-credentials.png)

3. Adja meg a központi telepítési beállításokat. Állítsa be a webalkalmazás helyi Git-tárházat az utasításokat követve az Azure Portalon. Git-tárház automatikusan létrejön.

    ![A Git-tárház beállítása](./media/app-insights-profiler-aspnetcore-linux/setup-git-repo.png)

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

1. [Hozzon létre egy Application Insights-erőforrást](./app-insights-create-new-resource.md).

2. Másolás a **Rendszerállapotkulcsot** Application Insights-erőforrás értékének, és a web apps állítson be a következő beállításokat:

    ```
    APPINSIGHTS_INSTRUMENTATIONKEY: [YOUR_APPINSIGHTS_KEY]
    ASPNETCORE_HOSTINGSTARTUPASSEMBLIES: Microsoft.ApplicationInsights.Profiler.AspNetCore
    ```

    ![Az Alkalmazásbeállítások konfigurálása](./media/app-insights-profiler-aspnetcore-linux/set-appsettings.png)

    Ha a beállítások módosítják, a hely automatikusan újraindul. Miután az új beállításokat alkalmazzák, a Profiler azonnal két percig fut. A Profiler majd két percig óránként fut.

3. Hozzon létre a webhely forgalom egy része. A hely frissítése forgalmat is létrehozhat **kapcsolatos** oldal néhány alkalommal.

4. Várjon az események 2 – 5 percet, amíg az Application Insightsba összesített.

5. Tallózással keresse meg az Application Insights **teljesítmény** panel az Azure Portalon. A Profiler nyomkövetések aljának jobb oldalán a panelen tekintheti meg.

    ![Profiler-nyomkövetések megtekintéséhez](./media/app-insights-profiler-aspnetcore-linux/view-traces.png)

## <a name="known-issues"></a>Ismert problémák

### <a name="the-enable-action-in-the-profiler-configuration-pane-doesnt-work"></a>Az engedélyezés műveletre a Profiler konfiguráció panelen nem működik

> [!NOTE]
> Ha alkalmazását a Linuxon futó App Service használatával, nem kell újra engedélyezni a Profiler az a **teljesítmény** ablaktáblán az Application Insights portálon. Például a NuGet-csomagot a projektben, és állítsa be az Application Insights **Rendszerállapotkulcsot** értéket a webalkalmazás-beállítások a Profiler engedélyezéséhez.

Ha követi a munkafolyamat engedélyezése [Application Insights Profiler az Windows](./app-insights-profiler.md) válassza **engedélyezése** a a **Profiler konfigurálása** ablaktáblán hibaüzenetet kap. Az engedélyezés műveletet próbál meg telepíteni a Profiler-ügynök Windows verziója a Linux-környezetet.

Dolgozunk a probléma megoldása.

![Ne kísérelje meg újra engedélyezni a Profiler a teljesítmény panelen](./media/app-insights-profiler-aspnetcore-linux/issue-enable-profiler.png)


## <a name="next-steps"></a>További lépések
Ha egyéni Azure App Service által üzemeltetett tárolók használja, kövesse a [ Service Profiler engedélyezése a tárolóalapú ASP.NET Core alkalmazás](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/tree/master/examples/EnableServiceProfilerForContainerApp) Application Insights Profiler engedélyezése.

Bármilyen probléma vagy javaslatok az Application Insights GitHub-adattárhoz: [ApplicationInsights – Profiler-AspNetCore: problémák](https://github.com/Microsoft/ApplicationInsights-Profiler-AspNetCore/issues).
