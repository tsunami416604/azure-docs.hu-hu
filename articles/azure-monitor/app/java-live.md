---
title: Application Insights Java-webalkalmazások, amelyek már az élő
description: Már fut a kiszolgálón webalkalmazás monitorozásának indítása
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 12f3dbb9-915f-4087-87c9-807286030b0b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/10/2016
ms.author: mbullwin
ms.openlocfilehash: 5d0866bdd96a1b0f626c369cfd646f6249a4da54
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54121379"
---
# <a name="application-insights-for-java-web-apps-that-are-already-live"></a>Application Insights Java-webalkalmazások, amelyek már az élő

Ha egy webalkalmazást, amely a j2ee-alapú kiszolgálón már fut, megkezdheti a figyelést az [Application Insights](../../azure-monitor/app/app-insights-overview.md) anélkül, hogy módosítsa a kódokat, vagy a projekthez újrafordítottuk kellene. Ezzel a lehetőséggel, a kiszolgáló nem kezelt kivételek és teljesítményszámlálók küldött HTTP-kérések adatainak beolvasása.

Ehhez egy [Microsoft Azure](https://azure.com)-előfizetésre van szüksége.

> [!NOTE]
> Ezen az oldalon az eljárás ad hozzá az SDK-t a webes alkalmazás futásidőben. Ez a modul rendszerállapot akkor hasznos, ha nem szeretné frissíteni, vagy a forrás kód ismételt felépítésére. Ha lehetséges, azt javasoljuk, de [az SDK hozzáadása a forráskódja](java-get-started.md) helyette. Amely további lehetőségeket kínál például kódírás felhasználói tevékenységek követése érdekében.
> 
> 

## <a name="1-get-an-application-insights-instrumentation-key"></a>1. Application Insights-kialakítási kulcs beszerzése
1. Jelentkezzen be a [Microsoft Azure-portálon](https://portal.azure.com)
2. Hozzon létre egy új Application Insights-erőforrást, és állítsa be a Java webalkalmazás alkalmazástípust.
   
    ![Adjon meg egy nevet, válassza ki a Java webalkalmazást, és kattintson a Létrehozás gombra.](./media/java-live/02-create.png)

    Az erőforrás néhány másodperc alatt létrejön.

4. Nyissa meg az új erőforrást, és a kialakítási kulcs beszerzése. Ezt a kulcsot nemsokára a kódprojektbe kell illesztenie.
   
    ![Az új erőforrás áttekintésében kattintson a Tulajdonságok gombra, és másolja le a kialakítási kulcsot](./media/java-live/03-key.png)

## <a name="2-download-the-sdk"></a>2. Az SDK letöltése
1. Töltse le a [Javához készült Application Insights SDK-t](https://aka.ms/aijavasdk). 
2. A kiszolgálón bontsa ki a könyvtárban, amelyből a projekt bináris fájljait a rendszer betölti az SDK tartalma. Tomcat használja, ha ez a könyvtár általában lenne alatt `webapps/<your_app_name>/WEB-INF/lib`

Vegye figyelembe, hogy meg kell ismételni ezt az egyes kiszolgáló-példányokon, és minden alkalmazáshoz.

## <a name="3-add-an-application-insights-xml-file"></a>3. Az Application Insights XML-fájl hozzáadása
Hozzon létre ApplicationInsights.xml a mappában, amelyben hozzá az SDK-t. Helyezze bele a következő XML-kódot.

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
* Az eseménykorreláció a HTTP-kérelemösszetevő további eleme. Azonosítót rendel a kiszolgáló által fogadott összes kérelemhez, és az azonosítót „Operation.Id” tulajdonságként hozzáadja a telemetria minden eleméhez. Ez lehetővé teszi, hogy beállít egy szűrőt a az egyes kérelmekkel társított telemetria korrelációját, ha [diagnosztikai keresés](../../azure-monitor/app/diagnostic-search.md).

## <a name="4-add-an-http-filter"></a>4. HTTP-szűrő hozzáadása
Keresse meg és nyissa meg a web.xml fájlt a projektben, és egyesítse a következő kódrészletet a webalkalmazás-csomópont alatt, ahol az alkalmazás szűrői konfigurálva vannak.

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

## <a name="5-check-firewall-exceptions"></a>5. Ellenőrizze a tűzfal kivételei
Szüksége lehet [állítsa be a kivételeket úgy, hogy a kimenő adatok küldése](../../azure-monitor/app/ip-addresses.md).

## <a name="6-restart-your-web-app"></a>6. Indítsa újra a webalkalmazást
## <a name="7-view-your-telemetry-in-application-insights"></a>7. A telemetria megtekintése az Application Insights szolgáltatásban
Térjen vissza az Application Insights-erőforráshoz a [Microsoft Azure Portalon](https://portal.azure.com).

HTTP-kérésekkel kapcsolatos telemetriai adatokat az Áttekintés panelen jelenik meg. (Ha nincsenek ott, várjon néhány másodpercig, majd kattintson a Frissítés gombra.)

![mintaadatok](./media/java-live/5-results.png)

Részletesebb mérőszámokért kattintson bármelyik diagramra. 

![](./media/java-live/6-barchart.png)

És a egy kérelem tulajdonságainak megtekintésekor láthatja a telemetriaeseményeket, például a kérések és kivételek társítva.

![](./media/java-live/7-instance.png)

[További információk a metrikákról.](../../azure-monitor/app/metrics-explorer.md)

## <a name="next-steps"></a>További lépések
* [Adjon telemetriát a weblapokhoz](javascript.md) figyelő Lapmegtekintések és felhasználói mérőszámok.
* [Beállíthat webes teszteket](../../azure-monitor/app/monitor-web-app-availability.md) , győződjön meg arról, hogy az alkalmazás mindig elérhető és válaszkész legyen.
* [Naplókivonatok rögzítése](java-trace-logs.md)
* [Eseményeket és naplókat kereshet](../../azure-monitor/app/diagnostic-search.md) problémák diagnosztizálásához.
* [A Spring Boot inicializáló alkalmazás konfigurálása](https://docs.microsoft.com/java/azure/spring-framework/configure-spring-boot-java-applicationinsights)
