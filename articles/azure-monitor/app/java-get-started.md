---
title: 'Rövid útmutató: Java webapp-elemzés az Azure Application Insights segítségével'
description: 'Alkalmazásteljesítmény-figyelés Java-webalkalmazásokhoz az Application Insights használatával. '
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 05/24/2019
ms.openlocfilehash: efa498ba600a664beab4b9a49eae2b2c3f1c8397
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298314"
---
# <a name="quickstart-get-started-with-application-insights-in-a-java-web-project"></a>Rövid útmutató: Az Application Insights első lépései java webes projektben

Ebben a rövid útmutatóban az Application Insights segítségével automatikusan instrument request, nyomon követheti a függőségek, és gyűjtse a teljesítményszámlálók, diagnosztizálni a teljesítményproblémák és kivételek, és írjon kódot, hogy nyomon kövesse, mit csinálnak a felhasználók az alkalmazással.

Az Application Insights egy bővíthető elemzési szolgáltatás a webfejlesztők számára, amely segít megérteni az élő alkalmazása teljesítményét és használatát. Az Application Insights a Linux, Unix vagy Windows rendszeren futó Java alkalmazásokat támogatja.

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Egy működő Java alkalmazás.

## <a name="get-an-application-insights-instrumentation-key"></a>Application Insights-kialakítási kulcs beszerzése

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Hozzon létre egy Application Insights-erőforrást az Azure Portalon. Állítsa be a Java webalkalmazás alkalmazástípust.

3. Keresse meg az új erőforrás kialakítási kulcsát. Ezt a kulcsot nemsokára a kódprojektbe kell illesztenie.

    ![Az új erőforrás áttekintésében kattintson a Tulajdonságok gombra, és másolja le a kialakítási kulcsot](./media/java-get-started/instrumentation-key-001.png)

## <a name="add-the-application-insights-sdk-for-java-to-your-project"></a>A Javához készült Application Insights SDK hozzáadása a projekthez

*Válassza ki a projekt típusát.*

# <a name="maven"></a>[Maven](#tab/maven)

Ha a projekt már be van állítva a Maven buildelésre való használatára, egyesítse a következő kódot a *pom.xml* fájllal.

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

Ha a projekt már be van állítva a Gradle buildelésre való használatára, egyesítse a következő kódot a *build.gradle* fájllal.

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
* *Milyen kapcsolat van a `-web-auto` `-web` és `-core` az összetevő között?*
  * `applicationinsights-web-auto`olyan metrikákat biztosít, amelyek nyomon követik a HTTP servlet kérelmek számát és a válaszidőket az Application Insights servlet szűrő futásidőben történő automatikus regisztrálásával.
  * `applicationinsights-web`is ad metrikák, amelyek nyomon követik a HTTP servlet kérelmek száma és a válaszidők, de manuális regisztrációt igényel az Application Insights servlet szűrő az alkalmazásban.
  * `applicationinsights-core`csak a csupasz API-t adja, például ha az alkalmazás nem servlet-alapú.
  
* *Hogyan frissíthetek az SDK legújabb verziójára?*
  * Ha Gradle-t vagy Mavent használsz...
    * Frissítse a buildfájlt a legújabb verzió megadásához.
  * Ha manuálisan kezeli a függőségeket...
    * Töltse le a legújabb [Javához készült Application Insights SDK-t](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest), és cserélje le a régieket. A változások leírását az [SDK kiadási megjegyzéseiben](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) találja.

## <a name="add-an-applicationinsightsxml-file"></a>*ApplicationInsights.xml* fájl hozzáadása
Adja hozzá az *ApplicationInsights.xml fájlt* a projekt erőforrásmappájához, vagy győződjön meg arról, hogy az hozzáadódik a projekt telepítési osztályának elérési útvonalához. Másolja bele a következő XML-t.

Cserélje le a műszerezési kulcsot az Azure Portalról kapottra.

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

Szükség esetén a konfigurációs fájl az alkalmazás számára elérhető bármely helyen lehet.  A rendszertulajdonság `-Dapplicationinsights.configurationDirectory` az *ApplicationInsights.xml*fájlt tartalmazó könyvtárat adja meg. Az `E:\myconfigs\appinsights\ApplicationInsights.xml` mappában tárolt konfigurációs fájl konfigurálásához például a `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"` tulajdonság használható.

* A kialakítási kulcsot a telemetria minden elemével megkapja, és ez közli az Application Insights eszközzel, hogy megjelenítse azt az erőforrásban.
* A HTTP-kérelemösszetevő nem kötelező. Automatikusan telemetriát küld a kérelmekkel és válaszidőkkel kapcsolatban a portálra.
* Az eseménykorreláció a HTTP-kérelemösszetevő további eleme. Azonosítót rendel a kiszolgáló által fogadott minden egyes kérelemhez. Ezután hozzáadja ezt az azonosítót tulajdonságként a telemetria minden eleméhez, mint a tulajdonság "Operation.Id". Lehetővé teszi az egyes kérelmekkel társított telemetria korrelációját, ha beállít egy szűrőt a [diagnosztikai keresésben][diagnostic].

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>A kialakítási kulcs beállításának egyéb módjai
Az Application Insights SDK ebben a sorrendben keresi a kulcsot:

1. Rendszertulajdonság: -DAPPINSIGHTS_INSTRUMENTATIONKEY=your_ikey
2. Környezeti változó: APPINSIGHTS_INSTRUMENTATIONKEY
3. Konfigurációs fájl: *ApplicationInsights.xml*

[Beállíthatja a programkódban](../../azure-monitor/app/api-custom-events-metrics.md#ikey) is:

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="add-agent"></a>Ügynök hozzáadása

[Telepítse a Java-ügynököt a](java-agent.md) kimenő HTTP-hívások, jdbc-lekérdezések, alkalmazásnaplózás és jobb műveletelnevezés rögzítéséhez.

## <a name="run-your-application"></a>Az alkalmazás futtatása
Futtassa hibakeresés módban a fejlesztési számítógépén, vagy tegye közzé a kiszolgálóján.

## <a name="view-your-telemetry-in-application-insights"></a>A telemetria megtekintése az Application Insights szolgáltatásban
Térjen vissza az Application Insights-erőforráshoz a [Microsoft Azure Portalon](https://portal.azure.com).

A HTTP-kérelemadatok az áttekintési panelen jelennek meg. (Ha nincsenek ott, várjon néhány másodpercig, majd kattintson a Frissítés gombra.)

![Áttekintést kapó mintaadatok képernyőképe](./media/java-get-started/overview-graphs.png)

[További információk a metrikákról.][metrics]

Részletesebb összesített mérőszámokért kattintson bármelyik diagramra.

![Az Application Insights hibáinak ablaktáblája diagramokkal](./media/java-get-started/006-barcharts.png)

<!--
[TODO update image with 2.5.0 operation naming provided by agent]
-->

### <a name="instance-data"></a>Példányadatok
Kattintson az adott kérelemtípusokra az egyes példányok megtekintéséhez.

![Részletezés egy adott mintanézetbe](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>Elemzés: Erőteljes lekérdezési nyelv
Ahogy egyre több adatot gyűjt össze, lekérdezéseket futtathat az adatok összegzéséhez és egyéni példányok megkereséséhez is.  Az [elemzés](../../azure-monitor/app/analytics.md) erőteljes eszköz a teljesítmény és a használat megértéséhez és diagnosztikai célokra is.

![Példa elemzésre](./media/java-get-started/0025.png)

## <a name="install-your-app-on-the-server"></a>Az alkalmazás telepítése a kiszolgálón
Most tegye közzé az alkalmazást a kiszolgálón, hagyja, hogy mások használják, és nézze, ahogyan a telemetria megjelenik a portálon.

* Győződjön meg arról, hogy a tűzfal lehetővé teszi, hogy az alkalmazás telemetriát küldjön ezekre a portokra:

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* Ha a kimenő forgalmat át kell irányítani egy tűzfalon, adja meg a `http.proxyHost` és a `http.proxyPort` rendszertulajdonságot.

* Windows-kiszolgálókon telepítse a következőt:

  * [Microsoft Visual C++ újraterjeszthető csomag](https://www.microsoft.com/download/details.aspx?id=40784)

    (Ez az összetevő lehetővé teszi a teljesítményszámlálókat.)

## <a name="azure-app-service-config-spring-boot"></a>Azure App Service konfiguráció (tavaszi rendszerindítás)

A Windows rendszeren futó tavaszi rendszerindítási alkalmazások további konfigurációt igényelnek az Azure App Services futtatásához. Módosítsa a **web.config fájlt,** és adja hozzá a következő konfigurációt:

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
A nem kezelt kivételeket és a kérési hibákat automatikusan gyűjti az Application Insights webszűrő.

Más kivételekre vonatkozó adatok gyűjtéséhez [a kódba beillesztheti a trackException() hívásokat.][apiexceptions]

## <a name="monitor-method-calls-and-external-dependencies"></a>Metódushívások és külső függőségek megfigyelése
[Telepítse a Java ügynököt](java-agent.md) a megadott belső módszerek és a JDBC-n keresztül végzett hívások naplózásához, időzítési adatokkal.

És az automatikus működés elnevezéséhez.

## <a name="w3c-distributed-tracing"></a>W3C elosztott nyomkövetés

Az Application Insights Java SDK mostantól támogatja a [W3C elosztott nyomkövetést.](https://w3c.github.io/trace-context/)

A bejövő SDK konfigurációt a [korrelációról](correlation.md#telemetry-correlation-in-the-java-sdk)szóló cikkünk ismerteti.

A kimenő SDK-konfiguráció az [AI-Agent.xml](java-agent.md) fájlban van definiálva.

## <a name="performance-counters"></a>Teljesítményszámlálók
A **Teljesítményszámlálók**tartományának megtekintéséhez nyissa meg a Vizsgálat , **Metrikák**.

![Képmutató a metrikák ablaktábláról a kijelölt folyamat-privát bájtokkal](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>Teljesítményszámláló-gyűjtemény testreszabása
A szabványos teljesítményszámlálók gyűjteményének letiltásához adja hozzá a következő kódot az *ApplicationInsights.xml* fájl gyökércsomópontja alá:

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

* [Adjon telemetriát a weblapokhoz][usage] a lapmegtekintések és a felhasználói mérőszámok megfigyelése érdekében.
* [Beállíthat webes teszteket][availability] annak biztosításához, hogy az alkalmazás mindig elérhető és válaszkész legyen.

## <a name="send-your-own-telemetry"></a>Saját telemetria küldése
Most, hogy telepítette az SDK-t, az API-val saját telemetriát küldhet.

* [Nyomon követheti az egyéni eseményeket és mérőszámokat][api], hogy megtudja, hogyan használják a felhasználók az alkalmazását.
* [Eseményeket és naplókat kereshet][diagnostic], amelyek segítenek a problémák diagnosztizálásában.

## <a name="availability-web-tests"></a>Rendelkezésre állási webes tesztek
Az Application Insights rendszeres időközönként teszteli a webhelyét, hogy működik és jól válaszol-e.

[További információ a rendelkezésre állási webes tesztek beállításáról.][availability]

## <a name="questions-problems"></a>Kérdései vannak? Problémákat tapasztal?
[A Java hibaelhárítása](java-troubleshoot.md)

## <a name="next-steps"></a>További lépések
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
