---
title: Szűrés és az Azure Application Insights SDK előfeldolgozása |} Microsoft Docs
description: Az írási Telemetriai processzorok és a telemetriai adatok inicializálók, az SDK-ban való vagy tulajdonságokat adhat az adatok az Application Insights portál telemetriai adatok elküldése előtt.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 11/23/2016
ms.author: mbullwin; borooji
ms.openlocfilehash: 06f116fc5096fe4bda9c8433c3a33726acb67eea
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Szűrés és az Application Insights SDK a telemetriai adatok előfeldolgozása


Írása, és beépülő modulokat az Application Insights SDK testreszabása hogyan telemetriai adatokat rögzíti, majd dolgozni, mielőtt továbbítja az Application Insights szolgáltatás konfigurálása.

* [A mintavételi](app-insights-sampling.md) telemetriai adatok mennyiségét csökkenti a statisztika befolyásolása nélkül. Azonos tartja kapcsolatos adatok mutat, így közöttük, ha a probléma diagnosztizálása navigálhat. A portálon a teljes számlálás szorozni, hogy a mintavételi kártalanítja.
* Szűrés Telemetriai processzorokat [ASP.NET](#filtering) vagy [Java](app-insights-java-filter-telemetry.md) lehetővé teszi, hogy válasszon, vagy módosítsa a az SDK-telemetriai adatokat a kiszolgálón való továbbítás előtt. A telemetriai adatok mennyiségének csökkentheti például kérelmek kizárja a robotokat. De szűrés több egyszerű megközelítése mint mintavételi forgalom csökkentése. Mi továbbított teljesebb körű vezérlése lehetővé teszi, de vegye figyelembe, hogy az hatással van a statisztika – például ki az összes sikeres kérelmek szűrése kell.
* [Telemetria inicializálók tulajdonságok hozzáadása](#add-properties) bármely telemetriai adatok küldése az alkalmazásból, beleértve a szabványos modulból telemetriai való. Például hozzáadhatja számított értékeket; vagy a portál adatainak szűréséhez verziószámokra.
* [Az SDK API-t](app-insights-api-custom-events-metrics.md) küldése egyéni események és metrikák használatával.

Előkészületek:

* Telepítse az Application Insights [SDK for ASP.NET](app-insights-asp-net.md) vagy [SDK Java](app-insights-java-get-started.md) az alkalmazásban.

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Szűrés: ITelemetryProcessor
Ez a módszer lehetővé teszi több közvetlen ellenőrzése alatt tartja a mi van, illetve tiltani szeretné a telemetriai adatok adatfolyamból. A mintavételi, párhuzamosan használható vagy külön-külön.

Telemetriai adatok szűrése, telemetriai processzort írása, és regisztrálja az SDK-val. A processzor végighalad az összes telemetriai adat, és dobja el, az adatfolyamból, vagy vegye fel a Tulajdonságok választhatja. Ez magában foglalja a szabványos modulból, mint a HTTP-kérelem adatgyűjtő és a függőségi adatgyűjtő telemetriai, valamint saját kezűleg írt telemetriai adatokat. Például szűrhetők telemetriai adatainak robots vagy sikeres függőségi hívások esetében érkező kérelmeket.

> [!WARNING]
> Az SDK által küldött telemetriai adatok szűrése feldolgozók segítségével döntés a statisztika, melyek megjelennek a portálon, és nehéz hajtsa végre a kapcsolódó elemek.
>
> Ehelyett érdemes [mintavételi](app-insights-sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Hozzon létre egy telemetriai processzor (C#)
1. Győződjön meg arról, hogy az Application Insights SDK célverzióját 2.0.0 verzió vagy újabb. Kattintson a jobb gombbal a projektre a Visual Studio Solution Explorerben, és válassza ki a NuGet-csomagok kezelése. A NuGet-Csomagkezelő ellenőrizze a Microsoft.ApplicationInsights.Web.
2. Szűrő létrehozásához ITelemetryProcessor megvalósításához. Ez egy másik bővítési pontot például telemetriai modul, telemetriai inicializáló és telemetriai csatorna.

    Figyelje meg, hogy a Telemetriai processzor feldolgozási láncolata összeállításához. Telemetria processzort hozható létre, ha át egy hivatkozást a következő feldolgozó a láncban. Ha telemetriai adatpont folyamat metódus számára, ne a tevékenységeket, majd majd hívja a következő Telemetriai processzor a lánc.

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {

        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return
            if (!OKtoSend(item)) { return; }
            // Modify the item if required
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }

    ```
1. Helyezze be a ApplicationInsights.config:

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Ez a szakaszában azonos ahol inicializálni a mintavételi szűrő.)

Az osztály nyilvános elnevezett tulajdonságok megadásával átadhatók karakterlánc-értékek a .config fájlból.

> [!WARNING]
> A nevét, és az osztály és a tulajdonság nevét, a kódban az .config fájl bármely tulajdonságnevek megfelelően kezeli. Ha a .config fájl egy nem létező típus vagy a tulajdonságra hivatkozik, az SDK-t is csendes nem minden telemetriai adatokat küldhet.
>
>

**Másik lehetőségként** tudja inicializálni a kódban a szűrőt. A megfelelő inicializálása az osztály - például a Global.asax.cs AppStart - a processzor beilleszteni a lánc:

```csharp

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

Ezt a pontot fogja használni a processzor után létrehozott TelemetryClients.

A következő kód bemutatja, hogyan egy telemetriai inicializáló hozzáadása az ASP.NET Core.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    var initializer = new SuccessfulDependencyFilter();
    var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();
    configuration.TelemetryInitializers.Add(initializer);
}
```

### <a name="example-filters"></a>Példa szűrők
#### <a name="synthetic-requests"></a>Szintetikus kérelmek
Botok és a webalkalmazás-tesztek szűrik. Bár a Metrikaböngésző felajánlja a szintetikus források szűrheti, ezt a beállítást, az SDK szűréssel csökken a forgalom.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else:
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Sikertelen hitelesítési
Szűrheti kérések "401-es" választ.

```csharp

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain:
        return;
    }
    // Send everything else:
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Kiszűrhetők a gyorsan távoli függőségi hívások esetében
Ha szeretné, amelyek lassú hívások diagnosztizálása, kiszűrhetők a gyorsan megfelelően.

> [!NOTE]
> Ez fogja döntés a statisztika, megjelenik a portálon. A függőség diagram, ha a függőségi hívások esetében minden hibák fog kinézni.
>
>

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;

    if (request != null && request.Duration.TotalMilliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### <a name="diagnose-dependency-issues"></a>Függőségi problémák diagnosztikája
[Ebben a blogban](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) függőségi eseményadatokat elküldésével automatikusan rendszeres pingelésre függőségek projekt ismerteti.


<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>Adja hozzá a tulajdonságok: ITelemetryInitializer
Telemetria inicializálók segítségével az összes telemetriai adat; küldött általános tulajdonságainak megadása és a szabványos telemetriai modulok kijelölt működés felülbírálásához.

Az Application Insights webes csomag például HTTP-kérelmekre vonatkozó telemetriai adatokat gyűjt. Alapértelmezés szerint azt észleli, ha bármely kérelem válaszkód sikertelenként > = 400. Azonban ha azt szeretné kezelni a 400 sikeres, megadhatja a telemetriai adatok inicializáló, amely beállítja a sikeres tulajdonságot.

Ha megad egy telemetriai inicializáló, nevezik Track*() módszerek meghívásakor. Ez magában foglalja a szabványos telemetriai modulok által meghívott módszerek. Konvenció ezek a modulok egyik tulajdonságnak sem, amely már be van állítva egy inicializáló által nem állít be.

**Adja meg az inicializáló**

*C#*

```csharp

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK
       * behavior of treating response codes >= 400 as failed requests
       *
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Az inicializáló betöltése**

Az ApplicationInsights.config:

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*Másik lehetőségként* az inicializáló a kód, például a Global.aspx.cs osztályból példányosítható:

```csharp
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Ez a minta több látszik.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>

### <a name="javascript-telemetry-initializers"></a>JavaScript telemetriai inicializálók
*JavaScript*

Szúrja be a telemetriai adatok inicializáló közvetlenül a portálon kapott inicializálási kód után:

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Elérhető a telemetryItem nem egyéni tulajdonság, témakör [Application Insights exportálása adatmodell](app-insights-export-data-model.md).

Tetszőleges számú inicializálók szerint adhat hozzá.

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor és ITelemetryInitializer
Mi az a telemetriai adatok processzorok és telemetriai inicializálók közötti különbség?

* Van néhány átfedéseket, mi mindent velük a: Tulajdonságok hozzáadása telemetriai egyaránt használható.
* TelemetryInitializers TelemetryProcessors előtt mindig fusson.
* TelemetryProcessors engedélyezi, hogy teljesen cserélje le, vagy vesse el a telemetriai adatok elemet.
* TelemetryProcessors teljesítmény számláló telemetriai nem feldolgozni.

## <a name="troubleshooting-applicationinsightsconfig"></a>Hibaelhárítási ApplicationInsights.config
* Győződjön meg arról, hogy a teljesen minősített típusnév és a szerelvény neve helyes.
* Győződjön meg arról, hogy az applicationinsights.config fájl a kimeneti könyvtárában, és a legutóbbi módosításokat tartalmazza.

## <a name="reference-docs"></a>Segédanyagok
* [API – Áttekintés](app-insights-api-custom-events-metrics.md)
* [ASP.NET-hivatkozás](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>SDK-kód
* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Következő lépések
* [Keresési események és a naplókat](app-insights-diagnostic-search.md)
* [Mintavételezés](app-insights-sampling.md)
* [hibaelhárítással](app-insights-troubleshoot-faq.md)
