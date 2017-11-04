---
title: "A Java-webalkalmazás az Azure Application Insights telemetria szűrése |} Microsoft Docs"
description: "Telemetria forgalom csökkentése a nem kell figyelnie események kiszűrésével."
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: f9e061c010667bc18ac54e6546cc25339e9c0e3e
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="filter-telemetry-in-your-java-web-app"></a>A Java-webalkalmazás az telemetriai szűrése

Szűrők hardvermódosításainak válassza ki a telemetriai adatok, amelyek a [Java-webalkalmazás küld az Application Insights](app-insights-java-get-started.md). Néhány, a-kész szűrő használható, és a saját egyéni szűrők is írhat.

A out-of-az-box szűrők a következők:

* Nyomkövetési súlyossági szint
* Adott URL-címek, kulcsszavak vagy válaszkódok
* Gyors válaszok – Ez azt jelenti, hogy kér, amelyhez az alkalmazás válaszolt gyorsan
* Adott események neve

> [!NOTE]
> Szűrők az alkalmazás a metrikák eltolódhat. Például dönthet úgy, hogy lassú válaszok meghatározásához akkor állítja be egy szűrőt, amely elveti a gyors válaszidők. De vegye figyelembe, hogy az Application Insights által jelentett átlagos válaszideje majd lesz lassabb, mint a sebesség, és a kérelmek száma kisebb, mint a valós szám lesz.
> Ha ez problémát jelent, akkor [mintavételi](app-insights-sampling.md) helyette.

## <a name="setting-filters"></a>Szűrők beállítása

A ApplicationInsights.xml, adjon hozzá egy `TelemetryProcessors` szakasz ebben a példában például:


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```




[Vizsgálja meg a beépített processzor teljes készletének](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal/processor).

## <a name="built-in-filters"></a>A beépített szűrők

### <a name="metric-telemetry-filter"></a>Metrika Telemetriai szűrő

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded`-Egyéni metrika nevek vesszővel tagolt listája.


### <a name="page-view-telemetry-filter"></a>Lap nézet Telemetriai szűrő

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS`-A lap betöltéséhez szükséges idő időtartam hivatkozik. Ha ez be van állítva, a lapok, amelyek a gyorsabb, mint a jelenleg betöltött nem jelenti.
* `NotNeededNames`-Lap neve vesszővel tagolt listája.
* `NotNeededUrls`-Töredékei URL vesszővel tagolt listája. Például `"home"` kiszűri összes lapok, amelyek "otthoni" URL-címét.


### <a name="request-telemetry-filter"></a>Telemetria szűrő kérése


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Szintetikus forrásszűrő

A SyntheticSource tulajdonság értéket tartalmazó összes telemetriai adat kiszűri. Ezek közé tartozik a botok, csillag és rendelkezésreállás figyelésére szolgáló tesztek érkező kérelmeket.

Összes szintetikus kérelem telemetriai szűrheti:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Adott szintetikus források telemetriai szűrheti:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded`-Szintetikus adatforrások nevének vesszővel tagolt listája.

### <a name="telemetry-event-filter"></a>Telemetria szűrő

Egyéni események szűrése (használja a rendszer naplózza [trackevent() függvény](app-insights-api-custom-events-metrics.md#trackevent)).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames`-Esemény nevek vesszővel tagolt listája.


### <a name="trace-telemetry-filter"></a>Nyomkövetési Telemetria szűrő

Szűrők nyomkövetési naplófájl (használja a rendszer naplózza [TrackTrace()](app-insights-api-custom-events-metrics.md#tracktrace) vagy egy [naplózási keretrendszer adatgyűjtő](app-insights-java-trace-logs.md)).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel`érvényes értékek a következők:
 *  KI - szűrheti az összes olyan nyomkövetés
 *  NYOMKÖVETÉS - nincs szűrés. a nyomkövetési szint egyenlő
 *  INFORMÁCIÓ - szűrő kimenő NYOMKÖVETÉSI szint
 *  Figyelmeztetés - szűrő NYOMKÖVETÉSI és információ
 *  Hiba – kimenő figyelmeztetés, információ, a NYOMKÖVETÉSI szűrő
 *  KRITIKUS - szűrő, az összes kritikus


## <a name="custom-filters"></a>Egyéni szűrők

### <a name="1-code-your-filter"></a>1. A szűrő-kód

A kódban, hozzon létre egy osztályt, amely megvalósítja az `TelemetryProcessor`:

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

       /* Any parameters that are required to support the filter.*/
       private final String successful;

       /* Initializers for the parameters, named "setParameterName" */
       public void setNotNeeded(String successful)
       {
          this.successful = successful;
       }

       /* This method is called for each item of telemetry to be sent.
          Return false to discard it.
          Return true to allow other processors to inspect it. */
       @Override
       public boolean process(Telemetry telemetry) {
        if (telemetry == null) { return true; }
        if (telemetry instanceof RequestTelemetry)
        {
            RequestTelemetry requestTelemetry = (RequestTelemetry)telemetry;
            return request.getSuccess() == successful;
        }
        return true;
       }
    }

```


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. A konfigurációs fájlban a szűrő meghívása

A ApplicationInsights.xml:

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

## <a name="troubleshooting"></a>Hibaelhárítás

*A szűrő nem működik.*

* Ellenőrizze, hogy megadta-e érvényes paraméterértékekért. Például időtartamok egész számnak kell lennie. Érvénytelen értékeket, akkor figyelmen kívül hagyja a szűrőt. Ha az egyéni szűrő konstruktor vagy set metódus kivételt jelez, akkor figyelmen kívül.

## <a name="next-steps"></a>Következő lépések

* [A mintavételi](app-insights-sampling.md) -érdemes lehet a metrikákat nem döntés alternatívájaként mintavételi.
