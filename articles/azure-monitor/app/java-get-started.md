---
title: 'Gyors útmutató: Java Web App Analytics az Azure Application Insights'
description: 'Alkalmazásteljesítmény-figyelés Java-webalkalmazásokhoz az Application Insights használatával. '
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 05/24/2019
ms.openlocfilehash: 484d4e8df8a8fdceed62a65858126a16d028121e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670083"
---
# <a name="quickstart-get-started-with-application-insights-in-a-java-web-project"></a>Rövid útmutató: a Application Insights használatának első lépései Java webes projektekben

Ebben a rövid útmutatóban a Application Insights használatával automatikusan kérheti a kérelmeket, nyomon követheti a függőségeket, és összegyűjtheti a teljesítményszámlálókat, diagnosztizálhatja a teljesítménnyel kapcsolatos hibákat és kivételeket, és kódot írhat az alkalmazással kapcsolatos felhasználók nyomon követéséhez.

A Application Insights egy bővíthető analitikai szolgáltatás webes fejlesztőknek, amely segít megérteni az élő alkalmazás teljesítményét és használatát. Az Application Insights a Linux, Unix vagy Windows rendszeren futó Java alkalmazásokat támogatja.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Működő Java-alkalmazás.

## <a name="get-an-application-insights-instrumentation-key"></a>Application Insights-kialakítási kulcs beszerzése

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
2. A Azure Portal hozzon létre egy Application Insights erőforrást. Állítsa be a Java webalkalmazás alkalmazástípust.

3. Keresse meg az új erőforrás kialakítási kulcsát. Ezt a kulcsot nemsokára a kódprojektbe kell illesztenie.

    ![Az új erőforrás áttekintésében kattintson a Tulajdonságok gombra, és másolja le a kialakítási kulcsot](./media/java-get-started/instrumentation-key-001.png)

## <a name="add-the-application-insights-sdk-for-java-to-your-project"></a>A Javához készült Application Insights SDK hozzáadása a projekthez

*Válassza ki a projekt típusát.*

# <a name="maven"></a>[Maven 3](#tab/maven)

Ha a projekt már be van állítva a Maven for Build használatára, egyesítse a következő kódot a *Pom. XML* fájlba.

Ezután frissítse a projektfüggőségeket, hogy letöltse a bináris fájlokat.

```XML
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.5.0</version>
      </dependency>
    </dependencies>
```

# <a name="gradle"></a>[Gradle](#tab/gradle)

Ha a projekt már be van állítva a Gradle for Build használatára, egyesítse a következő kódot a *Build. Gradle* fájlba.

Ezután frissítse a projektfüggőségeket, hogy letöltse a bináris fájlokat.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.5.0'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

# <a name="other-types"></a>[Egyéb típusok](#tab/other)

Töltse le a [legújabb verziót](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest), és a korábbi verziókat felülírva másolja a szükséges fájlokat a projektbe.

---

### <a name="questions"></a>Kérdések
* *Mi a kapcsolat a `-web-auto`, `-web` és `-core` összetevők között?*
  * `applicationinsights-web-auto` olyan mérőszámokat biztosít, amelyek nyomon követik a HTTP servlet-kérelmek számát és a válaszadási időpontokat, ha automatikusan regisztrálja az Application Insights servlet-szűrőt futásidőben.
  * a `applicationinsights-web` olyan mérőszámokat is biztosít, amelyek a HTTP servlet-kérelmek számát és a válaszadási időpontokat követik, de az alkalmazásban manuálisan kell regisztrálni az Application Insights servlet szűrőt.
  * `applicationinsights-core` csak az operációs rendszer nélküli API-t biztosítja, például ha az alkalmazás nem servlet-alapú.
  
* *Hogyan frissíthetek az SDK legújabb verziójára?*
  * Ha Gradle vagy Mavent használ...
    * Frissítse a Build-fájlt, és adja meg a legújabb verziót.
  * Ha manuálisan kezeli a függőségeket...
    * Töltse le a legújabb [Javához készült Application Insights SDK-t](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest), és cserélje le a régieket. A változások leírását az [SDK kiadási megjegyzéseiben](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) találja.

## <a name="add-an-applicationinsightsxml-file"></a>*ApplicationInsights. XML* fájl hozzáadása
Vegye fel a *ApplicationInsights. xml fájlt* a projekt erőforrások mappájába, vagy győződjön meg arról, hogy a projekt üzembe helyezési osztályának elérési útjához van adva. Másolja bele a következő XML-t.

Cserélje le a kialakítási kulcsot arra a típusra, amelyet a Azure Portal kapott.

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
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
   </TelemetryInitializers>

</ApplicationInsights>
```

Opcionálisan a konfigurációs fájl bármely, az alkalmazás számára elérhető helyen lehet.  A System tulajdonság `-Dapplicationinsights.configurationDirectory` a *ApplicationInsights. xml fájlt*tartalmazó könyvtárat adja meg. Az `E:\myconfigs\appinsights\ApplicationInsights.xml` mappában tárolt konfigurációs fájl konfigurálásához például a `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"` tulajdonság használható.

* A kialakítási kulcsot a telemetria minden elemével megkapja, és ez közli az Application Insights eszközzel, hogy megjelenítse azt az erőforrásban.
* A HTTP-kérelemösszetevő nem kötelező. Automatikusan telemetriát küld a kérelmekkel és válaszidőkkel kapcsolatban a portálra.
* Az eseménykorreláció a HTTP-kérelemösszetevő további eleme. Azonosítót rendel a kiszolgáló által fogadott összes kérelemhez. Ezután hozzáadja ezt az azonosítót tulajdonságként a telemetria minden eleméhez, mint a "Operation.Id" tulajdonságot. Lehetővé teszi, hogy összekapcsolja az egyes kérelmekhez társított telemetria egy szűrő beállításával a [diagnosztikai keresésben][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>A kialakítási kulcs beállításának egyéb módjai
Az Application Insights SDK ebben a sorrendben keresi a kulcsot:

1. System tulajdonság:-DAPPINSIGHTS_INSTRUMENTATIONKEY = your_ikey
2. Környezeti változó: APPINSIGHTS_INSTRUMENTATIONKEY
3. Konfigurációs fájl: *ApplicationInsights. XML*

[Beállíthatja a programkódban](../../azure-monitor/app/api-custom-events-metrics.md#ikey) is:

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="add-agent"></a>Ügynök hozzáadása

[Telepítse a Java-ügynököt](java-agent.md) a kimenő HTTP-hívások, a JDBC-lekérdezések, az alkalmazások naplózása és a jobb működés elnevezésének rögzítéséhez.

## <a name="run-your-application"></a>Az alkalmazás futtatása
Futtassa hibakeresés módban a fejlesztési számítógépén, vagy tegye közzé a kiszolgálóján.

## <a name="view-your-telemetry-in-application-insights"></a>A telemetria megtekintése az Application Insights szolgáltatásban
Térjen vissza az Application Insights-erőforráshoz a [Microsoft Azure Portalon](https://portal.azure.com).

A HTTP-kérelemadatok az áttekintési panelen jelennek meg. (Ha nincsenek ott, várjon néhány másodpercig, majd kattintson a Frissítés gombra.)

![A mintaadatok áttekintését bemutató képernyőkép](./media/java-get-started/overview-graphs.png)

[További információk a metrikákról.][metrics]

Részletesebb összesített mérőszámokért kattintson bármelyik diagramra.

![Application Insights hibák panel diagramokkal](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0 operation naming provided by agent]
-->

### <a name="instance-data"></a>Példányadatok
Kattintson az adott kérelemtípusokra az egyes példányok megtekintéséhez.

![Részletezés egy adott minta nézetbe](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Elemzés: Erőteljes lekérdezési nyelv
Ahogy egyre több adatot gyűjt össze, lekérdezéseket futtathat az adatok összegzéséhez és egyéni példányok megkereséséhez is.  Az [elemzés](../../azure-monitor/app/analytics.md) erőteljes eszköz a teljesítmény és a használat megértéséhez és diagnosztikai célokra is.

![Példa elemzésre](./media/java-get-started/0025.png)

## <a name="7-install-your-app-on-the-server"></a>7. Telepítse az alkalmazást a kiszolgálóra
Most tegye közzé az alkalmazást a kiszolgálón, hagyja, hogy mások használják, és nézze, ahogyan a telemetria megjelenik a portálon.

* Győződjön meg arról, hogy a tűzfal lehetővé teszi, hogy az alkalmazás telemetriát küldjön ezekre a portokra:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Ha a kimenő forgalmat át kell irányítani egy tűzfalon, adja meg a `http.proxyHost` és a `http.proxyPort` rendszertulajdonságot.

* Windows-kiszolgálókon telepítse a következőt:

  * [Microsoft Visual C++ újraterjeszthető csomag](https://www.microsoft.com/download/details.aspx?id=40784)

    (Ez az összetevő lehetővé teszi a teljesítményszámlálókat.)

## <a name="azure-app-service-config-spring-boot"></a>Azure App Service config (Spring boot)

A Windows rendszeren futó Spring boot-alkalmazások további konfigurálást igényelnek az Azure App Services-on való futtatáshoz. Módosítsa a **web. config** fájlt, és adja hozzá a következő konfigurációt:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <system.webServer>
        <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified"/>
        </handlers>
        <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\AzureWebAppExample-0.0.1-SNAPSHOT.jar&quot;">
        </httpPlatform>
    </system.webServer>
</configuration>
```

## <a name="exceptions-and-request-failures"></a>Kivételek és kérelemhibák
A nem kezelt kivételeket és a kérelmekkel kapcsolatos hibákat a Application Insights webes szűrő automatikusan gyűjti.

Ha más kivételekkel szeretne adatokat gyűjteni, a [kódban trackException () hívásokat is beszúrhat][apiexceptions].

## <a name="monitor-method-calls-and-external-dependencies"></a>Metódushívások és külső függőségek megfigyelése
[Telepítse a Java ügynököt](java-agent.md) a megadott belső módszerek és a JDBC-n keresztül végzett hívások naplózásához, időzítési adatokkal.

És automatikus művelet-elnevezéssel.

## <a name="w3c-distributed-tracing"></a>W3C elosztott nyomkövetés

A Application Insights Java SDK mostantól támogatja a [W3C elosztott nyomkövetést](https://w3c.github.io/trace-context/).

A bejövő SDK konfigurációját a [korrelációról](correlation.md#telemetry-correlation-in-the-java-sdk)szóló cikkben ismertetjük.

A kimenő SDK-konfiguráció a [AI-Agent. XML](java-agent.md) fájlban van definiálva.

## <a name="performance-counters"></a>Teljesítményszámlálók
Nyissa meg a **vizsgálat**, **mérőszámok**lehetőséget, és tekintse meg a teljesítményszámlálók tartományát.

![Képernyőfelvétel a metrikák panelről a folyamat saját bájtjainak kiválasztásával](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Teljesítményszámláló-gyűjtemény testreszabása
A teljesítményszámlálók szabványos készlete gyűjtésének letiltásához adja hozzá a következő kódot a *ApplicationInsights. XML* fájl gyökeréhez:

```XML
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>További teljesítményszámlálók gyűjtése
További gyűjtendő teljesítményszámlálókat határozhat meg.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>JMX-számlálók (a Java virtuális gép által feltárt)

```XML
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName`– Az Application Insights portálon megjelenő név.
* `objectName`– A JMX objektum neve.
* `attribute`– A JMX objektum nevének lehívni kívánt attribútuma
* `type`(választható) – A JMX objektum attribútumának típusa:
  * Alapértelmezett: egyszerű típus, például int vagy long.
  * `composite`: a teljesítményszámláló-adatok az „Attribútum.Adat” formátumban szerepelnek
  * `tabular`: a teljesítményszámláló-adatok táblázatsor formájában szerepelnek

#### <a name="windows-performance-counters"></a>Windows-teljesítményszámlálók
Mindegyik [Windows-teljesítményszámláló](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) egy kategória tagja (ugyanúgy, ahogyan a mezők osztályok tagjai). A kategóriák globálisak lehetnek, vagy számozott vagy elnevezett példányokkal rendelkezhetnek.

```XML
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName – Az Application Insights portálon megjelenő név.
* categoryName – A teljesítményszámláló kategóriája (teljesítményobjektum), amelyhez ez a teljesítményszámláló társítva van.
* counterName – A teljesítményszámláló neve.
* instanceName – A teljesítményszámláló-kategória példányneve, vagy üres sztring („”), ha a kategória egyetlen példányt tartalmaz. Ha a categoryName Folyamat, és a gyűjteni kívánt teljesítményszámláló az aktuális JVM folyamatról származik, amelyen az alkalmazása fut, adja meg a következőt: `"__SELF__"`.

### <a name="unix-performance-counters"></a>Unix-teljesítményszámlálók
* [Telepítse a gyűjteményt az Application Insights beépülő modullal](java-collectd.md) számos rendszer- és hálózati adat lekéréséhez.

## <a name="get-user-and-session-data"></a>Felhasználói és munkamenetadatok lekérése
Telemetriát küld a webkiszolgálóról. Az alkalmazás teljes körű megfigyelése érdekében további megfigyelést adhat hozzá:

* Az telemetria és a felhasználói mérőszámok figyeléséhez [vegyen fel weblapokat][usage] .
* [Állítson be webteszteket][availability] annak biztosítására, hogy az alkalmazás élő és rugalmas maradjon.

## <a name="send-your-own-telemetry"></a>Saját telemetria küldése
Most, hogy telepítette az SDK-t, az API-val saját telemetriát küldhet.

* [Nyomon követheti az egyéni eseményeket és mérőszámokat][api] , hogy megtudja, mit csinálnak a felhasználók az alkalmazással.
* [Keressen eseményeket és naplókat][diagnostic] a problémák diagnosztizálásához.

## <a name="availability-web-tests"></a>Rendelkezésre állási webes tesztek
Az Application Insights rendszeres időközönként teszteli a webhelyét, hogy működik és jól válaszol-e.

[További információ a rendelkezésre állási webes tesztek beállításáról.][availability]

## <a name="questions-problems"></a>Kérdései vannak? Problémákat tapasztal?
[A Java hibaelhárítása](java-troubleshoot.md)

## <a name="next-steps"></a>Következő lépések
* [Függőségi hívások figyelése](java-agent.md)
* [Unix-teljesítményszámlálók figyelése](java-collectd.md)
* [A weboldalak figyelésével](javascript.md) megfigyelheti az oldalbetöltési időket, az AJAX-hívásokat és a böngészőkivételeket.
* [Egyéni telemetriát](../../azure-monitor/app/api-custom-events-metrics.md) írhat a böngészőben vagy a kiszolgálón való használat nyomon követése érdekében.
* Az [Elemzések](../../azure-monitor/app/analytics.md) használatával hatékony telemetriai lekérdezéseket hajthat végre az alkalmazásból
* További információ: [Azure Java-fejlesztőknek](/java/azure).

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#trackexception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
[usage]: javascript.md
