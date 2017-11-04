---
title: "Az Application Insights Java webes alkalmazásokhoz, amelyek még élő"
description: "A kiszolgálón már futó webalkalmazás figyelése"
services: application-insights
documentationcenter: java
author: harelbr
manager: carmonm
ms.assetid: 12f3dbb9-915f-4087-87c9-807286030b0b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/10/2016
ms.author: mbullwin
ms.openlocfilehash: 152e21bd9fc6db424c3caff4ce425e54ecab11e2
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="application-insights-for-java-web-apps-that-are-already-live"></a>Az Application Insights Java webes alkalmazásokhoz, amelyek még élő


Ha már a J2EE kiszolgálón futó webalkalmazás, figyelés megkezdése a [Application Insights](app-insights-overview.md) kód módosításokat, vagy a projekt újrafordítása szükségessége nélkül. Ezzel a kapcsolóval akkor a kiszolgáló, a nem kezelt kivételek és a teljesítményszámlálók elküldött HTTP-kérelmek adatainak beolvasása.

Ehhez egy [Microsoft Azure](https://azure.com)-előfizetésre van szüksége.

> [!NOTE]
> Az eljárás ezen az oldalon az SDK hozzáadása a webes alkalmazás futásidőben. A futásidejű instrumentation akkor hasznos, ha nem szeretné frissíteni, vagy építse újra a forráskódot. De ha lehetséges, javasoljuk, hogy [adja hozzá az SDK forráskódja](app-insights-java-get-started.md) helyette. Amely több lehetőséget biztosít kódot ír, például felhasználói tevékenységek nyomon követésére.
> 
> 

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Application Insights-kialakítási kulcs beszerzése
1. Jelentkezzen be a [Microsoft Azure-portálon](https://portal.azure.com)
2. Hozzon létre egy új Application Insights-erőforrást, és állítsa be az alkalmazás típusának Java-webalkalmazáshoz.
   
    ![Adjon meg egy nevet, válassza ki a Java webalkalmazást, és kattintson a Létrehozás gombra.](./media/app-insights-java-live/02-create.png)

    Az erőforrás néhány másodpercen belül jön létre.

4. Nyissa meg az új erőforrást, és a rendszerállapot-kulcs beszerzése. Ezt a kulcsot nemsokára a kódprojektbe kell illesztenie.
   
    ![Az új erőforrás áttekintésében kattintson a Tulajdonságok gombra, és másolja le a kialakítási kulcsot](./media/app-insights-java-live/03-key.png)

## <a name="2-download-the-sdk"></a>2. Az SDK letöltése
1. Töltse le a [Javához készült Application Insights SDK-t](https://aka.ms/aijavasdk). 
2. A kiszolgálón bontsa ki az SDK tartalma, amelyből be vannak töltve a projekt bináris fájlokat a könyvtárba. Ha Tomcat használata esetén ez a könyvtár általában kell a`webapps/<your_app_name>/WEB-INF/lib`

Vegye figyelembe, hogy meg kell ismételni ezt összes server-példányt, és az egyes alkalmazásokhoz.

## <a name="3-add-an-application-insights-xml-file"></a>3. Az Application Insights XML-fájl hozzáadása
Hozzon létre a mappában, az SDK hozzá ApplicationInsights.xml. Helyezze be a következő XML.

Helyettesítse be az Azure Portalról kapott kialakítási kulcsot.

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">


      <!-- The key from the portal: -->

      <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>


      <!-- HTTP request component (not required for bare API) -->

      <TelemetryModules>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
      </TelemetryModules>

      <!-- Events correlation (not required for bare API) -->
      <!-- These initializers add context data to each event -->

      <TelemetryInitializers>
        <Add   type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
        <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>

      </TelemetryInitializers>
    </ApplicationInsights>
```

* A kialakítási kulcsot a telemetria minden elemével megkapja, és ez közli az Application Insights eszközzel, hogy megjelenítse azt az erőforrásban.
* A HTTP-kérelemösszetevő nem kötelező. Automatikusan telemetriát küld a kérelmekkel és válaszidőkkel kapcsolatban a portálra.
* Az eseménykorreláció a HTTP-kérelemösszetevő további eleme. Azonosítót rendel a kiszolgáló által fogadott összes kérelemhez, és az azonosítót „Operation.Id” tulajdonságként hozzáadja a telemetria minden eleméhez. Ez lehetővé teszi, hogy minden kérelemhez társított úgy, hogy egy szűrőt telemetriai adatok összefüggéseket [diagnosztikai keresési](app-insights-diagnostic-search.md).

## <a name="4-add-an-http-filter"></a>4. HTTP-szűrő hozzáadása
Keresse meg és nyissa meg a web.xml fájlt a projektben, és a webalkalmazás csomópont alatt, ahol az alkalmazás szűrők vannak konfigurálva a következő kódrészletét egyesíteni.

A legpontosabb eredmények érdekében le kell képezni a szűrőt az összes többi szűrő előtt.

```XML

    <filter>
      <filter-name>ApplicationInsightsWebFilter</filter-name>
      <filter-class>
        com.microsoft.applicationinsights.web.internal.WebRequestTrackingFilter
      </filter-class>
    </filter>
    <filter-mapping>
       <filter-name>ApplicationInsightsWebFilter</filter-name>
       <url-pattern>/*</url-pattern>
    </filter-mapping>
```

## <a name="5-check-firewall-exceptions"></a>5. Ellenőrizze a tűzfal kivételei közé
Szükség lehet [kimenő adatküldés kivételeket](app-insights-ip-addresses.md).

## <a name="6-restart-your-web-app"></a>6. A webalkalmazás újraindítása
## <a name="7-view-your-telemetry-in-application-insights"></a>7. A telemetria megtekintése az Application Insights szolgáltatásban
Térjen vissza az Application Insights-erőforráshoz a [Microsoft Azure Portalon](https://portal.azure.com).

Telemetriai adatainak HTTP-kérelmek az Áttekintés panel jelenik meg. (Ha nincsenek ott, várjon néhány másodpercig, majd kattintson a Frissítés gombra.)

![mintaadatok](./media/app-insights-java-live/5-results.png)

Részletesebb mérőszámokért kattintson bármelyik diagramra. 

![](./media/app-insights-java-live/6-barchart.png)

És egy kérelem tulajdonságainak megtekintésekor láthatja a telemetriai események például a kérelmek és kivételek társítva.

![](./media/app-insights-java-live/7-instance.png)

[További információk a metrikákról.](app-insights-metrics-explorer.md)

## <a name="next-steps"></a>Következő lépések
* [Telemetriai adatok felvétele a weblapok](app-insights-javascript.md) figyelő Lapmegtekintések és felhasználói metrikákat.
* [Webalkalmazás-tesztek beállítása](app-insights-monitor-web-app-availability.md) győződjön meg arról, az alkalmazás marad élő és rugalmas.
* [Naplózási nyomkövetés rögzítése](app-insights-java-trace-logs.md)
* [Keresést az események és a naplók](app-insights-diagnostic-search.md) problémák diagnosztizálásához.

