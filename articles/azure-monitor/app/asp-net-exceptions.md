---
title: Hibák és kivételek diagnosztizálása az Azure Application Insights szolgáltatással
description: A ASP.NET alkalmazások kivételeit és a kérelemtelemetriai adatokat rögzít.
ms.topic: conceptual
ms.date: 07/11/2019
ms.openlocfilehash: 9f24f09e7d2ef0a3e5f3a8f6546a9115118473ab
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892342"
---
# <a name="diagnose-exceptions-in-your-web-apps-with-application-insights"></a>Webalkalmazások kivételeinek diagnosztizálása az Application Insightsszal
Az élő webalkalmazásban lévő kivételeket az [Application Insights](../../azure-monitor/app/app-insights-overview.md)jelenti. A sikertelen kérelmeket az ügyfélen és a kiszolgálón lévő kivételekkel és egyéb eseményekkel korrelálhatja, így gyorsan diagnosztizálhatja az okokat.

## <a name="set-up-exception-reporting"></a>Kivételjelentés beállítása
* Kivételek jelentése a kiszolgálóalkalmazásból:
  * Azure-webalkalmazások: Az [Application Insights-bővítmény hozzáadása](../../azure-monitor/app/azure-web-apps.md)
  * Az Azure VM és az Azure virtuálisgép-méretezési készletIIS-üzemeltetésű alkalmazások: Az [alkalmazásfigyelési bővítmény](../../azure-monitor/app/azure-vm-vmss-apps.md) hozzáadása
  * Az [Application Insights SDK](../../azure-monitor/app/asp-net.md) telepítése az alkalmazáskódba, vagy
  * IIS webkiszolgálók: [Alkalmazáselemzési ügynök futtatása](../../azure-monitor/app/monitor-performance-live-website-now.md); Vagy
  * Java webalkalmazások: A [Java-ügynök](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) engedélyezése
* Telepítse a [JavaScript-kódrészletet](../../azure-monitor/app/javascript.md) a weblapokra, hogy elkapja a böngészőkivételeket.
* Egyes alkalmazáskeretekben vagy bizonyos beállításokkal további lépéseket kell tennie a további kivételek elfogásához:
  * [Webes űrlapok](#web-forms)
  * [MVC](#mvc)
  * [Webes API 1.*](#web-api-1x)
  * [Webes API 2.*](#web-api-2x)
  * [WCF](#wcf)

  Ez a cikk kifejezetten a . A . Ha rendelkezik .NET Core alkalmazással, olvassa el a [.NET Core SDK dokumentációját.](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)

## <a name="diagnosing-exceptions-using-visual-studio"></a>Kivételek diagnosztizálása a Visual Studio használatával
Nyissa meg az alkalmazásmegoldást a Visual Studióban a hibakereséshez.

Futtassa az alkalmazást a kiszolgálón vagy a fejlesztőgépen az F5 használatával.

Nyissa meg az Application Insights Search ablakot a Visual Studióban, és állítsa be úgy, hogy az alkalmazáseseményeit jelenítse meg. Hibakeresés közben ezt csak az Application Insights gombra kattintva teheti meg.

![Kattintson a jobb gombbal a projektre, és válassza az Application Insights, Open parancsot.](./media/asp-net-exceptions/34.png)

Figyelje meg, hogy szűrheti a jelentést, hogy csak kivételeket jelenítsen meg.

*Nincs kivétel mutatja? Lásd: [Kivételek rögzítése](#exceptions).*

Kattintson egy kivételjelentésre a veremnyomkövetés megjelenítéséhez.
Kattintson egy sorhivatkozásra a veremnyomkövetésben a megfelelő kódfájl megnyitásához.

A kódban figyelje meg, hogy a CodeLens a kivételek adatait jeleníti meg:

![CodeLens értesítés a kivételekről.](./media/asp-net-exceptions/35.png)

## <a name="diagnosing-failures-using-the-azure-portal"></a>Hibák diagnosztizálása az Azure Portal használatával
Az Application Insights egy válogatott APM-élményt nyújt, amely segít a figyelt alkalmazások hibáinak diagnosztizálásában. A kezdéshez kattintson a Hibák lehetőségre az Application Insights erőforrás menüjében található Vizsgálat szakaszban.
Meg kell jelennie egy teljes képernyős nézet, amely megmutatja a kérelmek hibaaránytrendeket, hányuk nem sikerült, és hány felhasználó érintett. A jobb oldalon a kiválasztott sikertelen műveletre jellemző leghasznosabb felosztások láthatók, beleértve az első három válaszkódot, a három legfontosabb kivételtípust és az első három sikertelen függőségi típust.

![Hibák osztályozási nézete (műveletek lap)](./media/asp-net-exceptions/failures0719.png)

Egyetlen kattintással áttekintheti a műveletek minden egyes részhalmazának reprezentatív mintáit. Különösen a kivételek diagnosztizálásához kattintson egy adott kivétel számára a végpontok utáni tranzakciók részletei lapon, például erre:

![Végpontok között a tranzakciók részletei lap](./media/asp-net-exceptions/end-to-end.png)

**Másik lehetőségként ahelyett,** hogy egy adott sikertelen művelet kivételeit vizsgálna, a kivételek általános nézetéből indulhat, ha a kivételek lapra vált a tetején. Itt láthatja a figyelt alkalmazáshoz gyűjtött összes kivételt.

*Nincs kivétel mutatja? Lásd: [Kivételek rögzítése](#exceptions).*


## <a name="custom-tracing-and-log-data"></a>Egyéni nyomkövetési és naplóadatok
Az alkalmazásra vonatkozó diagnosztikai adatok levételéhez beszúrhat kódot a saját telemetriai adatok küldéséhez. Ez a kérés, az oldalnézet és más automatikusan gyűjtött adatok mellett a diagnosztikai keresésben is megjelenik.

Erre számos lehetősége van:

* [A TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) általában használati minták figyelésére szolgál, de az általa küldött adatok az Egyéni események csoportban is megjelennek a diagnosztikai keresésben. Az események neve meg van nevezve, és karakterlánc-tulajdonságokat és numerikus mutatókat hordozhatnak, amelyeken [szűrheti a diagnosztikai kereséseket.](../../azure-monitor/app/diagnostic-search.md)
* [TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) lehetővé teszi, hogy küldjön hosszabb adatokat, mint a POST információkat.
* [A TrackException()](#exceptions) veremnyomkövetéseket küld. [További információk a kivételekről](#exceptions).
* Ha már használ naplózási keretrendszert, például a Log4Net vagy az NLog, [rögzítheti ezeket a naplókat,](asp-net-trace-logs.md) és megtekintheti őket a diagnosztikai keresésben a kérelem- és kivételadatok mellett.

Az események megtekintéséhez nyissa meg a [Keresés](../../azure-monitor/app/diagnostic-search.md) menüt a bal oldali menüből, válassza az Eseménytípusok legördülő **menüt,** majd az Egyéni esemény, a Nyomon követés vagy a Kivétel lehetőséget.

![Részletezés](./media/asp-net-exceptions/customevents.png)

> [!NOTE]
> Ha az alkalmazása sok telemetriát hoz létre, az adaptív mintavételezési modul automatikusan csökkenti a portálra küldött mennyiséget, és csupán az eseményeket megjelenítő töredékeket küld. Az ugyanazon művelet részét játszó eseményeket csoportként kijelöli vagy nem jelöli ki, így navigálhat a kapcsolódó események között. [További információ a mintavételről.](../../azure-monitor/app/sampling.md)
>
>

### <a name="how-to-see-request-post-data"></a>Hogyan lehet látni a kérelem POST adatok
A kérelem részletei nem tartalmazzák az alkalmazásnak postahívásban küldött adatokat. Az adatok jelentése:

* [Telepítse az SDK-t](../../azure-monitor/app/asp-net.md) az alkalmazásprojektbe.
* A [Microsoft.ApplicationInsights.TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)hívásához szúrjon be kódot az alkalmazásba. Küldje el a POST adatokat az üzenet paraméterben. A megengedett méret korlátozva van, ezért csak az alapvető adatokat kell elküldenie.
* Sikertelen kérelem vizsgálatakor keresse meg a kapcsolódó nyomkövetéseket.

## <a name="capturing-exceptions-and-related-diagnostic-data"></a><a name="exceptions"></a>Kivételek és kapcsolódó diagnosztikai adatok rögzítése
Először nem fogja látni a portálon az összes kivételt, amelyek hibákat okoznak az alkalmazásban. A böngészőkivételek megjelennek (ha a Weboldalakon a [JavaScript SDK-t](../../azure-monitor/app/javascript.md) használja). De a legtöbb szerver kivételek fogott IIS és meg kell írni egy kis kódot látni őket.

A következőket teheti:

* **Explicit módon naplózza a kivételeket** úgy, hogy kódot szúr be a kivételkezelőkbe a kivételek jelentéséhez.
* **A kivételek automatikus rögzítése** a ASP.NET keretrendszer konfigurálásával. A szükséges kiegészítések különbözőek a különböző típusú keretek esetében.

## <a name="reporting-exceptions-explicitly"></a>Kivételek kifejezett jelentése
A legegyszerűbb módja a TrackException() hívásbeszúrása egy kivételkezelőbe.

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

A tulajdonságok és a mérések paraméterei nem kötelezőek, de hasznosak a [szűréshez és](../../azure-monitor/app/diagnostic-search.md) további információk hozzáadásához. Ha például van egy olyan alkalmazásod, amely több játékot is képes futtatni, megtalálhatja az adott játékhoz kapcsolódó összes kivételjelentést. Az egyes szótárakba annyi elemet adhat hozzá, amennyit csak szeretne.

## <a name="browser-exceptions"></a>Böngészőkivételek
A legtöbb böngészőkivételt jelentia késelődés.

Ha a weblap tartalomkézbesítési hálózatokból vagy más tartományokból származó parancsfájlokat tartalmaz, győződjön meg arról, hogy a parancsfájlcímke rendelkezik az attribútummal, ```crossorigin="anonymous"```és hogy a kiszolgáló [CORS-fejléceket](https://enable-cors.org/)küld. Ez lehetővé teszi, hogy egy verem nyomkövetés és részletesen a kezeletlen JavaScript kivételek ezekből az erőforrásokból.

## <a name="reuse-your-telemetry-client"></a>A telemetriai ügyfél újrafelhasználása

> [!NOTE]
> TelemettryClient ajánlott példányosegyszer, és újra használni az alkalmazás teljes élettartama alatt.

Az alábbiakban egy példa segítségével TelemettryClient helyesen.

```csharp
public class GoodController : ApiController
{
    // OK
    private static readonly TelemetryClient telemetryClient;

    static GoodController()
    {
        telemetryClient = new TelemetryClient();
    }
}
```


## <a name="web-forms"></a>Webes űrlapok
Webes űrlapok esetén a HTTP-modul képes lesz összegyűjteni a kivételeket, ha nincsenek átirányítások konfigurálva CustomErrors.

Ha azonban aktív átirányításokkal rendelkezik, adja hozzá a következő sorokat a Application_Error függvényhez Global.asax.cs. (Ha még nem rendelkezik Global.asax fájllal, adjon hozzá.)

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
Az Application Insights Web SDK 2.6-os verziójával (beta3 és újabb verzió) kezdve az Application Insights automatikusan gyűjti az MVC 5+ vezérlők metódusaiban okozott kezeletlen kivételeket. Ha korábban egyéni kezelőt adott hozzá az ilyen kivételek nyomon követéséhez (a következő példákban leírtak szerint), eltávolíthatja azt, hogy megakadályozza a kivételek kettős nyomon követését.

Számos olyan eset van, amelyet a kivételszűrők nem tudnak kezelni. Például:

* A vezérlőkonstruktorokból kidobott kivételek.
* Az üzenetkezelők ből kidobott kivételek.
* Az útválasztás során kidobott kivételek.
* A választartalom szerializálása során fellépő kivételek.
* Az alkalmazás indításakor kidobott kivétel.
* A háttérfeladatokban okozott kivétel.

Az alkalmazás által kezelt összes *kivételt* manuálisan kell nyomon követni.
A vezérlőkből származó nem kezelt kivételek általában 500 "Belső kiszolgálóhiba" választ eredményeznek. Ha az ilyen válasz manuálisan jön létre a kezelt kivétel (vagy egyáltalán nem kivétel) `ResultCode` eredményeként nyomon követi a megfelelő kérelem telemetriai 500, azonban az Application Insights SDK nem tudja nyomon követni a megfelelő kivétel.

### <a name="prior-versions-support"></a>Korábbi verziók támogatása
Ha az Application Insights Web SDK 2.5 (és korábbi) MVC 4 (és korábbi) használatát használja, tekintse meg az alábbi példákat a kivételek nyomon követéséhez.

Ha a [CustomErrors](https://msdn.microsoft.com/library/h0hfz6fc.aspx) konfigurációja a `Off`, majd a [HTTP-modul](https://msdn.microsoft.com/library/ms178468.aspx) számára kivételeket fognak gyűjteni. Azonban ha ez `RemoteOnly` (alapértelmezett), `On`vagy , majd a kivétel törlődik, és nem érhető el az Application Insights automatikusan gyűjteni. Ezt úgy javíthatja ki, hogy felülbírálja a [System.Web.Mvc.HandleErrorAttribute osztályt](https://msdn.microsoft.com/library/system.web.mvc.handleerrorattribute.aspx), és alkalmazza a felülbírált osztályt az alábbi különböző MVC verzióknál ([GitHub-forrás):](https://github.com/AppInsightsSamples/Mvc2UnhandledExceptions/blob/master/MVC2App/Controllers/AiHandleErrorAttribute.cs)

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
Cserélje le a HandleError attribútumot az új attribútumra a vezérlőkben.

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
Regisztráljon `AiHandleErrorAttribute` globális szűrőként a Global.asax.cs:

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
Regisztrálja az AiHandleErrorAttribute attribútumot globális szűrőként FilterConfig.cs:

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
Az Application Insights Web SDK 2.6-os (beta3 és újabb) verziójával kezdve az Application Insights automatikusan gyűjti a WebAPI 2+ vezérlőmetódusaiban okozott kezeletlen kivételeket. Ha korábban egyéni kezelőt adott hozzá az ilyen kivételek nyomon követéséhez (a következő példákban leírtak szerint), eltávolíthatja azt, hogy megakadályozza a kivételek kettős nyomon követését.

Számos olyan eset van, amelyet a kivételszűrők nem tudnak kezelni. Például:

* A vezérlőkonstruktorokból kidobott kivételek.
* Az üzenetkezelők ből kidobott kivételek.
* Az útválasztás során kidobott kivételek.
* A választartalom szerializálása során fellépő kivételek.
* Az alkalmazás indításakor kidobott kivétel.
* A háttérfeladatokban okozott kivétel.

Az alkalmazás által kezelt összes *kivételt* manuálisan kell nyomon követni.
A vezérlőkből származó nem kezelt kivételek általában 500 "Belső kiszolgálóhiba" választ eredményeznek. Ha az ilyen válasz manuálisan jön létre a kezelt kivétel (vagy egyáltalán nem kivétel) eredményeként követi nyomon a megfelelő kérelem telemetriai `ResultCode` 500, azonban az Application Insights SDK nem tudja nyomon követni a megfelelő kivétel.

### <a name="prior-versions-support"></a>Korábbi verziók támogatása
Ha az Application Insights Web SDK 2.5 (és korábbi) WebAPI 1 (és korábbi) használatát használja, tekintse meg az alábbi példákat a kivételek nyomon követéséhez.

#### <a name="web-api-1x"></a>Webes API 1.x
A System.Web.http.filters.ExceptionFilterAttribute attribútum felülbírálása:

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

Ezt a felülbírált attribútumot hozzáadhatja adott vezérlőkhöz, vagy hozzáadhatja a WebApiConfig osztály globális szűrőkonfigurációjához:

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
Az IExceptionLogger implementációjának hozzáadása:

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

Adja hozzá ezt a WebApiConfig szolgáltatásaihoz:

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

Alternatívaként a következőket teheti:

1. Cserélje le az egyetlen ExceptionHandlert az IExceptionHandler egyéni implementációjára. Ez csak akkor van meghívva, ha a keretrendszer még mindig kiválaszthatja, hogy melyik válaszüzenetet küldje el (nem akkor, ha például a kapcsolat megszakad)
2. Kivételszűrők (a fenti Web API 1.x vezérlők szakaszában leírtak szerint) - nem minden esetben hívva.

## <a name="wcf"></a>WCF
Adjon hozzá egy osztályt, amely kiterjeszti az Attribútumot, és megvalósítja az IErrorHandler és az IServiceBehavior metódust.

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

## <a name="exception-performance-counters"></a>Kivétel teljesítményszámlálói
Ha [telepítette az Application Insights Agent-et a](../../azure-monitor/app/monitor-performance-live-website-now.md) kiszolgálón, a .NET-tel mérve leképezheti a kivételek arányának diagramját. Ez magában foglalja a kezelt és a nem kezelt .NET kivételeket is.

Nyisson meg egy Mérőszám-kezelő lapot, vegyen fel egy új diagramot, és válassza **a Kivételi arány**lehetőséget a Teljesítményszámlálók csoportban.

A .

Ez eltér az Application Insights portál által kiszámított "Kivételek" száma trackexception jelentések. A mintavételi időközök eltérőek, és az SDK nem küld TrackException jelentéseket az összes kezelt és nem kezelt kivételek.

## <a name="next-steps"></a>További lépések
* [Rest, SQL és egyéb függőségek hívásainak figyelése](../../azure-monitor/app/asp-net-dependencies.md)
* [Az oldal betöltési idejének, a böngészőkivételeknek és az AJAX-hívásoknak a figyelése](../../azure-monitor/app/javascript.md)
* [Teljesítményszámlálók figyelése](../../azure-monitor/app/performance-counters.md)
