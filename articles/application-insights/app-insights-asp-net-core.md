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
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: 459fbbe734577af474fae8de3acf9be732780d6c
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736534"
---
# <a name="application-insights-for-aspnet-core"></a>ASP.Net Core-hoz készült Application Insights

Azure Application Insights biztosít a webalkalmazás a kód szintre részletes megfigyelését. Könnyedén figyelheti a webalkalmazás rendelkezésre állását, teljesítményét és használatát. Emellett egyszerűen azonosíthatja és diagnosztizálhatja az alkalmazás hibáit anélkül, hogy meg kellene várnia, amíg egy felhasználó jelenti azokat.

Ez a cikk végigvezeti az ASP.NET Core minta létrehozása [Razor lapok](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) alkalmazás Visual Studio, és hogy milyen figyelése az Azure Application insights szolgáltatással.

## <a name="prerequisites"></a>Előfeltételek

- NET Core 2.0.0 SDK vagy újabb.
- [A Visual Studio 2017](https://www.visualstudio.com/downloads/) 15.7.3 verzió vagy újabb, az ASP.NET és a webes fejlesztési áttelepítendő feladatokhoz. 

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Az ASP.NET Core projekt létrehozása a Visual Studióban

1. Kattintson a jobb gombbal, majd indítsa el **Visual Studio 2017** rendszergazdaként.
2. Válassza ki **fájl** > **új** > **projekt** (Ctrl-Shift-N).

   ![Képernyőfelvétel a Visual Studio új projekt Fájlmenüt](./media/app-insights-asp-net-core/001-new-project.png)

3. Bontsa ki a **Visual C#** > Válasszon **.NET Core** > **ASP.NET Core webalkalmazás**. Adjon meg egy **neve** > **megoldásnév** > ellenőrzése **hozzon létre új Git-tárház**.

   ![Képernyőfelvétel a Visual Studio új projekt varázsló](./media/app-insights-asp-net-core/002-asp-net-core-web-application.png)

4. Válassza ki **.Net Core** > **ASP.NET Core 2.0** **webes alkalmazás** > **OK**.

    ![Képernyőfelvétel a Visual Studio új projekt kijelölés Fájlmenüt](./media/app-insights-asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Application Insights-keresés

Alapértelmezés szerint a Visual Studio 2015 Update 2-es vagy nagyobb az ASP.NET alapvető 2 + alapú projekt meg verzió kihasználhatják a [Application Insights keresési](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-visual-studio) még mielőtt meg explicit módon az Application Insights hozzáadása a projekthez.

Ez a funkció ügyfélfunkciókkal tesztelheti:

1. Az alkalmazás futtatása az IIS Express kattintva ![Képernyőfelvétel a Visual Studio IIS Express ikon](./media/app-insights-asp-net-core/004-iis-express.png)

2. Válassza ki **nézet** > **más Windows** > **Application Insights keresési**.

   ![Képernyőfelvétel a Visual Studio diagnosztikai eszközök](./media/app-insights-asp-net-core/005-view-other-windows-search.png)

3. A hibakeresési munkamenetben telemetriai érhető el jelenleg csak a helyi elemzés céljából. Az Application Insights teljesen engedélyezéséhez jelölje be **Readiness Telemetria** felső a jobb gombbal, vagy hajtsa végre a következő szakaszban.

   ![Képernyőfelvétel a Visual Studio Application Insights keresése](./media/app-insights-asp-net-core/006-search.png)

> [!NOTE]
> További információt hogyan Visual Studio fény beállításait, például [Application Insights keresési](app-insights-visual-studio.md) és [CodeLens](app-insights-visual-studio-codelens.md) helyileg előtt hozzáadta az Application Insights az ASP.NET Core projektjéhez tekintse meg a MAGYARÁZAT, a [Ez a cikk végére.](#Application-Insights-search-continued)

## <a name="add-application-insights-telemetry"></a>Application Insights Telemetria hozzáadása

1. Válassza ki **projekt** > **Application Insights Telemetria hozzáadása...** . (Vagy kattintson a jobb egérgombbal **kapcsolódó szolgáltatások** válassza ki a hozzá kapcsolódó szolgáltatás.)

    ![Képernyőfelvétel a Visual Studio új projekt kijelölés Fájlmenüt](./media/app-insights-asp-net-core/007-project-add-telemetry.png)

2. Válassza ki **Ismerkedés**. (A Visual Studio verziójától függően a szöveg függően eltérőek lehetnek. Ehelyett van néhány régebbi verziója egy **szabad Start** gombra.)

    ![Képernyőfelvétel a Visual Studio új projekt kijelölés Fájlmenüt](./media/app-insights-asp-net-core/008-get-started.png)

3. Válassza ki a megfelelő **előfizetés** > **erőforrás** > **regisztrálása**.

## <a name="changes-made-to-your-project"></a>A projekt Made módosításai

Az Application Insights alacsony többletterhelést. Az Application Insights telemetria hozzáadásával a projekt módosítások ellenőrzése:

Válassza ki **nézet** > **Explorer csapat** (Ctrl +\, Ctrl + M) > **projekt** > **módosítások**

- Teljes négy változásai:

  ![Képernyőkép a fájlok módosította az Application Insights hozzáadása](./media/app-insights-asp-net-core/009-changes.png)

- Egy új fájl jön létre:

   _ConnectedService.json_

```json
{
  "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
  "Version": "8.12.10405.1",
  "GettingStartedDocument": {
    "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
  }
}
```

- Három fájl módosítanak: (további megjegyzéseket kiemelés hozzá)

  _appsettings.json_

```json
{
  "Logging": {
    "IncludeScopes": false,
    "LogLevel": {
      "Default": "Warning"
    }
  },
// Changes to file post adding Application Insights Telemetry:
  "ApplicationInsights": {
    "InstrumentationKey": "10101010-1010-1010-1010-101010101010"
  }
}
//
```

  _ContosoDotNetCore.csproj_

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
  <PropertyGroup>
    <TargetFramework>netcoreapp2.0</TargetFramework>
 <!--Changes to file post adding Application Insights Telemetry:-->
    <ApplicationInsightsResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsResourceId>
    <ApplicationInsightsAnnotationResourceId>/subscriptions/2546c5a9-fa20-4de1-9f4a-62818b14b8aa/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/DotNetCore</ApplicationInsightsAnnotationResourceId>
<!---->
  </PropertyGroup>
  <ItemGroup>
 <!--Changes to file post adding Application Insights Telemetry:-->
    <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.1.1" />
<!---->
    <PackageReference Include="Microsoft.AspNetCore.All" Version="2.0.8" />
  </ItemGroup>
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.VisualStudio.Web.CodeGeneration.Tools" Version="2.0.4" />
  </ItemGroup>
<!--Changes to file post adding Application Insights Telemetry:-->
  <ItemGroup>
    <WCFMetadata Include="Connected Services" />
  </ItemGroup>
<!---->
</Project>
```

   _Program.cs_

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace DotNetCore
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
// Change to file post adding Application Insights Telemetry:
                .UseApplicationInsights()
//
                .UseStartup<Startup>()
                .Build();
    }
}
```

## <a name="synthetic-transactions-with-powershell"></a>Szintetikus tranzakciók a PowerShell használatával

Az alkalmazás a szintetikus tranzakciók ellenében automatizálásához.

1. Az alkalmazás futtatása az IIS Express kattintva ![Képernyőfelvétel a Visual Studio IIS Express ikon](./media/app-insights-asp-net-core/004-iis-express.png)

2. Másolja az URL-címet a webböngésző cím sorából. A formátumú http://localhost:{random portszámát}

   ![Képernyőkép a böngészőcímsorba URL-címe](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Futtassa a következő PowerShell-hurok 100 szintetikus tranzakciók szemben a teszt alkalmazás létrehozásához. Módosítsa a portszámot után **localhost:** megfelelően az előző lépésben másolt URL-címét.

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Nyissa meg az Application Insights portálon találja meg

A fenti szakaszban leírt PowerShell futtatása után nyissa meg az Application Insights tranzakciók megtekintése, és győződjön meg arról, hogy gyűjtenek adatokat. 

A Visual Studio menüjében válassza **projekt** > **Application Insights** > **nyitott Application Insights portál**

   ![Képernyőkép a Application Insights – áttekintés](./media/app-insights-asp-net-core/010-portal.png)

> [!NOTE]
> A példa a fenti képernyőfelvételen látható **élő adatfolyam**, **lapmegtekintés betöltési ideje**, és **tartozó sikertelen kérelmek** jelenleg nincsenek összegyűjtött. A következő szakaszban haladhat végig a minden egyes. Ha már gyűjtött **élő adatfolyam**, és **lapmegtekintés betöltési ideje**, csak kövesse a lépéseket **tartozó sikertelen kérelmek**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Sikertelen kérelmek, élő adatfolyam & lapmegtekintés betöltési idő gyűjtése

### <a name="failed-requests"></a>Sikertelen kérelmek

Technikailag **tartozó sikertelen kérelmek** vannak gyűjtenek, de még nincs történt. Egy egyéni kivétel mentén gyorsabban is hozzá kell adni a létező projekt egy valós kivétel szimulálásához. Ha az alkalmazás továbbra is fut a Visual Studio a folytatás előtt **állítsa le a hibakeresést** (Shift + F5)

1. A **Megoldáskezelőben** > bontsa ki a **lapok** > **About.cshtml** > Nyissa meg a **About.cshtml.cs**.

   ![Képernyőfelvétel a Visual Studio Solution Explorerben](./media/app-insights-asp-net-core/011-about.png)

2. A kivétel hozzáadása ``Message=`` és menteni a módosítást a fájlhoz.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc.RazorPages;

    namespace DotNetCore.Pages
    {
        public class AboutModel : PageModel
        {
            public string Message { get; set; }

            public void OnGet()
            {
                Message = "Your application description page.";
                throw new Exception("Test Exception");
            }
        }
    }
    ```

### <a name="live-stream"></a>Élő stream

Az ASP.NET Core frissítése az Application Insights élő adatfolyam funkciók eléréséhez a **Microsoft.ApplicationInsights.AspNetCore 2.2.0** NuGet-csomagok.

Válassza ki a Visual Studio eszközből **projekt** > **NuGet-csomagok kezelése** > **Microsoft.ApplicationInsights.AspNetCore** > verziója **2.2.0** > **frissítés**.

  ![Képernyőfelvétel a NuGet-Csomagkezelő](./media/app-insights-asp-net-core/012-nuget-update.png)

Több megerősítést kér történik. Olvassa el és fogadja el, ha elfogadja a változtatásokat.

### <a name="page-view-load-time"></a>Lapmegtekintés betöltési ideje

1. A Visual Studio programban navigáljon **Megoldáskezelőben** > **lapok** > két fájlt kell módosítani: _Layout.cshtml_, és  _ViewImports.cshtml_

2. _ A_ViewImports.cshtml_, adja hozzá:

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. A **_Layout.cshtml** adja hozzá az alábbi előtt a ``</head>`` címke más parancsfájlokat is előtt.

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Teszt sikertelen volt a kérelmeket, a lap betöltési ideje, az élő adatfolyam

Tesztelje, és győződjön meg arról, hogy minden működik:

1. Az alkalmazás futtatása az IIS Express kattintva ![Képernyőfelvétel a Visual Studio IIS Express ikon](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Keresse meg a **kapcsolatos** lap elindítani a teszt kivétel. (Ha hibakeresési módban futtatja, akkor kattintson **Folytatás** a kivétel a Visual Studio Application Insights megjelennek.)

3. Futtassa újra a szimulált PowerShell tranzakció parancsfájl korábbi (esetleg módosítsa a port számát, a parancsfájl.)

4. Ha nincs megnyitva, a Visual Studio menüből válassza a alkalmazások Insights – áttekintés **projekt** > **Application Insights** > **alkalmazás megnyitása Insights portálon találja meg**. 

   > [!TIP]
   > Ha még nem látja az új forgalom, ellenőrizze a **időtartománynak** kattintson **frissítése**.

   ![Képernyőfelvétel a áttekintése ablakban](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Válassza ki az élő adatfolyam

   ![Képernyőkép a metrikák élő adatfolyam](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Ha a PowerShell parancsfájl még mindig fut, akkor kell megjelennie élő metrika, ha a program nem futtatható a parancsprogram újra élő adatfolyam nyissa meg.)

## <a name="app-insights-sdk-comparison"></a>App Insights SDK összehasonlítása

Az Application Insights termékcsoport merevlemez dolgozott a szolgáltatás paritása eléréséhez a [.NET-keretrendszer SDK teljes](https://github.com/Microsoft/ApplicationInsights-dotnet) és a .net Core SDK. A 2.2.0 kiadása a [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) az Application Insights szolgáltatás résnek nagymértékben be van zárva.

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

## <a name="application-insights-search-continued"></a>Application Insights keresési továbbra is

Jobb Application Insights keresési működésének megismerése a Visual Studio egy ASP.NET Core 2 projekt akkor is, ha egy explicit az Application Insights NuGet-csomagok telepítése még nem történt meg. A hibakeresési kimeneti vizsgálata hasznos lehet.

Ha a kimenet a Word _insight_ azt ki vannak emelve az alábbihoz hasonló eredményeket:

```DebugOuput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

A következő két szerelvény betöltése CoreCLR: 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

És a _megszüntetni_ az Application Insights-példányokhoz telemetriai azt jelzi, hogy az alkalmazás társítva van egy ikey nincs-e, az adatok az alkalmazás futtatása közben létrehozott nem küldött az Azure-ba, és csak helyi keresési és elemzés számára érhető el.

Hogyan ezt akkor lehet része, hogy a NuGet-csomag _Microsoft.AspNetCore.All_ a függőség beállításához szükséges [ _Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1)

![Képernyőfelvétel a NuGet-függőség Microsoft.AspNETCore.all a diagramhoz](./media/app-insights-asp-net-core/013-dependency.png)

Visual Studio kívül ha ASP.NET Core a projekt VSCode vagy valamilyen más szerkesztő szerkesztése a szerelvények nem tudnák automatikusan betöltése során hibakeresési Ha explicit módon még nem vett fel a projektbe Application Insights.

Azonban a Visual Studio a megvilágítási be a helyi Application Insights szolgáltatásainak külső szerelvényből van használatával valósítható meg használatát a [IHostingStartup felület](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1) amely dinamikusan hozzáadja az Application Insights során hibakeresési.

További információt az alkalmazás továbbfejlesztésének egy [az ASP.NET Core IHostingStartup a külső szerelvény](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="how-to-disable-application-insights-in-visual-studio-net-core-projects"></a>A Visual Studio .NET Core-projektek az Application Insights letiltása

Az Application Insights keresési funkcióját be az automatikus világos egyes hasznosak lehetnek, megnéz hibakeresési telemetriai jönnek létre, ha már nem vár, hogy zavaró lehet.

Ha csak a telemetriai adatok generálása letiltása elegendő adhat hozzá a kódblokk konfigurálása módszer a _Startup.cs_ fájlt:

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

Továbbra is betölti a CoreCLR _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ és _Microsoft.ApplicationInsights.AspNetCore.dll_, de azok nem semmit.

Ha szeretné teljesen letiltani az Application Insights a Visual Studio .NET Core projektben, az elsődleges módszer kiválasztásához **eszközök** > **beállítások**  >   **Projektek és megoldások** > **webes projektek** >, és jelölje be a jelölőnégyzetet, az ASP.NET Core webes projektek helyi Application Insights letiltása. Ez a funkció hozzá lett adva, a Visual Studio 15,6.

![Képernyőfelvétel a Visual Studio beállítások ablak webes projektek képernyő](./media/app-insights-asp-net-core/014-disable.png)

Ha a Visual Studio egy korábbi verzióját futtatja, és akár IHostingStartup keresztül betöltött összes szerelvénynek teljesen eltávolítja:

`.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")`

a _Program.cs_:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

namespace DotNetCore
{
    public class Program
    {
        public static void Main(string[] args)
        {
            BuildWebHost(args).Run();
        }

        public static IWebHost BuildWebHost(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                .UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")
                .UseStartup<Startup>()
                .Build();
    }
}
```

Vagy adja hozzá azt is megteheti ``"ASPNETCORE_preventHostingStartup": "True"`` való _launchSettings.json_ környezeti változókat.

A probléma az alábbi módszerek valamelyikével, nem csak tiltsa le a letiltja semmit, amely, ami a IHostingStartup világos funkciót be lett a Visual Studio Application insights szolgáltatással.

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>További lépések
* [Megismerkedhet a felhasználók Forgalomáramlás](app-insights-usage-flows.md) tudni, hogy a felhasználók hogyan navigálnak az alkalmazás.
* [Konfigurálja a pillanatkép-gyűjtemény](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) forráskódja és változók állapotának megtekintéséhez el a rendszer kivételt hoz létre.
* [Az API-val](app-insights-api-custom-events-metrics.md) a saját eseményeket és metrikákat az alkalmazás teljesítményét és használatának részletes nézet küldését.
* [Rendelkezésreállás figyelésére szolgáló tesztek](app-insights-monitor-web-app-availability.md) ellenőrizze a világszerte folyamatosan az alkalmazást.
