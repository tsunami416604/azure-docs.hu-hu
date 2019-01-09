---
title: Hibák és kivételek, a web Apps alkalmazások az Azure Application insights segítségével diagnosztizálhatja |} A Microsoft Docs
description: ASP.NET-alkalmazások kérelmek telemetriai adatai mellett a kivételek rögzítése.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: d1e98390-3ce4-4d04-9351-144314a42aa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2017
ms.author: mbullwin
ms.openlocfilehash: cb32069de295b883cdc6d3a9fa495b1bea719c39
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54116986"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Az Application insights segítségével a webalkalmazások kivételeinek diagnosztizálása
Az élő webalkalmazását kivételek által jelentett [Application Insights](../../azure-monitor/app/app-insights-overview.md). Sikertelen kérelmek kapcsolhatja össze a kivételek és az ügyfél és a kiszolgáló, az eseményeket, hogy gyorsan diagnosztizálhatja a okok.

## <a name="set-up-exception-reporting"></a>Kivétel jelentéskészítés beállítása
* A kiszolgálóalkalmazás által jelentett kivételek rendelkezik:
  * Telepítés [Application Insights SDK](../../azure-monitor/app/asp-net.md) az alkalmazáskód, vagy
  * IIS-kiszolgálók: Futtatás [Application Insights-ügynökkel](../../azure-monitor/app/monitor-performance-live-website-now.md); vagy
  * Az Azure web apps: Adja hozzá a [Application Insights-bővítmény](../../azure-monitor/app/azure-web-apps.md)
  * Java-webalkalmazások: Telepítse a [Java-ügynök](../../azure-monitor/app/java-agent.md)
* Telepítse a [JavaScript-kódrészletet](../../azure-monitor/app/javascript.md) a weblapok a böngésző kivételeket.
* Egyes alkalmazás-keretrendszerek, vagy az egyes beállítások kell venni néhány további lépést több kivételeket:
  * [Web Forms keretrendszerre](#web-forms)
  * [MVC](#mvc)
  * [Web API 1.*](#web-api-1x)
  * [Web API 2.*](#web-api-2x)
  * [WCF](#wcf)

## <a name="diagnosing-exceptions-using-visual-studio"></a>A Visual Studio használatával kivételek diagnosztizálása
Nyissa meg az alkalmazás megoldás segítségével a hibakeresése a Visual studióban.

Futtassa az alkalmazást, vagy a kiszolgálón, vagy a fejlesztési számítógépén az F5 billentyűvel.

A Visual Studióban nyissa meg az Application Insights keresés ablakában, és állítsa az alkalmazásból származó események megjelenítéséhez. Hibakeresés, során csak az Application Insights gombra kattintva is ehhez.

![Kattintson a jobb gombbal a projektre, és válassza az Application Insights, nyissa meg.](./media/asp-net-exceptions/34.png)

Figyelje meg, hogy a jelentést, hogy csak kivételek szűrheti.

*Nincsenek kivételek megjelenítő? Lásd: [kivételek rögzítése](#exceptions).*

Kattintson egy kivétel jelentést, hogy annak veremkiíratási adataival.
Kattintson a megfelelő kódot fájl megnyitása a híváslánc-sor hivatkozást.

A kód figyelje meg, hogy a Codelensben a kivételek adatait jeleníti meg:

![A CodeLens értesítés a kivételeket.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Az Azure portal használatával hibák diagnosztizálása
Az Application Insights segít a figyelt alkalmazások hibáinak diagnosztizálása használt APM felülettel rendelkezik. Elindításához kattintson a vizsgálat szakaszában található Application Insights-erőforrás menü hibák esetén.
A teljes képernyős nézetben, amely bemutatja, a kérelmek, ezek közül hány sikertelen, és hány felhasználó érintett hiba arány trendjeit kell megjelennie. A jobb oldalon láthatja a leghasznosabb a kijelölt adott disztribúciók sikertelen volt a művelet, beleértve a 3 leggyakoribb válaszkódok, 3 leggyakoribb kivételtípus és felső 3 sikertelen függőségi típusnál.

![Hibák megtekintése (operations lap) osztályozása](./media/asp-net-exceptions/FailuresTriageView.png)

Egyetlen kattintással majd át is reprezentatív mintát minden ezen műveletek alkészleteiben. Különösen kivételek diagnosztizálásához, kattintson a száma, egy adott ekkor megjelenik egy kivétel részleteit tartalmazó panel, például a kivétel:

![Kivétel részleteit tartalmazó panel](./media/asp-net-exceptions/ExceptionDetailsBlade.png)

**Másik lehetőségként** helyett adott meghiúsuló műveletek kivételek megnézzük, akkor kezdhet a kivételek, átfogó képet átvált a Kivételek lapon:

![Hibák megtekintése (Kivételek lapon) osztályozása](./media/asp-net-exceptions/FailuresTriageView_Exceptions.png)

Itt láthatja a figyelt alkalmazás gyűjtött összes kivétel.

*Nincsenek kivételek megjelenítő? Lásd: [kivételek rögzítése](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Egyéni nyomkövetési és naplóadatok
Diagnosztikai adatok az alkalmazás adott lekéréséhez szúrhat be a saját telemetriai adatokat küldeni a kódot. Ez a diagnosztikai keresésben a kérést, lapmegtekintés és egyéb automatikusan gyűjtött adatok mellett jelenik meg.

Több lehetősége van:

* [A TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) általában szolgál a használati mintákat, figyelés, de az adatok is elküldi a diagnosztikai keresésben megjelenik egyéni eseményeket. Nevesített eseményeket és a karakterlánc-tulajdonságok és numerikus metrikák, amelyen is képes továbbítani [szűrése a diagnosztikai keresésben](../../azure-monitor/app/diagnostic-search.md).
* [TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) hosszabb adatok, például a POST információk elküldését teszi lehetővé.
* [TrackException()](#exceptions) veremkiíratásokat küld. [További tudnivalók a kivételek](#exceptions).
* Ha már használ egy naplózási keretrendszert, például a Log4Net, NLog, vagy, [ezeket a naplókat rögzítése](asp-net-trace-logs.md) és megtekintheti őket a kérések és kivételek adatok mellett a diagnosztikai keresésben.

Ezek az események megtekintéséhez nyissa meg a [keresési](../../azure-monitor/app/diagnostic-search.md), nyissa meg a szűrőt, és válassza a egyéni esemény, nyomkövetési vagy kivétel.

![Áthatoló részletezést](./media/asp-net-exceptions/viewCustomEvents.png)

> [!NOTE]
> Ha az alkalmazása sok telemetriát hoz létre, az adaptív mintavételezési modul automatikusan csökkenti a portálra küldött mennyiséget, és csupán az eseményeket megjelenítő töredékeket küld. Ugyanehhez a művelethez tartozó események lesz kiválasztva, vagy a jelölésük, úgy, hogy lehessen mozogni a kapcsolódó események között. [További tudnivalók a mintavételezésről.](../../azure-monitor/app/sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Kérelem POST adatainak megtekintése
A kérelem részletes adatainak az alkalmazáshoz a POST híváson a küldött adatok nem tartalmaznak. Ez az adatokat jelentette:

* [Az SDK telepítése](../../azure-monitor/app/asp-net.md) az alkalmazás-projektben.
* Szúrja be a kódot az alkalmazásban való meghívása [Microsoft.ApplicationInsights.TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace). A POST data küldenek az üzenet-paraméter. Az engedélyezett mérete korlátozva van, meg kell próbálnia az alapvető adatok küldése.
* Ha a sikertelen kérelmek vizsgálatához keresse meg a társított nyomkövetési.

![Áthatoló részletezést](./media/asp-net-exceptions/060-req-related.png)

## <a name="exceptions"></a> Kivételek és a kapcsolódó diagnosztikai adatok rögzítése
Először meg nem jelenik meg a portálon a kivételeket, amelyek hibákat okozhat az alkalmazásban. Láthatja, hogy bármely böngészőkivételek (Ha használja a [JavaScript SDK-t](../../azure-monitor/app/javascript.md) a weblapok). De a legtöbb kiszolgálókivételek az IIS által észlelt, és a egy kis megtekintheti őket a kód írása rendelkezik.

A következőket teheti:

* **Explicit módon a kivételek naplózásához** kód beszúrásával a kivételkezelők a kivételeket.
* **Automatikus kivételek rögzítése** az ASP.NET keretrendszer konfigurálásával. A szükséges kiegészítés különböznek a különböző típusú keretrendszer.

## <a name="reporting-exceptions-explicitly"></a>Explicit módon Reporting kivételek
A legegyszerűbb módja, ha szúrja be a trackexception() hívásait hívást egy kivételkezelő.

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

A tulajdonságok és a mértékek paraméterek megadása nem kötelező, de hasznos [szűrési és hozzáadása](../../azure-monitor/app/diagnostic-search.md) további információt. Például ha egy alkalmazás futtatható a több játékok, található egy adott játék kapcsolódó összes kivétel jelentést. Hozzáadhat elemeket a szótárakat tetszés szerint.

## <a name="browser-exceptions"></a>Böngészőkivételek
A legtöbb böngészőkivételek jelenti.

Ha a weblap tartalmazza a parancsfájlok a tartalomkézbesítési hálózatok vagy más tartományok, győződjön meg arról, a parancsfájl címke má atribut ```crossorigin="anonymous"```, és a kiszolgáló által küldött [CORS fejlécek](https://enable-cors.org/). Ez lehetővé teszi, hogy egy hívásláncot és a részletek beolvasása nem kezelt JavaScript-kivételeknek az ezekhez az erőforrásokhoz.

## <a name="web-forms"></a>Web Forms keretrendszerre
Web Forms a HTTP-modulja fogja tudni gyűjtése a kivételeket, ha nincs konfigurálva CustomErrors átirányítások.

De ha aktív átirányítások, adja hozzá a következő sorokat Global.asax.cs Application_Error funkció. (A Global.asax fájl hozzáadása, ha még nem rendelkezik egy.)

```csharp
    void Application_Error(object sender, EventArgs e)
    {
      if (HttpContext.Current.IsCustomErrorEnabled && Server.GetLastError () != null)
      {
         var ai = new TelemetryClient(); // or re-use an existing instance

         ai.TrackException(Server.GetLastError());
      }
    }
```

## <a name="mvc"></a>MVC
Application Insights webes SDK 2.6-os verziótól kezdve (beta3 és újabb verziók), az Application Insights összegyűjti a nem kezelt kivételek automatikus kapcsolatfigyelőben az MVC 5 + tartományvezérlők módszerek. Ha korábban hozzáadott egy egyéni kezelő az ilyen kivételek (a következő példákban leírt) nyomon követésére, eltávolíthatja a kivételek dupla követési megakadályozása.

Számos esetben működőképes, amely a kivétel szűrők nem tudja kezelni. Példa:

* A vezérlő konstruktorok fellépő kivételek.
* Az üzenet kezelők fellépő kivételek.
* Útválasztás során fellépő kivételek.
* Válasz a tartalom szerializálás során fellépő kivételek.
* Kivétel történt az alkalmazás indítási során.
* Kivétel történt a háttérben futó feladatok.

Minden kivétel *kezelt* alkalmazás továbbra is nyomon kell követni manuálisan.
Általában a tartományvezérlők származó nem kezelt kivételek 500 "Belső kiszolgálóhiba" választ eredményeznek. Ha ilyen válasz manuálisan kezelt kivétel (vagy egyáltalán nincs kivétel) eredményeként jön létre, a megfelelő kérelmek telemetriai adatai, a nyomon követett `ResultCode` 500, azonban az Application Insights SDK nem tud nyomon követni a megfelelő kivétel.

### <a name="prior-versions-support"></a>Korábbi verziók támogatása
Ha MVC 4 (és az előzetes) és Application Insights webes SDK 2.5-ös (előzetes) használ, tekintse meg az alábbi példák kivételeinek követéséről.

Ha a [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) konfigurációs van `Off`, akkor kivételeket lesz elérhető a [HTTP-modulja](https://msdn.microsoft.com/library/ms178468.aspx) gyűjtéséhez. Azonban ha `RemoteOnly` (alapértelmezett), vagy `On`, akkor a kivétel lesz bejelölve, és nem érhető el az Application Insights segítségével automatikusan begyűjtik. Letiltásával megoldhatja, amely a [System.Web.Mvc.HandleErrorAttribute osztály](https://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx), és alkalmazza a felülbírált osztály, ahogy az alábbi különböző MVC-verziók ([GitHub forrás](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)):

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
Cserélje le a HandleError attribútum a tartományvezérlőket az új attribútumot.

```csharp
    namespace MVC2App.Controllers
    {
        [AiHandleError]
        public class HomeController : Controller
        {
    ...
```

[Minta](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions)

#### <a name="mvc-3"></a>MVC 3
Regisztráljon `AiHandleErrorAttribute` Global.asax.cs globális szűrőként:

```csharp
    public class MyMvcApplication : System.Web.HttpApplication
    {
      public static void RegisterGlobalFilters(GlobalFilterCollection filters)
      {
         filters.Add(new AiHandleErrorAttribute());
      }
     ...
```

[Minta](https://github.com/AppInsightsSamples/Mvc3UnhandledExceptionTelemetry)

#### <a name="mvc-4-mvc5"></a>MVC 4, MVC5
FilterConfig.cs globális szűrőként regisztrálja az AiHandleErrorAttribute:

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

[Minta](https://github.com/AppInsightsSamples/Mvc5UnhandledExceptionTelemetry)

## <a name="web-api"></a>Webes API
Application Insights webes SDK 2.6-os verziótól kezdve (beta3 és újabb verziók), az Application Insights gyűjti nem kezelt kivételek száma a vezérlő metódusokhoz automatikusan a WebAPI 2 +. Ha korábban hozzáadott egy egyéni kezelő az ilyen kivételek (a következő példákban leírt) nyomon követésére, eltávolíthatja a kivételek dupla követési megakadályozása.

Számos esetben működőképes, amely a kivétel szűrők nem tudja kezelni. Példa:

* A vezérlő konstruktorok fellépő kivételek.
* Az üzenet kezelők fellépő kivételek.
* Útválasztás során fellépő kivételek.
* Válasz a tartalom szerializálás során fellépő kivételek.
* Kivétel történt az alkalmazás indítási során.
* Kivétel történt a háttérben futó feladatok.

Minden kivétel *kezelt* alkalmazás továbbra is nyomon kell követni manuálisan.
Általában a tartományvezérlők származó nem kezelt kivételek 500 "Belső kiszolgálóhiba" választ eredményeznek. Ha ilyen válasz manuálisan kezelt kivétel (vagy egyáltalán nincs kivétel) eredményeként jön létre a megfelelő kérést a telemetriai adatok követi nyomon, `ResultCode` 500, azonban az Application Insights SDK nem tud nyomon követni a megfelelő kivétel.

### <a name="prior-versions-support"></a>Korábbi verziók támogatása
Ha WebAPI-1 (és az előzetes) és Application Insights webes SDK 2.5-ös (előzetes) használ, tekintse meg az alábbi példák kivételeinek követéséről.

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

Felülbírált attribútum hozzáadása adott tartományvezérlőket, vagy adja hozzá a register-osztályt a globális szűrő-konfigurációt:

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

[Minta](https://github.com/AppInsightsSamples/WebApi_1.x_UnhandledExceptions)

#### <a name="web-api-2x"></a>Webes API 2.x
Módszer az iexceptionlogger felület egy megvalósításának hozzáadása:

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

Adja hozzá a register szolgáltatásban:

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

[Minta](https://github.com/AppInsightsSamples/WebApi_2.x_UnhandledExceptions)

Alternatív megoldásként sikerült:

1. Cserélje le a csak ExceptionHandler IExceptionHandler egyéni megvalósítását. Csak ezt nevezik, ha a keretrendszer továbbra is képes válassza ki a válasz üzenetek küldéséhez (nem amikor a kapcsolat megszakadt például)
2. Kivétel szűrők (leírtak szerint a webes API-t 1.x tartományvezérlőkön a fenti szakaszban) – nem minden esetben meghívva.

## <a name="wcf"></a>WCF
Adjon hozzá egy osztályt, amely kibővíti az attribútum, és az alábbiaknak és az IServiceBehavior valósítja meg.

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

[Minta](https://github.com/AppInsightsSamples/WCFUnhandledExceptions)

## <a name="exception-performance-counters"></a>Kivétel teljesítményszámlálók
Ha rendelkezik [telepítve van az Application Insights-ügynökkel](../../azure-monitor/app/monitor-performance-live-website-now.md) egy grafikont a kivételek száma, a .NET-mért kap a kiszolgálón. Ez magában foglalja a kezelt és a nem kezelt .NET-kivételeket.

A Metrikaböngésző panel megnyitásához, adjon hozzá egy új diagramot, és válassza ki **kivételek sebessége**ismertetett teljesítményszámlálók alapján.

A .NET-keretrendszer alapján számítja ki a sebesség kivételek száma leltár az időközt, és elosztja az időköz hossza.

Ez eltér a "Kivétel" száma az Application Insights portálon TrackException jelentések számbavételi számolható ki. A mintavételi időköze eltérőek, és az SDK nem küld az összes TrackException jelentések kezelnek, és nem kezelt kivételeket.

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>További lépések
* [REST, SQL és más, a függőségek hívásainak figyelése](../../azure-monitor/app/asp-net-dependencies.md)
* [Megfigyelheti az oldalbetöltési időket, a böngészőkivételek és AJAX-hívások](../../azure-monitor/app/javascript.md)
* [A figyelő teljesítményszámlálók](../../azure-monitor/app/performance-counters.md)
