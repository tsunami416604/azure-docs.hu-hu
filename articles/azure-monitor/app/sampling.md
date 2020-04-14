---
title: Telemetriai mintavétel az Azure Application Insightsban | Microsoft dokumentumok
description: Hogyan őrizi meg a telemetriai adatok mennyiségét.
ms.topic: conceptual
ms.date: 01/17/2020
ms.reviewer: vitalyg
ms.custom: fasttrack-edit
ms.openlocfilehash: 5e888e0606b7a9bcd9a7a94c28455d705c5f1bec
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255481"
---
# <a name="sampling-in-application-insights"></a>Application Insights-mintavétel

A mintavételezés az [Azure Application Insights szolgáltatása.](../../azure-monitor/app/app-insights-overview.md) Ez az ajánlott módja a telemetriai forgalom, az adatköltségek és a tárolási költségek csökkentésének, miközben megőrzi az alkalmazásadatok statisztikailag helyes elemzését. Mintavételezési is segít elkerülni az Application Insights a telemetriai adatok szabályozásával. A mintavételi szűrő kiválasztja a kapcsolódó elemeket, így diagnosztikai vizsgálatok során navigálhat az elemek között.

Amikor a metrikák száma jelenik meg a portálon, azok újranormalizálódik, hogy figyelembe vegyék a mintavételi. Ezzel minimálisra csökkenti a statisztikákra gyakorolt hatást.

## <a name="brief-summary"></a>Rövid összefoglaló

* A mintavételnek három különböző típusa van: adaptív mintavétel, rögzített sebességű mintavétel és lenyelési mintavétel.
* Az adaptív mintavételezés alapértelmezés szerint engedélyezve van az Application Insights ASP.NET és ASP.NET Core Software Development Kit (SDK) legújabb verzióiban. Azt is használják az [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-overview).
* Rögzített díjú mintavételi érhető el a legújabb verzióiban az Application Insights SDK-k ASP.NET, ASP.NET Core, Java (mind az ügynök és az SDK) és a Python.
* Betöltési mintavételi működik az Application Insights szolgáltatás végpontján. Csak akkor alkalmazandó, ha nincs más mintavétel. Ha az SDK mintát vesz a telemetriai adatokat, a betöltési mintavételi le van tiltva.
* Webalkalmazások esetén, ha egyéni eseményeket naplóz, és biztosítania kell, hogy az események egy `OperationId` készlete megmaradjon vagy elvetve legyen, az eseményeknek azonos értékkel kell rendelkezniük.
* Ha Analytics-lekérdezéseket ír, figyelembe kell [vennie a mintavételezést.](../../azure-monitor/log-query/aggregations.md) Különösen a rekordok egyszerű számolása helyett `summarize sum(itemCount)`a .
* Egyes telemetriai típusok, beleértve a teljesítmény metrikák és egyéni metrikák, mindig megmaradnak, függetlenül attól, hogy a mintavételi engedélyezve van-e vagy sem.

Az alábbi táblázat összefoglalja az egyes SDK-khoz rendelkezésre álló mintavételi típusokat és az alkalmazás típusát:

| Application Insights SDK | Adaptív mintavételezés támogatott | Rögzített sebességű mintavételezés támogatott | A betöltési mintavételi támogatás |
|-|-|-|-|
| ASP.NET | [Igen (alapértelmezés szerint be)](#configuring-adaptive-sampling-for-aspnet-applications) | [Igen](#configuring-fixed-rate-sampling-for-aspnet-applications) | Csak akkor, ha nincs más mintavétel |
| ASP.NET-mag | [Igen (alapértelmezés szerint be)](#configuring-adaptive-sampling-for-aspnet-core-applications) | [Igen](#configuring-fixed-rate-sampling-for-aspnet-core-applications) | Csak akkor, ha nincs más mintavétel |
| Azure Functions | [Igen (alapértelmezés szerint be)](#configuring-adaptive-sampling-for-azure-functions) | Nem | Csak akkor, ha nincs más mintavétel |
| Java | Nem | [Igen](#configuring-fixed-rate-sampling-for-java-applications) | Csak akkor, ha nincs más mintavétel |
| Python | Nem | [Igen](#configuring-fixed-rate-sampling-for-opencensus-python-applications) | Csak akkor, ha nincs más mintavétel |
| Az összes többi | Nem | Nem | [Igen](#ingestion-sampling) |

> [!NOTE]
> Az oldal nagy részén található információk az Application Insights SDK-k aktuális verzióira vonatkoznak. Az SDK-k régebbi verzióiról [az alábbi szakaszban](#older-sdk-versions)olvashat.

## <a name="types-of-sampling"></a>A mintavétel típusai

Három különböző mintavételi módszer létezik:

* **Adaptív mintavételezés** automatikusan beállítja az SDK-ból küldött telemetriai adatok a ASP.NET/ASP.NET Core alkalmazásban, és az Azure Functions. Ez az alapértelmezett mintavételezés, ha a ASP.NET vagy ASP.NET Core SDK.This is the default sampling when you use the ASP.NET or ASP.NET Core SDK. Adaptív mintavételezés jelenleg csak ASP.NET kiszolgálóoldali telemetriai adatok és az Azure Functions.

* **A rögzített sebességű mintavételezés** csökkenti a ASP.NET vagy ASP.NET Core vagy Java kiszolgálóról és a felhasználók böngészőiből küldött telemetriai adatok mennyiségét. Te állítod be az árat. Az ügyfél és a kiszolgáló szinkronizálja a mintavételezést, így a Keresés ben navigálhat a kapcsolódó oldalmegtekintések és kérések között.

* **Betöltési mintavételi** történik az Application Insights szolgáltatás végpontján történik. Elveti az alkalmazásból érkező telemetriai adatok egy részét, a beállított mintavételi sebességgel. Nem csökkenti az alkalmazásból küldött telemetriai forgalmat, de segít a havi kvótán belül maradni. A betöltési mintavételfőelőnye, hogy az alkalmazás újratelepítése nélkül állíthatja be a mintavételi arányt. A betöltési mintavételezés egységesen működik minden kiszolgálóés ügyfél esetében, de nem vonatkozik, ha bármilyen más típusú mintavételműködik.

> [!IMPORTANT]
> Adaptív vagy rögzített sebességű mintavételi módszerek működése esetén a lenyelési mintavételi mód le van tiltva.

## <a name="adaptive-sampling"></a>Adaptív mintavételezés

Adaptív mintavételezés idvanát befolyásolja a webkiszolgálóalkalmazásból az Application Insights szolgáltatás végpontjára küldött telemetriai adatok mennyiségét.

> [!TIP]
> Az adaptív mintavételezés alapértelmezés szerint engedélyezve van, ha az SDK ASP.NET vagy a ASP.NET Core SDK-t használja, és alapértelmezés szerint az Azure Functions is engedélyezve van.

A hangerő automatikusan be van állítva, hogy a megadott maximális `MaxTelemetryItemsPerSecond`forgalmi sebességen belül maradjon, és a beállításon keresztül vezérelhető. Ha az alkalmazás kis mennyiségű telemetriai adatokat hoz létre, például hibakereséskor vagy alacsony használat miatt, a `MaxTelemetryItemsPerSecond`mintavételi processzor nem fogja eldobni az elemeket, amíg a kötet alatt van. A telemetriai adatok mennyiségének növekedésével a mintavételi sebesség a célmennyiség elérése érdekében módosul. A kiigazítást rendszeres időközönként újraszámítják, és a kimenő átviteli sebesség mozgóátlagán alapul.

A célkötet elérése érdekében a létrehozott telemetriai adatok egy része ellesz vetve. De más típusú mintavételi, az algoritmus megtartja a kapcsolódó telemetriai elemeket. Ha például a keresésben ellenőrzi a telemetriai adatokat, megtalálhatja egy adott kivételhez kapcsolódó kérelmet.

Metrikák száma, például a kérelem aránya és a kivétel mértéke módosul, hogy kompenzálja a mintavételi arány, így azok megjelenítése megközelítőleg helyes értékeket Metric Explorer.

### <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Adaptív mintavételezés konfigurálása ASP.NET alkalmazásokhoz

> [!NOTE]
> Ez a szakasz ASP.NET alkalmazásokra vonatkozik, nem ASP.NET Core alkalmazásokra. [Ismerje meg az adaptív mintavételezés konfigurálását ASP.NET Core alkalmazásokhoz a jelen dokumentum későbbi részében.](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications)

A [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)alkalmazásban több paramétert is `AdaptiveSamplingTelemetryProcessor` módosíthat a csomóponton. A megjelenített számok az alapértelmezett értékek:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Az adaptív algoritmus által az **egyes kiszolgálógazdaokon**kitűzött célsebesség. Ha a webalkalmazás fut sok gazdagép, csökkentse ezt az értéket, hogy az Application Insights portálon a forgalom célsebességén belül maradjon.

* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Az az időköz, amelyben a telemetriai adatok aktuális sebességét újraértékeli. A kiértékelést mozgóátlagként végezzük. Érdemes lehet lerövidíteni ezt az időközt, ha a telemetriai adatok hajlamosak a hirtelen adatlöket.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Amikor a mintavételi százalékos érték megváltozik, milyen hamar engedélyezzük a mintavételi százalék újra, hogy kevesebb adatot rögzítsen?

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Amikor a mintavételi százalékos érték megváltozik, milyen hamar ismét növelhetjük a mintavételi százalékot, hogy több adatot rögzítsünk?

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Mivel a mintavételi százalék változó, mi az a minimális érték, amelyet beállíthatunk?

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Mivel a mintavételi százalék változó, mi az a maximális érték, amelyet beállíthatunk?

* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    A mozgóátlag kiszámításakor ez határozza meg azt a súlyt, amelyet a legutóbbi értékhez kell rendelni. Használjon 1-es vagy annál kisebb értéket. A kisebb értékek miatt az algoritmus kevésbé reagál a hirtelen változásokra.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Az alkalmazás indításakor mintavételezésre kiválasztott telemetriai adatok mennyisége. Ne csökkentse ezt az értéket hibakeresés közben.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Olyan típusok pontosvesszővel tagolt listája, amelyeket nem szeretne mintavételnek alávetni. Az elismert `Dependency`típusok `Event` `Exception`a `PageView` `Request`következők: , , , , , `Trace`. A megadott típusok összes telemetriai adatai továbbításra kerülnek; a nem megadott típusokmintavételre kerülnek.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    A mintavételnek kiveendő típusok pontosvesszővel tagolt listája. Az elismert `Dependency`típusok `Event` `Exception`a `PageView` `Request`következők: , , , , , `Trace`. A megadott típusokból mintát veszanak; a többi típus összes telemetriáját mindig továbbítja.

Az adaptív **mintavételezés kikapcsolásához** távolítsa el a `AdaptiveSamplingTelemetryProcessor` csomópont(oka)t a ból. `ApplicationInsights.config`

#### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternatíva: Adaptív mintavétel konfigurálása kódban

A mintavételi paraméter fájlban `.config` való beállítása helyett programozott módon is beállíthatja ezeket az értékeket.

1. Az összes `AdaptiveSamplingTelemetryProcessor` csomópont eltávolítása a `.config` fájlból.
2. Az adaptív mintavételezés konfigurálásához használja a következő kódrészletet:

    ```csharp
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    
    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([További információ a telemetriai processzorokról](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

Az egyes telemetriai típusok mintavételi sebességét külön-külön is módosíthatja, vagy akár ki is zárhat bizonyos típusokat a mintavételből:

```csharp
// The following configures adaptive sampling with 5 items per second, and also excludes Dependency telemetry from being subjected to sampling.
builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

### <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Adaptív mintavételezés konfigurálása ASP.NET Core alkalmazásokhoz

Nincs `ApplicationInsights.config` ASP.NET Core alkalmazások, így minden konfiguráció keresztül történik kódot.
Az adaptív mintavételezés alapértelmezés szerint engedélyezve van az összes ASP.NET Core alkalmazásban. Letilthatja vagy testreszabhatja a mintavételezési viselkedést.

#### <a name="turning-off-adaptive-sampling"></a>Adaptív mintavételezés kikapcsolása

Az alapértelmezett mintavételi szolgáltatás letiltható az Application `ConfigureServices`Insights `ApplicationInsightsServiceOptions` szolgáltatás `Startup.cs` hozzáadása közben a metódusban, a fájlon belüli használatával:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // ...

    var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
    aiOptions.EnableAdaptiveSampling = false;
    services.AddApplicationInsightsTelemetry(aiOptions);

    //...
}
```

A fenti kód letiltja az adaptív mintavételezést. Az alábbi lépéseket követve további testreszabási lehetőségeket adhat hozzá mintavételhez.

#### <a name="configure-sampling-settings"></a>Mintavételi beállítások konfigurálása

A mintavételi `TelemetryProcessorChainBuilder` viselkedés testreszabásához használja az alábbi kiterjesztési módszereket.

> [!IMPORTANT]
> Ha ezzel a módszerrel konfigurálja a mintavételt, kérjük, ügyeljen arra, hogy a `aiOptions.EnableAdaptiveSampling` tulajdonságot `false` a híváskor állítsa be. `AddApplicationInsightsTelemetry()`

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = configuration.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    
    builder.Build();

    // ...
}
```

### <a name="configuring-adaptive-sampling-for-azure-functions"></a>Adaptív mintavételezés konfigurálása az Azure Functions-hez

Kövesse [az ezen az oldalon](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling) található utasításokat az Azure Functionsben futó alkalmazások adaptív mintavételezésének konfigurálásához.

## <a name="fixed-rate-sampling"></a>Rögzített sebességű mintavétel

A rögzített mintavételezés csökkenti a webkiszolgálóról és a webböngészőkből küldött forgalmat. Az adaptív mintavételezéssel ellentétben csökkenti a telemetriai adatokat az Ön által meghatározott rögzített sebességgel. Rögzített díjú mintavételi áll rendelkezésre ASP.NET, ASP.NET Core, Java és Python alkalmazások.

Más mintavételi technikákhoz hasonlóan ez is megtartja a kapcsolódó elemeket. Szinkronizálja az ügyfél- és kiszolgálómintavételt is, így a kapcsolódó elemek megőrződnek – például ha megnéz egy oldalnézetet a Keresés ben, megtalálhatja a kapcsolódó kiszolgálói kérelmeket. 

A Metrika-kezelőben a díjakat, például a kérelmek és a kivételek számát megszorozza egy tényező, hogy kompenzálja a mintavételi arány, hogy azok megközelítőleg helyesek.

### <a name="configuring-fixed-rate-sampling-for-aspnet-applications"></a>Rögzített sebességű mintavételezés konfigurálása ASP.NET alkalmazásokhoz

1. **Adaptív mintavételezés letiltása:** A , [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)távolítsa el vagy véleményezze ki a `AdaptiveSamplingTelemetryProcessor` csomópontot.

    ```xml
    <TelemetryProcessors>
        <!-- Disabled adaptive sampling:
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
        </Add>
        -->
    ```

2. **Engedélyezze a rögzített sebességű mintavételi modult.** Adja hozzá ezt [`ApplicationInsights.config`](../../azure-monitor/app/configuration-with-applicationinsights-config.md)a kódrészletet a következőhöz:
   
    ```XML
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <!-- Set a percentage close to 100/N where N is an integer. -->
            <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
            <SamplingPercentage>10</SamplingPercentage>
        </Add>
    </TelemetryProcessors>
    ```

      Másik lehetőségként a mintavételi paraméter `ApplicationInsights.config` fájlban való beállítása helyett programozott módon állíthatja be ezeket az értékeket:

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

    // ...

    var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
    // For older versions of the Application Insights SDK, use the following line instead:
    // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();
    ```

    ([További információ a telemetriai processzorokról](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-for-aspnet-core-applications"></a>Rögzített sebességű mintavételezés konfigurálása ASP.NET core alkalmazásokhoz

1. **Adaptív mintavételezés letiltása**: A `ConfigureServices` módszer ben a következő vel `ApplicationInsightsServiceOptions`lehet módosításokat végrehajtani:

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        // ...

        var aiOptions = new Microsoft.ApplicationInsights.AspNetCore.Extensions.ApplicationInsightsServiceOptions();
        aiOptions.EnableAdaptiveSampling = false;
        services.AddApplicationInsightsTelemetry(aiOptions);

        //...
    }
    ```

2. **Engedélyezze a rögzített sebességű mintavételi modult.** A módszerben az `Configure` alábbi kódrészletben látható módon lehet módosításokat végrehajtani:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;
        // For older versions of the Application Insights SDK, use the following line instead:
        // var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }
    ```

### <a name="configuring-fixed-rate-sampling-for-java-applications"></a>Rögzített sebességű mintavételezés konfigurálása Java-alkalmazásokhoz

Alapértelmezés szerint nincs mintavételi engedélyezve a Java-ügynök és az SDK. Jelenleg csak a rögzített mintavételi arányt támogatja. Az adaptív mintavételezés java nem támogatott.

#### <a name="configuring-java-agent"></a>Java-ügynök konfigurálása

1. [Alkalmazásinsights-agent-3.0.0-PREVIEW.2.jar letöltése](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.2/applicationinsights-agent-3.0.0-PREVIEW.2.jar)

1. A mintavételezés engedélyezéséhez `ApplicationInsights.json` adja hozzá a következőket a fájlhoz:

```json
{
  "instrumentationSettings": {
    "preview": {
      "sampling": {
        "fixedRate": {
          "percentage": 10 //this is just an example that shows you how to enable only only 10% of transaction 
        }
      }
    }
  }
}
```

#### <a name="configuring-java-sdk"></a>A Java SDK konfigurálása

1. Töltse le és konfigurálja webalkalmazását a legújabb [Application Insights Java SDK-val.](../../azure-monitor/app/java-get-started.md)

2. **Engedélyezze a rögzített mintavételi modult** a `ApplicationInsights.xml` következő kódrészlet fájlba való hozzáadásával:

    ```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type="FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name="SamplingPercentage" value="50" />
            </Processor>
        </BuiltInProcessors>
    </TelemetryProcessors>
    ```

3. A címke alábbi címkéiben a következő címkék használatával `Processor` vehet fel `FixedRateSamplingTelemetryProcessor`vagy zárhat ki bizonyos típusú telemetriai adatokat a mintavételből:
   
    ```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
    ```

A mintavételből bevonható vagy a mintavételből kizárható `Event` `Exception`telemetriai típusok a `PageView`következők: `Dependency`, , , , , `Request`, és `Trace`.

> [!NOTE]
> A mintavételi százalékhoz olyan százalékot válasszon, amely közel 100/N, ahol N egész szám.  Jelenleg a mintavételezés nem támogatja az egyéb értékeket.

### <a name="configuring-fixed-rate-sampling-for-opencensus-python-applications"></a>Rögzített sebességű mintavételezés konfigurálása OpenCensus Python-alkalmazásokhoz

Műszer alkalmazása a legújabb [OpenCensus Azure Monitor exportőrök](../../azure-monitor/app/opencensus-python.md).

> [!NOTE]
> A mérőszámok exportőre számára nem áll rendelkezésre rögzített mintavétel. Ez azt jelenti, hogy az egyéni metrikák az egyetlen telemetriai típusok, ahol a mintavételezés nem konfigurálható. A metrikák exportőr elküldi az összes telemetriai, hogy a nyomon követi.

#### <a name="fixed-rate-sampling-for-tracing"></a>Rögzített sebességű mintavétel a nyomon követéshez ####
Megadhat egy `sampler` elemet is a(z) `Tracer` konfigurációja részeként. Ha nincs megadva explicit `ProbabilitySampler` mintavevő, a rendszer alapértelmezés szerint használja. Alapértelmezés `ProbabilitySampler` szerint 1/10000-es sebességet használna, ami azt jelenti, hogy minden 10000 kérelemből egyet küld a rendszer az Application Insightsnak. A továbbiakban megtudhatja, hogyan adhat meg mintavételezési frekvenciát.

A mintavételi arány megadásához `Tracer` győződjön meg arról, hogy a mintavételező 0,0 és 1,0 közötti mintavételi sebességgel rendelkező mintavételi sebességet ad meg. Az 1.0 mintavételi arány 100%-os, ami azt jelenti, hogy az összes kérelmet telemetriaként küldi el az Application Insights.

```python
tracer = Tracer(
    exporter=AzureExporter(
        instrumentation_key='00000000-0000-0000-0000-000000000000',
    ),
    sampler=ProbabilitySampler(1.0),
)
```

#### <a name="fixed-rate-sampling-for-logs"></a>Rögzített sebességű mintavétel a naplókhoz ####
A rögzített ütemű mintavételezést `AzureLogHandler` `logging_sampling_rate` a választható argumentum módosításával állíthatja be. Ha nem ad meg argumentumot, 1,0 mintavételi arányt fog használni. Az 1.0 mintavételi arány 100%-os, ami azt jelenti, hogy az összes kérelmet telemetriaként küldi el az Application Insights.

```python
exporter = metrics_exporter.new_metrics_exporter(
    instrumentation_key='00000000-0000-0000-0000-000000000000',
    logging_sampling_rate=0.5,
)
```

### <a name="configuring-fixed-rate-sampling-for-web-pages-with-javascript"></a>Rögzített sebességű mintavételezés konfigurálása javascripttel rendelkező weblapokhoz

A JavaScript-alapú weblapok konfigurálhatók az Application Insights használatára. Telemetriai adatokat küld az ügyfélalkalmazás fut a felhasználó böngészőjében, és a lapok bármely kiszolgálóról üzemeltethető.

Amikor [konfigurálja a JavaScript-alapú weblapokat az Application Insights,](javascript.md)módosítsa a JavaScript-kódrészletet, amely az Application Insights portálon kapott.

> [!TIP]
> A javascriptet is magában foglaló ASP.NET alkalmazásban `_Layout.cshtml`a kódrészlet általában bekerül.

Helyezzen be `samplingPercentage: 10,` egy sort, mint a műszerezési kulcs előtt:

```xml
<script>
    var appInsights = // ... 
    ({ 
      // Value must be 100/N where N is an integer.
      // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
      samplingPercentage: 10, 

      instrumentationKey: ...
    }); 

    window.appInsights = appInsights; 
    appInsights.trackPageView(); 
</script>
```

A mintavételi százalékhoz olyan százalékot válasszon, amely közel 100/N, ahol N egész szám. Jelenleg a mintavételezés nem támogatja az egyéb értékeket.

#### <a name="coordinating-server-side-and-client-side-sampling"></a>A kiszolgálóoldali és az ügyféloldali mintavétel koordinálása

Az ügyféloldali JavaScript SDK a kiszolgálóoldali SDK-val együtt részt vesz a rögzített sebességű mintavételezésben. A műszeres oldalak csak ügyféloldali telemetriai adatokat küld ugyanattól a felhasználótól, amelyhez a kiszolgálóoldali SDK úgy döntött, hogy a mintavételbe belefoglalja. Ez a logika úgy van kialakítva, hogy megőrizze a felhasználói munkamenetek integritását az ügyfél- és kiszolgálóoldali alkalmazások között. Ennek eredményeképpen az Application Insights bármely telemetriai eleméből megtalálhatja az összes többi telemetriai elemetriai elemetelemet ezen felhasználó vagy munkamenet számára, és a Keresésben navigálhat a kapcsolódó oldalmegtekintések és kérések között.

Ha az ügyfél- és kiszolgálóoldali telemetriai adatok nem jelenítik meg az összehangolt mintákat:

* Ellenőrizze, hogy engedélyezte-e a mintavételezést mind a kiszolgálón, mind az ügyfélen.
* Ellenőrizze, hogy ugyanazt a mintavételi százalékot állította-e be az ügyfélben és a kiszolgálón is.
* Győződjön meg arról, hogy az SDK-verzió 2.0 vagy újabb.

## <a name="ingestion-sampling"></a>Lenyelési mintavétel

A betöltési mintavételezés azon a ponton működik, ahol a webkiszolgáló, a böngészők és az eszközök telemetriai adatai elérik az Application Insights szolgáltatás végpontját. Bár nem csökkenti az alkalmazásból küldött telemetriai forgalmat, csökkenti az Application Insights által feldolgozott és megőrzött (és felszámított) feldolgozott és felszámított mennyiséget.

Akkor használja ezt a mintavételezési típust, ha az alkalmazás gyakran túllépi a havi kvótát, és nincs lehetősége az SDK-alapú mintavételi típusok egyikének használatára sem. 

Állítsa be a mintavételi arányt a Használati és becsült költségek oldalon:

![Az alkalmazás áttekintése panelen kattintson a Beállítások, Kvóta, Minták elemre, majd válasszon mintavételi arányt, majd kattintson a Frissítés gombra.](./media/sampling/data-sampling.png)

Más típusú mintavételi, az algoritmus megtartja a kapcsolódó telemetriai elemeket. Ha például a keresésben ellenőrzi a telemetriai adatokat, megtalálhatja egy adott kivételhez kapcsolódó kérelmet. Metrikák száma, például a kérelem sebessége és a kivétel mértéke helyesen megmarad.

A mintavétellel elvetett adatpontok nem érhetők el az Application Insights egyik szolgáltatásában, például a [folyamatos exportálásban.](../../azure-monitor/app/export-telemetry.md)

A betöltési mintavételezés nem működik, amíg adaptív vagy rögzített sebességű mintavételműködik. Az adaptív mintavételezés alapértelmezés szerint engedélyezve van, ha a ASP.NET SDK vagy a ASP.NET Core SDK van használatban, vagy ha az Application Insights engedélyezve van az [Azure App Service-ben](azure-web-apps.md) vagy állapotfigyelő használatával. Ha az Application Insights-szolgáltatás végpontja telemetriai adatokat kap, megvizsgálja a telemetriai adatokat, és ha a mintavételi arány 100%-nál kisebb (ami azt jelzi, hogy a telemetria mintavételezése folyamatban van), majd a beállított betöltési mintavételi arány figyelmen kívül lesz hagyva.

> [!WARNING]
> A portálcsempén látható érték a betöltési mintavételezéshez beállított értéket jelzi. Nem jelenti a tényleges mintavételi arányt, ha valamilyen SDK-mintavételezés (adaptív vagy rögzített sebességű mintavétel) működik.

## <a name="when-to-use-sampling"></a>Mikor kell mintavételt alkalmazni?

Általában a legtöbb kis és közepes méretű alkalmazások nem kell mintavételezés. A leghasznosabb diagnosztikai információkat és a legpontosabb statisztikákat az összes felhasználói tevékenységre vonatkozó adatok gyűjtésével szerezheti be. 

A mintavétel fő előnyei a következők:

* Az Application Insights szolgáltatás csökken ("szabályozások") adatpontok, amikor az alkalmazás küld egy nagyon magas telemetriai rövid időintervallumban. Mintavételi csökkenti annak valószínűségét, hogy az alkalmazás látni fogja a szabályozás bekövetkezése.
* A tarifacsomag adatpontjainak [kvótáján](pricing.md) belül való megtartása. 
* A telemetriai adatok gyűjteményéből származó hálózati forgalom csökkentése. 

### <a name="which-type-of-sampling-should-i-use"></a>Milyen típusú mintavételt használjak?

**Használjon betöltési mintavételt, ha:**

* Gyakran használja a havi telemetriai kvótát.
* Túl sok telemetriai adatokat kap a felhasználók webböngészőiből.
* Az SDK olyan verzióját használja, amely nem támogatja a mintavételezést – például ASP.NET 2-nél korábbi verziókat.

**Rögzített mintavételt akkor használjon, ha:**

* Szinkronizálott mintavételezést szeretne az ügyfél és a kiszolgáló között, hogy a [Keresés](../../azure-monitor/app/diagnostic-search.md)ben események et vizsgálva navigálhasson az ügyfélen és a kiszolgálón lévő kapcsolódó események, például az oldalmegtekintések és a HTTP-kérelmek között.
* Biztos abban, hogy az alkalmazásnak megfelelő mintavételi százaléka van. Elég magasnak kell lennie ahhoz, hogy pontos metrikákat kapjon, de a díjszabási kvótát és a sávszélesség-szabályozási korlátokat meghaladó sebesség alatt.

**Adaptív mintavételezés használata:**

Ha a mintavétel egyéb formáinak használatának feltételei nem érvényesek, javasoljuk az adaptív mintavételezést. Ez a beállítás alapértelmezés szerint engedélyezve van a ASP.NET/ASP.NET Core SDK-ban. Nem csökkenti a forgalmat, amíg el nem ér egy bizonyos minimális sebességet, ezért az alacsony felhasználású webhelyekből valószínűleg egyáltalán nem vesznek mintát.

## <a name="knowing-whether-sampling-is-in-operation"></a>Annak ismerete, hogy a mintavétel működik-e

A tényleges mintavételi arány felderítéséhez függetlenül attól, hogy hol alkalmazták, használjon egy [ilyen Analytics-lekérdezést:](../../azure-monitor/app/analytics.md)

```kusto
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Ha azt `RetainedPercentage` látja, hogy bármely típus kevesebb, mint 100, majd az ilyen típusú telemetriai a mintavétel alatt áll.

> [!IMPORTANT]
> Az Application Insights nem minta munkamenet, metrikák (beleértve az egyéni metrikák), vagy a teljesítményszámláló telemetriai típusok a mintavételi technikák bármelyikében. Ezek a típusok mindig ki vannak zárva a mintavételi, mint a pontosság csökkentése is nagyon nemkívánatos az ilyen telemetriai típusok.

## <a name="how-sampling-works"></a>A mintavétel iratása

A mintavételi algoritmus dönti el, hogy mely telemetriai elemeket dobja el, és melyeket kell megtartani. Ez igaz, hogy a mintavételezést az SDK vagy az Application Insights szolgáltatás végzi. A mintavételi döntés több olyan szabályon alapul, amelyek célja az összes egymással összefüggő adatpont sértetlensítése, az Application Insights diagnosztikai élményének fenntartása, amely még csökkentett adatkészlet esetén is végrehajtható és megbízható. Ha például az alkalmazás egy sikertelen kérelem szerepel egy mintában, a további telemetriai elemek (például a kivétel és a kérelemhez naplózott nyomkövetések) megmaradnak. Mintavétel vagy tartja, vagy csepp őket együtt. Ennek eredményeképpen, ha megtekinti a kérelem részleteit az Application Insights, mindig láthatja a kérelmet a kapcsolódó telemetriai elemekkel együtt.

A mintavételi döntés a kérelem működési azonosítóján alapul, ami azt jelenti, hogy egy adott művelethez tartozó összes telemetriai elem megmarad vagy eldobható. A telemetriai elemek, amelyek nem rendelkeznek a művelet azonosító készlet (például a telemetriai elemek jelentett aszinkron szálak http-környezet nélkül) mintavételi egyszerűen rögzíti az egyes típusú telemetriai elemek százalékos arányát.

Amikor a telemetriai adatokat vissza önnek, az Application Insights szolgáltatás a metrikák at a gyűjtemény során használt azonos mintavételi százalékkal, a hiányzó adatpontok kompenzálása érdekében. Ezért ha megnézi a telemetriai adatokat az Application Insightsban, a felhasználók statisztikailag helyes közelítéseket látnak, amelyek nagyon közel vannak a valós számokhoz.

A közelítés pontossága nagymértékben függ a beállított mintavételi százaléktól. Emellett a pontosság növeli az alkalmazások, amelyek kezelik a nagy mennyiségű általában hasonló kérelmek sok felhasználó. Másrészt, az alkalmazások, amelyek nem működnek jelentős terheléssel, mintavételezésnem van szükség, mivel ezek az alkalmazások általában elküldi az összes telemetriai adatokat, miközben a kvótán belül marad, anélkül, hogy adatvesztést a szabályozás. 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

*Mi az alapértelmezett mintavételi viselkedés a ASP.NET és ASP.NET Core SDK-kban?*

* Ha a fenti SDK legújabb verzióinak egyikét használja, az adaptív mintavételezés alapértelmezés szerint másodpercenként öt telemetriai elemsel engedélyezve van.
  Alapértelmezés szerint `AdaptiveSamplingTelemetryProcessor` két csomópont ot ad hozzá, az egyik tartalmazza a `Event` `Event` mintavételi típust, míg a másik kizárja a típust a mintavételből. Ez a konfiguráció azt jelenti, hogy az SDK megpróbálja korlátozni `Event` a telemetriai elemek öt telemetriai elemek `Events` típusú, és öt telemetriai elemek minden más típusú kombinált, ezáltal biztosítva, hogy a mintavételt a többi telemetriai típusoktól elkülönítve. Az eseményeket általában üzleti telemetriai adatokhoz használják, és valószínűleg nem befolyásolhatják a diagnosztikai telemetriai kötetek.
  
  Az alábbi ábrán az alapértelmezettként `ApplicationInsights.config` létrehozott fájl látható. A Core ASP.NET ugyanazok az alapértelmezett viselkedések vannak engedélyezve a kódban. Az alapértelmezett viselkedés módosításához használja [a lap korábbi szakaszában található példákat.](#configuring-adaptive-sampling-for-aspnet-core-applications)

    ```xml
    <TelemetryProcessors>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <ExcludedTypes>Event</ExcludedTypes>
        </Add>
        <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
            <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
            <IncludedTypes>Event</IncludedTypes>
        </Add>
    </TelemetryProcessors>
    ```

*A telemetriai adatok többször is mintavételezhetők?*

* Nem. SamplingTelemetryProcessors figyelmen kívül hagyja a mintavételi szempontokat, ha az elem már mintavételezett elemeket. Ugyanez igaz a betöltési mintavételezésre is, amely nem alkalmaz mintavételezést az Okra az SDK-ban már mintavételezett elemekre.

*Miért nem mintavételezési egy egyszerű "gyűjtsük össze x százaléka minden telemetriai típus"?*

* Bár ez a mintavételi megközelítés magas szintű pontosságot biztosítana a metrikaközelítésekben, megtörné a felhasználónkénti, munkamenetenkénti és kérésenkénti diagnosztikai adatok korrelációját, ami kritikus fontosságú a diagnosztika szempontjából. Ezért a mintavételezés idáig jobban működik a szabályzatok, például a "gyűjti össze az összes telemetriai elem x százaléka az alkalmazás felhasználók", vagy "összegyűjti az összes telemetriai adatok X százaléka az alkalmazáskérelmek". A kérelmekhez nem kapcsolódó telemetriai elemek (például a háttéraszinkron feldolgozás) esetében a tartalék az, hogy "az egyes telemetriai típusokhoz tartozó összes elem X százalékát gyűjtse össze". 

*Változhat a mintavételi százalék az idő múlásával?*

* Igen, az adaptív mintavételezés fokozatosan módosítja a mintavételi százalékot a telemetria jelenleg megfigyelt térfogata alapján.

*Ha rögzített mintavételt használok, honnan tudhatom, hogy melyik mintavételi százalék működik a legjobban az alkalmazásomszámára?*

* Ennek egyik módja az adaptív mintavételezés, annak kiderítése, hogy milyen ütemben telepszik le (lásd a fenti kérdést), majd átváltson a rögzített sebességű mintavételezésre ezzel az aránysal. 
  
    Különben ki kell találnod. Elemezze az aktuális telemetriai használat az Application Insightsban, figyelje meg a szabályozás, amely előforduló, és becsülje meg az összegyűjtött telemetriai adatok mennyiségét. Ez a három bemenet, a kiválasztott tarifacsomaggal együtt azt javasolják, hogy mennyit szeretne csökkenteni az összegyűjtött telemetriai adatok mennyiségét. Azonban a felhasználók számának növekedése, vagy valamilyen más változás a telemetriai adatok mennyisége érvénytelenítheti a becslést.

*Mi történik, ha a mintavételi százalékot túl alacsonyra állítom?*

* A túlzottan alacsony mintavételi százalékok túlzottan agresszív mintavételezést okoznak, és csökkentik a közelítések pontosságát, amikor az Application Insights megpróbálja kompenzálni az adatok megjelenítését az adatmennyiség-csökkentéshez. A diagnosztikai élmény is negatívhatással lehet, mivel a ritkán sikertelen vagy lassú kérelmek némelyike mintavételezhető.

*Mi történik, ha a mintavételi százalékot túl magasra állítom?*

* Túl magas mintavételi százalék konfigurálása (nem elég agresszív) az összegyűjtött telemetriai adatok mennyiségének elégtelen csökkenését eredményezi. Továbbra is előfordulhat, hogy a szabályozással kapcsolatos telemetriai adatvesztést tapasztal, és az Application Insights használatának költsége magasabb lehet, mint amit a túlterhelési díjak miatt tervezett.

*Milyen platformokon használhatom a mintavételt?*

* Betöltési mintavételautomatikusan előfordulhat egy bizonyos kötet feletti telemetriai adatok, ha az SDK nem végez mintavételezést. Ez a konfiguráció például akkor működik, ha a ASP.NET SDK vagy a Java SDK régebbi verzióját használja.
* Ha az aktuális ASP.NET vagy ASP.NET Core SDK-k (üzemelteti az Azure-ban vagy a saját kiszolgálóján), alapértelmezés szerint adaptív mintavételezést kap, de átválthat a rögzített sebességű, a fent leírtak szerint. Fix sebességű mintavételezés esetén a böngésző SDK automatikusan szinkronizálja a minta kapcsolódó események. 
* Ha az aktuális Java-ügynököt használja, `ApplicationInsights.json` konfigurálhatja (Java SDK esetén konfigurálhatja) `ApplicationInsights.xml`a rögzített sebességű mintavételezés bekapcsolását. A mintavételezés alapértelmezés szerint ki van kapcsolva. Rögzített sebességű mintavételezés esetén a böngésző SDK és a kiszolgáló automatikusan szinkronizálja a kapcsolódó események mintavételével.

*Vannak bizonyos ritka események, amelyeket mindig látni akarok. Hogyan juthatok át a mintavételi modulon?*

* Ennek legjobb módja egy egyéni [Telemetriai initializer írása,](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)amely a `SamplingPercentage` megtartani kívánt telemetriai elem 100-ra állítja be, az alábbiak szerint. Mivel az inicializálók garantáltan a telemetriai processzorok előtt futnak (beleértve a mintavételezést is), ez biztosítja, hogy minden mintavételi technika figyelmen kívül hagyja ezt az elemet a mintavételi szempontokból. Egyéni telemetriai inicializálók érhetők el a ASP.NET SDK, a ASP.NET Core SDK, a JavaScript SDK és a Java SDK. Konfigurálhatja például a telemetriai inicializálót a ASP.NET SDK használatával:

    ```csharp
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            if(somecondition)
            {
                ((ISupportSampling)telemetry).SamplingPercentage = 100;
            }
        }
    }
    ```

## <a name="older-sdk-versions"></a>Régebbi SDK-verziók

Adaptív mintavételezés érhető el az Application Insights SDK ASP.NET v2.0.0-beta3 és újabb, Microsoft.ApplicationInsights.AspNetCore SDK v2.2.0-beta1 és újabb, és alapértelmezés szerint engedélyezve van.

A rögzített díjú mintavételezés az SDK szolgáltatása a 2.0.0 és a Java SDK 2.0.1-es és 2.0.1-es verziójától ASP.NET verzióban.

A ASP.NET SDK 2.5.0-beta2 és ASP.NET Core SDK 2.2.0-beta3 v2.2.0-beta3 előtti mintavételi döntés a "felhasználó" (azaz a legjellemzőbb webes alkalmazások) felhasználói azonosítójának kivonatán alapult. A felhasználókat nem definiáló alkalmazások (például a webszolgáltatások) esetében a mintavételi döntés a kérelem műveletazonosítóján alapult. A ASP.NET és a ASP.NET Core SDK-k legújabb verziói a mintavételi döntéshez a műveleti azonosítót használják.

## <a name="next-steps"></a>További lépések

* [A szűrés](../../azure-monitor/app/api-filtering-sampling.md) szigorúbb anamnézist biztosíthat az SDK által küldött adatok felett.
* Olvassa el a Fejlesztői hálózat [cikkét: A telemetria optimalizálása az Application Insights segítségével.](https://msdn.microsoft.com/magazine/mt808502.aspx)
