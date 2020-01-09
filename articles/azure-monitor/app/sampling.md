---
title: Telemetria mintavételezés az Azure Application Insightsban | Microsoft Docs
description: A telemetria mennyiségének megtartása a vezérlés alatt.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/14/2019
ms.reviewer: vitalyg
ms.openlocfilehash: d88df0c7e17d297162a1921021b89f02077c2ac7
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75560385"
---
# <a name="sampling-in-application-insights"></a>Application Insights-mintavétel

A mintavétel az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)egyik funkciója. Az ajánlott módszer a telemetria-forgalom és-tárolás csökkentése, ugyanakkor az alkalmazásadatok statisztikailag helyes elemzésének megőrzése. A szűrő kiválasztja a kapcsolódó elemeket, így az elemek között lehet navigálni, amikor diagnosztikai vizsgálatokat végez.
Ha a mérőszámok a portálon jelennek meg, akkor a rendszer a mintavétel során újranormalizálja őket. Ezzel a módszerrel csökkentheti a statisztikára gyakorolt hatást.

A mintavétel csökkenti a forgalom és az adatforgalmi költségeket, és segít elkerülni a szabályozást.

## <a name="in-brief"></a>Röviden:

* A mintavételezés 1 *n* rekordban marad, és elveti a többit. Például megtarthat egy öt eseményt, a mintavételezési arány 20%. 
* Az adaptív mintavételezés alapértelmezés szerint engedélyezve van a ASP.NET legújabb verziójában, és ASP.NET Core szoftverfejlesztői készletek (SDK-k).
* A mintavételt manuálisan is beállíthatja. Ez a portálon, a *használati és becsült költségek lapon*, a ASP.net SDK-ban, a ApplicationInsights. config fájlban, az ASP.net Core SDK-n keresztül, a ApplicationInsights. xml fájlban pedig a Java SDK-ban is konfigurálható.
* Ha egyéni eseményeket naplóz, és meg kell győződnie arról, hogy az események egy csoportjának megőrzése vagy elvetése megtörtént, akkor az eseményeknek azonos OperationId értékkel kell rendelkezniük.
* A (z) " *n* " mintavétel a (z) `itemCount`tulajdonság minden rekordjában megjelenik, amely a keresésben a "kérések száma" vagy az "események száma" rövid névvel jelenik meg. `itemCount==1`, ha a mintavételezés nem működik.
* Ha elemzési lekérdezéseket ír, [vegye figyelembe a mintavételezést](../../azure-monitor/log-query/aggregations.md). A rekordok egyszerűen számlálása helyett a `summarize sum(itemCount)`t kell használnia.

## <a name="types-of-sampling"></a>Mintavételi típusok

Három alternatív mintavételi módszer létezik:

* Az **adaptív mintavételezés** automatikusan módosítja az SDK-ból a ASP.net/ASP.net Core-alkalmazásban eljuttatott telemetria mennyiségét. Ez az alapértelmezett mintavétel a ASP.NET web SDK v 2.0.0-beta3 és a Microsoft. ApplicationInsights. AspNetCore SDK v 2.2.0 – béta-től kezdődően.  Az adaptív mintavételezés jelenleg csak a ASP.NET kiszolgálóoldali telemetria érhető el.

* A **rögzített arányú mintavételezés** csökkenti a ASP.NET vagy ASP.net Core vagy Java-kiszolgálóról és a felhasználói böngészőkből eljuttatott telemetria mennyiségét. Beállítja a díjszabást. Az ügyfél és a kiszolgáló szinkronizálja a mintavételezést, hogy a keresés során navigáljon a kapcsolódó nézetek és kérések között.

* Betöltési **mintavételezés** A Azure Portal működik. A rendszer elveti az alkalmazásból érkező egyes telemetria a beállított mintavételi sebességgel. Nem csökkenti az alkalmazásból eljuttatott telemetria forgalmat, de segít megőrizni a havi kvótán belül. A betöltési mintavételezés legfőbb előnye, hogy az alkalmazás újbóli üzembe helyezése nélkül állíthatja be a mintavételezési sebességet. A betöltési mintavételezés egységesen működik az összes kiszolgáló és ügyfél esetében.

Ha az adaptív vagy rögzített arányú mintavétel működik, a betöltési mintavételezés le van tiltva.


## <a name="adaptive-sampling-in-your-aspnetaspnet-core-web-applications"></a>Adaptív mintavételezés a ASP.NET/ASP.NET Core-webalkalmazásokban

Az adaptív mintavételezés Application Insights a ASP.NET v 2.0.0-beta3 és újabb, a Microsoft. ApplicationInsights. AspNetCore SDK v 2.2.0 – béta és újabb verziókban érhető el, és alapértelmezés szerint engedélyezve van.

Az adaptív mintavételezés hatással van a webkiszolgáló-alkalmazásból a Application Insights szolgáltatási végpontra elküldett telemetria mennyiségére. A kötetet a rendszer automatikusan módosítja, hogy a megadott maximális adatforgalomon belül maradjon, és a `MaxTelemetryItemsPerSecond`beállításával vezérelhető. Ha az alkalmazás alacsony telemetria (például hibakeresés vagy alacsony kihasználtság miatt) hoz létre, akkor a mintavételi processzor nem távolítja el az elemeket, amíg a kötet `MaxTelemetryItemsPerSecond`alatt van. Ahogy a telemetria mennyisége növekszik, a mintavételezési arány úgy módosul, hogy a cél kötetet lehessen elérni.

A cél kötet eléréséhez a létrehozott telemetria némelyikét elveti a rendszer. Más típusú mintavételezéshez hasonlóan az algoritmus is megőrzi a kapcsolódó telemetria elemeket. Ha például a keresés során megkeresi a telemetria, az adott kivételhez kapcsolódó kérést is megtalálhatja.

A metrikák számát, például a kérelmek arányát és a kivételek arányát úgy kell beállítani, hogy kompenzálni lehessen a mintavételezési sebességet, hogy a mérőszám-kezelőben megközelítőleg helyes értékeket mutassanak.

## <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Adaptív mintavételezés konfigurálása ASP.NET-alkalmazásokhoz

[További](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) információ az adaptív mintavételezés konfigurálásáról ASP.net Core alkalmazásokhoz. 

A [ApplicationInsights. config fájlban](../../azure-monitor/app/configuration-with-applicationinsights-config.md)számos paramétert módosíthat a `AdaptiveSamplingTelemetryProcessor` csomópontban. A megjelenített számok az alapértelmezett értékek:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Az adaptív algoritmus által az **egyes kiszolgálók gazdagépére**irányuló célként megadott sebesség. Ha a webalkalmazás sok gazdagépen fut, csökkentse ezt az értéket úgy, hogy a Application Insights portálon maradjon a forgalmi arányon belül.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    A telemetria aktuális sebességének újraértékelésének időköze. A kiértékelést mozgóátlagként kell végrehajtani. Előfordulhat, hogy le kívánja rövidíteni ezt az intervallumot, ha a telemetria a hirtelen törtek.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Ha a mintavételezés százalékos értéke megváltozik, azt követően, hogy a mintavételezési százalékarányt később is engedélyezzük, a kevesebb adatmennyiséget is csökkentheti.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    A mintavételezés százalékos értékének megváltozásakor a további adatmennyiség megnövekedéséhez a mintavételezési százalékos arányt később is növelheti.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Mivel a mintavételezési százalék változó, mi az a minimális érték, amelyet be lehet állítani.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Mivel a mintavételezési százalék változó, mi az a maximális érték, amelyet be lehet állítani.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    A mozgóátlag kiszámításakor a legutóbbi értékhez rendelt súlyozást. 1 értékkel egyenlő vagy annál kisebb értéket használjon. A kisebb értékek miatt az algoritmus kevésbé lesz újraaktiválva a hirtelen változásokkal.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Az alkalmazás első elindításakor hozzárendelt érték. Hibakeresés közben ne csökkentse az értéket.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Pontosvesszővel tagolt lista, amelyet nem szeretne mintát venni. A felismert típusok: függőség, esemény, kivétel, oldalmegtekintés, kérelem, nyomkövetés. A rendszer továbbítja a megadott típusok összes példányát. a nem megadott típusok mintául szolgálnak.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    A mintavételhez használni kívánt típusok pontosvesszővel tagolt listája. A felismert típusok: függőség, esemény, kivétel, oldalmegtekintés, kérelem, nyomkövetés. A megadott típusok mintául szolgálnak. a többi típus összes példánya mindig továbbítva lesz.


Az adaptív mintavételezés **kikapcsolásához** távolítsa el a AdaptiveSamplingTelemetryProcessor csomópont (oka) t a applicationinsights-config fájlból.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternatíva: adaptív mintavételezés konfigurálása a kódban

Ahelyett, hogy a mintavételi paramétert a. config fájlban állítsa be, programozott módon állíthatja be ezeket az értékeket.

1. Távolítsa el az összes `AdaptiveSamplingTelemetryProcessor` csomópontot a. config fájlból.
2. Használja az alábbi kódrészletet az adaptív mintavételezés konfigurálásához.

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Enable AdaptiveSampling so as to keep overall telemetry volume to 5 items per second.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([További információ a telemetria processzorokról](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

Az egyes telemetria-típusok mintavételi sebességét egyenként is beállíthatja, vagy kizárhat bizonyos típusokat is a mintavételből. 

*C#*

```csharp
    // The following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

## <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Az adaptív mintavételezés konfigurálása ASP.NET Core alkalmazásokhoz.

ASP.NET Core alkalmazások esetében nincs `ApplicationInsights.Config`, így minden konfiguráció kód használatával történik.
Az adaptív mintavételezés alapértelmezés szerint engedélyezve van az összes ASP.NET Core alkalmazáshoz. Letilthatja vagy testreszabhatja a mintavételezési viselkedést.

### <a name="turning-off-adaptive-sampling"></a>Az adaptív mintavételezés kikapcsolása

Az alapértelmezett mintavételi funkció letiltható a Application Insights szolgáltatás hozzáadásakor a metódusban ```ConfigureServices```a `Startup.cs` fájlban lévő ```ApplicationInsightsServiceOptions``` használatával:

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

A fenti kód letiltja a mintavételezési funkciót. Kövesse az alábbi lépéseket a mintavételezés további testreszabási lehetőségekkel való hozzáadásához.

### <a name="configure-sampling-settings"></a>Mintavételezési beállítások konfigurálása

A mintavételezési viselkedés testre szabásához használja a ```TelemetryProcessorChainBuilder``` bővítményi metódusait.

> [!IMPORTANT]
> Ha ezzel a módszerrel konfigurálja a mintavételezést, győződjön meg arról, hogy a aiOptions. EnableAdaptiveSampling = FALSE; értéket használja. beállítások a AddApplicationInsightsTelemetry ().

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, TelemetryConfiguration configuration)
{
    var builder = configuration.TelemetryProcessorChainBuilder;
    // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
    // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    // Using adaptive sampling
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5);

    // Alternately, the following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    // builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");

    builder.Build();

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));
    // ...
}

```

**Ha a fenti módszer használatával konfigurálja a mintavételezést, ügyeljen arra, hogy a AddApplicationInsightsTelemetry () ```aiOptions.EnableAdaptiveSampling = false;``` beállításait használja.**

## <a name="fixed-rate-sampling-for-aspnet-aspnet-core-java-websites-and-python-applications"></a>Rögzített díjszabás a ASP.NET, a ASP.NET Core, a Java-webhelyekhez és a Python-alkalmazásokhoz

A rögzített arányú mintavételezés csökkenti a webkiszolgálóról és a webböngészőkből eljuttatott forgalmat. Az adaptív mintavételezéstől eltérően az Ön által meghatározott rögzített arányban csökkenti a telemetria. Emellett szinkronizálja az ügyfél és a kiszolgáló mintavételezését is, hogy a kapcsolódó elemek megmaradjanak – például amikor megtekinti a keresési oldal nézetét, megtalálhatja a kapcsolódó kérést.

Más mintavételi technikákhoz hasonlóan a kapcsolódó elemek is megmaradnak. Minden HTTP-kérelem esetén a kérést és a kapcsolódó eseményeket elveti vagy továbbítja a rendszer.

Metrikaböngésző esetében a kérelmek és a kivételek számát a rendszer a mintavételezési sebesség kompenzálására szolgáló tényezővel szorozza meg, így azok körülbelül helyesek.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Rögzített arányú mintavételezés konfigurálása a ASP.NET-ben

1. Az **adaptív mintavételezés letiltása**: a [ApplicationInsights. config fájlban](../../azure-monitor/app/configuration-with-applicationinsights-config.md)távolítsa el a `AdaptiveSamplingTelemetryProcessor` csomópontot, vagy tegye megjegyzésbe.

    ```xml

    <TelemetryProcessors>

    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    ```

2. **Engedélyezze a rögzített sebességű mintavételi modult.** Adja hozzá ezt a kódrészletet a [ApplicationInsights. config fájlhoz](../../azure-monitor/app/configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```
   ### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Alternatíva: a rögzített arányú mintavételezés engedélyezése a kiszolgálói kódban
    
    Ahelyett, hogy a mintavételi paramétert a. config fájlban állítsa be, programozott módon állíthatja be ezeket az értékeket. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    // If you are on ApplicationInsights SDK v 2.8.0-beta2 or higher, use the following line instead
    // var builder = TelemetryConfiguration.Active.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```
([További információ a telemetria processzorokról](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-in-aspnet-core"></a>Rögzített arányú mintavételezés konfigurálása ASP.NET Core

1. Az **adaptív mintavételezés letiltása**: a (z) ```ConfigureServices```metódusban végrehajtott módosítások a ```ApplicationInsightsServiceOptions```használatával hajthatók végre:

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

2. **Engedélyezze a rögzített sebességű mintavételi modult.** A (z) ```Configure``` metódus módosításait az alábbi kódrészletben látható módon lehet elvégezni:

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

        var builder = configuration.TelemetryProcessorChainBuilder;
        // version 2.5.0-beta2 and above should use the following line instead of above. (https://github.com/Microsoft/ApplicationInsights-aspnetcore/blob/develop/CHANGELOG.md#version-250-beta2)
        // var builder = configuration.DefaultTelemetrySink.TelemetryProcessorChainBuilder;

        // Using fixed rate sampling   
        double fixedSamplingPercentage = 10;
        builder.UseSampling(fixedSamplingPercentage);

        builder.Build();

        // ...
    }

    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>Rögzített arányú mintavétel konfigurálása JAVA-ban ###

1. Webalkalmazás letöltése és konfigurálása a legújabb [Application bepillantást Java SDK](../../azure-monitor/app/java-get-started.md) -val

2. A **rögzített arányú mintavételi modul engedélyezéséhez** adja hozzá a következő kódrészletet a ApplicationInsights. xml fájlhoz.

    ```XML
        <TelemetryProcessors>
            <BuiltInProcessors>
                <Processor type = "FixedRateSamplingTelemetryProcessor">
                    <!-- Set a percentage close to 100/N where N is an integer. -->
                    <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                    <Add name = "SamplingPercentage" value = "50" />
                </Processor>
            </BuiltInProcessors>
        </TelemetryProcessors>
    ```

3. A mintavételezési telemetria belefoglalhatja vagy kizárhatja a mintavételből a "FixedRateSamplingTelemetryProcessor" processzor címkéjén belüli alábbi címkék használatával.
    ```XML
        <ExcludedTypes>
            <ExcludedType>Request</ExcludedType>
        </ExcludedTypes>

        <IncludedTypes>
            <IncludedType>Exception</IncludedType>
        </IncludedTypes>
    ```

A mintavételből befoglalható vagy kizárható telemetria-típusok a következők: függőség, esemény, kivétel, oldalmegtekintés, kérelem és nyomkövetés.

> [!NOTE]
> A mintavételezési százaléknál válasszon egy olyan százalékot, amely a 100/N értéknél közelebb van, ahol N egész szám.  A mintavétel jelenleg nem támogatja a többi értéket.
> 
> 

<a name="other-web-pages"></a>

### <a name="configuring-fixed-rate-sampling-in-opencensus-python"></a>Rögzített arányú mintavételezés konfigurálása a OpenCensus Pythonban ###

1. Az alkalmazást a legújabb [OpenCensus Azure monitor-exportőrökkel](../../azure-monitor/app/opencensus-python.md)alakíthatja ki.

> [!NOTE]
> A rögzített arányú mintavételezés csak a trace exportőr használatával érhető el. Ez azt jelenti, hogy a bejövő és a kimenő kérelmek az egyetlen telemetria, ahol a mintavétel konfigurálható.
> 
> 

2. Megadhat egy `sampler` elemet is a(z) `Tracer` konfigurációja részeként. Ha nincs megadva explicit sampler, a rendszer alapértelmezés szerint a ProbabilitySampler fogja használni. A ProbabilitySampler alapértelmezés szerint a 1/10000-as sebességet fogja használni, ami azt jelenti, hogy minden 10000-kérelem közül egyet Application Insights küld a rendszer. A továbbiakban megtudhatja, hogyan adhat meg mintavételezési frekvenciát.

3. A mintavételező megadása során győződjön meg arról, hogy a(z) `Tracer` 0,0 és 1,0 közötti mintavételezési frekvenciát ad meg (a végértékeket is beleértve). A 1,0-es mintavételi sebesség a 100%-ot jelöli, ami azt jelenti, hogy az összes kérést a rendszer telemetria fogja elküldeni Application Insights.

    ```python
    tracer = Tracer(
        exporter=AzureExporter(
            instrumentation_key='00000000-0000-0000-0000-000000000000',
        ),
        sampler=ProbabilitySampler(1.0),
    )
    ```

## <a name="ingestion-sampling"></a>Betöltési mintavételezés

Ez a mintavételezési módszer azon a ponton működik, ahol a webkiszolgálóról, böngészőkből és eszközökből származó telemetria eléri a Application Insights szolgáltatási végpontot. Bár ez nem csökkenti az alkalmazásból eljuttatott telemetria-forgalmat, csökkenti a feldolgozott és a megőrzött és a Application Insights által felszámított összeget.

Akkor használja ezt a mintavételezési típust, ha az alkalmazás gyakran túllépi a havi kvótát, és nem használhatja az SDK-alapú mintavételezési típusokat sem. 

Állítsa be a mintavételezési sebességet a használati és becsült költségek oldalon:

![Az alkalmazás áttekintés paneljén kattintson a beállítások, kvóta, minták, majd a mintavételi sebesség lehetőségre, és kattintson a frissítés elemre.](./media/sampling/data-sampling.png)

Más típusú mintavételezéshez hasonlóan az algoritmus is megőrzi a kapcsolódó telemetria-elemeket. Ha például a keresés során megkeresi a telemetria, az adott kivételhez kapcsolódó kérést is megtalálhatja. A metrikák számát, például a kérelmek arányát és a kivételek arányát megfelelően megőrzi a rendszer.

A mintavételezéssel elvetett adatpontok nem érhetők el semmilyen Application Insights funkcióban, például a [folyamatos exportálásban](../../azure-monitor/app/export-telemetry.md).

A betöltési mintavételezés nem működik, ha az SDK-alapú adaptív vagy rögzített arányú mintavétel működik. Az adaptív mintavételezés alapértelmezés szerint engedélyezve van, ha a ASP.NET/ASP.NET Core SDK engedélyezve van a Visual Studióban, vagy engedélyezve van az Azure-webalkalmazás-bővítményekben vagy a Állapotmonitor használatával, és a betöltési mintavételezés le van tiltva. Ha az SDK mintavételi sebessége kisebb, mint 100% (azaz az elemek mintavételezése folyamatban van, a rendszer figyelmen kívül hagyja a beállított betöltési mintavételezési sebességet.

> [!WARNING]
> A csempén látható érték jelzi a betöltési mintavételezéshez beállított értéket. A tényleges mintavételezési sebességet nem tükrözi, ha az SDK-mintavétel működik.
>
>
## <a name="sampling-for-web-pages-with-javascript"></a>A JavaScripttel rendelkező weblapok mintavételezése
Bármely kiszolgálóról konfigurálhat weblapokat rögzített arányú mintavételezéshez. 

Ha [Application Insights weblapjait konfigurálja](../../azure-monitor/app/javascript.md), módosítsa a Application Insights portálról beolvasott JavaScript-kódrészletet. (A ASP.NET-alkalmazásokban a kódrészlet általában a _Layout. cshtml.)  Szúrjon be egy sort, például `samplingPercentage: 10,` a kialakítási kulcs előtt:

    <script>
    var appInsights= ... 
    }({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
    samplingPercentage: 10, 

    instrumentationKey:...
    }); 

    window.appInsights=appInsights; 
    appInsights.trackPageView(); 
    </script> 

A mintavételezési százaléknál válasszon egy olyan százalékot, amely a 100/N értéknél közelebb van, ahol N egész szám.  A mintavétel jelenleg nem támogatja a többi értéket.

Ha a kiszolgálón a rögzített díjszabású mintavételezés is engedélyezve van, az ügyfelek és a kiszolgáló szinkronizálva lesz, így a keresés során a kapcsolódó oldalletöltések és a kérelmek között lehet navigálni.

## <a name="when-to-use-sampling"></a>Mikor kell használni a mintavételezést?

Az adaptív mintavételezés automatikusan engedélyezve van a legújabb .NET-és .NET Core SDK-k esetén. Függetlenül attól, hogy az SDK melyik verzióját használja, engedélyezheti a betöltési mintavételezést, hogy Application Insights az összegyűjtött adatok mintavételezését.

Alapértelmezés szerint nem engedélyezett a mintavételezés a Java SDK-ban. Jelenleg csak a rögzített sebességű mintavételezést támogatja. A Java SDK nem támogatja az adaptív mintavételezést.

Általánosságban elmondható, hogy a legtöbb kis-és közepes méretű alkalmazáshoz nem kell mintavételezést végeznie. A leghasznosabb diagnosztikai információk és a legpontosabb statisztikák a felhasználói tevékenységekre vonatkozó adatgyűjtéssel szerezhetők be. 

A mintavétel fő előnyei a következők:

* Application Insights a szolgáltatás elveszíti az adatpontokat ("szabályozások"), amikor az alkalmazás nagyon nagy mennyiségű telemetria küld rövid időn belül. 
* Az adatpontok [kvótáján](../../azure-monitor/app/pricing.md) belül megtarthatja az árképzési szintet. 
* A telemetria-gyűjtemény hálózati forgalmának csökkentése érdekében. 

### <a name="which-type-of-sampling-should-i-use"></a>Milyen típusú mintavételezést érdemes használni?

**A betöltési mintavételezés használata, ha:**

* Gyakran halad a telemetria havi kvótája.
* Az SDK olyan verzióját használja, amely nem támogatja a mintavételezést – például a 2 ASP.NET korábbi verziók esetében.
* Túl sok telemetria kap a felhasználók webböngészőjéből.

**A rögzített arányú mintavételezés használata:**

* A ASP.NET Web Services-2.0.0 vagy újabb verziójához, illetve a Java SDK v 2.0.1-es vagy újabb verzióhoz készült Application Insights SDK-t használja, és
* Szinkronizált mintavételezést kíván az ügyfél és a kiszolgáló között, így ha a [Keresés](../../azure-monitor/app/diagnostic-search.md)során eseményeket vizsgál, akkor az ügyfél és a kiszolgáló kapcsolódó eseményei között, például az oldalletöltések és a HTTP-kérelmek között is megtekintheti az adatokat.
* Biztos abban, hogy az alkalmazáshoz tartozó mintavételi százalék megfelelő. Elég magasnak kell lennie ahhoz, hogy pontos mérőszámok legyenek, de a díjszabása nem haladhatja meg az árképzési kvótát és a szabályozási korlátot. 

**Adaptív mintavételezés használata:**

Ha a mintavételezés más formáinak használatára vonatkozó feltételek nem érvényesek, javasoljuk az adaptív mintavételezést. Ez a beállítás alapértelmezés szerint engedélyezve van a ASP.NET/ASP.NET Core Server SDK-ban. Nem csökkenti a forgalmat, amíg el nem éri egy bizonyos minimális értéket, ezért az alacsony használatú helyeket nem érinti a rendszer.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Hogyan tudni, hogy a mintavétel működik-e?

Ha a tényleges mintavételezési sebességet szeretné felderíteni, függetlenül attól, hogy hol alkalmazták, használjon egy [elemzési lekérdezést](../../azure-monitor/app/analytics.md) , például a következőt:

```
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Ha a RetainedPercentage bármilyen típusú, 100-nál kisebb, akkor az adott elem mintavételezés alatt áll.

**A Application Insights a fent ismertetett mintavételi technikákban nem tárgyalja a munkamenetet, a metrikákat és a teljesítményszámlálók telemetria-típusokat. Ezeket a típusokat a rendszer mindig kizárja a mintavételezésből, mivel a pontosság csökkentése igen nemkívánatos lehet a telemetria-típusok esetében**

## <a name="how-does-sampling-work"></a>Hogyan működik a mintavételezés?

Az SDK rögzített arányú mintavételi funkciója a 2.0.0 és a Java SDK 2.0.1-es és újabb verzióiban elérhető ASP.NET-verziókban. Az adaptív mintavételezés az SDK egyik funkciója, amely a 2.0.0-től kezdve a ASP.NET verzióiban érhető el. A betöltési mintavételezés a Application Insights szolgáltatás egyik funkciója, amely akkor működik, ha az SDK nem végez mintavételezést.

A mintavételi algoritmus eldönti, hogy mely telemetria kell eldobni, és melyeket kell megőrizni (legyen az SDK-ban vagy a Application Insights szolgáltatásban). A mintavételi döntés több olyan szabályon alapul, amely az összes egymással összefüggő adatpont megőrzését célozza, és a diagnosztikai élmény fenntartása a Application Insights, amely akár csökkentett adatkészlettel is végrehajtható és megbízható. Ha például egy sikertelen kérelem esetében az alkalmazás további telemetria-elemeket küld (például a kérelemből naplózott kivételeket és nyomkövetéseket), a mintavételezés nem osztja szét ezt a kérést és más telemetria. Ezzel együtt tartja vagy eldobja őket. Ennek eredményeképpen, amikor megtekinti a kérelem részleteit a Application Insightsban, mindig láthatja a kérést a hozzá tartozó telemetria-elemekkel együtt. 

A mintavételi döntés a kérelem műveleti AZONOSÍTÓján alapul, ami azt jelenti, hogy egy adott művelethez tartozó összes telemetria-elem megmarad vagy el van dobva. A telemetria nem rendelkező elemek esetében (például a http-környezet nélküli aszinkron szálakból jelentett telemetria-elemek esetében) a mintavétel egyszerűen rögzíti az egyes típusú telemetria elemek százalékos arányát. A 2.5.0-Beta2 a .NET SDK-val és a ASP.NET Core SDK-val kapcsolatos 2.2.0-beta3 megelőzően a mintavételezési döntés a "felhasználó" (azaz a leggyakoribb webalkalmazások) definiált alkalmazások felhasználói AZONOSÍTÓjának kivonatán alapul. Azon alkalmazások típusai esetében, amelyek nem határoznak meg felhasználókat (például webszolgáltatásokat), a mintavételi döntés a kérelem műveleti AZONOSÍTÓján alapul.

Ha visszaküldi a telemetria, a Application Insights szolgáltatás a gyűjtemény időpontjában használt mintavételi százalékkal módosítja a mérőszámokat, hogy kompenzálja a hiányzó adatpontokat. Ezért, amikor a Application Insights telemetria tekinti meg, a felhasználók statisztikailag helyes közelítéseket látnak, amelyek nagyon közel vannak a valós számokhoz.

A közelítés pontossága nagyrészt a beállított mintavételi százaléktól függ. Emellett a pontosság a sok felhasználótól származó, általában hasonló kérelmek nagy mennyiségét kezelő alkalmazások esetében is növekszik. Másfelől azonban a jelentős terheléssel nem rendelkező alkalmazások esetében nincs szükség mintavételezésre, mivel ezek az alkalmazások általában a kvótán belül is elküldhetik az összes telemetria, anélkül, hogy adatvesztést okozna a szabályozás. 

> [!WARNING]
> A Application Insights nem tartalmaz mérőszámokat és munkamenetek telemetria-típusokat. A pontosság csökkentése igen nemkívánatos lehet a telemetria-típusok esetében.
> 

### <a name="adaptive-sampling"></a>Adaptív mintavételezés

Az adaptív mintavételezés egy olyan összetevőt is felvesz, amely figyeli az SDK-ban jelenleg továbbított átvitel sebességét, és a mintavételi százalékot úgy módosítja, hogy a cél maximális sebességén belül maradjon. A rendszer rendszeres időközönként újraszámítja a beállítást, és a kimenő átviteli sebesség mozgóátlagán alapul.

## <a name="sampling-and-the-javascript-sdk"></a>Mintavételezés és a JavaScript SDK

Az ügyféloldali (JavaScript) SDK a kiszolgálóoldali SDK-val együtt a rögzített arányú mintavételezéshez is részt vesz. Az átalakított lapok csak azokról a felhasználókról küldenek ügyféloldali telemetria, amelyeknek a kiszolgálóoldali döntése szerint a "minta". Ez a logika úgy van kialakítva, hogy fenntartsa a felhasználói munkamenet integritását az ügyfél és a kiszolgálók között. Ennek eredményeképpen a Application Insights bármely adott telemetria megtalálhatja a felhasználóhoz vagy a munkamenethez tartozó összes többi telemetria elemet is. 

*Az ügyfél és a kiszolgálóoldali telemetria nem jelenítenek meg koordinált mintákat a fent leírt módon.*

* Ellenőrizze, hogy engedélyezte-e a rögzített arányú mintavételezést a kiszolgálón és az ügyfélen egyaránt.
* Győződjön meg arról, hogy az SDK verziószáma 2,0 vagy újabb.
* Győződjön meg arról, hogy ugyanazt a mintavételezési százalékot állítja be az ügyfél és a kiszolgáló között.

### <a name="sampling-in-azure-functions"></a>Mintavételezés Azure Functions

A Azure Functions alkalmazásban futó alkalmazások mintavételezésének konfigurálásához [kövesse az alábbi](https://docs.microsoft.com/azure/azure-functions/functions-monitoring#configure-sampling) utasításokat.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

*Mi az alapértelmezett mintavételi viselkedés a ASP.NET és a ASP.NET Core SDK-ban?*

* Ha a fenti SDK legújabb verzióinak egyikét használja, az adaptív mintavétel alapértelmezés szerint öt telemetria elemmel van engedélyezve.
  Alapértelmezés szerint 2 AdaptiveSamplingTelemetryProcessors van hozzáadva, és az egyik tartalmazza a mintavételezési esemény típusát, a másik pedig kizárja az esemény típusát a mintavételezésből. Ez a konfiguráció azt jelenti, hogy az SDK megpróbálja a telemetria elemeket öt telemetria-elemre korlátozni, és öt telemetria-elemet egyesíteni az összes többi típustól, így biztosítva, hogy az események a többi telemetria-típustól elkülönítve legyenek. Az eseményeket általában üzleti telemetria használják, és a legvalószínűbb, hogy a diagnosztikai telemetria kötetek nem érinthetik.
  
  Az alábbi ábrán a generált alapértelmezett ApplicationInsights. config fájl látható. A leírtak szerint két különálló AdaptiveSamplingTelemetryProcessor-csomópont van hozzáadva, amelyek közül az egyik az eseménytípus, és egy másik is. A ASP.NET Coreban pontosan ugyanaz az alapértelmezett viselkedés van engedélyezve a kódban. Az alapértelmezett viselkedés módosításához használja a dokumentum korábbi szakaszának példáit.

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

*Egynél többször is telemetria a mintavétel?*

* Nem. A SamplingTelemetryProcessors figyelmen kívül hagyja a mintavételezési megfontolások elemeit, ha az elem már mintavétel alatt áll. Ugyanez érvényes a betöltési mintavételezésre is, amely nem alkalmazza a mintavételezést az SDK-ban már megvizsgált elemekre.

*Miért nem egy egyszerű "az egyes telemetria-típusok X százalékának összegyűjtése"?*

* Habár ez a mintavételezési módszer magas szintű pontosságot biztosít a mérőszámok közelítésében, a diagnosztikai adatok felhasználónként, munkamenetben és kérésben való összekapcsolásának lehetősége a diagnosztika szempontjából kritikus fontosságú. Ezért a mintavétel jobban működik "az összes telemetria elemek összegyűjtése az alkalmazások felhasználóinak X százalékához" vagy "az összes telemetria összegyűjtése az alkalmazás-kérelmek X százalékához" logikával. A kérésekhez nem társított telemetria-elemek (például a háttérben futó aszinkron feldolgozás) esetében a tartalék az összes elem X százalékának összegyűjtése az egyes telemetria-típusokhoz. 

*Az idő múlásával a mintavételezés százalékos változása is megváltozik?*

* Igen, az adaptív mintavételezés fokozatosan megváltoztatja a mintavételezési százalékot a telemetria aktuálisan megfigyelt mennyisége alapján.

*Ha fix sebességű mintavételezést használok, Honnan tudhatom, hogy melyik mintavételi százalék fog működni a legmegfelelőbben az alkalmazáshoz?*

* Az egyik módszer az, hogy az adaptív mintavételezéssel kezdődjön, milyen arányban kell megállapítania (lásd a fenti kérdést), majd váltson a rögzített arányú mintavételezésre az adott díjszabás használatával. 
  
    Ellenkező esetben meg kell találnia. Elemezze az aktuális telemetria-használatot Application Insightsban, figyelje meg az esetlegesen előforduló szabályozást, és becsülje meg az összegyűjtött telemetria mennyiségét. Ez a három bemenet a kiválasztott díjszabási csomaggal együtt arra utal, hogy mennyit érdemes csökkenteni az összegyűjtött telemetria mennyiségét. Előfordulhat azonban, hogy a felhasználók számának növekedése vagy a telemetria mennyiségének más eltolódása érvényteleníti a becslést.

*Mi történik, ha túl alacsonyra állítja a mintavételi százalékot?*

* A túlzottan alacsony mintavételezési százalékos arány (agresszív mintavételezés) csökkenti a közelítések pontosságát, amikor Application Insights megkísérli kompenzálni az adatok vizualizációját az adatmennyiség csökkentése érdekében. A diagnosztikai élmény negatív hatással lehet, mivel előfordulhat, hogy a ritkán fellépő vagy lassú kérelmek közül néhányat nem lehet kivenni.

*Mi történik, ha túl magas mintavételi százalékot konfigurálok?*

* A túl magas mintavételezési arány (nem elég agresszív) beállítása a begyűjtött telemetria mennyiségének elégtelen csökkenését eredményezi. Továbbra is tapasztalhatja a szabályozással kapcsolatos telemetria adatvesztést, és a Application Insights használatának költségei magasabbak lehetnek, mint a túlhasználatos költségek miatt.

*Milyen platformokon használhatok mintavételezést?*

* Ha az SDK nem végez mintavételezést, a betöltési mintavételezés automatikusan megtörténhet egy adott köteten felüli bármely telemetria esetében. Ez a konfiguráció például akkor fog működni, ha a ASP.NET SDK vagy a Java SDK korábbi verziója (1.0.10 vagy korábban) régebbi verzióját használja.
* Ha a ASP.NET SDK-2.0.0 vagy a fenti, illetve a ASP.NET CORE SDK-ban (az Azure-ban vagy a saját kiszolgálón is üzemeltetett) verziót használja, akkor alapértelmezés szerint az adaptív mintavételezést is igénybe veheti, a fent leírtak szerint. A rögzített arányú mintavételezéssel a böngésző SDK automatikusan szinkronizál a mintavételsel kapcsolatos eseményekkel. 
* Ha a Java SDK 2.0.1-es vagy újabb verzióját használja, beállíthatja a ApplicationInsights. xml fájlt a rögzített sebességű mintavételezés bekapcsolásához. A mintavétel alapértelmezés szerint ki van kapcsolva. A rögzített arányú mintavételezéssel a böngésző SDK automatikusan szinkronizál a mintavételsel kapcsolatos eseményekkel.

*Bizonyos ritkán előforduló események mindig látni szeretnék. Hogyan szerezhetem be a mintavételi modult a múltban?*

* Ennek a legjobb módja az, ha egy egyéni [TelemetryInitializer](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)ír, amely a `SamplingPercentage` 100-ra állítja be a megőrizni kívánt telemetria-elemen az alább látható módon. Mivel az inicializálók garantáltan a telemetria processzorok (beleértve a mintavételezést is) előtt futnak, ez biztosítja, hogy az összes mintavételi módszer figyelmen kívül hagyja ezt az elemet a mintavételezési megfontolások alapján.

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

## <a name="next-steps"></a>Következő lépések

* A [szűréssel](../../azure-monitor/app/api-filtering-sampling.md) szigorúbban VEZÉRELHETI az SDK által küldött adatokat.
* Olvassa el a fejlesztői hálózat című cikket a [telemetria optimalizálása Application Insightsával](https://msdn.microsoft.com/magazine/mt808502.aspx).
