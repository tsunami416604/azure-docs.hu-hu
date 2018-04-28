---
title: Az Azure Application Insights for ASP.NET Core |} Microsoft Docs
description: Webalkalmazások rendelkezésre állását, teljesítményét és használatának a figyelheti.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/21/2018
ms.author: mbullwin
ms.openlocfilehash: 2245fcdaa8b7e85ea37e9af9c939cd188c4d7ed9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="application-insights-for-aspnet-core"></a>ASP.Net Core-hoz készült Application Insights

Azure Application Insights biztosít a webalkalmazás a kód szintre részletes megfigyelését. Könnyedén figyelheti a webalkalmazás rendelkezésre állását, teljesítményét és használatát. Emellett egyszerűen azonosíthatja és diagnosztizálhatja az alkalmazás hibáit anélkül, hogy meg kellene várnia, amíg egy felhasználó jelenti azokat.

Ez a cikk végigvezeti az ASP.NET Core minta létrehozása [Razor lapok](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) alkalmazás Visual Studio, és hogy milyen figyelése az Azure Application insights szolgáltatással.

## <a name="prerequisites"></a>Előfeltételek

- NET Core 2.0.0 SDK vagy újabb.
- [A Visual Studio 2017](https://www.visualstudio.com/downloads/) 15.3 vagy az ASP.NET és a webes fejlesztési áttelepítendő feladatokhoz újabb verziója.

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Az ASP.NET Core projekt létrehozása a Visual Studióban

1. Kattintson a jobb gombbal, majd indítsa el **Visual Studio 2017** rendszergazdaként.
2. Válassza ki **fájl** > **új** > **projekt** (Ctrl-Shift-N).

   ![Képernyőfelvétel a Visual Studio új projekt Fájlmenüt](./media/app-insights-asp-net-core/0001-file-new-project.png)

3. Bontsa ki a **Visual C#** > Válasszon **.NET Core** > **ASP.NET Core webalkalmazás**. Adjon meg egy **neve** > **megoldásnév** > ellenőrzése **hozzon létre új Git-tárház**.

   ![Képernyőfelvétel a Visual Studio új projekt varázsló](./media/app-insights-asp-net-core/0002-new-project-web-application.png)

4. Válassza ki **.Net Core** > **ASP.NET Core 2.0** **webes alkalmazás** > **OK**.

    ![Képernyőfelvétel a Visual Studio új projekt kijelölés Fájlmenüt](./media/app-insights-asp-net-core/0003-dot-net-core.png)

## <a name="add-application-insights-telemetry"></a>Application Insights Telemetria hozzáadása

1. Válassza ki **projekt** > **Application Insights Telemetria hozzáadása...** (Másik lehetőségként kattintson a jobb egérgombbal **kapcsolódó szolgáltatások** > kapcsolódó szolgáltatási adja hozzá.)

    ![Képernyőfelvétel a Visual Studio új projekt kijelölés Fájlmenüt](./media/app-insights-asp-net-core/0004-add-application-insights-telemetry.png)

2. Válassza a **Start Free** (Ingyenes verzió indítása) lehetőséget.

    ![Képernyőfelvétel a Visual Studio új projekt kijelölés Fájlmenüt](./media/app-insights-asp-net-core/0005-start-free.png)

3. Válassza ki a megfelelő **előfizetés** > **erőforrás** >, és hogy gyűjtemény több mint 1 GB-os havi engedélyezi-e > **regisztrálása**.

    ![Képernyőfelvétel a Visual Studio új projekt kijelölés Fájlmenüt](./media/app-insights-asp-net-core/0006-register.png)

## <a name="changes-made-to-your-project"></a>A projekt Made módosításai

Application Insights jelenleg még a nagyon alacsony többletterhelést okoznak. Az Application Insights telemetria hozzáadásával a projekt módosítások ellenőrzése:

Válassza ki **nézet** > **Explorer csapat** (Ctrl +\, Ctrl + M) > **projekt** > **módosítások**

- Teljes négy változásai:

  ![Képernyőkép a fájlok módosította az Application Insights hozzáadása](./media/app-insights-asp-net-core/0007-changes.png)

- Egy új fájl jön létre:

   **ConnectedService.json**

  ![Képernyőkép a fájlok módosította az Application Insights hozzáadása](./media/app-insights-asp-net-core/0008-connectedservice-json.png)

- Három fájlok módosítja:

  **appsettings.json**

   ![Képernyőkép a fájlok módosította az Application Insights hozzáadása](./media/app-insights-asp-net-core/0009-appsettings-json.png)

  **ContosoDotNetCore.csproj**

   ![Képernyőkép a fájlok módosította az Application Insights hozzáadása](./media/app-insights-asp-net-core/0010-contoso-netcore-csproj.png)

   **Program.cs**

   ![Képernyőkép a fájlok módosította az Application Insights hozzáadása](./media/app-insights-asp-net-core/0011-program-cs.png)

## <a name="synthetic-transactions-with-powershell"></a>Szintetikus tranzakciók a PowerShell használatával

Elindítani az alkalmazást, és kattint körül hivatkozások manuálisan teszt forgalom létrehozásához használható. Azonban érdemes gyakran hozzon létre egy egyszerű szintetikus tranzakció PowerShell.

1. Az alkalmazás futtatása az IIS Express kattintva ![Képernyőfelvétel a Visual Studio IIS Express ikon](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Másolja az URL-címet a webböngésző cím sorából. A formátumú http://localhost:{random portszámát}

   ![Képernyőkép a böngészőcímsorba URL-címe](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Futtassa a következő PowerShell-hurok 100 szintetikus tranzakciók szemben a teszt alkalmazás létrehozásához. Módosítsa a portszámot után **localhost:** megfelelően az előző lépésben másolt URL-címét.

   ```PS
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Nyissa meg az Application Insights portálon találja meg

A fenti szakaszban leírt PowerShell futtatása után nyissa meg az Application Insights tranzakciók megtekintése, és győződjön meg arról, hogy gyűjtenek adatokat. 

A Visual Studio menüjében válassza **projekt** > **Application Insights** > **nyitott Application Insights portál**

   ![Képernyőkép a Application Insights – áttekintés](./media/app-insights-asp-net-core/0014-portal-01.png)

> [!NOTE]
> A példa a fenti képernyőfelvételen látható **élő adatfolyam**, **lapmegtekintés betöltési ideje**, és **tartozó sikertelen kérelmek** jelenleg nincsenek összegyűjtött. A következő szakaszban haladhat végig a minden egyes. Ha már gyűjtött **élő adatfolyam**, és **lapmegtekintés betöltési ideje**, csak kövesse a lépéseket **tartozó sikertelen kérelmek**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Sikertelen kérelmek, élő adatfolyam & lapmegtekintés betöltési idő gyűjtése

### <a name="failed-requests"></a>Sikertelen kérések

Technikailag **tartozó sikertelen kérelmek** vannak gyűjtenek, de még nincs történt. Egy egyéni kivétel mentén gyorsabban is hozzá kell adni a létező projekt egy valós kivétel szimulálásához. Ha az alkalmazás továbbra is fut a Visual Studio a folytatás előtt **állítsa le a hibakeresést** (Shift + F5)

1. A **Megoldáskezelőben** > bontsa ki a **lapok** > **About.cshtml** > Nyissa meg a **About.cshtml.cs**.

   ![Képernyőfelvétel a Visual Studio Solution Explorerben](./media/app-insights-asp-net-core/0015-solution-explorer-about.png)

2. A kivétel hozzáadása ``Message=`` > a fájlhoz a módosítás mentéséhez.

   ```C#
   throw new Exception("Test Exception");
   ```

   ![Képernyőkép a kivétel kódja](./media/app-insights-asp-net-core/000016-exception.png)

### <a name="live-stream"></a>Élő stream

Az ASP.NET Core frissítése az Application Insights élő adatfolyam funkciók eléréséhez a **Microsoft.ApplicationInsights.AspNetCore 2.2.0** NuGet-csomagok.

Válassza ki a Visual Studio eszközből **projekt** > **NuGet-csomagok kezelése** > **Microsoft.ApplicationInsights.AspNetCore** > verziója **2.2.0** > **frissítés**.

  ![Képernyőfelvétel a NuGet-Csomagkezelő](./media/app-insights-asp-net-core/0017-update-nuget.png)

Több megerősítést kér fordulhat elő, olvassa el és fogadja el, ha elfogadja a változtatásokat.

### <a name="page-view-load-time"></a>Lapmegtekintés betöltési ideje

1. A Visual Studio programban navigáljon **Megoldáskezelőben** > **lapok** > két fájlt kell módosítani: **_Layout.cshtml**, és **_ ViewImports.cshtml**

2. A **_ViewImports.cshtml**, adja hozzá:

   ```C#
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```
     ![Képernyőkép a _ViewImports.cshtml kód módosítása](./media/app-insights-asp-net-core/00018-view-imports.png)

3. A **_Layout.cshtml** adja hozzá az alábbi előtt a ``</head>`` címke, de más parancsfájlokat előtt.

    ```C#
    @Html.Raw(snippet.FullScript)
    ```
    ![Képernyőkép a layout.cshtml kód módosítása](./media/app-insights-asp-net-core/0018-layout-cshtml.png)

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Teszt sikertelen volt a kérelmeket, a lap betöltési ideje, az élő adatfolyam

Most, hogy befejezte az előző lépéseket ügyfélfunkciókkal tesztelheti, és győződjön meg arról, hogy minden működik.

1. Az alkalmazás futtatása az IIS Express kattintva ![Képernyőfelvétel a Visual Studio IIS Express ikon](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Keresse meg a **kapcsolatos** lap elindítani a teszt kivétel. (Ha hibakeresési módban futtatja, akkor kattintson **Folytatás** a Visual Studióban, mielőtt a kivétel fog észlelnie kell az Application Insights.)

3. Futtassa újra a szimulált PowerShell tranzakció parancsfájl korábbi (esetleg módosítsa a port számát, a parancsfájl.)

4. Ha nincs megnyitva, a Visual Studio menüből válassza a alkalmazások Insights – áttekintés **projekt** > **Application Insights** > **alkalmazás megnyitása Insights portálon találja meg**. 

   > [!TIP]
   > Ha még nem látja az új forgalom, ellenőrizze a **időtartománynak** kattintson **frissítése**.

   ![Képernyőfelvétel a áttekintése ablakban](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Válassza ki az élő adatfolyam

   ![Képernyőkép a metrikák élő adatfolyam](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Ha a PowerShell parancsfájl még mindig fut, akkor kell megjelennie élő metrika, ha a program nem futtatható a parancsprogram újra élő adatfolyam nyissa meg.)

## <a name="app-insights-sdk-comparison"></a>App Insights SDK összehasonlítása

Az Application Insights termékcsoport merevlemez dolgozott a eléréséhez szolgáltatásparitást között a lehető legközelebb a [.NET-keretrendszer SDK teljes](https://github.com/Microsoft/ApplicationInsights-dotnet) és a .net Core SDK. A 2.2.0 kiadása a [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) az Application Insights szolgáltatás résnek nagymértékben be van zárva.

Bővebb információt a különbségek és közötti kompromisszumot [.NET és a .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

   | SDK összehasonlítása | ASP.NET        | Az ASP.NET Core 2.1.0    | Az ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Élő metrikák**      | **+** |**-** | **+** |
   | **Kiszolgáló Telemetriai csatorna** | **+** |**-** | **+**|
   |**Adaptív mintavétel**| **+** | **-** | **+**|
   | **SQL függőségi hívások esetében**     | **+** |**-** | **+**|
   | **Teljesítményszámlálók*** | **+** | **-**| **-**|

_Teljesítményszámlálók_ ebben a környezetben hivatkozik [kiszolgálóoldali teljesítményszámlálók](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) , például a processzor, memória és a lemezkihasználtság lekéréséhez.

## <a name="open-source-sdk"></a>Open-source SDK
[Olvassa el, és hozzájárulnak a kódot](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>További lépések
* [Megismerkedhet a felhasználók Forgalomáramlás](app-insights-usage-flows.md) tudni, hogy a felhasználók hogyan navigálnak az alkalmazás.
* [Konfigurálja a pillanatkép-gyűjtemény](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) forráskódja és változók állapotának megtekintéséhez el a rendszer kivételt hoz létre.
* [Az API-val](app-insights-api-custom-events-metrics.md) a saját eseményeket és metrikákat az alkalmazás teljesítményét és használatának részletes nézet küldését.
* [Rendelkezésreállás figyelésére szolgáló tesztek](app-insights-monitor-web-app-availability.md) ellenőrizze a világszerte folyamatosan az alkalmazást.
