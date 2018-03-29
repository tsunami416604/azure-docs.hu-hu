---
title: Az Azure Application Insights telemetria mintavételi |} Microsoft Docs
description: Hogyan kell fenntartani a vezérlése alatt telemetriai adatok mennyiségét.
services: application-insights
documentationcenter: windows
author: vgorbenko
manager: carmonm
ms.assetid: 015ab744-d514-42c0-8553-8410eef00368
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: mbullwin
ms.openlocfilehash: 300b9b7786c17972c5c48df7e5b6d28491adc095
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2018
---
# <a name="sampling-in-application-insights"></a>Application Insights-mintavétel


A mintavétel az szolgáltatása [Azure Application Insights](app-insights-overview.md). Az ajánlott módszer a telemetriai adatok és a tárolás, az alkalmazásadatok statisztikailag megfelelő elemzési adatainak megőrzése mellett csökkentése érdekében. A szűrő elemek kapcsolódó, választja ki, így navigálhat diagnosztikai vizsgálatok során elemek között.
Metrika számát a portálon jelennek meg, amikor azok figyelembe véve a mintavételi minimalizálása érdekében a hatása, ha a statisztikákat a rendszer renormalized.

Mintavételi csökkenti a forgalom és az adatok költségeket, és segít elkerülni a sávszélesség-szabályozás.

## <a name="in-brief"></a>Röviden:
* Mintavételi őrzi meg az 1 *n* rögzíti, és elveti a többi. Például akkor lehet, hogy továbbra is 1: 5 események, a mintavételi ráta 20 %-os. 
* Mintavételi automatikusan történik, ha az alkalmazás nagy mennyiségű telemetriai adatokat küld az ASP.NET web server apps.
* Azt is beállíthatja, hogy a mintavételi manuálisan, vagy a portál az árképzést ismertető oldalra; vagy az ASP.NET SDK-t a .config fájl; vagy a Java SDK ApplicationInsights.xml fájl is a a hálózati forgalom csökkentése érdekében.
* Ha egyéni események naplózása, és győződjön meg arról, hogy egy megőrzött vagy elvetett együtt szeretné, győződjön meg arról, hogy rendelkeznek-e az azonos OperationID azonosítójú érték.
* A mintavételi osztó *n* tulajdonságában rekordokban levő jelentett `itemCount`, amelyen a Keresés a rövid név "kérelem száma" vagy "események száma" alatt jelenik meg. Ha a mintavételi nincs a művelet, `itemCount==1`.
* Írás Analytics lekérdezések, akkor [mintavételi figyelembe](app-insights-analytics-tour.md#counting-sampled-data). Különösen helyett egyszerűen számbavételi rekordok, használjon `summarize sum(itemCount)`.

## <a name="types-of-sampling"></a>Mintavételi típusai
Alternatív mintavételi három módszer áll rendelkezésre:

* **Adaptív mintavételi** automatikusan beállítja az SDK-t az ASP.NET-alkalmazás által küldött telemetriai adatok mennyiségét. SDK-v 2.0.0-beta3 kezdve ez a mintavételi mód az alapértelmezett. Adaptív mintavételi jelenleg csak az ASP.NET kiszolgálóoldali telemetriai használható. 
* **Rögzített mintavételi** az ASP.NET- vagy Java kiszolgálóról, és a felhasználók böngészőjének a telemetriai adatok mennyiségét csökkenti. A sebesség beállítása. Az ügyfél és kiszolgáló szinkronizálja a mintavételi, a keresés, navigálhat kapcsolódó Lapmegtekintések és kérések között.
* **Adatfeldolgozást mintavételi** működik az Azure portálon. Törli az alkalmazást, a mintavételi ráta, amely nem érkezik telemetriai adatok némelyike. Az alkalmazásból küldött telemetriai forgalom nem csökkenthető, de lehetővé teszi a havi kvótán belül. Adatfeldolgozást mintavételi fő előnye, hogy a mintavételi ráta állíthatja be az alkalmazás üzembe helyezésével, és minden kiszolgálók és ügyfelek egységesen működik. 

Ha az adaptív vagy rögzített arány mintavételi művelet, adatfeldolgozást mintavételi le van tiltva.

## <a name="ingestion-sampling"></a>Adatfeldolgozást mintavétel
Ezt az űrlapot mintavételi a pont, ahol a webkiszolgáló böngészők és eszközök telemetriai eléri az Application Insights szolgáltatás végpont működik. Ezzel nem csökkenthető az alkalmazásból küldött telemetriai-forgalom, bár ez csökkentheti a feldolgozott és maradnak (majd díjat) az Application Insights által.

Az ilyen mintavételi típusú, ha az alkalmazás gyakran kerül felett a havi kvótát, és nem kell a mintavételi SDK-alapú típusú bármelyikével lehetőséget. 

A mintavételi ráta beállítását a kvóták és árképzési panel:

![Alkalmazás – áttekintés paneljén kattintson a beállítások, a kvóta, a mintákat, majd válassza ki a mintavételi ráta, és kattintson a frissítés gombra.](./media/app-insights-sampling/04.png)

Mintavételi más típusú, mint az algoritmus megtartja a kapcsolódó telemetriai elemeket. Például a telemetriai adatokat, a Keresés most vizsgálatakor ellenőrizze, hogy képes lesz megkereshető egy adott kivételhez kapcsolódó a kérelem. A metrika számít például kérelmek aránya, és kivétel arány megfelelően megőrződnek.

Hogy a rendszer elveti a mintavétellel már nem érhetők el bármilyen Application Insights szolgáltatás például [a folyamatos exportálás](app-insights-export-telemetry.md).

Adatfeldolgozást mintavételi nem működik, amikor az SDK-alapú adaptív vagy rögzített mintavételi művelet van. Vegye figyelembe, hogy adaptív mintavételi alapértelmezés szerint engedélyezve van az ASP.NET SDK engedélyezve van, a Visual Studio vagy állapotfigyelő segítségével, és adatfeldolgozást mintavételi le van tiltva. Ha a mintavételi ráta az SDK-t a 100 %-nál kisebb, az Ön által beállított adatfeldolgozást mintavételi ráta figyelmen kívül hagyja.

> [!WARNING]
> A csempén megjelenő érték adatfeldolgozást mintavételi beállított értékeket. A tényleges mintavételi ráta az nem jelent, ha SDK mintavételi művelet.
> 
> 

## <a name="adaptive-sampling-at-your-web-server"></a>A webkiszolgáló adaptív mintavétel
Adaptív mintavételi az Application Insights SDK az ASP.NET v 2.0.0-beta3 és újabb verzióihoz érhető el, és alapértelmezés szerint engedélyezve van. 

Adaptív mintavételi hatással van az Application Insights szolgáltatás végpontjának a webalkalmazás-kiszolgáló által küldött telemetriai adatok mennyiségét. A kötet tartani a megadott legnagyobb mértékben forgalom belül automatikusan módosul.

Azt nem működik, telemetriai adatokat kis mennyiségű úgy egy alkalmazást a hibakeresés, vagy nem befolyásolja az alacsony használat rendelkező webhely.

A célkötet eléréséhez a generált telemetriai adatok némelyike program elveti. De mintavételi más típusú, mint az algoritmus megtartja a kapcsolódó telemetriai elemeket. Például a telemetriai adatokat, a Keresés most vizsgálatakor ellenőrizze, hogy képes lesz megkereshető egy adott kivételhez kapcsolódó a kérelem. 

A metrika számít például kérelmek aránya, és kivétel arány igazítva kiegyensúlyozása érdekében a mintavételi ráta, hogy azok körülbelül megfelelő értékek megjelenítése a metrika Intézőben.

### <a name="update-nuget-packages"></a>NuGet-csomagok frissítése ###

A projekt NuGet-csomagok frissítése a legújabb *kiadás előtti* Application Insights verzióját. A Visual Studióban, kattintson a jobb gombbal a projektre a Megoldáskezelőben, válassza a NuGet-csomagok kezelése, ellenőrizze **közé tartoznak az előzetes** és Microsoft.ApplicationInsights.Web megkereséséhez. 

### <a name="configuring-adaptive-sampling"></a>Adaptív mintavétel konfigurálása ###

A [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), a több paraméterrel beállíthatja a `AdaptiveSamplingTelemetryProcessor` csomópont. Az ábrán látható az alapértelmezett értékeket:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`
  
    Az adaptív algoritmus célja a cél gyakorisága **minden kiszolgáló állomáson**. Ha sok gazdagép a webalkalmazás fut, csökkentse a ezt az értéket, hogy az Application Insights portáljáról-forgalmat a cél aránya belül.
* `<EvaluationInterval>00:00:15</EvaluationInterval>` 
  
    Az időköz, ahol az aktuális telemetriai mérték újraértékelése is megtörténik. Kiértékelési mozgóátlaga szerint történik. Rövidítse le ezt az időközt, ha a telemetriai adatok hirtelen felszakadásáig kívánt.
* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`
  
    Mintavételi százalékos érték módosításokat, ha hamarosan hogyan után azt engedélyezettek csökkenthető a mintavételi arány újra kevesebb adatot rögzítéséhez.
* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`
  
    Mintavételi százalékos érték módosításokat, ha hamarosan hogyan után azt engedélyezettek újra az adatok rögzítéséhez mintavételi arány növelése érdekében.
* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`
  
    Mivel százalékos mintavételi változik, mi az a minimális értéke azt hogy jogosult-e beállítva.
* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`
  
    Mivel százalékos mintavételi változik, mi az a maximális érték azt hogy jogosult-e beállítva.
* `<MovingAverageRatio>0.25</MovingAverageRatio>` 
  
    A mozgóátlag számítás a súlyozás a legfrissebb értéket hozzárendelni. Az 1-nél kisebb vagy egyenlő, érték. A kisebb értékek módosításokat az algoritmus kevesebb reaktív hirtelen számára.
* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`
  
    Ha az alkalmazás elkezdte rendelt értéket. Nem csökkenti a akkor hibakeresése közben. 

* `<ExcludedTypes>Trace;Exception</ExcludedTypes>`
  
    Egy pontosvesszővel tagolt listáját, amelyek nem kívánt mintát venni. Felismert típusok a következők: függőségi, esemény, kivétel, PageView, kérelem, nyomkövetési. A megadott típusú példányainak továbbításuk; a nem megadott típusok lekérdező.

* `<IncludedTypes>Request;Dependency</IncludedTypes>`
  
    Egy pontosvesszővel tagolt listáját kell mintát venni kívánt típusokat. Felismert típusok a következők: függőségi, esemény, kivétel, PageView, kérelem, nyomkövetési. A megadott típusoknak lekérdező; a más típusú példányainak mindig továbbítja.


**Kapcsolja ki a** adaptív mintavételi, távolítsa el a AdaptiveSamplingTelemetryProcessor csomópont applicationinsights-config.

### <a name="alternative-configure-adaptive-sampling-in-code"></a>Alternatív: Adaptív mintavétel konfigurálása a kódban
Helyett, ha a mintavételi paramétert a .config kiterjesztésű fájl, programozott módon állíthatja be ezeket az értékeket. Ez lehetővé teszi, hogy adjon meg egy visszahívási függvény, amelyet mindig, amikor a mintavételi ráta újraértékelése is megtörténik. Használhat, például, hogy megtudja, milyen mintavételi ráta használatban van.

Távolítsa el a `AdaptiveSamplingTelemetryProcessor` csomópontot a .config fájlból.

*C#*

```csharp

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var adaptiveSamplingSettings = new SamplingPercentageEstimatorSettings();

    // Optional: here you can adjust the settings from their defaults.

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;

    builder.UseAdaptiveSampling(
         adaptiveSamplingSettings,

        // Callback on rate re-evaluation:
        (double afterSamplingTelemetryItemRatePerSecond,
         double currentSamplingPercentage,
         double newSamplingPercentage,
         bool isSamplingPercentageChanged,
         SamplingPercentageEstimatorSettings s
        ) =>
        {
          if (isSamplingPercentageChanged)
          {
             // Report the sampling rate.
             telemetryClient.TrackMetric("samplingPercentage", newSamplingPercentage);
          }
      });

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([További információ a telemetriai adatok processzorok](app-insights-api-filtering-sampling.md#filtering).)

<a name="other-web-pages"></a>

## <a name="sampling-for-web-pages-with-javascript"></a>Mintavételi JavaScript weblapok
Konfigurálhatja a weblapok rögzített mintavételi tetszőleges kiszolgálóról. 

Ha Ön [a weblapok konfigurálja az Application Insights](app-insights-javascript.md), módosítsa a JavaScript kódrészletet, hogy az Application Insights portáljáról. (Az ASP.NET alkalmazásokban, a részlet általában kerül a _Layout.cshtml.)  Például a sor beszúrása `samplingPercentage: 10,` előtt a instrumentation kulcs:

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

A mintavételi arány válassza százalékában, amelynek mérete megközelítőleg 100/N, ahol N az egész szám.  Mintavételi jelenleg nem támogatja a más értékek.

Ha rögzített mintavételi a kiszolgálón is engedélyezi, az ügyfelek és a kiszolgáló szinkronizálja, a keresés, navigálhat kapcsolódó Lapmegtekintések és kérések között.

## <a name="fixed-rate-sampling-for-aspnet-and-java-web-sites"></a>Az ASP.NET és a Java webhelyek rögzített mintavétel
Rögzített alapján mintavételi csökkenti a webkiszolgálót és a böngészők által küldött forgalmat. Adaptív mintavételi eltérően csökkenti telemetriai rögzített kulcs határoz meg. Azt is szinkronizálja az ügyfél és kiszolgáló mintavételi, hogy a kapcsolódó elemek megmaradnak – például ha egy lap nézetben, a keresési tekinti meg, megtalálhatja a kapcsolódó kérelemre.

A mintavételi algoritmus megtartja a kapcsolódó elemeket. Az egyes HTTP-kérelmek esemény, a kérelem és a kapcsolódó események vagy elvetett vagy továbbított együtt. 

A Metrikaböngészőben például a kérelem és a kivételesemények számát díjszabás vannak faktor szorzata kiegyensúlyozása érdekében a mintavételi ráta, hogy azok körülbelül helyességét.

### <a name="configuring-fixed-rate-sampling-in-aspnet"></a>Az ASP.NET rögzített mintavétel konfigurálása ###

1. **A projekt NuGet-csomagok frissítése** a legújabb *kiadás előtti* Application Insights verzióját. A Visual Studióban, kattintson a jobb gombbal a projektre a Megoldáskezelőben, válassza a NuGet-csomagok kezelése, ellenőrizze **közé tartoznak az előzetes** és Microsoft.ApplicationInsights.Web megkereséséhez. 
2. **Tiltsa le a adaptív mintavételi**: A [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), távolítsa el vagy megjegyzéssé a `AdaptiveSamplingTelemetryProcessor` csomópont.
   
    ```xml
   
    <TelemetryProcessors>
   
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->

    ```

3. **A rögzített mintavételi modul engedélyezése.** Adja hozzá ezt a kódrészletet [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):
   
    ```XML
   
    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
   
      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>
   
    ```

### <a name="configuring-fixed-rate-sampling-in-java"></a>A JAVA rögzített mintavétel konfigurálása ###

1. Töltse le és konfigurálja a webes alkalmazás legfrissebb [application insights java SDK](app-insights-java-get-started.md)

2. **A rögzített mintavételi modul engedélyezése** ApplicationInsights.xml fájlt ad hozzá a következő kódrészletet.

```XML
    <TelemetryProcessors>
        <BuiltInProcessors>
            <Processor type = "FixedRateSamplingTelemetryProcessor">
                <!-- Set a percentage close to 100/N where N is an integer. -->
                <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
                <Add name = "SamplingPercentage" value = "50" />
            </Processor>
        </BuilrInProcessors>
    <TelemetryProcessors/>
```

3. Megadhat vagy telemetriai adott típusú kizárni a processzor címkére "FixedRateSamplingTelemetryProcessor" a következő címkék használatával mintavétel
```XML
    <ExcludedTypes>
        <ExcludedType>Request</ExcludedType>
    </ExcludedTypes>

    <IncludedTypes>
        <IncludedType>Exception</IncludedType>
    </IncludedTypes>
```
A része, vagy kizárja a mintavételi telemetriai típusok a következők: függőségi, esemény, kivétel, PageView, kérelem és követéséhez.

> [!NOTE]
> A mintavételi arány válassza százalékában, amelynek mérete megközelítőleg 100/N, ahol N az egész szám.  Mintavételi jelenleg nem támogatja a más értékek.
> 
> 

### <a name="alternative-enable-fixed-rate-sampling-in-your-server-code"></a>Másik megoldás: a kiszolgáló kódjában található rögzített mintavételi engedélyezése
Helyett, ha a mintavételi paramétert a .config kiterjesztésű fájl, programozott módon állíthatja be ezeket az értékeket. 

*C#*

```csharp

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([További információ a telemetriai adatok processzorok](app-insights-api-filtering-sampling.md#filtering).)

## <a name="when-to-use-sampling"></a>Mikor érdemes használni a mintavételi?
Adaptív mintavételi automatikusan engedélyezi az ASP.NET SDK verzió 2.0.0-beta3 használatakor vagy újabb. Melyik verzióját az SDK-t használja, attól függetlenül engedélyezi a mintavételhez. az összegyűjtött adatokat az Application Insights adatfeldolgozást mintavételi engedélyezheti.

Nincs mintavételi alapértelmezés szerint engedélyezve van a Java SDK-ban. Jelenleg csak a támogatott rögzített arány mintavételi. Adaptív mintavételi nem támogatott Java SDK-ban.

Általában a legtöbb kis és közepes méretű alkalmazásokhoz nem szükséges mintavételi. A leghasznosabb információkat a diagnosztikai és a legpontosabb statisztika nyerhetők adatokat gyűjt a felhasználói tevékenységekről. 

A mintavételi fő előnyei a következők:

* Application Insights szolgáltatás elhagyta a(z) ("szabályozások") adatpontok rövid üzenetet küld a nagyon nagy mértékben telemetriai adatot az alkalmazás időintervallum. 
* Tartsa belül a [kvóta](app-insights-pricing.md) az adatokat a tarifacsomagot. 
* Az Alkalmazáshasználati adatok gyűjtése hálózati forgalom csökkentése érdekében. 

### <a name="which-type-of-sampling-should-i-use"></a>Milyen típusú mintavételi érdemes használni?
**Használja az adatfeldolgozást mintavételi, ha:**

* Gyakran halad át a havi kvóta telemetriai adatot.
* Az SDK-t, amely nem támogatja a mintavételi – például az ASP.NET-verziók korábbi, mint 2-es használata.
* A felhasználók webböngészővel kap nagy mennyiségű telemetriai adatokat.

**Használjon rögzített mintavételi, ha:**

* Az Application Insights SDK használ az ASP.NET web services, verziószám: 2.0.0 vagy újabb vagy Java SDK v2.0.1 vagy újabb verzióját, és
* Ügyfél és kiszolgáló közötti szinkronizált mintavételi kívánt, az, hogy ha éppen vizsgálja a események [keresési](app-insights-diagnostic-search.md), az ügyfél és kiszolgáló, például a lapmegtekintések és a http-kérelmek kapcsolódó események közti léphet.
* Biztos abban, a megfelelő mintavételi arány az alkalmazásra vonatkozóan. Elég nagy az beszerzése pontos mérőszámokat kell lennie, de alatt, amely meghaladja a árképzési kvóta és a sávszélesség-szabályozási korlátok. 

**Adaptív mintavételi használja:**

Ha más űrlap mintavételi feltételek nem vonatkoznak, adaptív mintavételi ajánlott. Ez az ASP.NET Server SDK verzió 2.0.0-beta3 alapértelmezés szerint engedélyezve van, vagy később. Nem csökkenti forgalom a meghatározott minimális mértékben eléréséig, ezért alacsony használható helyeket nem érinti.

## <a name="how-do-i-know-whether-sampling-is-in-operation"></a>Hogyan állapítható meg, hogy a mintavétel van-e művelet?
Annak megállapításához, a tényleges mintavételi ráta, függetlenül attól, hol van érvényben, használjon egy [Analytics lekérdezési](app-insights-analytics.md) Ez például:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

Az egyes megőrzi a rekord, `itemCount` azt jelzi, amely azt jelöli, eredeti rekordok száma egyenlő 1 + az előző elvetett rekordok száma. 

## <a name="how-does-sampling-work"></a>Hogyan működik a mintavételi?
Az SDK-t 2.0.0 ASP.NET verziókat és a Java SDK verzió 2.0.1 rögzített mintavételi szolgáltatása és újabb verziók esetében. Adaptív mintavételi az ASP.NET verziókat és újabb verziók esetében 2.0.0 SDK szolgáltatása. Mintavételi adatfeldolgozást az Application Insights-szolgáltatásának egy funkciója, és lehet a műveletet, ha az SDK nem hajt végre mintavétel. 

A mintavételi algoritmus úgy dönt, mely telemetriai elemek eldobása, és melyeket kívánja megtartani (az SDK-ban vagy akár az Application Insights szolgáltatásban). A mintavételi döntési különböző szabályok, célja, hogy minden egymáshoz adatpontok változatlanok maradnak, egy diagnosztikai megoldást vezet be, hogy végrehajthatóként és megbízhatóbb, még akkor is csökkentett adatkészlet az Application insightsban karbantartása megőrzése alapul. Például ha a sikertelen kérelmek az alkalmazás további telemetriai elemeket (például kivétel és naplózza a kérést a nyomkövetések) küld, mintavételi nem elválasztja az ehhez a kérelemhez és egyéb telemetriai adatokat. Az tartja vagy együtt elutasítja azokat. Emiatt ha a kérelem részletes adatait az Application Insightsban tekinti meg, bármikor megtekintheti a kérelem és a kapcsolódó telemetriai elemek. 

Az alkalmazások, amelyek meghatározzák a "user" (Ez azt jelenti, hogy a legjellemzőbb webalkalmazások), a mintavételi döntést a felhasználói azonosítóját, ami azt jelenti, hogy bármely adott felhasználó összes telemetriai adat vagy megőrzi, vagy kihagyott kivonatának alapul. Milyen típusú alkalmazások felhasználók számára (például a web services) nem meghatározó a mintavételi döntési alapul a Műveletazonosító a kérelem. Végezetül cikkeknél a telemetriai adatok sem be (például telemetriai elemek nem http-környezetben az aszinkron szál jelentett) és a felhasználó nem művelet azonosítója mintavételi egyszerűen rögzíti a rendelkezésre álló telemetriai elemek százalékaként. 

Telemetria bemutató vissza, ha az Application Insights szolgáltatás a metrikák a azonos mintavételi arány kiegyensúlyozása érdekében a hiányzó adatpontokat gyűjtemény, idején használt állítja be. Ezért ha az Application Insights telemetria, a felhasználók azért jelent meg, statisztikailag megfelelő becsléseket, amelyek nagyon közel valós számok.

A közelítés a pontosság nagymértékben függ a konfigurált mintavételi arány. A pontosság is, az alkalmazások, amelyek kezelik a felhasználók sok általában hasonló kérelmek nagy mennyiségű növeli. Másrészről olyan alkalmazások, amelyek nem működnek a jelentős terhelés esetén mintavételi nem szükséges, mivel ezek az alkalmazások általában küldhet a telemetriai adatok maradva a kvótán belül anélkül, hogy ez adatvesztést a sávszélesség-szabályozás. 

> [!WARNING]
> Az Application Insights nem minta metrikák és a munkamenetek telemetriai adatok típusát. Lehet, hogy a pontosság csökkenésével magas nemkívánatos a telemetria-típusok.
> 

### <a name="adaptive-sampling"></a>Adaptív mintavétel
Adaptív mintavételi ad hozzá egy összetevő, amely figyeli a jelenlegi átviteli az SDK-ból, és beállítja a mintavételi arány ahhoz, hogy a cél maximális sebesség belül maradjanak kipróbálásához. A módosítás rendszeres időközönként újraszámítja, és a kimenő átviteli sebességet mozgóátlaga alapul.

## <a name="sampling-and-the-javascript-sdk"></a>Mintavételi és a JavaScript SDK
Az ügyféloldali (JavaScript) SDK vesz részt a rögzített mintavételi a kiszolgálóoldali SDK-val együtt. A felműszerezett lapok csak küldése ügyféloldali telemetriai, amelynek a kiszolgálóoldali "mintát." a döntésről végrehajtott azonos felhasználói A logikai egységének fenntartására szolgáló felhasználói munkamenet között - és kiszolgáló-ügyféloldalon tervezték. Ennek eredményeképpen bármely Application Insights telemetria adott eleménél minden egyéb telemetriai elemek találhatók a felhasználó vagy a munkamenet. 

*Az ügyfél és a kiszolgálóoldali telemetriai ne jelenjen meg koordinált minták fent leírtak.*

* Győződjön meg arról, hogy a rögzített mintavételi mind a kiszolgáló és az ügyfél engedélyezve.
* Győződjön meg arról, hogy az SDK-verzió-e a 2.0-s vagy újabb.
* Ellenőrizze, hogy az azonos mintavételi arány beállíthatja az ügyfél és kiszolgáló.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
*Miért nem mintavételi egy egyszerű "collect X valamennyi telemetriai típusú százaléka"?*

* A mintavételi megközelítést metrika becsléseket a nagyon nagy pontosságú volna meg, amíg megszűnését összehangolhatók a felhasználó, a munkamenet és a kérést, ami kritikus diagnosztikai diagnosztikai adatokat. Ezért mintavételi works jobban az "összes telemetriai elemek összegyűjtése az X százalék az alkalmazás felhasználók" vagy "app kérelmek százalékos X az összes telemetriai adat gyűjtése" logikát. A telemetriai elemek nem a kérések (például a háttérben történő aszinkron feldolgozás) társított, ismét alá esik, hogy "a gyűjtés X összes elemet az egyes telemetriai százalékát." 

*A mintavételi arány idővel megváltozhat?*

* Igen, adaptív mintavételi fokozatosan módosítja a mintavételi arány, telemetriai adatok jelenleg megfigyelt mennyisége alapján.

*Ha rögzített mintavételi használatához honnan tudhatom, hogy mely mintavételi százalékos fog működni a legjobb az alkalmazásom?*

* Egyirányú kezdődnie adaptív mintavételi, megtudhatja, mi értékelés rendezi a (lásd a fenti kérdés), és rögzített majd átváltása mintát vesz adott alapján. 
  
    Ellenkező esetben van kitalálni. Az Application Insights az aktuális telemetriai használatának elemzése, láthatja a szabályozás, hogy folyamatban van, és becsléséhez gyűjtött telemetriai adatok mennyiségét. Ezen három bemeneti adatokat, és a választott tarifacsomaghoz javasoljuk, hogy mennyire érdemes a gyűjtött telemetriai adatok mennyiségének csökkentése érdekében. Azonban a felhasználók számának növelését vagy valamilyen más shift telemetriai adatok mennyiségének esetleg érvénytelenné válnak a becsült.

*Mi történik, ha a mintavételi arány túl alacsony konfigurálni?*

* A túl alacsony mintavételi arány (over-aggressive mintavételi) csökkenti a becsléseket pontosságának, amikor az Application Insights megkísérli az adatok az adatok kötet csökkentése érdekében a képi megjelenítés kártalanítja. Emellett diagnosztikai élmény előfordulhat, hogy csökkenéséhez vezet, a ritkán meghibásodott vagy lassú kérelmek egy részénél lehet mintát venni ki.

*Mi történik, ha a mintavételi arány túl magas konfigurálni?*

* A gyűjtött telemetriai adatok mennyiségének elegendő csökkenést konfigurálása (nem agresszív elég) túl magas mintavételi arány eredménye. Továbbra is problémákat tapasztalhat a sávszélesség-szabályozás kapcsolatos telemetriai adatok elvesztését, és lehet, hogy az Application Insights költségeinek magasabb, mint amennyi tervezett keretét költségek miatt.

*Milyen platformokon használható mintavételi?*

* Adatfeldolgozást mintavételi automatikusan történik az összes telemetriai fent egy bizonyos köteten, ha az SDK nem hajt végre mintavétel. Ez akkor működik, például ha egy régebbi verzióját, az ASP.NET SDK vagy Java SDK(1.0.10 or before) korábbi verzióját használja.
* ASP.NET SDK verziók 2.0.0 használata vagy újabb (az Azure-ban vagy a saját kiszolgáló üzemelteti), adaptív alapértelmezés szerint a mintavételi kap, de lehet váltani rögzített fent leírt módon. A rögzített mintavételi, a böngésző SDK automatikusan szinkronizálja a kapcsolódó események mintavételhez. 
* Ha 2.0.1-es verziója Java SDK használata, illetve újabb ApplicationInsights.xml rögzített arány mintavételi bekapcsolása lehet konfigurálni. Mintavételi alapértelmezés szerint ki van kapcsolva. A rögzített mintavételi, a böngésző SDK automatikusan szinkronizálja a kapcsolódó események mintavételhez.

*Nincsenek az egyes ritka események, hogy mindig szeretné. Hogyan szerezhető be őket a mintavételi modul túli?*

* Egy új TelemetryConfiguration (nem az alapértelmezett Active) rendelkező TelemetryClient külön példányt inicializálni. Használja a ritka események küldésére.

## <a name="next-steps"></a>További lépések
* [Szűrés](app-insights-api-filtering-sampling.md) biztosíthat további szigorú ellenőrzése az SDK küld.

