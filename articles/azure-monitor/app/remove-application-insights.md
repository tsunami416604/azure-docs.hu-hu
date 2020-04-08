---
title: Alkalmazáselemzési adatok eltávolítása a Visual Studióban – Azure-figyelő
description: Az Application Insights SDK ASP.NET és ASP.NET Core eltávolítása a Visual Studióban.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 1c9ff8d3d305645ac7d113421e2c6c5f8451bd2b
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80805104"
---
# <a name="how-to-remove-application-insights-in-visual-studio"></a>Az Application Insights eltávolítása a Visual Studióban

Ez a cikk bemutatja, hogyan távolíthatja el a ASP.NET és ASP.NET Core Application Insights SDK-t a Visual Studióban.

Az Application Insights eltávolításához el kell távolítania a NuGet-csomagokat és -hivatkozásokat az alkalmazás API-jából. A NuGet-csomagokat a Visual Studio Csomagkezelő konzolvagy a NuGet-megoldás kezelése segítségével távolíthatja el. A következő szakaszokban két módszer látható a NuGet csomagok eltávolításához, valamint a projekthez automatikusan hozzáadott lehetőségekhez. Győződjön meg arról, hogy erősítse meg a hozzáadott fájlokat, és területek a saját kódot, amelyben hívásokat kezdeményezett az API-t eltávolítjuk.

## <a name="uninstall-using-the-package-management-console"></a>Eltávolítás a Csomagkezelő konzol használatával

# <a name="net"></a>[.NET](#tab/net)

1. A Csomagkezelő konzol megnyitásához válassza a felső menü Eszközök > NuGet csomagkezelő > Csomagkezelő konzolját.
     
    ![A felső menüben kattintson az Eszközök > a NuGet csomagkezelő > csomagkezelő konzoljára](./media/remove-application-insights/package-manager.png)

    > [!NOTE]
    > Ha a nyomkövetési gyűjtemény engedélyezve van, először el kell távolítania a Microsoft.ApplicationInsights.TraceListener alkalmazást. Írja `Uninstall-package Microsoft.ApplicationInsights.TraceListener` be az alábbi lépést a Microsoft.ApplicationInsights.Web eltávolításához.

1. Írja be a következő parancsot: `Uninstall-Package Microsoft.ApplicationInsights.Web -RemoveDependencies`

    A parancs megadása után az Application Insights-csomag és annak összes függősége eltávolításra kerül a projektből.
    
    ![Adja meg a parancsot a konzolban](./media/remove-application-insights/package-management-console.png)

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. A Csomagkezelő konzol megnyitásához válassza a felső menü Eszközök > NuGet csomagkezelő > Csomagkezelő konzolját.

    ![A felső menüben kattintson az Eszközök > a NuGet csomagkezelő > csomagkezelő konzoljára](./media/remove-application-insights/package-manager.png)

1. Írja be a következő parancsot: ` Uninstall-Package Microsoft.ApplicationInsights.AspNetCore -RemoveDependencies`

    A parancs megadása után az Application Insights-csomag és annak összes függősége eltávolításra kerül a projektből.

---

## <a name="uninstall-using-the-visual-studio-nugetui"></a>Eltávolítás a Visual Studio NuGet felhasználói felületével

# <a name="net"></a>[.NET](#tab/net)

1. A jobb oldali *Megoldáskezelőben* kattintson a jobb gombbal a **Megoldás** gombra, és válassza **a NuGet csomagok kezelése a megoldáshoz**lehetőséget.

    Ekkor megjelenik egy képernyő, amely lehetővé teszi a projekt részét képezi NuGet csomagok szerkesztését.
    
     ![Kattintson a jobb gombbal a Megoldás elemre a Megoldáskezelőben, majd válassza a NuGet csomagok kezelése a megoldáshoz lehetőséget.](./media/remove-application-insights/manage-nuget-framework.png)

    > [!NOTE]
    > Ha a nyomkövetési gyűjtemény engedélyezve van, először el kell távolítania a Microsoft.ApplicationInsights.TraceListener elemet a kijelölt függőségek eltávolítása nélkül, majd az alábbi lépéseket követve távolítsa el a Microsoft.ApplicationInsights.Web elemet a kijelölt függőségek eltávolításával.
    
1. Kattintson a "Microsoft.ApplicationInsights.Web" csomagra.A jobb oldalon jelölje be a *Project* melletti jelölőnégyzetet az összes projekt kijelöléséhez.
    
1. Ha az eltávolításkor el szeretné távolítani az összes függőséget, kattintson a **Beállítások** legördülő menügombra annak a szakasznak a alatt, ahol a projektet kiválasztotta.

    Az *Eltávolítási beállítások csoportban*jelölje be a *Függőségek eltávolítása jelölőnégyzetet.*

1. Válassza az **Eltávolítás** lehetőséget.
    
    ![A függőségek eltávolításának, majd az eltávolításnak a ellenőrzése](./media/remove-application-insights/uninstall-framework.png)

    Megjelenik egy párbeszédpanel, amely en megjelenik az összes eltávolítandó függőség az alkalmazásból.Az eltávolításhoz válassza az **OK gombot.** 
    
    ![A függőségek eltávolításának, majd az eltávolításnak a ellenőrzése](./media/remove-application-insights/preview-uninstall-framework.png)
    
1.  Miután minden ellett távolodva, a *Solution Explorer*ben továbbra is megjelenhet az "ApplicationInsights.config" és a "AiHandleErrorAttribute.cs" .A két fájlt manuálisan is törölheti.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

1. A jobb oldali *Megoldáskezelőben* kattintson a jobb gombbal a **Megoldás** gombra, és válassza **a NuGet csomagok kezelése a megoldáshoz**lehetőséget.

    Ekkor megjelenik egy képernyő, amely lehetővé teszi a projekt részét képezi NuGet csomagok szerkesztését.

    ![Kattintson a jobb gombbal a Megoldás elemre a Megoldáskezelőben, majd válassza a NuGet csomagok kezelése a megoldáshoz lehetőséget.](./media/remove-application-insights/manage-nuget-core.png)

1. Kattintson a "Microsoft.ApplicationInsights.AspNetCore" csomagra. A jobb oldalon jelölje be a *Project* melletti jelölőnégyzetet az összes projekt kijelöléséhez, majd válassza az **Eltávolítás**lehetőséget.

    ![A függőségek eltávolításának, majd az eltávolításnak a ellenőrzése](./media/remove-application-insights/uninstall-core.png)

---

## <a name="what-is-created-when-you-add-application-insights"></a>Mi jön létre az Application Insights hozzáadásakor?

Amikor hozzáadja az Application Insightsot a projekthez, fájlokat hoz létre, és kódot ad hozzá néhány fájlhoz. A NuGet csomagok kizárólag eltávolítása nem mindig dobja el a fájlokat és a kódot. Az Application Insights teljes eltávolításához ellenőrizze és törölje manuálisan a hozzáadott kódot vagy fájlokat a projektben hozzáadott API-hívásokkal együtt.

# <a name="net"></a>[.NET](#tab/net)

Amikor hozzáadja az Application Insights telemetriai adatait egy Visual Studio ASP.NET projekthez, a következő fájlokat adja hozzá:

- ApplicationInsights.config
- AiHandleErrorAttribute.cs

A szöveg a következő kódokkal egészül ki:

- [A projekt neve].csproj

    ```C#
     <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4</ApplicationInsightsResourceId>
    ```

- Csomagok.config

    ```xml
    <packages>
    ...
    
      <package id="Microsoft.ApplicationInsights" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Agent.Intercept" version="2.4.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.DependencyCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.PerfCounterCollector" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.Web" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer" version="2.12.0" targetFramework="net472" />
      <package id="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" version="2.12.0" targetFramework="net472" />
    
      <package id="Microsoft.AspNet.TelemetryCorrelation" version="1.0.7" targetFramework="net472" />
    
      <package id="System.Buffers" version="4.4.0" targetFramework="net472" />
      <package id="System.Diagnostics.DiagnosticSource" version="4.6.0" targetFramework="net472" />
      <package id="System.Memory" version="4.5.3" targetFramework="net472" />
      <package id="System.Numerics.Vectors" version="4.4.0" targetFramework="net472" />
      <package id="System.Runtime.CompilerServices.Unsafe" version="4.5.2" targetFramework="net472" />
    ...
    </packages>
    ```

- Layout.cshtml

    Ha a projekt rendelkezik Layout.cshtml fájllal, az alábbi kód hozzáadódik.
    
    ```html
    <head>
    ...
        <script type = 'text/javascript' >
            var appInsights=window.appInsights||function(config)
            {
                function r(config){ t[config] = function(){ var i = arguments; t.queue.push(function(){ t[config].apply(t, i)})} }
                var t = { config:config},u=document,e=window,o='script',s=u.createElement(o),i,f;for(s.src=config.url||'//az416426.vo.msecnd.net/scripts/a/ai.0.js',u.getElementsByTagName(o)[0].parentNode.appendChild(s),t.cookie=u.cookie,t.queue=[],i=['Event','Exception','Metric','PageView','Trace','Ajax'];i.length;)r('track'+i.pop());return r('setAuthenticatedUserContext'),r('clearAuthenticatedUserContext'),config.disableExceptionTracking||(i='onerror',r('_'+i),f=e[i],e[i]=function(config, r, u, e, o) { var s = f && f(config, r, u, e, o); return s !== !0 && t['_' + i](config, r, u, e, o),s}),t
            }({
                instrumentationKey:'00000000-0000-0000-0000-000000000000'
            });
            
            window.appInsights=appInsights;
            appInsights.trackPageView();
        </script>
    ...
    </head>
    ```

- ConnectedService.json

    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=613413"
      }
    }
    ```

- FilterConfig.cs

    ```csharp
            public static void RegisterGlobalFilters(GlobalFilterCollection filters)
            {
                filters.Add(new ErrorHandler.AiHandleErrorAttribute());// This line was added
            }
    ```

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Amikor hozzáadja az Application Insights telemetriai adatait egy Visual Studio ASP.NET Core sablonprojekthez, a következő kódot adja hozzá:

- [A projekt neve].csproj

    ```csharp
      <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <ApplicationInsightsResourceId>/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Default-ApplicationInsights-EastUS/providers/microsoft.insights/components/WebApplication4core</ApplicationInsightsResourceId>
      </PropertyGroup>
    
      <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.12.0" />
      </ItemGroup>
    
      <ItemGroup>
        <WCFMetadata Include="Connected Services" />
      </ItemGroup>
    ```

- Appsettings.json:

    ```json
    "ApplicationInsights": {
        "InstrumentationKey": "00000000-0000-0000-0000-000000000000"
    ```

- ConnectedService.json
    
    ```json
    {
      "ProviderId": "Microsoft.ApplicationInsights.ConnectedService.ConnectedServiceProvider",
      "Version": "16.0.0.0",
      "GettingStartedDocument": {
        "Uri": "https://go.microsoft.com/fwlink/?LinkID=798432"
      }
    }
    ```
- Startup.cs

    ```csharp
       public void ConfigureServices(IServiceCollection services)
            {
                services.AddRazorPages();
                services.AddApplicationInsightsTelemetry(); // This is added
            }
    ```

---

## <a name="next-steps"></a>További lépések

- [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview)