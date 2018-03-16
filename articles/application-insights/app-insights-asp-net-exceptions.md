---
title: "Diagnosztizálja a hibákat és kivételeket a webalkalmazásokban az Azure Application insights szolgáltatással |} Microsoft Docs"
description: "ASP.NET alkalmazások együtt – kéréstelemetria kivételei rögzíti."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d1e98390-3ce4-4d04-9351-144314a42aa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: mbullwin
ms.openlocfilehash: ee04fc3338dec7893f9f33322bd6b9af932199e7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Kivételek az Application insights szolgáltatással a webalkalmazások diagnosztizálásához
Az élő webalkalmazását kivételek által jelentett [Application Insights](app-insights-overview.md). Sikertelen kérelmek hozhatók kivételeket és az ügyfél és a kiszolgáló, az eseményeket, így gyorsan felderítheti az az oka.

## <a name="set-up-exception-reporting"></a>Kivétel jelentéskészítés beállítása
* Szeretné, hogy a kiszolgáló alkalmazás jelentett kivételek:
  * Telepítés [Application Insights SDK](app-insights-asp-net.md) az alkalmazás kódjában, vagy
  * Az IIS webkiszolgálón: futtatása [Application Insights Agent](app-insights-monitor-performance-live-website-now.md); vagy
  * Azure-webalkalmazásokban: vegye fel a [Application Insights-bővítmény](app-insights-azure-web-apps.md)
  * Java-webalkalmazások: telepítse a [Java-ügynök](app-insights-java-agent.md)
* Telepítse a [JavaScript részlet](app-insights-javascript.md) a weblapok a böngésző kivételeket.
* Egyes alkalmazás-keretrendszerbeli vagy az egyes beállítások néhány további lépések elvégzésével további kivételeket kell:
  * [Web Forms keretrendszerre](#web-forms)
  * [MVC](#mvc)
  * [Web API 1.*](#web-api-1x)
  * [Web API 2.*](#web-api-2x)
  * [WCF](#wcf)

## <a name="diagnosing-exceptions-using-visual-studio"></a>Visual Studio használatával kivételek diagnosztizálásáról
Nyissa meg az alkalmazás megoldást a Visual Studio, a hibakeresés érdekében.

Futtassa az alkalmazást, és a kiszolgálón vagy a fejlesztői gépen F5 használatával.

Nyissa meg az Application Insights keresési ablak a Visual Studio, majd állítsa be az alkalmazás származó események megjelenítéséhez. Hibakeresése, közben ehhez csak az Application Insights gombra kattintva.

![Kattintson jobb gombbal a projektre, és válassza ki az Application Insights, nyissa meg.](./media/app-insights-asp-net-exceptions/34.png)

Figyelje meg, hogy a jelentésben csak kivételek szűrheti.

*Nincsenek kivételek megjelenítő? Lásd: [kivételek rögzítése](#exceptions).*

Kattintson a Veremkivonat megjelenítendő kivételjelentést.
Kattintson az alábbi veremkivonatban, a megfelelő kódot fájl megnyitásához egy sor mutató hivatkozást.  

A kódban figyelje meg, hogy a CodeLens a kivételek adatait jeleníti meg:

![Kivételek CodeLens értesítés.](./media/app-insights-asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Az Azure portál használatával hibák diagnosztizálása
Az Application Insights tartalmaz egy válogatott APM élmény segítséget nyújtanak a felügyelt alkalmazások hibáinak diagnosztizálásához. Elindításához kattintson a vizsgálat szakaszában található Application Insights-erőforrás menü hibák esetén. Bemutatja, hogy hiba arány trendekről a licencekkel kapcsolatos a kéréseket, azok hány sikertelen, és hány felhasználó érintett teljes képernyős nézet kell megjelennie. A jobb oldalon láthatja, néhány a leghasznosabb azokat a terjesztéseket megadott a kijelölt műveletek, például az első 3 válaszkódot, az első 3 kivétel típusok és a sikertelen függőségi típusnál felső 3 sikertelenek lesznek. 

![Hibák osztályozhatja nézet (operations lap)](./media/app-insights-asp-net-exceptions/FailuresTriageView.png)

Egyetlen kattintással majd tekintheti át reprezentatív minta az egyes műveletek ezek részhalmaza. Különösen diagnosztizálásához kivételek, rákattinthat a jelenik meg egy kivételek részletei panelen, ez például egy adott kivétel száma:

![Kivétel részleteit megjelenítő panelen](./media/app-insights-asp-net-exceptions/ExceptionDetailsBlade.png)

**Másik lehetőségként** helyett megtekint egy adott hibás művelet kivételeket, akkor kezdhet a kivételeket, átfogó áttekintést átvált a Kivételek lapon:

![Hibák osztályozhatja nézet (Kivételek lapon)](./media/app-insights-asp-net-exceptions/FailuresTriageView_Exceptions.png)

Itt láthatja a figyelt alkalmazás által gyűjtött összes kivétel.

*Nincsenek kivételek megjelenítő? Lásd: [kivételek rögzítése](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Egyéni nyomkövetés és naplóadatok
Diagnosztikai adatok lekérése az alkalmazáshoz megadott, szúrhat be a saját telemetriai adatokat küldeni a kódot. Ez a kérelem, nézet és egyéb automatikusan gyűjtött adatok mellett diagnosztikai keresési jelenik meg.

Több lehetőség közül választhat:

* [A trackevent() függvény](app-insights-api-custom-events-metrics.md#trackevent) tipikus felhasználási területe a használati szokásokat, figyelés, de az is elküldi az adatokat az egyéni események diagnosztikai keresésben. Események megnevezett, és képes továbbítani az kapcsolatikarakterlánc-tulajdonságokat és amelyen is numerikus metrikák [szűrheti a diagnosztikai keresések](app-insights-diagnostic-search.md).
* [TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) lehetővé teszi, hogy hosszabb adatküldés például a POST-adatokat.
* [TrackException()](#exceptions) küld kivételeseményekhez megadhat veremkiíratásokat. [További tudnivalók a kivételek](#exceptions).
* Ha már használ egy naplózási keretrendszert, például a Log4Net vagy NLog, akkor [lesznek a naplók rögzítése](app-insights-asp-net-trace-logs.md) és lássák a diagnosztikai keresési kérelem és a kivétel adatai mellett.

Ezek az események megtekintéséhez nyissa meg a [keresési](app-insights-diagnostic-search.md), nyissa meg a szűrő, és válassza a egyéni esemény, nyomkövetésre, vagy kivétel.

![Részletezés](./media/app-insights-asp-net-exceptions/viewCustomEvents.png)

> [!NOTE]
> Ha az alkalmazása sok telemetriát hoz létre, az adaptív mintavételezési modul automatikusan csökkenti a portálra küldött mennyiséget, és csupán az eseményeket megjelenítő töredékeket küld. Események azonos művelet részét képező fog kell vagy legyen kiválasztva csoportként, úgy, hogy a kapcsolódó események közti léphet. [További információk a mintavétel.](app-insights-sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Kérelem POST adatainak megtekintése
Szolgáltatáskérés részleteinek nem tartalmazza az alkalmazást a POST híváson a küldött adatokat. Az adatok jelentette:

* [Az SDK telepítése](app-insights-asp-net.md) a alkalmazás projektben.
* Helyezze be a kódot az alkalmazás hívása [Microsoft.ApplicationInsights.TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace). A POST-adatokat küldenek a üzenet paraméter. Nincs a megengedett méretet, a megadott korlát, meg kell próbálnia úgy, hogy az alapvető adatok küldése.
* A sikertelen kérelmek vizsgálatánál található a társított nyomkövetési adatokat.  

![Részletezés](./media/app-insights-asp-net-exceptions/060-req-related.png)

## <a name="exceptions"></a> Kivételeket és a kapcsolódó diagnosztikai adatokat rögzítése
Először nem jelenik meg a portálon a kivételeket, amelyek az alkalmazás hibákhoz vezethet. Látni fogja, a böngésző kivételek (használata a [JavaScript SDK](app-insights-javascript.md) a weblapok). De a legtöbb kivételek az IIS által észlelt, és el kell írni a kód azok bit.

A következőket teheti:

* **Kivételek explicit módon jelentkezzen** kód beszúrásával a kivételkezelők jelentheti a kivételeket.
* **Kivételek automatikusan rögzítése** úgy konfigurálja az ASP.NET keretrendszer. A szükséges kiegészítés eltérőek a keretrendszer különböző típusú.

## <a name="reporting-exceptions-explicitly"></a>Explicit módon Reporting kivételek
A legegyszerűbb módja TrackException() hívásakor be egy kivételkezelőbe.

```javascript
    try
    { ...
    }
    catch (ex)
    {
      appInsights.trackException(ex, "handler loc",
        {Game: currentGame.Name,
         State: currentGame.State.ToString()});
    }
```

```csharp
    var telemetry = new TelemetryClient();
    ...
    try
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string>
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }
```

```VB
    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try
```

A tulajdonságok és a mérési paraméterek megadása nem kötelező, de hasznos a [szűrést és a hozzáadása](app-insights-diagnostic-search.md) további információt. Például ha egy alkalmazás futtatható több játékok, találta egy adott játékkal kapcsolatos összes kivétel jelentéseket. Szeretné, hogy mindegyik szótár elemek is hozzáadhat.

## <a name="browser-exceptions"></a>Böngészőkivételek
A legtöbb webböngésző kivételek jelenti.

Ha a weblap tartalmazza a parancsfájlok tartalomkézbesítési hálózat vagy a más tartományokban, győződjön meg arról, a script kód attribútuma ```crossorigin="anonymous"```, és a kiszolgáló által küldött [CORS fejlécek](http://enable-cors.org/). Ez lehetővé teszi a veremkivonatot és részletes lekérése nem kezelt JavaScript-kivételeknek az ezekhez az erőforrásokhoz.

## <a name="web-forms"></a>Web Forms keretrendszerre
Web Forms keretrendszerre a HTTP-modulja lesznek képesek a kivételek gyűjtése, ha nincs átirányítja a CustomErrors konfigurálva van.

De ha rendelkezik active átirányításokat, adja hozzá a következő sorokat Global.asax.cs Application_Error funkciójának. (Ha adja hozzá a Global.asax fájl még nem rendelkezik.)

```csharp
    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError  () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }
```

## <a name="mvc"></a>MVC
Application Insights webes SDK 2.6-os verziótól kezdve (beta3 és újabb verziók), az Application Insights gyűjti, nem kezelt kivételek az MVC 5 + vezérlők módszereket automatikusan. Ha korábban hozzáadott egyéni kezelő ilyen kivételek (a következő példákban leírt) nyomon követésére, eltávolíthatja, megakadályozhatja, hogy az kivételek dupla követését.

Nincsenek olyan esetek számát, amelyet a kivételszűrők nem tudja kezelni. Példa:

* A vezérlő konstruktorok kivételek.
* Az üzenet kezelők kivételek.
* Útválasztás során okozott kivételeket.
* Válasz a tartalom szerializálás során okozott kivételeket.
* Kivétel történt az alkalmazás indítás folyamán.
* Kivétel történt a háttérben futó feladatot.

Minden kivétel *kezelt* alkalmazás továbbra is szeretné manuálisan követhető nyomon. Nem kezelt kivételek rendszerint származó tartományvezérlők 500-as "Belső kiszolgálóhiba" válasz eredményez. Ha ilyen válasz manuálisan kezelt kivétel (vagy egyáltalán nincs kivétel) rendszer nyomon követi a megfelelő – kéréstelemetria `ResultCode` 500, azonban a Application Insights SDK nem tudja nyomon követni a megfelelő kivétel.

### <a name="prior-versions-support"></a>A korábbi verziói támogatják az
Ha MVC 4 (és az előzetes) Application Insights webes SDK 2.5 (és az előzetes) használ, tekintse meg az alábbi példák a kivételek nyomon követésére.

Ha a [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) konfiguráció `Off`, akkor kivételeket elérhető lesz a [HTTP-modulja](https://msdn.microsoft.com/library/ms178468.aspx) gyűjtéséhez. Azonban ha `RemoteOnly` (alapértelmezett), vagy `On`, akkor a kivétel törlődik, és nem érhető el az Application Insights segítségével automatikusan begyűjtik a rendszer. Ezt úgy javíthatja ki, amely felülbírálásával a [System.Web.Mvc.HandleErrorAttribute osztály](http://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx), és a felülbírált osztály alkalmazása, ahogy az alábbi különböző MVC verzióihoz ([github forrás](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

```csharp
    using System;
    using System.Web.Mvc;
    using Microsoft.ApplicationInsights;

    namespace MVC2App.Controllers
    {
      [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, Inherited = true, AllowMultiple = true)]
      public class AiHandleErrorAttribute : HandleErrorAttribute
      {
        public override void OnException(ExceptionContext filterContext)
        {
            if (filterContext != null && filterContext.HttpContext != null && filterContext.Exception != null)
            {
                //If customError is Off, then AI HTTPModule will report the exception
                if (filterContext.HttpContext.IsCustomErrorEnabled)
                {   //or reuse instance (recommended!). see note above  
                    var ai = new TelemetryClient();
                    ai.TrackException(filterContext.Exception);
                }
            }
            base.OnException(filterContext);
        }
      }
    }
```

#### <a name="mvc-2"></a>MVC 2
A HandleError attribútum cserélje le a tartományvezérlőket az új attribútumot.

```csharp
    namespace MVC2App.Controllers
    {
       [AiHandleError]
       public class HomeController : Controller
       {
    ...
```

[minta](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Regisztrálni `AiHandleErrorAttribute` Global.asax.cs globális szűrőként:

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[minta](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
FilterConfig.cs globális szűrőként regisztrálja a AiHandleErrorAttribute:

```csharp
    public class FilterConfig
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
        // Default replaced with the override to track unhandled exceptions
        filters.Add(new AiHandleErrorAttribute());
      }
    }
```

[minta](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Webes API
Application Insights webes SDK 2.6-os verziótól kezdve (beta3 és újabb verziók), az Application Insights gyűjti nem kezelt kivételek száma a vezérlő metódusokhoz automatikusan WebAPI 2 + a. Ha korábban hozzáadott egyéni kezelő ilyen kivételek (a következő példákban leírt) nyomon követésére, eltávolíthatja, megakadályozhatja, hogy az kivételek dupla követését.

Nincsenek olyan esetek számát, amelyet a kivételszűrők nem tudja kezelni. Példa:

* A vezérlő konstruktorok kivételek.
* Az üzenet kezelők kivételek.
* Útválasztás során okozott kivételeket.
* Válasz a tartalom szerializálás során okozott kivételeket.
* Kivétel történt az alkalmazás indítás folyamán.
* Kivétel történt a háttérben futó feladatot.

Minden kivétel *kezelt* alkalmazás továbbra is szeretné manuálisan követhető nyomon. Nem kezelt kivételek rendszerint származó tartományvezérlők 500-as "Belső kiszolgálóhiba" válasz eredményez. Ha ilyen válasz manuálisan kezelt kivétel (vagy egyáltalán nincs kivétel) rendszer nyomon követi a megfelelő kérést a telemetriai adatok `ResultCode` 500, azonban a Application Insights SDK nem tudja nyomon követni a megfelelő kivétel.

### <a name="prior-versions-support"></a>A korábbi verziói támogatják az
Ha WebAPI 1 (és az előzetes) Application Insights webes SDK 2.5 (és az előzetes) használ, tekintse meg az alábbi példák a kivételek nyomon követésére.

#### <a name="web-api-1x"></a>Webes API-t 1.x
Override System.Web.Http.Filters.ExceptionFilterAttribute:

```csharp
    using System.Web.Http.Filters;
    using Microsoft.ApplicationInsights;

    namespace WebAPI.App_Start
    {
      public class AiExceptionFilterAttribute : ExceptionFilterAttribute
      {
        public override void OnException(HttpActionExecutedContext actionExecutedContext)
        {
            if (actionExecutedContext != null && actionExecutedContext.Exception != null)
            {  //or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(actionExecutedContext.Exception);    
            }
            base.OnException(actionExecutedContext);
        }
      }
    }
```

Felülbírált attribútum hozzáadása adott tartományvezérlők, vagy vegye fel a globális szűrőkonfigurációt a register osztályban:

```csharp
    using System.Web.Http;
    using WebApi1.x.App_Start;

    namespace WebApi1.x
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            config.Routes.MapHttpRoute(name: "DefaultApi", routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional });
            ...
            config.EnableSystemDiagnosticsTracing();

            // Capture exceptions for Application Insights:
            config.Filters.Add(new AiExceptionFilterAttribute());
        }
      }
    }
```

[minta](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Web API 2.x
Iexceptionlogger felület egy megvalósításának hozzáadása:

```csharp
    using System.Web.Http.ExceptionHandling;
    using Microsoft.ApplicationInsights;

    namespace ProductsAppPureWebAPI.App_Start
    {
      public class AiExceptionLogger : ExceptionLogger
      {
        public override void Log(ExceptionLoggerContext context)
        {
            if (context !=null && context.Exception != null)
            {//or reuse instance (recommended!). see note above
                var ai = new TelemetryClient();
                ai.TrackException(context.Exception);
            }
            base.Log(context);
        }
      }
    }
```

Adja hozzá a register-szolgáltatásaira ezt:

```csharp
    using System.Web.Http;
    using System.Web.Http.ExceptionHandling;
    using ProductsAppPureWebAPI.App_Start;

    namespace WebApi2WithMVC
    {
      public static class WebApiConfig
      {
        public static void Register(HttpConfiguration config)
        {
            // Web API configuration and services

            // Web API routes
            config.MapHttpAttributeRoutes();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
            config.Services.Add(typeof(IExceptionLogger), new AiExceptionLogger());
        }
      }
     }
```

[minta](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Alternatív megoldásként sikerült:

1. Cserélje le a csak ExceptionHandler IExceptionHandler egyéni végrehajtását. Csak ezt nevezik, amikor a keretrendszer továbbra is képes, mely válaszüzenetet küldeni (nem amikor a kapcsolat megszakadt példányhoz) kiválasztása
2. Kivétel szűrők (leírtak a Web API 1.x tartományvezérlőkön a fenti szakaszban) - hívása nem minden esetben.

## <a name="wcf"></a>WCF
Adjon hozzá egy osztály, amely kibővíti az attribútumot, és megvalósítja az IErrorHandler és az IServiceBehavior.

```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.ServiceModel.Description;
    using System.ServiceModel.Dispatcher;
    using System.Web;
    using Microsoft.ApplicationInsights;

    namespace WcfService4.ErrorHandling
    {
      public class AiLogExceptionAttribute : Attribute, IErrorHandler, IServiceBehavior
      {
        public void AddBindingParameters(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase,
            System.Collections.ObjectModel.Collection<ServiceEndpoint> endpoints,
            System.ServiceModel.Channels.BindingParameterCollection bindingParameters)
        {
        }

        public void ApplyDispatchBehavior(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
            foreach (ChannelDispatcher disp in serviceHostBase.ChannelDispatchers)
            {
                disp.ErrorHandlers.Add(this);
            }
        }

        public void Validate(ServiceDescription serviceDescription,
            System.ServiceModel.ServiceHostBase serviceHostBase)
        {
        }

        bool IErrorHandler.HandleError(Exception error)
        {//or reuse instance (recommended!). see note above
            var ai = new TelemetryClient();

            ai.TrackException(error);
            return false;
        }

        void IErrorHandler.ProvideFault(Exception error,
            System.ServiceModel.Channels.MessageVersion version,
            ref System.ServiceModel.Channels.Message fault)
        {
        }
      }
    }

Add the attribute to the service implementations:

    namespace WcfService4
    {
        [AiLogException]
        public class Service1 : IService1
        {
         ...
```

[minta](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Kivétel teljesítményszámlálói
Ha rendelkezik [az Application Insights-ügynök telepítése](app-insights-monitor-performance-live-website-now.md) a kiszolgálón, és a kivételeket, .NET mérhető diagram kaphat. Ez magában foglalja a kezelt, mind a nem kezelt .NET-kivételek.

Nyissa meg a metrika Explorer panelt, új diagram hozzáadása, és válassza ki **kivétel arány**, a teljesítményszámlálók felsorolt.

A .NET-keretrendszer sebessége alapján kiszámítja kivételek száma számbavételi időközönként történik, és elosztja a időköz hosszát.

Ez eltér a "Kivételek" száma az Application Insights portáljáról TrackException jelentések számbavételi számítja ki. A mintavételi időköze különböző, és az SDK nem küldje TrackException jelentések összes kezelt és kezeletlen kivételek.

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player] 

## <a name="next-steps"></a>További lépések
* [REST, SQL és más függőségek hívásainak figyelése](app-insights-asp-net-dependencies.md)
* [Lapbetöltési idők, a böngésző kivételeket és a AJAX-hívások figyelése](app-insights-javascript.md)
* [A figyelő teljesítményszámlálói](app-insights-performance-counters.md)
