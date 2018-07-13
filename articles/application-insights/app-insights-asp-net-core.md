---
title: Az Azure Application Insights az ASP.NET Core |} A Microsoft Docs
description: Figyeli a webalkalmazásokat a rendelkezésre állását, teljesítményét és használatát.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: mbullwin
ms.openlocfilehash: 6635906b6aae418fa3754f1152fae3e0d8903ffc
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38989765"
---
# <a name="application-insights-for-aspnet-core"></a>ASP.Net Core-hoz készült Application Insights

Az Azure Application Insights a a webalkalmazás a code szintre részletes figyelését teszi lehetővé. Könnyedén figyelheti a webalkalmazása rendelkezésre állását, teljesítményét és használatát. Emellett egyszerűen azonosíthatja és diagnosztizálhatja az alkalmazás hibáit anélkül, hogy meg kellene várnia, amíg egy felhasználó jelenti azokat.

Ez a cikk végigkalauzolja egy mintául szolgáló ASP.NET Core [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) alkalmazáshoz a Visual Studio és az Azure Application Insights figyelési elindítása.

## <a name="prerequisites"></a>Előfeltételek

- NET Core 2.0.0 SDK-t vagy újabb.
- [A Visual Studio 2017](https://www.visualstudio.com/downloads/) 15.7.3 verzió vagy újabb verzió az ASP.NET- és fejlesztési számítási feladatot. 

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Hozzon létre egy ASP.NET Core-projektet a Visual Studióban

1. Kattintson a jobb gombbal, majd indítsa el **Visual Studio 2017** rendszergazdaként.
2. Válassza ki **fájl** > **új** > **projekt** (Ctrl-Shift-N).

   ![Képernyőfelvétel a Visual Studio új projekt Fájlmenüt](./media/app-insights-asp-net-core/001-new-project.png)

3. Bontsa ki a **Visual C#** > Válasszon **.NET Core** > **ASP.NET Core-webalkalmazás**. Adjon meg egy **neve** > **megoldásnevet** > ellenőrizze **hozzon létre új Git-tárház**.

   ![A Visual Studio új projekt varázsló képernyőképe](./media/app-insights-asp-net-core/002-asp-net-core-web-application.png)

4. Válassza ki **.Net Core** > **az ASP.NET Core 2.0** **webes alkalmazás** > **OK**.

    ![Képernyőfelvétel a Visual Studio új projekt kijelölés Fájlmenüt](./media/app-insights-asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Application Insights-keresés

Alapértelmezés szerint a Visual Studio 2015 Update 2-es vagy nagyobb az ASP.NET Core 2 + alapú projektet verzió kihasználhatja [Application Insights-keresés](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio) még mielőtt, explicit módon Application Insights hozzáadása a projekthez.

Ez a funkció teszteléséhez:

1. Az IIS Express kattintva az alkalmazás futtatása ![Képernyőfelvétel a Visual Studio IIS Express ikon](./media/app-insights-asp-net-core/004-iis-express.png)

2. Válassza ki **nézet** > **Other Windows** > **Application Insights-keresés**.

   ![Képernyőfelvétel a Visual Studio-diagnosztikai eszközök](./media/app-insights-asp-net-core/005-view-other-windows-search.png)

3. A hibakeresési munkamenet telemetriájában jelenleg csak helyi elemzés céljából. Az Application Insights teljes körű engedélyezéséhez jelölje be **Telemetriai készültségi** felső jobb gombbal, vagy hajtsa végre a következő szakaszban.

   ![Képernyőfelvétel a Visual Studio Application Insights-keresés](./media/app-insights-asp-net-core/006-search.png)

> [!NOTE]
> További információ a módját, például a Visual Studio lámpa funkciók mentése [Application Insights keresés](app-insights-visual-studio.md) és [Codelensben](app-insights-visual-studio-codelens.md) helyileg előtt hozzáadta az Application Insights az ASP.NET Core-projektet, tekintse meg a Magyarázat: a [a cikk végén.](#Application-Insights-search-continued)

## <a name="add-application-insights-telemetry"></a>Application Insights Telemetria hozzáadása

1. Válassza ki **projekt** > **Application Insights Telemetria hozzáadása...** . (Vagy a jobb gombbal **csatlakoztatott szolgáltatás** , és válassza ki a csatlakoztatott szolgáltatás hozzáadása.)

    ![Képernyőfelvétel a Visual Studio új projekt kijelölés Fájlmenüt](./media/app-insights-asp-net-core/007-project-add-telemetry.png)

2. Válassza ki **Ismerkedés**. (A Visual Studio verziójától függően a szöveg kissé eltérhet. Ehelyett rendelkezik néhány régebbi verzió egy **ingyenes próba megkezdése** gombra.)

    ![Képernyőfelvétel a Visual Studio új projekt kijelölés Fájlmenüt](./media/app-insights-asp-net-core/008-get-started.png)

3. Válassza ki a megfelelő **előfizetés** > **erőforrás** > **regisztrálása**.

## <a name="changes-made-to-your-project"></a>Made módosítja a projekthez

Application Insights jelenleg alacsony többletterhelést okoznak. A módosítások fényében, amelyek a projekt adja hozzá az Application Insights telemetria áttekintése:

Válassza ki **nézet** > **Team Explorer** (Ctrl +\, Ctrl + M) > **projekt** > **módosítások**

- Összesen négy változásai:

  ![Az Application Insights hozzáadásával módosított fájlok képernyőképe](./media/app-insights-asp-net-core/009-changes.png)

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

- Három fájlt módosítanak: (kiemelés hozzá további megjegyzések)

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

## <a name="synthetic-transactions-with-powershell"></a>Szintetikus tranzakciók a PowerShell-lel

Automatizálhatja az alkalmazás a szintetikus tranzakciók kérelmeket.

1. Az IIS Express kattintva az alkalmazás futtatása ![Képernyőfelvétel a Visual Studio IIS Express ikon](./media/app-insights-asp-net-core/004-iis-express.png)

2. Másolja az URL-címet a böngésző címsorában. A következő formátumban van `http://localhost:{random port number}`

   ![Képernyőkép a böngészőcímsorba URL-címe](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Futtassa a következő PowerShell-hurok létrehozása a vizsga alkalmazás 100 szintetikus tranzakciókat. Módosítsa a portszámot után **localhost:** megfelelően az előző lépésben másolt URL-címét.

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-application-insights-portal"></a>Nyissa meg az Application Insights portál

Futtatás után a PowerShell, az előző szakaszban nyissa meg az Application Insights a tranzakciók megtekintése, és győződjön meg arról, hogy gyűjtenek adatokat. 

A Visual Studio menüjében válassza **projekt** > **Application Insights** > **Application Insights portál megnyitása**

   ![Képernyőkép az Application Insights áttekintése](./media/app-insights-asp-net-core/010-portal.png)

> [!NOTE]
> A fenti képernyőképen látható **élő Stream**, **lapmegtekintés betöltési ideje**, és **sikertelen kérelmek** jelenleg nem gyűjtött. A következő szakasz mindegyik hozzáadása vezeti végig. Ha már gyűjtött **élő Stream**, és **lapmegtekintés betöltési ideje**, csak kövesse a lépéseket **sikertelen kérelmek**.

## <a name="collect-failed-requests-live-stream--page-view-load-time"></a>Sikertelen kérelmek, az élő Stream és a lapmegtekintés betöltési ideje gyűjtése

### <a name="failed-requests"></a>Sikertelen kérelmek

Technikailag **sikertelen kérelmek** összegyűjtése folyamatban van, de még nincs történt. A folyamat egy egyéni kivétel mentén felgyorsítása érdekében lehet hozzáadni a meglévő projekt egy való életből vett kivétel szimulálásához. Ha az alkalmazása továbbra is fut a Visual Studióban a folytatás előtt **hibakeresés leállításához** (Shift + F5)

1. A **Megoldáskezelőben** > bontsa ki a **oldalak** > **About.cshtml** > Nyissa meg a **About.cshtml.cs**.

   ![Képernyőfelvétel a Visual Studio Solution Explorerben](./media/app-insights-asp-net-core/011-about.png)

2. Adja hozzá a kivétel ``Message=`` , és mentse a fájlt a módosítást.

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

Az Application Insights élő Stream funkciójának az ASP.NET Core frissítésével eléréséhez a **Microsoft.ApplicationInsights.AspNetCore 2.2.0** NuGet-csomagok.

Válassza ki a Visual Studióban a **projekt** > **NuGet-csomagok kezelése** > **Microsoft.ApplicationInsights.AspNetCore** > verzió **2.2.0** > **frissítés**.

  ![Képernyőfelvétel a NuGet-Csomagkezelő](./media/app-insights-asp-net-core/012-nuget-update.png)

Több megerősítési kérések akkor fordul elő. Olvassa el és fogadja el, ha elfogadja a módosításokat.

### <a name="page-view-load-time"></a>Lapmegtekintés betöltési ideje

1. A Visual Studióban keresse meg **Megoldáskezelőben** > **oldalak** > két fájlt kell módosítani: _Layout.cshtml_, és  _ViewImports.cshtml_

2. Az __ViewImports.cshtml_, adja hozzá:

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. A **_Layout.cshtml** adja hozzá az alábbi előtt a ``</head>`` címke, is minden további parancsfájlok előtt.

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-live-stream"></a>Teszt sikertelen volt a kérelmek, a lapmegtekintés betöltési ideje, az élő Stream

Teszteléséhez, és győződjön meg arról, hogy minden működik:

1. Az IIS Express kattintva az alkalmazás futtatása ![Képernyőfelvétel a Visual Studio IIS Express ikon](./media/app-insights-asp-net-core/0012-iis-express.png)

2. Keresse meg a **kapcsolatos** a vizsgált kivételt kiváltó lapot. (Ha hibakeresési módban futtatja, kell kattintson **Folytatás** megjelennek az Application Insights a kivétel a Visual studióban.)

3. Futtassa újra a szimulált PowerShell tranzakció-szkript korábban (szükség lehet a port számát a parancsfájlt, módosíthatja.)

4. Ha nem még mindig nyitva a Visual Studio menüjében válassza az Applications Insights áttekintése **projekt** > **Application Insights** > **alkalmazás megnyitása Insights-portál**. 

   > [!TIP]
   > Ha az új adatforgalom még nem lát, ellenőrizze a **időtartomány** kattintson **frissítése**.

   ![Képernyőkép az áttekintési ablaka](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Válassza ki az élő Stream

   ![Képernyőkép a Stream az élő metrikák](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Ha a PowerShell parancsfájl továbbra is fut, élő metrikák kell látnia, ha leállt, futtassa a parancsfájlt újra az élő Stream meg.)

## <a name="app-insights-sdk-comparison"></a>App Insights SDK-t összehasonlító

Az Application Insights termékcsoport dolgozott a merevlemez között funkcióparitás eléréséhez a [teljes .NET-keretrendszer SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) és a .net Core SDK-t. A 2.2.0 kiadása a [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) az Application Insights szolgáltatás résnek nagymértékben be van zárva.

Ismerje meg jobban az eltérések és közötti kompromisszumot kínál a [.NET és .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

   | SDK-t összehasonlító | ASP.NET        | Az ASP.NET Core 2.1.0    | Az ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Élő metrikák**      | **+** |**-** | **+** |
   | **Kiszolgáló Telemetriai csatorna** | **+** |**-** | **+**|
   |**Az adaptív mintavétel**| **+** | **-** | **+**|
   | **SQL függőségi hívások**     | **+** |**-** | **+**|
   | **Teljesítményszámlálók*** | **+** | **-**| **-**|

_Teljesítményszámlálók_ ebben a környezetben hivatkozik [kiszolgálóoldali teljesítményszámlálók](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) , például a processzor, memória és lemezhasználat.

## <a name="open-source-sdk"></a>Open-source SDK
[Olvassa el, és hozzájárulnak a kódot](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)

## <a name="application-insights-search-continued"></a>Application Insights keresés folytatása

Jobb az Application Insights keresés működésének megismerése a Visual Studióban egy ASP.NET Core 2 projekt akkor is, ha az Application Insights NuGet-csomagok egy explicit telepítése még nem történt. Megvizsgálhatja a hibakeresési kimeneti hasznos lehet.

Ha a kimeneti szóra keres _insight_ azt ki vannak emelve az eredmények a következőhöz hasonló:

```DebugOuput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

A következő két szerelvények betöltése coreclr-nek: 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

És a _nincs beállítva_ az Application Insights minden példánya telemetriai jelzi, hogy az alkalmazás akkor nem változón társítva, így az alkalmazás futása közben létrehozott az adatok nem küld az Azure-ba, és csak elérhető helyi keresés és elemzés.

Része hogyan Ez akkor lehetséges, hogy a NuGet-csomag _Microsoft.AspNetCore.All_ függőségként veszi [ _Microsoft.ASPNetCoreApplicationInsights.HostingStartup_](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1)

![Képernyőfelvétel a NuGet függőségi grafikon a Microsoft.AspNETCore.all](./media/app-insights-asp-net-core/013-dependency.png)

A Visual Studio-en kívül, ha egy ASP.NET Core-projektet a VSCode vagy valamilyen más szerkesztővel szerkesztése ezekkel a szerelvényekkel nem automatikusan betöltése során hibakeresési Ha még nem explicit módon hozzáadva a projekthez az Application Insights.

Azonban a Visual Studióban a világítás mentése helyi Application Insights szolgáltatást a külső szerelvények van használatával valósítható meg használatát a [IHostingStartup felület](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1) amely hibakeresés során dinamikusan hozzáadja az Application Insights.

További információ az alkalmazás továbbfejlesztésének egy [ASP.NET Core IHostingStartup és a külső szerelvény](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="how-to-disable-application-insights-in-visual-studio-net-core-projects"></a>Hogyan lehet letiltani az Application Insights a Visual Studio .NET Core-projektek

Az Application Insights keresési funkciók mentése a automatikus világos egyes hasznosak lehetnek, jönnek létre, ha nem várt érték láthasson hibakeresési telemetriai zavaró lehet.

Ha csak letiltja a telemetriai adatokat. generációs elegendő is hozzáadhat a kódblokk konfigurálása módszer a _Startup.cs_ fájlt:

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

Továbbra is betölti a CoreCLR _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ és _Microsoft.ApplicationInsights.AspNetCore.dll_, de azok nem csinál semmi.

Ha azt szeretné, teljesen letiltja az Application Insights a Visual Studio .NET Core-projektben,-e az előnyben részesített módszer kiválasztásához **eszközök** > **beállítások**  >   **Projektek és megoldások** > **webes projektek** >, és jelölje be a jelölőnégyzetet, helyi Application Insights az ASP.NET Core webes projektek a letiltása. Ez a funkció a Visual Studio 15,6 lett hozzáadva.

![Képernyőfelvétel a Visual Studio beállítások ablak Web-projektek képernyő](./media/app-insights-asp-net-core/014-disable.png)

Ha a Visual Studio egy korábbi verzióját futtatja, és teljesen eltávolítja az összes szerelvények IHostingStartup optionfile is felveheti:

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

Vagy vegyen fel alternatív megoldásként ``"ASPNETCORE_preventHostingStartup": "True"`` való _launchSettings.json_ környezeti változókat.

Ezen módszerek bármelyikével problémája, nem csak letiltja semmit, a Visual Studióban, amely a funkció be IHostingStartup világos volt támaszkodva kikapcsolja az Application Insights.

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>További lépések
* [Ismerje meg a felhasználók folyamatok](app-insights-usage-flows.md) tudni, hogy a felhasználók hogyan navigálnak az alkalmazáson keresztül.
* [Pillanatkép gyűjtésének konfigurálása](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) forráskód és a változók állapotának megtekintéséhez jelenleg a függvény kivételt vált ki.
* [Az API-val](app-insights-api-custom-events-metrics.md) küldése a saját események és mérőszámok az alkalmazás teljesítményének és használatának részletesebb megjelenítéséhez.
* [Rendelkezésre állási tesztek](app-insights-monitor-web-app-availability.md) ellenőrizze az alkalmazás folyamatosan az egész világon.
