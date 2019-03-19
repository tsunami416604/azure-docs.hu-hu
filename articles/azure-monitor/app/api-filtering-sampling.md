---
title: Szűrése és előfeldolgozása a az Azure Application Insights SDK |} A Microsoft Docs
description: Az írási Telemetriai processzorok és az SDK Telemetriát inicializálók szűrését és tulajdonságokat adhat az adatokat, a telemetria az Application Insights portálon történő elküldése előtt.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 38a9e454-43d5-4dba-a0f0-bd7cd75fb97b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: 1b55a2b053b86d3260fdca201357445d2556c444
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082466"
---
# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Az Application Insights SDK-t a telemetria szűrése és előfeldolgozása


Írhat, és hogyan telemetriai adatokat rögzített, és az Application Insights szolgáltatás való továbbítás előtt feldolgozott testre szabhatja az Application Insights SDK beépülő modulok beállítása.

* [Mintavételi](../../azure-monitor/app/sampling.md) telemetriai adatok mennyisége csökkenti a statisztikákat befolyásolása nélkül. Tartja a egymáshoz kapcsolódó adatpontok, hogy a probléma diagnosztizálásakor közöttük navigálhat. A portálon az érintett teljes kompenzálják a mintavételi és szorzata.
* Szűrés Telemetriai processzorral [ASP.NET](#filtering) vagy [Java](../../azure-monitor/app/java-filter-telemetry.md) kiválaszthat vagy az SDK-ban a telemetria módosítása előtt a kiszolgáló felé. Például akkor csökkentheti a telemetriai adatok mennyisége kérelmek kizárása a robotok által. De szűrése egy több alapszintű megközelítést, mint a mintavételi forgalom csökkentésére. Lehetővé teszi a mi továbbított jobban szabályozhatja, de érdemes figyelembe vennie, hogy befolyásolja a statisztikák – például, ha kiszűr minden sikeres kérések rendelkezik.
* [Telemetria inicializálók tulajdonságok hozzáadása](#add-properties) , bármely az alkalmazásból, többek között a globális modulok telemetriát küldött telemetriát. Például hozzáadhat kiszámított értékek; vagy a portálon az adatok szűréséhez verziószáma.
* [Az SDK API-t](../../azure-monitor/app/api-custom-events-metrics.md) egyéni eseményeket és mérőszámokat küldésére használják.

Előkészületek:

* Telepítse az Application Insights [SDK for ASP.NET](../../azure-monitor/app/asp-net.md) vagy [SDK Java](../../azure-monitor/app/java-get-started.md) az alkalmazásban.

<a name="filtering"></a>

## <a name="filtering-itelemetryprocessor"></a>Szűrés: ITelemetryProcessor
Ez a technológia biztosítja több közvetlen ellenőrzése alatt mi tartalmaz, vagy a telemetria-adatfolyam zárva. Ezzel együtt mintavételezése esetén, vagy külön-külön.

A telemetriai adatok szűréséhez, írása a telemetriai adatokat feldolgozó, és regisztrálja az SDK-val. Minden telemetriai adat halad át a processzor, és Ön kiválaszthatja, dobja el, a streamből, vagy adja hozzá tulajdonságokat. Ez magában foglalja a globális modulok, például a HTTP-kérelem gyűjtő és a függőségi gyűjtő származó telemetriai adatok, valamint a saját maga hozott telemetriai adatokat. Például szűrhet, telemetriai adatokat gyűjthessen robotok, vagy sikeres függőségi hívások érkező kérelmeket.

> [!WARNING]
> Szűrés az SDK által küldött telemetriát processzorokat használó is tevékenységdiagramon statisztikák jelenik meg a portálon, és megnehezíti az hajtsa végre a kapcsolódó elemek.
>
> Ehelyett érdemes [mintavételi](../../azure-monitor/app/sampling.md).
>
>

### <a name="create-a-telemetry-processor-c"></a>Hozzon létre egy telemetriai processzor (C#)
1. Győződjön meg arról, hogy a projekt Application Insights SDK 2.0.0-s verzió vagy újabb. Kattintson a jobb gombbal a projektre a Visual Studio Megoldáskezelőben, és válassza a NuGet-csomagok kezelése. A NuGet-Csomagkezelőt ellenőrizze a Microsoft.ApplicationInsights.Web lehetőséget.
2. Szűrő létrehozása ITelemetryProcessor megvalósításához. Ez az egy másik indításkiterjesztési pont például telemetriai modul, a telemetriainicializálót és a telemetria csatorna.

    Figyelje meg, hogy a Telemetriai processzorok feldolgozási láncolatától hozhatnak létre. Amikor példányosítania telemetriai processzorra, átadhatja egy hivatkozást a láncban következő processzor. Ha a telemetriai adatok pont számára a folyamat módszer van, annak működik, és ekkor meghívja a következő telemetriai adatokat feldolgozó a lánc.

    ```csharp

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
3. Illessze be az applicationinsights.config fájlban:

```xml

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Ez a mintavételi szűrő táblafiókhoz ahol ugyanabban a szegmensben.)

A .config fájlt a karakterlánc-értékeket az osztályban található nyilvános elnevezett tulajdonságok megadásával adhat át.

> [!WARNING]
> Körültekintően, megfelelő a neve, és bármely tulajdonságnevek az osztály és a tulajdonság neve a kódban a .config fájlban. Ha a .config fájl hivatkozik egy nem létező típusa vagy tulajdonság, az SDK előfordulhat, hogy csendes nem semmilyen telemetriai adatot küld.
>
>

**Másik lehetőségként** lehet inicializálni a kód a szűrőt. A láncban a processzor beszúrása – például a Global.asax.cs AppStart - megfelelő inicializálási osztályban:

```csharp

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

Ezt követően létrehozott TelemetryClients fogja használni a processzorok.

### <a name="example-filters"></a>Példa szűrők
#### <a name="synthetic-requests"></a>Szintetikus kéréseket
Szűrje ki a robotok és a webes tesztek. Bár a Metrikaböngésző lehetővé teszi meghatározott szintetikus források szűréséhez, ezt a beállítást az SDK szűrésével csökkentheti az adatforgalmat.

```csharp

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else:
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>A sikertelen hitelesítés
Szűrje a kérések a "401-es" választ ki.

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

#### <a name="filter-out-fast-remote-dependency-calls"></a>Szűrje ki a gyors távoli függőségi hívások
Ha csak szeretné, amelyek lassú hívások diagnosztizálása, kiszűrhetők a gyorsan azokat.

> [!NOTE]
> Ez lesz döntés a statisztikákat, akkor jelenik meg a portálon. A függőségi diagram fog megjelenni, ha a függőségi hívások összes hibák.
>
>

```csharp

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
[Ebben a blogbejegyzésben](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) egy projektet a függőségi problémák diagnosztikája elküldésével automatikusan rendszeres pingelésre függőségek ismerteti.


<a name="add-properties"></a>

## <a name="add-properties-itelemetryinitializer"></a>Adja hozzá a tulajdonságai: ITelemetryInitializer
Telemetria inicializálók használatával küldött összes telemetriai adat; a globális tulajdonságok meghatározása és a normál telemetriai modulok kiválasztott viselkedés felülbírálásához.

Az Application Insights Web csomag például HTTP-kérésekkel kapcsolatos telemetriai adatokat gyűjt. Alapértelmezés szerint azt megőrzendő tartalomként jelöli meg bármely kérelem válaszkód sikertelenként > = 400. Azonban ha azt szeretné kezelni a 400-as a siker, megadhat egy telemetriainicializáló, amely beállítja a sikeres tulajdonságot.

Ha megad egy telemetriainicializáló, azt nevezzük, amikor a Track*() módszerekkel nevezzük. Ez magában foglalja a normál telemetriai modulok által meghívott módszerek. Szabályok szerint ezeket a modulokat bármilyen tulajdonság, amely már be van állítva a inicializátor nem állít be.

**Az inicializáló definiálása**

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

Az applicationinsights.config fájlban:

```xml
    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/>
        ...
      </TelemetryInitializers>
    </ApplicationInsights>
```

*Másik lehetőségként* példányosítható az inicializáló a kódban, például a Global.aspx.cs osztályból:

```csharp
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Továbbiak ehhez a mintához.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>

### <a name="java-telemetry-initializers"></a>Java-telemetria inicializálók

[A Java SDK-dokumentáció](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.telemetryinitializer?view=azure-java-stable)

```Java
public interface TelemetryInitializer
{ /** Initializes properties of the specified object. * @param telemetry The {@link com.microsoft.applicationinsights.telemetry.Telemetry} to initialize. */

void initialize(Telemetry telemetry); }
```

Ezután regisztrálja az egyéni inicializáló az applicationinsights.xml fájlt.

```xml
<Add type="mypackage.MyConfigurableContextInitializer">
<Param name="some_config_property" value="some_value" />
</Add>
```

### <a name="javascript-telemetry-initializers"></a>JavaScript-telemetria inicializálók
*JavaScript*

Szúrjon be egy telemetriainicializáló közvetlenül a portáltól kapott inicializálási kód után:

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

                // To check the telemetry item�s type - for example PageView:
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

Elérhető a telemetryItem nem egyéni tulajdonságok összegzése, lásd: [Application Insights exportálása adatmodell](../../azure-monitor/app/export-data-model.md).

Tetszés szerinti számú inicializálók adhat hozzá.

## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor és ITelemetryInitializer
Mi a különbség, telemetriai processzorok és a telemetria inicializálók között?

* Vannak bizonyos átfedésben van az, hogy mit tehet velük: Tulajdonságok hozzáadása a telemetriai adatokat egyaránt használható.
* TelemetryInitializers TelemetryProcessors előtt mindig fusson.
* TelemetryProcessors lehetővé teszik a teljes mértékben lecserélheti vagy egy telemetriai elem elveti.
* TelemetryProcessors nem dolgozza fel a teljesítmény számláló telemetriát.

## <a name="troubleshooting-applicationinsightsconfig"></a>Hibaelhárítás az applicationinsights.config fájlban
* Győződjön meg arról, hogy a típus teljes neve és a szerelvény neve helyesen-e.
* Győződjön meg arról, hogy az applicationinsights.config fájlban található a kimeneti könyvtárba, és minden olyan friss módosításokat tartalmaz.

## <a name="reference-docs"></a>Segédanyagok
* [API – Áttekintés](../../azure-monitor/app/api-custom-events-metrics.md)
* [ASP.NET-referencia](https://msdn.microsoft.com/library/dn817570.aspx)

## <a name="sdk-code"></a>SDK Code
* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next"></a>Következő lépések
* [Keresési események és naplók](../../azure-monitor/app/diagnostic-search.md)
* [Mintavételezés](../../azure-monitor/app/sampling.md)
* [hibaelhárítással](../../azure-monitor/app/troubleshoot-faq.md)
