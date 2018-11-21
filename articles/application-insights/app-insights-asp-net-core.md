---
title: Az Azure Application Insights az ASP.NET Core |} A Microsoft Docs
description: Figyelheti a webalkalmazások rendelkezésre állását, teljesítményét és használatát.
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
ms.openlocfilehash: 3e838cd45eefa5b5b644992bb8eae36abf3ec33a
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52276346"
---
# <a name="application-insights-for-aspnet-core"></a>ASP.Net Core-hoz készült Application Insights

Az Azure Application Insights a a webalkalmazás a code szintre részletes figyelését teszi lehetővé. Könnyedén figyelheti a webalkalmazása rendelkezésre állását, teljesítményét és használatát. Emellett egyszerűen azonosíthatja és diagnosztizálhatja az alkalmazás hibáit anélkül, hogy meg kellene várnia, amíg egy felhasználó jelenti azokat.

Ez a cikk végigvezeti az egy mintául szolgáló ASP.NET Core létrehozásának lépésein [Razor Pages](https://docs.microsoft.com/aspnet/core/mvc/razor-pages/?tabs=visual-studio) alkalmazáshoz a Visual Studióban. Azt is bemutatja, hogyan figyelése az Application Insights használatával.

## <a name="prerequisites"></a>Előfeltételek

- .NET core 2.0.0 SDK vagy újabb
- [A Visual Studio 2017](https://www.visualstudio.com/downloads/) 15.7.3 verzió vagy újabb verzió, az ASP.NET- és fejlesztési számítási feladatot

## <a name="create-an-aspnet-core-project-in-visual-studio"></a>Hozzon létre egy ASP.NET Core-projektet a Visual Studióban

1. Kattintson a jobb gombbal **Visual Studio 2017**, majd válassza ki **Futtatás rendszergazdaként**.
2. Válassza ki **fájl** > **új** > **projekt** (Ctrl + Shift + N).

   ![Képernyőfelvétel a Visual Studio új projekt menü](./media/app-insights-asp-net-core/001-new-project.png)

3. Bontsa ki a **Visual C#** elemet. Válassza ki **.NET Core** > **ASP.NET Core-webalkalmazás**. Adja meg a projekt nevét és a egy megoldás nevét, és válassza ki **hozzon létre új Git-tárház**.

   ![Képernyőfelvétel a Visual Studio új projekt varázsló](./media/app-insights-asp-net-core/002-asp-net-core-web-application.png)

4. Válassza ki **.NET Core** > **az ASP.NET Core 2.0** **webes alkalmazás** > **OK**.

    ![Képernyőfelvétel a Visual Studio új projekt sablon kiválasztása](./media/app-insights-asp-net-core/003-web-application.png)

## <a name="application-insights-search"></a>Application Insights-keresés

A Visual Studio 2015 Update 2 vagy újabb egy ASP.NET Core 2 + alapú projekttel, akkor kihasználhatja [Application Insights keresés](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio), még mielőtt, explicit módon Application Insights hozzáadása a projekthez.

Ez a funkció teszteléséhez:

1. Futtassa az alkalmazást. Futtassa az alkalmazást, jelölje be a **IIS Express** ikonra (![képernyőfelvétel a Visual Studio IIS Express ikon](./media/app-insights-asp-net-core/004-iis-express.png)).

2. Válassza ki **nézet** > **más Windows** > **Application Insights keresés**.

   ![A Visual Studio képernyőképe a diagnosztikai eszközök kiválasztása](./media/app-insights-asp-net-core/005-view-other-windows-search.png)

3. A hibakeresési munkamenet telemetriájában jelenleg csak helyi analysis érhető el. Az Application Insights teljes körű engedélyezéséhez jelölje be **Telemetriai készültségi** a következő szakaszban ismertetett lépéseket felső sarokban, vagy a teljes.

   ![Képernyőfelvétel a Visual Studio Application Insights-keresés](./media/app-insights-asp-net-core/006-search.png)

> [!NOTE]
> További információ a módját, például a Visual Studio lámpa funkciók mentése [Application Insights keresés](app-insights-visual-studio.md) és [Codelensben](app-insights-visual-studio-codelens.md) helyileg előtt Application Insights hozzáadása az ASP.NET Core-projektet, tekintse meg [ Application Insights keresés folyamatos](#application-insights-search-continued).

## <a name="add-application-insights-telemetry"></a>Application Insights Telemetria hozzáadása

1. Válassza ki **projekt** > **Application Insights Telemetria hozzáadása**. (Vagy a jobb gombbal **csatlakoztatott szolgáltatás**, majd válassza ki **csatlakoztatott szolgáltatás hozzáadása**.)

    ![Új projekt menüben a Visual Studio képernyőképe](./media/app-insights-asp-net-core/007-project-add-telemetry.png)

2. Válassza ki **Ismerkedés**. (A Visual Studio verziójától függően a szöveg kis mértékben eltérhetnek. Egyes régebbi verziók esetében a **ingyenes próba megkezdése** inkább gombra.)

    ![Képernyőkép az Application Insights első lépések gombra](./media/app-insights-asp-net-core/008-get-started.png)

3. Válassza ki az előfizetését, majd válassza ki **erőforrás** > **regisztrálása**.

## <a name="changes-made-to-your-project"></a>A projekt végrehajtott módosítások

Application Insights jelenleg alacsony többletterhelést okoznak. A módosítások fényében, amelyek a projekt adja hozzá az Application Insights telemetria áttekintése:

Válassza ki **nézet** > **Team Explorer** (Ctrl +\, Ctrl + M) > **projekt** > **módosítások**

- A módosítások összesen négy jelennek meg:

  ![Az Application Insights hozzáadásával módosított fájlok képernyőképe](./media/app-insights-asp-net-core/009-changes.png)

- Egy új fájl jön létre:

  -  _ConnectedService.json_

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "8.12.10405.1",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```

- Három fájlt módosítanak (kiemelés hozzá további megjegyzések):

  - _appSettings.JSON_:

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

  - _ContosoDotNetCore.csproj_:

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

  -  _Program.cs_:

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

Szintetikus tranzakciók használatával automatizálhatja a kérelmeket az alkalmazás:

1. Futtassa az alkalmazást, válassza a ![Képernyőfelvétel a Visual Studio IIS Express ikon](./media/app-insights-asp-net-core/004-iis-express.png) ikon.

2. Másolja az URL-címet a böngésző címsorában. Az URL-cím a következő formátumban kell `http://localhost:<port number>`.

   ![Képernyőkép a böngésző URL-CÍMÉT a címsorba](./media/app-insights-asp-net-core/0013-copy-url.png)

3. Futtassa a következő PowerShell-hurok létrehozása 100 szintetikus tranzakciók a vizsga alkalmazás használatával. Módosítsa a portszámot után `localhost:` megfelelően az előző lépésben másolt URL-címét. Példa:

   ```PowerShell
   for ($i = 0 ; $i -lt 100; $i++)
   {
    Invoke-WebRequest -uri http://localhost:50984/
   }
   ```

## <a name="open-the-application-insights-portal"></a>Az Application Insights portál megnyitása

Után az előző szakaszban futtatta a PowerShell-parancsokat, nyissa meg az Application Insights megtekintheti a tranzakciókat, és győződjön meg arról, hogy gyűjtenek adatokat. 

A Visual Studio menüjében válassza **projekt** > **Application Insights** > **Application Insights portál megnyitása**.

   ![Képernyőkép az Application Insights áttekintése](./media/app-insights-asp-net-core/010-portal.png)

> [!NOTE]
> Az előző példa képernyőfelvételen **élő Stream**, **lapmegtekintés betöltési ideje**, és **sikertelen kérelmek** nem gyűjtött. Ez a szakasz végigvezeti a hozzáadásához minden egyes lépéseket. Ha már gyűjt **élő Stream** és **lapmegtekintés betöltési ideje**, hajtsa végre a lépéseket csak **sikertelen kérelmek**.

## <a name="collect-failed-requests-live-stream-and-page-view-load-time"></a>Sikertelen kérelmek, az élő Stream és a lapmegtekintés betöltési ideje gyűjtése

### <a name="failed-requests"></a>Sikertelen kérelmek

Technikailag sikertelen kérelmek gyűjtött, de még nincsenek sikertelen kérelmek történt. Gyorsítsa fel a folyamatot, egy egyéni kivétel adhat hozzá a meglévő projekt egy való életből vett kivétel szimulálásához. Ha az alkalmazás továbbra is fut a Visual Studióban, mielőtt továbblépne, válassza ki a **hibakeresés leállításához** (Shift + F5).

1. A **Megoldáskezelőben**, bontsa ki a **oldalak** > **About.cshtml**, majd nyissa meg *About.cshtml.cs*.

   ![Képernyőfelvétel a Visual Studio Solution Explorerben](./media/app-insights-asp-net-core/011-about.png)

2. Adja hozzá a kivétel ``Message=``, majd mentse a módosítást a fájl.

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

Az élő Stream funkciók, az Application Insights az ASP.NET Core használatának eléréséhez a Microsoft.ApplicationInsights.AspNetCore 2.2.0 NuGet-csomagok frissítése.

A Visual Studióban válassza ki a **projekt** > **NuGet-csomagok kezelése** > **Microsoft.ApplicationInsights.AspNetCore** > (verzió) **2.2.0** > **frissítés**.

  ![Képernyőfelvétel a NuGet-Csomagkezelő](./media/app-insights-asp-net-core/012-nuget-update.png)

Több megerősítési kérések jelennek meg. Olvassa el és fogadja el, ha elfogadja a módosításokat.

### <a name="page-view-load-time"></a>Lapmegtekintés betöltési ideje

1. A Visual Studióban nyissa meg **Megoldáskezelőben** > **oldalak**. Két fájlt kell módosítania: *Layout.cshtml* és *ViewImports.cshtml*.

2. A *ViewImports.cshtml*, adja hozzá a kódot:

   ```csharp
   @using Microsoft.ApplicationInsights.AspNetCore
   @inject JavaScriptSnippet snippet
   ```

3. A *Layout.cshtml*, előtt az alábbi kódot a ``</head>`` címke és egyéb szkripteket előtt:

    ```csharp
    @Html.Raw(snippet.FullScript)
    ```

### <a name="test-failed-requests-page-view-load-time-and-live-stream"></a>Teszt sikertelen kérelmeket, lapmegtekintés betöltési ideje, és az élő Stream

Tesztelje, és győződjön meg arról, hogy minden működik:

1. Futtassa az alkalmazást. Futtassa az alkalmazást, válassza a ![Képernyőfelvétel a Visual Studio IIS Express ikon](./media/app-insights-asp-net-core/004-iis-express.png) ikon.

2. Nyissa meg a **kapcsolatos** a vizsgált kivételt kiváltó lapot. (Ha hibakeresési módban, a Visual Studióban válassza ki a **Folytatás** az Application Insights megjelennek a kivétel.)

3. Futtassa újra a szimulált PowerShell tranzakció parancsfájl korábban már használt. (Előfordulhat, hogy kell módosítani a port számát, a parancsfájl.)

4. Ha a **áttekintése** Applications Insights lapjának még nincs megnyitva, a Visual Studio menüjében válassza a **projekt** > **Application Insights**  >  **Nyissa meg az Application Insights portál**. 

   > [!TIP]
   > Ha nem látja az új forgalmat, ellenőrizze a értékét **időtartomány**, majd válassza ki **frissítése**.

   ![Az áttekintés ablak képernyőképe](./media/app-insights-asp-net-core/0019-overview-updated.png)

5. Válassza ki **élő Stream**.

   ![Képernyőkép a Stream az élő metrikák](./media/app-insights-asp-net-core/0020-live-metrics-stream.png)

   (Ha a PowerShell-parancsfájl továbbra is fut, megtekintheti az élő metrikákat. Ha a PowerShell-parancsfájl futása leállt, futtassa a parancsprogramot az élő Stream nyílt.)

## <a name="application-insights-sdk-comparison"></a>Application Insights SDK-t összehasonlító

Az Application Insights termékcsoport dolgozott a merevlemez között funkcióparitás eléréséhez a [teljes .NET-keretrendszer SDK](https://github.com/Microsoft/ApplicationInsights-dotnet) és a .NET Core SDK-t. A 2.2.0 kiadása a [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore) az Application Insights nagymértékben bezárja a szolgáltatás közötti eltérés.

Az alábbi táblázat ismerteti a különbségeket több és közti kompromisszummal [.NET és .NET Core](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server):

   | SDK-t összehasonlító | ASP.NET        | Az ASP.NET Core 2.1.0    | Az ASP.NET Core 2.2.0 |
  |:-- | :-------------: |:------------------------:|:----------------------:|
   | **Élő metrikák**      | **+** |**-** | **+** |
   | **Kiszolgáló telemetriai csatorna** | **+** |**-** | **+**|
   |**Az adaptív mintavétel**| **+** | **-** | **+**|
   | **SQL függőségi hívások**     | **+** |**-** | **+**|
   | **Teljesítményszámlálók*** | **+** | **-**| **-**|

Tekintse meg ebben a környezetben teljesítményszámlálók [kiszolgálóoldali teljesítményszámlálók](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) , például a processzor, memória és lemezhasználat.

## <a name="open-source-sdk"></a>Open-source SDK
[Olvassa el, és hozzájárulnak a kód](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates).

## <a name="application-insights-search-continued"></a>Application Insights keresés folytatása

Ez a szakasz segítségével jobban megismerheti az Application Insights keresés működése a Visual Studióban egy ASP.NET Core 2 projekt. Ezzel a módszerrel úgy működik, akkor is, ha még nem explicit módon telepítette az Application Insights NuGet-csomagok még. Azt is hasznos lehet a hibakeresési kimeneti vizsgálatához.

Ha a kimeneti szóra keres _insight_, az alábbihoz hasonló eredményeket vannak kiemelve:

```DebugOutput
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.aspnetcore.applicationinsights.hostingstartup\2.0.3\lib\netcoreapp2.0\Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll'.
'dotnet.exe' (CoreCLR: clrhost): Loaded 'C:\Program Files\dotnet\store\x64\netcoreapp2.0\microsoft.applicationinsights.aspnetcore\2.1.1\lib\netstandard1.6\Microsoft.ApplicationInsights.AspNetCore.dll'.

Application Insights Telemetry (unconfigured): {"name":"Microsoft.ApplicationInsights.Dev.Message","time":"2018-06-03T17:32:38.2796801Z","tags":{"ai.location.ip":"127.0.0.1","ai.operation.name":"DEBUG /","ai.internal.sdkVersion":"aspnet5c:2.1.1","ai.application.ver":"1.0.0.0","ai.cloud.roleInstance":"CONTOSO-SERVER","ai.operation.id":"de85878e-4618b05bad11b5a6","ai.internal.nodeName":"CONTOSO-SERVER","ai.operation.parentId":"|de85878e-4618b05bad11b5a6."},"data":{"baseType":"MessageData","baseData":{"ver":2,"message":"Request starting HTTP/1.1 DEBUG http://localhost:53022/  0","severityLevel":"Information","properties":{"AspNetCoreEnvironment":"Development","Protocol":"HTTP/1.1","CategoryName":"Microsoft.AspNetCore.Hosting.Internal.WebHost","Host":"localhost:53022","Path":"/","Scheme":"http","ContentLength":"0","DeveloperMode":"true","Method":"DEBUG"}}}}
```

A kimenetben coreclr-nek két szerelvényből tölti be: 

- _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_
- _Microsoft.ApplicationInsights.AspNetCore.dll_.

A _nincs beállítva_ hivatkozás az Application Insights telemetria minden példánya azt jelzi, hogy az alkalmazás nem változón társítva. Az alkalmazás futása közben létrehozott az adatok nem küldi el az Azure-bA. Az adatok csak a helyi keresési és elemzési érhető el.

Az funkciók azért lehetséges részben a NuGet-csomag _Microsoft.AspNetCore.All_ veszi [ _Microsoft.ASPNetCoreApplicationInsights.HostingStartup_ ](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.applicationinsights.hostingstartup.applicationinsightshostingstartup?view=aspnetcore-2.1) függőségként.

![Képernyőfelvétel a NuGet függőségi grafikon a Microsoft.AspNETCore.all](./media/app-insights-asp-net-core/013-dependency.png)

A Visual Studio-en kívül Szerkesztés egy ASP.NET Core-projektet a VSCode vagy más szerkesztőbe, ha ezekkel a szerelvényekkel nem automatikusan betöltése során hibakeresési Ha még nem explicit módon hozzáadva a projekthez az Application Insights.

Azonban a Visual Studióban, a külső szerelvények helyi Application Insights funkciók mentése a világítás végezhető el a [IHostingStartup felület](https://docs.microsoft.com/dotnet/api/microsoft.aspnetcore.hosting.ihostingstartup?view=aspnetcore-2.1). A felület dinamikusan hozzáadja az Application Insights hibakeresése során.

További információ az alkalmazás továbbfejlesztésének egy [ASP.NET Core IHostingStartup és a külső szerelvény](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/platform-specific-configuration?view=aspnetcore-2.1). 

### <a name="disable-application-insights-in-visual-studio-net-core-projects"></a>Tiltsa le az Application Insights a Visual Studio .NET Core-projektek

Bár a automatikus világos-fel az Application Insights keresés funkció hasznos lehet, megrendítő azt látni, hibakeresési telemetriai jönnek létre, ha nem várt, ehelyett zavaró lehet.

Csak a telemetria generációs letiltása nem elegendő, ha a kódblokkot, amellyel is hozzáadhat a **konfigurálása** módszere a _Startup.cs_ fájlt:

```csharp
  var configuration = app.ApplicationServices.GetService<Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration>();
            configuration.DisableTelemetry = true;
            if (env.IsDevelopment())
```

Coreclr-nek is betöltődik _Microsoft.AspNetCore.ApplicationInsights.HostingStartup.dll_ és _Microsoft.ApplicationInsights.AspNetCore.dll_, de a fájlok nem csinál semmi.

Ha azt szeretné, teljesen letiltja az Application Insights a Visual Studio .NET Core-projektben,-e az előnyben részesített módszer kiválasztásához **eszközök** > **beállítások**  >   **Projektek és megoldások** > **webes projektek**. Válassza ki a **tiltsa le a helyi Application Insights az ASP.NET Core webes projektek** jelölőnégyzetet. Ez a funkció a Visual Studio 15,6 lett hozzáadva.

![Képernyőfelvétel a Visual Studio beállítások ablak Web-projektek képernyő](./media/app-insights-asp-net-core/014-disable.png)

Ha a Visual Studio egy korábbi verzióját futtatja, és teljesen eltávolítja az összes szerelvények keresztül betöltött *IHostingStartup*, két lehetősége van:

* Adjon hozzá `.UseSetting(WebHostDefaults.PreventHostingStartupKey, "true")` való _Program.cs_:

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

* Adjon hozzá ``"ASPNETCORE_preventHostingStartup": "True"`` való _launchSettings.json_ környezeti változókat.

Ezen módszerek bármelyikével problémája, hogy azok ne tiltsa le csak az Application Insights. A módszerek bármit használta a szolgáltatást a Visual studióban is letilthatók a *IHostingStartup* világos felskálázás funkció.

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player] 

## <a name="next-steps"></a>További lépések
* [Ismerje meg a felhasználói folyamatok](app-insights-usage-flows.md) tudni, hogy a felhasználók hogyan navigálnak az alkalmazáson keresztül.
* [Pillanatkép gyűjtésének konfigurálása](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger#configure-snapshot-collection-for-aspnet-core-20-applications) forráskód és a változók állapotának megtekintéséhez jelenleg a függvény kivételt vált ki.
* [Az API-val](app-insights-api-custom-events-metrics.md) küldése a saját események és mérőszámok az alkalmazás teljesítményének és használatának részletesebb megjelenítéséhez.
* Használat [rendelkezésre állási tesztek](app-insights-monitor-web-app-availability.md) ellenőrizheti az alkalmazás folyamatosan az egész világon.
