---
title: A Java-webalkalmazását az Azure Application Insights telemetria szűrése |} A Microsoft Docs
description: Csökkenthető a telemetriai forgalom szűrésével ki az eseményeket, nem kell figyelnie.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/23/2016
ms.author: mbullwin
ms.openlocfilehash: 6bd8d0cee01853547efd028feef0a97f9398024e
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015522"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>A Java-webalkalmazásban szűrőtelemetria

Szűrők hardvermódosításainak válassza ki a telemetriát, amely a [küld az Application Insights Java-webalkalmazás](java-get-started.md). Néhány-a-beépített szűrőket, amelyeket használhat, és a saját egyéni szűrőket is kiírhatja.

A-beépített szűrők a következők:

* Nyomkövetési súlyossági szint
* Konkrét URL-címeket, kulcsszavakat vagy válaszkódok
* Gyors válaszokat – vagyis a kérelmeket, amelyhez az alkalmazás válasza, gyorsan
* Az események egyedi neve

> [!NOTE]
> Szűrők az alkalmazás a metrikák döntés. Például dönthet úgy, hogy lassúak a válaszok diagnosztizálhatja, hogy beállítsa az elveti a gyors válaszidők szűrőt. De vegye figyelembe, hogy az átlagos válaszidők az Application Insights által jelentett igaz sebessége lassabb lesz, és a kérelmek száma kisebb, mint a valós szám lesz.
> Ez nagyon fontos, ha [mintavételi](../../azure-monitor/app/sampling.md) helyette.

## <a name="setting-filters"></a>Szűrők beállítása

Az applicationinsights.xml fájlt, adja hozzá a `TelemetryProcessors` szakasz példához hasonlóan:


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




[Vizsgálja meg a beépített processzorok teljes körű](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal/processor).

## <a name="built-in-filters"></a>Beépített szűrők

### <a name="metric-telemetry-filter"></a>Metrikaszűrőjének Telemetria

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded` -Egyéni metrikák nevei vesszővel tagolt listája.


### <a name="page-view-telemetry-filter"></a>Lapmegtekintések telemetriai adatai szűrő

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS` – A lap betöltéséhez szükséges idő időtartam hivatkozik. Ha a beállítás engedélyezése esetén gyorsabb, mint a jelenleg betöltött oldalak nem jelenti.
* `NotNeededNames` – Lap neve vesszővel tagolt listája.
* `NotNeededUrls` -Töredékek URL-cím vesszővel tagolt listája. Ha például `"home"` szűri ki minden olyan lap, amely rendelkezik a "home" URL-címét.


### <a name="request-telemetry-filter"></a>Telemetria szűrése kérése


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Szintetikus adatforrásszűrő

Kiszűri a SyntheticSource tulajdonság értéket tartalmazó összes telemetriai adat. Ezek közé tartozik a robotok, a csillag és a rendelkezésre állási tesztek érkező kérelmeket.

Szűrje ki a szintetikus kéréseket az összes telemetriai adatokat:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Szűrje ki a meghatározott szintetikus források telemetriája:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded` – Szintetikus adatforrások nevének vesszővel tagolt listája.

### <a name="telemetry-event-filter"></a>Telemetriai események szűrése

Egyéni események szűrők (használatával naplózza [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)).


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames` – Az események neve vesszővel tagolt listája.


### <a name="trace-telemetry-filter"></a>Nyomkövetési Telemetriai szűrő

Nyomkövetési naplók szűrők (használatával naplózza [TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) vagy egy [naplózási keretrendszer gyűjtő](java-trace-logs.md)).

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel` Érvényes értékek a következők:
 *  KI - összes nyomkövetési kiszűréséhez
 *  NYOMKÖVETÉS - szűrés. Nyomkövetési szint egyenlő
 *  INFORMÁCIÓ - szűrő ki NYOMKÖVETÉSI szint
 *  FIGYELMEZTETÉS – ki információkat és a NYOMKÖVETÉSI szűrő
 *  HIBA – meg figyelmeztetés, információ a NYOMKÖVETÉSI szűrő
 *  KRITIKUS - szűrő, kivéve a kritikus fontosságú


## <a name="custom-filters"></a>Egyéni szűrők

### <a name="1-code-your-filter"></a>1. A szűrő-kód

A kódban, hozzon létre egy osztályt, amely megvalósítja `TelemetryProcessor`:

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


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. A szűrő a konfigurációs fájl meghívása

Az applicationinsights.xml fájlt:

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

*A szűrés nem működik.*

* Ellenőrizze, hogy megadta-e érvényes paraméterértékeket. Például időtartamok egész számoknak kell lenniük. Érvénytelen értékek miatt a szűrőt, hogy figyelmen kívül lesz hagyva. Ha az egyéni szűrő konstruktor vagy set metódus kivételt jelez, akkor figyelmen kívül.

## <a name="next-steps"></a>További lépések

* [Mintavételi](../../azure-monitor/app/sampling.md) -fontolja meg a mintavétel a metrikák nem tevékenységdiagramon alternatívájaként.
