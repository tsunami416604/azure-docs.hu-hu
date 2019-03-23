---
title: Az Azure Application Insights telemetria mintavételi |} A Microsoft Docs
description: Hogyan kell fenntartani a ellenőrzése alatt telemetriai adatok mennyisége.
services: application-insights
documentationcenter: windows
author: cijothomas
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/14/2019
ms.reviewer: vitalyg
ms.author: cithomas
ms.openlocfilehash: cd0369f45529082ac929b1d87608204033cd78f6
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370516"
---
# <a name="sampling-in-application-insights"></a>Application Insights-mintavétel

Mintavételi funkciója a [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md). Az ajánlott módszer a csökkentése érdekében a telemetriai forgalom és a tárolás, miközben megőrzik a alkalmazásadatok statisztikailag helyes elemzése. A szűrő elemek kapcsolódó, kiválasztja az, hogy diagnosztikai vizsgálatok során elemek között akkor tud mozogni.
Metrika számát a portálon jelennek meg, amikor azok vannak renormalized fiók mintavételi figyelembe. Így minimálisra csökkenti a statisztikák gyakorolt hatását.

Mintavételezés csökkenti a forgalom és az költségeket, és elkerülheti a szabályozás.

## <a name="in-brief"></a>Röviden:

* Mintavételi őrzi meg az 1 *n* rögzíti, és elveti a többi. Például akkor előfordulhat, hogy egy az öt események, a mintavételi ráta 20 %-os megőrzése. 
* A legújabb verzió az ASP.NET és ASP.NET Core szoftverfejlesztői készletek (SDK-k) alapértelmezés szerint engedélyezve van a adaptív mintavételezés.
* Mintavétel manuálisan is beállíthatja. Ez lehet beállítani a portálon a *használat és becsült költségek lapon*, az ASP.NET SDK-ban az ApplicationInsights.config fájlban, az ASP.NET Core SDK kódot keresztül vagy a Java SDK-t az applicationinsights.xml fájlt a fájlt.
* Egyéni események és a szükséges, hogy események egy meghatározott készletének megőrzött vagy elvetett együtt jelentkezik, ha az események OperationId ugyanazzal az értékkel kell rendelkeznie.
* A mintavételi osztó *n* tulajdonságában rekordokban levő jelentett `itemCount`, amely a Keresés a rövid név "kérések száma" vagy "események száma" alatt jelenik meg. `itemCount==1`Amikor mintavételi nem szerepel a művelet.
* Ha Analytics-lekérdezések írása, akkor [figyelembevétele mintavételi](../../azure-monitor/log-query/aggregations.md). Különösen helyett egyszerűen Rekordok számlálása, használjon `summarize sum(itemCount)`.

## <a name="types-of-sampling"></a>Mintavételi típusai

Vannak más mintavételi három módszer:

* **Az adaptív mintavételezési** automatikusan igazodik az ASP.NET/ASP.NET Core-alkalmazás az SDK által küldött telemetriai adatok mennyisége. Ez az az alapértelmezett a mintavételezés és újabb verziók esetében az ASP.NET Web SDK v 2.0.0-beta3 és Microsoft.ApplicationInsights.AspNetCore SDK v 2.2.0-beta1 és újabb verziók esetében.  Az adaptív mintavételezési jelenleg csak az ASP.NET kiszolgálóoldali telemetria érhető el.

* **Rögzített mintavételi** csökkenti az ASP.NET- vagy ASP.NET Core- vagy Java kiszolgáló és a felhasználók böngészőinek küldött telemetriai adatok mennyisége. A arány beállításához. Az ügyfél és kiszolgáló szinkronizálni fogja a mintavételi úgy, hogy a keresési navigálhat kapcsolódó Lapmegtekintések és a kérések között.

* **Betöltési mintavételt** működik az Azure Portalon. Elveti a mintavételi ráta beállított, az alkalmazásból érkező telemetriai adatok némelyike. Nem csökkenthető a telemetriai forgalom az alkalmazásból küldött, de segít megvédeni a havi kvótán belül. A fő betöltési mintavételt előnye, hogy képes-e a mintavételi ráta beállítását az alkalmazás újbóli telepítése nélkül. Betöltési mintavételt egységesen összes kiszolgálók és ügyfelek esetében működik.

Ha az adaptív vagy rögzített méretű arány mintavételi művelet, a betöltési mintavételt le van tiltva.


## <a name="adaptive-sampling-in-your-aspnetaspnet-core-web-applications"></a>Az adaptív mintavételezés a a ASP.NET/ASP.NET Core-webalkalmazások

Az adaptív mintavételezési érhető el az Application Insights SDK for ASP.NET v 2.0.0-beta3 vagy újabb, Microsoft.ApplicationInsights.AspNetCore SDK v 2.2.0-beta1 és újabb verziók, és alapértelmezés szerint engedélyezve van.

Az adaptív mintavételezési hatással van az Application Insights szolgáltatás végpontjának a webalkalmazás-kiszolgáló által küldött telemetriai adatok mennyisége. A kötetet, hogy a forgalom a megadott maximális sebesség belül automatikusan módosul, és a beállítás védi `MaxTelemetryItemsPerSecond`. Ha az alkalmazást hoz létre egy kis mennyiségű telemetriai adatokat, például hibakeresést, vagy az alacsony kihasználtságú miatt elemek nem lekérése mintavételezés, amíg a kötet nem éri el `MaxTelemetryItemsPerSecond`. Mintavételi ráta telemetriai nő a mennyisége, hogy a célkötet módosul.

A célkötet eléréséhez a generált telemetriai adatokat néhány elveti. De mintavételi más típusú, például az algoritmus megtartja a kapcsolódó telemetriai adat elemek. Például ha a telemetria keresése a vizsgálat lesz találta meg a kérelem egy adott kivétel kapcsolatos.

Metrika számolja, például a kérések aránya és kivételek sebessége módosítani a mintavételi ráta kiegyenlítése érdekében, hogy azok Metrikaböngésző körülbelül megfelelő értékek megjelenítése.

## <a name="configuring-adaptive-sampling-for-aspnet-applications"></a>Az ASP.NET-alkalmazások az adaptív mintavétel konfigurálása

[Ismerje meg,](../../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-core-applications) adaptív mintavétel az ASP.NET Core-alkalmazások konfigurálásáról. 

A [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), módosíthatja a több paramétert a `AdaptiveSamplingTelemetryProcessor` csomópont. A látható adatok az alapértelmezett értékeket:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    A cél arány, amely a célja az adaptív algoritmus **minden kiszolgáló gazdagépen**. Ha a webalkalmazás fut, számos gazdagépeken, csökkentse ezt az értéket, hogy lépje a forgalmat az Application Insights portálon, a cél aránya.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Az időközt, amely telemetriai adatokat jelenlegi sebessége újraértékeli van. Értékelés mozgóátlaga szerint történik. Előfordulhat, hogy szeretné Rövidítse le ezt az időközt, ha a telemetria hirtelen adatlöketekkel.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Mintavétel százalékos értéket módosításokat, ha hamarosan hogyan után azt engedélyezett az alacsonyabb procento vzorkování ismét az kevesebb adatot rögzítéséhez.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Mintavétel százalékos értéket módosításokat, ha hamarosan hogyan után azt engedélyezettek újra a további adatok rögzítése a mintavételi arány növelése érdekében.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Mintavétel százaléka változik, ahogy mi az a minimális érték, hogy jogosult-e beállítva.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Mintavétel százaléka változik, ahogy mi az a maximális érték, hogy jogosult-e beállítva.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    A mozgó átlag kiszámításakor a súly a legfrissebb értéket rendelni. 1-nél kisebb vagy egyenlő, érték használata. A kisebb értékek a módosításokat az algoritmus a hirtelen kevesebb reaktív.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Ha az alkalmazás elkezdte rendelt értéket. Érték nem csökkenti, hibakeresés során.

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Pontosvesszővel tagolt listáját, amelyek nem szeretné, hogy mintát venni. Felismert típusok a következők: Nyomkövetési esemény, kivétel, lapnézetben szereplő, a kérelem, függőség. A megadott típusú összes példánya továbbított; a típusok, amelyek nincsenek megadva történik az adatgyűjtés.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Pontosvesszővel tagolt listáját kell mintát venni kívánt típusokat. Felismert típusok a következők: Nyomkövetési esemény, kivétel, lapnézetben szereplő, a kérelem, függőség. A megadott típusok vonnak. mindig az összes példányát a más típusú, a rendszer nem továbbít.


**Kikapcsolhatják a** adaptív mintavétel, távolítsa el az AdaptiveSamplingTelemetryProcessor csomópont applicationinsights-config.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternatív: Adaptív mintavétel konfigurálása a code-ban

Ahelyett, hogy a mintavétel paraméter beállítása a .config fájlban, programozott módon állíthatja be ezeket az értékeket.

1. Távolítsa el az összes a `AdaptiveSamplingTelemetryProcessor` a .config fájlt a csomópontokon.
2. Az alábbi kódrészlet használatával adaptív mintavételezés konfigurálhatja.

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

([Megismerheti a telemetriai adatok processzorok](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

Külön-külön is módosíthatja a mintavételi ráta az egyes telemetriai adatokat, vagy még akkor is kizárhat bizonyos típusú minden mintavételezése a. 

*C#*

```csharp
    // The following configures adaptive sampling with 5 items per second, and also excludes DependencyTelemetry from being subject to sampling.
    builder.UseAdaptiveSampling(maxTelemetryItemsPerSecond:5, excludedTypes: "Dependency");
```

## <a name="configuring-adaptive-sampling-for-aspnet-core-applications"></a>Az adaptív mintavételezési az ASP.NET Core-alkalmazások konfigurálása.

Nincs nem `ApplicationInsights.Config` az ASP.NET Core-alkalmazások, így minden konfigurációs meghatározhatják kódot.
Adaptív mintavételezés minden ASP.NET Core-alkalmazás alapértelmezés szerint engedélyezve van. Tiltsa le, vagy testre szabható a mintavételi viselkedése.

### <a name="turning-off-adaptive-sampling"></a>Az adaptív mintavételezés kikapcsolásával

Az alapértelmezett mintavételi szolgáltatás letiltható az Application Insights szolgáltatást, a metódus hozzáadásakor ```ConfigureServices```révén ```ApplicationInsightsServiceOptions```:

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

A fenti kód mintavételezés funkció letiltja. További testreszabási lehetőségek a mintavétel hozzáadása az alábbi lépésekkel.

### <a name="configure-sampling-settings"></a>Mintavételi beállítások konfigurálása

A bővítmény módszerekkel ```TelemetryProcessorChainBuilder``` mintavételi viselkedésének testreszabása az alább látható módon.

> [!IMPORTANT]
> Ha ezt a módszert használja, konfigurálja a mintavételi, ellenőrizze, hogy aiOptions.EnableAdaptiveSampling használandó = false; AddApplicationInsightsTelemetry()-beállításokat.

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    var configuration = app.ApplicationServices.GetService<TelemetryConfiguration>();

    var builder = configuration .TelemetryProcessorChainBuilder;
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

**Ha a fenti módszerrel a mintavétel konfigurálására ügyeljen arra, hogy használjon ```aiOptions.EnableAdaptiveSampling = false;``` AddApplicationInsightsTelemetry()-beállításokat.**

## <a name="fixed-rate-sampling-for-aspnet-aspnet-core-and-java-websites"></a>Rögzített mintavételi ASP.NET, ASP.NET Core és a Java-webhelyekhez

Rögzített a mintavételezés csökkenti a webkiszolgálót és a böngészők által küldött forgalmat. Ellentétben az adaptív mintavételezés csökkenti gyakorisággal úgy döntött, Ön által rögzített telemetria. Azt is szinkronizálja az ügyfél és kiszolgáló mintavételi úgy, hogy a kapcsolódó elemek megmaradnak – például ha egy lapmegtekintés kifejezést a keresőmezőbe, annak a kapcsolódó kérelem.

Más mintavételi technikák, például ez is megtartja a kapcsolódó elemek. Minden HTTP-kérelem esemény, a kérelem és a kapcsolódó események rendszer elvetett vagy továbbított együtt.

A Metrikaböngészőben díjszabás, például a kérések és kivételek száma vannak faktor szorzata kompenzálják a mintavételi ráta, hogy megközelítőleg megfelelő legyenek.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Az ASP.NET-ben rögzített mintavétel konfigurálása

1. **Tiltsa le az adaptív mintavételezési**: A [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), távolítsa el, vagy tegye megjegyzésbe a `AdaptiveSamplingTelemetryProcessor` csomópont.

    ```xml

    <TelemetryProcessors>

    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    ```

2. **A rögzített mintavételi modul engedélyezése.** Adja hozzá a kódrészletet [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```
   ### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Alternatív: a kiszolgáló kódjában található rögzített mintavételi engedélyezése
    
    Ahelyett, hogy a mintavétel paraméter beállítása a .config fájlban, programozott módon állíthatja be ezeket az értékeket. 

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
([Megismerheti a telemetriai adatok processzorok](../../azure-monitor/app/api-filtering-sampling.md#filtering).)

### <a name="configuring-fixed-rate-sampling-in-aspnet-core"></a>Az ASP.NET Core rögzített mintavétel konfigurálása

1. **Tiltsa le az adaptív mintavételezési**:  A metódus módosíthatók ```ConfigureServices```révén ```ApplicationInsightsServiceOptions```:

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

2. **A rögzített mintavételi modul engedélyezése.** A metódus módosíthatók ```Configure``` az alábbi kódrészletben látható módon:

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

### <a name="configuring-fixed-rate-sampling-in-java"></a>Java-környezetben rögzített mintavétel konfigurálása ###

1. Töltse le és a webalkalmazások konfigurálása a legújabb [application insights java SDK](../../azure-monitor/app/java-get-started.md)

2. **A rögzített mintavételi modulok** ApplicationInsights.xml fájl ad hozzá az alábbi kódrészletet.

    ```XML
        <TelemetryProcessors>
            <BuiltInProcessors>
                <Processor type = "FixedRateSamplingTelemetryProcessor">
                    <!-- Set a percentage close to 100/N where N is an integer. -->
                    <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                    <Add name = "SamplingPercentage" value = "50" />
                </Processor>
            </BuiltInProcessors>
        <TelemetryProcessors/>
    ```

3. Megadhat vagy mintavételi a processzor "FixedRateSamplingTelemetryProcessor" címkére a következő címkék használatával adott típusú telemetriát kizárása
    ```XML
        <ExcludedTypes>
            <ExcludedType>Request</ExcludedType>
        </ExcludedTypes>

        <IncludedTypes>
            <IncludedType>Exception</IncludedType>
        </IncludedTypes>
    ```

A foglalt vagy mintavételi zárva telemetriai típusok a következők: Függőségi, Event, kivétel, oldalmegtekintés, kérelem és nyomkövetési.

> [!NOTE]
> A mintavételi arányt, amely közel 100/N van, ahol N az egész százalékos válassza.  Mintavétel jelenleg nem támogatja a többi értéket.
> 
> 

<a name="other-web-pages"></a>



## <a name="ingestion-sampling"></a>Betöltési mintavételt

Az ilyen típusú mintavételi működik, a pont, ahol a webkiszolgáló, böngészők és eszközökről érkező telemetriai adatok eléri-e az Application Insights szolgáltatás végpontját. Ezzel nem csökkenthető a telemetriai forgalom, az alkalmazás által küldött, bár ez csökkentheti a feldolgozott és őrzi meg (és fizetnie) az Application Insights.

Mintavétel az ilyen típusú használja, ha az alkalmazás gyakran havi kvótáját keresztül haladnak, és nem rendelkezik a mintavételi SDK-alapú típusú valamelyikét használja. 

A mintavételi ráta beállítását a a használat és becsült költségek lapot:

![Az alkalmazás áttekintése panelen kattintson a beállítások, a kvótát, a mintákat, majd válassza ki a mintavételi ráta, és kattintson a frissítés gombra.](./media/sampling/04.png)

Mintavételi más típusú, például az algoritmus megtartja a kapcsolódó telemetriai adat elemek. Például ha a telemetria keresése a vizsgálat lesz találta meg a kérelem egy adott kivétel kapcsolatos. Metrika számolja, például a kérések aránya és kivételek sebessége megfelelően megmaradnak.

Mintavétellel elvesznek adatpontok nem érhetők el minden olyan Application Insights szolgáltatásban például [folyamatos exportálás](../../azure-monitor/app/export-telemetry.md).

Betöltési mintavételt SDK-alapú adaptív vagy rögzített mintavételi művelet közben nem működik. Az adaptív mintavételezési alapértelmezés szerint engedélyezve van, amikor ASP.NET/ASP.NET Core SDK-t a Visual Studióban engedélyezve van, vagy az Azure-webalkalmazás-bővítmény vagy állapotfigyelő használatával engedélyezve van, és a betöltési mintavételt le van tiltva. Ha az SDK a mintavételi ráta kevesebb mint 100 %-os (vagyis) elemek vannak mintavételezése), majd az Ön által beállított feldolgozási mintavételi ráta a rendszer figyelmen kívül hagyja.

> [!WARNING]
> Az érték a csempén látható, amely a betöltési mintavételt beállított értékét jelöli. Ha SDK mintavételi van folyamatban, nem jelentik a tényleges mintavételi ráta.
>
>
## <a name="sampling-for-web-pages-with-javascript"></a>A JavaScript weblapokon mintavétel
Weblapok rögzített mintavételi bármely kiszolgálóról is beállíthatja. 

Amikor Ön [a weblapok konfigurálása az Application Insights](../../azure-monitor/app/javascript.md), módosíthatja a JavaScript-kódrészletet képest az Application Insights portálon. (Az ASP.NET-alkalmazások, a kódrészlet általában kerül _Layout.cshtml.)  Például a sor beszúrása `samplingPercentage: 10,` előtt a kialakítási kulcsot:

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

A mintavételi arányt, amely közel 100/N van, ahol N az egész százalékos válassza.  Mintavétel jelenleg nem támogatja a többi értéket.

Ha rögzített mintavételi a kiszolgálón is engedélyezi, az ügyfelek és a kiszolgáló szinkronizálja úgy, hogy a keresési navigálhat kapcsolódó Lapmegtekintések és a kérések között.

## <a name="when-to-use-sampling"></a>Mikor érdemes használni a mintavételi?

Adaptív mintavételezés a legújabb .NET és .NET Core SDK-k automatikusan engedélyezve van. Melyik verzióját használja az SDK-t, függetlenül attól, hogy a rendszer mintát a gyűjtött adatok az Application Insights betöltési mintavételt is engedélyezheti.

Nincs mintavételi alapértelmezés szerint engedélyezve van a Java SDK-ban. Jelenleg csak a támogatott rögzített arány mintavételt. Az adaptív mintavételezési nem támogatott a Java SDK-t.

Általában a legtöbb kis és közepes méretű alkalmazásokhoz nem szükséges mintavételt. A legfontosabb diagnosztikai adatokat és a legpontosabb statisztikák azok kaphatják meg a felhasználói tevékenységekre vonatkozó adatok gyűjtése. 

Mintavételi fő előnyei a következők:

* Application Insights szolgáltatás elhagyta a(z) ("szabályozások") az adatpontok az alkalmazás rövid küld telemetriai nagyon nagy mértékű időintervallum. 
* Belül tartani a [kvóta](../../azure-monitor/app/pricing.md) adatpontok a tarifacsomagot. 
* A telemetriai adatok gyűjtésére a hálózati forgalom csökkentése érdekében. 

### <a name="which-type-of-sampling-should-i-use"></a>Milyen típusú mintavételi használjam?

**Használja a betöltési mintavételt, ha:**

* Gyakran végignézzük a telemetriai adatok havi kvótáját.
* Egy, az SDK-t, amely nem támogatja a mintavételi – például az ASP.NET-verziók 2-nél korábbi verziót használ.
* Túl sok telemetriát kap a felhasználók webböngészővel.

**Használja a rögzített mintavételi, ha:**

* Az Application Insights SDK-t használja az ASP.NET web services 2.0.0-s verzió vagy újabb, vagy a Java SDK v2.0.1 vagy újabb, és
* Azt szeretné, ügyfél és kiszolgáló közötti mintavétel szinkronizált úgy, hogy mikor lekérdezéskapcsolatokról események [keresési](../../azure-monitor/app/diagnostic-search.md), az ügyfél és kiszolgáló, mint például a lapmegtekintések és a http-kérelmekre a kapcsolódó események között akkor tud mozogni.
* Biztos a megfelelő mintavételi arányt, az alkalmazás számára. Elég nagy a pontos metrika beolvasása kell lennie, de alább aránya, amely meghaladja a díjszabási kvótát és a szabályozási korlátok. 

**Az adaptív mintavételezési használja:**

A mintavételezési többi formáját használati feltételei csak akkor érvényesíthetők, ha az adaptív mintavételezési javasoljuk. Ez a beállítás a ASP.NET/ASP.NET Core kiszolgálóoldali SDK alapértelmezés szerint engedélyezve van. Azt nem csökkenti forgalom mindaddig, amíg egy bizonyos minimális sebességet, ezért alacsony használatú helyeket nem érinti.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Honnan tudhatom meg, hogy mintavételi van folyamatban?

Fedezze fel a tényleges mintavételi ráta, függetlenül attól, hol van alkalmazva, használja az [elemzési lekérdezés](../../azure-monitor/app/analytics.md) a:

```
union requests,dependencies,pageViews,browserTimings,exceptions,traces
| where timestamp > ago(1d)
| summarize RetainedPercentage = 100/avg(itemCount) by bin(timestamp, 1h), itemType
```

Ha bármilyen RetainedPercentage 100-nál kisebb, majd az adott elem van mintavételezése.

**Az Application Insights nem minta munkamenet, metrikák és teljesítményszámlálók telemetriatípusok bármely mintavételi fent leírt módszerek a. Ezek a típusok mindig ki vannak zárva a mintavételi, lehet, hogy a pontosság csökkenését magas nemkívánatos ezen telemetriai típusok esetén**

## <a name="how-does-sampling-work"></a>Hogyan működik a mintavételi?

Rögzített mintavételezés funkció az ASP.NET verziót 2.0.0 SDK-t és a Java SDK verziója 2.0.1 és újabb verziók esetében. Az adaptív mintavételezési funkciója SDK 2.0.0-s és újabb verziók esetében az ASP.NET-verziókban. Betöltési mintavételt egy szolgáltatás, Application Insights szolgáltatás, és lehet a műveletet, ha az SDK nem hajt végre mintavételt.

A mintavételezési algoritmus úgy dönt, hogy melyik telemetriai elemek dobja el, és melyeket tartani (hogy az SDK-ban vagy az Application Insights szolgáltatásban). A mintavételi döntést különböző szabályok, amelyek célja az, hogy minden egymáshoz adatpont sérülése nélkül, egy diagnosztikai információt biztosít az Application Insights gyakorlatban is használható és megbízható még egy kisebb adatkészletet a fenntartása megőrzése alapul. Például ha a sikertelen kérelmek az alkalmazás elküldi a további telemetria elemek (például kivétel, és ezt a kérést a naplózott nyomkövetési), mintavételi nem kettéosztjuk a kérelem és egyéb telemetriát. Azt tartja, vagy megszakítja őket minden egy helyen. Ennek eredményeképpen ha a kérelem részletes adatainak az Application Insights, bármikor megtekintheti a kérelem és a kapcsolódó telemetria elemek. 

A mintavételi döntést a kérést, ami azt jelenti, hogy egy adott művelethez tartozó összes telemetriai elem van-e vagy megőrzi, vagy eldobott művelet azonosítója alapján. A telemetriai adatok elemek, amelyek nem rendelkeznek a művelet azonosítója (a példában telemetriai elemek nincs http-környezetben az aszinkron szálak jelentett) set mintavételi egyszerűen százalékos arányában minden típusú elemek telemetriai adatokat rögzíti. 2.5.0-beta2 .NET SDK, és az ASP.NET Core SDK 2.2.0-beta3, mielőtt a mintavételi döntést az alkalmazásokhoz, amelyek meghatározzák a "user" Felhasználóazonosító kivonatát alapul (azaz leggyakoribb webes alkalmazások). A felhasználók (például a web services) nem meghatározó alkalmazástípusok a mintavételi döntést a művelet azonosítója a kérelem volt alapul.

Telemetriai adatokat vissza szerkesztésekor az Application Insights szolgáltatás a metrikák a meghiúsult lépések kompenzációjához a hiányzó adatpontokat a gyűjteményt, idején használt azonos procento vzorkování állítja be. Ezért ha megnézi a telemetriát az Application insights szolgáltatásban, a felhasználók jelennek meg, amelyek nagyon közel valós számok statisztikailag helyes becsült dátumokkal.

A előállításához pontosságát nagymértékben függ a konfigurált mintavételi arányt. A pontosság is, az alkalmazásokat, amelyek nagy mennyiségű, több felhasználó általában hasonló kérelmek kezelésére nő. Másrészről alkalmazásokhoz, melyek nem működnek az komoly terhelést, mintavételi nem szükséges, mivel ezek az alkalmazások általában küldhet a telemetriai adatok kihívásainak a kvótán belül anélkül, hogy ez az adatvesztést a szabályozás. 

> [!WARNING]
> Az Application Insights nem minta metrikák és a munkamenetek telemetriatípusok. Lehet, hogy a pontosság csökkenését magas nemkívánatos ezen telemetriai típusok esetén.
> 

### <a name="adaptive-sampling"></a>Az adaptív mintavétel

Az adaptív mintavételezési hozzáad egy összetevő, amely a jelenlegi átviteli SDK figyeli, és beállítja a mintavételi arányt, próbálja meg a célként megadott maximális sebesség belül. A módosítás rendszeres időközönként a rendszer újraszámítja, és a kimenő átviteli sebességet mozgóátlagát alapul.

## <a name="sampling-and-the-javascript-sdk"></a>Mintavételi és a JavaScript SDK

Az ügyféloldali (JavaScript) SDK-t a kiszolgálóoldali SDK-val együtt rögzített mintavételi részt vesz. A finomhangolt lapok csak küld ügyféloldali telemetria a ugyanazokat a felhasználókat, amelynek a kiszolgálóoldali "a minta". a döntésről végzett A logikai célja között – és a kiszolgáló-ügyféloldalon felhasználói munkamenet sértetlenségének megőrzéséhez. Ennek eredményeképpen az Application Insights bármely adott telemetriai elem található minden egyéb telemetriai elem a felhasználó vagy a munkamenet. 

*Saját ügyfél és a kiszolgálóoldali telemetria ne jelenjen meg koordinált minták, hogy fent leírtak alapján.*

* Győződjön meg arról, hogy a mintavétel rögzített, mind a kiszolgáló és az ügyfél engedélyezve.
* Győződjön meg arról, hogy az SDK-verziója-e a 2.0-s vagy újabb.
* Ellenőrizze, hogy az azonos procento vzorkování beállíthatja az ügyfélen és kiszolgálón.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

*Mi az az alapértelmezett mintavételi viselkedésének az ASP.NET és ASP.NET Core SDK-t?*

* Ha a legfrissebb verzióit a fenti SDK-t használ, az adaptív mintavételezési alapértelmezés szerint engedélyezve van az öt telemetriai tétel / másodperc.
  2 AdaptiveSamplingTelemetryProcessors alapértelmezés szerint hozzáadva és a egy mintavételi eseménytípus magában foglalja, és a másik eseménytípus nem tartalmazza a mintavételi. Ez a konfiguráció azt jelenti, hogy az SDK megpróbálja öt telemetriai elem esemény típusú telemetriai elemeket, és minden más típusú kombinált, ezzel biztosítva, hogy eseményeket külön-külön vannak mintavételezés a más Telemetriai öt telemetriai elem korlátozza. Események általában használhatók üzleti telemetriát, és valószínűleg nem befolyásolja a telemetria diagnosztikája kötetek.
  
  Az alábbiakban látható a létrehozott alapértelmezett ApplicationInsights.Config fájlban. Leírt, nincsenek két külön AdaptiveSamplingTelemetryProcessor csomópont hozzáadott, egyet kivéve eseménytípusok, és a egy másikba felvegyük. Az ASP.NET Core a pontos alapértelmezett viselkedést engedélyezve van a kódban. Ez az alapértelmezett viselkedés módosításához használja a dokumentum korábbi szakaszában a példákat.

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

*Képes telemetriai mintát venni egynél többször?*

* Nem. SamplingTelemetryProcessors figyelmen kívül a mintavétel szempontok, ha az elem már mintavételezés elemét. Ugyanez igaz a betöltési mintavételt, amely nem érvényes mintavételi már az adatgyűjtés az SDK-t az elemeket, akkor is. "

*Miért nem mintavételi egy egyszerű "collect X egyes telemetriatípus százaléka"?*

* Bár a mintavételezéses módszer biztosítja a magas szintű metrika becsült dátumokkal pontosságát, megszűnését összehangolhatók felhasználónként, munkamenet, és a kérést, ami kritikus fontosságú diagnosztikai diagnosztikai adatokat. Ezért mintavételezésének működése jobban "az összes telemetriai elemek összegyűjtése a %-OS X alkalmazáshasználók" vagy "X alkalmazás kérések százalékos aránya az összes telemetriai adat gyűjtése" logikát. A telemetriai adatok elemek nincs társítva a kérelmek (például a háttérben történő aszinkron feldolgozás), a tartalék, hogy "az összes elem az egyes telemetriai %-OS X collect." 

*A mintavételi arányt idővel változhat?*

* Igen, az adaptív mintavételezési fokozatosan módosítja a mintavételi arányt, a telemetriai adatok jelenleg megfigyelt mennyisége alapján.

*Rögzített mintavételi használata, Honnan tudható, mely mintavételi százalékos működnek a legjobban az alkalmazásomhoz?*

* Egyik módja van a következővel kell kezdődnie adaptív mintavételezés, ismerje meg, milyen értékelés rendezi a (lásd a fenti kérdésnél), és váltson rögzített mintavétel, hogy a díj alapján. 
  
    Ellenkező esetben meg kell becslés alapján. Elemezheti az aktuális telemetriai adatok használatát az Application Insights, figyelje meg minden olyan szabályozási történik, és megbecsülheti a gyűjtött telemetriai adatok mennyisége. Ezeket a három bemeneteket, és a kiválasztott tarifacsomaghoz tartozó, javasoljuk, hogy mennyit érdemes csökkenteni az összegyűjtött telemetriai adatok mennyisége. Azonban a felhasználók számának növelését, vagy valamilyen más shift a telemetriai adatok mennyisége előfordulhat, hogy érvényteleníti az Ön becsült költsége.

*Mi történik, ha túl alacsony procento vzorkování konfigurálni?*

* A túl alacsony procento vzorkování (rendelkező agresszív mintavételi) csökkenti a becsült dátumokkal pontosságát, amikor az Application Insights esetében történő kompenzálás adatok mennyiségi csökkentésére az adatok a Vizualizáció megpróbál. Is diagnosztikai információt biztosít negatív megváltozhatnak, mivel egyes ritkán meghibásodott vagy lassú kérelmek lehet mintát venni ki.

*Mi történik, ha túl magas procento vzorkování konfigurálható?*

* A gyűjtött telemetriai adatok mennyisége nem elegendő csökkenést konfigurálása (nem agresszív elég) túl magas procento vzorkování eredménye. Akkor is előfordulhatnak, szabályozás kapcsolatos telemetriai adatokat az adatvesztést, és lehet, hogy az Application Insights költségeinek magasabb, mint amennyi tervezett miatt kerettúllépési díjfizetést tesz szükségessé.

*Milyen platformokon használható mintavételi?*

* Betöltési mintavételt automatikusan történik a semmilyen telemetriai adatot fent egy bizonyos köteten, ha az SDK nem hajt végre mintavételt. Ez a konfiguráció akkor működik, például egy régebbi verziója, az ASP.NET SDK vagy a Java SDK(1.0.10 or before) előző verziójának használatakor.
* Használata az ASP.NET SDK-verziókra 2.0.0-s vagy újabb, vagy az ASP.NET CORE SDK 2.2.0-ás verzió vagy újabb (az Azure-ban vagy a saját kiszolgáló található), adaptív mintavételezés alapértelmezés szerint megjelenik, de a fent leírt rögzített válthat. A böngésző SDK rögzített mintavételezése esetén, az automatikusan szinkronizálja a kapcsolódó események mintát. 
* Ha a Java SDK 2.0.1 verzióját használja, vagy a fenti konfigurálhatja az applicationinsights.xml fájlt rögzített arány mintavételezés bekapcsolása. Mintavételi alapértelmezés szerint ki van kapcsolva. A böngésző SDK rögzített mintavételezése esetén, az automatikusan szinkronizálja a kapcsolódó események mintát.

*Nincsenek az egyes ritka események, hogy mindig szeretné. Hogyan kaphatok őket a mintavételi modul korábbi?*

* Ennek érdekében a legjobb módszer az, hogy egyéni írási [TelemetryProcessor](../../azure-monitor/app/api-filtering-sampling.md#filtering), mely beállítja a `SamplingPercentage` a szereplő telemetriai elem a 100 szeretne megőrzött, ahogy az alábbi. Ez biztosítja, hogy az összes mintavételi technikák figyelmen kívül hagyja ezt az elemet a mintavételi beavatkozást.

```csharp
    if(somecondition)
    {
        ((ISupportSampling)item).SamplingPercentage = 100;
    }
```

## <a name="next-steps"></a>További lépések

* [Szűrés](../../azure-monitor/app/api-filtering-sampling.md) az SDK-t küld, további szigorú ellenőrzési biztosíthat.